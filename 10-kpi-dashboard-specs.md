# KPI Definitions & Dashboard Specifications: Xore

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-KPI-001 |
| Version | 1.1 |
| Author | Pinky Choudhary, Business Analyst |
| Tools | Tableau (Project Health), Power BI (Product Metrics) |
| Last Updated | 2025-11-25 |

---

## 1. KPI Framework

KPIs are organized into two categories: Project Health (delivery metrics tracked during development) and Product Metrics (platform performance tracked post-launch).

---

## 2. Project Health KPIs (Tableau Dashboard)

### Dashboard: Xore Project Health Tracker

**Audience**: Product Owner, Tech Lead, BA/PM  
**Refresh**: Updated after each sprint (bi-weekly)  
**Data Source**: JIRA export (CSV) + manual sprint data (Google Sheets)

### 2.1 KPI Definitions

#### KPI-P01: Sprint Velocity

| Field | Value |
|-------|-------|
| Definition | Total story points delivered per sprint |
| Formula | SUM(story_points) WHERE status = 'Done' AND sprint = [current] |
| Target | 30-38 points per sprint |
| Visualization | Bar chart (velocity per sprint) with trend line |
| Source | JIRA sprint report export |

#### KPI-P02: Sprint Burndown

| Field | Value |
|-------|-------|
| Definition | Remaining story points over time within a sprint |
| Formula | Committed_points - CUMSUM(completed_points) by day |
| Target | Linear descent from committed to zero |
| Visualization | Line chart (ideal burndown vs actual) |
| Source | JIRA sprint report |

#### KPI-P03: Backlog Completion Rate

| Field | Value |
|-------|-------|
| Definition | Percentage of total backlog story points delivered |
| Formula | (Delivered_points / Total_backlog_points) x 100 |
| Target | 100% of Must Have items by Sprint 6 |
| Visualization | Progress bar with MoSCoW breakdown |
| Source | JIRA backlog |

#### KPI-P04: Defect Density

| Field | Value |
|-------|-------|
| Definition | Number of defects per sprint relative to stories delivered |
| Formula | COUNT(defects) / COUNT(stories_delivered) per sprint |
| Target | Below 0.5 defects per story |
| Visualization | Stacked bar (defects by severity per sprint) |
| Source | JIRA defect tracker |

#### KPI-P05: UAT Pass Rate

| Field | Value |
|-------|-------|
| Definition | Percentage of UAT test cases passing on first execution |
| Formula | (Passed_tests / Total_tests) x 100 |
| Target | >= 90% |
| Visualization | Donut chart (Pass / Fail / Blocked) |
| Source | UAT test results spreadsheet |

#### KPI-P06: Risk Exposure Score

| Field | Value |
|-------|-------|
| Definition | Sum of risk scores for all active (unresolved) risks |
| Formula | SUM(probability x impact) WHERE status != 'Resolved' |
| Target | Declining trend over project lifecycle |
| Visualization | Area chart (risk exposure over time) |
| Source | Risk register |

### 2.2 Tableau Dashboard Layout

```
┌──────────────────────────────────────────────────────────┐
│                  XORE PROJECT HEALTH                      │
├───────────────────────┬──────────────────────────────────┤
│  Sprint Velocity      │  Sprint Burndown                 │
│  [Bar Chart]          │  [Line Chart]                    │
│  6 sprints            │  Current sprint detail           │
├───────────────────────┼──────────────────────────────────┤
│  Backlog Completion   │  Defect Density                  │
│  [Progress Bar]       │  [Stacked Bar]                   │
│  MoSCoW breakdown     │  By severity per sprint          │
├───────────────────────┼──────────────────────────────────┤
│  UAT Pass Rate        │  Risk Exposure                   │
│  [Donut Chart]        │  [Area Chart]                    │
│  Pass/Fail/Blocked    │  Over project timeline           │
└───────────────────────┴──────────────────────────────────┘
```

**Filters**: Sprint selector, Date range, MoSCoW priority  
**Interactivity**: Click sprint bar to drill into story details; hover for defect descriptions  

