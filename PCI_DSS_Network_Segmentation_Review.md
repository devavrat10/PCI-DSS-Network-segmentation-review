# PCI DSS Network Segmentation Review

**Scope validation and segmentation effectiveness assessment**

| Item | Detail |
| --- | --- |
| **Client** | Progress Commtech (fictional e-commerce platform provider) |
| **Engagement** | PCI DSS network segmentation and scope review |
| **Document version** | 1.0 |
| **Date** | 17 October 2026 |
| **Status** | Draft for management discussion |
| **Classification** | Confidential (illustrative portfolio scenario) |
| **Prepared by** | Devavrat Yadav |

> [!NOTE]
> This document is a portfolio exercise based on a **fictional scenario**. Progress Commtech, its network, IP addresses and products are illustrative. Nothing in this document constitutes a statement of PCI DSS compliance or a formal assessment by a Qualified Security Assessor.

> **Word version:** a printable copy of this report is available in [PCI_DSS_Network_Segmentation_Review.docx](PCI_DSS_Network_Segmentation_Review.docx).

## Document control

### Revision history

| Version | Description | Author |
| --- | --- | --- |
| 0.1 | Original project brief and enhanced architecture | Taimuri Jlal (original project brief); Devavrat Yadav (enhancements) |
| 1.0 | Restructured as a client-style assessment report: executive summary, claims analysis, provisional scope classification, nine detailed findings (F-09 added), QSA question bank, remediation roadmap, residual risk register; questionnaire expanded from 61 to 86 questions (new Section I on e-commerce payment page security); glossary and diagrams added | Devavrat Yadav |

### Distribution

| Recipient | Role in review |
| --- | --- |
| Chief Information Security Officer | Decision owner |
| Head of Infrastructure and Network Security | Review |
| Head of Engineering | Review |
| Compliance Lead | Review |

## Contents

