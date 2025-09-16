# Senior Data Engineer Assignment: Marketing Attribution Pipeline

Build a marketing attribution system using **Dagster** and **dbt** to solve real-world attribution modeling challenges.

## 📋 Assignment Overview

**Time Allocation**: 8-12 hours  
**Level**: Senior Data Engineer  
**Objective**: Build complete attribution pipeline using modern data stack  
**Focus Areas**: dbt transformations, Dagster orchestration, attribution modeling

## 🎯 What You'll Build

Create a marketing attribution system that:
1. Processes marketing events and purchase transactions
2. Links marketing touchpoints to revenue using attribution logic  
3. Implements First-Touch and Last-Touch attribution models
4. Generates channel performance analytics
5. Uses **dbt** for transformations and **Dagster** for orchestration

## 📊 Data Overview

The `data/raw/` directory contains two CSV files:

### `events.csv` (~92 events)

All user events including page views and purchases:

- `client_id` - Unique client identifier
- `event_type` - Event type: 'page_view' or 'purchase'
- `event_time` - When the event occurred
- `session_id` - User session identifier
- `channel` - Marketing channel (organic_search, paid_search, social, direct, email)
- `campaign_id` - Marketing campaign identifier
- `utm_source` - UTM source parameter
- `utm_medium` - UTM medium parameter  
- `page_url` - Page URL for the event
- `referrer_url` - Referring page URL
- `revenue` - Purchase amount (only for 'purchase' events)
- `product_id` - Product identifier (only for 'purchase' events)
- `product_category` - Product category (only for 'purchase' events)

**Event Types:**
- **page_view**: Marketing touchpoints when client visits pages
- **purchase**: Conversion events with revenue and product data

### `clients.csv` (~20 clients)

Client dimension data:

- `client_id` - Unique client identifier  
- `registration_date` - When client registered
- `cohort` - Registration cohort (e.g., "2023-Q1")
- `customer_segment` - Client segment (new_customer, returning_customer, vip_customer)

## 🎯 Attribution Models Explained

### First-Touch Attribution
- **Definition**: 100% credit to first marketing touchpoint before purchase
- **Use Case**: Brand awareness, top-of-funnel optimization  
- **Logic**: Find earliest non-direct marketing event within 30-day window

### Last-Touch Attribution
- **Definition**: 100% credit to last marketing touchpoint before purchase
- **Use Case**: Conversion optimization, bottom-of-funnel analysis
- **Logic**: Find latest non-direct marketing event before purchase

### Attribution Window
- **30 Days**: Look back 30 days from purchase time
- **Cross-Session**: Link events across multiple client sessions
- **Direct Channel**: Exclude 'direct' visits from attribution (unless only channel)

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

## 🎯 Core Requirements

### 1. dbt Data Modeling (40 points)

**Staging Models**: Clean and validate raw data
- `stg_events` - Clean and typed event data (both page_view and purchase)
- `stg_clients` - Client dimension data

**Mart Models**: Attribution business logic
- `client_journeys` - Complete client paths from first touch to purchase
- `first_touch_attribution` - Revenue attributed to first marketing touch
- `last_touch_attribution` - Revenue attributed to last marketing touch
- `channel_performance` - Performance metrics by marketing channel

### 2. Attribution Logic (35 points)
- **Attribution Window**: 30-day lookback from purchase
- **First-Touch Model**: Credit first marketing touch in journey (exclude 'direct')
- **Last-Touch Model**: Credit last marketing touch in journey (exclude 'direct')
- **Cross-Session Tracking**: Link events across client sessions
- **Direct Channel Handling**: Only attribute to 'direct' if no other channels exist

### 3. Dagster Orchestration & Testing (25 points)
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

## 📊 Evaluation Criteria (100 points total)

### Technical Implementation (40 points)
- **dbt Best Practices** (15 points): Proper staging/mart structure, tests, documentation
- **Code Quality** (10 points): Clean, readable, maintainable SQL and Python
- **Data Quality** (10 points): Comprehensive validation and testing
- **Performance** (5 points): Efficient queries for large datasets

### Attribution Logic Correctness (35 points)
- **Attribution Models** (15 points): Accurate implementation of first-touch and last-touch
- **Business Rules** (10 points): Proper channel mapping and window logic
- **Edge Cases** (10 points): Handling of complex client journeys

### Dagster Architecture & Documentation (25 points)
- **Asset Design** (10 points): Proper asset-based architecture and dependencies
- **Documentation** (10 points): Clear README, model descriptions, business logic explanation
- **Testing** (5 points): dbt tests and data quality validations

### Scoring Guide:
- **90-100 points**: Exceptional - Senior+ level
- **80-89 points**: Strong - Senior level
- **70-79 points**: Good - Senior- level
- **60-69 points**: Adequate - Middle+ level
- **Below 60**: Needs improvement

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

### 🎯 Bonus Points
- Unit testing pipeline components
- CI/CD setup with GitHub Actions
- Advanced Dagster features (sensors, schedules)
- Additional analytics insights
- Performance optimizations

---

**Focus on building solid attribution logic first, then add advanced features. Good luck! 🚀**