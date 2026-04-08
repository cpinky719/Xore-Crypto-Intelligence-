# Functional Requirements Specification (FRS): Xore

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-FRS-001 |
| Version | 2.1 |
| Status | Approved |
| Author | Pinky Choudhary, Business Analyst |
| Last Updated | 2025-10-01 |

---

## 1. Introduction

This document translates the business requirements from XORE-BRD-001 into detailed functional specifications. Each functional requirement maps to one or more business requirements and includes input/output specifications, processing logic, and validation rules.

## 2. System Modules

The system is organized into six functional modules:

1. **Data Ingestion Module** (Backend Services)
2. **Agent Orchestration Module** (CrewAI Agents)
3. **Prediction Engine Module** (ML Models)
4. **Chat Interface Module** (Frontend)
5. **Widget & Dashboard Module** (Frontend)
6. **Learn Mode Module** (Frontend + Backend)

---

## 3. Functional Requirements

### 3.1 Data Ingestion Module

#### FR-101: Market Data Service

**Maps to**: BR-01, BR-09

| Field | Specification |
|-------|--------------|
| Source | LiveCoinWatch REST API |
| Endpoint | `/api/v1/market/prices` |
| Refresh Interval | 60 seconds (configurable) |
| Assets | BTC, ETH |

**Input**: API key, asset symbol, currency (USD)

**Processing Logic**:
1. Poll LiveCoinWatch API at configured interval
2. Parse response for price, volume_24h, market_cap, percent_change_24h
3. Validate data completeness (all fields non-null)
4. Store in time-series format with UTC timestamp
5. Publish to internal message queue for agent consumption

**Output**: Structured market data token with fields: `asset`, `price`, `volume_24h`, `market_cap`, `percent_change_24h`, `timestamp`

**Error Handling**: If API returns error or timeout (>10s), use last cached value and set `stale_data: true` flag.

#### FR-102: On-Chain Data Service

**Maps to**: BR-01, BR-09

| Field | Specification |
|-------|--------------|
| Source | Ankr Multichain API |
| Endpoint | `/api/v1/onchain/metrics` |
| Refresh Interval | 5 minutes |
| Assets | BTC, ETH |

**Input**: Blockchain identifier, metric type

**Processing Logic**:
1. Query Ankr API for active addresses, transaction count, gas fees (ETH), hash rate (BTC)
2. Normalize metric values to standard units
3. Compute 7-day moving averages for trend detection
4. Store with UTC timestamp

**Output**: On-chain metrics token with fields: `asset`, `active_addresses`, `tx_count`, `avg_fee`, `hash_rate` (BTC only), `7d_ma_active_addresses`, `timestamp`

#### FR-103: News Sentiment Service

**Maps to**: BR-01, BR-09

| Field | Specification |
|-------|--------------|
| Source | Google Search API |
| Endpoint | `/api/v1/news/sentiment` |
| Refresh Interval | 15 minutes |
| Assets | BTC, ETH |

**Processing Logic**:
1. Execute search queries: "[asset] news", "[asset] market analysis"
2. Filter results to last 24 hours
3. Extract title, snippet, source, publish date
4. Run sentiment classification on each result (positive/negative/neutral)
5. Compute aggregate sentiment score (-1.0 to +1.0)
6. Identify dominant themes via keyword extraction

**Output**: News sentiment token with fields: `asset`, `sentiment_score`, `article_count`, `dominant_themes[]`, `top_articles[]`, `timestamp`

---

### 3.2 Agent Orchestration Module

#### FR-201: Market Analyst Agent

**Maps to**: BR-02

**Input**: Market data token (FR-101)

**Processing Logic**:
1. Receive market data token from data ingestion pipeline
2. Analyze price action: trend direction, momentum, volatility
3. Compare current metrics against 7-day, 30-day, and 90-day historical averages
4. Generate technical analysis summary with support/resistance levels
5. Assign market condition label: Bullish, Bearish, Sideways, Volatile

