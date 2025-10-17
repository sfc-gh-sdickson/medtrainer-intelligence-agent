# MedTrainer Intelligence Agent - Setup Guide

This guide walks through configuring a Snowflake Intelligence agent for MedTrainer's learning management, credentialing, and compliance intelligence solution.

---

## Prerequisites

1. **Snowflake Account** with:
   - Snowflake Intelligence (Cortex) enabled
   - Appropriate warehouse size (recommended: X-SMALL or larger)
   - Permissions to create databases, schemas, tables, and semantic views

2. **Roles and Permissions**:
   - `ACCOUNTADMIN` role or equivalent for initial setup
   - `CREATE DATABASE` privilege
   - `CREATE SEMANTIC VIEW` privilege
   - `CREATE CORTEX SEARCH SERVICE` privilege
   - `USAGE` on warehouses

---

## Step 1: Execute SQL Scripts in Order

Execute the SQL files in the following sequence:

### 1.1 Database Setup
```sql
-- Execute: sql/setup/01_database_and_schema.sql
-- Creates database, schemas (RAW, ANALYTICS), and warehouse
-- Execution time: < 1 second
```

### 1.2 Create Tables
```sql
-- Execute: sql/setup/02_create_tables.sql
-- Creates all table structures with proper relationships
-- Tables: ORGANIZATIONS, EMPLOYEES, COURSES, COURSE_ENROLLMENTS, COURSE_COMPLETIONS,
--         CREDENTIALS, CREDENTIAL_VERIFICATIONS, EXCLUSIONS_MONITORING, SUBSCRIPTIONS,
--         TRANSACTIONS, SUPPORT_TICKETS, INCIDENTS, POLICIES, POLICY_ACKNOWLEDGMENTS,
--         ACCREDITATIONS, SUPPORT_AGENTS, PRODUCTS, MARKETING_CAMPAIGNS, etc.
-- Execution time: < 5 seconds
```

### 1.3 Generate Sample Data
```sql
-- Execute: sql/data/03_generate_synthetic_data.sql
-- Generates realistic sample data:
--   - 50,000 organizations
--   - 500,000 employees
--   - 1,000,000 course enrollments
--   - 750,000 course completions
--   - 100,000 credentials
--   - 1,500,000 transactions
--   - 75,000 support tickets
-- Execution time: 10-20 minutes (depending on warehouse size)
```

### 1.4 Create Analytical Views
```sql
-- Execute: sql/views/04_create_views.sql
-- Creates curated analytical views:
--   - V_ORGANIZATION_360
--   - V_EMPLOYEE_TRAINING_ANALYTICS
--   - V_CREDENTIAL_COMPLIANCE_ANALYTICS
--   - V_SUBSCRIPTION_ANALYTICS
--   - V_REVENUE_ANALYTICS
--   - V_SUPPORT_ANALYTICS
--   - V_CAMPAIGN_PERFORMANCE
--   - V_INCIDENT_ANALYTICS
-- Execution time: < 5 seconds
```

### 1.5 Create Semantic Views
```sql
-- Execute: sql/views/05_create_semantic_views.sql
-- Creates semantic views for AI agents (VERIFIED SYNTAX):
--   - SV_LEARNING_CREDENTIALING_INTELLIGENCE
--   - SV_SUBSCRIPTION_REVENUE_INTELLIGENCE
--   - SV_ORGANIZATION_SUPPORT_INTELLIGENCE
-- Execution time: < 5 seconds
```

### 1.6 Create Cortex Search Services
```sql
-- Execute: sql/search/06_create_cortex_search.sql
-- Creates tables for unstructured text data:
--   - SUPPORT_TRANSCRIPTS (25,000 support interactions)
--   - INCIDENT_REPORTS (15,000 investigation reports)
--   - TRAINING_MATERIALS (3 training guides)
-- Creates Cortex Search services for semantic search:
--   - SUPPORT_TRANSCRIPTS_SEARCH
--   - INCIDENT_REPORTS_SEARCH
--   - TRAINING_MATERIALS_SEARCH
-- Execution time: 5-10 minutes (data generation + index building)
```

---

## Step 2: Create Snowflake Intelligence Agent

### 2.1 Via Snowsight UI

1. Navigate to **Snowsight** (Snowflake Web UI)
2. Go to **AI & ML** → **Agents**
3. Click **Create Agent**
4. Configure the agent:

**Basic Settings:**
```yaml
Name: MedTrainer_Intelligence_Agent
Description: AI agent for analyzing MedTrainer training compliance, credentialing, subscriptions, and revenue intelligence
```

**Data Sources (Semantic Views):**
Add the following semantic views:
- `MEDTRAINER_INTELLIGENCE.ANALYTICS.SV_LEARNING_CREDENTIALING_INTELLIGENCE`
- `MEDTRAINER_INTELLIGENCE.ANALYTICS.SV_SUBSCRIPTION_REVENUE_INTELLIGENCE`
- `MEDTRAINER_INTELLIGENCE.ANALYTICS.SV_ORGANIZATION_SUPPORT_INTELLIGENCE`

