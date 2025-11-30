# n8n Docker Image Security Assessment

**Scan Date:** November 2025  
**Image:** `n8nio/n8n:latest`  
**Digest:** `873a79619a34`  
**Platform:** linux/arm64  
**Scanner:** Docker Scout v1.18.4  
**Base Image:** `n8nio/base:22.21.0`

---

## Executive Summary

| Severity | Count | Status |
|----------|-------|--------|
| **Critical** | 0 | ✅ Clear |
| **High** | 10 | ⚠️ Review required |
| **Medium** | 12 | 📋 Monitor |
| **Low** | 2 | ℹ️ Acceptable |

**Total Packages Scanned:** 1,789  
**Vulnerable Packages:** 14  
**Total Vulnerabilities:** 21

**Overall Assessment:** The n8n image has no critical vulnerabilities but contains 10 high-severity issues requiring review before production deployment. Several high-severity CVEs have no available fix, requiring risk acceptance and compensating controls.

---

## High Severity Vulnerabilities

### No Fix Available ⛔

These vulnerabilities cannot be patched by updating dependencies. Require risk acceptance or compensating controls.

#### xlsx 0.20.2 (Excel Processing Library)

| CVE | CVSS | Type | Description |
|-----|------|------|-------------|
| CVE-2023-30533 | 7.8 | Using Components with Known Vulnerabilities | Prototype pollution via crafted Excel files |
| CVE-2024-22363 | 7.5 | Using Components with Known Vulnerabilities | Denial of service via malformed spreadsheet |

**Risk Context:** Only exploitable if n8n workflows import/process Excel files from untrusted sources.

**Mitigation:**
- Avoid processing Excel files from untrusted sources
- Validate file inputs before processing
- Consider using CSV instead of Excel where possible

#### expr-eval-fork 3.0.0 (Expression Evaluation Library)

| CVE | CVSS | Type | Description |
|-----|------|------|-------------|
| CVE-2025-12735 | 8.6 | Code Injection | Improper control of code generation allows arbitrary code execution |

**Risk Context:** This is concerning for a workflow automation tool. Attackers with workflow edit access could potentially inject malicious code.

**Mitigation:**
- Restrict workflow creation/editing to trusted users only
- Review workflows for suspicious expressions
- Monitor workflow execution logs

---

### Fix Available (Pending Upstream Update) 🟠

These will be resolved when n8n updates their dependencies.

#### libpng 1.6.47 (Image Processing)

| CVE | CVSS | Fixed In | Description |
|-----|------|----------|-------------|
| CVE-2025-65018 | High | 1.6.51-r0 | Memory corruption vulnerability |
| CVE-2025-64720 | High | 1.6.51-r0 | Buffer overflow vulnerability |

**Risk Context:** Only relevant if processing PNG images in workflows.

#### node-forge 1.3.1 (Cryptography Library)

| CVE | CVSS | Fixed In | Description |
|-----|------|----------|-------------|
| CVE-2025-66031 | 8.7 | 1.3.2 | Uncontrolled recursion causing denial of service |
| CVE-2025-12816 | 8.7 | 1.3.2 | Interpretation conflict in certificate validation |

**Risk Context:** Affects TLS/HTTPS connections. Could impact secure communications.

#### glob 10.3.3 / 10.4.5 / 11.0.1 (File Pattern Matching)

| CVE | CVSS | Fixed In | Description |
|-----|------|----------|-------------|
| CVE-2025-64756 | 7.5 | 10.5.0 / 11.1.0 | OS command injection via crafted glob patterns |

**Risk Context:** Requires attacker-controlled input to file path operations. Lower risk in typical workflows.

---

## Medium Severity Vulnerabilities

| Package | Version | CVEs | Type | Fix Available |
|---------|---------|------|------|---------------|
| libpng | 1.6.47-r0 | CVE-2025-64506, CVE-2025-64505 | Memory safety | Yes (1.6.51-r0) |
| graphicsmagick | 1.3.45-r0 | CVE-2025-27796, CVE-2025-27795, CVE-2025-32460 | Image processing | No |
| libde265 | 1.0.15-r1 | CVE-2024-38950, CVE-2024-38949 | HEVC decoding | No |
| libcurl | 8.14.1-r2 | CVE-2025-10966 | HTTP client | No |
| js-yaml | 3.14.1, 4.1.0 | CVE-2025-64718 | Prototype pollution | Yes (4.1.1) |
| body-parser | 2.2.0 | CVE-2025-13466 | Resource consumption | Yes (2.2.1) |

---

## Low Severity Vulnerabilities

| Package | Version | CVEs | Fix Available |
|---------|---------|------|---------------|
| openssh | 10.0_p1-r9 | CVE-2025-61985, CVE-2025-61984 | Yes (10.0_p1-r10) |

---

## Deployment Recommendations

### Development/Testing Environment

✅ **Acceptable to deploy** with monitoring.

- Vulnerabilities require specific conditions to exploit
- Low risk in isolated development environments
- Monitor n8n releases for security updates

### Production Environment

⚠️ **Deploy with compensating controls:**

1. **Network Isolation**
   - Run n8n in an isolated network segment
   - Use a reverse proxy (nginx/Caddy) for TLS termination
   - Restrict outbound network access to required services only

2. **Access Control**
   - Implement authentication (n8n supports basic auth, OAuth)
   - Restrict workflow creation/editing to trusted administrators
   - Use separate n8n instances for different trust levels

3. **Input Validation**
   - Do not process Excel files from untrusted sources
   - Validate all external inputs in workflows
   - Sanitize webhook payloads

4. **Monitoring & Logging**
   - Enable n8n execution logging
   - Monitor for unusual workflow patterns
   - Set up alerts for failed authentication attempts

5. **Version Management**
   - Pin to specific n8n versions instead of `latest`
   - Test updates in staging before production
   - Subscribe to n8n security advisories

---

## Risk Acceptance Statement

For production deployment, the following risks are accepted:

| Risk | Severity | Justification | Owner | Review Date |
|------|----------|---------------|-------|-------------|
| xlsx CVEs (no fix) | High | Excel processing not used / inputs validated | | |
| expr-eval-fork CVE (no fix) | High | Workflow access restricted to trusted users | | |
| Unfixed medium CVEs | Medium | Compensating controls in place | | |

*Fill in Owner and Review Date columns before deployment.*

---

## Re-Scan Schedule

Run vulnerability scans:
- Before each production deployment
- After n8n version updates
- Monthly for long-running deployments

```bash
# Quick scan
docker scout quickview n8nio/n8n:latest

# Full CVE report
docker scout cves n8nio/n8n:latest

# High severity only
docker scout cves n8nio/n8n:latest --only-severity critical,high
```

---

## References

- [n8n GitHub Repository](https://github.com/n8n-io/n8n)
- [n8n Security Advisories](https://github.com/n8n-io/n8n/security)
- [n8n Docker Hub](https://hub.docker.com/r/n8nio/n8n)
- [Docker Scout Documentation](https://docs.docker.com/scout/)
- [NIST National Vulnerability Database](https://nvd.nist.gov/)

---

## Changelog

| Date | Action | Notes |
|------|--------|-------|
| November 2025 | Initial assessment | n8n:latest (digest 873a79619a34) |

---

*This assessment was generated using Docker Scout. Vulnerability data is point-in-time and should be re-verified before deployment.*