**Output**: Market analysis token with fields: `asset`, `trend`, `momentum_score`, `volatility_index`, `support_level`, `resistance_level`, `market_condition`, `confidence`, `summary_text`

#### FR-202: On-Chain Analyst Agent

**Maps to**: BR-02

**Input**: On-chain metrics token (FR-102)

**Processing Logic**:
1. Receive on-chain metrics token
2. Evaluate network health: active address trend, transaction volume trend
3. Detect anomalies: sudden spikes in active addresses or gas fees
4. Compare metrics against historical baselines
5. Generate network health assessment

**Output**: On-chain analysis token with fields: `asset`, `network_health_score`, `address_trend`, `volume_trend`, `anomalies[]`, `assessment_text`

#### FR-203: News Analyst Agent

**Maps to**: BR-02

**Input**: News sentiment token (FR-103)

**Processing Logic**:
1. Receive news sentiment token
2. Evaluate sentiment distribution across articles
3. Identify sentiment shift (improving/declining vs. 24h prior)
4. Flag high-impact events (regulatory, exchange, institutional)
5. Generate sentiment summary with key themes

**Output**: News analysis token with fields: `asset`, `overall_sentiment`, `sentiment_shift`, `high_impact_events[]`, `key_themes[]`, `summary_text`

#### FR-204: Trading Strategist Agent

**Maps to**: BR-02

**Input**: Market analysis token (FR-201), On-chain analysis token (FR-202), News analysis token (FR-203), Prediction output (FR-301)

**Processing Logic**:
1. Receive all agent analysis tokens and prediction model output
2. Weight each signal source: Market (30%), On-Chain (20%), News (20%), Prediction Model (30%)
3. Compute composite signal score (-1.0 to +1.0)
4. Apply risk thresholds: Strong Buy (>0.6), Buy (0.3 to 0.6), Neutral (-0.3 to 0.3), Sell (-0.6 to -0.3), Strong Sell (<-0.6)
5. Generate unified trading insight with reasoning chain

**Output**: Trading signal token with fields: `asset`, `signal` (Strong Buy/Buy/Neutral/Sell/Strong Sell), `composite_score`, `signal_weights{}`, `reasoning_chain[]`, `confidence`, `timestamp`

#### FR-205: Agent Orchestration (CrewAI)

**Maps to**: BR-02

**Processing Logic**:
1. Initialize all four agents at system startup
2. Trigger analysis cycle on data refresh or user query
3. Execute agents in dependency order: Data agents (parallel) then Trading Strategist
4. Manage inter-agent token passing
5. Handle agent timeout (30s max per agent) with partial result fallback
6. Log all agent interactions for audit

---

### 3.3 Prediction Engine Module

#### FR-301: Hybrid Transformer-GRU Price Prediction

**Maps to**: BR-03

| Field | Specification |
|-------|--------------|
| Architecture | Transformer encoder + GRU decoder |
| Input Window | 30 days of daily OHLCV data |
| Prediction Horizon | 24 hours (directional: up/down) |
| Target Accuracy | 60%+ directional accuracy |
| Assets | BTC, ETH (separate models) |

**Processing Logic**:
1. Retrieve 30-day historical OHLCV data for target asset
2. Normalize features using min-max scaling
3. Pass through Transformer encoder for pattern extraction
4. Feed encoded representation into GRU decoder
5. Output probability distribution: P(up), P(down)
6. Select direction with higher probability as prediction
7. Confidence = max(P(up), P(down))

**Output**: Prediction token with fields: `asset`, `direction` (up/down), `confidence`, `p_up`, `p_down`, `model_version`, `timestamp`

#### FR-302: Model Retraining Pipeline

**Maps to**: BR-03

**Processing Logic**:
1. Scheduled weekly batch job (configurable)
2. Pull latest historical data from LiveCoinWatch
3. Split data: 80% train, 10% validation, 10% test
4. Train model with early stopping on validation loss
5. Evaluate directional accuracy on test set
6. If accuracy >= threshold, promote model; otherwise, retain previous version
7. Log training metrics to Jupyter notebook archive

