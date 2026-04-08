# UAT Test Plan & Test Cases: Xore

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-UAT-001 |
| Version | 1.0 |
| Author | Pinky Choudhary, Business Analyst / QA Coordinator |
| UAT Sprint | Sprint 6 (Weeks 15-16) |
| Last Updated | 2025-12-08 |

---

## 1. Test Plan Summary

### 1.1 Objective

Validate that the Xore platform meets all business requirements defined in XORE-BRD-001 and functional specifications in XORE-FRS-001 before production launch.

### 1.2 Scope

- All Must Have and Should Have user stories (Epics 1-8)
- End-to-end workflows: data ingestion through chat response delivery
- Cross-browser compatibility: Chrome, Firefox, Safari
- Error handling and graceful degradation scenarios

### 1.3 Entry Criteria

- All Sprint 1-5 stories marked "Done" in JIRA
- Development environment stable with no known P0 defects
- Test data available (live API connections active)
- UAT environment deployed and accessible

### 1.4 Exit Criteria

- Pass rate >= 90%
- All P0 (Critical) and P1 (High) defects resolved
- Product Owner sign-off obtained

### 1.5 UAT Results Summary

| Metric | Value |
|--------|-------|
| Total Test Cases | 50 |
| Passed | 47 |
| Failed | 3 |
| Pass Rate | 94% |
| P0 Defects Found | 0 |
| P1 Defects Found | 2 (resolved) |
| P3 Defects Found | 1 (deferred) |

---

## 2. Test Cases

### Epic 1: Data Ingestion

#### TC-001: Market Data Loads on Startup

| Field | Value |
|-------|-------|
| Req | BR-01, FR-101 |
| Precondition | System started via start_all.sh; LiveCoinWatch API reachable |
| Steps | 1. Open the web UI at localhost:3000. 2. Observe the price ticker widget. |
| Expected Result | BTC and ETH prices displayed within 60 seconds of page load. Price, 24h volume, and 24h change % all populated. |
| Status | **PASS** |

#### TC-002: Market Data Refreshes Automatically

| Field | Value |
|-------|-------|
| Req | BR-01, FR-101 |
| Steps | 1. Note the current BTC price and timestamp. 2. Wait 90 seconds. 3. Verify the price or timestamp has updated. |
| Expected Result | Data refreshes within 60-second interval. Timestamp updates. |
| Status | **PASS** |

#### TC-003: Stale Data Handling

| Field | Value |
|-------|-------|
| Req | BR-10, FR-101 |
| Precondition | Simulate API failure (disconnect network or block LiveCoinWatch) |
| Steps | 1. Block LiveCoinWatch API. 2. Wait for refresh cycle. 3. Check data freshness indicator. |
| Expected Result | Last cached data displayed. "Data may be stale" warning visible. is_stale flag set in backend. |
| Status | **PASS** |

#### TC-004: On-Chain Metrics Available for BTC and ETH

| Field | Value |
|-------|-------|
| Req | BR-01, FR-102 |
| Steps | 1. Query "show me on-chain metrics for BTC" in chat. 2. Repeat for ETH. |
| Expected Result | Active addresses, transaction count, and fees displayed for both assets. BTC includes hash rate. ETH includes gas fees. |
| Status | **PASS** |

#### TC-005: News Sentiment Aggregation

| Field | Value |
|-------|-------|
| Req | BR-01, FR-103 |
| Steps | 1. Query "what's the news sentiment for BTC?" 2. Verify response includes sentiment score, article count, and themes. |
| Expected Result | Aggregate sentiment score (-1.0 to +1.0) displayed. At least 3 articles referenced. Dominant themes listed. |
| Status | **PASS** |

#### TC-006: Data Freshness Indicators Visible

| Field | Value |
|-------|-------|
| Req | BR-01, FR-104 |
| Steps | 1. Hover over data freshness timestamp on each widget. |
| Expected Result | "Last updated: X minutes ago" shown for market data, on-chain, and news. Tooltip shows source name and refresh rate. |
| Status | **PASS** |

---

### Epic 2: Multi-Agent System

#### TC-007: Full Analysis Cycle Completes

| Field | Value |
|-------|-------|
| Req | BR-02, FR-205 |
| Steps | 1. Query "give me a full analysis of BTC." 2. Monitor agent activity panel. |
| Expected Result | All four agents show "Analyzing" then "Complete" status. Response includes market, on-chain, news, and trading analysis. Total time under 30 seconds. |
| Status | **PASS** |