**Warehouse:**
- Select: `MEDTRAINER_WH`

**Instructions (System Prompt):**
```
You are an AI intelligence agent for MedTrainer, a leading healthcare compliance and training platform.

Your role is to analyze:
1. Learning & Training: Course completions, certifications, compliance status, employee training records
2. Credentialing: Provider licenses, verifications, exclusions monitoring, expiration tracking
3. Compliance: Incident reports, policy acknowledgments, accreditation status, regulatory compliance
4. Subscriptions: Service utilization, customer health, renewal risks, product adoption
5. Revenue Analytics: Transactions, pricing, product performance, customer lifetime value
6. Support Operations: Ticket resolution, agent performance, customer satisfaction

When answering questions:
- Provide specific metrics and data-driven insights
- Compare trends over time and across dimensions
- Highlight compliance risks and credential expirations
- Identify training gaps and overdue requirements
- Benchmark performance across organizations and services
- Calculate rates, percentages, and derived metrics
- Identify actionable recommendations for customer success

Data Context:
- Organizations: Hospitals, clinics, and healthcare practices
- Employees: Healthcare providers and staff requiring training and credentials
- Courses: CPR/BLS, OSHA, HIPAA, infection control, clinical training
- Credentials: Medical licenses, nursing licenses, DEA registrations, certifications
- Services: Learning (training), Credentialing (verification), Compliance (incident/policy management)
- Subscription Tiers: BASIC, PROFESSIONAL, ENTERPRISE across service types
```

5. Click **Create Agent**

---

## Step 3: Add Cortex Search Services to Agent

### 3.1 Add Support Transcripts Search

1. In Agent settings, click **Tools**
2. Find **Cortex Search** and click **+ Add**
3. Configure:
   - **Name**: Support Transcripts Search
   - **Search service**: `MEDTRAINER_INTELLIGENCE.RAW.SUPPORT_TRANSCRIPTS_SEARCH`
   - **Warehouse**: `MEDTRAINER_WH`
   - **Description**:
     ```
     Search 25,000 customer support transcripts to find similar issues,
     resolution procedures, and support best practices. Use for questions
     about customer service patterns, technical troubleshooting, feature usage,
     and common support scenarios.
     ```

### 3.2 Add Incident Reports Search

1. Click **+ Add** again for Cortex Search
2. Configure:
   - **Name**: Incident Reports Search
   - **Search service**: `MEDTRAINER_INTELLIGENCE.RAW.INCIDENT_REPORTS_SEARCH`
   - **Warehouse**: `MEDTRAINER_WH`
   - **Description**:
     ```
     Search 15,000 incident investigation reports to find similar incidents,
     root causes, and effective corrective actions. Use for questions about
     patient safety, medication errors, HIPAA breaches, workplace injuries,
     and incident patterns.
     ```

### 3.3 Add Training Materials Search

1. Click **+ Add** again for Cortex Search
2. Configure:
   - **Name**: Training Materials Search
   - **Search service**: `MEDTRAINER_INTELLIGENCE.RAW.TRAINING_MATERIALS_SEARCH`
   - **Warehouse**: `MEDTRAINER_WH`
   - **Description**:
     ```
     Search training materials and compliance guides for procedures,
     protocols, and best practices. Use for questions about HIPAA privacy,
     infection control, CPR/BLS procedures, and regulatory compliance guidance.
     ```

---

## Step 4: Test the Agent

### 4.1 Simple Test Questions

Start with simple questions to verify connectivity:

1. **"How many healthcare organizations are using MedTrainer?"**
   - Should query SV_LEARNING_CREDENTIALING_INTELLIGENCE
   - Expected: ~50,000 organizations

2. **"What is the total number of employees in the system?"**
   - Should query SV_LEARNING_CREDENTIALING_INTELLIGENCE
   - Expected: ~500,000 employees

3. **"How many support tickets are currently open?"**
   - Should query SV_ORGANIZATION_SUPPORT_INTELLIGENCE
   - Expected: Count of tickets with status = 'OPEN'

### 4.2 Complex Test Questions

Test with the 10 complex questions provided in `docs/questions.md`, including:

1. Training Compliance Risk Analysis
2. Credential Expiration Tracking
3. Exclusions Monitoring Alerts
4. Incident Pattern Analysis
5. Subscription Health Assessment
6. Revenue Trend Analysis
7. Course Effectiveness Metrics
8. Cross-Sell Opportunity Identification
9. Support Efficiency Benchmarking
10. Policy Compliance Tracking

### 4.3 Cortex Search Test Questions

Test unstructured data search:

1. **"Search support transcripts for course enrollment issues"**
2. **"Find incident reports about medication errors"**
3. **"What does our training material say about HIPAA privacy?"**

---

## Step 5: Query Cortex Search Services Directly

