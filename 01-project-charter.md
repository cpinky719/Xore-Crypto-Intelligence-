# Project Charter: Xore - AI-Driven Crypto Intelligence Platform

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-PC-001 |
| Version | 1.2 |
| Status | Approved |
| Author | Pinky Choudhary, Business Analyst / Project Manager |
| Last Updated | 2025-11-15 |

---

## 1. Project Purpose

Xore addresses the gap between raw cryptocurrency data and actionable trading intelligence. Retail and semi-professional crypto traders currently rely on fragmented tools, delayed signals, and manual analysis across multiple platforms. Xore consolidates market data, on-chain analytics, news sentiment, and deep learning predictions into a single conversational interface.

The platform uses a multi-agent AI architecture where specialized agents (Market, On-Chain, News, Trading) collaborate autonomously to generate signals, reducing the cognitive load on traders and enabling faster decision-making.

## 2. Business Objectives

- Deliver a unified crypto intelligence platform supporting BTC and ETH analysis
- Reduce average signal generation time from manual multi-tool workflows (estimated 15-30 minutes) to under 60 seconds via automated multi-agent analysis
- Provide educational content through Learn Mode to increase user retention and crypto literacy
- Achieve model prediction accuracy (directional) of 60% or higher on 24-hour BTC/ETH price movement
- Support local LLM deployment (DeepSeek R1, Llama 2) to ensure data privacy and offline capability

## 3. Scope

### In Scope

- Multi-agent system (CrewAI) with four specialized agents: Market Analyst, On-Chain Analyst, News Analyst, Trading Strategist
- Hybrid Transformer-GRU deep learning model for price prediction
- Real-time data integration: LiveCoinWatch (market), Ankr (on-chain), Google Search (news)
- Next.js frontend with chat interface, real-time widgets, and Learn Mode
- FastAPI backend with RESTful API endpoints
- Local LLM integration via vLLM (DeepSeek R1-Distill-Llama-8B, Llama 2)
- Support for BTC and ETH asset analysis
- API documentation via Swagger/OpenAPI (localhost:8000/docs)

### Out of Scope

- Support for altcoins beyond BTC and ETH (future phase)
- Live trading execution or wallet integration
- Mobile native application (web-responsive only for v1)
- Regulatory compliance advisory features
- Multi-language support (English only for v1)

## 4. Stakeholders

| Stakeholder | Role | Interest |
|-------------|------|----------|
| Product Owner | Strategic direction, feature prioritization | High |
| Development Team (3 Engineers) | Backend, frontend, ML model development | High |
| Data Science Lead | Model architecture, training pipeline | High |
| Business Analyst / PM (Pinky Choudhary) | Requirements, planning, delivery management | High |
| End Users (Crypto Traders) | Platform usability, signal accuracy | High |
| UX Designer | Interface design, user flows | Medium |

## 5. Success Criteria

- All 42 user stories delivered and accepted through UAT
- Prediction model achieves 60%+ directional accuracy on validation set
- Chat response latency under 5 seconds for standard queries
- System uptime of 99% during UAT and launch window
- Zero critical (P0) defects at launch

## 6. Constraints

- GPU hardware required for local LLM and model inference (CUDA-compatible)
- Linux-only deployment environment
- API rate limits on LiveCoinWatch and Ankr free tiers
- Team size: 3 developers, 1 data scientist, 1 BA/PM, 1 UX designer
- 16-week delivery timeline

## 7. Assumptions

- LiveCoinWatch, Ankr, and Google Search APIs will remain available and stable throughout development
- GPU resources will be provisioned before Sprint 1
- DeepSeek R1 model weights are freely downloadable from HuggingFace
- End users have basic familiarity with cryptocurrency concepts
- The development team has experience with Python, Next.js, and FastAPI

## 8. High-Level Timeline

```
Week 1-2:   Discovery & Planning (Charter, BRD, Stakeholder Analysis)
Week 3-4:   Requirements & Design (FRS, Data Dictionary, Process Flows, Backlog)
Week 5-6:   Sprint 1 - Foundation (API scaffolding, LLM setup, data pipelines)
Week 7-8:   Sprint 2 - Data Layer (Market/On-Chain/News data services)
Week 9-10:  Sprint 3 - Intelligence (Multi-agent system, prediction model v1)
Week 11-12: Sprint 4 - Interface (Chat UI, widgets, real-time updates)
Week 13-14: Sprint 5 - Education & Polish (Learn Mode, UX refinement)
Week 15-16: Sprint 6 - Launch Prep (UAT, bug fixes, documentation, deploy)
```

## 9. Budget Summary

| Category | Allocation |
|----------|-----------|
| Cloud GPU (Development/Testing) | Infrastructure budget |
| API Subscriptions (LiveCoinWatch, Ankr) | Operational budget |
| Development Team (6 members x 16 weeks) | Personnel budget |
| Tools (JIRA, Confluence, Tableau, Power BI) | Tooling budget |

## 10. Approval

| Name | Role | Date |
|------|------|------|
| [Product Owner] | Sponsor | 2025-09-01 |
| Pinky Choudhary | BA / Project Manager | 2025-09-01 |
| [Tech Lead] | Technical Authority | 2025-09-01 |
