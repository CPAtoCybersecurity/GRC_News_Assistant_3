# Docker Scout Setup Guide

This guide provides detailed instructions for installing and configuring Docker Scout for vulnerability scanning.

## Overview

Docker Scout scans container images for known vulnerabilities (CVEs) before deployment. The free tier includes up to 3 repositories.

## Prerequisites

- Docker installed and running
- Docker Hub account (free)

## Create a Docker Hub Account

1. Go to https://hub.docker.com/signup
2. Create a free account with your email address
   - **Tip:** Gmail and Outlook tend to have better email delivery rates than work emails or smaller providers
3. Verify your email address

## Installation

### Docker Desktop (macOS/Windows)

Docker Scout is pre-installed with Docker Desktop 4.17+. Just sign in to your Docker account:

```bash
# Verify Scout is available
docker scout version

# Login to Docker Hub (required for Scout)
docker login
```

### Linux (All Distros)

#### Step 1: Install Docker Scout CLI

```bash
# Create the Docker CLI plugins directory (required on fresh installs)
mkdir -p ~/.docker/cli-plugins

# Download the official install script
curl -fsSL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh -o install-scout.sh

# Review the script (recommended)
less install-scout.sh

# Run the installer
sh install-scout.sh
```

#### Step 2: Create a Personal Access Token (PAT)

**Important:** Docker Scout requires a Personal Access Token for authentication, even if `docker login` succeeds.

