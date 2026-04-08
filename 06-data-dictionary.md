# Data Dictionary: Xore

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-DD-001 |
| Version | 1.3 |
| Author | Pinky Choudhary, Business Analyst |
| Database Systems | MySQL (primary), AWS Athena (analytics) |
| Last Updated | 2025-11-20 |

---

## 1. Overview

This document defines the data structures, field definitions, data types, and relationships used across the Xore platform. It serves as the single source of truth for all backend data models and inter-agent communication tokens.

---

## 2. Database Schema: MySQL (Operational)

### 2.1 Table: `market_data`

Stores real-time and historical market price data from LiveCoinWatch.

| Column | Data Type | Nullable | Default | Description |
|--------|----------|----------|---------|-------------|
| id | BIGINT (PK) | No | AUTO_INCREMENT | Unique record identifier |
| asset | VARCHAR(10) | No | — | Asset symbol (BTC, ETH) |
| price_usd | DECIMAL(18,8) | No | — | Current price in USD |
| volume_24h | DECIMAL(20,2) | No | — | 24-hour trading volume in USD |
| market_cap | DECIMAL(20,2) | No | — | Total market capitalization in USD |
| pct_change_24h | DECIMAL(8,4) | No | — | 24-hour price change percentage |
| source | VARCHAR(50) | No | 'livecoinwatch' | Data provider name |
| is_stale | BOOLEAN | No | FALSE | True if data is from cache due to API failure |
| timestamp | DATETIME | No | CURRENT_TIMESTAMP | UTC timestamp of data capture |

**Index**: `idx_market_asset_ts` on (`asset`, `timestamp`)

### 2.2 Table: `onchain_metrics`

Stores blockchain on-chain metrics from Ankr.

| Column | Data Type | Nullable | Default | Description |
|--------|----------|----------|---------|-------------|
| id | BIGINT (PK) | No | AUTO_INCREMENT | Unique record identifier |
| asset | VARCHAR(10) | No | — | Asset symbol (BTC, ETH) |
| active_addresses | INT | No | — | Number of unique active addresses |
| tx_count | INT | No | — | Transaction count in period |
| avg_fee_usd | DECIMAL(12,6) | Yes | NULL | Average transaction fee in USD |
| hash_rate | DECIMAL(20,4) | Yes | NULL | Network hash rate (BTC only, TH/s) |
| ma_7d_active_addr | DECIMAL(12,2) | Yes | NULL | 7-day moving average of active addresses |
| source | VARCHAR(50) | No | 'ankr' | Data provider name |
| timestamp | DATETIME | No | CURRENT_TIMESTAMP | UTC timestamp |

**Index**: `idx_onchain_asset_ts` on (`asset`, `timestamp`)

### 2.3 Table: `news_articles`

Stores individual news articles and their sentiment scores.

| Column | Data Type | Nullable | Default | Description |
|--------|----------|----------|---------|-------------|
| id | BIGINT (PK) | No | AUTO_INCREMENT | Unique record identifier |
| asset | VARCHAR(10) | No | — | Related asset symbol |
| title | VARCHAR(500) | No | — | Article headline |
| snippet | TEXT | Yes | NULL | Article excerpt |
| source_url | VARCHAR(1000) | No | — | Original article URL |
| publisher | VARCHAR(200) | Yes | NULL | Publication source name |
| sentiment_score | DECIMAL(4,3) | No | — | Score from -1.000 to +1.000 |
| sentiment_label | ENUM('positive','negative','neutral') | No | — | Classified sentiment |
| published_at | DATETIME | Yes | NULL | Article publication date |
| ingested_at | DATETIME | No | CURRENT_TIMESTAMP | When the record was created |

**Index**: `idx_news_asset_date` on (`asset`, `ingested_at`)

### 2.4 Table: `news_sentiment_aggregate`

Stores computed aggregate sentiment per asset per refresh cycle.

| Column | Data Type | Nullable | Default | Description |
|--------|----------|----------|---------|-------------|
| id | BIGINT (PK) | No | AUTO_INCREMENT | Unique record identifier |
| asset | VARCHAR(10) | No | — | Asset symbol |
| sentiment_score | DECIMAL(4,3) | No | — | Aggregate score (-1.000 to +1.000) |
| article_count | INT | No | — | Number of articles in the aggregation |
| dominant_themes | JSON | Yes | NULL | Array of top keyword themes |
| timestamp | DATETIME | No | CURRENT_TIMESTAMP | UTC timestamp of aggregation |

### 2.5 Table: `predictions`

Stores model prediction outputs.

| Column | Data Type | Nullable | Default | Description |
|--------|----------|----------|---------|-------------|
| id | BIGINT (PK) | No | AUTO_INCREMENT | Unique record identifier |
| asset | VARCHAR(10) | No | — | Asset symbol |
| direction | ENUM('up','down') | No | — | Predicted price direction |
| confidence | DECIMAL(5,4) | No | — | Confidence score (0.0000 to 1.0000) |
| p_up | DECIMAL(5,4) | No | — | Probability of upward movement |
| p_down | DECIMAL(5,4) | No | — | Probability of downward movement |
| model_version | VARCHAR(50) | No | — | Model identifier/version |
| actual_direction | ENUM('up','down') | Yes | NULL | Actual outcome (filled retroactively) |
| timestamp | DATETIME | No | CURRENT_TIMESTAMP | Prediction generation time |