### 2.3 SQL Queries (Data Preparation)

```sql
-- Sprint Velocity (MySQL / JIRA export)
SELECT sprint_name, 
       SUM(story_points) AS velocity,
       COUNT(*) AS stories_delivered
FROM jira_issues
WHERE status = 'Done' AND issue_type = 'Story'
GROUP BY sprint_name
ORDER BY sprint_start_date;

-- Defect Density
SELECT s.sprint_name,
       COUNT(d.id) AS defect_count,
       COUNT(DISTINCT st.id) AS stories_delivered,
       ROUND(COUNT(d.id) * 1.0 / NULLIF(COUNT(DISTINCT st.id), 0), 2) AS density
FROM sprints s
LEFT JOIN jira_issues d ON d.sprint_id = s.id AND d.issue_type = 'Bug'
LEFT JOIN jira_issues st ON st.sprint_id = s.id AND st.issue_type = 'Story' AND st.status = 'Done'
GROUP BY s.sprint_name;

-- Backlog Completion by Priority (AWS Athena)
SELECT priority_moscow,
       SUM(CASE WHEN status = 'Done' THEN story_points ELSE 0 END) AS delivered,
       SUM(story_points) AS total,
       ROUND(SUM(CASE WHEN status = 'Done' THEN story_points ELSE 0 END) * 100.0 / SUM(story_points), 1) AS pct_complete
FROM jira_issues
WHERE issue_type = 'Story'
GROUP BY priority_moscow;
```

---

## 3. Product Metrics KPIs (Power BI Dashboard)

### Dashboard: Xore Product Performance

**Audience**: Product Owner, Stakeholders  
**Refresh**: Daily (automated via MySQL connection)  
**Data Source**: MySQL operational database + Athena analytics

### 3.1 KPI Definitions

#### KPI-M01: Prediction Accuracy (Rolling 7-Day)

| Field | Value |
|-------|-------|
| Definition | Percentage of correct directional predictions over the past 7 days |
| Formula | COUNT(correct) / COUNT(total) x 100 WHERE timestamp >= NOW() - INTERVAL 7 DAY |
| Target | >= 60% |
| Visualization | Line chart (daily accuracy) with 7-day rolling average |
| Source | predictions table (direction vs actual_direction) |

#### KPI-M02: Average Response Latency

| Field | Value |
|-------|-------|
| Definition | Average time from chat query submission to response render |
| Formula | AVG(response_timestamp - query_timestamp) |
| Target | < 5 seconds |
| Visualization | Histogram (latency distribution) + KPI card (current average) |
| Source | Backend API logs |

#### KPI-M03: Agent Success Rate

| Field | Value |
|-------|-------|
| Definition | Percentage of agent executions completing without timeout or error |
| Formula | COUNT(status='complete') / COUNT(total_executions) x 100 per agent |
| Target | >= 95% per agent |
| Visualization | Grouped bar chart (per agent: Complete / Timeout / Error) |
| Source | Backend agent execution logs |

#### KPI-M04: Data Freshness Compliance

| Field | Value |
|-------|-------|
| Definition | Percentage of time each data source is within its expected refresh window |
| Formula | COUNT(on_time_refreshes) / COUNT(total_refresh_cycles) x 100 |
| Target | >= 98% |
| Visualization | Heatmap (source x hour, color = on-time/late) |
| Source | market_data, onchain_metrics, news_sentiment_aggregate timestamps |

#### KPI-M05: Signal Distribution

| Field | Value |
|-------|-------|
| Definition | Distribution of trading signals over time |
| Formula | COUNT per signal type (Strong Buy/Buy/Neutral/Sell/Strong Sell) by day |
| Target | No single signal exceeds 60% of daily outputs (indicates model diversity) |
| Visualization | Stacked area chart (signal type over time) |
| Source | trading_signals table |

#### KPI-M06: Learn Mode Engagement

