# Process Flows & Data Flow Diagrams: Xore

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-PF-001 |
| Version | 1.2 |
| Author | Pinky Choudhary, Business Analyst |
| Tools | MS Visio (original), Mermaid (repository version) |
| Last Updated | 2025-10-15 |

---

## 1. System Context Diagram

Shows Xore's boundaries and external interactions.

```mermaid
graph TB
    subgraph External Systems
        LCW[LiveCoinWatch API]
        ANKR[Ankr Multichain API]
        GSEARCH[Google Search API]
        HF[HuggingFace Model Hub]
    end

    subgraph Xore Platform
        BE[FastAPI Backend]
        FE[Next.js Frontend]
        LLM[vLLM Server]
        DB[(MySQL Database)]
    end

    subgraph Users
        TRADER[Crypto Trader]
    end

    LCW -->|Market Data| BE
    ANKR -->|On-Chain Metrics| BE
    GSEARCH -->|News Articles| BE
    HF -->|Model Weights| LLM

    BE <-->|API Calls| FE
    BE <-->|Inference| LLM
    BE <-->|Read/Write| DB

    TRADER <-->|Chat & Widgets| FE
```

---

## 2. Data Ingestion Pipeline Flow

```mermaid
flowchart TD
    START([Scheduled Trigger]) --> MARKET_FETCH[Fetch Market Data<br/>LiveCoinWatch API]
    START --> ONCHAIN_FETCH[Fetch On-Chain Metrics<br/>Ankr API]
    START --> NEWS_FETCH[Fetch News Articles<br/>Google Search API]

    MARKET_FETCH --> MARKET_VAL{Data Valid?}
    MARKET_VAL -->|Yes| MARKET_STORE[Store in market_data table]
    MARKET_VAL -->|No| MARKET_CACHE[Use Cached Data<br/>Set is_stale = TRUE]
    MARKET_CACHE --> MARKET_STORE

    ONCHAIN_FETCH --> ONCHAIN_VAL{Data Valid?}
    ONCHAIN_VAL -->|Yes| ONCHAIN_CALC[Compute 7-Day MA]
    ONCHAIN_VAL -->|No| ONCHAIN_CACHE[Use Cached Data]
    ONCHAIN_CALC --> ONCHAIN_STORE[Store in onchain_metrics table]
    ONCHAIN_CACHE --> ONCHAIN_STORE

    NEWS_FETCH --> NEWS_FILTER[Filter to Last 24 Hours]
    NEWS_FILTER --> NEWS_SENT[Run Sentiment Classification]
    NEWS_SENT --> NEWS_AGG[Compute Aggregate Score]
    NEWS_AGG --> NEWS_STORE[Store in news_articles &<br/>news_sentiment_aggregate tables]

    MARKET_STORE --> TOKENS[Generate Data Tokens]
    ONCHAIN_STORE --> TOKENS
    NEWS_STORE --> TOKENS

    TOKENS --> AGENTS[Push to Agent Queue]
```

---

## 3. Multi-Agent Analysis Flow

```mermaid
flowchart TD
    TRIGGER([Analysis Trigger<br/>Data Refresh or User Query]) --> ORCHESTRATOR[CrewAI Orchestrator]

    ORCHESTRATOR --> PARALLEL{Execute in Parallel}

    PARALLEL --> MARKET_AGENT[Market Analyst Agent<br/>Receives: Market Data Token]
    PARALLEL --> ONCHAIN_AGENT[On-Chain Analyst Agent<br/>Receives: On-Chain Token]
    PARALLEL --> NEWS_AGENT[News Analyst Agent<br/>Receives: News Sentiment Token]

    MARKET_AGENT --> MARKET_OUT[Market Analysis Token<br/>Trend, Momentum, Volatility]
    ONCHAIN_AGENT --> ONCHAIN_OUT[On-Chain Analysis Token<br/>Network Health, Anomalies]
    NEWS_AGENT --> NEWS_OUT[News Analysis Token<br/>Sentiment, Themes, Events]

    MARKET_OUT --> WAIT{All Agents Complete?}
    ONCHAIN_OUT --> WAIT
    NEWS_OUT --> WAIT

    WAIT -->|Yes| TRADING[Trading Strategist Agent]
    WAIT -->|Timeout 30s| PARTIAL[Use Available Partial Results]
    PARTIAL --> TRADING

    PRED[Prediction Model Output] --> TRADING

    TRADING --> WEIGHT[Apply Signal Weights<br/>Market 30% / OnChain 20%<br/>News 20% / Prediction 30%]
    WEIGHT --> SCORE[Compute Composite Score]
    SCORE --> SIGNAL[Generate Trading Signal<br/>Strong Buy / Buy / Neutral / Sell / Strong Sell]
    SIGNAL --> REASON[Build Reasoning Chain]
    REASON --> STORE_SIGNAL[Store in trading_signals table]
    STORE_SIGNAL --> RESPONSE[Return to User via Chat or Widget]
```

---

## 4. User Chat Query Flow

