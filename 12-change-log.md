# Change Request Log: Xore

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-CR-001 |
| Version | 1.0 |
| Author | Pinky Choudhary, Project Manager |
| Last Updated | 2025-12-10 |

---

## Change Request Process

1. Requester submits CR with description, justification, and impact assessment
2. BA/PM (Pinky) evaluates scope, schedule, and resource impact
3. Tech Lead assesses technical feasibility
4. Product Owner approves or rejects
5. If approved, BA/PM updates backlog, sprint plan, and affected documentation

---

## Change Requests

### CR-001: Switch Widget Updates from Polling to WebSocket

| Field | Value |
|-------|-------|
| Requested By | Tech Lead |
| Date Submitted | 2025-10-01 (Sprint 1 Review) |
| Priority | Medium |
| Status | **Approved & Implemented** |

**Description**: Replace HTTP polling (every 60s) with WebSocket connections for real-time widget updates (price ticker, agent status, prediction).

**Justification**: WebSocket provides lower latency and reduces unnecessary API calls. Improves user experience with instant updates during active analysis cycles.

**Impact Assessment**:
- Schedule: Adds 3 story points to Sprint 4 (frontend refactor)
- Scope: No new features; replaces existing refresh mechanism
- Risk: WebSocket reconnection logic needed (added R-006 to risk register)

**Decision**: Approved by Product Owner on 2025-10-02. Implemented in Sprint 4.

---

### CR-002: Signal Badge Color Scheme Adjustment

| Field | Value |
|-------|-------|
| Requested By | Product Owner |
| Date Submitted | 2025-11-05 (Sprint 4 Review) |
| Priority | Low |
| Status | **Approved & Implemented** |

**Description**: Change signal badge colors from bright red/green to a more nuanced palette: dark green (Strong Buy), light green (Buy), gray (Neutral), orange (Sell), dark red (Strong Sell).

**Justification**: Original red/green felt too aggressive and binary. Nuanced palette reduces emotional reaction and better represents the 5-level signal spectrum.

**Impact Assessment**:
- Schedule: 2 story points in Sprint 5
- Scope: CSS-only change; no functional impact
- Risk: None

**Decision**: Approved on 2025-11-06. Implemented in Sprint 5.

---

### CR-003: Add Altcoin Support (XRP, SOL, ADA)

| Field | Value |
|-------|-------|
| Requested By | Product Owner |
| Date Submitted | 2025-11-05 (Sprint 4 Review) |
| Priority | High |
| Status | **Rejected (Deferred to v2)** |

**Description**: Extend platform support to include XRP, SOL, and ADA in addition to BTC and ETH.

**Justification**: Broader asset coverage would attract more users and increase platform value.

**Impact Assessment**:
- Schedule: Estimated 2 additional sprints (4 weeks). Would push launch beyond 16-week window.
- Scope: Requires new data pipelines per asset, model retraining per asset, agent adaptation, and UI changes.
- Risk: High. Doubles the data pipeline surface area and model training effort.

**Decision**: Rejected for v1 on 2025-11-06 by Product Owner. Altcoin support is the top priority for v2 roadmap. Architecture designed to support future expansion (asset parameter is variable throughout the codebase).

---

### CR-004: Add Article Cap to News Agent

| Field | Value |
|-------|-------|
| Requested By | BA/PM (Pinky) |
| Date Submitted | 2025-12-03 (Sprint 6 UAT) |
| Priority | High |
| Status | **Approved & Implemented** |

**Description**: Cap news article processing at 20 articles per refresh cycle, selecting top 20 by relevance score. Add parallel sentiment scoring.

**Justification**: UAT test TC-041 revealed the News Agent times out when processing 50+ articles sequentially. Root cause identified as sequential sentiment scoring without a result cap.

**Impact Assessment**:
- Schedule: 3 story points in Sprint 6 (from defect budget)
- Scope: Modifies News Sentiment Service (FR-103) processing logic
- Risk: Minor. Reducing article count may miss some relevant articles, but top-20 by relevance covers the most impactful news.

**Decision**: Approved on 2025-12-03. Implemented and retested within Sprint 6.

---

### CR-005: Add Mobile-Responsive Learn Mode

| Field | Value |
|-------|-------|
| Requested By | UX Designer |
| Date Submitted | 2025-12-05 (Sprint 6) |
| Priority | Low |
| Status | **Deferred to Post-Launch** |

**Description**: Fix minor CSS alignment issues on Learn Mode progress bar at mobile viewport widths (375px).

**Justification**: Cosmetic issue. Progress bar overflows by ~10px on small screens. Content remains readable.

**Impact Assessment**:
- Schedule: 1-2 story points
- Scope: CSS-only fix
- Risk: None

**Decision**: Deferred. Not a launch blocker. Tracked as XORE-BUG-017 (P3) for post-launch fix.

---

## Summary

| CR ID | Description | Status | Sprint |
|-------|------------|--------|--------|
| CR-001 | Polling to WebSocket | Approved & Implemented | Sprint 4 |
| CR-002 | Signal badge colors | Approved & Implemented | Sprint 5 |
| CR-003 | Altcoin support | Rejected (Deferred v2) | — |
| CR-004 | News agent article cap | Approved & Implemented | Sprint 6 |
| CR-005 | Mobile Learn Mode CSS | Deferred | Post-Launch |

**Approval Rate**: 3 approved, 1 rejected, 1 deferred (out of 5 total CRs)
