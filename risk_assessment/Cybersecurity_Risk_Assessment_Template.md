# Cybersecurity Risk Assessment

### Executive Summary

**Application:** n8n - Workflow Automation with AI Integration  
**Assessment Date:**   
**Assessment Type:** 
- [ ] Initial
- [ ] Periodic
- [ ] Change-Triggered  

**Deployment Model:**
- [ ] Self-hosted
- [ ] Cloud
- [ ] Hybrid 

**Design ("Lethal Trifecta" check)**:**
- [ ] **Private data access** (e.g. databases, emails, APIs, files)
- [ ] **Untrusted input** (webhooks, emails, Slack, public APIs)
- [ ] **External communication** (HTTP requests, posting to GitHub, Slack, emails)

**Residual Risk:**

### 1. Requestor Information

|Field|Details|
|---|---|
|Name, Title||
|Department||
|Business Sponsor||

### 2. Business Objective

**Purpose:** 

**Scope:**

## 3. Architecture & Deployment Details

### Components

|Component|Type|Location|Description|
|---|---|---|---|
|n8n Server||||
|Database||||
|AI Services||||
|Integrated Systems||||
|||||

### Data Flow Diagram

[Attach diagram here]

### Assets

|Asset Name|Description|Data Classification|
|---|---|---|
| | |Public |
|||Internal Use|
|||Confidential|
|||Sensitive|
---

## 4. GOVERN (GV) - Governance & Risk Strategy

### 4.1 Organizational Context (GV.OC)

|#|Question|Yes (Lower Risk)|No (Higher Risk)|Not Applicable|Comments|
|---|---|---|---|---|---|
|**GV.OC-03**|Are legal, regulatory, and contractual requirements identified for this AI/automation use?|||||

### 4.2 Policy (GV.PO)

|#|Question|Yes (Lower Risk)|No (Higher Risk)|Not Applicable|Comments|
|---|---|---|---|---|---|
|**GV.PO-01**|Is there a formal Acceptable Use Policy governing n8n workflow creation and AI integration?|||||
|**GV.PO-01**|Does the policy prevent shadow n8n instances across departments?|||||

### 4.4 Supply Chain Risk Management (GV.SC)

|#|Question|Yes (Lower Risk)|No (Higher Risk)|Not Applicable|Comments|
|---|---|---|---|---|---|
|**GV.SC-02**|Are all LLM providers (OpenAI, Anthropic, etc.) and integrated tools security-vetted?|||||
|**GV.SC-02**|Is there a review process before installing community nodes?|||||

## 5. IDENTIFY (ID) - Asset Management & Risk Assessment

### 5.1 Asset Management (ID.AM)

|#|Question|Yes (Lower Risk)|No (Higher Risk)|Not Applicable|Comments|
|---|---|---|---|---|---|
|**ID.AM-03**|Is there a data flow diagram showing all n8n connections to internal systems?|||||

### 5.2 Risk Assessment (ID.RA)

|#|Question|Yes (Lower Risk)|No (Higher Risk)|Not Applicable|Comments|
|---|---|---|---|---|---|
|**ID.RA-01**|Are vulnerability scans performed on the n8n host regularly?|||||
|**ID.RA-02**|Is there a process for reviewing n8n security advisories?|||||
|**ID.RA-05**|Are workflows tested against adversarial inputs before production?|||||
|**ID.RA-06**|Are business logic decisions requiring human judgment identified and excluded from full automation?|||||
|**ID.RA-07**|Are workflow changes assessed for security impact?|||||

## 6. PROTECT (PR) - Security Controls

### 6.1 Identity Management & Access Control (PR.AA)

