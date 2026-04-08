# Tableau Dashboard Specification: Xore Project Health Tracker

## Dashboard Overview

| Field | Value |
|-------|-------|
| Dashboard Name | Xore Project Health Tracker |
| Tool | Tableau Desktop / Tableau Public |
| Audience | Product Owner, Tech Lead, BA/PM |
| Refresh Cadence | Bi-weekly (end of each sprint) |
| Data Sources | JIRA Export (CSV), Sprint Tracker (Google Sheets) |

---

## Data Source Configuration

### Source 1: JIRA Export (jira_issues.csv)

| Column | Type | Description |
|--------|------|-------------|
| issue_key | String | JIRA issue ID (e.g., XORE-101) |
| issue_type | String | Story, Bug, Task, Tech Debt |
| summary | String | Issue title |
| status | String | To Do, In Progress, Done |
| priority_moscow | String | Must Have, Should Have, Could Have |
| story_points | Integer | Fibonacci estimate |
| sprint_name | String | Sprint 1 through Sprint 6 |
| sprint_start_date | Date | Sprint start date |
| sprint_end_date | Date | Sprint end date |
| assignee | String | Team member name |
| created_date | Date | Issue creation date |
| resolved_date | Date | Issue resolution date (null if open) |
| severity | String | P0, P1, P2, P3 (bugs only) |
| epic | String | Parent epic name |

### Source 2: Sprint Tracker (sprint_tracker.gsheet)

| Column | Type | Description |
|--------|------|-------------|
| sprint_name | String | Sprint identifier |
| committed_points | Integer | Points committed at sprint planning |
| delivered_points | Integer | Points delivered at sprint review |
| capacity_points | Integer | Team capacity for the sprint |
| start_date | Date | Sprint start |
| end_date | Date | Sprint end |

**Join**: Inner join on `sprint_name`

---

## Sheet Specifications

### Sheet 1: Sprint Velocity (Bar Chart)

**Purpose**: Track team delivery velocity across sprints to identify trends and capacity alignment.

| Setting | Value |
|---------|-------|
| Chart Type | Grouped bar chart |
| X-Axis | sprint_name (ordered chronologically) |
| Y-Axis | SUM(story_points) |
| Color | Blue = Delivered, Light Gray = Committed |
| Reference Line | Average velocity (horizontal line) |
| Tooltip | Sprint name, committed points, delivered points, delta |
| Filter | issue_type = 'Story' OR 'Tech Debt' |

**Calculated Fields**:
```
// Velocity Delta
[delivered_points] - [committed_points]

// Velocity Trend (table calc)
WINDOW_AVG(SUM([delivered_points]), -2, 0)  // 3-sprint rolling average
```

### Sheet 2: Sprint Burndown (Line Chart)

**Purpose**: Visualize remaining work within the current/selected sprint against the ideal burndown.

| Setting | Value |
|---------|-------|
| Chart Type | Dual-line chart |
| X-Axis | Day within sprint (1-10 for 2-week sprint) |
| Y-Axis | Remaining story points |
| Line 1 | Ideal burndown (linear from committed to 0) |
| Line 2 | Actual burndown (cumulative completion) |
| Color | Gray dashed = Ideal, Blue solid = Actual |
| Filter | Sprint selector (dropdown) |

**Calculated Fields**:
```
// Ideal Remaining
[committed_points] - ([committed_points] / 10 * [sprint_day])

// Actual Remaining
[committed_points] - RUNNING_SUM(SUM([story_points]))
// Where status = 'Done' and resolved_date <= sprint_day
```

### Sheet 3: Backlog Completion (Stacked Progress Bar)

**Purpose**: Show overall backlog progress segmented by MoSCoW priority.

| Setting | Value |
|---------|-------|
| Chart Type | Horizontal stacked bar |
| X-Axis | Percentage (0-100%) |
| Color Segments | Dark Blue = Must Have Done, Medium Blue = Should Have Done, Light Blue = Could Have Done, Gray = Remaining |
| Label | Percentage complete per priority |

