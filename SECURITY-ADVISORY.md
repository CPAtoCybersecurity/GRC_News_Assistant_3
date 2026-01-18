# Security Advisory: Critical n8n Vulnerabilities

**Last Updated:** January 14, 2026
**Severity:** CRITICAL
**Action Required:** Update to n8n 2.0.0 or later immediately

---

## Executive Summary

Multiple critical vulnerabilities have been disclosed in n8n workflow automation platform affecting versions prior to 2.0.0. These vulnerabilities allow remote code execution (RCE) and complete server compromise. 

---

## Critical Vulnerabilities

### CVE-2025-68613 - Expression Injection RCE (CVSS 9.9)

| Field | Value |
|-------|-------|
| **CVE ID** | [CVE-2025-68613](https://nvd.nist.gov/vuln/detail/CVE-2025-68613) |
| **CVSS Score** | 9.9 (Critical) |
| **Affected Versions** | 0.211.0 to < 1.120.4, < 1.121.1, < 1.122.0 |
| **Fixed Versions** | 1.120.4, 1.121.1, 1.122.0, 2.0.0+ |
| **Attack Vector** | Network (requires authentication) |

**Description:** A sandbox escape vulnerability in n8n's workflow expression evaluation mechanism allows authenticated users to execute arbitrary OS commands with the privileges of the n8n process.

**Impact:** Full server compromise. An attacker with workflow creation/editing permissions can execute arbitrary code on the host system.

**References:**
- [Orca Security Blog](https://orca.security/resources/blog/cve-2025-68613-n8n-rce-vulnerability/)
- [The Hacker News](https://thehackernews.com/2025/12/critical-n8n-flaw-cvss-99-enables.html)
- [NVD Entry](https://nvd.nist.gov/vuln/detail/CVE-2025-68613)

---

### CVE-2026-21858 "Ni8mare" - Unauthenticated RCE (CVSS 10.0)

| Field | Value |
|-------|-------|
| **CVE ID** | CVE-2026-21858 |
| **CVSS Score** | 10.0 (Critical) |
| **Affected Versions** | All versions <= 1.65.0 |
| **Fixed Versions** | 1.121.0, 2.0.0+ |
| **Attack Vector** | Network (NO authentication required) |

**Description:** A content-type confusion vulnerability allows unauthenticated attackers to read arbitrary files from the server, including n8n configuration files containing API credentials and tokens. When combined with LLM-powered chatbot nodes, attackers can exfiltrate sensitive data.

**Impact:** Complete server compromise without authentication. Credential theft, data exfiltration, and potential lateral movement.

**References:**
- [The Hacker News](https://thehackernews.com/2026/01/critical-n8n-vulnerability-cvss-100.html)
- [CyberScoop](https://cyberscoop.com/n8n-critical-vulnerability-massive-risk/)
- [CSO Online](https://www.csoonline.com/article/4113980/critical-rce-flaw-allows-full-takeover-of-n8n-ai-workflow-platform.html)

---

### CVE-2025-68668 "N8scape" - Python Sandbox Bypass (CVSS 9.9)

| Field | Value |
|-------|-------|
| **CVE ID** | CVE-2025-68668 |
| **CVSS Score** | 9.9 (Critical) |
| **Affected Versions** | 1.0.0 to < 2.0.0 |
| **Fixed Versions** | 2.0.0+ |
| **Attack Vector** | Network (requires authentication) |

**Description:** A sandbox bypass vulnerability in the Python Code Node (using Pyodide) allows authenticated users to escape the sandbox and execute arbitrary operating system commands.

**Impact:** Full server compromise for any authenticated user with workflow permissions.

**References:**
- [The Hacker News](https://thehackernews.com/2026/01/new-n8n-vulnerability-99-cvss-lets.html)

---

### CVE-2026-21877 - Dangerous File Upload (CVSS 10.0)

| Field | Value |
|-------|-------|
| **CVE ID** | CVE-2026-21877 |
| **CVSS Score** | 10.0 (Critical) |
| **Affected Versions** | < 1.121.3 |
| **Fixed Versions** | 1.121.3, 2.0.0+ |
| **Attack Vector** | Network (requires authentication) |

**Description:** An unrestricted file upload vulnerability allows authenticated attackers to upload and execute malicious code.

**Impact:** Full instance compromise.

---

## Affected Instances

According to [Censys](https://www.esecurityplanet.com/threats/103k-n8n-automation-instances-at-risk-from-rce-flaw/), as of December 2025, there were **103,476 potentially vulnerable n8n instances** exposed to the internet, primarily located in:
- United States
- Germany
- France
- Brazil
- Singapore

---

## Required Actions

### Immediate Actions (Do Now)

1. **Update n8n to version 2.0.0 or later**
   ```bash
   # Update your docker-compose.yml or .env
   N8N_VERSION=2.0.0

   # Pull and restart
   docker compose pull
   docker compose down
   docker compose up -d
   ```

2. **Verify your version**
   ```bash
   docker compose exec n8n n8n --version
   ```

3. **Check for exposed instances**
   - Ensure n8n is NOT directly accessible from the internet
   - Use VPN, firewall, or reverse proxy with authentication

### If Immediate Update Is Not Possible

1. **Restrict workflow permissions** to only trusted administrators
2. **Block public access** to n8n entirely
3. **Disable webhooks and forms** temporarily
4. **Monitor logs** for suspicious activity
5. **Plan emergency update** within 24-48 hours

---

## n8n 2.0 Security Improvements

Version 2.0.0 (released December 5, 2025) includes significant security hardening:

| Feature | Description |
|---------|-------------|
| **Task Runners** | Code node executions now run in isolated environments by default |
| **Environment Variable Blocking** | Code nodes can no longer access environment variables |
| **Dangerous Nodes Disabled** | Nodes allowing arbitrary command execution are disabled by default |
| **Save vs Publish** | New workflow publishing model prevents accidental exposure |

**Breaking Changes:** Review the [n8n 2.0 Breaking Changes](https://docs.n8n.io/2-0-breaking-changes/) before upgrading.

---

## GRC News Assistant Specific Guidance

This project's hardened Docker configuration provides defense-in-depth:

| Control | Protection Provided |
|---------|---------------------|
| Non-root execution | Limits post-exploitation capabilities |
| Read-only filesystem | Prevents persistent malware installation |
| Capability dropping | Blocks kernel-level exploits |
| Network isolation | Database cannot reach the internet |
| Resource limits | Prevents cryptomining abuse |

**However, these controls do NOT prevent the RCE vulnerabilities.** You MUST update to n8n 2.0.0+.

---

## Version History

| Date | Version | Security Status |
|------|---------|-----------------|
| Jan 2026 | 2.0.0+ | **Recommended** - All known critical CVEs patched |
| Dec 2025 | 1.121.3 | Patched for CVE-2026-21877 |
| Nov 2025 | 1.121.0 | Patched for CVE-2026-21858 |
| Nov 2025 | 1.120.4 | Patched for CVE-2025-68613 |
| < 1.120.4 | Various | **VULNERABLE** - Do not use |

---

## Additional Resources

- [n8n Release Notes](https://docs.n8n.io/release-notes/)
- [n8n GitHub Releases](https://github.com/n8n-io/n8n/releases)
- [n8n Security Advisories](https://github.com/n8n-io/n8n/security/advisories)
- [n8n 2.0 Blog Post](https://blog.n8n.io/introducing-n8n-2-0/)
- [TechRadar: How to Stay Safe](https://www.techradar.com/pro/security/a-critical-n8n-flaw-has-been-discovered-heres-how-to-stay-safe)

---

## Contact

If you discover a security issue with this project, please open a GitHub issue or contact the maintainer directly.

**Stay secure!**
