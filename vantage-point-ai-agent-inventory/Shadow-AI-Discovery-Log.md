# Shadow-AI Discovery Log
**Vantage Point Insurance** · Document ID: VPI-AIG-INV-003 · Version 1.0 · Classification: Internal – Confidential
**Discovery window:** 2026-06-01 to 2026-06-28 · **Lead:** AI Governance Analyst, with Security Operations and Procurement

---

## 1. Why Self-Reporting Is Not Enough

The initial BU survey returned **13** AI systems. Technical and financial discovery found **8 more** (38% of the final inventory). Self-reporting fails because:

- Business users do not recognise vendor features as "AI" (e.g., CCaaS sentiment scoring).
- AI features are enabled by default in SaaS upgrades without a procurement event.
- Individuals adopt free or expensed tools that never reach IT.
- Employees under-report tools they suspect are prohibited.

## 2. Discovery Sources & Method

| # | Source | Owner | Method | Signal sought |
|---|---|---|---|---|
| S1 | Accounts-payable & purchasing-card data (18 months) | Procurement / Finance | Keyword and merchant-category search: "AI", "GPT", "copilot", "assistant", known AI vendor list (~240 vendors) | Paid AI subscriptions |
| S2 | Employee expense reports | Finance | Same keyword set on expense line descriptions | Individually expensed AI tools |
| S3 | SSO / identity provider logs | IAM | Third-party app OAuth consent grants; apps with mail/calendar/drive scopes | User-authorised AI apps & bots |
| S4 | CASB / secure web gateway | SecOps | Traffic to AI-category domains; upload volume by user and app | Unsanctioned GenAI use and data egress |
| S5 | DNS logs | SecOps | Queries to known AI API endpoints from non-approved hosts | Developer use of AI APIs outside approved platforms |
| S6 | Endpoint software & browser-extension inventory | EUC | Extensions with AI functionality and broad page-read permissions | AI browser extensions |
| S7 | Vendor contract & release-note review | Procurement / Legal | Review of top 40 SaaS contracts and last 4 releases for AI features | Embedded AI enabled by default |
| S8 | Code repositories | AppSec | Search for AI SDK imports and API keys | Internal builds not on register |

**Privacy safeguard:** Discovery analysed tool usage at application level. Content of prompts was not inspected. Individual-level results were shared only with line managers for disposition, not for disciplinary purposes, consistent with the amnesty notice issued 2026-05-28.

## 3. Discovery Funnel

| Stage | Count |
|---|---|
| Raw signals (domains, merchants, apps, extensions) | 1,146 |
| After de-duplication and removing non-AI false positives | 97 |
| Distinct AI tools/features in scope BUs | 31 |
| Already on self-reported list | 13 |
| Excluded (not AI per definition, or negligible use < 3 users & no data upload) | 9 |
| Found and decommissioned during discovery (not entered) | 1 |
| **New inventory entries** | **8** |

## 4. Findings

| Log ID | Finding | Source(s) | BU | Users | Data exposure observed | Inventory ref | Risk | Disposition | Owner | Status |
|---|---|---|---|---|---|---|---|---|---|---|
| SAI-001 | CoreClaims AI correspondence add-on enabled in Q1 release; letters auto-sent | S7 | CLM | System-wide | Claimant PII to vendor LLM | UC-010 / AG-002 | High | **Remediate** — downgrade to approval mode; DPA amendment | Dir. Claims Ops | In progress |
| SAI-002 | CCaaS sentiment-based complaint escalation active by default | S7 | CS | 140 agents | Voice data | UC-015 | Medium | **Sanction** — add to inventory, review model card from vendor | Dir. Contact Centre | Closed |
| SAI-003 | ReachOut AI payment-reminder agent purchased on p-card by Billing team | S1 | CS | 4 admins | Billing PII to vendor | UC-017 / AG-004 | High | **Sanction with conditions** — security review, contract, TCPA review | Dir. Billing | In progress |
| SAI-004 | Consumer GenAI chat tools used via personal accounts | S4 | All | 312 users | 2,140 uploads in 4 weeks; DLP matched policy numbers in 61 | UC-018 | High | **Replace** — enterprise GenAI tenant rolled out; block consumer domains after 30 days | CISO | In progress |
| SAI-005 | AI writing browser extension with "read all page data" permission | S6 | UW, CS | 47 | Page content incl. policy admin screens | UC-019 | High | **Block** — extension removed via endpoint policy | EUC Manager | Closed |
| SAI-006 | Meeting note-taker bot authorised by individual underwriters | S3 | UW | 23 | Broker call recordings stored externally | UC-020 / AG-005 | High | **Block & replace** — revoke grants, enable native transcription in sanctioned VC platform | CISO | Closed |
| SAI-007 | Spreadsheet AI add-in on rating workbooks | S6, S2 | UW | 9 | Rating data (non-PII, proprietary) | UC-021 | Medium | **Assess** — vendor review; interim restriction to non-production data | Dir. UW Operations | Open |
| SAI-009 | Homeowners aerial roof-condition score treated by UW as a "data product", not AI; contract confirms ML model with auto-decline threshold | S7 | UW | System-wide | Address + imagery; consumer eligibility impact | UC-002 | High | **Sanction with conditions** — T1 assessment, threshold documentation, adverse-action notice review | VP Personal Lines | In progress |
| SAI-008 | Developer calling public LLM API from claims analytics notebook with personal key | S5, S8 | CLM | 2 | Sample claims notes (PII) | — (decommissioned) | High | **Remediate** — key revoked, workload moved to approved private endpoint, incident logged | AppSec Lead | Closed |

## 5. Disposition Framework

| Disposition | When used | Outcome |
|---|---|---|
| **Sanction** | Legitimate need, acceptable risk | Add to inventory, assign owner, standard controls |
| **Sanction with conditions** | Legitimate need, controls missing | Time-bound remediation plan; inventory entry flagged |
| **Replace** | Need is real but tool unacceptable | Provide sanctioned alternative, then block |
| **Block** | Unacceptable risk, no business case | Technical block + communication |
| **Remediate** | Already embedded in process | Reduce autonomy / scope, contract changes |
| **Assess** | Insufficient information | Due-diligence review within 30 days |

## 6. Root Causes

1. **No AI clause in procurement** — p-card and low-value purchases bypass security review.
2. **No change-control trigger for vendor AI features** — release notes are not reviewed for new AI capability.
3. **Unmet demand** — no sanctioned GenAI tool existed before this project, driving consumer use.
4. **OAuth consent open to all users** — any employee could grant mail/calendar access to third-party apps.

## 7. Preventive Controls Implemented

| Control | Addresses | Owner |
|---|---|---|
| OAuth admin-consent required for apps requesting mail, calendar or file scopes | Root cause 4 | IAM |
| AI-category domains monitored in CASB with coaching page; block list for high-risk tools | Root cause 3 | SecOps |
| P-card merchant category and keyword alerting for AI vendors | Root cause 1 | Finance |
| Vendor release-note review for AI features added to SaaS change management | Root cause 2 | IT Change Mgmt |
| Quarterly automated re-run of S1–S6 with results to AI Governance | All | AI Governance |

## 8. Recurrence Schedule

Shadow-AI discovery is repeated **quarterly** (S1–S6 automated, S7–S8 manual), and results feed the [Intake Process](Intake-Process.md) as discovered entries.
