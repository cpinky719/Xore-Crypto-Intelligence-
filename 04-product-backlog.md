# Product Backlog & User Stories: Xore

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-PB-001 |
| Version | 3.0 |
| Author | Pinky Choudhary, Business Analyst / Product Manager |
| Tool | JIRA (migrated to Markdown for repository) |
| Last Updated | 2025-12-10 |

---

## Prioritization Framework

All stories are prioritized using MoSCoW:
- **Must Have (M)**: Core platform functionality; launch blocker if missing
- **Should Have (S)**: Important features that significantly enhance value
- **Could Have (C)**: Nice-to-have features for v1; otherwise deferred to v2
- **Won't Have (W)**: Explicitly excluded from v1 scope

**Story Point Scale**: Fibonacci (1, 2, 3, 5, 8, 13)

---

## Epic 1: Data Ingestion & Pipeline

### US-101: Market Price Data Ingestion
- **As a** trader, **I want** real-time BTC and ETH price data refreshed every 60 seconds, **so that** my analysis is based on current market conditions.
- **Priority**: Must Have | **Points**: 5 | **Sprint**: 1
- **Acceptance Criteria**:
  - LiveCoinWatch API integration returns price, volume, market cap, 24h change
  - Data refreshes every 60 seconds automatically
  - Stale data flag set when API is unreachable; last cached value used
  - Unit tests cover API response parsing and error scenarios

### US-102: On-Chain Metrics Ingestion
- **As a** trader, **I want** on-chain metrics (active addresses, transaction count, fees) for BTC and ETH, **so that** I can assess network health alongside price data.
- **Priority**: Must Have | **Points**: 5 | **Sprint**: 2
- **Acceptance Criteria**:
  - Ankr API integration returns active addresses, tx count, gas fees (ETH), hash rate (BTC)
  - 7-day moving averages computed on ingestion
  - Data refreshes every 5 minutes
  - Missing metrics handled gracefully (nulls logged, partial data still usable)

### US-103: News Sentiment Ingestion
- **As a** trader, **I want** news articles about BTC and ETH collected and scored for sentiment, **so that** I understand market mood without manually reading news.
- **Priority**: Must Have | **Points**: 8 | **Sprint**: 2
- **Acceptance Criteria**:
  - Google Search API returns articles from the last 24 hours
  - Each article scored as positive, negative, or neutral
  - Aggregate sentiment score computed (-1.0 to +1.0)
  - Dominant themes extracted via keyword analysis
  - Refreshes every 15 minutes

### US-104: Data Freshness Indicators
- **As a** user, **I want** to see when data was last updated, **so that** I know if I'm looking at current or stale information.
- **Priority**: Should Have | **Points**: 3 | **Sprint**: 4
- **Acceptance Criteria**:
  - Each data source displays "Last updated: X minutes ago" in the UI
  - Stale data (>2x normal refresh interval) highlighted with a warning icon
  - Tooltip explains the data source and expected refresh rate

---

## Epic 2: Multi-Agent Intelligence System

### US-201: Market Analyst Agent
- **As a** system, **I want** a Market Analyst agent that interprets price data into trend analysis, **so that** the Trading Strategist has structured market context.
- **Priority**: Must Have | **Points**: 8 | **Sprint**: 3
- **Acceptance Criteria**:
  - Agent receives market data tokens and produces market analysis tokens
  - Analysis includes trend direction, momentum score, volatility index, support/resistance
  - Market condition labeled: Bullish, Bearish, Sideways, or Volatile
  - Processing completes within 30 seconds

### US-202: On-Chain Analyst Agent
- **As a** system, **I want** an On-Chain Analyst agent that evaluates blockchain health metrics, **so that** network activity informs trading signals.
- **Priority**: Must Have | **Points**: 5 | **Sprint**: 3
- **Acceptance Criteria**:
  - Agent receives on-chain tokens and produces health assessment tokens
  - Network health score computed on 0-100 scale
  - Anomalies (address spikes, fee surges) flagged automatically
  - Processing completes within 30 seconds

### US-203: News Analyst Agent
- **As a** system, **I want** a News Analyst agent that interprets sentiment data into actionable news intelligence, **so that** market mood informs trading signals.
- **Priority**: Must Have | **Points**: 5 | **Sprint**: 3
- **Acceptance Criteria**:
  - Agent receives news sentiment tokens and produces news analysis tokens
  - Overall sentiment classified with sentiment shift direction
  - High-impact events (regulatory, institutional) flagged
  - Key themes summarized in natural language

