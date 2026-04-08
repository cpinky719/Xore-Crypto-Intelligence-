# Sprint Retrospective Summary: Xore

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-RETRO-001 |
| Version | 1.0 |
| Author | Pinky Choudhary, Scrum Master / BA |
| Last Updated | 2025-12-10 |

---

## Overview

This document consolidates insights from all six sprint retrospectives. It captures recurring themes, action items, and their outcomes to provide a continuous improvement record for the Xore project.

---

## Sprint-by-Sprint Summary

### Sprint 1 Retrospective

**What Went Well**:
- Architecture decisions (FastAPI + Next.js + vLLM) validated early. No technology pivots needed.
- All infrastructure provisioned before sprint start, allowing immediate development.
- LiveCoinWatch API integration smoother than expected.

**What to Improve**:
- API keys scattered across multiple config files. No centralized secret management.

**Action Items**:

| Action | Owner | Target | Outcome |
|--------|-------|--------|---------|
| Centralize API keys in backend/config/settings.py | Backend Dev | Sprint 2 | Completed. All keys in single config with .env file support. |

---

### Sprint 2 Retrospective

**What Went Well**:
- Parallel workstreams (backend data pipelines + UX wireframes) maximized team throughput.
- All three data sources integrated and producing consistent output formats.
- UX wireframes approved on first review with minor comments.

**What to Improve**:
- Ankr API documentation was incomplete for BTC-specific metrics. Cost the team ~4 hours of investigation.
- No shared knowledge base for external API quirks and workarounds.

**Action Items**:

| Action | Owner | Target | Outcome |
|--------|-------|--------|---------|
| Create Confluence page documenting all external API quirks | BA/PM | Sprint 3 | Completed. "External API Reference" page created with known issues, rate limits, and workarounds. |

---

### Sprint 3 Retrospective

**What Went Well**:
- CrewAI agent framework worked well. Token-passing pattern between agents is clean and extensible.
- BTC prediction model hit 62% accuracy on first training run.
- Trading Strategist's weighted scoring logic tested and validated against historical scenarios.

**What to Improve**:
- Sprint was significantly over-committed (52 points vs 38 capacity). Two stories had mid-sprint blockers.
- ETH model underperformed at 59% (below 60% target). Needs retraining with more data.
- Signal weight normalization bug in Trading Strategist took 3 days to diagnose.

**Action Items**:

| Action | Owner | Target | Outcome |
|--------|-------|--------|---------|
| Cap sprint commitment at 38 points maximum | BA/PM | Sprint 4+ | Enforced. No subsequent sprint exceeded capacity. |
| Schedule ETH model retraining with expanded dataset | ML Engineer | Sprint 5 | Completed. ETH accuracy improved to 63%. |
| Add unit tests for signal weight normalization | Backend Dev | Sprint 4 | Completed. Edge cases now covered. |

---

### Sprint 4 Retrospective

**What Went Well**:
- End-to-end chat flow functional. User query to rendered response works reliably.
- Response time measured at 3.8 seconds average, well under the 5-second target.
- Product Owner demo went well. Positive feedback on overall flow and widget design.
- FRS-defined API contracts made frontend-backend integration straightforward.

**What to Improve**:
- WebSocket connections occasionally drop on network change. Widgets show stale data silently.
- Product Owner requested signal badge color changes (CR-002). Should have included PO in design review earlier.

**Action Items**:

| Action | Owner | Target | Outcome |
|--------|-------|--------|---------|
| Implement WebSocket auto-reconnect with exponential backoff | Backend Dev | Sprint 5 | Completed. Tested under simulated network interruption. |
| Include Product Owner in UX design reviews going forward | BA/PM | Ongoing | Implemented. PO attended Sprint 5 design review. |

---

### Sprint 5 Retrospective

**What Went Well**:
- Steady velocity (30 points). Team comfortable and productive.
- Learn Mode received positive feedback from internal testers.
- ETH model retrained successfully, exceeding accuracy target.
- WebSocket reconnection hardened and stable.

**What to Improve**:
- Learn Mode content authoring was underestimated. Writing 10 educational topics took more effort than the 5-point estimate suggested.
- No formal content review process for Learn Mode material.

**Action Items**:

| Action | Owner | Target | Outcome |
|--------|-------|--------|---------|
| Establish content review process for future Learn Mode additions | BA/PM | Post-launch | Documented in operational runbook. |
| Account for content authoring effort separately in future estimates | BA/PM | Future projects | Noted as lesson learned. |

---

### Sprint 6 Retrospective

**What Went Well**:
- UAT pass rate of 94% exceeded the 90% target.
- All P0/P1 defects found and resolved within the sprint.
- start_all.sh works reliably on clean environments.
- Documentation complete and reviewable.

**What to Improve**:
- One deferred defect (P3 mobile Learn Mode CSS) means v1 launches with a known cosmetic issue.
- UAT test cases could have been written earlier (Sprint 4-5) to allow more preparation time.

**Action Items**:

| Action | Owner | Target | Outcome |
|--------|-------|--------|---------|
| Fix P3 mobile CSS issue | Frontend Dev | Post-launch (Week 1) | Tracked as XORE-BUG-017. |
| Write UAT test cases in parallel with development (not after) for future projects | BA/PM | Future projects | Noted as process improvement. |

---

## Recurring Themes Across Sprints

### Positive Patterns

1. **Well-defined requirements paid off**: The FRS and API contract documentation created in Weeks 3-4 significantly reduced integration friction in Sprints 4-5. Both frontend and backend developers cited the FRS as the primary reference during implementation.

2. **Early architecture decisions held**: No technology pivots were needed across 6 sprints. The initial selection of FastAPI, Next.js, CrewAI, and vLLM was validated through delivery.

3. **Parallel workstreams worked**: Structuring sprints to allow parallel work (e.g., backend pipelines + UX design in Sprint 2, data agents + ML training in Sprint 3) maximized team throughput with a small team.

4. **Agile discipline improved**: After the Sprint 3 over-commitment, the team respected capacity limits and maintained sustainable velocity (26-33 points) for the remaining sprints.

### Areas for Growth

1. **Estimation accuracy**: Sprint 3 over-commitment and Sprint 5 content authoring underestimate suggest the team tends toward optimism on complex or novel tasks. Using planning poker more rigorously and adding buffer for first-time activities would help.

2. **External dependency documentation**: Both Ankr (Sprint 2) and LiveCoinWatch (Sprint 1) had undocumented behaviors. Creating a dependency research task at project start would front-load this learning.

3. **Stakeholder inclusion in design**: The Sprint 4 color scheme change request could have been avoided by including the Product Owner in the Sprint 2 UX review. Earlier stakeholder involvement in visual design decisions saves rework.

4. **UAT preparation timeline**: Writing UAT test cases in Sprint 6 compressed the testing window. Starting test case authoring in Sprint 4 (when features stabilize) would allow more thorough testing.

---

## Key Metrics Summary

| Metric | Value |
|--------|-------|
| Total Sprints | 6 |
| Average Velocity | 30.3 points/sprint |
| Total Points Delivered | 182 |
| Sprint Over-Commitment Incidents | 1 (Sprint 3) |
| Action Items Generated | 10 |
| Action Items Completed | 9 (1 post-launch) |
| Action Item Completion Rate | 90% |
