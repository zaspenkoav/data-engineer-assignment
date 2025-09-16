# Technical Assignment Details

## 🎯 Core Requirements

### 1. dbt Data Modeling (25 points)

**Staging Models**: Clean and validate raw data
- `stg_events` - Clean and typed event data (both page_view and purchase)
- `stg_clients` - Client dimension data

**Mart Models**: Attribution business logic
- `client_journeys` - Complete client paths from first touch to purchase
- `first_touch_attribution` - Revenue attributed to first marketing touch
- `last_touch_attribution` - Revenue attributed to last marketing touch  
- `channel_performance` - Performance metrics by marketing channel

### 2. Attribution Logic (25 points)
- **Attribution Window**: 30-day lookback from purchase
- **First-Touch Model**: Credit first marketing touch in journey (exclude 'direct')
- **Last-Touch Model**: Credit last marketing touch in journey (exclude 'direct')  
- **Cross-Session Tracking**: Link events across client sessions
- **Direct Channel Handling**: Only attribute to 'direct' if no other channels exist

### 3. Dagster Orchestration (20 points)
- **Asset-based Pipeline**: Use Dagster assets paradigm
- **dbt Integration**: Orchestrate dbt models with Dagster
- **Data Quality Checks**: Validate data after transformations
- **Dependency Management**: Proper asset dependencies

## 🔍 Key Business Questions

Your attribution pipeline should answer:

```sql
-- Which channel drives the most first-touch revenue?
SELECT 
    channel,
    SUM(attributed_revenue) as first_touch_revenue,
    COUNT(DISTINCT client_id) as attributed_clients
FROM marts.first_touch_attribution 
GROUP BY channel 
ORDER BY first_touch_revenue DESC;

-- What's the typical customer journey length?
SELECT 
    journey_length_days,
    COUNT(*) as client_count,
    AVG(total_revenue) as avg_revenue
FROM marts.client_journeys 
GROUP BY journey_length_days
ORDER BY journey_length_days;

-- Raw data: Page views vs Purchases by channel
SELECT 
    event_type,
    channel,
    COUNT(*) as event_count,
    COUNT(DISTINCT client_id) as unique_clients,
    SUM(revenue) as total_revenue
FROM {{ ref('stg_events') }}
GROUP BY event_type, channel
ORDER BY event_type, total_revenue DESC;

-- How do first-touch vs last-touch attribution compare?
SELECT 
    ft.channel as first_touch_channel,
    lt.channel as last_touch_channel,
    COUNT(*) as journey_count,
    AVG(ft.attributed_revenue) as avg_first_touch_revenue,
    AVG(lt.attributed_revenue) as avg_last_touch_revenue
FROM marts.first_touch_attribution ft
JOIN marts.last_touch_attribution lt USING (client_id, event_time)
GROUP BY ft.channel, lt.channel
ORDER BY journey_count DESC;
```

## 📋 Evaluation Criteria

### Technical Implementation (40%)
- **dbt Best Practices**: Proper staging/mart structure, tests, documentation
- **Dagster Architecture**: Asset-based design, proper dependencies
- **Code Quality**: Clean, readable, maintainable SQL and Python
- **Data Quality**: Comprehensive validation and testing

### Attribution Logic (35%)
- **Correctness**: Accurate implementation of attribution models
- **Edge Cases**: Handling of complex client journeys  
- **Business Rules**: Proper channel mapping and window logic
- **Performance**: Efficient queries for large datasets

### Documentation & Testing (25%)
- **Model Documentation**: Clear dbt model descriptions
- **Business Logic**: Explanation of attribution approach
- **Testing**: dbt tests and data quality validations
- **Architecture**: System design and decisions

## 🚨 Technical Challenges

1. **Multi-Touch Journeys**: Clients may have complex paths with multiple channels
2. **Session Stitching**: Linking events across different client sessions
3. **Attribution Windows**: Correctly implementing 30-day lookback logic  
4. **Channel Prioritization**: Handling cases where multiple touchpoints exist
5. **Data Quality**: Dealing with missing UTM parameters, invalid timestamps
6. **Performance**: Optimizing attribution queries for scale

## 💡 Implementation Tips

### dbt Best Practices
```sql
-- Use explicit column selection
SELECT 
    client_id,
    event_time,
    channel
FROM {{ ref('stg_events') }}

-- Add data quality tests
version: 2
models:
  - name: first_touch_attribution
    tests:
      - not_null:
          column_name: client_id
      - relationships:
          to: ref('stg_clients')
          field: client_id
```

### Dagster Assets
```python
@asset(deps=[dbt_assets])
def attribution_quality_check():
    """Validate attribution model outputs"""
    # Implementation here
```

## ❓ FAQ

**Q: Which attribution model is more important?**  
A: Implement both. Different models serve different business purposes.

**Q: How do I handle clients with no marketing touches?**
A: Credit to 'direct' channel or create 'organic' attribution.

**Q: What if a client has purchases outside the attribution window?**
A: Only attribute purchases within 30 days of marketing touches.

**Q: Should I handle mobile app vs web events differently?**
A: Treat them the same unless explicitly specified otherwise.

## 📬 Submission Checklist

- [ ] dbt project with all required models
- [ ] Dagster orchestration working end-to-end  
- [ ] Database populated with attribution results
- [ ] README with your approach and assumptions
- [ ] At least basic dbt tests implemented
- [ ] Pipeline runs successfully via `dagster dev`

## 🛠 Technical Setup

**You are free to choose:**
- Database (PostgreSQL recommended)
- Infrastructure setup (Docker, local, cloud)
- Dependencies and versions
- Project structure and organization

**Required technologies:**
- dbt (data transformations)
- Dagster (orchestration)
- SQL database for results

---

**Focus on building solid attribution logic first, then add advanced features. 🚀**