**Index**: `idx_pred_asset_ts` on (`asset`, `timestamp`)

### 2.6 Table: `trading_signals`

Stores the Trading Strategist agent's composite output.

| Column | Data Type | Nullable | Default | Description |
|--------|----------|----------|---------|-------------|
| id | BIGINT (PK) | No | AUTO_INCREMENT | Unique record identifier |
| asset | VARCHAR(10) | No | — | Asset symbol |
| signal | ENUM('strong_buy','buy','neutral','sell','strong_sell') | No | — | Trading recommendation |
| composite_score | DECIMAL(5,4) | No | — | Weighted composite (-1.0 to +1.0) |
| weight_market | DECIMAL(3,2) | No | 0.30 | Market signal weight used |
| weight_onchain | DECIMAL(3,2) | No | 0.20 | On-chain signal weight |
| weight_news | DECIMAL(3,2) | No | 0.20 | News signal weight |
| weight_prediction | DECIMAL(3,2) | No | 0.30 | Prediction signal weight |
| reasoning | JSON | No | — | Array of reasoning chain steps |
| confidence | DECIMAL(5,4) | No | — | Overall confidence score |
| timestamp | DATETIME | No | CURRENT_TIMESTAMP | Signal generation time |

### 2.7 Table: `learn_topics`

Stores educational content for Learn Mode.

| Column | Data Type | Nullable | Default | Description |
|--------|----------|----------|---------|-------------|
| id | INT (PK) | No | AUTO_INCREMENT | Topic identifier |
| category | ENUM('market_basics','onchain','ai_ml','trading_strategies') | No | — | Topic category |
| title | VARCHAR(200) | No | — | Topic title |
| summary | VARCHAR(500) | No | — | Brief topic summary |
| content | TEXT | No | — | Full educational content (Markdown) |
| order_index | INT | No | — | Display order within category |
| created_at | DATETIME | No | CURRENT_TIMESTAMP | Creation timestamp |

---

## 3. Inter-Agent Communication Tokens

These JSON structures are used for agent-to-agent data passing within CrewAI.

### 3.1 Market Data Token

```json
{
  "token_type": "market_data",
  "asset": "BTC",
  "price": 67452.18,
  "volume_24h": 28500000000.00,
  "market_cap": 1325000000000.00,
  "pct_change_24h": 2.34,
  "is_stale": false,
  "timestamp": "2025-11-15T14:30:00Z"
}
```

### 3.2 Market Analysis Token

```json
{
  "token_type": "market_analysis",
  "asset": "BTC",
  "trend": "bullish",
  "momentum_score": 0.72,
  "volatility_index": 0.45,
  "support_level": 65000.00,
  "resistance_level": 70000.00,
  "market_condition": "Bullish",
  "confidence": 0.78,
  "summary_text": "BTC showing strong bullish momentum..."
}
```

### 3.3 On-Chain Analysis Token

```json
{
  "token_type": "onchain_analysis",
  "asset": "BTC",
  "network_health_score": 82,
  "address_trend": "increasing",
  "volume_trend": "stable",
  "anomalies": [],
  "assessment_text": "Network health remains strong..."
}
```

### 3.4 News Analysis Token

```json
{
  "token_type": "news_analysis",
  "asset": "BTC",
  "overall_sentiment": "positive",
  "sentiment_shift": "improving",
  "high_impact_events": ["institutional_adoption"],
  "key_themes": ["ETF inflows", "mining efficiency"],
  "summary_text": "Sentiment has shifted positively..."
}
```

### 3.5 Trading Signal Token

```json
{
  "token_type": "trading_signal",
  "asset": "BTC",
  "signal": "buy",
  "composite_score": 0.48,
  "signal_weights": {
    "market": 0.30,
    "onchain": 0.20,
    "news": 0.20,
    "prediction": 0.30
  },
  "reasoning_chain": [
    "Market trend is bullish with strong momentum",
    "On-chain health is above average",
    "News sentiment is positive and improving",
    "Prediction model forecasts upward movement with 65% confidence"
  ],
  "confidence": 0.72,
  "timestamp": "2025-11-15T14:35:00Z"
}
```

---

## 4. AWS Athena (Analytics Layer)

For analytical queries, data is periodically exported to S3 and queryable via Athena.

| Athena Table | Source MySQL Table | Partition | Use Case |
|-------------|-------------------|-----------|----------|
| `xore_analytics.market_data` | `market_data` | By date | Historical price trend analysis |
| `xore_analytics.predictions` | `predictions` | By date | Model accuracy tracking |
| `xore_analytics.trading_signals` | `trading_signals` | By date | Signal distribution analysis |

---

## 5. Data Retention Policy

| Data Type | Retention Period | Archive Strategy |
|-----------|-----------------|-----------------|
| Market Data | 1 year (operational), indefinite (Athena) | Daily export to S3 |
| On-Chain Metrics | 1 year (operational), indefinite (Athena) | Daily export to S3 |
| News Articles | 90 days (operational) | Not archived |
| Predictions | Indefinite (both) | Daily export to S3 |
| Trading Signals | Indefinite (both) | Daily export to S3 |
| Chat Sessions | Session only (browser) | Not persisted server-side |
