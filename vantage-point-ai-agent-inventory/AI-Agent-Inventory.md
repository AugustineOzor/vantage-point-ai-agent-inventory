# AI Agent Inventory
**Vantage Point Insurance** · Document ID: VPI-AIG-INV-002 · Version 1.0 · Classification: Internal – Confidential
**Owner:** Head of AI Governance (with CISO co-ownership of permission fields) · **Review cycle:** Monthly for T1 agents, quarterly otherwise

---

## 1. Why a Separate Register

A standard AI inventory answers *"what does this system decide or recommend?"*. An agent inventory must answer *"what can this system **do** — and how do we stop it?"*

Agents call tools, write to systems of record, move money and contact customers. Their risk is driven by **permissions and autonomy**, not only model accuracy. This register therefore uses a **distinct schema**. Every agent here is cross-referenced to its parent entry in the AI Use-Case Inventory (`UC-###`), but agent-specific fields live only here.

## 2. Autonomy Level Scale

| Level | Name | Description | Human role |
|---|---|---|---|
| **A0** | Assist | Generates content/suggestions; cannot call tools | Human performs every action |
| **A1** | Propose | Prepares actions (drafts, populated forms); human must execute | Human-in-the-loop — executes |
| **A2** | Approve-to-act | Executes actions only after explicit per-action human approval | Human-in-the-loop — approves |
| **A3** | Act-with-oversight | Executes autonomously within bounded limits; humans sample and can intervene | Human-on-the-loop |
| **A4** | Autonomous | Executes without routine human review, including multi-step plans | Human-out-of-the-loop |

**Policy constraint:** No agent may operate at A4 on consumer-impacting actions. A3 requires a tested kill switch, transaction limits and AI Risk Committee approval.

## 3. Schema

| Field | Description |
|---|---|
| Agent ID | AG-### |
| Parent use case | UC-### link |
| Agent framework / platform | Orchestration layer (e.g., vendor platform, internal LangGraph service) |
| Underlying model(s) | Foundation model and version |
| Tools / plugins | Every tool, API or connector the agent can invoke |
| Action permissions | Read / Write / Delete / Send / Pay — per system |
| Service identity | Non-human identity used, and whether it is dedicated |
| Credential scope | Least-privilege status of that identity |
| Autonomy level | A0–A4 |
| Human-approval gates | Which actions require approval, by whom |
| Transaction / rate limits | Hard ceilings enforced outside the model |
| Data reach | Data stores readable via tools |
| Kill switch | Mechanism, owner, last test date, time-to-halt |
| Logging | Are prompts, tool calls and outputs logged and retained? |
| Prompt-injection exposure | Does the agent ingest untrusted external content? |
| Risk tier | T1–T4 (inherits from UC, may be raised) |

## 4. Register — Identity, Tools & Permissions

| Agent ID | Name | Parent | Platform / Model | Tools / plugins | Action permissions | Service identity | Least privilege? |
|---|---|---|---|---|---|---|---|
| AG-001 | Glass Claim Auto-Settlement Agent | UC-012 | Internal orchestration service; commercial LLM via private endpoint | Policy Admin API (read), Claims API (read/write), Payments API (create), Vendor glass-network API | Read policy · Create/update claim · **Issue payment ≤ $1,500** | svc-glass-agent (dedicated) | Partial — Claims API scope includes *close claim*, not needed |
| AG-002 | Claims Correspondence Agent | UC-010 | CoreClaims AI add-on (vendor-hosted LLM) | Claims API (read), Document generation, Outbound email | Read claim · Generate letter · **Send email to claimant** | Shared integration account | **No** — shared with 3 other integrations |
| AG-003 | Customer Service Chatbot | UC-013 | ConverseCX platform, vendor LLM | Policy lookup, Billing lookup, Address change, ID-card resend, Live-agent handoff | Read policy/billing · **Update mailing address** · Send ID card | svc-cx-bot (dedicated) | Yes |
| AG-004 | Payment Reminder Outreach Agent | UC-017 | ReachOut AI (vendor SaaS) | Billing API (read), SMS gateway, Email, Payment-plan module | Read billing · Send SMS/email · **Offer & enrol payment plan** | API key, vendor-held | **No** — key also permits plan cancellation |
| AG-005 | Meeting Note-Taker Bot | UC-020 | SaaS note-taker (unsanctioned) | Calendar, Video-conference join, Cloud storage, Email | Join meetings · Record · **Email summaries to all attendees incl. external brokers** | Individual user OAuth grants | **No** — user-granted full calendar + mail scope |
| AG-006 | UW Referral Prep Agent | UC-003 | Internal, Azure OpenAI | Submission store (read), Third-party data APIs (read), UW workbench (write draft) | Read submissions · Query external data · Create draft referral | svc-uw-prep (dedicated) | Yes |

