# Cybersecurity Risk Assessment

### Executive Summary

**Application:** GRC News Assistant 3.0 - n8n Workflow Automation with AI Integration
**Assessment Date:** November 23, 2025
**Assessment Type:**
- [X] Initial
- [ ] Periodic
- [ ] Change-Triggered

**Deployment Model:**
- [X] Self-hosted
- [ ] Cloud
- [ ] Hybrid

**Design:**
- [ ] **Sensitive data access** (databases, APIs, files): Public data - news articles, private Notion Database but no sensitive data inside
- [X] **Untrusted input** (webhooks, emails, public APIs): No webhook, email or chat input, but public RSS feeds and blog/news content that could contain malicious payloads
- [X] **External communication** (HTTP requests, emails): Fetch content from RSS feed URLs, Anthropic API for processing, Notion API for database storage

**Current Residual Risk (Docker on Localhost):** **MODERATE** - Running n8n in Docker provides ~40-50% risk reduction through container isolation. However, supply chain vulnerabilities, compromised dependencies, and potential container escapes still pose risks. The container limits direct file system access but shares the host kernel and network.

**Mitigated Risk (with VM):** **MODERATE** - Moving to a Virtual Machine reduces risk by ~65%, isolating the host file system and limiting persistence.

**Mitigated Risk (with SaaS):** **LOW** - Using n8n Cloud eliminates localhost exposure entirely, providing professional security controls and isolation.

### 1. Requestor Information

|Field|Details|
|---|---|
|Name, Title|Steve, GRC Practitioner|
|Department|Governance, Risk & Compliance|
|Business Sponsor|N/A|

### 2. Business Objective

**Purpose:** Automate the collection, analysis, and prioritization of cybersecurity news and advisories to enhance threat awareness and generate security awareness training content.