| Field | Value |
|-------|-------|
| Definition | Number of Learn Mode topic views and completion rate |
| Formula | Views: COUNT(topic_views); Completion: COUNT(completed) / COUNT(total_topics) |
| Target | Engagement increasing week-over-week |
| Visualization | Bar chart (views per topic) + completion funnel |
| Source | Frontend analytics events |

### 3.2 Power BI Dashboard Layout

```
┌──────────────────────────────────────────────────────────┐
│                XORE PRODUCT PERFORMANCE                   │
├────────────┬────────────┬────────────┬───────────────────┤
│ Pred Acc   │ Avg Latency│ Agent Rate │ Data Freshness    │
│ [KPI Card] │ [KPI Card] │ [KPI Card] │ [KPI Card]        │
│  63%       │  3.8s      │  97%       │  99.2%            │
├────────────┴────────────┴────────────┴───────────────────┤
│  Prediction Accuracy Trend (7-Day Rolling)                │
│  [Line Chart - 30 day window]                             │
├──────────────────────────┬───────────────────────────────┤
│  Signal Distribution     │  Agent Success Rate            │
│  [Stacked Area Chart]    │  [Grouped Bar by Agent]        │
├──────────────────────────┼───────────────────────────────┤
│  Response Latency        │  Learn Mode Engagement         │
│  [Histogram]             │  [Bar + Funnel]                │
├──────────────────────────┴───────────────────────────────┤
│  Data Freshness Heatmap (Source x Hour)                   │
│  [Heatmap - 24h x 3 sources]                             │
└──────────────────────────────────────────────────────────┘
```

**Filters**: Asset (BTC/ETH), Date range, Agent name  
**Interactivity**: Click signal bar to see reasoning chain; hover latency bar for percentile detail

### 3.3 SQL Queries (Data Preparation)

```sql
-- Prediction Accuracy (Rolling 7-Day)
SELECT DATE(timestamp) AS pred_date,
       asset,
       COUNT(*) AS total_predictions,
       SUM(CASE WHEN direction = actual_direction THEN 1 ELSE 0 END) AS correct,
       ROUND(SUM(CASE WHEN direction = actual_direction THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 1) AS accuracy_pct
FROM predictions
WHERE actual_direction IS NOT NULL
  AND timestamp >= DATE_SUB(NOW(), INTERVAL 7 DAY)
GROUP BY DATE(timestamp), asset;

-- Signal Distribution
SELECT DATE(timestamp) AS signal_date,
       asset,
       signal,
       COUNT(*) AS signal_count
FROM trading_signals
WHERE timestamp >= DATE_SUB(NOW(), INTERVAL 30 DAY)
GROUP BY DATE(timestamp), asset, signal;

-- Agent Success Rate
SELECT agent_name,
       COUNT(*) AS total_runs,
       SUM(CASE WHEN status = 'complete' THEN 1 ELSE 0 END) AS successful,
       ROUND(SUM(CASE WHEN status = 'complete' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 1) AS success_rate
FROM agent_execution_log
WHERE timestamp >= DATE_SUB(NOW(), INTERVAL 7 DAY)
GROUP BY agent_name;

-- Data Freshness Compliance
SELECT source_name,
       HOUR(expected_refresh_time) AS hour_of_day,
       COUNT(*) AS total_cycles,
       SUM(CASE WHEN actual_refresh_time <= expected_refresh_time + INTERVAL 30 SECOND THEN 1 ELSE 0 END) AS on_time,
       ROUND(SUM(CASE WHEN actual_refresh_time <= expected_refresh_time + INTERVAL 30 SECOND THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 1) AS compliance_pct
FROM data_refresh_log
GROUP BY source_name, HOUR(expected_refresh_time);
```

---

## 4. Reporting Cadence

| Report | Frequency | Audience | Tool |
|--------|-----------|----------|------|
| Sprint Health Snapshot | Bi-weekly (end of sprint) | Product Owner, Tech Lead | Tableau |
| Weekly Velocity Update | Weekly | BA/PM internal tracking | JIRA + Excel |
| Product Performance Daily | Daily (automated) | Product Owner | Power BI |
| Monthly Executive Summary | Monthly | Leadership | Power BI PDF export |