## 5. Register — Autonomy, Oversight & Control

| Agent ID | Autonomy | Human-approval gates | Hard limits (enforced outside model) | Kill switch | Last test | Time-to-halt | Logging | Untrusted input? | Tier |
|---|---|---|---|---|---|---|---|---|---|
| AG-001 | **A3** | Payments > $1,500 → adjuster; any coverage ambiguity → adjuster; 5% random sample reviewed daily | $1,500/claim; $40k/day aggregate; 1 payment per claim | Feature flag in orchestration service + Payments API key revocation | **Not tested** | Unknown | Tool calls ✔ · Prompts ✔ · 13-month retention | Yes — claimant photo captions, free-text FNOL | **T1** |
| AG-002 | **A4 (non-compliant)** | None — letters sent automatically | None | Vendor support ticket only | Never | ~48 hrs (vendor SLA) | Vendor-side only; no customer access | Yes — claimant emails | T2 → **raised to T1** |
| AG-003 | A3 | Address change requires OTP to registered phone; payment actions not permitted | 1 address change / 30 days | Admin console toggle → static FAQ mode | 2026-07-14 | < 2 min | Full transcript + tool calls, 24 months | Yes — customer chat | T2 |
| AG-004 | A3 | Payment plans > 6 instalments → billing rep | Plan max 12 months; 3 contacts / 14 days (TCPA alignment) | Vendor console pause | 2026-05-02 | ~15 min | Vendor-side, exportable | Yes — SMS replies | T2 |
| AG-005 | **A4** | None | None | Revoke OAuth grants per user | n/a | Unknown | None accessible to VPI | Yes — meeting audio | T2 (pending disposition) |
| AG-006 | A1 | Underwriter must submit every referral | Read-only on external data | Service disable | 2026-08-20 | < 5 min | Full, 24 months | Yes — broker PDFs | T2 |

## 6. Findings

| ID | Finding | Agents | Severity | Recommendation |
|---|---|---|---|---|
| F-01 | Kill switch exists but has never been tested on the only agent that moves money | AG-001 | **Critical** | Execute tabletop + live halt test before pilot expansion; document time-to-halt target ≤ 5 min |
| F-02 | Agent sends customer communications autonomously with no approval gate, no limits and vendor-only halt | AG-002 | **Critical** | Downgrade to A2 (adjuster approves send) until vendor provides admin kill switch and audit log export |
| F-03 | Shared / over-scoped non-human identities | AG-001, AG-002, AG-004 | High | Dedicated service identities, scope reduction, quarterly entitlement review by IAM |
| F-04 | Unsanctioned agent exfiltrates broker meeting content to external SaaS | AG-005 | High | Block via CASB, revoke grants, offer sanctioned alternative (see Discovery Log SAI-006) |
| F-05 | All agents ingest untrusted content; no prompt-injection testing on record | All | High | Add indirect prompt-injection test cases to pre-deployment testing; enforce tool-call allow-lists |
| F-06 | Hard limits for AG-001 are enforced in prompt *and* API; AG-002 relies on prompt only | AG-002 | Medium | All limits must be enforced deterministically outside the model |

## 7. Minimum Control Baseline for Agents (by autonomy level)

| Control | A1 | A2 | A3 | A4 |
|---|---|---|---|---|
| Dedicated non-human identity | ✔ | ✔ | ✔ | Prohibited for consumer-impacting |
| Tool allow-list | ✔ | ✔ | ✔ | — |
| Deterministic limits outside model | — | ✔ | ✔ | — |
| Full tool-call logging | ✔ | ✔ | ✔ | — |
| Kill switch tested | — | Annual | **Quarterly** | — |
| Prompt-injection testing | ✔ | ✔ | ✔ | — |
| AI Risk Committee approval | — | — | ✔ | — |

## 8. Framework Traceability

| Requirement | Evidence |
|---|---|
| NIST AI RMF MANAGE 2.4 (mechanisms to supersede, disengage or deactivate) | Kill-switch fields, Section 5 |
| NIST AI RMF GOVERN 1.6 / MAP 3.5 (human oversight processes defined) | Autonomy and approval-gate fields |
| ISO/IEC 42001 A.6.2.6 (operation & monitoring), A.6.2.8 (event logs) | Logging fields |
| ISO/IEC 42001 A.9.4 (intended use) | Permissions and hard limits bound intended use |
| OWASP LLM06:2025 Excessive Agency | Permissions, least-privilege and autonomy controls |
| OWASP LLM01:2025 Prompt Injection | Untrusted-input field and F-05 |
