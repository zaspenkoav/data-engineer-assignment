# Data Engineer Assignment: Marketing Attribution Pipeline

Build a data pipeline for marketing attribution using **Dagster** and **dbt**.

## 📋 Assignment Overview

**Time Allocation**: 8-12 hours
**Level**: Senior Data Engineer
**Task**: Build automated data pipeline for marketing attribution
**Technology Stack**: dbt (transformations) + Dagster (orchestration)

## 🎯 Engineering Task

Implement data pipeline that:

1. Processes raw marketing events and purchase data
2. Creates staging models with data validation
3. Builds attribution mart tables according to specifications
4. Orchestrates transformations through Dagster assets
5. Includes data quality checks and testing

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

## 🎯 Attribution Models (Business Context)

### First-Touch Attribution
**Definition**: Credit full purchase revenue to the first marketing touchpoint in customer journey

### Last-Touch Attribution
**Definition**: Credit full purchase revenue to the last marketing touchpoint before purchase

### Business Rules to Implement
- **Attribution Window**: 30 days before purchase
- **Cross-Session Tracking**: Link events by client_id across sessions
- **Direct Traffic**: Handle customers with no marketing touchpoints

## 🛠 Technical Setup

**You are free to choose:**
- Database (PostgreSQL recommended)
- Infrastructure setup (Docker, local)
- Dependencies and versions
- Project structure and organization

**Required technologies:**
- dbt (data transformations)
- Dagster (orchestration)
- SQL database for results

## 🎯 Technical Requirements

### Required Data Models

#### Staging Layer

- `stg_events` - Cleaned and validated event data
- `stg_clients` - Client dimension table

#### Data Marts

- `first_touch_attribution` - Attribution to first marketing touchpoint
- `last_touch_attribution` - Attribution to last marketing touchpoint
- `channel_performance` - Aggregated channel metrics

### Pipeline Architecture

- **dbt Models**: SQL transformations for data processing
- **Dagster Assets**: Orchestration and dependency management
- **Data Quality**: Validation checks and tests
- **Documentation**: Model descriptions and business logic

## 📊 Required Output Data Structure

### first_touch_attribution

```text
client_id (string) - Client identifier
purchase_time (timestamp) - When purchase occurred
attributed_channel (string) - Channel receiving attribution credit
attributed_revenue (decimal) - Revenue amount attributed
first_touch_time (timestamp) - Time of first marketing touchpoint
days_to_conversion (integer) - Days from first touch to purchase
```

### last_touch_attribution

```text
client_id (string) - Client identifier
purchase_time (timestamp) - When purchase occurred
attributed_channel (string) - Channel receiving attribution credit
attributed_revenue (decimal) - Revenue amount attributed
last_touch_time (timestamp) - Time of last marketing touchpoint
days_to_conversion (integer) - Days from last touch to purchase
```

### channel_performance

```text
channel (string) - Marketing channel name
first_touch_revenue (decimal) - Total revenue via first-touch model
last_touch_revenue (decimal) - Total revenue via last-touch model
total_conversions (integer) - Number of attributed purchases
unique_clients (integer) - Number of unique clients attributed
```

## 📊 Evaluation Areas

### Technical Implementation

- **dbt Structure**: Proper staging/mart organization and SQL quality
- **Dagster Integration**: Asset-based architecture and orchestration
- **Data Quality**: Comprehensive testing and validation
- **Code Quality**: Readable, maintainable implementation

### Business Logic Accuracy

- **Attribution Models**: Correct first-touch and last-touch implementation
- **Business Rules**: 30-day window, cross-session tracking
- **Edge Cases**: Handling clients with no touchpoints, direct traffic

### Engineering Excellence

- **Pipeline Design**: Scalable and maintainable architecture
- **Documentation**: Clear setup instructions and business logic
- **Testing**: dbt tests and Dagster validations

## 🚨 Implementation Considerations

### Data Engineering Challenges

1. **Cross-Session Linking**: Events from same client across multiple sessions
2. **Time Window Logic**: 30-day attribution window implementation
3. **Data Validation**: Handle missing/invalid data appropriately
4. **Dependency Management**: Correct model build order
5. **Performance**: Efficient processing for production scale

### Expected Edge Cases

- Clients with purchases but no marketing events
- Multiple purchases from same client
- Events outside attribution window
- Missing or malformed timestamps
- Direct traffic handling

## ❓ Technical FAQ

**Q: How to handle clients with no marketing touchpoints?**
A: Design solution for attribution when only direct traffic exists.

**Q: Multiple purchases from same client?**
A: Each purchase should have independent attribution calculation.

**Q: What constitutes a valid marketing touchpoint?**
A: Events within 30-day window before purchase with non-direct channel.

**Q: How to link events across sessions?**
A: Use client_id as the primary identifier for cross-session tracking.

## 📬 Deliverables

### Required Components
- [ ] Complete dbt project with staging and mart models
- [ ] Dagster assets for pipeline orchestration
- [ ] Database populated with all required tables
- [ ] README with setup instructions and architecture decisions
- [ ] dbt tests for data quality validation
- [ ] Working pipeline via `dagster dev`

### Project Structure

```text
project/
├── dbt_project.yml
├── models/
│   ├── staging/
│   └── marts/
├── dagster_project/
├── README.md
└── docker-compose.yml (optional)
```

### Success Criteria

- Pipeline runs end-to-end without errors
- All required tables populated with correct structure
- Attribution logic handles edge cases appropriately
- Code is well-documented and maintainable

---

## Focus: Build robust, production-ready data pipeline infrastructure 🔧
