# Stakeholder Analysis & RACI Matrix: Xore

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-SA-001 |
| Version | 1.1 |
| Author | Pinky Choudhary, Business Analyst / Project Manager |
| Last Updated | 2025-09-10 |

---

## 1. Stakeholder Register

| ID | Stakeholder | Role | Interest Level | Influence Level | Engagement Strategy |
|----|-------------|------|---------------|----------------|-------------------|
| S1 | Product Owner | Strategic sponsor, feature prioritization, final acceptance | High | High | Weekly 1:1 updates, Sprint Review demos, approval on BRD/FRS |
| S2 | Tech Lead | Architecture decisions, code quality, technical feasibility | High | High | Daily standups, Sprint Planning input, design reviews |
| S3 | Backend Developer | API development, agent implementation, data pipelines | High | Medium | Sprint ceremonies, JIRA task assignment, code reviews |
| S4 | Frontend Developer | UI implementation, widget development, UX execution | High | Medium | Sprint ceremonies, JIRA task assignment, wireframe reviews |
| S5 | ML Engineer | Model architecture, training, prediction serving | High | Medium | Sprint ceremonies, model review sessions, notebook reviews |
| S6 | UX Designer | User research, wireframes, visual design | Medium | Medium | Design sprint sessions (Sprint 2), async review on Figma |
| S7 | BA / PM (Pinky) | Requirements, planning, delivery, stakeholder communication | High | High | All ceremonies, documentation ownership, UAT coordination |
| S8 | End Users (Traders) | Platform consumers, feedback providers | High | Low | UAT participation, feedback surveys, beta testing |

---

## 2. Stakeholder Influence/Interest Grid

```
HIGH INFLUENCE
    ┌─────────────────────┬─────────────────────┐
    │   Keep Satisfied    │   Manage Closely     │
    │                     │                      │
    │                     │  S1 (Product Owner)  │
    │                     │  S2 (Tech Lead)      │
    │                     │  S7 (BA/PM)          │
    ├─────────────────────┼─────────────────────┤
    │      Monitor        │   Keep Informed      │
    │                     │                      │
    │                     │  S3 (Backend Dev)    │
    │                     │  S4 (Frontend Dev)   │
    │                     │  S5 (ML Engineer)    │
    │                     │  S6 (UX Designer)    │
    │                     │  S8 (End Users)      │
    └─────────────────────┴─────────────────────┘
LOW INFLUENCE          LOW INTEREST      HIGH INTEREST
```

---

## 3. Communication Plan

| Stakeholder | Channel | Frequency | Content |
|-------------|---------|-----------|---------|
| Product Owner | 1:1 Meeting | Weekly | Progress, risks, decisions needed |
| Product Owner | Sprint Review | Bi-weekly | Demo, acceptance, backlog review |
| Tech Lead | Standup | Daily | Blockers, architecture decisions |
| Dev Team (S3-S5) | Standup | Daily | Task progress, blockers |
| Dev Team (S3-S5) | Sprint Planning | Bi-weekly | Story commitment, estimation |
| Dev Team (S3-S5) | Sprint Retro | Bi-weekly | Process improvements |
| UX Designer | Design Review | As needed | Wireframe/mockup feedback |
| End Users | UAT Session | Sprint 6 | Testing, feedback collection |
| All | Confluence | Continuous | Documentation updates |
| All | JIRA | Continuous | Task tracking, status |

---

## 4. RACI Matrix

**R** = Responsible, **A** = Accountable, **C** = Consulted, **I** = Informed

| Activity | Product Owner (S1) | Tech Lead (S2) | Backend Dev (S3) | Frontend Dev (S4) | ML Engineer (S5) | UX Designer (S6) | BA/PM - Pinky (S7) |
|----------|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| **Planning & Requirements** |
| Project Charter | A | C | I | I | I | I | R |
| Business Requirements (BRD) | A | C | I | I | I | I | R |
| Functional Requirements (FRS) | A | C | C | C | C | I | R |
| Product Backlog Creation | A | C | C | C | C | I | R |
| Sprint Planning | I | C | R | R | R | I | A |
| User Story Writing | A | C | I | I | I | I | R |
| **Design & Architecture** |
| System Architecture | I | A | R | C | C | I | C |
| Database Design | I | A | R | I | C | I | C |
| UX Wireframes | A | I | I | C | I | R | C |
| API Contract Design | I | A | R | C | C | I | R |
| Process Flow Diagrams | I | C | C | C | C | I | R |
| **Development** |
| Backend API Development | I | A | R | I | I | I | I |
| Frontend UI Development | I | A | I | R | I | C | I |
| ML Model Development | I | A | I | I | R | I | I |
| Agent Development (CrewAI) | I | A | R | I | C | I | I |
| Data Pipeline Development | I | A | R | I | I | I | I |
| **Quality & Testing** |
| Unit Testing | I | A | R | R | R | I | I |
| Integration Testing | I | A | R | R | R | I | C |
| UAT Planning | A | C | I | I | I | I | R |
| UAT Execution | A | I | C | C | C | I | R |
| Defect Triage | I | A | C | C | C | I | R |
| **Delivery & Operations** |
| Sprint Review Facilitation | C | C | C | C | C | I | R |
| Sprint Retrospective | C | C | R | R | R | I | A |
| Risk Management | C | C | I | I | I | I | R |
| Change Request Processing | A | C | I | I | I | I | R |
| Stakeholder Reporting | A | I | I | I | I | I | R |
| Documentation | C | C | C | C | C | I | R |
| Deployment | I | A | R | R | R | I | C |
| **Dashboards & Reporting** |
| Tableau Project Health Dashboard | I | I | I | I | I | I | R |
| Power BI Product Metrics Dashboard | C | I | C | I | C | I | R |
| KPI Definition & Tracking | A | C | I | I | I | I | R |

---

## 5. Decision Authority Matrix

| Decision Type | Decision Maker | Consulted | Informed |
|--------------|---------------|-----------|----------|
| Feature prioritization (MoSCoW) | Product Owner | BA/PM, Tech Lead | Dev Team |
| Architecture/technology choices | Tech Lead | Backend Dev, ML Engineer | Product Owner, BA/PM |
| Sprint scope commitment | Dev Team (consensus) | BA/PM | Product Owner |
| Risk escalation and mitigation | BA/PM | Product Owner, Tech Lead | Dev Team |
| Change request approval | Product Owner | BA/PM, Tech Lead | Dev Team |
| UAT acceptance | Product Owner | BA/PM | Dev Team |
| Launch go/no-go | Product Owner | Tech Lead, BA/PM | All |