#### TC-008: Trading Signal Generation

| Field | Value |
|-------|-------|
| Req | BR-02, FR-204 |
| Steps | 1. Trigger full analysis. 2. Verify trading signal in response. |
| Expected Result | Signal displayed (Strong Buy/Buy/Neutral/Sell/Strong Sell). Composite score shown. Reasoning chain explains contributing factors. |
| Status | **PASS** |

#### TC-009: Agent Timeout Handling

| Field | Value |
|-------|-------|
| Req | BR-10, FR-205 |
| Precondition | Simulate slow agent (add artificial delay to News Agent > 30s) |
| Steps | 1. Trigger full analysis. 2. Observe behavior after 30-second timeout. |
| Expected Result | Trading Strategist proceeds with partial results from available agents. Response notes which agent timed out. No system crash. |
| Status | **PASS** |

#### TC-010: Market Analyst Output Accuracy

| Field | Value |
|-------|-------|
| Req | BR-02, FR-201 |
| Steps | 1. Query "analyze BTC market trend." 2. Compare output with current market data. |
| Expected Result | Trend direction, momentum score, volatility index, support/resistance levels present. Values are reasonable given current market data. |
| Status | **PASS** |

#### TC-011: On-Chain Analyst Output

| Field | Value |
|-------|-------|
| Req | BR-02, FR-202 |
| Steps | 1. Query "how healthy is the ETH network?" |
| Expected Result | Network health score (0-100), address trend, and any anomalies reported. |
| Status | **PASS** |

#### TC-012: News Analyst Output

| Field | Value |
|-------|-------|
| Req | BR-02, FR-203 |
| Steps | 1. Query "what's the news saying about BTC?" |
| Expected Result | Sentiment classification, sentiment shift direction, key themes, and high-impact events (if any). |
| Status | **PASS** |

---

### Epic 3: Prediction Engine

#### TC-013: Prediction Available on Page Load

| Field | Value |
|-------|-------|
| Req | BR-03, FR-302 |
| Steps | 1. Open the web UI. 2. Check the prediction widget. |
| Expected Result | Latest prediction displayed with direction (up/down), confidence %, and timestamp. |
| Status | **PASS** |

#### TC-014: Prediction via Chat Query

| Field | Value |
|-------|-------|
| Req | BR-03, FR-302 |
| Steps | 1. Query "what's the prediction for ETH?" |
| Expected Result | Response includes direction, confidence, and model version. Prediction matches widget display. |
| Status | **PASS** |

#### TC-015: Prediction Confidence Range

| Field | Value |
|-------|-------|
| Req | BR-03, FR-301 |
| Steps | 1. Check prediction confidence for BTC and ETH over multiple refreshes. |
| Expected Result | Confidence always between 50% and 100%. P(up) + P(down) approximately equals 1.0. |
| Status | **PASS** |

---

### Epic 4: Chat Interface

#### TC-016: Free-Text Query Processing

| Field | Value |
|-------|-------|
| Req | BR-04, FR-401 |
| Steps | 1. Type "How is Bitcoin doing today?" 2. Submit. |
| Expected Result | Response returned within 5 seconds. Response contains relevant market analysis. Typing indicator shown during processing. |
| Status | **PASS** |

#### TC-017: Structured Response with Data Card

| Field | Value |
|-------|-------|
| Req | BR-04, FR-402 |
| Steps | 1. Query "show me BTC price data." |
| Expected Result | Response includes a structured data card with price, volume, market cap, and 24h change. |
| Status | **PASS** |

#### TC-018: Signal Badge Display

| Field | Value |
|-------|-------|
| Req | BR-04, FR-402 |
| Steps | 1. Trigger full analysis query. 2. Verify signal badge in response. |
| Expected Result | Color-coded badge displayed: green (buy signals), yellow (neutral), red (sell signals). |
| Status | **PASS** |

#### TC-019: Chat Session History

| Field | Value |
|-------|-------|
| Req | BR-04, FR-403 |
| Steps | 1. Send 5 different queries. 2. Scroll up through chat history. |
| Expected Result | All 5 queries and responses visible in scrollable history. |
| Status | **PASS** |

#### TC-020: Clear Chat Function

| Field | Value |
|-------|-------|
| Req | BR-04, FR-403 |
| Steps | 1. Send messages. 2. Click "Clear Chat" button. |
| Expected Result | Chat history cleared. Fresh conversation started. |
| Status | **PASS** |

#### TC-021: Response Time Under 5 Seconds