**Scope:** Daily automated processing of RSS feeds from trusted cybersecurity sources (Simply Cyber, CISA Advisories, Daniel Miessler's Unsupervised Learning, CISO Series) through AI-powered analysis and storage in Notion database for team consumption.

## 3. Architecture & Deployment Details

### Components

![GRC News Assistant 3.0 Deployment](n8n_Deployment.png)
https://app.excalidraw.com/l/1U6BgkXrdYQ/1NxRV12zBlm

|Component|Type|Location|Description|
|---|---|---|---|
|n8n Server|Workflow Automation|Docker Container (localhost:5678)|Core automation platform running in containerized environment|
|Docker Host|Container Runtime|Local Machine|Docker Desktop/Engine providing container isolation|
|Anthropic API|AI Service|Cloud (External)|Claude AI models for content cleaning and analysis|
|Notion API|Database Service|Cloud (External)|Document database for storing processed articles|
|RSS Feed Sources|Content Sources|External|4 cybersecurity news feeds providing input data|
|Schedule Trigger|Automation Component|n8n Internal|Daily execution at 5:00 AM|

### Data Flow Diagram
![GRC News Assistant 3.0 Workflow](workflow.png)

```
RSS Feeds → Date Filter → Content Extraction → HTTP GET →
→ HTML to Markdown → AI Text Cleaning → AI Labeling/Rating →
→ JSON Processing → Notion Database Storage
```

### Assets

|Asset Name|Description|Data Classification*|
|---|---|---|
|RSS Feed Content|Public cybersecurity news and advisories|Public|
|Anthropic API Key|Authentication credential for Claude AI|Confidential|
|Notion API Token|Authentication credential for database access|Confidential|
|Processed Articles|Analyzed and rated GRC content with metadata|Internal Use|
|AI Prompts|Custom prompts for content analysis and labeling|Public|

_*Data Clasification Scale: Public → Internal Use → Confidential → Sensitive_


## 4. GOVERN (GV) - Governance & Risk Strategy

### 4.1 Organizational Context (GV.OC)

|#|Question|Yes (Lower Risk)|No (Higher Risk)|Not Applicable|Comments|
|---|---|---|---|---|---|
|**GV.OC-03**|Are legal, regulatory, and contractual requirements identified for this AI/automation use?|X|||Copyrighted content|

### 4.2 Policy (GV.PO)

|#|Question|Yes (Lower Risk)|No (Higher Risk)|Not Applicable|Comments|
|---|---|---|---|---|---|
|**GV.PO-01**|Is there a formal Acceptable Use Policy governing n8n workflow creation and AI integration?|X||||
|**GV.PO-01**|Does the policy prevent shadow n8n instances across departments?|X||||

### 4.4 Supply Chain Risk Management (GV.SC)

|#|Question|Yes (Lower Risk)|No (Higher Risk)|Not Applicable|Comments|
|---|---|---|---|---|---|
|**GV.SC-02**|Are all LLM providers (OpenAI, Anthropic, etc.) and integrated tools security-vetted?|X|||Anthropic and Notion are established vendors with security programs|
|**GV.SC-02**|Is there a review process before installing community nodes?|||X|No community nodes|

## 5. IDENTIFY (ID) - Asset Management & Risk Assessment

### 5.1 Asset Management (ID.AM)

| #            | Question                                                                      | Yes (Lower Risk) | No (Higher Risk) | Not Applicable | Comments |
| ------------ | ----------------------------------------------------------------------------- | ---------------- | ---------------- | -------------- | -------- |
| **ID.AM-03** | Is there a data flow diagram showing all n8n connections to internal systems? | X                |                  |                |          |

### 5.2 Risk Assessment (ID.RA)

|#|Question|Yes (Lower Risk)|No (Higher Risk)|Not Applicable|Comments|
|---|---|---|---|---|---|
|**ID.RA-01**|Are vulnerability scans performed on the n8n host regularly?||X||Implement monthly vulnerability scanning|
|**ID.RA-02**|Is there a process for reviewing n8n security advisories?||X||Subscribe to n8n security notifications|
|**ID.RA-05**|Are workflows tested against adversarial inputs before production?||X||No evidence of prompt injection testing|
|**ID.RA-06**|Are business logic decisions requiring human judgment identified and excluded from full automation?|X|||Workflow only processes and labels content, no critical decisions|
|**ID.RA-07**|Are workflow changes assessed for security impact?||X||Implement change control process|

## 6. PROTECT (PR) - Security Controls

### 6.1 Identity Management & Access Control (PR.AA)

| #            | Question                                                                                                            | Yes (Lower Risk) | No (Higher Risk) | Not Applicable | Comments                                                  |
| ------------ | ------------------------------------------------------------------------------------------------------------------- | ---------------- | ---------------- | -------------- | --------------------------------------------------------- |
| **PR.AA-01** | Are administrative credentials stored in a password keeper, secret server or key vault - not hard coded into nodes? | X                |                  |                | Credentials stored in n8n credential manager              |
| **PR.AA-01** | Are n8n API credentials managed in the built-in encrypted credential store?                                         | X                |                  |                | Using encrypted credential store for Anthropic and Notion |
| **PR.AA-01** | Do credentials (API integrations and service accounts) rotate on a defined schedule?                                |                  | X                |                |                                                           |
| **PR.AA-01** | Are webhook endpoints protected with authentication tokens?                                                         |                  |                  | X              | No webhooks in this workflow                              |
| **PR.AA-03** | Is SSO/SAML/OAuth or external authentication (e.g., Entra ID, LDAP) configured for n8n?                             |                  | X                |                | Local authentication only                                 |
| **PR.AA-05** | Is multi-factor authentication required for admin access?                                                           |                  | X                |                |                                                           |
| **PR.AA-06** | Is least privilege and role-based access control implemented for n8n administration and workflow execution?         | X                |                  |                | 1 admin and 1 user                                        |
| **PR.AA-06** | Are repository integrations scoped to specific repos/branches?                                                      |                  |                  | X              | No repository integrations                                |
| **PR.AA-06** | Will AI nodes have read-only access?                                                                                | X                |                  |                | AI only processes content, no write operations            |
| **PR.AA-06** | Do MCP servers require authentication (not anonymous access)?                                                       |                  |                  | X              | No MCP servers in use                                     |
| **PR.AA-06** | Are MCP servers secured with OAuth 2.0 or rotated API keys (not long-lived static credentials)?                     |                  |                  | X              |                                                           |
| **PR.AA-06** | Do MCP servers with OAuth 2.0 or similar token-based authentication have short TTLs?                                |                  |                  | X              |                                                           |

### 6.2 Data Security (PR.DS)

| #            | Question                                                                                    | Yes (Lower Risk) | No (Higher Risk) | Not Applicable | Comments                                                                                                                                                                                    |
| ------------ | ------------------------------------------------------------------------------------------- | ---------------- | ---------------- | -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **PR.DS-01** | Is workflow data encrypted at rest?                                                         | X                |                  |                | FileVault for OS is sufficient for use case.  <br>n8n uses SQLite by default which doesn't have native encryption, but overkill for this use case, as long as n8n credentials are encrypted |
| **PR.DS-02** | Are all AI service connections and integrations secured using HTTPS with TLS 1.2 or higher? | X                |                  |                | All external APIs use HTTPS                                                                                                                                                                 |
| **PR.DS-05** | Are any Data Loss Prevention (DLP) controls in place?                                       |                  | X                |                |                                                                                                                                                                                             |
| **PR.DS-05** | Is there alerting for unusual workflow patterns or data exfiltration?                       |                  | X                |                |                                                                                                                                                                                             |
| **PR.DS-05** | Are prompts sanitized before being sent to LLMs?                                            |                  | X                |                | Risk of prompt injection from RSS content                                                                                                                                                   |
| **PR.DS-05** | Is sensitive data masked/redacted/omitted before LLM processing?                            | X                |                  |                | Only public RSS content processed                                                                                                                                                           |
| **PR.DS-05** | Are there guardrails between user inputs and LLM prompts?                                   |                  | X                |                | RSS content directly inserted into prompts                                                                                                                                                  |
| **PR.DS-05** | Is there protection against data poisoning in shared knowledge bases?                       | X                |                  |                | Read-only RSS sources from trusted providers                                                                                                                                                |
| **PR.DS-11** | Are workflow configurations backed up regularly?                                            |                  | X                |                | Manual, ad hoc backups                                                                                                                                                                      |
| **PR.DS-11** | Are backups encrypted and stored securely?                                                  | X                |                  |                |                                                                                                                                                                                             |

### 6.3 Platform Security (PR.PS)

| #            | Question                                                           | Yes (Lower Risk) | No (Higher Risk) | Not Applicable | Comments                                 |
| ------------ | ------------------------------------------------------------------ | ---------------- | ---------------- | -------------- | ---------------------------------------- |
| **PR.PS-03** | Has n8n been moved from test to a hardened production environment? |                  | X                |                | Currently running on localhost in Docker |
| **PR.PS-02** | Is the n8n application kept up to date with security patches?      |                  | X                |                | Establish patch management process       |
| **PR.PS-01** | Is the host system hardened (e.g to CIS benchmarks)?               |                  | X                |                | Move to a production environment         |
| **PR.PS-05** | Are execution permissions restricted for workflows?                |                  |                  | X              |                                          |
| **PR.PS-06** | Is code review performed for custom n8n nodes?                     |                  | X                |                | Team of 1                                |
| **PR.PS-05** | Is there rate limiting on LLM API calls per workflow?              |                  | X                |                | Risk of excessive API usage              |
| **PR.PS-01** | Can workflows be quickly disabled/rolled back if compromised?      | X                |                  |                |                                          |

### 6.4 Infrastructure Resilience (PR.IR)

| #            | Question                                                                                             | Yes (Lower Risk) | No (Higher Risk) | Not Applicable | Comments                                        |
| ------------ | ---------------------------------------------------------------------------------------------------- | ---------------- | ---------------- | -------------- | ----------------------------------------------- |
| **PR.IR-01** | Is n8n only accessible from the corporate network (not internet-facing, and no external web search)? |                  |                  | X              |                                                 |
| **PR.IR-01** | If internet-accessible, is it protected by VPN or zero-trust access?                                 |                  |                  | X              | Not internet-facing, but pulls public RSS feeds |
| **PR.IR-01** | Are IP whitelisting/access control lists configured?                                                 |                  |                  | X              | Local access only                               |
| **PR.IR-01** | Is the n8n interface behind a Web Application Firewall (WAF)?                                        |                  |                  | X              |                                                 |

## 7. DETECT (DE) - Monitoring & Detection

### 7.1 Continuous Monitoring (DE.CM)

| #            | Question                                                                               | Yes (Lower Risk) | No (Higher Risk) | Not Applicable | Comments            |
| ------------ | -------------------------------------------------------------------------------------- | ---------------- | ---------------- | -------------- | ------------------- |
| **DE.CM-09** | Is an EDR agent installed on the n8n host?                                             | X                |                  |                |                     |
| **DE.CM-01** | Are security events from n8n forwarded to centralized SIEM?                            |                  | X                |                |                     |
| **DE.CM-03** | Is workflow execution activity monitored for anomalies?                                |                  | X                |                |                     |
| **DE.CM-03** | Are failed authentication attempts monitored?                                          |                  | X                |                |                     |
| **DE.CM-09** | Is resource usage (CPU/memory) monitored for crypto-mining?                            |                  | X                |                |                     |
| **DE.CM-03** | Can LLM prompts/responses be logged without violating privacy regulations?             | X                |                  |                | Public content only |
| **DE.CM-01** | Are there alerts for unusual LLM API usage patterns?                                   |                  | X                |                |                     |
| **DE.CM-03** | Is there monitoring for workflows triggering other workflows excessively?              |                  |                  | X              |                     |
| **DE.CM-09** | Are workflow execution errors and failures monitored and analyzed for security issues? |                  | X                |                |                     |

### 7.2 Adverse Event Analysis (DE.AE)

| #            | Question                                                                     | Yes (Lower Risk) | No (Higher Risk) | Not Applicable | Comments               |
| ------------ | ---------------------------------------------------------------------------- | ---------------- | ---------------- | -------------- | ---------------------- |
| **DE.AE-04** | Can malicious workflow patterns be detected?                                 |                  | X                |                | No behavioral analysis |
| **DE.AE-02** | Are there defined thresholds for abnormal workflow behavior?                 |                  | X                |                |                        |
| **DE.AE-02** | Can the system detect prompt injection attempts in logs (if available)?      |                  | X                |                |                        |
| **DE.AE-03** | Are there alerts for workflows accessing systems outside their normal scope? |                  | X                |                |                        |

## 8. RESPOND (RS) - Incident Response

### 8.1 Incident Management (RS.MA)

| #            | Question                                                               | Yes (Lower Risk) | No (Higher Risk) | Not Applicable | Comments                              |
| ------------ | ---------------------------------------------------------------------- | ---------------- | ---------------- | -------------- | ------------------------------------- |
| **RS.MA-01** | Is there an incident response plan for compromised workflows?          |                  | X                |                |                                       |
| **RS.MA-03** | Can suspicious workflows be quickly identified and isolated?           | X                |                  |                | n8n provides workflow disable feature |
| **RS.MA-01** | Can you trace prompt injection attacks through the workflow history?   |                  | X                |                | Limited audit trail                   |
| **RS.MA-02** | Is there a process to identify and notify affected downstream systems? | X                |                  |                | Only Notion database affected         |

### 8.2 Incident Analysis (RS.AN)

| #            | Question                                                                  | Yes (Lower Risk) | No (Higher Risk) | Not Applicable   | Comments |
| ------------ | ------------------------------------------------------------------------- | ---------------- | ---------------- | ----- | -------- |
| **RS.AN-08** | Are webhook URLs and API endpoints validated?                             |X             |             |  |Using trusted RSS feeds only          |
| **RS.AN-03** | Is there capability to analyze workflow execution history for root cause? |X             |             |  |n8n provides execution history          |

## 9. RECOVER (RC) - Recovery & Resilience

### 9.1 Recovery Planning (RC.RP)

| #            | Question                                                                     | Yes (Lower Risk) | No (Higher Risk) | Not Applicable | Comments                              |
| ------------ | ---------------------------------------------------------------------------- | ---------------- | ---------------- | -------------- | ------------------------------------- |
| **RC.RP-01** | Are workflow backups maintained with version control?                        | X                |                  |                | GitHub                                |
| **RC.RP-02** | Can compromised workflows be isolated without affecting critical operations? | X                |                  |                | Non-critical news processing workflow |

---

## 11. Risk Assessment

### Top 5 Threat Scenarios

| Threat # | Threat Scenario                                                                                                                                   | Impact (lower for VM and SaaS due to containment) | Likelihood               | Residual Risk (Docker, Localhost) | Residual Risk (VM) | Residual Risk (SaaS) |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------- | ------------------------ | --------------------------------- | ------------------ | -------------------- |
| T001     | **Supply Chain Attack via Malicious Package** - Compromised npm package or typosquatting attack installs backdoor, potentially escaping container | **High**                                          | **Moderate to Very Low** | **Moderate**                      | **Low**            | **Very Low**         |
| T002     | **Container Escape to Host System** - Exploiting Docker vulnerabilities or misconfigurations to access host filesystem and credentials            | **High to N/A**                                   | **Low**                  | **Low**                           | **N/A**            | **N/A**              |
| T003     | **Prompt Injection Attack** - Malicious RSS content manipulates AI to execute unintended actions or leak API credentials                          | **High to Very Low**                              | **Low**                  | **Low**                           | **Low**            | **Very Low**         |
| T004     | **Remote Code Execution via Dependencies** - Known CVEs in npm packages (vm2, lodash, axios) allow code execution within container                | **High to Low**                                   | **Moderate to Very Low** | **Moderate**                      | **Low**            | **Very Low**         |
### Risk Matrix

|                     |              |          | LEVEL OF IMPACT |          |               |
| ------------------- | ------------ | -------- | --------------- | -------- | ------------- |
| **LIKELIHOOD*** | **Very low** | **Low**  | **Moderate**    | **High** | **Very High** |
| **Very High**       | Very Low     | Low      | Moderate        | High     | Very High     |
| **High**            | Very Low     | Low      | Moderate        | High     | Very High     |
| **Moderate**        | Very Low     | Low      | Moderate        | Moderate | High          |
| **Low**             | Very Low     | Low      | Low             | Low      | Moderate      |
| **Very Low**        | Very Low     | Very Low | Very Low        | Low      | Low           |

*Threat event occurs and results in adverse impact

Risk assessment methodology from: [NIST SP 800-30 Rev. 1: Guide for Conducting Risk Assessments](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-30r1.pdf)
## 12. Action Items

### Option A: Virtual Machine Migration (65% Risk Reduction)

| #   | Action                                         | Due Date | Owner |
| --- | ---------------------------------------------- | -------- | ----- |
| A1  | Deploy Kali Linux or Ubuntu VM in VirtualBox   | Day 2    |       |
| A2  | Configure VM with NAT networking (not bridged) | Day 2    |       |
| A3  | Disable clipboard sharing and shared folders   | Day 2    |       |
| A4  | Create separate API keys for VM environment    | Day 3    |       |
| A5  | Implement VM snapshots before each run         | Day 3    |       |
| A6  | Set up isolated network segment for VM         | Week 1   |       |

### Option B: n8n Cloud Migration (85% Risk Reduction)

| #   | Action                                        | Due Date | Owner |
| --- | --------------------------------------------- | -------- | ----- |
| B1  | Purchase n8n Cloud subscription               | Day 2    |       |
| B2  | Export all workflows from localhost           | Day 2    |       |
| B3  | Configure cloud instance with IP restrictions | Day 3    |       |
| B4  | Enable audit logging and monitoring           | Day 3    |       |
| B5  | Implement webhook authentication              | Day 4    |       |
| B6  | Set up automated backups                      | Week 1   |       |

### Post-Migration Security Hardening

| #   | Action                                     | Due Date | Owner |
| --- | ------------------------------------------ | -------- | ----- |
| 4   | Implement input sanitization for RSS feeds | Week 2   |       |
| 5   | Enable API rate limiting and cost alerts   | Week 2   |       |
| 6   | Deploy monitoring for unusual activity     | Week 3   |       |
| 7   | Create incident response playbook          | Week 3   |       |
| 8   | Establish monthly dependency updates       | Month 1  |       |
| 9   | Document acceptable use policy             | Month 1  |       |

## Risk Assessment Approval

|Role|Name|Signature|Date|
|---|---|---|---|
|Security Assessor||_________________||
|Application Owner||_________________||
|Business Sponsor||_________________||
|CISO/Security Lead||_________________||