### US-204: Trading Strategist Agent
- **As a** system, **I want** a Trading Strategist that synthesizes all agent analyses and the prediction model into a unified signal, **so that** users receive one clear recommendation.
- **Priority**: Must Have | **Points**: 13 | **Sprint**: 3
- **Acceptance Criteria**:
  - Receives analysis tokens from all three agents plus prediction output
  - Applies weighted scoring: Market 30%, On-Chain 20%, News 20%, Prediction 30%
  - Generates signal: Strong Buy, Buy, Neutral, Sell, or Strong Sell
  - Reasoning chain explains which factors contributed to the signal
  - Confidence score accompanies the signal

### US-205: Agent Orchestration & Lifecycle
- **As a** developer, **I want** agents managed through CrewAI with proper lifecycle control, **so that** they initialize, execute, and recover from errors reliably.
- **Priority**: Must Have | **Points**: 8 | **Sprint**: 3
- **Acceptance Criteria**:
  - All agents initialize at system startup
  - Data agents run in parallel; Trading Strategist runs after all complete
  - 30-second timeout per agent with partial-result fallback
  - Agent interactions logged with timestamps

---

## Epic 3: Prediction Engine

### US-301: Transformer-GRU Model Training
- **As a** data scientist, **I want** a trained hybrid Transformer-GRU model for BTC and ETH, **so that** the platform can predict 24-hour price direction.
- **Priority**: Must Have | **Points**: 13 | **Sprint**: 3
- **Acceptance Criteria**:
  - Model architecture: Transformer encoder + GRU decoder
  - Trained on 30-day rolling window of daily OHLCV data
  - Separate models for BTC and ETH
  - Directional accuracy >= 60% on hold-out test set
  - Training reproducible via Jupyter notebook

### US-302: Real-Time Prediction Serving
- **As a** trader, **I want** to see the model's latest price prediction when I open the platform, **so that** I have an AI-generated signal immediately.
- **Priority**: Must Have | **Points**: 5 | **Sprint**: 4
- **Acceptance Criteria**:
  - Latest prediction available via API endpoint
  - Prediction includes direction, confidence, and timestamp
  - Prediction refreshes daily (or on-demand via chat query)
  - API response time under 2 seconds

### US-303: Model Retraining Pipeline
- **As a** data scientist, **I want** an automated weekly retraining pipeline, **so that** the model adapts to recent market conditions.
- **Priority**: Should Have | **Points**: 5 | **Sprint**: 5
- **Acceptance Criteria**:
  - Weekly cron job triggers retraining
  - New model evaluated against current model on test set
  - Model promoted only if accuracy meets threshold
  - Training metrics logged for audit

---

## Epic 4: Chat Interface

### US-401: Chat Text Input & Processing
- **As a** user, **I want** to type questions about crypto markets in natural language, **so that** I don't need to learn a query syntax.
- **Priority**: Must Have | **Points**: 8 | **Sprint**: 4
- **Acceptance Criteria**:
  - Chat input accepts free-text queries
  - Intent classified by local LLM: market, prediction, onchain, news, learn, general
  - Response returned within 5 seconds
  - Typing indicator shown during processing

### US-402: Structured Response Rendering
- **As a** user, **I want** chat responses to include data cards, charts, and signal badges alongside text, **so that** I get both narrative and visual analysis.
- **Priority**: Must Have | **Points**: 8 | **Sprint**: 4
- **Acceptance Criteria**:
  - Text responses rendered as formatted paragraphs
  - Data cards display key metrics in a structured layout
  - Signal badge shows color-coded recommendation (green/yellow/red)
  - Chart widget renders inline price visualization

### US-403: Session History
- **As a** user, **I want** my chat history preserved during my session, **so that** I can scroll back through previous analyses.
- **Priority**: Should Have | **Points**: 3 | **Sprint**: 4
- **Acceptance Criteria**:
  - Messages stored in browser session storage
  - Scrollable history up to 100 messages
  - Session cleared on browser close
  - Manual "Clear Chat" button available

---

## Epic 5: Dashboard Widgets

### US-501: Live Price Ticker
- **As a** user, **I want** a price ticker always visible showing BTC and ETH prices, **so that** I have constant market awareness.
- **Priority**: Should Have | **Points**: 3 | **Sprint**: 4
- **Acceptance Criteria**:
  - Displays current price, 24h change %, 24h volume
  - Green/red color coding for positive/negative change
  - Updates every 60 seconds without page refresh

### US-502: Agent Activity Panel
- **As a** user, **I want** to see which AI agents are currently active, **so that** I understand what analysis is running.
- **Priority**: Should Have | **Points**: 3 | **Sprint**: 4
- **Acceptance Criteria**:
  - Each agent shown with status: Idle, Analyzing, Complete, Error
  - Last run timestamp displayed
  - Expandable to show last analysis summary