- [1. Executive summary](#1-executive-summary)
  - [1.1 Context](#11-context)
  - [1.2 Overall conclusion](#12-overall-conclusion)
  - [1.3 Headline findings](#13-headline-findings)
  - [1.4 Priority recommendations](#14-priority-recommendations)
  - [1.5 Decisions requested from management](#15-decisions-requested-from-management)
- [2. Engagement overview](#2-engagement-overview)
  - [2.1 Background](#21-background)
  - [2.2 Objectives](#22-objectives)
  - [2.3 Scope of review](#23-scope-of-review)
  - [2.4 Approach and methodology](#24-approach-and-methodology)
  - [2.5 Basis of preparation, assumptions and limitations](#25-basis-of-preparation-assumptions-and-limitations)
  - [2.6 Risk rating methodology](#26-risk-rating-methodology)
- [3. Current-state architecture and documentation claims](#3-current-state-architecture-and-documentation-claims)
  - [3.1 Network architecture as documented](#31-network-architecture-as-documented)
  - [3.2 Documentation claims versus initial observations](#32-documentation-claims-versus-initial-observations)
  - [3.3 Initial observations on the documented design](#33-initial-observations-on-the-documented-design)
- [4. Phase 1: Defining the true CDE boundary](#4-phase-1-defining-the-true-cde-boundary)
  - [4.1 Guiding questions](#41-guiding-questions)
  - [4.2 Cardholder data flow](#42-cardholder-data-flow)
  - [4.3 Provisional scope classification](#43-provisional-scope-classification)
- [5. Phase 2: Trust relationships across the segmentation boundary](#5-phase-2-trust-relationships-across-the-segmentation-boundary)
  - [5.1 Trust relationship risk summary](#51-trust-relationship-risk-summary)
  - [5.2 Trust relationship matrix (working template)](#52-trust-relationship-matrix-working-template)
- [6. Phase 3: Segmentation weaknesses and gap analysis](#6-phase-3-segmentation-weaknesses-and-gap-analysis)
  - [6.1 Common segmentation failure patterns](#61-common-segmentation-failure-patterns)
  - [6.2 Gap analysis summary](#62-gap-analysis-summary)
  - [6.3 Detailed findings](#63-detailed-findings)
- [7. Phase 4: QSA challenge preparation](#7-phase-4-qsa-challenge-preparation)
  - [7.1 Finding write-up template](#71-finding-write-up-template)
  - [7.2 Anticipated lines of QSA questioning](#72-anticipated-lines-of-qsa-questioning)
- [8. Phase 5: Scope determination](#8-phase-5-scope-determination)
  - [8.1 Scope determination document structure](#81-scope-determination-document-structure)
  - [8.2 Scope decision principles](#82-scope-decision-principles)
  - [8.3 Exclusion register (template)](#83-exclusion-register-template)
- [9. Recommended target-state architecture](#9-recommended-target-state-architecture)
  - [9.1 Design intent](#91-design-intent)
  - [9.2 Proposed logical architecture](#92-proposed-logical-architecture)
  - [9.3 Proposed server and appliance inventory](#93-proposed-server-and-appliance-inventory)
  - [9.4 Segmentation and traffic-control principles](#94-segmentation-and-traffic-control-principles)
  - [9.5 Illustrative permitted-flow matrix](#95-illustrative-permitted-flow-matrix)
- [10. Remediation roadmap and residual risk](#10-remediation-roadmap-and-residual-risk)
  - [10.1 Remediation roadmap](#101-remediation-roadmap)
  - [10.2 Residual risk register](#102-residual-risk-register)
  - [10.3 Implementation notes](#103-implementation-notes)
- [11. Assessment questionnaire](#11-assessment-questionnaire)
  - [A. Scope, payment model and data flows](#a-scope-payment-model-and-data-flows)
  - [B. Segmentation architecture and firewall controls](#b-segmentation-architecture-and-firewall-controls)
  - [C. Identity, authentication and privileged access](#c-identity-authentication-and-privileged-access)
  - [D. Shared services and security-impacting systems](#d-shared-services-and-security-impacting-systems)
  - [E. Logging, monitoring and data protection](#e-logging-monitoring-and-data-protection)
  - [F. Backup, resilience and recovery](#f-backup-resilience-and-recovery)
  - [G. Change management, cloud and third parties](#g-change-management-cloud-and-third-parties)
  - [H. Governance, evidence and QSA readiness](#h-governance-evidence-and-qsa-readiness)
  - [I. E-commerce payment page and application security](#i-e-commerce-payment-page-and-application-security)
- [12. Engagement deliverables and quality criteria](#12-engagement-deliverables-and-quality-criteria)
  - [12.1 Deliverables checklist](#121-deliverables-checklist)
  - [12.2 Quality criteria](#122-quality-criteria)
  - [12.3 Red flags in a weak review](#123-red-flags-in-a-weak-review)
- [Appendix A: Interview and evidence tracker](#appendix-a-interview-and-evidence-tracker)
- [Appendix B: Reviewer reflection questions](#appendix-b-reviewer-reflection-questions)
- [Appendix C: Glossary](#appendix-c-glossary)
- [Appendix D: References](#appendix-d-references)

---

## 1. Executive summary

### 1.1 Context

Progress Commtech is an e-commerce platform provider that processes approximately 4 million card transactions a year and is pursuing PCI DSS compliance. Management believes it has implemented network segmentation that isolates the Cardholder Data Environment (CDE) from the rest of the estate, and intends to rely on that segmentation to limit the scope of the PCI DSS assessment.

This review tests that belief. We treated segmentation as a **risk claim that must be defended with evidence**, not as a diagram to be accepted. Our work covered five phases: defining the true CDE boundary, examining trust relationships, identifying segmentation weaknesses, preparing for Qualified Security Assessor (QSA) challenge, and documenting a scope determination that would withstand audit.

### 1.2 Overall conclusion

> [!IMPORTANT]
> **Overall conclusion (preliminary)**
>
> On the evidence available, the documented segmentation **cannot yet be relied upon to reduce PCI DSS scope**. None of the four management claims about segmentation is fully supported: two are unsupported and two are only partly supported by the documented architecture. Until the gaps below are validated and closed, we recommend Progress Commtech plans its assessment on a materially **larger scope** than the CDE subnet (10.3.0.0/24) that is currently documented.

Overall segmentation assurance rating: **High risk**. This is a preliminary, document-based rating. Findings are hypotheses to be confirmed through the evidence requests in Appendix A.

### 1.3 Headline findings

| ID | Finding | Severity | Scope consequence |
| --- | --- | --- | --- |
| F-09 | The CDE is defined by subnet, not by data flow. The documented data flow shows card numbers (PAN) entering through DMZ web servers that sit outside the labelled CDE. | 🔴 **Critical** | DMZ web tier likely in CDE scope |
| F-05 | The corporate /16 is broad, and no enforceable inter-zone rules are shown. Only the perimeter firewall is depicted. | 🔴 **Critical** | Corporate network may be in scope if segmentation cannot be demonstrated |
| F-01 | Shared corporate identity (Active Directory) may undermine isolation. | 🟠 **High** | AD and its administrators become security-impacting |
| F-02 | Administration is concentrated on a single jump host located in the corporate zone. | 🟠 **High** | Jump host and admin workstations in scope |
| F-04 | The backup chain may contain card data and sits outside the CDE. | 🟠 **High** | Backup systems, media and admins in scope |
| F-06 | Shared management tools (patching, EDR, scanning, DNS/NTP) may hold privileged paths into the CDE. | 🟠 **High** | Management plane in scope |
| F-03 | The logging pipeline may receive card data. | 🟡 **Medium** | SIEM and collectors may be in scope |
| F-07 | Tokenization may be wrongly treated as an automatic scope reduction. | 🟡 **Medium** | Token systems and vault access remain in scope |
| F-08 | Outbound connections and third-party access are not shown. | 🟡 **Medium** | Egress paths and TPSP access unscoped |

### 1.4 Priority recommendations

1. **Stop relying on the “fully segmented” statement** in external or audit-facing material until it has been evidenced by firewall rule exports and denied-path testing (F-05, F-09).
2. **Redraw the CDE from actual data flows**, including the DMZ web tier, TLS termination points, logging and backup paths (F-09, F-03, F-04).
3. **Separate identity and privileged access** for the CDE: dedicated or tightly controlled authentication, MFA, and redundant hardened jump hosts behind a PAM solution (F-01, F-02).
4. **Deploy default-deny internal segmentation** with named rule owners and scheduled rule reviews (F-05).
5. **Evaluate reducing the card-data footprint** at source, for example hosted payment fields or redirect so that PAN never touches Progress Commtech’s web tier. This is the single change most likely to shrink scope (F-07, F-09).

### 1.5 Decisions requested from management

| # | Decision | Needed by |
| --- | --- | --- |
| 1 | Approve a conservative interim scope (CDE + DMZ web tier + security-impacting systems) for planning purposes. | Within 2 weeks |
| 2 | Name an accountable owner for the CDE, segmentation controls and residual risk. | Within 2 weeks |
| 3 | Fund the identity, PAM and internal-firewall workstreams described in Section 10. | Next budget cycle |
| 4 | Decide whether to pursue payment-page redesign to reduce PAN exposure (strategic option). | Within 60 days |

## 2. Engagement overview

### 2.1 Background

| Attribute | Detail |
| --- | --- |
| Client | Progress Commtech |
| Industry | E-commerce platform provider |
| Annual card transactions | Approximately 4 million |
| Current state | Pursuing PCI DSS compliance; has implemented what it believes is network segmentation |
| Reviewer role | Assess the segmentation and determine whether it reduces PCI DSS scope or creates a false sense of security |
| Standard | PCI DSS v4.0 as stated in the brief. See Section 2.5 regarding v4.0.1 |

### 2.2 Objectives

- Establish what actually constitutes the CDE, based on how cardholder data (CHD) really flows.
- Identify every trust relationship that crosses the claimed segmentation boundary.
- Document where the segmentation design does not hold up, with specific, evidence-led findings.
- Anticipate the questions a QSA will ask and define the evidence needed to answer them.
- Produce a scope determination that would survive audit, with defended exclusions and documented residual risk.

### 2.3 Scope of review

| In scope | Out of scope |
| --- | --- |
| Documented network architecture and zones<br>CHD data flows and payment channels<br>Trust relationships: authentication, DNS, NTP, patching, backup, logging, administration<br>Firewall and segmentation design, and target-state design<br>QSA readiness and evidence requirements | Technical penetration testing and live configuration review (to be performed in the validation phase)<br>Application code review<br>Formal Report on Compliance (ROC) or attestation of compliance<br>Business continuity and non-payment compliance regimes |

### 2.4 Approach and methodology

The review follows five sequential phases. Each phase produces a defined deliverable that feeds the next.

| Phase | Objective | Key activities | Deliverable |
| --- | --- | --- | --- |
| 1. Define the true CDE boundary | Identify what actually constitutes the CDE, not what people wish it were | Trace CHD flows end to end; identify connected-to and security-impacting systems | CHD data flow diagram |
| 2. Examine trust relationships | Map every trust relationship crossing the boundary | Review authentication, DNS, time, patching, backup, logging, administration | Trust relationship matrix with risk rating |
| 3. Identify segmentation weaknesses | Document where the design does not hold up | Test the design against known failure patterns | Segmentation gap analysis (minimum 5 findings) |
| 4. Anticipate QSA challenges | Pre-empt assessor questions | Write each finding with the likely challenge and required evidence | QSA challenge preparation document |
| 5. Document scope determination | Create a final scope position that survives audit | Record inclusions and exclusions with rationale | Scope determination document |

### 2.5 Basis of preparation, assumptions and limitations

- This report is based on the documented architecture and management’s documentation claims. **No live configuration, rule base or traffic has been examined**; all findings are therefore preliminary and must be validated against evidence.
- **PCI DSS version.** The brief refers to v4.0. v4.0 was retired on 31 December 2024 and v4.0.1 is the current version; the future-dated requirements of v4.0 became mandatory on 31 March 2025. Progress Commtech should confirm the version applicable to its assessment.
- **Merchant or service provider.** The scenario does not state whether Progress Commtech processes cards only for itself or on behalf of merchants. A platform provider that does so is likely a service provider, with stricter obligations (for example more frequent segmentation testing and scope confirmation). This must be confirmed early.
- The scenario is **fictional** and all IP addresses, host counts and products are illustrative placeholders, not a production implementation plan.
- This report demonstrates an assessment approach. It is **not a statement of PCI DSS compliance**; actual compliance requires evidence and formal assessment by a QSA.

### 2.6 Risk rating methodology

Each finding is rated on the likelihood that the weakness is exploited or the claim fails under assessor scrutiny, and the impact on PCI DSS scope and cardholder-data security.

| Rating | Definition | Expected response |
| --- | --- | --- |
| 🔴 **Critical** | The segmentation claim is likely invalid, or scope is materially understated. | Immediate action; escalate to executive owner |
| 🟠 **High** | A credible path exists by which compromise outside the CDE could affect the CDE. | Remediate within 90 days |
| 🟡 **Medium** | Weakness likely to expand scope or draw assessor challenge; exploitation less direct. | Remediate within 180 days |
| 🟢 **Low** | Minor or documentary weakness with limited scope or security effect. | Track and fix in normal cycle |

## 3. Current-state architecture and documentation claims

### 3.1 Network architecture as documented

Figure 1 reproduces the architecture described in Progress Commtech’s documentation. A single perimeter firewall (Palo Alto PA-850) fronts a DMZ and a corporate zone. The CDE and a management zone sit below, and the boundary between the corporate/DMZ zones and the CDE is described as the “claimed segmentation boundary”.

<p align="center"><img src="../images/current.png" alt="Figure 1: Current-state network architecture as documented (illustrative)" width="560"><br><em>Figure 1: Current-state network architecture as documented (illustrative)</em></p>

| Zone | Subnet | Assets |
| --- | --- | --- |
| DMZ | 10.1.0.0/24 | Load balancer (10.1.0.5); 3 web servers (10.1.0.10–12) |
| Corporate | 10.2.0.0/16 | AD domain controllers (10.2.1.10–11); file servers (10.2.1.20–22); jump host (10.2.1.50); workstations (10.2.2.0/24) |
| CDE | 10.3.0.0/24 | Payment app server (10.3.0.10); card database (10.3.0.20); HSM (10.3.0.30); tokenization service (10.3.0.40); log collector, Splunk forwarder (10.3.0.50) |
| Management | 10.4.0.0/24 | SIEM, Splunk (10.4.0.10); backup server (10.4.0.20); patch management, WSUS (10.4.0.30) |

### 3.2 Documentation claims versus initial observations

| # | Management claim | Initial observation from the documented design | Status | Evidence to validate |
| --- | --- | --- | --- | --- |
| 1 | “The CDE is fully segmented from the corporate network” | Only the perimeter firewall is shown. No internal firewall, ACLs or rule base are depicted. Corporate AD, the jump host and management tools sit outside the CDE yet interact with it. | ❌ Unsupported | Firewall and routing exports; denied-path test results from corporate to CDE |
| 2 | “Only authorized systems can communicate with the CDE” | No allow-list, rule ownership or business justification is documented. The CDE receives traffic from the DMZ, the jump host, the management zone and any shared services. | ❌ Unsupported | Rule base with justification; rule review records; flow matrix |
| 3 | “Administrative access requires going through the jump host” | A jump host exists, but it is a single host inside the corporate zone. Alternative paths (console, hypervisor, vendor access, direct database admin) are not addressed. | ⚠️ Partially supported | Network path tests; PAM and MFA configuration; session records |
| 4 | “All CDE traffic is logged and monitored” | A log collector in the CDE forwards to the SIEM in the management zone, but “all traffic” is an unevidenced absolute. Log content (possible PAN) and alerting are not documented. | ⚠️ Partially supported | Log source inventory; sample logs; alert catalogue; SIEM coverage |

### 3.3 Initial observations on the documented design

- **The CDE is drawn as a subnet.** PCI DSS scope follows data and connectivity, not subnet labels (see Section 4).
- **The jump host is in the corporate zone**, so the administrative path into the CDE begins in a zone that is claimed to be separate.
- **The management zone depends on the CDE and vice versa.** The log collector pushes data out to the SIEM, the backup server reaches the card database, and WSUS pushes software in.
- **The corporate zone is a flat /16** that includes domain controllers, file servers and user workstations.
- **Outbound connections are not shown**, including the payment processor, vendor remote access and update sources.

## 4. Phase 1: Defining the true CDE boundary

The objective is to identify what actually constitutes the CDE. Under PCI DSS, scope includes systems that store, process or transmit cardholder data; systems that are connected to those systems; and systems that could affect the security of the CDE.

### 4.1 Guiding questions

| # | Question | Why it matters |
| --- | --- | --- |
| 1 | Which systems store, process or transmit cardholder data? | Defines the core CDE |
| 2 | Which systems are connected to systems that handle CHD? | Connected-to systems are in scope |
| 3 | Which systems could impact the security of the CDE? | Security-impacting systems are in scope |
| 4 | Where does cardholder data actually flow? | Scope follows data, not diagrams |
| 5 | Where is TLS terminated, and which components see PAN in clear text? | Load balancers and WAFs that decrypt traffic become CDE components |
| 6 | Does the payment page post card data to Progress Commtech, or to a third-party hosted field or redirect? | Determines whether the web tier is in the CDE |
| 7 | Is PAN ever received outside the main flow (support tickets, email, chat, call recordings, batch files)? | Hidden CHD repositories expand scope |
| 8 | Is sensitive authentication data (SAD) ever stored after authorization, even temporarily in logs or queues? | Storage of SAD after authorization is prohibited |

### 4.2 Cardholder data flow

Figure 2 traces the documented flow of card data. The brief’s own analysis shows PAN entering at the web server. This has an important consequence: **the DMZ web tier transmits PAN and therefore cannot sit outside the CDE** (Finding F-09). Dashed paths are unverified and must be tested.

<p align="center"><img src="../images/flow.png" alt="Figure 2: Cardholder data flow with unverified paths (illustrative)" width="520"><br><em>Figure 2: Cardholder data flow with unverified paths (illustrative)</em></p>

The open question the data flow raises is: **is the log server in scope, and what data does it receive?** If application or database logs contain PAN, the log collector, SIEM and anything that administers them are in scope.

### 4.3 Provisional scope classification

The table below classifies each documented component using the three PCI DSS scope categories. It is provisional and must be confirmed with the evidence requested.

| Component | Provisional classification | Rationale | To confirm |
| --- | --- | --- | --- |
| Web servers (3), 10.1.0.10–12 | CDE | Per the documented flow, PAN is entered here and transmitted onwards | Payment page model; whether PAN transits or is only redirected |
| Load balancer, 10.1.0.5 | CDE or connected-to | Becomes CDE if it terminates TLS and sees PAN in clear text | TLS termination point |
| Payment app server, 10.3.0.10 | CDE | Processes and transmits PAN | None |
| Card database, 10.3.0.20 | CDE | Stores cardholder data | Stored data elements; retention |
| HSM, 10.3.0.30 | CDE | Protects cryptographic keys used on CHD | Key custody and admin access |
| Tokenization service, 10.3.0.40 | CDE | Handles PAN to generate tokens | Detokenization rights |
| Log collector, 10.3.0.50 | CDE | Resides in CDE; may receive PAN in logs | Log content |
| Perimeter firewall (PA-850) | Security-impacting | Network security control enforcing the boundary | Rule base; admin access |
| AD domain controllers, 10.2.1.10–11 | Security-impacting (pending) | In scope if CDE authentication or admin depends on corporate AD | CDE authentication dependency |
| Jump host, 10.2.1.50 | Security-impacting | Sole documented administrative path into the CDE | Hardening; MFA; routes |
| Admin workstations (within 10.2.2.0/24) | Security-impacting | Used to administer the CDE via the jump host | Which workstations; hardening |
| Other workstations and file servers | Out of scope (conditional) | Exclusion defensible only if denied-path tests prove isolation | Negative connectivity tests; no CHD in files |
| SIEM, 10.4.0.10 | Security-impacting; CDE if PAN in logs | Receives CDE logs; admins could alter monitoring | Log content; admin roles |
| Backup server, 10.4.0.20 | CDE if backups hold CHD | Reaches the card database and stores copies | Backup data inventory |
| Patch management (WSUS), 10.4.0.30 | Security-impacting | Pushes software to CDE systems | Update integrity; admin roles |

## 5. Phase 2: Trust relationships across the segmentation boundary

A trust relationship exists wherever a system inside the CDE relies on, or can be influenced by, a system outside it. Each such dependency is a potential way for the CDE to be affected, and so a potential scope expansion.

### 5.1 Trust relationship risk summary

| Connection type | Question | Risk level | Why |
| --- | --- | --- | --- |
| Authentication | Does the CDE use corporate AD? | 🟠 **High** | AD compromise yields CDE credentials |
| Administration | Who has admin access, and from where? | 🟠 **High** | Admin paths bypass network controls |
| Patching | How are CDE systems patched? | 🟠 **High** | Update channel can deliver malicious code |
| Backup | Where do backups go, and do they contain CHD? | 🟠 **High** | Backup copies extend the CHD footprint |
| Third-party / vendor access | Which vendors can reach the CDE, and how? | 🟠 **High** | Remote access is a common breach path |
| DNS | Where do CDE systems resolve DNS? | 🟡 **Medium** | Poisoned resolution can redirect flows |
| Logging | Where do logs flow, and what do they contain? | 🟡 **Medium** | Logs can leak PAN and be altered |
| Vulnerability scanning / EDR | What privileged access do these tools hold? | 🟡 **Medium** | Management consoles control many hosts |
| Certificates / PKI | Who issues and manages CDE certificates? | 🟡 **Medium** | Rogue certificates enable interception |
| Time sync | What is the NTP source for CDE systems? | 🟢 **Low** | Affects log integrity and correlation |

### 5.2 Trust relationship matrix (working template)

This matrix is completed during validation. “Initial risk” is deliberately conservative: it remains as shown until evidence supports a lower rating.

| Dependency | Source → destination | Purpose / protocol | Can impact CDE? | Evidence to request | Initial risk |
| --- | --- | --- | --- | --- | --- |
| Corporate AD / identity | To be confirmed | Authentication / directory protocols | Yes / No / TBD | Architecture, auth config, privileged access test | High until validated |
| DNS / NTP | To be confirmed | Name resolution / time sync | Yes / No / TBD | Resolver config, ACLs, NTP config, logs | Medium until validated |
| Patching / EDR | To be confirmed | Software and endpoint management | Yes / No / TBD | Management paths, update integrity, admin roles | High until validated |
| Backup / recovery | To be confirmed | Backup, restore, replication | Yes / No / TBD | Data inventory, backup ACLs, restore test | High until validated |
| Logging / SIEM | To be confirmed | Security telemetry | Yes / No / TBD | Sample logs, masking, collector permissions | Medium until validated |
| PAM / jump hosts | To be confirmed | Privileged administration | Yes / No / TBD | MFA, session records, host hardening, route tests | High until validated |
| Vulnerability scanning | To be confirmed | Assessment and discovery | Yes / No / TBD | Scan scope, credentials, reports, remediation tickets | Medium until validated |
| Third-party / processor | To be confirmed | Authorization / support / remote access | Yes / No / TBD | Contracts, data flow, responsibility matrix | High until validated |

## 6. Phase 3: Segmentation weaknesses and gap analysis

### 6.1 Common segmentation failure patterns

| Pattern | How it manifests | Scope consequence |
| --- | --- | --- |
| 1. Shared services | Corporate zone and CDE both authenticate against the same AD. If AD is compromised, the attacker holds credentials for the CDE. | AD becomes security-impacting |
| 2. Jump host as single point | Admin workstation → jump host → CDE. Compromise of the jump host gives access to the entire CDE. | Jump host and its admins in scope |
| 3. Log aggregation | CDE systems send logs, possibly containing PAN, to a SIEM outside the CDE. | If logs contain CHD, the SIEM is in scope |
| 4. Backup paths | CDE database → backup server → tape or other storage. Backups contain CHD. | Entire backup chain is in scope |
| 5. Broad network ranges and implicit trust | Large subnets (such as a corporate /16) and permissive rules allow unintended paths. | Unintended systems become connected |
| 6. Management plane reach | Patch, EDR, scanner and configuration tools hold privileged access into the CDE. | Tool and tool administrators in scope |

### 6.2 Gap analysis summary

| ID | Finding | Pattern | Severity | Primary validation |
| --- | --- | --- | --- | --- |
| F-01 | Shared corporate identity may undermine isolation | 1 | 🟠 **High** | CDE authentication dependency; privileged access test |
| F-02 | Administrative path concentrated on a single jump host | 2 | 🟠 **High** | Route tests; PAM and MFA review |
| F-03 | Logging pipeline may receive card data | 3 | 🟡 **Medium** | Log sampling for PAN/SAD |
| F-04 | Backup chain may extend scope | 4 | 🟠 **High** | Backup data inventory and access review |
| F-05 | Broad corporate subnet and unclear conduits | 5 | 🔴 **Critical** | Firewall export; denied-path tests |
| F-06 | Shared management tools can impact the CDE | 6 | 🟠 **High** | Management tool access mapping |
| F-07 | Tokenization treated as automatic scope reduction | n/a | 🟡 **Medium** | Detokenization and vault access review |
| F-08 | Outbound connections and third-party access not shown | 5, 6 | 🟡 **Medium** | Egress rules; TPSP register |
| F-09 | CDE defined by subnet rather than data flow | n/a | 🔴 **Critical** | Payment-page model; TLS termination; flow trace |

F-01 to F-08 are taken from the original project; F-09 has been added because the documented data flow contradicts the documented CDE boundary.

### 6.3 Detailed findings

Each finding follows the standard write-up template (Section 7.1). The “reality” field records what the documented design implies; it is a hypothesis until evidenced.

#### F-09 — CDE defined by subnet rather than by cardholder data flow

| Field | Detail |
| --- | --- |
| **Severity** | 🔴 **Critical** |
| **Current state** | The CDE is documented as 10.3.0.0/24. The documented data flow shows PAN entering through DMZ web servers (10.1.0.10–12) and passing to the payment application by API call. |
| **Segmentation claim** | The CDE is the 10.3.0.0/24 subnet and everything else is outside it. |
| **Reality (to validate)** | Components that transmit PAN are in the CDE regardless of subnet. The web servers, and any load balancer, WAF or CDN that terminates TLS, are likely CDE components. The documented boundary understates scope. |
| **PCI DSS scope impact** | DMZ web tier, TLS-terminating components and their administrators move into the CDE. |
| **QSA challenge** | “Show me the data flow diagram. Where is PAN first received, and which components can see it in clear text?” |
| **Evidence required** | Current data flow diagram; payment page implementation (direct post, iframe, redirect); TLS termination configuration; packet or log evidence of where PAN appears. |
| **Recommendation** | Redraw the CDE from validated data flows. Evaluate a hosted payment field or redirect model so that PAN bypasses Progress Commtech’s web tier altogether. |
| **Residual risk** | Even after redesign, any component that can influence the payment page remains security-impacting. |

#### F-05 — Broad corporate subnet and unclear conduits

| Field | Detail |
| --- | --- |
| **Severity** | 🔴 **Critical** |
| **Current state** | The corporate zone is a /16 containing domain controllers, file servers and user workstations. Only a perimeter firewall is shown. |
| **Segmentation claim** | “The CDE is fully segmented from the corporate network.” |
| **Reality (to validate)** | No internal rule base, ACL or security group is evidenced. Routing may allow corporate hosts to reach the CDE. |
| **PCI DSS scope impact** | If segmentation cannot be demonstrated, the corporate network is connected to the CDE and in scope. |
| **QSA challenge** | “Demonstrate that a corporate workstation cannot initiate a connection to any CDE host.” |
| **Evidence required** | Firewall, routing, ACL and cloud security-group exports; rule review records; negative connectivity test results from representative source networks. |
| **Recommendation** | Introduce an internal segmentation firewall with default-deny, named rule owners, justified allow rules and periodic review. Run segmentation tests after every significant change. |
| **Residual risk** | Configuration drift between tests; mitigated by continuous rule monitoring. |

#### F-01 — Shared corporate identity may undermine isolation

| Field | Detail |
| --- | --- |
| **Severity** | 🟠 **High** |
| **Current state** | AD domain controllers sit in the corporate zone. The documentation does not state how CDE systems authenticate. |
| **Segmentation claim** | Only authorized systems can communicate with the CDE; the CDE is isolated. |
| **Reality (to validate)** | If CDE systems or administrators rely on corporate AD, compromise of the corporate identity plane can yield CDE credentials. |
| **PCI DSS scope impact** | AD domain controllers and their administrators become security-impacting; shared credentials can bypass network segmentation. |
| **QSA challenge** | “If I compromise a domain admin in corporate AD, can I authenticate to or reset credentials in the CDE?” |
| **Evidence required** | Identity architecture; CDE authentication configuration; group memberships; MFA settings; privileged access test results. |
| **Recommendation** | Use a dedicated identity source for the CDE (or a strictly controlled trust) with MFA for all access into the CDE. Do not assume AD is out of scope by label. |
| **Residual risk** | Compromise of the dedicated identity service remains a path to the CDE. |

#### F-02 — Administrative path concentrated on a single jump host

| Field | Detail |
| --- | --- |
| **Severity** | 🟠 **High** |
| **Current state** | One jump host (10.2.1.50) in the corporate zone is the documented administrative route into the CDE. |
| **Segmentation claim** | “Administrative access requires going through the jump host.” |
| **Reality (to validate)** | A single host creates both an availability and a compromise-concentration risk. Because it sits in the corporate zone, a corporate-side compromise can reach it. Other admin paths may exist. |
| **PCI DSS scope impact** | The jump host, its administrators and admin workstations are in scope. A jump host is not a substitute for network controls. |
| **QSA challenge** | “Can administrators reach CDE hosts by any route other than the jump host? Show me the denied attempts.” |
| **Evidence required** | Route and firewall tests; PAM and MFA configuration; host hardening baseline; session recordings; list of authorized administrators. |
| **Recommendation** | Deploy redundant hardened jump hosts in a dedicated privileged-access segment, behind a PAM solution with MFA, time-bound approval and session recording; restrict routes to approved protocols. |
| **Residual risk** | Privileged credential theft or insider abuse through a legitimate admin path. |

#### F-04 — Backup chain may extend scope

| Field | Detail |
| --- | --- |
| **Severity** | 🟠 **High** |
| **Current state** | A backup server (10.4.0.20) resides in the management zone. Backup contents, destinations and access controls are not documented. |
| **Segmentation claim** | The CDE is isolated and only authorized systems can communicate with it. |
| **Reality (to validate)** | If backups, snapshots, replicas or recovery environments contain CHD, they carry the CHD footprint outside the CDE. |
| **PCI DSS scope impact** | Backup server, storage, media, recovery sites and their administrators are in scope. |
| **QSA challenge** | “Which backups contain cardholder data, who can read or restore them, and how are they encrypted?” |
| **Evidence required** | Backup data inventory; backup ACLs and credentials; encryption and key details; restore-test evidence. |
| **Recommendation** | Protect the whole backup chain. Use separate backup credentials and paths, encryption, immutable or offline copies, and test restores including access controls. |
| **Residual risk** | Recovery operations temporarily widen access; require approval and logging. |

#### F-06 — Shared management tools can impact the CDE

| Field | Detail |
| --- | --- |
| **Severity** | 🟠 **High** |
| **Current state** | WSUS (10.4.0.30) and the SIEM sit in the management zone. EDR, vulnerability scanning, configuration management and DNS/NTP are not depicted. |
| **Segmentation claim** | Only authorized systems can communicate with the CDE. |
| **Reality (to validate)** | Patch, EDR, scanning and configuration tools often hold privileged paths into the CDE. An attacker controlling the tool controls the hosts it manages. |
| **PCI DSS scope impact** | These tools and their administrators are security-impacting systems and in scope. |
| **QSA challenge** | “Which management tools can run code or change configuration on CDE hosts, and who administers them?” |
| **Evidence required** | Tool inventory; agent and console permissions; update integrity controls; admin role lists. |
| **Recommendation** | Document each dependency. Use initiated or brokered flows only, restrict control-plane access, enforce MFA on consoles, and verify update integrity. |
| **Residual risk** | Supply-chain compromise of a management product. |

#### F-03 — Logging pipeline may receive card data

| Field | Detail |
| --- | --- |
| **Severity** | 🟡 **Medium** |
| **Current state** | A log collector in the CDE forwards to a SIEM in the management zone. Log content is not documented. |
| **Segmentation claim** | “All CDE traffic is logged and monitored.” |
| **Reality (to validate)** | Application logs, database logs, traces and error dumps can contain PAN or sensitive authentication data. |
| **PCI DSS scope impact** | If logs contain CHD, the SIEM, collectors and their administrators are in scope. SAD must not be retained after authorization, even if encrypted. |
| **QSA challenge** | “Show me a sample of raw application and database logs. Do any contain full PAN?” |
| **Evidence required** | Log source inventory; sampled log review; masking or filtering rules; SIEM access roles. |
| **Recommendation** | Filter or mask card data at source before forwarding; verify using representative samples; confirm whether any receiving system is now in scope. |
| **Residual risk** | New code or debug settings can reintroduce PAN into logs; add a recurring scan. |

#### F-07 — Tokenization treated as automatic scope reduction

| Field | Detail |
| --- | --- |
| **Severity** | 🟡 **Medium** |
| **Current state** | A tokenization service (10.3.0.40) resides in the CDE. The documentation does not describe how tokens are used downstream. |
| **Segmentation claim** | Implicit: systems consuming tokens are outside scope. |
| **Reality (to validate)** | Systems that can reverse tokens, access the token vault, or otherwise affect the CDE remain in scope. Tokens alone do not prove an exclusion. |
| **PCI DSS scope impact** | Token consumers may be out of scope only if they cannot obtain PAN or affect the token service. |
| **QSA challenge** | “Can any system outside the CDE detokenize, and how is that access controlled?” |
| **Evidence required** | Token architecture; detokenization API permissions; vault access controls; list of token consumers. |
| **Recommendation** | Document token flows and restrict detokenization to named CDE services; justify each exclusion explicitly. |
| **Residual risk** | Token vault compromise exposes PAN. |

#### F-08 — Outbound connections and third-party access not shown

| Field | Detail |
| --- | --- |
| **Severity** | 🟡 **Medium** |
| **Current state** | The architecture shows no payment-processor connection, vendor remote access, DNS, update or monitoring egress. |
| **Segmentation claim** | Only authorized systems can communicate with the CDE. |
| **Reality (to validate)** | Outbound paths and vendor access create additional connections to and from the CDE that are not evidenced. |
| **PCI DSS scope impact** | Egress paths and third-party service providers (TPSPs) are part of the scope picture and responsibility matrix. |
| **QSA challenge** | “List every outbound destination from the CDE and every third party with access, with business justification.” |
| **Evidence required** | Egress rules; proxy logs; TPSP list with attestations and responsibility matrices; remote-access records. |
| **Recommendation** | Inventory all egress and vendor access; enforce destination allow-lists; make vendor access disabled by default, time-bound, MFA-protected and monitored. |
| **Residual risk** | A trusted third party is itself compromised. |

## 7. Phase 4: QSA challenge preparation

A QSA will test whether Progress Commtech’s scope argument still stands when challenged. Preparation means knowing the likely question, and holding the evidence that answers it, before the assessment begins.

### 7.1 Finding write-up template

Every finding in this report, and every finding raised during validation, follows this structure.

| Field | Detail |
| --- | --- |
| **Finding ID and title** | Unique identifier and concise risk statement |
| **Severity and rationale** | Critical / High / Medium / Low, with business and technical rationale |
| **Current state** | What was observed and which evidence supports it |
| **Segmentation claim** | What the organization says is isolated or controlled |
| **Reality / validation** | Observed traffic, dependency, access path, or missing evidence |
| **PCI DSS scope impact** | Potential CDE, connected-to or security-impacting scope consequence; validate against applicable requirements |
| **QSA challenge** | The likely question an assessor will ask |
| **Evidence required** | Specific exports, configurations, test results, interviews and samples |
| **Recommendation** | Specific remediation, owner, target date and validation test |
| **Residual risk / acceptance** | Remaining exposure and the accountable risk owner, if applicable |

### 7.2 Anticipated lines of QSA questioning

| Topic | Likely QSA question | What a defensible answer looks like | Evidence to hold ready |
| --- | --- | --- | --- |
| Scope and data flow | Where is PAN first received, and which components see it in clear text? | A current, approved data flow diagram reconciled to the asset inventory | Data flow diagrams; TLS termination config; packet or log samples |
| Segmentation effectiveness | Prove a corporate host cannot reach the CDE. | Test results from representative source networks showing blocked paths and permitted flows | Segmentation test report; firewall exports |
| Rule governance | Who owns each firewall rule and when was it last reviewed? | Every rule has an owner, justification and recent review | Rule base; review records; change tickets |
| Identity | Can a corporate admin obtain CDE access? | CDE authentication is separate or tightly controlled, with MFA | Identity architecture; MFA config; access reviews |
| Privileged access | Are there any administrative paths other than the jump host? | Only the PAM-controlled path exists, and attempts elsewhere are blocked and alerted | Route tests; PAM config; session recordings |
| Logging | Do any logs contain PAN or SAD? | Sampled evidence that card data is masked or absent before forwarding | Sample logs; masking rules; SIEM roles |
| Backups | Which backups hold cardholder data and who can restore them? | A backup data inventory with restricted, encrypted, tested copies | Backup inventory; ACLs; restore test |
| Exclusions | Why is this system out of scope? | A written rationale backed by technical evidence and a review date | Exclusion register; test results |
| Third parties | Which providers can touch the CDE or card data? | A TPSP register with responsibilities and current attestations | TPSP list; contracts; responsibility matrix |
| Change | What happens to scope when something changes? | A defined process triggering scope review and segmentation retest | Change procedure; recent change records |

> [!NOTE]
> **If you were the QSA, what would be your first question?**
>
> “Show me your cardholder data flow diagram, and show me how you know it is complete.” Everything else in the assessment depends on the answer.

## 8. Phase 5: Scope determination

### 8.1 Scope determination document structure

The final scope determination that Progress Commtech presents to its assessor should follow this structure.

| Section | Content |
| --- | --- |
| 1. Executive summary | Scope statement; key findings; risk rating |
| 2. CDE definition | Systems that store CHD; systems that process CHD; systems that transmit CHD |
| 3. Connected systems | Systems with direct connectivity; justification for inclusion or exclusion |
| 4. Security-impacting systems | Systems that could affect CDE security; justification for inclusion or exclusion |
| 5. Segmentation assessment | Controls in place; gaps identified; residual risk |
| 6. Recommendations | Immediate actions; long-term improvements |

### 8.2 Scope decision principles

- Do not declare any system out of scope **based solely on subnet labels or a diagram**. Verify all CHD flows, connectivity and security-impacting paths.
- Use the **actual applicable PCI DSS version, assessment type, payment channel and assessor guidance** when finalizing scope.
- Confirm whether the company stores PAN and whether SAD is ever retained after authorization. The scenario does not give enough evidence to conclude this.
- Record each inclusion or exclusion with a **rationale, evidence reference, control owner and review date**.
- Confirm scope at least annually and after any significant change (more frequently if Progress Commtech is a service provider).

### 8.3 Exclusion register (template)

Every exclusion must be defended. The register below is populated during validation, starting with the conditional exclusions identified in Section 4.3.

| System / network | Proposed exclusion | Technical evidence supporting exclusion | Control owner | Review date |
| --- | --- | --- | --- | --- |
| Workstations, 10.2.2.0/24 (non-admin) | Out of scope | Denied-path tests from this subnet to CDE and jump host | To be assigned | To be set |
| File servers, 10.2.1.20–22 | Out of scope | Negative connectivity tests; data discovery confirming no CHD | To be assigned | To be set |
| Token consumers (downstream systems) | Out of scope | No detokenization rights; no vault access | To be assigned | To be set |

## 9. Recommended target-state architecture

### 9.1 Design intent

The proposed design replaces the broad, ambiguous trust layout with explicit security zones and controlled conduits. It is a **target-state for the fictional scenario, not evidence that these controls are implemented**. Actual scope depends on verified data flows, connectivity, administrative paths and security impact.

### 9.2 Proposed logical architecture

<p align="center"><img src="../images/target.png" alt="Figure 3: Proposed target-state logical architecture (illustrative)" width="650"><br><em>Figure 3: Proposed target-state logical architecture (illustrative)</em></p>

### 9.3 Proposed server and appliance inventory

| Zone / subnet | Proposed assets | Purpose and design justification | Scope / assessment note |
| --- | --- | --- | --- |
| Edge / DMZ<br>10.1.0.0/24 | DDoS/CDN service; WAF; external load balancer; 3 web servers | Protect public entry points; filter web attacks; distribute HTTPS traffic | Web components may be in scope where they transmit CHD or can affect payment-page security; validate the actual payment model |
| API<br>10.1.1.0/24 | API gateway; API management node | Authenticate and rate-limit application calls; enforce approved routes to payment services | Assess whether PAN traverses the API and whether it can impact CDE security |
| Payment tier<br>10.3.1.0/24 | Payment application nodes x2; message broker / queue | Separate payment logic from the public web tier; support controlled processing and resilience | CDE if storing, processing or transmitting CHD; connected and security-impacting systems require evaluation |
| Data tier<br>10.3.2.0/24 | Card database primary/standby; database audit service | Restrict database access to payment services; record privileged and data-access events | In scope if CHD is stored; verify PAN minimization, retention, encryption and key separation |
| Tokenization<br>10.3.3.0/24 | Tokenization service x2; token vault | Replace PAN with tokens for downstream workflows; reduce unnecessary PAN exposure | Tokenization does not automatically remove connected systems from scope; assess reversibility and vault access |
| Key management<br>10.3.4.0/24 | HSM pair; key-management administration service | Protect cryptographic keys and constrain cryptographic operations | Evaluate key custody, admin access, key lifecycle and systems that can affect security |
| Security monitoring<br>10.4.1.0/24 | SIEM indexer; log relay; network detection sensor | Centralize security events and monitor critical paths | Determine whether logs contain PAN/SAD; sanitize logs and tightly control monitoring components |
| Privileged access<br>10.4.2.0/24 | PAM vault; two hardened jump hosts; session recording | Centralize privileged credentials, MFA and auditable admin sessions | Security-impacting; compromise could affect the CDE. Do not treat jump hosts as a substitute for network controls |
| Backup / recovery<br>10.4.3.0/24 | Backup proxy; immutable backup repository; recovery orchestrator | Support recoverability while limiting write access and protecting copies | If backups contain CHD, data and relevant backup systems are in scope; confirm encryption and restoration paths |
| Security services<br>10.4.4.0/24 | Patch repository; EDR management; internal DNS/NTP relays | Provide managed services through explicit firewall rules and logging | Assess whether each service can impact CDE security; restrict administration and prevent broad trust |
| Corporate<br>10.2.0.0/16 | AD domain controllers; corporate file servers; admin workstations; user endpoints | Separate business IT from payment processing | Shared identity or admin paths may expand scope; evaluate whether the CDE relies on corporate AD and whether it can be isolated |
| Resilience / external integrations | Payment processor connector; outbound proxy; vulnerability scanner; configuration management | Support payment authorization, controlled egress, scanning and baseline enforcement | Document vendor connections, allow-listed destinations, scan coverage, service accounts and rule-review evidence |

### 9.4 Segmentation and traffic-control principles

- **Default-deny** between zones; create narrowly scoped allow rules by source, destination, service, protocol and business justification.
- Public traffic terminates at approved edge controls; only explicitly approved application flows reach payment services.
- Only payment application identities reach the card database, on the required database port. No direct user or web-tier database access unless explicitly justified.
- Administrative access uses **MFA-protected PAM and hardened jump hosts**, with named accounts, session logging, time-bound approval and emergency-access review.
- Corporate AD, DNS, NTP, EDR, patching, backup and logging are shared-service dependencies: document their flows and assess their ability to affect CDE security.
- Separate backup management credentials and network paths; use immutable or offline protection where appropriate; test restores.
- Check logs for PAN and sensitive authentication data before centralizing them. Do not retain SAD after authorization, even if encrypted, where PCI DSS prohibits it.
- Perform segmentation testing at the required frequency and after significant changes. Test allowed and denied paths from representative source zones, not just firewall configuration screenshots.
- Maintain a current asset inventory, data flow diagrams, firewall-rule ownership, rule review evidence and approved exceptions.
- All IP addresses and host counts in this document are illustrative placeholders, not a production implementation plan.

### 9.5 Illustrative permitted-flow matrix

| Source | Destination | Allowed flow (illustrative) | Control / evidence to validate |
| --- | --- | --- | --- |
| Internet | WAF / edge | HTTPS 443 only; other ingress denied unless justified | WAF policy, TLS configuration, edge logs |
| WAF / load balancer | Web tier | Approved web application ports only | Firewall rule, health-check definition, change ticket |
| Web / API tier | Payment application | Specific API over TLS; no general subnet-to-subnet access | API authentication, mTLS where suitable, firewall logs |
| Payment application | Card DB | Required DB port only; application service identity | DB ACL, service account permissions, DB audit logs |
| Payment application | HSM | Approved cryptographic API only | HSM policy, key ceremony records, privileged-access evidence |
| Payment application | Tokenization service | Specific tokenization API only | API logs, token-vault access controls |
| CDE assets | Log relay / SIEM | Outbound log forwarding to approved collector; sanitize data | Sample log review, filtering rules, SIEM permissions |
| PAM / jump hosts | CDE administration interfaces | Approved protocols from hardened admin path only; MFA and session recording | PAM configuration, access approvals, session recordings |
| Patch repository / EDR | CDE agents | Initiated or brokered management flows only; no broad inbound trust | Agent policy, repository integrity, firewall rules |
| CDE backup proxy | Immutable backup repository | Dedicated backup protocol; segregated credentials; restricted restore rights | Backup architecture, immutability settings, restore test |
| Corporate endpoints | CDE | Deny by default; no direct user subnet access | Negative tests, firewall exports, exception register |
| CDE | Internet | Deny by default; narrowly allow-listed destinations only where needed | Egress rules, proxy logs, business approvals |

## 10. Remediation roadmap and residual risk

### 10.1 Remediation roadmap

Actions are sequenced so that evidence-gathering and scope-protecting steps come first, followed by architectural change. Owners are shown as roles to be confirmed by management.

| Horizon | Action | Findings | Owner (role) | Validation |
| --- | --- | --- | --- | --- |
| Immediate<br>0–30 days | Withdraw “fully segmented” statements from audit-facing material pending evidence | F-05, F-09 | CISO / Compliance lead | Approved wording; document register |
| Immediate<br>0–30 days | Collect firewall, routing and ACL exports; run denied-path tests from corporate and DMZ to CDE | F-05 | Network security lead | Test report signed off |
| Immediate<br>0–30 days | Confirm payment page model, TLS termination points and where PAN is first received | F-09 | Head of engineering | Updated data flow diagram |
| Immediate<br>0–30 days | Sample application, database, proxy and SIEM logs for PAN and SAD | F-03 | Security operations | Sample review record |
| Immediate<br>0–30 days | Inventory backups and replicas for CHD; list who can read or restore them | F-04 | Infrastructure lead | Backup data inventory |
| Immediate<br>0–30 days | Enforce MFA and block non-jump-host admin routes into the CDE | F-02, F-01 | Infrastructure lead | Route tests; MFA report |
| Near term<br>30–90 days | Provide a dedicated or tightly controlled identity source for the CDE | F-01 | Identity architect | Privileged access test |
| Near term<br>30–90 days | Deploy PAM and redundant hardened jump hosts in a dedicated segment | F-02 | Infrastructure lead | Session recording review |
| Near term<br>30–90 days | Implement internal segmentation firewall with default-deny, named rule owners and review cycle | F-05 | Network security lead | Rule base review; retest |
| Near term<br>30–90 days | Mask or filter card data at source in logs; re-sample | F-03 | Application owners | Clean sample results |
| Near term<br>30–90 days | Segregate backup credentials and paths; encrypt; add immutable copy; test restore | F-04 | Infrastructure lead | Restore test evidence |
| Near term<br>30–90 days | Broker patch, EDR and scanner flows; restrict console access; enforce MFA | F-06 | Security engineering | Tool permission review |
| Near term<br>30–90 days | Document egress and vendor access; enforce allow-lists and time-bound access | F-08 | Vendor management | Egress rule review; TPSP register |
| Strategic<br>90–180 days | Evaluate hosted payment fields or redirect to remove PAN from the web tier | F-09, F-07 | CTO / Product | Architecture decision record |
| Strategic<br>90–180 days | Document token architecture and restrict detokenization | F-07 | Payments architect | Permission review |
| Strategic<br>90–180 days | Automate segmentation testing and firewall compliance checks; institute scope review on change | F-05 | Compliance lead | Scope review log |
| Strategic<br>90–180 days | Commission independent segmentation penetration test | All | CISO | Test report; remediation tracker |

### 10.2 Residual risk register

Even with perfect implementation of the recommendations, the following risks remain and must be explicitly owned and accepted.

| ID | Residual risk | Why it remains | Ongoing treatment | Rating |
| --- | --- | --- | --- | --- |
| RR-01 | Compromise of privileged accounts or the PAM platform | Legitimate admin paths must exist to operate the CDE | MFA, session recording, just-in-time access, alerting | 🟠 **High** |
| RR-02 | Firewall configuration drift between tests | Rules change continuously | Rule ownership, scheduled reviews, automated compliance checks | 🟡 **Medium** |
| RR-03 | Supply-chain or zero-day compromise of shared security tooling | Management tools need privileged reach | Vendor assurance, update integrity, console isolation | 🟡 **Medium** |
| RR-04 | Third-party processor or service-provider compromise | Responsibility is shared and outside Progress Commtech’s control | TPSP due diligence, responsibility matrix, monitoring | 🟡 **Medium** |
| RR-05 | PAN reappearing in logs, exports or dumps | Developer and operator behavior changes over time | Recurring log scans, secure development controls, data discovery | 🟡 **Medium** |
| RR-06 | Data flow changes not triggering scope review | Business change outpaces governance | Change-triggered scope review; annual confirmation | 🟡 **Medium** |
| RR-07 | Segmentation reduces scope but never removes security-impacting systems | Systems that affect CDE security always stay in scope | Accurate inventory; continued hardening of those systems | 🟢 **Low** |

### 10.3 Implementation notes

- Use the architecture as a **proposed design baseline only**. Validate address plans, firewall platforms, availability, throughput, cloud and on-premises dependencies and business continuity before any real deployment.
- Do not claim PCI DSS compliance from this portfolio exercise. It demonstrates an assessment approach; actual compliance requires evidence and formal assessment appropriate to the organization.
- Record every accepted risk with an accountable owner, expiry date and compensating control.

## 11. Assessment questionnaire

Use this questionnaire in interviews and evidence review. Record each response as **Y** (Yes), **N** (No), **P** (Partial) or **N/A** (Not applicable); identify the evidence reviewed; name the owner; and track remediation. **A “Yes” response without evidence is not a validated control.**

> [!NOTE]
> **Indicative mapping to PCI DSS**
>
> Section A relates mainly to Requirements 1.2 (network and data flow diagrams), 3 and 12.5.2 (scope). Section B to Requirement 1 and the segmentation testing requirements in 11.4. Section C to Requirements 7 and 8. Section D to Requirements 1, 5, 6, 11 and 12. Section E to Requirements 3 and 10. Section F to Requirements 3 and 9. Section G to Requirements 6, 12.8, 12.9 and 12.10. Section H to Requirement 12. Section I to Requirements 6.4 and 11.6. Mapping is indicative; confirm against the applicable version.

### A. Scope, payment model and data flows

| # | Assessment question | Response | Evidence / owner / action |
| --- | --- | --- | --- |
| A1 | Which payment channels are used (e-commerce, mobile, call center, recurring billing, card-present, or third-party hosted payment page)? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| A2 | At what exact point is PAN first received, and which components can see it in clear text? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| A3 | Does the company store, process or transmit PAN? Where are data-at-rest and data-in-transit locations documented? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| A4 | Is sensitive authentication data (such as full track data, card verification code, or PIN/PIN block) ever received or retained after authorization? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| A5 | Are payment processor, acquirer, gateway, tokenization provider and other third parties documented with responsibilities and data flows? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| A6 | Are data-flow diagrams and network diagrams current, version-controlled, approved and reconciled to the asset inventory? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| A7 | Which systems are proposed as out of scope, and what technical evidence supports each exclusion? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| A8 | Has the scope been confirmed against the applicable PCI DSS assessment method and payment channel? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| A9 | Does Progress Commtech process card data only for its own sales, or on behalf of merchants or other customers (service provider status)? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| A10 | Is card data ever received outside the main payment flow, such as in support tickets, email, chat, call recordings or manual batch files? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| A11 | Has a data discovery exercise (automated scan or equivalent) been performed to confirm CHD is not present outside documented locations? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |

### B. Segmentation architecture and firewall controls

| # | Assessment question | Response | Evidence / owner / action |
| --- | --- | --- | --- |
| B1 | Are CDE, DMZ, corporate, security-management, backup and shared-service zones separated by enforceable controls? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| B2 | Is inter-zone traffic denied by default, with documented business justification for every permitted rule? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| B3 | Are firewall rules reviewed periodically by rule owner, and are unused, shadowed, overly broad or temporary rules removed? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| B4 | Can a corporate workstation initiate a connection to any CDE host? Have negative connectivity tests been performed? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| B5 | Are there alternate paths into the CDE through VPN, wireless, cloud networks, hypervisors, management interfaces or vendor links? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| B6 | Are routing tables, ACLs, security groups, network virtual appliances and physical firewall rules all included in the review? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| B7 | Are segmentation controls tested at the applicable PCI DSS frequency and after significant changes? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| B8 | Do tests demonstrate both permitted business flows and blocked unauthorized paths from representative networks? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| B9 | Are virtualization, container and overlay networks (such as Kubernetes or SDN) included in the segmentation design, and are they tested? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| B10 | Are out-of-band management interfaces (hypervisor consoles, server BMCs, network device management) isolated from user networks? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| B11 | Are wireless networks either absent from the CDE or separated and tested to the same standard? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |

### C. Identity, authentication and privileged access

| # | Assessment question | Response | Evidence / owner / action |
| --- | --- | --- | --- |
| C1 | Does CDE authentication depend on corporate Active Directory or another shared identity service? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| C2 | If corporate identity is compromised, could an attacker obtain or reset CDE credentials or privileged access? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| C3 | Are unique user IDs, MFA, least privilege and strong authentication enforced for administrative access? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| C4 | Are privileged accounts separate from day-to-day accounts and reviewed on a defined schedule? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| C5 | Are jump hosts hardened, patched, monitored and restricted to approved destinations and protocols? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| C6 | Is PAM used to vault credentials, rotate secrets, approve access and record privileged sessions where appropriate? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| C7 | Are service accounts non-interactive where possible, uniquely owned, scoped, rotated and monitored? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| C8 | Is emergency or break-glass access time-bound, logged, independently reviewed and tested? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| C9 | Is MFA enforced for all access into the CDE (not only administrators), including remote and vendor access? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| C10 | Are joiner, mover and leaver processes and periodic account reviews applied to all CDE accounts, including shared-service administrators? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |

### D. Shared services and security-impacting systems

| # | Assessment question | Response | Evidence / owner / action |
| --- | --- | --- | --- |
| D1 | Which DNS, NTP, directory, certificate, vulnerability scanning, EDR, patching, configuration-management and monitoring services does the CDE depend on? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| D2 | Can administrators of shared services change CDE security settings, credentials, software or network access? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| D3 | Are these services included in scope where they can impact CDE security, or is a defensible exclusion rationale documented? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| D4 | Are management consoles isolated from ordinary user networks and protected with MFA and least privilege? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| D5 | Are software repositories and update packages authenticated and integrity-checked before deployment? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| D6 | Are vulnerability scans credentialed where required, cover all in-scope assets, and have findings tracked to closure? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| D7 | Are endpoint and network security alerts monitored with documented escalation and response SLAs? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| D8 | Are CDE systems synchronized to controlled, trusted time sources, and can those sources be altered by non-CDE administrators? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| D9 | Who operates the certificate authority and key stores used by CDE systems, and are those systems themselves assessed? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |

### E. Logging, monitoring and data protection

| # | Assessment question | Response | Evidence / owner / action |
| --- | --- | --- | --- |
| E1 | Do application, web, database, API, proxy, firewall, HSM and identity logs ever contain full PAN or sensitive authentication data? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| E2 | Are log fields masked or filtered before forwarding, and has this been verified using representative sample events? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| E3 | Are audit logs protected from unauthorized modification and deletion, time-synchronized, retained and reviewed as required? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| E4 | Can SIEM administrators or log-collector hosts alter security controls or affect CDE security? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| E5 | Are alerts defined for failed logins, privilege changes, policy changes, unusual data access and segmentation-control failures? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| E6 | Are cryptographic keys managed separately from encrypted data with documented access, rotation, backup and recovery procedures? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| E7 | Is PAN rendered unreadable when stored, and is retention limited to documented business, legal and regulatory needs? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| E8 | Are data exports, troubleshooting dumps, support bundles and application traces checked for accidental card-data exposure? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| E9 | Are audit log retention periods (including the portion immediately available for analysis) defined and evidenced? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| E10 | Is there file-integrity or change-detection monitoring on critical CDE systems and configuration files? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |

### F. Backup, resilience and recovery

| # | Assessment question | Response | Evidence / owner / action |
| --- | --- | --- | --- |
| F1 | Do database, application, file, snapshot, archive and disaster-recovery copies contain CHD or sensitive authentication data? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| F2 | Which systems and administrators can create, read, delete or restore backups containing CHD? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| F3 | Are backup networks, credentials, repositories and recovery consoles segregated from routine corporate administration? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| F4 | Are backups encrypted where required, with keys and access rights managed appropriately? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| F5 | Is immutability or offline protection implemented for critical backups, and can production administrators bypass it? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| F6 | Are restore tests performed and documented, including verification of access controls after recovery? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| F7 | Are disaster-recovery sites and cloud regions included in the data-flow, inventory and scope assessment? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| F8 | Is physical media, if used, inventoried, secured in transit and storage, and destroyed when no longer needed? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |

### G. Change management, cloud and third parties

| # | Assessment question | Response | Evidence / owner / action |
| --- | --- | --- | --- |
| G1 | Are architecture, firewall, routing, security-group, identity and payment-flow changes risk-assessed and approved before implementation? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| G2 | Are significant changes followed by appropriate scope review and segmentation testing? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| G3 | If cloud services are used, are VPC/VNet boundaries, security groups, private endpoints, IAM roles and management-plane access documented? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| G4 | Are provider responsibilities and shared-responsibility boundaries understood and evidenced? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| G5 | Are vendor remote-access paths disabled by default, time-bound, MFA-protected, monitored and approved? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| G6 | Are third-party service-provider compliance status, responsibility matrices and contractual security obligations reviewed? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| G7 | Are production and non-production environments separated, and is real card data prohibited in test environments unless specifically controlled? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| G8 | Does secure development practice (code review, testing) specifically check that code does not log, cache or expose PAN or SAD? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| G9 | Does the incident response plan cover cardholder-data compromise and segmentation-control failure, and has it been tested? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |

### H. Governance, evidence and QSA readiness

| # | Assessment question | Response | Evidence / owner / action |
| --- | --- | --- | --- |
| H1 | Who owns the CDE, segmentation controls, firewall rules, payment application, card database and residual risks? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| H2 | Is there an approved scope statement identifying included systems, connected systems, security-impacting systems and exclusions? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| H3 | Are risks rated consistently using likelihood, impact, control effectiveness and documented risk acceptance criteria? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| H4 | Does every identified gap have an accountable owner, target date, interim control and closure evidence? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| H5 | Can the team produce firewall exports, rule reviews, diagrams, test results, access reviews, sample logs, scan reports and change records promptly? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| H6 | Are exceptions time-bound, approved by the right risk owner, monitored and periodically reassessed? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| H7 | Has the organization prepared a clear rationale for how segmentation reduces scope without relying only on labels or diagrams? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| H8 | Which assumptions remain unverified, and what test or evidence would close each uncertainty? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| H9 | Is the scope documented and confirmed at least every 12 months, and after significant change (and every six months if Progress Commtech is a service provider)? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| H10 | Is there a named PCI DSS program owner and a reporting line to senior management or the board? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |

### I. E-commerce payment page and application security

This section addresses risks specific to an e-commerce platform and was added to the original questionnaire.

| # | Assessment question | Response | Evidence / owner / action |
| --- | --- | --- | --- |
| I1 | Is the payment page implemented as direct post, hosted fields/iframe, or full redirect to a third party, and is this documented and approved? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| I2 | Is there an inventory of all scripts loaded on payment pages, with written business justification and authorization for each? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| I3 | Is there a mechanism to detect unauthorized changes to payment-page content, scripts and HTTP headers, with alerting and response? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| I4 | Are public-facing web applications protected by a WAF or equivalent, and are application vulnerabilities addressed on a defined schedule? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| I5 | Are Content Security Policy, subresource integrity or equivalent controls used to limit what can execute on payment pages? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| I6 | Are tag managers, analytics and session-replay tools prevented from capturing payment form fields? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| I7 | Are PAN, tokens or session identifiers ever placed in URLs, query strings, referrer headers or client-side storage? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |
| I8 | Is the payment page hosting environment (including CDN configuration) controlled by Progress Commtech, and who can change it? | Y / N / P / N/A | Evidence:<br>Owner:<br>Action: |

## 12. Engagement deliverables and quality criteria

### 12.1 Deliverables checklist

| # | Deliverable | Location in this document | Status |
| --- | --- | --- | --- |
| 1 | Completed data flow diagram (CHD movement) | Section 4.2 | Draft complete |
| 2 | Trust relationship matrix | Sections 5.1 and 5.2 | Draft complete; evidence pending |
| 3 | Segmentation gap analysis (minimum 5 findings) | Section 6 (9 findings) | Draft complete |
| 4 | QSA challenge preparation document | Section 7 | Draft complete |
| 5 | Scope determination document | Sections 4.3 and 8 | Provisional; evidence pending |
| 6 | 5-minute video walkthrough explaining the reasoning | Separate recording | Not started |

### 12.2 Quality criteria

The review demonstrates strong GRC judgment if it meets the following criteria.

| Criterion | Evidence of achievement | Where addressed |
| --- | --- | --- |
| Defended exclusions | Explicitly states what is out of scope and why | Sections 4.3, 8.3 |
| Identified hidden scope | Finds systems others missed (logs, backups, shared services, web tier) | Sections 4.3, 6 |
| Challenged assumptions | Questions “segmented” claims with specific technical evidence | Section 3.2 |
| Anticipated auditor questions | Each finding includes the likely QSA challenge | Sections 6.3, 7.2 |
| Provided actionable recommendations | Specific steps, not just “fix segmentation” | Section 10.1 |
| Documented residual risk | Acknowledges the gaps that remain after improvements | Section 10.2 |

### 12.3 Red flags in a weak review

- A diagram with no analysis.
- Accepting documentation claims at face value.
- No discussion of trust relationships.
- Missing backup and logging paths.
- Generic recommendations without specifics.

> [!NOTE]
> **Guiding principle**
>
> Compliance is not about diagrams. It is about whether your argument would still stand when things go wrong.

## Appendix A: Interview and evidence tracker

Use this tracker to manage evidence requests. Link each item to the finding it supports or closes.

| ID | Evidence item | Owner | Requested | Received | Status / observation | Finding |
| --- | --- | --- | --- | --- | --- | --- |
| E-01 | Current network diagram and routing / VLAN / cloud network exports |  |  |  |  |
| E-02 | CHD data-flow diagram and payment provider integration documentation |  |  |  |  |
| E-03 | Firewall / ACL / security-group rule exports and recent review evidence |  |  |  |  |
| E-04 | Segmentation penetration test results and remediation records |  |  |  |  |
| E-05 | CDE asset inventory, operating systems, software and ownership |  |  |  |  |
| E-06 | Identity architecture, MFA settings, PAM configuration and access reviews |  |  |  |  |
| E-07 | Sample application, database, proxy and SIEM logs reviewed for card data |  |  |  |  |
| E-08 | Backup data inventory, access controls, encryption details and restore-test evidence |  |  |  |  |
| E-09 | Vulnerability scans, patch reports, EDR coverage and exception register |  |  |  |  |
| E-10 | Third-party service-provider list, responsibilities and remote-access records |  |  |  |  |
| E-11 | Approved scope statement and most recent annual scope confirmation |  |  |  |  |
| E-12 | Payment-page script inventory, authorizations and change-detection alerts |  |  |  |  |
| E-13 | Incident response plan and most recent test record |  |  |  |  |
| E-14 | Service provider attestations of compliance and responsibility matrices |  |  |  |  |

## Appendix B: Reviewer reflection questions

To be answered on completion of the project.

| # | Question | Reviewer response |
| --- | --- | --- |
| 1 | What assumption in the original documentation was most dangerous? |  |
| 2 | If you were the QSA, what would be your first question? |  |
| 3 | What is the single change that would most reduce scope? |  |
| 4 | Where did you have to make a judgment call without clear guidance? |  |
| 5 | What residual risk remains even with perfect implementation of your recommendations? |  |
| 6 | Which finding would you be least comfortable defending to a QSA, and why? |  |
| 7 | What evidence would change your scope conclusion for an excluded system? |  |
| 8 | How would your conclusions change if Progress Commtech were assessed as a service provider rather than a merchant? |  |
| 9 | Which management claim would you test first, and what is the cheapest test that would disprove it? |  |

## Appendix C: Glossary

| Term | Meaning |
| --- | --- |
| CDE | Cardholder Data Environment: systems that store, process or transmit cardholder data, plus systems connected to them or that could affect their security |
| CHD | Cardholder data, including primary account number (PAN), cardholder name, expiry date and service code |
| PAN | Primary Account Number: the card number |
| SAD | Sensitive authentication data: full track data, card verification codes and PINs/PIN blocks; must not be stored after authorization |
| QSA | Qualified Security Assessor: an assessor qualified by the PCI Security Standards Council |
| TPSP | Third-party service provider |
| NSC | Network security control, such as a firewall, ACL or cloud security group |
| PAM | Privileged access management |
| HSM | Hardware security module used to protect and use cryptographic keys |
| SIEM | Security information and event management platform |
| EDR | Endpoint detection and response |
| WSUS | Windows Server Update Services, a patch distribution service |
| Connected-to system | A system that has connectivity to the CDE and is therefore in scope even if it does not handle CHD |
| Security-impacting system | A system that could affect the security of the CDE, such as identity, patching or management tooling |
| Conduit | A controlled, documented and enforced network path between two security zones |

## Appendix D: References

- [PCI DSS requirements and supporting documents](https://www.pcisecuritystandards.org/)
- [PCI SSC document library, including guidance on scoping and network segmentation](https://www.pcisecuritystandards.org/document_library)
- [PCI SSC document library, including segmentation and penetration testing guidance](https://www.pcisecuritystandards.org/document_library)

---

*End of document.*