| #            | Question                                                                                                            | Yes (Lower Risk) | No (Higher Risk) |  Not Applicable   | Comments |
| ------------ | ------------------------------------------------------------------------------------------------------------------- | ---------------- | ---------------- | ----- | -------- |
| **PR.AA-01** | Are administrative credentials stored in a password keeper, secret server or key vault - not hard coded into nodes? |             |             |  |          |
| **PR.AA-01** | Are n8n API credentials managed in the built-in encrypted credential store?                                         |             |             |  |          |
| **PR.AA-01** | Do credentials (API integrations and service accounts) rotate on a defined schedule?                                |             |             |  |          |
| **PR.AA-01** | Are webhook endpoints protected with authentication tokens?                                                         |             |             |  |          |
| **PR.AA-03** | Is SSO/SAML/OAuth or external authentication (e.g., Entra ID, LDAP) configured for n8n?                             |             |             |  |          |
| **PR.AA-05** | Is multi-factor authentication required for admin access?                                                           |             |             |  |          |
| **PR.AA-06** | Is least privilege and role-based access control implemented for n8n administration and workflow execution?         |             |             |  |          |
| **PR.AA-06** | Are repository integrations scoped to specific repos/branches?                                                      |             |             |  |          |
| **PR.AA-06** | Will AI nodes have read-only access?                                                                                |             |             |  |          |
| **PR.AA-06** | Do MCP servers require authentication (not anonymous access)?                                                       |             |             |  |          |
| **PR.AA-06** | Are MCP servers secured with OAuth 2.0 or rotated API keys (not long-lived static credentials)?                     |             |             |  |          |
| **PR.AA-06** | Do MCP servers with OAuth 2.0 or similar token-based authentication have short TTLs?                                |             |             |  |          |

### 6.2 Data Security (PR.DS)

|#|Question|Yes (Lower Risk)|No (Higher Risk)|Not Applicable|Comments|
|---|---|---|---|---|---|
|**PR.DS-01**|Is workflow data encrypted at rest?|||||
|**PR.DS-02**|Are all AI service connections and integrations secured using HTTPS with TLS 1.2 or higher?|||||
|**PR.DS-05**|Are any Data Loss Prevention (DLP) controls in place?|||||
|**PR.DS-05**|Is there alerting for unusual workflow patterns or data exfiltration?|||||
|**PR.DS-05**|Are prompts sanitized before being sent to LLMs?|||||
|**PR.DS-05**|Is sensitive data masked/redacted/omitted before LLM processing?|||||
|**PR.DS-05**|Are there guardrails between user inputs and LLM prompts?|||||
|**PR.DS-05**|Is there protection against data poisoning in shared knowledge bases?|||||
|**PR.DS-11**|Are workflow configurations backed up regularly?|||||
|**PR.DS-11**|Are backups encrypted and stored securely?|||||

### 6.3 Platform Security (PR.PS)

|#|Question|Yes (Lower Risk)|No (Higher Risk)|Not Applicable|Comments|
|---|---|---|---|---|---|
|**PR.PS-03**|Has n8n been moved from test to a hardened production environment?|||||
|**PR.PS-02**|Is the n8n application kept up to date with security patches?|||||
|**PR.PS-01**|Is the host system hardened (e.g to CIS benchmarks)?|||||
|**PR.PS-05**|Are execution permissions restricted for workflows?|||||
|**PR.PS-06**|Is code review performed for custom n8n nodes?|||||
|**PR.PS-05**|Is there rate limiting on LLM API calls per workflow?|||||
|**PR.PS-01**|Can workflows be quickly disabled/rolled back if compromised?|||||

### 6.4 Infrastructure Resilience (PR.IR)

|#|Question|Yes (Lower Risk)|No (Higher Risk)|Not Applicable|Comments|
|---|---|---|---|---|---|
|**PR.IR-01**|Is n8n only accessible from the corporate network (not internet-facing, and no external web search)?|||||
|**PR.IR-01**|If internet-accessible, is it protected by VPN or zero-trust access?|||||
|**PR.IR-01**|Are IP whitelisting/access control lists configured?|||||
|**PR.IR-01**|Is the n8n interface behind a Web Application Firewall (WAF)?|||||

## 7. DETECT (DE) - Monitoring & Detection

### 7.1 Continuous Monitoring (DE.CM)

| #            | Question                                                                               | Yes (Lower Risk) | No (Higher Risk) | Not Applicable   | Comments |
| ------------ | -------------------------------------------------------------------------------------- | ---------------- | ---------------- | ----- | -------- |
| **DE.CM-09** | Is an EDR agent installed on the n8n host?                                             |             |             |  |          |
| **DE.CM-01** | Are security events from n8n forwarded to centralized SIEM?                            |             |             |  |          |
| **DE.CM-03** | Is workflow execution activity monitored for anomalies?                                |             |             |  |          |
| **DE.CM-03** | Are failed authentication attempts monitored?                                          |             |             |  |          |
| **DE.CM-09** | Is resource usage (CPU/memory) monitored for crypto-mining?                            |             |             |  |          |
| **DE.CM-03** | Can LLM prompts/responses be logged without violating privacy regulations?             |             |             |  |          |
| **DE.CM-01** | Are there alerts for unusual LLM API usage patterns?                                   |             |             |  |          |
| **DE.CM-03** | Is there monitoring for workflows triggering other workflows excessively?              |             |             |  |          |
| **DE.CM-09** | Are workflow execution errors and failures monitored and analyzed for security issues? |             |             |  |          |