```mermaid
flowchart TD
    USER([User Types Query]) --> INPUT[Chat Input Component]
    INPUT --> API[POST /api/v1/chat/message]
    API --> LLM_INTENT[Local LLM: Intent Classification]

    LLM_INTENT --> ROUTE{Route by Intent}

    ROUTE -->|market_query| TRIGGER_MARKET[Trigger Market Agent]
    ROUTE -->|prediction_query| TRIGGER_PRED[Fetch Latest Prediction]
    ROUTE -->|onchain_query| TRIGGER_ONCHAIN[Trigger On-Chain Agent]
    ROUTE -->|news_query| TRIGGER_NEWS[Trigger News Agent]
    ROUTE -->|general_analysis| TRIGGER_ALL[Trigger Full Analysis Cycle]
    ROUTE -->|learn_topic| LEARN[Fetch Learn Mode Content]
    ROUTE -->|greeting| GREET[Generate Greeting Response]

    TRIGGER_MARKET --> COMPOSE[Compose Response]
    TRIGGER_PRED --> COMPOSE
    TRIGGER_ONCHAIN --> COMPOSE
    TRIGGER_NEWS --> COMPOSE
    TRIGGER_ALL --> COMPOSE
    LEARN --> COMPOSE
    GREET --> COMPOSE

    COMPOSE --> LLM_RESP[Local LLM: Natural Language Response]
    LLM_RESP --> RENDER{Response Type}

    RENDER -->|Text| TEXT_BLOCK[Text Paragraph]
    RENDER -->|Data| DATA_CARD[Structured Data Card]
    RENDER -->|Signal| SIGNAL_BADGE[Color-Coded Signal Badge]
    RENDER -->|Chart| CHART_WIDGET[Inline Chart]
    RENDER -->|Learn| LEARN_CARD[Learn More Card]

    TEXT_BLOCK --> DISPLAY[Render in Chat Window]
    DATA_CARD --> DISPLAY
    SIGNAL_BADGE --> DISPLAY
    CHART_WIDGET --> DISPLAY
    LEARN_CARD --> DISPLAY

    DISPLAY --> SESSION[Save to Session History]
```

---

## 5. Prediction Model Pipeline Flow

```mermaid
flowchart TD
    subgraph Training Pipeline - Weekly
        HIST[Fetch 1 Year Historical OHLCV] --> SPLIT[Split: 80% Train / 10% Val / 10% Test]
        SPLIT --> NORM[Min-Max Normalization]
        NORM --> WINDOW[Create 30-Day Rolling Windows]
        WINDOW --> TRAIN[Train Transformer-GRU Model]
        TRAIN --> EVAL{Accuracy >= 60%?}
        EVAL -->|Yes| PROMOTE[Promote as Active Model]
        EVAL -->|No| RETAIN[Retain Previous Model]
        PROMOTE --> LOG[Log Metrics to Notebook]
        RETAIN --> LOG
    end

    subgraph Inference Pipeline - On Demand
        RECENT[Fetch Latest 30 Days OHLCV] --> NORM_INF[Normalize with Saved Scaler]
        NORM_INF --> MODEL[Run Through Active Model]
        MODEL --> OUTPUT[Direction + Confidence]
        OUTPUT --> STORE[Store in predictions table]
        STORE --> SERVE[Serve via GET /api/v1/prediction/latest]
    end
```

---

## 6. Learn Mode User Flow

```mermaid
flowchart TD
    USER([User]) --> NAV{Entry Point}

    NAV -->|Click Learn Tab| LANDING[Learn Mode Landing Page]
    NAV -->|Click Learn More in Chat| CONTEXT[Contextual Learn Card]

    LANDING --> CATEGORY[Browse Categories<br/>Market Basics / On-Chain /<br/>AI-ML / Trading Strategies]
    CATEGORY --> TOPIC[Select Topic]
    TOPIC --> CONTENT[Read Topic Content]
    CONTENT --> PROGRESS[Update Progress Tracker]
    CONTENT --> BOOKMARK{Bookmark?}
    BOOKMARK -->|Yes| SAVE_BM[Save to Bookmarks]
    BOOKMARK -->|No| NEXT[Continue to Next Topic]

    CONTEXT --> EXPAND[Expand Inline Explanation]
    EXPAND --> FULL{View Full Topic?}
    FULL -->|Yes| TOPIC
    FULL -->|No| RETURN[Return to Chat]
```

---

## 7. Deployment Architecture Flow

```mermaid
flowchart LR
    subgraph start_all.sh
        S1[1. Start vLLM Server] --> HC1{Health Check OK?}
        HC1 -->|Yes| S2[2. Start FastAPI Backend]
        HC1 -->|No| FAIL1[Exit with Error]
        S2 --> HC2{Health Check OK?}
        HC2 -->|Yes| S3[3. Start Next.js Frontend]
        HC2 -->|No| FAIL2[Exit with Error]
        S3 --> HC3{Health Check OK?}
        HC3 -->|Yes| READY[System Ready<br/>localhost:3000]
        HC3 -->|No| FAIL3[Exit with Error]
    end
```

---

## 8. Diagram Source Files

The Mermaid diagrams above are also available as standalone files in the `diagrams/` directory for use in Visio, Lucidchart, or other tools:

- `diagrams/system-context.mermaid`
- `diagrams/agent-interaction-flow.mermaid`
- `diagrams/data-pipeline.mermaid`
- `diagrams/user-journey.mermaid`