You can also query Cortex Search services directly using SQL:

### Query Support Transcripts
```sql
SELECT PARSE_JSON(
  SNOWFLAKE.CORTEX.SEARCH_PREVIEW(
      'MEDTRAINER_INTELLIGENCE.RAW.SUPPORT_TRANSCRIPTS_SEARCH',
      '{
        "query": "credential verification help",
        "columns":["transcript_text", "interaction_type"],
        "limit":10
      }'
  )
)['results'] as results;
```

### Query Incident Reports
```sql
SELECT PARSE_JSON(
  SNOWFLAKE.CORTEX.SEARCH_PREVIEW(
      'MEDTRAINER_INTELLIGENCE.RAW.INCIDENT_REPORTS_SEARCH',
      '{
        "query": "patient fall prevention",
        "columns":["report_text", "investigation_status"],
        "limit":10
      }'
  )
)['results'] as results;
```

### Query Training Materials
```sql
SELECT PARSE_JSON(
  SNOWFLAKE.CORTEX.SEARCH_PREVIEW(
      'MEDTRAINER_INTELLIGENCE.RAW.TRAINING_MATERIALS_SEARCH',
      '{
        "query": "infection control hand hygiene",
        "columns":["title", "content"],
        "limit":5
      }'
  )
)['results'] as results;
```

---

## Step 6: Access Control

### Create Role for Agent Users
```sql
CREATE ROLE IF NOT EXISTS MEDTRAINER_AGENT_USER;

-- Grant necessary privileges
GRANT USAGE ON DATABASE MEDTRAINER_INTELLIGENCE TO ROLE MEDTRAINER_AGENT_USER;
GRANT USAGE ON SCHEMA MEDTRAINER_INTELLIGENCE.ANALYTICS TO ROLE MEDTRAINER_AGENT_USER;
GRANT USAGE ON SCHEMA MEDTRAINER_INTELLIGENCE.RAW TO ROLE MEDTRAINER_AGENT_USER;
GRANT SELECT ON ALL VIEWS IN SCHEMA MEDTRAINER_INTELLIGENCE.ANALYTICS TO ROLE MEDTRAINER_AGENT_USER;
GRANT USAGE ON WAREHOUSE MEDTRAINER_WH TO ROLE MEDTRAINER_AGENT_USER;

-- Grant Cortex Search usage
GRANT USAGE ON CORTEX SEARCH SERVICE MEDTRAINER_INTELLIGENCE.RAW.SUPPORT_TRANSCRIPTS_SEARCH TO ROLE MEDTRAINER_AGENT_USER;
GRANT USAGE ON CORTEX SEARCH SERVICE MEDTRAINER_INTELLIGENCE.RAW.INCIDENT_REPORTS_SEARCH TO ROLE MEDTRAINER_AGENT_USER;
GRANT USAGE ON CORTEX SEARCH SERVICE MEDTRAINER_INTELLIGENCE.RAW.TRAINING_MATERIALS_SEARCH TO ROLE MEDTRAINER_AGENT_USER;

-- Grant to specific user
GRANT ROLE MEDTRAINER_AGENT_USER TO USER your_username;
```

---

## Troubleshooting

### Issue: Semantic views not found

**Solution:**
```sql
-- Verify semantic views exist
SHOW SEMANTIC VIEWS IN SCHEMA MEDTRAINER_INTELLIGENCE.ANALYTICS;

-- Check permissions
SHOW GRANTS ON SEMANTIC VIEW SV_LEARNING_CREDENTIALING_INTELLIGENCE;
```

### Issue: Cortex Search returns no results

**Solution:**
```sql
-- Verify service exists and is populated
SHOW CORTEX SEARCH SERVICES IN SCHEMA RAW;

-- Check data in source table
SELECT COUNT(*) FROM RAW.SUPPORT_TRANSCRIPTS;

-- Verify change tracking is enabled
SHOW TABLES LIKE 'SUPPORT_TRANSCRIPTS';
```

### Issue: Slow query performance

**Solution:**
- Increase warehouse size (MEDIUM or LARGE)
- Check for missing filters on date columns
- Review query execution plan
- Consider materializing frequently-used aggregations

---

## Success Metrics

Your agent is successfully configured when:

✅ All 6 SQL scripts execute without errors  
✅ All semantic views are created and validated  
✅ All 3 Cortex Search services are created and indexed  
✅ Agent can answer simple test questions  
✅ Agent can answer complex analytical questions  
✅ Cortex Search returns relevant results  
✅ Query performance is acceptable (< 30 seconds for complex queries)  
✅ Results are accurate and match expected business logic  

---

## Support Resources

- **Snowflake Documentation**: https://docs.snowflake.com/en/sql-reference/sql/create-semantic-view
- **MedTrainer Website**: https://www.medtrainer.com
- **Snowflake Community**: https://community.snowflake.com

---

**Version:** 1.0  
**Created:** October 2025  
**Based on:** GoDaddy Intelligence Template