**Calculated Fields**:
```
// Completion Rate by Priority
SUM(IF [status] = 'Done' THEN [story_points] END) /
SUM([story_points])
```

### Sheet 4: Defect Density (Stacked Bar)

**Purpose**: Monitor defect volume and severity per sprint relative to stories delivered.

| Setting | Value |
|---------|-------|
| Chart Type | Stacked bar chart |
| X-Axis | sprint_name |
| Y-Axis | COUNT of bugs |
| Color Stack | Red = P0/P1, Orange = P2, Yellow = P3 |
| Reference Line | 0.5 defects/story threshold |
| Secondary Axis | Defect density ratio (line overlay) |
| Filter | issue_type = 'Bug' |

**Calculated Fields**:
```
// Defect Density
COUNT(IF [issue_type] = 'Bug' THEN [issue_key] END) /
COUNTD(IF [issue_type] = 'Story' AND [status] = 'Done' THEN [issue_key] END)
```

### Sheet 5: UAT Pass Rate (Donut Chart)

**Purpose**: Summarize UAT test execution results.

| Setting | Value |
|---------|-------|
| Chart Type | Donut chart |
| Segments | Green = Pass, Red = Fail, Gray = Blocked/Deferred |
| Center Label | Overall pass rate percentage |
| Values | Pass: 47, Fail: 3, Deferred: 0 |

### Sheet 6: Risk Exposure Over Time (Area Chart)

**Purpose**: Track aggregate risk exposure trend across the project lifecycle.

| Setting | Value |
|---------|-------|
| Chart Type | Area chart |
| X-Axis | Sprint / Date |
| Y-Axis | SUM(risk_score) for active risks |
| Color | Red gradient (darker = higher exposure) |
| Annotations | Key risk events (e.g., "R-003 Resolved") |

**Data**: Manual entry from risk register review log.

---

## Dashboard Layout

```
┌────────────────────────────────────────────────────────────────┐
│  XORE PROJECT HEALTH TRACKER              [Sprint Filter: v]   │
├────────────────────────────┬───────────────────────────────────┤
│                            │                                   │
│   Sprint Velocity          │   Sprint Burndown                 │
│   [Grouped Bar Chart]      │   [Dual Line Chart]               │
│   H: 300px                 │   H: 300px                        │
│                            │                                   │
├────────────────────────────┼───────────────────────────────────┤
│                            │                                   │
│   Backlog Completion       │   Defect Density                  │
│   [Stacked Progress Bar]   │   [Stacked Bar + Line]            │
│   H: 200px                 │   H: 250px                        │
│                            │                                   │
├────────────────────────────┼───────────────────────────────────┤
│                            │                                   │
│   UAT Pass Rate            │   Risk Exposure                   │
│   [Donut Chart]            │   [Area Chart]                    │
│   H: 250px                 │   H: 250px                        │
│                            │                                   │
└────────────────────────────┴───────────────────────────────────┘

Total Dashboard Size: 1200 x 900 px (fits standard monitor)
```

## Interactivity

| Interaction | Behavior |
|-------------|----------|
| Click velocity bar | Drills into story-level detail for that sprint |
| Sprint filter dropdown | Updates all sheets to selected sprint |
| Hover on defect bar | Shows defect descriptions and severity |
| Click backlog segment | Filters to that MoSCoW priority's stories |

## Color Palette

| Element | Hex Code | Usage |
|---------|----------|-------|
| Primary Blue | #2E75B6 | Delivered velocity, actual burndown |
| Light Blue | #9DC3E6 | Should Have, secondary metrics |
| Gray | #D9D9D9 | Committed, ideal burndown, remaining |
| Green | #70AD47 | Pass, on-target |
| Red | #FF4444 | P0/P1 defects, fail |
| Orange | #FFC000 | P2 defects, warning |
| Yellow | #FFE699 | P3 defects |
