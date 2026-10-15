# AI Use-Case Inventory
**Vantage Point Insurance** · Document ID: VPI-AIG-INV-001 · Version 1.0 · Classification: Internal – Confidential
**Owner:** Head of AI Governance · **Approved by:** AI Risk Committee · **Review cycle:** Quarterly attestation, continuous intake

---

## 1. Purpose

This register is the single system of record for every AI system operated by, or on behalf of, Vantage Point Insurance within the Underwriting, Claims and Customer Service business units. It is the prerequisite for risk assessment, impact assessment, monitoring, policy enforcement and regulatory reporting.

## 2. Definition of "AI System" Used

An entry qualifies if it is a machine-based system that, for explicit or implicit objectives, **infers from inputs how to generate outputs** such as predictions, content, recommendations or decisions that can influence physical or virtual environments (aligned to the OECD / EU AI Act definition).

| Included | Excluded (logged with rationale) |
|---|---|
| Supervised ML models (GLMs with ML-derived features, GBMs, neural networks) | Pure rules engines with no learned parameters |
| Vendor SaaS with embedded ML/LLM features | Static actuarial rating tables |
| Generative AI tools (enterprise or individual) | RPA bots executing fixed scripts with no inference |
| Third-party data scores consumed in decisions (e.g., ML-derived risk scores) | Standard BI dashboards |

## 3. Schema

| Field | Description | Allowed values |
|---|---|---|
| ID | Unique identifier | UC-### |
| Name | Business name of the system | Free text |
| Business unit | Owning BU | UW / CLM / CS |
| Purpose | What the system does and the decision it supports | Free text |
| Business owner | Accountable executive | Named role |
| Technical owner | Accountable for build/operation | Named role |
| Source | Build type | Internal / Vendor / Embedded / GenAI tool |
| Vendor | Supplier, if any | Free text |
| Data categories | Inputs used | PII, Sensitive PII, Claims, Telematics, Credit-based, Third-party, Images, Free text |
| Affected parties | Who the output is about | Applicants, Policyholders, Claimants, Employees |
| Users | Who uses the output | Role |
| Decision impact | Influence on outcome | Automated decision / Decision support / Informational |
| Consumer-impacting | Affects eligibility, price, claim payment or coverage | Y / N |
| Lifecycle stage | Current stage | Idea / Pilot / Production / Retiring |
| Agent? | Can take actions via tools/APIs | Y → also in AI-Agent-Inventory |
| Risk tier | Internal tier (Section 4) | T1–T4 |
| Discovery source | How it was found | Self-reported / Shadow-AI discovery / Contract review |
| Last assessment | Date of most recent RA/IA | Date / Not yet |

## 4. Risk-Tier Criteria

| Tier | Label | Criteria (any one triggers) | Governance consequence |
|---|---|---|---|
| **T1** | Critical | Automated or materially determinative decision on eligibility, price, claim payment or fraud referral; or uses protected-class proxies | Full risk + impact assessment, bias testing (SB21-169 style), AI Risk Committee approval, quarterly monitoring |
| **T2** | High | Decision support on consumer outcomes with human review; or processes sensitive PII at scale; or action-taking agent | Risk assessment, bias screen, BU-level approval, semi-annual review |
| **T3** | Moderate | Internal efficiency use touching PII but not consumer outcomes | Lightweight assessment, annual review |
| **T4** | Low | No PII, no consumer impact, informational only | Register entry and acceptable-use controls only |

## 5. Register

