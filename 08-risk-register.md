# Risk Register: Xore

## Document Control

| Field | Value |
|-------|-------|
| Document ID | XORE-RR-001 |
| Version | 2.0 |
| Author | Pinky Choudhary, Project Manager |
| Review Frequency | Every sprint (bi-weekly) |
| Last Updated | 2025-12-10 |

---

## Risk Scoring

**Probability**: 1 (Very Low) to 5 (Very High)  
**Impact**: 1 (Negligible) to 5 (Critical)  
**Risk Score** = Probability x Impact  
**Thresholds**: Low (1-6), Medium (7-12), High (13-19), Critical (20-25)

---

## Active Risks

### R-001: External API Rate Limits or Downtime

| Field | Value |
|-------|-------|
| Category | Technical / External Dependency |
| Probability | 4 | 
| Impact | 4 |
| Risk Score | 16 (High) |
| Owner | Backend Developer |
| Status | Mitigated |

**Description**: LiveCoinWatch, Ankr, or Google Search APIs may enforce rate limits, change pricing, or experience downtime, disrupting data ingestion.

**Mitigation Strategy**:
- Implemented caching layer with configurable TTL per data source
- Stale data flag (`is_stale`) alerts users when data is from cache
- Graceful degradation: agents operate on last available data rather than failing
- Monitor API usage against rate limits via backend logging

**Contingency**: If a provider permanently changes terms, evaluate alternative APIs (CoinGecko for market data, Infura for on-chain, Bing News for sentiment).

---

### R-002: GPU Availability for LLM and Model Inference

| Field | Value |
|-------|-------|
| Category | Infrastructure |
| Probability | 3 |
| Impact | 5 |
| Risk Score | 15 (High) |
| Owner | ML Engineer |
| Status | Mitigated |

**Description**: The system requires CUDA-compatible GPU for vLLM serving and prediction model inference. GPU shortages, driver issues, or misconfiguration could prevent system startup.

**Mitigation Strategy**:
- Hardware requirements documented in project prerequisites
- `start_vllm.sh` includes GPU availability check with clear error messages
- Tested on two different GPU configurations (RTX 3090, A100) for compatibility
- Model quantization option documented as fallback for lower-VRAM GPUs

**Contingency**: For demonstration/testing environments without GPU, document a CPU-only mode with reduced model (smaller Llama variant) accepting higher latency.

---

### R-003: Prediction Model Accuracy Below Target

| Field | Value |
|-------|-------|
| Category | Technical / Data Science |
| Probability | 3 |
| Impact | 3 |
| Risk Score | 9 (Medium) |
| Owner | ML Engineer |
| Status | Resolved |

**Description**: The hybrid Transformer-GRU model may not achieve the 60% directional accuracy target, undermining the platform's core value proposition.

**Mitigation Strategy**:
- Iterative model development with weekly evaluation checkpoints
- BTC model achieved 62% accuracy in Sprint 3; ETH initially at 59%, improved to 63% in Sprint 5
- Automated retraining pipeline ensures model adapts to recent data
- Trading Strategist weights prediction at 30% (not dominant), reducing single-source dependency

**Resolution**: Both models exceed 60% threshold after Sprint 5 retraining.

---

### R-004: Scope Creep from Stakeholder Requests

| Field | Value |
|-------|-------|
| Category | Project Management |
| Probability | 4 |
| Impact | 3 |
| Risk Score | 12 (Medium) |
| Owner | BA/PM (Pinky) |
| Status | Mitigated |

**Description**: Stakeholders may request additional features (altcoin support, live trading, mobile app) that expand scope beyond the 16-week timeline.

**Mitigation Strategy**:
- Scope boundary clearly documented in Project Charter (Section 3)
- All change requests routed through formal Change Request process
- MoSCoW prioritization enforced: new requests evaluated against existing "Must Have" items
- Product Owner educated on trade-offs during weekly 1:1s
- Backlog includes "Won't Have" category for explicitly deferred items

---

### R-005: Team Member Unavailability

| Field | Value |
|-------|-------|
| Category | Resource |
| Probability | 2 |
| Impact | 4 |
| Risk Score | 8 (Medium) |
| Owner | BA/PM (Pinky) |
| Status | Accepted |

**Description**: Small team (6 members) with single-person ownership of most domains. Unplanned absence of any developer could delay sprint delivery.

**Mitigation Strategy**:
- Code reviews ensure at least two people understand each module
- Architecture documentation maintained in Confluence
- Cross-training sessions scheduled for critical knowledge areas (CrewAI agent setup, vLLM configuration)
- Sprint planning includes 10% buffer for unplanned capacity loss

---

### R-006: WebSocket Stability for Real-Time Updates

| Field | Value |
|-------|-------|
| Category | Technical |
| Probability | 3 |
| Impact | 2 |
| Risk Score | 6 (Low) |
| Owner | Backend Developer |
| Status | Resolved |

**Description**: WebSocket connections may drop on network changes, causing widgets to show stale data without user awareness.

**Mitigation Strategy**:
- Auto-reconnect with exponential backoff implemented in Sprint 5
- Visual indicator when WebSocket connection is lost
- Fallback to polling (60s interval) if WebSocket fails 3 consecutive reconnects

**Resolution**: Tested under simulated network interruption in Sprint 5. Reconnection stable.

---

### R-007: Data Privacy and Regulatory Compliance

| Field | Value |
|-------|-------|
| Category | Legal / Compliance |
| Probability | 2 |
| Impact | 4 |
| Risk Score | 8 (Medium) |
| Owner | Product Owner |
| Status | Mitigated |

**Description**: Crypto intelligence platforms may face regulatory scrutiny. The system must not be positioned as financial advice.

**Mitigation Strategy**:
- Disclaimer displayed on all trading signals: "For informational purposes only. Not financial advice."
- No user financial data collected or stored
- Local LLM option ensures no user queries sent to external providers
- No live trading execution capability in v1 (out of scope)

---

### R-008: CrewAI Framework Maturity

| Field | Value |
|-------|-------|
| Category | Technical / Dependency |
| Probability | 2 |
| Impact | 3 |
| Risk Score | 6 (Low) |
| Owner | Tech Lead |
| Status | Accepted |

**Description**: CrewAI is a relatively new framework. Breaking changes in updates or undocumented limitations could impact agent orchestration.

**Mitigation Strategy**:
- CrewAI version pinned in `requirements.txt`
- Agent orchestration logic abstracted behind an interface layer, allowing framework swap if needed
- Community monitoring for known issues and patches

---

## Risk Summary Dashboard

| Risk Score | Count | Risks |
|-----------|-------|-------|
| Critical (20-25) | 0 | — |
| High (13-19) | 2 | R-001, R-002 |
| Medium (7-12) | 4 | R-003, R-004, R-005, R-007 |
| Low (1-6) | 2 | R-006, R-008 |

**Resolved**: R-003, R-006

---

## Risk Review Log

| Date | Sprint | Changes |
|------|--------|---------|
| 2025-09-10 | Pre-Sprint | Initial risk register created with 6 risks |
| 2025-10-08 | Sprint 2 | R-006 added (WebSocket stability identified) |
| 2025-10-22 | Sprint 3 | R-003 partially mitigated (BTC model at 62%) |
| 2025-11-19 | Sprint 5 | R-003 resolved (ETH at 63%); R-006 resolved |
| 2025-12-03 | Sprint 6 | R-007 added (compliance consideration during UAT) |
| 2025-12-10 | Sprint 6 | Final review; all high risks mitigated |