1. Go to https://hub.docker.com/settings/security
2. Click "New Access Token"
3. Give it a description (e.g., "Docker Scout CLI")
4. Set permissions to **Read-only** (sufficient for scanning)
5. Click "Generate" and copy the token immediately (you won't see it again)

#### Step 3: Authenticate with Docker Hub

```bash
# Login using your PAT
docker login -u <your-dockerhub-username>
# When prompted for password, paste your PAT (not your account password)
```

#### Step 4: Configure Scout Authentication

Even after `docker login` succeeds, Scout may not recognize your credentials. Set these environment variables:

```bash
export DOCKER_SCOUT_HUB_USER=<your-dockerhub-username>
export DOCKER_SCOUT_HUB_PASSWORD=<your-PAT-token>
```

To make this permanent, add to your shell profile:

**Kali Linux / Zsh users:**
```zsh
echo 'export DOCKER_SCOUT_HUB_USER=<your-dockerhub-username>' >> ~/.zshrc
echo 'export DOCKER_SCOUT_HUB_PASSWORD=<your-PAT-token>' >> ~/.zshrc
source ~/.zshrc
```

**Ubuntu / Bash users:**
```bash
echo 'export DOCKER_SCOUT_HUB_USER=<your-dockerhub-username>' >> ~/.bashrc
echo 'export DOCKER_SCOUT_HUB_PASSWORD=<your-PAT-token>' >> ~/.bashrc
source ~/.bashrc
```

**Security Warning:** Never share or commit your PAT token. If you accidentally expose it (e.g., in a screenshot, chat, or git commit), revoke it immediately at https://hub.docker.com/settings/security and create a new one.

**Tip:** Check which shell you're using with `echo $SHELL`. Kali Linux defaults to zsh; most other distros default to bash.

#### Step 5: Verify Installation

```bash
docker scout version
docker scout quickview nginx:latest
```

## Usage

### Basic Scanning Commands

```bash
# Quick vulnerability overview
docker scout quickview n8nio/n8n:latest

# Detailed CVE report
docker scout cves n8nio/n8n:latest

# Filter by severity
docker scout cves n8nio/n8n:latest --only-severity high

# Critical and high only
docker scout cves n8nio/n8n:latest --only-severity critical,high

# Get upgrade recommendations
docker scout recommendations n8nio/n8n:latest
```

### Interpreting Scan Results

#### Severity Levels

| Severity | Action Required |
|----------|-----------------|
| **Critical** | Do not deploy until fixed. Actively exploited or trivially exploitable. |
| **High** | Review before production. May require specific conditions to exploit. |
| **Medium** | Monitor and plan updates. Lower risk but should be tracked. |
| **Low** | Informational. Generally acceptable in most environments. |

#### Key Questions to Ask

1. **Is there a fix available?** Check the "Fixed version" field. No fix = accept risk or find alternative.
2. **Is the vulnerable component used?** A vulnerability in xlsx only matters if you process Excel files.
3. **Is the attack vector relevant?** Network-accessible (AV:N) is higher risk than local-only (AV:L).
4. **What's the CVSS score?** 9.0+ is critical, 7.0-8.9 is high, 4.0-6.9 is medium, below 4.0 is low.

### Re-scan Regularly

Run scans before each deployment and after image updates:

```bash
# Quick check for new vulnerabilities
docker scout quickview n8nio/n8n:latest

# Full CVE report
docker scout cves n8nio/n8n:latest --only-severity critical,high
```

## Troubleshooting

### Installer Fails

If the script fails, install manually:

```bash
# Download Scout (choose ONE based on your architecture)
# Check your architecture with: uname -m
# x86_64 = AMD64 | aarch64 = ARM64

# For AMD64/Intel/x86_64:
curl -fsSL https://github.com/docker/scout-cli/releases/latest/download/docker-scout_linux_amd64.tar.gz -o scout.tar.gz

# For ARM64 (Raspberry Pi, Apple Silicon VMs, etc.):
# curl -fsSL https://github.com/docker/scout-cli/releases/latest/download/docker-scout_linux_arm64.tar.gz -o scout.tar.gz

# Extract and install
tar -xzf scout.tar.gz
mv docker-scout ~/.docker/cli-plugins/
chmod +x ~/.docker/cli-plugins/docker-scout
rm scout.tar.gz

# Verify
docker scout version
```

### "Log in with your Docker ID" Error (After Successful docker login)

This is a known issue where Scout doesn't recognize credentials from `docker login`. 

**Solution:** Set environment variables explicitly:

```bash
export DOCKER_SCOUT_HUB_USER=<your-dockerhub-username>
export DOCKER_SCOUT_HUB_PASSWORD=<your-PAT-token>

# Then retry
docker scout quickview nginx:latest
```

### Docker Hub Verification Emails Not Arriving

- Check spam/junk folders
- Try creating a new account with Gmail or Outlook (better delivery rates)
- Wait 5-10 minutes and request again

### PAT Permission Levels

When creating your Personal Access Token, choose based on your needs:

| Permission | Use Case |
|------------|----------|
| **Public repo read-only** | Only scanning public images |
| **Read-only** | Scanning public + private images (recommended for Scout) |
| **Read-write** | Pushing images to Docker Hub |
| **Read-write-delete** | Full repository management |

For Docker Scout vulnerability scanning, **Read-only** is recommended.

## Alternative: Run Scout as Container

If you prefer not to install the CLI or continue having credential issues:

```bash
docker run -it \
  -e DOCKER_SCOUT_HUB_USER=<your-dockerhub-username> \
  -e DOCKER_SCOUT_HUB_PASSWORD=<your-PAT-token> \
  docker/scout-cli quickview nginx:latest
```

## Alternative Vulnerability Scanners

If Docker Scout continues to be problematic, these alternatives require no Docker Hub authentication:

### Trivy (Aqua Security)

```bash
# Install on Debian/Kali
sudo apt install trivy

# Or via script
curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin

# Scan n8n
trivy image n8nio/n8n:latest
```

### Grype (Anchore)

```bash
# Install
curl -sSfL https://raw.githubusercontent.com/anchore/grype/main/install.sh | sh -s -- -b /usr/local/bin

# Scan n8n
grype n8nio/n8n:latest
```

Both provide CVE reports comparable to Docker Scout without requiring any account setup.