| ID | Name | BU | Purpose | Business owner | Source / Vendor | Data categories | Affected parties | Decision impact | Consumer-impacting | Stage | Agent? | Tier | Discovery |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| UC-001 | Auto Pricing Model v4 | UW | GBM-derived rating factors feeding personal auto premium | VP Personal Lines | Internal | PII, Telematics, Credit-based, Claims history | Applicants, Policyholders | Automated decision | Y | Production | N | **T1** | Self-reported |
| UC-002 | Homeowners Property Risk Score | UW | Aerial-imagery roof-condition score used in eligibility | VP Personal Lines | Vendor – AerialScore Inc. | Images, Address, Third-party | Applicants | Decision support (auto-decline below threshold) | Y | Production | N | **T1** | Contract review |
| UC-003 | Small Commercial Appetite Triage | UW | Classifies submissions into appetite / refer / decline | VP Commercial | Internal | Business data, Third-party | Applicants | Decision support | Y | Production | **Y** | T2 | Self-reported |
| UC-004 | Underwriting Submission Extractor | UW | LLM extraction of fields from broker PDFs/emails | Dir. UW Operations | Vendor – DocuMind | PII, Free text | Applicants | Informational | N | Production | N | T3 | Self-reported |
| UC-005 | Third-Party Insurance Score | UW | Credit-based insurance score consumed in rating | VP Personal Lines | Vendor – bureau score | Credit-based | Applicants | Automated decision (input) | Y | Production | N | **T1** | Self-reported |
| UC-006 | Claims Fraud Propensity Model | CLM | Scores FNOL claims for SIU referral | VP Claims | Internal | Claims, PII, Third-party | Claimants | Decision support (auto-referral > 0.82) | Y | Production | N | **T1** | Self-reported |
| UC-007 | Auto Damage Photo Estimator | CLM | Computer-vision repair estimate from claimant photos | Dir. Auto Claims | Vendor – VisionClaim | Images, PII | Claimants | Decision support | Y | Production | N | T2 | Self-reported |
| UC-008 | Claims Triage & Assignment | CLM | Predicts complexity and routes claim to adjuster tier | Dir. Claims Ops | Internal | Claims, PII | Claimants | Decision support | N | Production | N | T3 | Self-reported |
| UC-009 | Litigation Propensity Model | CLM | Predicts likelihood of attorney involvement | VP Claims | Internal | Claims, PII | Claimants | Decision support | Y | Pilot | N | T2 | Self-reported |
| UC-010 | Claims Correspondence Drafting Agent | CLM | Drafts and sends status letters to claimants | Dir. Claims Ops | Vendor – CoreClaims AI add-on | PII, Claims | Claimants | Automated action | Y | Pilot | **Y** | T2 | Shadow-AI discovery |
| UC-011 | Subrogation Opportunity Finder | CLM | Identifies recoverable claims | Dir. Recovery | Internal | Claims | Third parties | Decision support | N | Production | N | T3 | Self-reported |
| UC-012 | Low-Value Glass Claim Auto-Settlement Agent | CLM | Validates coverage and issues payment < $1,500 | VP Claims | Internal | Claims, PII, Payments | Claimants | Automated decision + action | Y | Pilot | **Y** | **T1** | Self-reported |
| UC-013 | Customer Service Chatbot | CS | Answers policy/billing questions on web & app | Dir. Customer Experience | Vendor – ConverseCX | PII, Policy data | Policyholders | Automated action | N | Production | **Y** | T2 | Self-reported |
| UC-014 | Call Summarisation & QA | CS | Transcribes and summarises calls, scores agent QA | Dir. Contact Centre | Embedded – CCaaS platform | Voice, PII, Sensitive PII | Policyholders, Employees | Informational | N | Production | N | T3 | Self-reported |
| UC-015 | Sentiment-Based Complaint Escalation | CS | Flags calls likely to become complaints | Dir. Contact Centre | Embedded – CCaaS platform | Voice, PII | Policyholders | Decision support | N | Production | N | T3 | Shadow-AI discovery |
| UC-016 | Agent-Assist Knowledge Copilot | CS | Retrieves answers for service reps (RAG) | Dir. Contact Centre | Internal on Azure OpenAI | Policy docs | Employees | Informational | N | Production | N | T4 | Self-reported |
| UC-017 | Payment Reminder Outreach Agent | CS | Sends SMS/email reminders, offers payment plans | Dir. Billing | Vendor – ReachOut AI | PII, Billing | Policyholders | Automated action | Y | Pilot | **Y** | T2 | Shadow-AI discovery |
| UC-018 | Public GenAI Chat (individual accounts) | All | Drafting emails, summarising documents | n/a – unsanctioned | Multiple consumer tools | Unknown – PII exposure risk | Unknown | Informational | N | Shadow | N | T3 (pending) | Shadow-AI discovery |
| UC-019 | Browser AI Writing Extension | UW, CS | Grammar/rewriting extension with cloud processing | n/a – unsanctioned | Browser extension | Free text incl. PII | Unknown | Informational | N | Shadow | N | T3 (pending) | Shadow-AI discovery |
| UC-020 | Meeting Note-Taker Bot | UW | Joins broker calls, records and summarises | n/a – unsanctioned | SaaS note-taker | Voice, PII, broker data | Brokers, Applicants | Informational | N | Shadow | **Y** | T2 (pending) | Shadow-AI discovery |
| UC-021 | Spreadsheet AI Add-in | UW | Generates formulas and analysis on rating data | n/a – unsanctioned | Office add-in | Rating data | n/a | Informational | N | Shadow | N | T3 (pending) | Shadow-AI discovery |

## 6. Summary Statistics

| Dimension | Count |
|---|---|
| Total entries | 21 |
| By BU | UW 7 · CLM 7 · CS 5 · Cross-BU 2 |
| By tier | T1: 5 · T2: 7 · T3: 8 · T4: 1 |
| Consumer-impacting | 10 |
| Agents (cross-listed) | 6 |
| Shadow / unsanctioned | 4 (dispositions in Shadow-AI-Discovery-Log) |
| By discovery source | Self-reported 13 · Shadow-AI / contract discovery 8 |
| No assessment on record | 17 |

## 7. Immediate Actions Raised

| # | Action | Owner | Due |
|---|---|---|---|
| A-01 | Commission risk + impact assessments for all 5 T1 systems, starting with UC-002 (auto-decline threshold undocumented) | Head of AI Governance | 60 days |
| A-02 | Run disparate-impact testing on UC-001, UC-005, UC-006 consistent with Colorado SB21-169 methodology | Chief Actuary / Data Science | 90 days |
| A-03 | Freeze UC-012 expansion beyond pilot until kill-switch test passes (see Agent Inventory) | VP Claims | Immediate |
| A-04 | Disposition all 4 shadow entries per Shadow-AI Discovery Log | CISO | 30 days |
| A-05 | Add AI-feature disclosure and change-notification clause to vendor contracts for UC-002, UC-010, UC-014, UC-015 | Procurement | Next renewal |

## 8. Framework Traceability

| Requirement | Evidence in this document |
|---|---|
| ISO/IEC 42001 Cl. 4.3 | Section 5 defines systems in AIMS scope |
| ISO/IEC 42001 A.4.2–A.4.6 | Data categories, source/vendor, owners captured per system |
| NIST AI RMF GOVERN 1.6 | Entire register |
| NIST AI RMF MAP 1.1 / 4.1 | Purpose, affected parties, third-party source fields |
| NAIC AI Bulletin – AIS Program | Inventory of AI systems used in insurance decisions |