---

### 3.4 Chat Interface Module

#### FR-401: Chat Input Processing

**Maps to**: BR-04

**Processing Logic**:
1. Accept free-text user input via chat text field
2. Pass input to local LLM for intent classification
3. Route to appropriate agent(s) based on intent
4. Display typing indicator during processing
5. Render response with mixed content (text + structured data)

**Supported Intents**: `market_query`, `prediction_query`, `onchain_query`, `news_query`, `general_analysis`, `learn_topic`, `greeting`

#### FR-402: Chat Response Rendering

**Maps to**: BR-04

**Response Types**:
- Text: Natural language analysis paragraphs
- Data Card: Structured metric display (price, volume, sentiment score)
- Chart: Inline price chart or prediction visualization
- Signal Badge: Color-coded trading signal indicator
- Learn Card: Educational content block with expandable details

#### FR-403: Session Management

**Maps to**: BR-04

- Conversation history maintained in browser session storage
- Maximum 100 messages per session
- Session cleared on browser close or manual reset
- No server-side conversation persistence (v1)

---

### 3.5 Widget & Dashboard Module

#### FR-501: Price Ticker Widget

**Maps to**: BR-05

- Displays BTC and ETH current price, 24h change (%), 24h volume
- Updates via WebSocket or polling (60s interval)
- Color coding: green for positive change, red for negative

#### FR-502: Agent Status Panel

**Maps to**: BR-05

- Shows each agent name, status (Idle/Analyzing/Complete/Error), last run timestamp
- Updates in real-time during analysis cycles
- Click-to-expand shows last analysis summary per agent

#### FR-503: Prediction Output Widget

**Maps to**: BR-05

- Displays prediction direction (up/down arrow), confidence percentage, timestamp
- Confidence meter visual: gauge from 0% to 100%
- Historical prediction accuracy displayed as rolling 7-day score

---

### 3.6 Learn Mode Module

#### FR-601: Learn Mode Navigation

**Maps to**: BR-06

- Accessible via "Learn" tab in main navigation
- Topics organized into categories: Market Basics, On-Chain Analytics, AI/ML Concepts, Trading Strategies
- Progress bar showing completed vs. total topics

#### FR-602: Contextual Learning

**Maps to**: BR-06

- When chat response contains technical terms, display inline "Learn More" link
- Link expands to show a brief explanation card
- User can bookmark topics for later review

---

## 4. API Endpoint Summary

| Method | Endpoint | Description | Module |
|--------|----------|-------------|--------|
| GET | `/api/v1/market/prices` | Current market data | Data Ingestion |
| GET | `/api/v1/onchain/metrics` | Current on-chain metrics | Data Ingestion |
| GET | `/api/v1/news/sentiment` | Current news sentiment | Data Ingestion |
| POST | `/api/v1/chat/message` | Process chat input | Chat Interface |
| GET | `/api/v1/agents/status` | Agent status overview | Agent Orchestration |
| GET | `/api/v1/prediction/latest` | Latest prediction output | Prediction Engine |
| GET | `/api/v1/learn/topics` | List learning topics | Learn Mode |
| GET | `/api/v1/learn/topic/{id}` | Get topic content | Learn Mode |
| GET | `/docs` | Swagger API documentation | API |

## 5. Traceability Matrix

| Business Req | Functional Reqs |
|-------------|----------------|
| BR-01 | FR-101, FR-102, FR-103 |
| BR-02 | FR-201, FR-202, FR-203, FR-204, FR-205 |
| BR-03 | FR-301, FR-302 |
| BR-04 | FR-401, FR-402, FR-403 |
| BR-05 | FR-501, FR-502, FR-503 |
| BR-06 | FR-601, FR-602 |
| BR-07 | FR-401 (LLM routing) |
| BR-08 | API Endpoint Summary |
| BR-09 | FR-101, FR-102, FR-103, FR-301 |
| BR-10 | Error handling in all FRs |
