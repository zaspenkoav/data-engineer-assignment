# Data Engineer Assignment: Marketing Attribution Pipeline

Build an end-to-end data pipeline for marketing attribution analysis.

**Time allocation:** 8-12 hours
**Level:** Senior Data Engineer
**Stack:** dbt + Dagster + SQL Database

---

## Task Overview

Design and implement a data pipeline that:
1. Processes raw marketing events and client data
2. Creates clean, validated staging models
3. Builds attribution analytics for marketing channels
4. Orchestrates the entire pipeline with proper dependencies

---

## Business Context

### Marketing Attribution Problem

A company tracks customer interactions across multiple marketing channels (paid search, social media, email, organic search, direct). They need to understand which channels drive conversions (purchases).

**Your task:** Build a data pipeline that attributes revenue to marketing channels using different attribution models.

### Attribution Models to Implement

1. **First-Touch Attribution**
   - Credit goes to the first marketing touchpoint in customer journey
   - Example: Customer sees Facebook ad (first touch) → searches on Google → purchases
   - Facebook gets full credit

2. **Last-Touch Attribution**
   - Credit goes to the last marketing touchpoint before purchase
   - Same example: Google search gets full credit

### Business Rules
- Consider touchpoints within **30 days** before purchase
- Track customers across multiple sessions
- Handle cases where customers have no marketing touchpoints (direct traffic)

---

## Data

The `data/raw/` directory contains sample data:

### events.csv (~92 records)
Marketing events and purchases:
- Client interactions with marketing channels
- Purchase transactions
- Session and campaign tracking
- Timestamps and revenue data

### clients.csv (~20 records)
Client dimension data:
- Client identifiers
- Registration information
- Segmentation data

**Note:** Analyze the actual data files to understand the complete schema.

---

## Technical Requirements

### Required Stack
- **dbt** - data transformations
- **Dagster** - orchestration
- **SQL database** - your choice (PostgreSQL recommended)

### Your Architecture Should Include

#### Staging Layer
Clean and validated source data with:
- Proper data types
- Basic validation
- Naming conventions

#### Attribution Mart Layer
Analytics-ready tables for:
- First-touch attribution analysis
- Last-touch attribution analysis
- Channel performance metrics

#### Orchestration
Dagster assets that:
- Manage dependencies correctly
- Can run incrementally
- Include data quality checks

---

## What We're Evaluating

### Data Engineering (40%)
- **Pipeline design:** Proper staging/mart separation, incremental logic
- **SQL quality:** Readable, maintainable, performant queries
- **Data modeling:** Appropriate schema design for analytics
- **Data quality:** Tests and validation

### Business Logic (30%)
- **Attribution accuracy:** Correct implementation of business rules
- **Edge case handling:** Direct traffic, missing data, multi-purchase scenarios
- **Window logic:** Proper 30-day attribution window
- **Cross-session tracking:** Linking events across sessions

### Engineering Excellence (30%)
- **Code organization:** Clear project structure
- **Documentation:** README, model descriptions, business logic
- **Dagster integration:** Asset-based architecture
- **Testing:** dbt tests and validation logic

---

## Deliverables

### Required
1. Complete dbt project with:
   - Staging models
   - Mart models for attribution analysis
   - dbt tests for data quality
   - Model documentation

2. Dagster orchestration:
   - Asset definitions
   - Proper dependency management
   - Data quality checks

3. Database with populated tables

4. README with:
   - Architecture decisions and trade-offs
   - Setup instructions (prerequisites, installation)
   - How attribution logic works
   - Design decisions for edge cases
   - How to run the pipeline

### Success Criteria
- ✅ Pipeline runs end-to-end successfully
- ✅ Attribution logic produces reasonable results
- ✅ Code is well-organized and documented
- ✅ Edge cases are handled appropriately
- ✅ Tests validate data quality

---

## Key Engineering Challenges

You'll need to solve:
- **Cross-session linking:** How to connect events from same customer?
- **Time windows:** How to filter events within attribution window?
- **Edge cases:** What happens with direct traffic, no touchpoints, multiple purchases?
- **Performance:** Can your solution scale to millions of events?
- **Data quality:** How do you validate correctness?

---

## Architecture Decisions

You have freedom to decide:
- Exact database schema design
- Which SQL dialect features to use
- How to structure dbt models
- Incremental vs full refresh strategy
- How to handle edge cases
- Testing strategy

**We want to see your engineering thinking**, not just implementation.

---

## Setup Freedom

**Your choice:**
- Local database or Docker
- Specific database (PostgreSQL, DuckDB, etc.)
- Project structure and organization
- Dependencies and versions
- Additional tooling

**We'll evaluate:** How well you justify your technical choices.

---

## Recommended Approach

1. **Understand the data first** - explore CSVs, understand relationships
2. **Design your schema** - sketch out staging and mart tables
3. **Implement incrementally:**
   - Start with staging models
   - Implement one attribution model
   - Add orchestration
   - Add tests
   - Implement second attribution model
4. **Document decisions** - explain trade-offs in README

---

## Hints (Optional)

Some questions to consider:
- How will you identify "first" and "last" touchpoints per purchase?
- What defines a marketing touchpoint vs direct traffic?
- How to handle customers with multiple purchases?
- What to do when there are no marketing touchpoints?
- How to test your attribution logic is correct?

---

## Resources

- [dbt Documentation](https://docs.getdbt.com/)
- [Dagster Documentation](https://docs.dagster.io/)
- [Marketing Attribution Explained](https://en.wikipedia.org/wiki/Attribution_(marketing))

---

## Questions?

If you need clarifications on business requirements - just ask!

We're evaluating your **data engineering skills and architectural thinking**, not your ability to guess requirements.

Good luck! 🚀