### US-503: Prediction Confidence Widget
- **As a** user, **I want** a visual gauge showing the prediction model's confidence, **so that** I can weight the AI signal appropriately.
- **Priority**: Should Have | **Points**: 3 | **Sprint**: 4
- **Acceptance Criteria**:
  - Gauge visual from 0-100% confidence
  - Direction arrow (up/down) with prediction
  - Rolling 7-day accuracy score displayed

---

## Epic 6: Learn Mode

### US-601: Learn Mode Landing Page
- **As a** new user, **I want** an educational section that explains crypto concepts, **so that** I can learn while using the platform.
- **Priority**: Should Have | **Points**: 5 | **Sprint**: 5
- **Acceptance Criteria**:
  - Accessible via "Learn" navigation tab
  - Topics organized: Market Basics, On-Chain, AI/ML, Trading Strategies
  - Progress bar showing completion status
  - Responsive layout for all screen sizes

### US-602: Contextual Learn Links in Chat
- **As a** user, **I want** inline "Learn More" links when technical terms appear in chat responses, **so that** I can understand the analysis without leaving the conversation.
- **Priority**: Could Have | **Points**: 5 | **Sprint**: 5
- **Acceptance Criteria**:
  - Technical terms automatically detected and linked
  - Click expands an inline explanation card
  - Card includes a "Bookmark" option for later review

---

## Epic 7: Local LLM & Infrastructure

### US-701: vLLM Server Setup
- **As a** developer, **I want** vLLM configured to serve the local LLM, **so that** all inference runs locally without external API dependencies.
- **Priority**: Must Have | **Points**: 5 | **Sprint**: 1
- **Acceptance Criteria**:
  - `start_vllm.sh` script initializes vLLM server
  - DeepSeek R1-Distill-Llama-8B loaded from `backend/llm-models/`
  - Health check endpoint confirms model is serving
  - Graceful error message if GPU unavailable

### US-702: FastAPI Backend Scaffolding
- **As a** developer, **I want** the FastAPI application scaffolded with routing, middleware, and error handling, **so that** feature development can proceed on a stable foundation.
- **Priority**: Must Have | **Points**: 5 | **Sprint**: 1
- **Acceptance Criteria**:
  - FastAPI app with CORS middleware configured
  - Router structure matching module boundaries
  - Global exception handler with structured error responses
  - Swagger docs auto-generated at `/docs`

### US-703: Next.js Frontend Scaffolding
- **As a** developer, **I want** the Next.js frontend set up with routing, layout, and API integration layer, **so that** UI features can be built on a stable base.
- **Priority**: Must Have | **Points**: 5 | **Sprint**: 1
- **Acceptance Criteria**:
  - Next.js app with page routing for Chat, Learn, and Dashboard views
  - API client service connecting to FastAPI backend
  - Responsive layout with navigation
  - Development hot-reload functional

### US-704: Startup Orchestration Script
- **As a** developer, **I want** a single `start_all.sh` script that launches backend, frontend, and LLM server, **so that** the entire system starts with one command.
- **Priority**: Should Have | **Points**: 3 | **Sprint**: 6
- **Acceptance Criteria**:
  - Script starts vLLM, FastAPI, and Next.js in correct order
  - Health checks confirm each service is running
  - Logs output to separate files per service
  - Ctrl+C gracefully shuts down all services

---

## Epic 8: Quality & Launch

### US-801: UAT Test Execution
- **As a** BA/PM, **I want** UAT test cases executed and results documented, **so that** we have confidence in launch readiness.
- **Priority**: Must Have | **Points**: 8 | **Sprint**: 6
- **Acceptance Criteria**:
  - All test cases from UAT plan executed
  - Pass rate >= 90%
  - All P0 and P1 defects resolved before launch
  - Test results documented with evidence

### US-802: API Documentation Review
- **As a** developer, **I want** all API endpoints documented and verified, **so that** the Swagger docs are accurate at launch.
- **Priority**: Should Have | **Points**: 3 | **Sprint**: 6
- **Acceptance Criteria**:
  - All endpoints listed with request/response schemas
  - Example requests provided for each endpoint
  - Authentication requirements documented

---

## Backlog Summary

| Epic | Stories | Total Points | Must | Should | Could |
|------|---------|-------------|------|--------|-------|
| Data Ingestion | 4 | 21 | 18 | 3 | 0 |
| Multi-Agent System | 5 | 39 | 39 | 0 | 0 |
| Prediction Engine | 3 | 23 | 18 | 5 | 0 |
| Chat Interface | 3 | 19 | 16 | 3 | 0 |
| Dashboard Widgets | 3 | 9 | 0 | 9 | 0 |
| Learn Mode | 2 | 10 | 0 | 5 | 5 |
| Infrastructure | 4 | 18 | 15 | 3 | 0 |
| Quality & Launch | 2 | 11 | 8 | 3 | 0 |
| **Total** | **26** | **150** | **114** | **31** | **5** |
