# Business Requirements Document (BRD): Xore

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-BRD-001 |
| Version | 2.0 |
| Status | Approved |
| Author | Pinky Choudhary, Business Analyst |
| Reviewed By | Product Owner, Tech Lead |
| Last Updated | 2025-09-15 |

---

## 1. Executive Summary

Xore is a crypto intelligence platform that automates the collection, analysis, and synthesis of market data, on-chain metrics, and news sentiment to generate trading insights. This document captures the business requirements that drive the platform's design and functionality.

## 2. Business Context

### 2.1 Problem Statement

Cryptocurrency traders currently face three core challenges:

1. **Data Fragmentation**: Market prices, on-chain activity, and news signals live across dozens of disconnected tools and websites. Synthesizing these into a coherent view takes 15-30 minutes per decision cycle.
2. **Signal Noise**: Raw data feeds contain significant noise. Without analytical models, distinguishing meaningful patterns from random fluctuation requires deep technical expertise.
3. **Steep Learning Curve**: New entrants to crypto trading lack accessible educational resources integrated into the analysis workflow.

### 2.2 Proposed Solution

A unified platform where specialized AI agents autonomously gather, analyze, and synthesize data from multiple sources, delivering insights through a conversational interface. An integrated Learn Mode provides contextual education.

### 2.3 Business Drivers

- Growing retail participation in crypto markets demands accessible intelligence tools
- AI/ML maturity enables automated multi-source analysis at scale
- Privacy-conscious users require local LLM deployment options
- Educational integration improves user retention and reduces churn

## 3. Business Requirements

### BR-01: Multi-Source Data Aggregation

**Priority**: Must Have (MoSCoW)

The system shall aggregate real-time data from at least three distinct source categories: market price data, blockchain on-chain metrics, and news/sentiment signals.

**Acceptance Criteria**:
- Market data refreshes at minimum every 60 seconds from LiveCoinWatch
- On-chain metrics available for BTC and ETH via Ankr
- News sentiment derived from Google Search results within the last 24 hours
- Data freshness timestamp visible to users

### BR-02: Autonomous Multi-Agent Analysis

**Priority**: Must Have

The system shall employ specialized AI agents that independently analyze their assigned data domains and collaboratively produce unified trading insights.

**Acceptance Criteria**:
- Four distinct agents operational: Market Analyst, On-Chain Analyst, News Analyst, Trading Strategist
- Each agent produces independent analysis tokens
- Trading Strategist synthesizes all agent outputs into a unified signal
- Agent collaboration visible in the UI via status indicators

### BR-03: Deep Learning Price Prediction

**Priority**: Must Have

The system shall provide directional price predictions for BTC and ETH using a hybrid deep learning model.

**Acceptance Criteria**:
- Hybrid Transformer-GRU model trained on historical price data
- Prediction horizon: 24-hour directional movement (up/down)
- Directional accuracy of 60% or higher on validation dataset
- Prediction confidence score included with each output
- Model retraining pipeline documented and reproducible

### BR-04: Conversational Interface

**Priority**: Must Have

Users shall interact with the system through a natural-language chat interface that accepts queries and returns structured analysis.

**Acceptance Criteria**:
- Chat interface supports free-text input
- Response time under 5 seconds for standard queries
- Responses include structured data (charts, metrics) alongside natural language
- Conversation history persisted within a session

### BR-05: Real-Time Dashboard Widgets

**Priority**: Should Have

The UI shall display real-time widgets showing key market metrics, agent status, and prediction outputs.

**Acceptance Criteria**:
- Price ticker widget with live BTC/ETH prices
- Agent activity status panel
- Prediction output widget with confidence meter
- Widgets update without full page refresh

### BR-06: Educational Learn Mode

**Priority**: Should Have

The system shall include an educational module that explains crypto concepts, trading strategies, and how the AI agents work.

**Acceptance Criteria**:
- Contextual learning content triggered by user queries
- Standalone Learn Mode section accessible from navigation
- Content covers: market basics, on-chain metrics explanation, how AI models generate predictions
- Progress tracking for completed topics

### BR-07: Local LLM Deployment

**Priority**: Must Have

The system shall support running language models locally via vLLM for privacy and offline operation.

**Acceptance Criteria**:
- Compatible with DeepSeek R1-Distill-Llama-8B and Llama 2
- Model loading and inference managed through vLLM
- Startup script (`start_vllm.sh`) automates model server initialization
- System functional without external API calls for LLM inference

### BR-08: API Documentation

**Priority**: Should Have

All backend endpoints shall be documented via auto-generated API docs.

**Acceptance Criteria**:
- Swagger/OpenAPI docs available at `/docs` endpoint
- All request/response schemas documented
- Authentication requirements specified per endpoint

### BR-09: Asset Coverage

**Priority**: Must Have

The platform shall support analysis of Bitcoin (BTC) and Ethereum (ETH).

**Acceptance Criteria**:
- All agents capable of analyzing both BTC and ETH
- User can switch between assets in the UI
- Asset-specific data pipelines operational for both tokens

### BR-10: System Reliability

**Priority**: Must Have

The platform shall maintain high availability and graceful error handling.

**Acceptance Criteria**:
- System uptime of 99% during operating hours
- Graceful fallback when external APIs are unreachable
- Error messages displayed to user in plain language
- Backend logging captures all errors with timestamps and context

## 4. Non-Functional Requirements

| Requirement | Specification |
|-------------|--------------|
| Performance | Chat response under 5s; widget refresh under 2s |
| Scalability | Architecture supports future addition of altcoins |
| Security | Local LLM option; no user data sent to external LLM APIs |
| Usability | Responsive web design; intuitive chat-first interface |
| Maintainability | Modular backend; agents independently deployable |
| Compatibility | Linux with CUDA GPU; Chrome/Firefox/Safari browsers |

## 5. Data Requirements

| Data Source | Provider | Refresh Rate | Assets |
|-------------|----------|-------------|--------|
| Market Prices | LiveCoinWatch API | 60 seconds | BTC, ETH |
| On-Chain Metrics | Ankr API | 5 minutes | BTC, ETH |
| News Sentiment | Google Search API | 15 minutes | BTC, ETH |
| Historical Prices | LiveCoinWatch API | Daily batch | BTC, ETH |

## 6. Dependencies

- LiveCoinWatch API availability and rate limits
- Ankr API availability and rate limits
- Google Search API availability
- HuggingFace model repository access (initial setup only)
- CUDA-compatible GPU hardware

## 7. Glossary

| Term | Definition |
|------|-----------|
| CrewAI | Framework for orchestrating multiple AI agents |
| GRU | Gated Recurrent Unit, a type of recurrent neural network |
| Transformer | Neural network architecture using self-attention mechanisms |
| vLLM | High-throughput LLM serving engine |
| On-Chain Metrics | Data derived directly from blockchain transactions |
| Directional Accuracy | Percentage of correct up/down predictions |
| MoSCoW | Prioritization framework: Must, Should, Could, Won't |
