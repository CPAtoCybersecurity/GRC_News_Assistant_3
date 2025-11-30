# GRC News Assistant 3.0 - Docker Stack Security Assessment

**Scan Date:** November 2025  
**Scanner:** Docker Scout v1.18.4  
**Platform:** linux/arm64

---

## Executive Summary

| Image | Critical | High | Medium | Low | Status |
|-------|----------|------|--------|-----|--------|
| `n8nio/n8n:latest` | 0 | 10 | 12 | 2 | ⚠️ Review required |
| `postgres:15-alpine` | 0 | 4 | 6 | 2 | ⚠️ Review required |
| ~~`redis:7-alpine`~~ | ~~4~~ | ~~39~~ | ~~29~~ | ~~3~~ | 🛑 ~~Do not use~~ |
| **`redis:alpine`** | **0** | **0** | **0** | **2** | ✅ **Use this** |

**Post-Remediation Total:** 36 vulnerabilities (0 critical, 14 high, 18 medium, 4 low)

### 🛑 MANDATORY ACTION

Replaced `redis:7-alpine` with `redis:alpine` in `docker-compose.yml`. This eliminated **4 critical** and **39 high** severity vulnerabilities.

See [Redis Vulnerabilities](#redis-vulnerabilities-critical-) section for details.

---

## Image Details

### n8nio/n8n:latest

| Property | Value |
|----------|-------|
| Digest | `873a79619a34` |
| Base Image | `n8nio/base:22.21.0` |
| Size | 276 MB |
| Packages | 1,789 |
| Vulnerabilities | 0C / 10H / 12M / 2L |

### postgres:15-alpine

| Property | Value |
|----------|-------|
| Digest | `1ecbd7256ab9` |
| Size | 105 MB |
| Packages | 66 |
| Vulnerabilities | 0C / 4H / 6M / 2L |

### redis:alpine ✅ (Remediated)

| Property | Value |
|----------|-------|
| Digest | `1023a5ac993a` |
| Size | ~18 MB |
| Packages | 29 |
| Vulnerabilities | **0C / 0H / 0M / 2L** |

> ⚠️ **Do not use `redis:7-alpine`** - it contains 4 critical and 39 high severity vulnerabilities.

---

## Overall Assessment

After applying the mandatory Redis remediation:

1. ✅ **Redis is now clean** - Using `redis:alpine` eliminates all critical/high vulnerabilities
2. ⚠️ **n8n has unfixable high-severity CVEs** - Require risk acceptance
3. ⚠️ **Postgres has fixable high CVEs** - Monitor for upstream updates

---

## Redis Vulnerabilities (CRITICAL) 🛑

### ✅ MANDATORY REMEDIATION

The default `redis:7-alpine` image has critical vulnerabilities. **You must use `redis:alpine` instead.**

| Image | Critical | High | Medium | Low | Status |
|-------|----------|------|--------|-----|--------|
| `redis:7-alpine` | 4 | 39 | 29 | 3 | 🛑 Do not use |
| `redis:7.4-alpine3.21` | 4 | 39 | 29 | 3 | 🛑 Do not use |
| **`redis:alpine`** | **0** | **0** | **0** | **2** | ✅ **Use this** |

**Required Change in `docker-compose.yml`:**

```yaml
# ❌ INSECURE - Do not use
image: redis:7-alpine

# ✅ SECURE - Use this instead
image: redis:alpine
```

**Apply the fix:**
```bash
# Edit docker-compose.yml to use redis:alpine, then:
docker compose down
docker compose pull
docker compose up -d
```

---

### Why `redis:7-alpine` Is Vulnerable

The `redis:7-alpine` image uses Go stdlib 1.18.2 (from 2022) in its `gosu` binary, which has **4 critical vulnerabilities**:

| CVE | Type | Fixed In |
|-----|------|----------|
| CVE-2024-24790 | Network exploitation | Go 1.21.11 |
| CVE-2023-24540 | Code execution | Go 1.19.9 |
| CVE-2023-24538 | Code execution | Go 1.19.8 |
| CVE-2025-22871 | Network exploitation | Go 1.23.8 |

### High Severity in `redis:7-alpine` (39 CVEs)

Notable issues that are **eliminated by using `redis:alpine`**:

| CVE | Type | Notes |
|-----|------|-------|
| CVE-2023-44487 | HTTP/2 Rapid Reset | **CISA Known Exploited Vulnerability (KEV)** |
| CVE-2023-39325 | DoS via HTTP/2 | Related to above |
| CVE-2022-41723 | Panic via HTTP/2 | Resource exhaustion |
| CVE-2023-29403 | Privilege escalation | Setuid binary exploitation |

---

## PostgreSQL Vulnerabilities

### Summary

| Severity | Count | All Fixable? |
|----------|-------|--------------|
| Critical | 0 | N/A |
| High | 4 | ✅ Yes |
| Medium | 6 | ✅ Yes (5 of 6) |
| Low | 2 | ✅ Yes |
| Unspecified | 1 | ❌ No |

### High Severity (4 CVEs)

All 4 high-severity CVEs are in Go stdlib 1.24.6 (used by entrypoint scripts):

| CVE | Fixed In |
|-----|----------|
| CVE-2025-61725 | Go 1.24.8 |
| CVE-2025-61723 | Go 1.24.8 |
| CVE-2025-58188 | Go 1.24.8 |
| CVE-2025-58187 | Go 1.24.9 |

### Risk Assessment

**Lower risk because:**
- PostgreSQL is on an isolated internal network
- Cannot make outbound internet connections
- Only n8n can communicate with it
- Vulnerabilities are in entrypoint scripts, not the database engine itself

**Recommendation:** Monitor for updated `postgres:15-alpine` image and update when available.

---

## n8n Vulnerabilities

## n8n Vulnerabilities

### High Severity - No Fix Available ⛔

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
| ~~Redis critical CVEs~~ | ~~Critical~~ | ✅ **REMEDIATED** - Use `redis:alpine` | | |
| xlsx CVEs (no fix) | High | Excel processing not used / inputs validated | | |
| expr-eval-fork CVE (no fix) | High | Workflow access restricted to trusted users | | |
| Postgres high CVEs | High | Network isolated, fixes expected upstream | | |
| n8n high CVEs (fixable) | High | Fixes pending upstream, monitor for updates | | |
| Unfixed medium CVEs | Medium | Compensating controls in place | | |

*Fill in Owner and Review Date columns before deployment.*

---

## Re-Scan Schedule

Run vulnerability scans:
- Before each production deployment
- After any image version updates
- Monthly for long-running deployments

```bash
# Quick scan all images
docker scout quickview n8nio/n8n:latest
docker scout quickview postgres:15-alpine
docker scout quickview redis:alpine

# Full CVE reports (critical and high only)
docker scout cves n8nio/n8n:latest --only-severity critical,high
docker scout cves postgres:15-alpine --only-severity critical,high
docker scout cves redis:alpine --only-severity critical,high
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
| November 2025 | Initial assessment | Full stack: n8n, postgres, redis |

---

*This assessment was generated using Docker Scout. Vulnerability data is point-in-time and should be re-verified before deployment.*
