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

## 📋 Next Steps

👉 **See [ASSIGNMENT.md](ASSIGNMENT.md) for detailed technical requirements, evaluation criteria, and implementation guidelines.**

---

**Good luck! This is a challenging but realistic senior-level data engineering problem. 🚀**