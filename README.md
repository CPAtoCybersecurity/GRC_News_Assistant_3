# GRC News Assistant 3.0

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![n8n Compatible](https://img.shields.io/badge/n8n-Compatible-orange.svg)](https://n8n.io)
[![Notion Integration](https://img.shields.io/badge/Notion-Integrated-black.svg)](https://notion.so)

An intelligent Governance, Risk, and Compliance (GRC) news aggregation system that automatically collects, analyzes, and rates cybersecurity content using AI to help security professionals focus on what matters most.

## Features

- **Multi-Source RSS Aggregation**: Automated collection from curated GRC news sources
- **AI-Powered Analysis**: Content rated from S-Tier to D-Tier using Claude AI
- **Smart Labeling**: Automatic categorization with 30+ GRC-specific labels
- **Notion Database Integration**: Organized storage with rich metadata
- **Quality Scoring**: 1-100 quality assessment based on GRC relevance
- **Business Context Focus**: Prioritizes business-friendly security content
- **Automated Workflow**: Scheduled daily processing at 5 AM

## Rating System

| Tier | Description | Criteria |
|------|-------------|----------|
| **S Tier** | Must Consume Immediately! | 18+ ideas, STRONG theme matching |
| **A Tier** | Should Consume This Week | 15+ ideas, GOOD theme matching |
| **B Tier** | Consume When Time Allows | 12+ ideas, DECENT theme matching |
| **C Tier** | Maybe Skip It | 10+ ideas, SOME theme matching |
| **D Tier** | Definitely Skip It | Few quality ideas, little relevance |

## Architecture

![GRC News Assistant 3.0 Workflow](risk_assessment/workflow.png)

### Data Sources

- **CISA Cybersecurity Advisories** - Official security alerts
- **Simply Cyber Newsletter** - GRC-focused content
- **Unsupervised Learning** - Daniel Miessler's security insights
- **CISO Series** - Executive-level security podcasts

### Content Labels

The system automatically applies relevant labels from:

`Governance`, `Risk`, `Compliance`, `Awareness`, `Training`, `Career`, `Leadership`, `Framework`, `Audit`, `Privacy`, `DataProtection`, `CloudSecurity`, `Incident`, `Breach`, `Regulation`, `Policy`, `Metrics`, `Vendor`, `ThirdParty`, `BusinessContinuity`, `DisasterRecovery`, `Communication`, `Stakeholder`, `Budget`, `ROI`, `Automation`, `AITools`, `Standards`, `ISO`, `NIST`, `SOC`, `GDPR`

## Installation

### Prerequisites

- n8n (self-hosted or cloud)
- Notion account with API access
- Anthropic Claude API key
- Docker (for local installation)

### Quick Start

1. **Clone the Repository**
   ```bash
   git clone https://github.com/yourusername/GRC-News-Assistant-3.0.git
   cd GRC-News-Assistant-3.0
   ```

2. **Set Up n8n**
   ```bash
   # Using Docker Compose
   cd n8n
   docker-compose up -d
   ```
   Access n8n at `http://localhost:5678`

3. **Configure Notion Database**
   - Create a new Notion database
   - Add these properties (exact names and types required):
     - `Title` (Title property)
     - `URL` (URL property)
     - `Labels` (Multi-select property)
     - `Rating` (Select property with options: S Tier, A Tier, B Tier, C Tier, D Tier)
     - `Quality Score` (Number property)
     - `Summary` (Text property)
     - `Published Date` (Date property)
     - `Processed Date` (Date property)
     - `Source` (Select property with options: CISA Cybersecurity Advisories, Simply Cyber Newsletter, Daniel Miessler, CISO Series)
     - `Snippet` (Text property)
     - `Rating Explanation` (Text property)
   - Copy your database ID from the URL: `notion.so/[workspace]/[database-id]?v=[view-id]`
   - Share the database with your Notion integration

4. **Import and Configure Workflow**

   **IMPORTANT: This public workflow file requires you to replace these placeholders:**

   - Open `n8n/workflows/GRC_News_Assistant_3_PUBLIC.json` in a text editor
   - Replace ALL occurrences of:
     - `YOUR_NOTION_DATABASE_ID` → Your actual Notion database ID (appears 2 times)
     - `NOTION_CREDENTIAL_ID` → Will be replaced when you connect credentials in n8n
     - `ANTHROPIC_CREDENTIAL_ID` → Will be replaced when you connect credentials in n8n

   Example:
   ```json
   // Before:
   "value": "YOUR_NOTION_DATABASE_ID",

   // After:
   "value": "2ad7a039-2c8d-803f-9216-edaebebf4419",
   ```

5. **Import to n8n**
   - Open n8n interface
   - Go to Workflows → Import from File
   - Select your modified `GRC_News_Assistant_3_PUBLIC.json`
   - The workflow will import with broken credential connections (this is normal)

6. **Connect Credentials**
   - **Notion Integration**:
     1. Go to https://www.notion.so/my-integrations
     2. Create new integration with capabilities: Read, Write, Insert
     3. Copy the Internal Integration Token
     4. In n8n, click on "Create a database page" node
     5. Click "Create New" for credentials
     6. Paste your Integration Token
     7. Save and test connection

   - **Anthropic API**:
     1. Get API key from https://console.anthropic.com
     2. Click on both "fabric" nodes in the workflow
     3. Create new Anthropic credential
     4. Add your API key
     5. Save and test

7. **Test the Workflow**
   - Click "Execute Workflow" to run manually
   - Check your Notion database for new entries
   - Review execution logs for any errors

## Usage

### Automatic Processing
The workflow runs automatically every day at 5 AM, processing articles from the last day depending on the source.

### Manual Execution
1. Open n8n workflow
2. Click "Execute Workflow" button
3. Monitor progress in execution log

### Viewing Results in Notion
- Articles appear in your Notion database
- Filter by Rating to see highest priority content
- Use Labels to find specific topics
- Sort by Quality Score for relevance

## Configuration

### Adjusting Date Ranges
Edit the "Check Publication Date" nodes to modify how far back to look for articles:
- CISA Advisories: 1 day
- Simply Cyber: 1 day
- Other sources: 1 day

### Customizing AI Prompts
The `fabric:label_and_rate` node contains the AI prompt that:
- Focuses on GRC professional development
- Prioritizes business-friendly content
- Penalizes purely technical content
- Identifies vendor propaganda

### Adding New RSS Sources
1. Add new RSS Feed Read node
2. Create Check Publication Date node
3. Add Edit Fields node to normalize data
4. Connect to Merge node

## Notion Database Schema

| Column | Type | Description |
|--------|------|-------------|
| Title | Title | Article headline |
| URL | URL | Link to original article |
| Labels | Multi-select | GRC categories |
| Rating | Select | S/A/B/C/D Tier |
| Quality Score | Number | 1-100 relevance score |
| Summary | Text | One-sentence summary |
| Published Date | Date | Original publication date |
| Processed Date | Date | When processed by workflow |
| Source | Select | Which RSS feed |
| Snippet | Text | Article excerpt (2000 char max) |
| Rating Explanation | Text | AI's reasoning |

## AI Rating Themes

Content is evaluated against these key themes:
- Security awareness for business leaders
- GRC program development
- Career advancement for GRC professionals
- Translating technical risks to business language
- Compliance simplification
- Security culture building
- Practical GRC implementation
- Demonstrating security value to executives
- Bridging security teams and business objectives

## Security Notes

### What's Been Redacted

- **Notion Database ID**: `YOUR_NOTION_DATABASE_ID`
- **Credential IDs**: 
  - `NOTION_CREDENTIAL_ID`
  - `ANTHROPIC_CREDENTIAL_ID`

### Security Best Practices

1. **Never commit credentials** to version control
2. **Use environment variables** for sensitive data in production
3. **Rotate API keys** regularly
4. **Limit Notion integration permissions** to only required databases
5. **Monitor API usage** for both Notion and Anthropic
6. **Use n8n's built-in credential encryption**

### Optional Customizations

You may also want to customize:
- RSS feed URLs (if you have different sources)
- Schedule trigger time (currently set to 5 AM)
- Date range filters (currently 3-10 days depending on source)
- AI model selection (currently using Claude 3.5 Haiku)

## Troubleshooting

### Common Issues

**No items appearing in Notion**
- Verify Notion integration has database access
- Check n8n execution logs for errors
- Ensure RSS feeds are returning recent content

**Incorrect ratings or labels**
- Review AI API quota/limits
- Check prompt formatting in workflow
- Verify JSON parsing in "Process for Notion" node

**Workflow not triggering**
- Confirm Schedule Trigger is active
- Check n8n instance timezone settings
- Verify workflow is activated (not paused)

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request

### Areas for Contribution
- Additional RSS sources
- Enhanced AI prompts
- Alternative database integrations
- Notification systems
- Analytics dashboards

## License

MIT License - see [LICENSE](./LICENSE) file for details

## Acknowledgments

- Built with [n8n](https://n8n.io) workflow automation
- Powered by [Anthropic Claude](https://anthropic.com) AI
- Inspired by the Simply Cyber Community, Dr. Gerald Auger, Daniel Miessler, Network Chuck, John Hammond
- RSS sources
