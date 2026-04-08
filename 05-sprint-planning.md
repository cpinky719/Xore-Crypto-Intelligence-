# Sprint Planning: Xore

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-SP-001 |
| Methodology | Agile Scrum |
| Sprint Duration | 2 weeks |
| Total Sprints | 6 |
| Author | Pinky Choudhary, Scrum Master / BA |
| Tools | JIRA, MS Project |

---

## Team Capacity

| Role | Count | Availability | Points/Sprint |
|------|-------|-------------|---------------|
| Backend Developer | 1 | 100% | ~12 |
| Frontend Developer | 1 | 100% | ~10 |
| ML Engineer | 1 | 100% | ~10 |
| BA / PM (Pinky) | 1 | 100% | Facilitation |
| UX Designer | 1 | 50% | Design support |

**Team Velocity Target**: 30-38 story points per sprint

---

## Sprint 1: Foundation (Weeks 5-6)

**Sprint Goal**: Establish the technical foundation — backend API, frontend shell, and local LLM serving.

| Story ID | Story Title | Points | Assignee | Status |
|----------|------------|--------|----------|--------|
| US-701 | vLLM Server Setup | 5 | ML Engineer | Done |
| US-702 | FastAPI Backend Scaffolding | 5 | Backend Dev | Done |
| US-703 | Next.js Frontend Scaffolding | 5 | Frontend Dev | Done |
| US-101 | Market Price Data Ingestion | 5 | Backend Dev | Done |

**Sprint Capacity**: 20 points | **Committed**: 20 points | **Delivered**: 20 points

**Sprint 1 Review Notes**:
- All stories accepted. LiveCoinWatch integration tested with live data.
- vLLM server startup time is ~45 seconds on test GPU — acceptable.
- Frontend dev environment hot-reload working smoothly.
- Decision: Use WebSocket for real-time widget updates (not polling) starting Sprint 4.

**Sprint 1 Retrospective**:
- What went well: Clean architecture decisions made early; all infra ready on day 1.
- What to improve: API key management needs a centralized config approach — currently scattered.
- Action: Consolidate all API keys into `backend/config/settings.py` before Sprint 2.

---

## Sprint 2: Data Layer (Weeks 7-8)

**Sprint Goal**: Complete all data ingestion pipelines. The system should be able to collect, process, and store data from all three external sources.

| Story ID | Story Title | Points | Assignee | Status |
|----------|------------|--------|----------|--------|
| US-102 | On-Chain Metrics Ingestion | 5 | Backend Dev | Done |
| US-103 | News Sentiment Ingestion | 8 | Backend Dev | Done |
| US-104 | Data Freshness Indicators | 3 | Frontend Dev | Done |
| -- | Tech Debt: Centralize API config | 3 | Backend Dev | Done |
| -- | Design: Chat UI wireframes | 5 | UX Designer | Done |
| -- | Design: Widget layout mockups | 3 | UX Designer | Done |

**Sprint Capacity**: 32 points | **Committed**: 27 points | **Delivered**: 27 points

**Sprint 2 Review Notes**:
- Ankr API integration complete; BTC hash rate metric required additional parsing.
- News sentiment scoring uses a simple keyword-based approach for v1; ML-based scoring deferred.
- UX wireframes reviewed and approved by Product Owner.
- Data freshness indicators render correctly across all data sources.

**Sprint 2 Retrospective**:
- What went well: Parallel work between backend and UX design maximized throughput.
- What to improve: Ankr API documentation was sparse — took extra time for BTC-specific metrics.
- Action: Document all external API quirks in a shared Confluence page.

---

## Sprint 3: Intelligence Core (Weeks 9-10)

**Sprint Goal**: Deliver the multi-agent system and prediction model. This is the highest-risk sprint — the core intelligence layer.

| Story ID | Story Title | Points | Assignee | Status |
|----------|------------|--------|----------|--------|
| US-201 | Market Analyst Agent | 8 | Backend Dev | Done |
| US-202 | On-Chain Analyst Agent | 5 | Backend Dev | Done |
| US-203 | News Analyst Agent | 5 | Backend Dev | Done |
| US-204 | Trading Strategist Agent | 13 | Backend Dev + ML | Done |
| US-205 | Agent Orchestration (CrewAI) | 8 | Backend Dev | Done |
| US-301 | Transformer-GRU Model Training | 13 | ML Engineer | Done |

**Sprint Capacity**: 38 points | **Committed**: 52 points | **Delivered**: 46 points

**Notes**: US-204 (Trading Strategist) and US-301 (Model Training) ran in parallel. The Trading Strategist initially had a bug in signal weight normalization — resolved mid-sprint. Model training achieved 62% directional accuracy on BTC and 59% on ETH. ETH model to be improved in Sprint 5 retraining pipeline.

**Sprint 3 Retrospective**:
- What went well: CrewAI framework worked well for agent orchestration; token-passing pattern clean.
- What to improve: Sprint was over-committed (52 pts vs 38 capacity). Two stories had blockers resolved only in the second week.
- Action: Cap sprint commitment at 38 points going forward. Add buffer stories only if capacity opens.

---

## Sprint 4: Interface & Integration (Weeks 11-12)

**Sprint Goal**: Build the user-facing chat interface and dashboard widgets. Connect the frontend to the agent intelligence layer.