| Field | Value |
|-------|-------|
| Req | BR-04, FR-401 |
| Steps | 1. Send 10 different queries. 2. Time each response. |
| Expected Result | Average response time < 5 seconds. No individual response exceeds 10 seconds. |
| Status | **PASS** |

#### TC-022: Unknown Query Handling

| Field | Value |
|-------|-------|
| Req | BR-10 |
| Steps | 1. Type "ajksdflkj random text." 2. Submit. |
| Expected Result | System responds with a helpful fallback message (not an error). Suggests valid query types. |
| Status | **PASS** |

---

### Epic 5: Dashboard Widgets

#### TC-023: Price Ticker Displays Both Assets

| Field | Value |
|-------|-------|
| Req | BR-05, FR-501 |
| Steps | 1. Verify ticker shows BTC and ETH. |
| Expected Result | Both assets displayed with price, 24h change %, and volume. Green/red coloring correct. |
| Status | **PASS** |

#### TC-024: Agent Status Panel Updates

| Field | Value |
|-------|-------|
| Req | BR-05, FR-502 |
| Steps | 1. Trigger analysis. 2. Watch agent panel during execution. |
| Expected Result | Agent statuses transition: Idle -> Analyzing -> Complete. Last run timestamp updates. |
| Status | **PASS** |

#### TC-025: Prediction Confidence Gauge

| Field | Value |
|-------|-------|
| Req | BR-05, FR-503 |
| Steps | 1. View prediction widget. |
| Expected Result | Gauge displays confidence percentage. Direction arrow shown. Rolling 7-day accuracy visible. |
| Status | **PASS** |

#### TC-026: Widget Real-Time Updates (No Page Refresh)

| Field | Value |
|-------|-------|
| Req | BR-05 |
| Steps | 1. Leave page open for 5 minutes. 2. Verify widgets update without manual refresh. |
| Expected Result | Price ticker, agent panel, and prediction widget all update automatically. |
| Status | **PASS** |

---

### Epic 6: Learn Mode

#### TC-027: Learn Mode Landing Page

| Field | Value |
|-------|-------|
| Req | BR-06, FR-601 |
| Steps | 1. Click "Learn" in navigation. |
| Expected Result | Landing page shows four categories with topic listings. Progress bar visible. |
| Status | **PASS** |

#### TC-028: Topic Content Display

| Field | Value |
|-------|-------|
| Req | BR-06, FR-601 |
| Steps | 1. Select a topic from Market Basics category. |
| Expected Result | Full topic content displayed in readable format. |
| Status | **PASS** |

#### TC-029: Contextual Learn Link in Chat

| Field | Value |
|-------|-------|
| Req | BR-06, FR-602 |
| Steps | 1. Query involving technical term (e.g., "what does GRU mean in the prediction model?"). |
| Expected Result | Response includes inline "Learn More" link for the technical term. Link expands to explanation card. |
| Status | **PASS** |

#### TC-030: Learn Mode Progress Tracking

| Field | Value |
|-------|-------|
| Req | BR-06, FR-601 |
| Steps | 1. Read 3 topics. 2. Return to landing page. |
| Expected Result | Progress bar reflects 3 completed topics. Completed topics marked visually. |
| Status | **PASS** |

---

### Epic 7: Infrastructure

#### TC-031: start_all.sh Launches Full System

| Field | Value |
|-------|-------|
| Req | BR-07, US-704 |
| Steps | 1. Run ./start_all.sh from project root. |
| Expected Result | vLLM, FastAPI, and Next.js start in order. Health checks pass. Web UI accessible at localhost:3000. API docs at localhost:8000/docs. |
| Status | **PASS** |

#### TC-032: LLM Responds Without External API

| Field | Value |
|-------|-------|
| Req | BR-07 |
| Steps | 1. Disconnect internet (except localhost). 2. Send a chat query. |
| Expected Result | Local LLM processes the query. Response generated (data agents may show stale data, but LLM inference works). |
| Status | **PASS** |

#### TC-033: API Documentation Accessible

| Field | Value |
|-------|-------|
| Req | BR-08 |
| Steps | 1. Navigate to localhost:8000/docs. |
| Expected Result | Swagger UI loads. All endpoints listed with schemas. |
| Status | **PASS** |

#### TC-034: Asset Switching (BTC to ETH)

| Field | Value |
|-------|-------|
| Req | BR-09 |
| Steps | 1. Query about BTC. 2. Query about ETH. 3. Verify responses are asset-specific. |
| Expected Result | Each response contains data specific to the queried asset. No data mixing between BTC and ETH. |
| Status | **PASS** |