### 7.2 Adverse Event Analysis (DE.AE)

| #            | Question                                                                     | Yes (Lower Risk) | No (Higher Risk) | Not Applicable   | Comments |
| ------------ | ---------------------------------------------------------------------------- | ---------------- | ---------------- | ----- | -------- |
| **DE.AE-04** | Can malicious workflow patterns be detected?                                 |             |             |  |          |
| **DE.AE-02** | Are there defined thresholds for abnormal workflow behavior?                 |             |             |  |          |
| **DE.AE-02** | Can the system detect prompt injection attempts in logs (if available)?      |             |             |  |          |
| **DE.AE-03** | Are there alerts for workflows accessing systems outside their normal scope? |             |             |  |          |

## 8. RESPOND (RS) - Incident Response

### 8.1 Incident Management (RS.MA)

| #            | Question                                                               | Yes (Lower Risk) | No (Higher Risk) | Not Applicable   | Comments |
| ------------ | ---------------------------------------------------------------------- | ---------------- | ---------------- | ----- | -------- |
| **RS.MA-01** | Is there an incident response plan for compromised workflows?          |             |             |  |          |
| **RS.MA-03** | Can suspicious workflows be quickly identified and isolated?           |             |             |  |          |
| **RS.MA-01** | Can you trace prompt injection attacks through the workflow history?   |             |             |  |          |
| **RS.MA-02** | Is there a process to identify and notify affected downstream systems? |             |             |  |          |

### 8.2 Incident Analysis (RS.AN)

| #            | Question                                                                  | Yes (Lower Risk) | No (Higher Risk) | Not Applicable   | Comments |
| ------------ | ------------------------------------------------------------------------- | ---------------- | ---------------- | ----- | -------- |
| **RS.AN-08** | Are webhook URLs and API endpoints validated?                             |             |             |  |          |
| **RS.AN-03** | Is there capability to analyze workflow execution history for root cause? |             |             |  |          |

## 9. RECOVER (RC) - Recovery & Resilience

### 9.1 Recovery Planning (RC.RP)

| #            | Question                                                                     | Yes (Lower Risk) | No (Higher Risk) | Not Applicable   | Comments |
| ------------ | ---------------------------------------------------------------------------- | ---------------- | ---------------- | ----- | -------- |
| **RC.RP-01** | Are workflow backups maintained with version control?                        |             |             |  |          |
| **RC.RP-02** | Can compromised workflows be isolated without affecting critical operations? |             |             |  |          |

---

## 11. Risk Assessment

### Risk Matrix

|                     |              |          | LEVEL OF IMPACT |          |               |
| ------------------- | ------------ | -------- | --------------- | -------- | ------------- |
| **LIKELIHOOD*<br>** | **Very low** | **Low**  | **Moderate**    | **High** | **Very High** |
| **Very High**       | Very Low     | Low      | Moderate        | High     | Very High     |
| **High**            | Very Low     | Low      | Moderate        | High     | Very High     |
| **Moderate**        | Very Low     | Low      | Moderate        | Moderate | High          |
| **Low**             | Very Low     | Low      | Low             | Low      | Moderate      |
| **Very Low**        | Very Low     | Very Low | Very Low        | Low      | Low           |

*Threat event occurs and results in adverse impact

Risk assessment methodology from: [NIST SP 800-30 Rev. 1: Guide for Conducting Risk Assessments](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-30r1.pdf)

### Top 5 Threat Scenarios

| Threat # | Threat Scenario | Key Controls | Impact | Likelihood | Residual Risk |
| -------- | --------------- | ------------ | ------ | ---------- | ------------- |
| T001     |                 |              |        |            |               |
| T002     |                 |              |        |            |               |
| T003     |                 |              |        |            |               |
| T004     |                 |              |        |            |               |
| T005     |                 |              |        |            |               |

---

## 12. Action Items

|#|Action|Due Date|Owner|
|---|---|---|---|
|1||||
|2||||
|3||||

## Risk Assessment Approval

|Role|Name|Signature|Date|
|---|---|---|---|
|Security Assessor||||
|Application Owner||||
|Business Sponsor||||
|CISO/Security Lead||||