| Story ID | Story Title | Points | Assignee | Status |
|----------|------------|--------|----------|--------|
| US-401 | Chat Text Input & Processing | 8 | Frontend Dev | Done |
| US-402 | Structured Response Rendering | 8 | Frontend Dev | Done |
| US-403 | Session History | 3 | Frontend Dev | Done |
| US-501 | Live Price Ticker | 3 | Frontend Dev | Done |
| US-502 | Agent Activity Panel | 3 | Frontend Dev | Done |
| US-503 | Prediction Confidence Widget | 3 | Frontend Dev | Done |
| US-302 | Real-Time Prediction Serving | 5 | ML Engineer | Done |

**Sprint Capacity**: 38 points | **Committed**: 33 points | **Delivered**: 33 points

**Sprint 4 Review Notes**:
- Chat interface functional end-to-end: user query -> LLM intent -> agent analysis -> rendered response.
- Average response time measured at 3.8 seconds — under the 5-second target.
- Widget real-time updates implemented via WebSocket (decision from Sprint 1 review).
- Product Owner demo well-received; requested color scheme adjustment for signal badges.

**Sprint 4 Retrospective**:
- What went well: Frontend-backend integration was smooth thanks to well-defined API contracts from FRS.
- What to improve: WebSocket reconnection logic needs hardening — occasional drops on network change.
- Action: Add WebSocket auto-reconnect with exponential backoff in Sprint 5.

---

## Sprint 5: Education & Polish (Weeks 13-14)

**Sprint Goal**: Build Learn Mode, refine UX based on internal feedback, and implement the model retraining pipeline.

| Story ID | Story Title | Points | Assignee | Status |
|----------|------------|--------|----------|--------|
| US-601 | Learn Mode Landing Page | 5 | Frontend Dev | Done |
| US-602 | Contextual Learn Links in Chat | 5 | Frontend Dev | Done |
| US-303 | Model Retraining Pipeline | 5 | ML Engineer | Done |
| -- | UX: Signal badge color refinement | 2 | Frontend Dev | Done |
| -- | Tech: WebSocket reconnection hardening | 3 | Backend Dev | Done |
| -- | Tech: ETH model accuracy improvement | 5 | ML Engineer | Done |
| -- | Content: Learn Mode topic content (10 topics) | 5 | BA (Pinky) | Done |

**Sprint Capacity**: 38 points | **Committed**: 30 points | **Delivered**: 30 points

**Sprint 5 Review Notes**:
- Learn Mode live with 10 topics covering all four categories.
- ETH model retrained — accuracy improved from 59% to 63%.
- Contextual learn links detect 15 technical terms in chat responses.
- WebSocket reconnection tested under simulated network interruption — stable.

**Sprint 5 Retrospective**:
- What went well: Steady velocity (30 pts); team comfortable with the codebase.
- What to improve: Learn Mode content authoring was more time-consuming than estimated.
- Action: Remaining content can be added post-launch as it doesn't block core functionality.

---

## Sprint 6: Launch Preparation (Weeks 15-16)

**Sprint Goal**: Execute UAT, resolve defects, finalize documentation, and deploy to production.

| Story ID | Story Title | Points | Assignee | Status |
|----------|------------|--------|----------|--------|
| US-801 | UAT Test Execution | 8 | BA (Pinky) + Team | Done |
| US-802 | API Documentation Review | 3 | Backend Dev | Done |
| US-704 | Startup Orchestration Script | 3 | Backend Dev | Done |
| -- | Defect: News agent timeout on large result sets | 3 | Backend Dev | Done |
| -- | Defect: Prediction widget not updating after model refresh | 2 | Frontend Dev | Done |
| -- | Defect: Chat session not clearing on manual reset | 1 | Frontend Dev | Done |
| -- | Documentation: System admin guide | 3 | BA (Pinky) | Done |
| -- | Documentation: User guide (in-app) | 3 | BA (Pinky) | Done |

**Sprint Capacity**: 38 points | **Committed**: 26 points | **Delivered**: 26 points

**Sprint 6 Review Notes**:
- UAT pass rate: 94% (47/50 test cases passed). 3 failures traced to 2 defects resolved in-sprint, 1 low-priority deferred.
- All P0 and P1 defects resolved. 1 P3 deferred (cosmetic alignment on mobile Learn Mode).
- `start_all.sh` tested on clean environment — successful.
- API docs reviewed and accurate for all 9 endpoints.
- System deployed and operational.

---

## Velocity Summary

| Sprint | Committed | Delivered | Velocity |
|--------|-----------|-----------|----------|
| Sprint 1 | 20 | 20 | 20 |
| Sprint 2 | 27 | 27 | 27 |
| Sprint 3 | 52 | 46 | 46 |
| Sprint 4 | 33 | 33 | 33 |
| Sprint 5 | 30 | 30 | 30 |
| Sprint 6 | 26 | 26 | 26 |
| **Average** | **31.3** | **30.3** | **30.3** |

**Observations**:
- Sprint 3 was the only sprint with a delivery shortfall (52 committed vs 46 delivered). This was corrected in subsequent sprints by respecting the 38-point capacity cap.
- The team's sustainable velocity stabilized around 30 points per sprint after the initial ramp-up.
- Total story points delivered: 182 (including tech debt and defect stories beyond the core 150-point backlog).

---

## Burndown Chart Data (Cumulative)

| Sprint | Planned Cumulative | Actual Cumulative |
|--------|--------------------|-------------------|
| Sprint 1 | 25 | 20 |
| Sprint 2 | 50 | 47 |
| Sprint 3 | 88 | 93 |
| Sprint 4 | 113 | 126 |
| Sprint 5 | 138 | 156 |
| Sprint 6 | 150 | 182 |

Note: Actual exceeded planned because tech debt, defects, and design stories were added beyond the original 150-point backlog.