---

### Cross-Cutting: Error Handling & Edge Cases

#### TC-035: Graceful API Failure Recovery

| Field | Value |
|-------|-------|
| Req | BR-10 |
| Steps | 1. Block all external APIs. 2. Send a chat query. |
| Expected Result | System responds with cached/stale data. User informed of data staleness. No crash or 500 error. |
| Status | **PASS** |

#### TC-036: Concurrent User Queries

| Field | Value |
|-------|-------|
| Req | BR-10 |
| Steps | 1. Open two browser tabs. 2. Send queries simultaneously from both. |
| Expected Result | Both queries processed independently. No cross-contamination of responses. |
| Status | **PASS** |

#### TC-037: Long Query Input

| Field | Value |
|-------|-------|
| Req | BR-04 |
| Steps | 1. Paste a 500-character query into chat. |
| Expected Result | Query processed without truncation error. Response relevant to the query content. |
| Status | **PASS** |

#### TC-038: Browser Compatibility - Chrome

| Field | Value |
|-------|-------|
| Steps | 1. Open Xore in Chrome. 2. Execute TC-016, TC-023, TC-027. |
| Expected Result | All pass. Layout renders correctly. |
| Status | **PASS** |

#### TC-039: Browser Compatibility - Firefox

| Field | Value |
|-------|-------|
| Steps | 1. Open Xore in Firefox. 2. Execute TC-016, TC-023, TC-027. |
| Expected Result | All pass. Layout renders correctly. |
| Status | **PASS** |

#### TC-040: Browser Compatibility - Safari

| Field | Value |
|-------|-------|
| Steps | 1. Open Xore in Safari. 2. Execute TC-016, TC-023, TC-027. |
| Expected Result | All pass. Layout renders correctly. |
| Status | **PASS** |

---

### Failed Test Cases

#### TC-041: News Agent Timeout on Large Result Set (FAILED -> FIXED)

| Field | Value |
|-------|-------|
| Steps | 1. Query news during high-volume news cycle (>50 articles). |
| Expected Result | News agent completes within 30 seconds. |
| Actual Result | News agent timed out at 30s due to processing 50+ articles sequentially. |
| Defect | XORE-BUG-014 (P1) - News agent pagination needed |
| Resolution | Implemented article cap (top 20 by relevance) and parallel sentiment scoring. Fixed in Sprint 6. |
| Retest Status | **PASS** |

#### TC-042: Prediction Widget Not Updating After Model Refresh (FAILED -> FIXED)

| Field | Value |
|-------|-------|
| Steps | 1. Trigger model retrain. 2. Check prediction widget. |
| Expected Result | Widget shows new prediction from retrained model. |
| Actual Result | Widget continued showing old prediction. Required page refresh. |
| Defect | XORE-BUG-015 (P1) - WebSocket not pushing prediction updates after retrain |
| Resolution | Added prediction refresh event to WebSocket channel. Fixed in Sprint 6. |
| Retest Status | **PASS** |

#### TC-043: Chat Session Clear Not Working (FAILED -> FIXED)

| Field | Value |
|-------|-------|
| Steps | 1. Send messages. 2. Click Clear Chat. 3. Observe. |
| Expected Result | All messages cleared. |
| Actual Result | Messages cleared visually but session storage not purged; messages reappeared on scroll. |
| Defect | XORE-BUG-016 (P1) - Session storage clear incomplete |
| Resolution | Added sessionStorage.clear() call alongside UI state reset. Fixed in Sprint 6. |
| Retest Status | **PASS** |

---

### Deferred

#### TC-044: Learn Mode Mobile Layout (DEFERRED)

| Field | Value |
|-------|-------|
| Steps | 1. Open Learn Mode on mobile viewport (375px width). |
| Expected Result | Content readable. Progress bar adapts. |
| Actual Result | Minor alignment issue on progress bar at 375px. Content readable but bar overflows by ~10px. |
| Defect | XORE-BUG-017 (P3) - Cosmetic; deferred to post-launch |

---

## 3. UAT Sign-Off

| Approver | Role | Date | Signature |
|----------|------|------|-----------|
| [Product Owner] | Sponsor / Acceptance Authority | 2025-12-10 | Approved |
| Pinky Choudhary | BA / UAT Coordinator | 2025-12-10 | Approved |
| [Tech Lead] | Technical Authority | 2025-12-10 | Approved |
