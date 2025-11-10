# Claude Code MCP Servers Guide

Comprehensive guide to the most useful Model Context Protocol (MCP) servers for Claude Code in 2025.

---

## Table of Contents

1. [What is MCP?](#what-is-mcp)
2. [Setup & Configuration](#setup--configuration)
3. [Essential MCP Servers](#essential-mcp-servers)
4. [Development Tools](#development-tools)
5. [Database & Data](#database--data)
6. [Testing & Automation](#testing--automation)
7. [Cloud & Infrastructure](#cloud--infrastructure)
8. [Business & Productivity](#business--productivity)
9. [AI & Content](#ai--content)
10. [Installation Examples](#installation-examples)
11. [Best Practices](#best-practices)
12. [Resources](#resources)

---

## What is MCP?

**Model Context Protocol (MCP)** is an open-source standard that enables Claude Code to connect to external tools, databases, APIs, and services. Since its launch in November 2024, MCP has become the de facto standard for AI-tool integrations with thousands of community-built servers.

### Key Benefits

- **Universal Protocol**: Implement once, unlock entire ecosystem of integrations
- **Extensible**: Connect to any service that provides an MCP server
- **Standardized**: Open protocol adopted by OpenAI (ChatGPT), Google (Gemini), and others
- **Secure**: Controlled access with authentication and permission management

### How MCP Works

```
┌─────────────┐         ┌─────────────┐         ┌──────────────┐
│ Claude Code │ ←─MCP──→ │ MCP Server  │ ←────→ │ External     │
│             │         │             │         │ Service/Tool │
└─────────────┘         └─────────────┘         └──────────────┘
```

MCP servers expose tools, resources, and prompts that Claude Code can use to:
- Query databases
- Manage repositories
- Control browsers
- Access APIs
- Analyze code
- And much more

---

## Setup & Configuration

### Installation Methods

Claude Code supports three transport types:

#### 1. HTTP Servers (Recommended)
Best for remote, cloud-based services:
```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

#### 2. Stdio Servers (Local)
Run as local processes on your machine:
```bash
claude mcp add --transport stdio <name> -- <command>
```

#### 3. SSE Servers (Deprecated)
Server-Sent Events transport (being phased out).

### Configuration Scopes

| Scope | Purpose | Storage | Command Flag |
|-------|---------|---------|--------------|
| **User** | Cross-project access | User account settings | `--scope user` |
| **Project** | Team collaboration | `.mcp.json` in repo | `--scope project` |
| **Local** | Personal, project-specific | User settings | (default) |

### Management Commands

```bash
claude mcp list                  # Show all configured servers
claude mcp get <name>            # View specific server details
claude mcp add <name> -- <cmd>   # Add new server
claude mcp remove <name>         # Delete server configuration
/mcp                            # Check status and authenticate (in Claude Code)
```

### Environment Variables

Set credentials securely during installation:
```bash
claude mcp add --transport stdio airtable \
  --env AIRTABLE_API_KEY=YOUR_KEY \
  -- npx -y airtable-mcp-server
```

For `.mcp.json` files, use expansion syntax:
```json
{
  "env": {
    "API_KEY": "${MY_API_KEY:-default_value}"
  }
}
```

---

## Essential MCP Servers

These are the must-have servers for any Claude Code developer.

### 1. Sequential Thinking ⭐

**Purpose**: Structured problem-solving through methodical reasoning

**Why Essential**: Enables Claude to break complex tasks into logical steps, maintain context across extended reasoning chains, and revise approaches when needed.

**Installation**:
```bash
claude mcp add sequential-thinking npx -- -y @modelcontextprotocol/server-sequential-thinking
```

**Use Cases**:
- Architectural planning and system design
- Large-scale refactoring projects
- Complex debugging requiring multi-step analysis
- Root cause analysis
- Performance optimization strategies

**Example**:
```
You: "Design a microservices architecture for our e-commerce platform"
Claude: [Uses Sequential Thinking to methodically analyze requirements,
         propose services, evaluate trade-offs, and iterate on design]
```

### 2. GitHub ⭐

**Purpose**: Complete GitHub repository and workflow management

**Why Essential**: Transforms Claude into a full development partner for code reviews, PR management, issue tracking, and CI/CD monitoring.

**Installation**:
```bash
npx @composio/mcp@latest setup github --client claude
```

**Use Cases**:
- Review pull request diffs
- Create and manage issues
- Analyze commit history
- Debug regression bugs by finding problematic commits
- Monitor CI/CD workflow status
- Automate release management

**Example**:
```
You: "Find the commit that introduced the authentication bug"
Claude: [Uses GitHub MCP to search commits, analyze diffs, and identify the exact changeset]
```

### 3. Filesystem ⭐

**Purpose**: Secure file operations with configurable access controls

**Why Essential**: Enables automated file management, documentation updates, and project organization while maintaining security boundaries.

**Installation**:
```bash
# Already included in Claude Code, just configure allowed directories
claude mcp add filesystem npx -- @modelcontextprotocol/server-filesystem /path/to/allowed/directory
```

**Use Cases**:
- Read, write, and edit project files
- Organize directory structures
- Update documentation automatically
- Batch file operations
- Project cleanup and maintenance

**Example**:
```
You: "Update all README files to include the new API endpoint"
Claude: [Uses Filesystem MCP to locate and update all README.md files]
```

### 4. Memory ⭐

**Purpose**: Knowledge graph-based persistent memory across sessions

**Why Essential**: Maintains project context and decisions across coding sessions, crucial for long-term projects.

**Installation**:
```bash
npm install -g @modelcontextprotocol/server-memory
claude mcp add memory npx -- @modelcontextprotocol/server-memory
```

**Use Cases**:
- Remember architectural decisions
- Track project conventions and patterns
- Maintain context for multi-day features
- Store team preferences and standards
- Recall previous debugging insights

**Example**:
```
You: "What database pattern did we decide to use for user sessions?"
Claude: [Uses Memory MCP to recall previous architectural decision]
```

### 5. Fetch ⭐

**Purpose**: Web content retrieval optimized for LLM usage

**Why Essential**: Enables Claude to fetch documentation, API specs, and web resources in real-time.

**Installation**:
```bash
npm install -g @modelcontextprotocol/server-fetch
claude mcp add fetch npx -- @modelcontextprotocol/server-fetch
```

**Use Cases**:
- Fetch latest API documentation
- Retrieve library documentation
- Access web-based resources
- Download and process remote content

**Example**:
```
You: "Get the latest React 19 documentation for Server Components"
Claude: [Uses Fetch MCP to retrieve current documentation from React website]
```

---

## Development Tools

### Git

**Purpose**: Read, search, and manipulate Git repositories

**Installation**:
```bash
npm install -g @modelcontextprotocol/server-git
claude mcp add git npx -- @modelcontextprotocol/server-git
```

**Use Cases**:
- Analyze commit history
- Search through git logs
- Compare branches
- Manage git workflows
- Generate changelogs

### Context7

**Purpose**: Real-time API specification and documentation fetching

**Installation**:
```bash
# Typically requires API key from Context7
claude mcp add context7 npx -- context7-mcp-server
```

**Use Cases**:
- Access up-to-date framework documentation (React, Next.js, Vue)
- Fetch library API specs
- Avoid documentation drift
- Get accurate method signatures

**Example**:
```
You: "How do I use the new Next.js 15 parallel routes?"
Claude: [Uses Context7 to fetch latest Next.js 15 documentation]
```

### GitLab

**Purpose**: GitLab repository and CI/CD integration

**Installation**:
```bash
claude mcp add gitlab npx -- -y gitlab-mcp-server
# Set GITLAB_TOKEN environment variable
```

**Use Cases**:
- Manage merge requests
- Trigger CI/CD pipelines
- Review pipeline failures
- Manage issues and milestones

### Docker

**Purpose**: Secure code execution in isolated containers

**Installation**:
```bash
claude mcp add docker npx -- -y docker-mcp-server
```

**Use Cases**:
- Run code in isolated environments
- Test across different OS/environments
- Secure sandbox execution
- Container management

### E2B

**Purpose**: Secure cloud-based code sandbox execution

**Installation**:
```bash
npm install -g @e2b/mcp-server
claude mcp add e2b npx -- @e2b/mcp-server
```

**Use Cases**:
- Execute untrusted code safely
- Run tests in isolated environments
- Cloud-based code execution
- Multi-language support

---

## Database & Data

### PostgreSQL ⭐

**Purpose**: Natural language database queries for PostgreSQL

**Installation**:
```bash
# Clone repository
git clone https://github.com/modelcontextprotocol/servers.git
cd servers/src/postgres
npm install

# Add to Claude Code
claude mcp add postgres npx -- postgres-mcp-server \
  --env POSTGRES_URL=postgresql://user:pass@localhost:5432/dbname
```

**Use Cases**:
- Query databases with natural language
- Generate SQL from English descriptions
- Analyze database schemas
- Retrieve and manipulate records
- Database migrations assistance

**Example**:
```
You: "Show me all users who signed up in the last 7 days"
Claude: [Generates and executes SQL query, returns results]
```

### GreptimeDB

**Purpose**: Real-time metrics, logging, and time-series analytics

**Installation**:
```bash
claude mcp add greptimedb npx -- -y greptimedb-mcp-server
```

**Use Cases**:
- Analyze application metrics
- Query time-series data
- Performance monitoring
- Log analysis

### Supabase

**Purpose**: PostgreSQL database and authentication access

**Installation**:
```bash
npx @composio/mcp@latest setup supabase --client claude
```

**Use Cases**:
- Full-stack database operations
- User authentication management
- Real-time subscriptions
- Storage management

### MSSQL

**Purpose**: Microsoft SQL Server natural language interface

**Installation**:
```bash
claude mcp add mssql npx -- -y mssql-mcp-server
```

**Use Cases**:
- Query SQL Server databases
- Schema management
- Data manipulation
- Enterprise database integration

### ClickHouse

**Purpose**: Time-series and analytics database queries

**Installation**:
```bash
claude mcp add clickhouse npx -- -y clickhouse-mcp-server
```

**Use Cases**:
- Analytics queries
- High-performance data retrieval
- Time-series analysis
- Big data processing

### Neo4j

**Purpose**: Graph database interaction

**Installation**:
```bash
claude mcp add neo4j npx -- -y neo4j-mcp-server
```

**Use Cases**:
- Relationship analysis
- Graph traversal
- Network analysis
- Knowledge graphs

### Chroma ⭐

**Purpose**: Semantic document management and vector search

**Installation**:
```bash
claude mcp add chroma npx -- -y chroma-mcp-server
```

**Use Cases**:
- Semantic search across documents
- Vector database operations
- AI-powered retrieval
- Document similarity analysis

### Milvus

**Purpose**: Vector search and semantic memory

**Installation**:
```bash
claude mcp add milvus npx -- -y milvus-mcp-server
```

**Use Cases**:
- High-performance vector search
- Semantic code search
- AI embeddings storage
- Similarity search

---

## Testing & Automation

### Playwright ⭐

**Purpose**: Browser automation and E2E testing

**Installation**:
```bash
claude mcp add playwright npx -- -y playwright-mcp-server
```

**Use Cases**:
- Generate E2E tests automatically
- Test UI workflows
- Browser automation
- Accessibility testing
- Visual regression testing
- Cross-browser compatibility

**Example**:
```
You: "Create a test for the login flow"
Claude: [Uses Playwright to explore the page, identify selectors,
         and generate complete TypeScript test]
```

### Puppeteer

**Purpose**: Web browser control and automation

**Installation**:
```bash
npm install puppeteer
claude mcp add puppeteer npx -- -y puppeteer-mcp-server
```

**Use Cases**:
- Automated testing
- Web scraping
- Screenshot generation
- Form automation
- Performance testing

**Example**:
```
You: "Test clicking the login button and verify the redirect"
Claude: [Uses Puppeteer to automate browser and verify behavior]
```

### Sentry ⭐

**Purpose**: Error tracking and production issue analysis

**Installation**:
```bash
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
```

**Use Cases**:
- Analyze error patterns
- Correlate code changes with production issues
- Debug production failures
- Monitor application health
- Track error trends

**Example**:
```
You: "What's causing the spike in authentication errors?"
Claude: [Uses Sentry MCP to analyze error patterns and identify root cause]
```

### PostHog

**Purpose**: Product analytics and feature usage tracking

**Installation**:
```bash
claude mcp add posthog npx -- -y posthog-mcp-server
```

**Use Cases**:
- Analyze feature usage
- A/B test results
- User behavior tracking
- Data-driven development decisions

---

## Cloud & Infrastructure

### AWS

**Purpose**: AWS Lambda, DynamoDB, and infrastructure management

**Installation**:
```bash
# Requires Python 3.10+ and AWS credentials
pip install aws-mcp-server
claude mcp add aws python -- -m aws_mcp_server
```

**Use Cases**:
- Lambda function management
- DynamoDB operations
- Infrastructure as code
- CloudFormation templates
- S3 bucket operations

### Cloudflare ⭐

**Purpose**: Edge computing, Workers, and R2 storage

**Installation**:
```bash
claude mcp add cloudflare npx -- -y cloudflare-mcp-server
```

**Use Cases**:
- Deploy Workers
- Manage R2 storage
- Configure edge rules
- DNS management
- Analytics access

**Features**: 16 specialized servers covering the complete Cloudflare ecosystem

### Google Cloud Platform

**Purpose**: BigQuery, Dataflow, and GCP services

**Installation**:
```bash
claude mcp add gcp npx -- -y gcp-mcp-server
```

**Use Cases**:
- BigQuery data analysis
- Dataflow pipeline management
- Cloud Functions deployment
- GCP resource management

**Note**: Community-maintained, not official Google support

### Azure DevOps

**Purpose**: Azure Pipelines and project integration

**Installation**:
```bash
claude mcp add azure-devops npx -- -y azure-devops-mcp-server
```

**Use Cases**:
- Pipeline management
- Work item tracking
- Release management
- Build automation

### Netlify

**Purpose**: JAMstack deployment and hosting

**Installation**:
```bash
claude mcp add netlify npx -- -y netlify-mcp-server
```

**Use Cases**:
- Deploy static sites
- Manage serverless functions
- Configure build settings
- Domain management

### Vercel

**Purpose**: Next.js and frontend deployment

**Installation**:
```bash
claude mcp add vercel npx -- -y vercel-mcp-server
```

**Use Cases**:
- Deploy Next.js applications
- Manage preview deployments
- Configure edge functions
- Analytics access

---

## Business & Productivity

### Notion

**Purpose**: Workspace content and project management

**Installation**:
```bash
npx @composio/mcp@latest setup notion --client claude
```

**Use Cases**:
- Update documentation
- Manage project tasks
- Search knowledge base
- Create meeting notes
- Track project status

### Linear

**Purpose**: Issue tracking and project management

**Installation**:
```bash
claude mcp add linear npx -- -y linear-mcp-server
```

**Use Cases**:
- Create and update issues
- Track sprint progress
- Manage project roadmaps
- Team collaboration

### Jira (Atlassian)

**Purpose**: Agile project management

**Installation**:
```bash
claude mcp add jira npx -- -y jira-mcp-server
```

**Use Cases**:
- Issue management
- Sprint planning
- Backlog grooming
- Workflow automation

### Slack

**Purpose**: Team communication integration

**Installation**:
```bash
claude mcp add slack npx -- -y slack-mcp-server
```

**Use Cases**:
- Send notifications
- Monitor channels
- Respond to messages
- Team updates

### Asana

**Purpose**: Task and workflow management

**Installation**:
```bash
claude mcp add asana npx -- -y asana-mcp-server
```

**Use Cases**:
- Task creation and updates
- Project tracking
- Team coordination
- Deadline management

### Figma

**Purpose**: Design to code conversion

**Installation**:
```bash
npx @composio/mcp@latest setup figma --client claude
```

**Use Cases**:
- Convert designs to React components
- Extract design tokens
- Generate component code
- Design system integration

**Example**:
```
You: "Generate a React component from the login screen design"
Claude: [Uses Figma MCP to access design and generate component code]
```

---

## AI & Content

### ElevenLabs

**Purpose**: Text-to-speech synthesis

**Installation**:
```bash
claude mcp add elevenlabs npx -- -y elevenlabs-mcp-server
```

**Use Cases**:
- Generate voice content
- Audio documentation
- Accessibility features
- Voice UI testing

### OpenAI

**Purpose**: Access GPT models and embeddings

**Installation**:
```bash
claude mcp add openai npx -- -y openai-mcp-server
```

**Use Cases**:
- Multi-model workflows
- Embeddings generation
- Specialized AI tasks
- Model comparison

### Mux

**Purpose**: Video management and streaming

**Installation**:
```bash
claude mcp add mux npx -- -y mux-mcp-server
```

**Use Cases**:
- Video upload and processing
- Streaming configuration
- Video analytics
- Thumbnail generation

### Firecrawl

**Purpose**: Web scraping and content extraction

**Installation**:
```bash
claude mcp add firecrawl npx -- -y firecrawl-mcp-server
```

**Use Cases**:
- Extract web content
- Convert HTML to markdown
- Data collection
- Content migration

### Exa

**Purpose**: AI-optimized search engine

**Installation**:
```bash
claude mcp add exa npx -- -y exa-mcp-server
```

**Use Cases**:
- Research automation
- Content discovery
- Information gathering
- Competitive analysis

### Tavily

**Purpose**: Search with content extraction

**Installation**:
```bash
claude mcp add tavily npx -- -y tavily-mcp-server
```

**Use Cases**:
- Research tasks
- Content aggregation
- Market research
- Competitive intelligence

---

## Advanced Specialized Servers

### Apidog ⭐

**Purpose**: API specification to code generation

**Installation**:
```bash
claude mcp add apidog npx -- -y apidog-mcp-server \
  --oas=<oas-url-or-path>
```

**Use Cases**:
- Generate code from OpenAPI specs
- API contract validation
- SDK generation
- Eliminate specification drift

**Example**:
```
You: "Generate TypeScript client for the REST API"
Claude: [Uses Apidog to read OpenAPI spec and generate type-safe client]
```

### Stripe

**Purpose**: Payment processing automation

**Installation**:
```bash
claude mcp add --scope project --transport http stripe https://mcp.stripe.com
```

**Use Cases**:
- Payment integration
- Subscription management
- Invoice generation
- Webhook handling

### PayPal

**Purpose**: Payment gateway integration

**Installation**:
```bash
claude mcp add paypal npx -- -y paypal-mcp-server
```

**Use Cases**:
- Payment processing
- Order management
- Refund handling
- Transaction tracking

### Mailgun

**Purpose**: Email delivery infrastructure

**Installation**:
```bash
claude mcp add mailgun npx -- -y mailgun-mcp-server
```

**Use Cases**:
- Transactional emails
- Email validation
- Delivery analytics
- Template management

### Twilio

**Purpose**: SMS and voice capabilities

**Installation**:
```bash
claude mcp add twilio npx -- -y twilio-mcp-server
```

**Use Cases**:
- SMS notifications
- Voice calls
- Two-factor authentication
- Communication workflows

### Zapier

**Purpose**: Cross-platform workflow automation

**Installation**:
```bash
npx @composio/mcp@latest setup zapier --client claude
```

**Use Cases**:
- Multi-app integrations
- Workflow automation
- Event-driven actions
- Process orchestration

**Example**:
```
You: "Send Slack notification when CI/CD pipeline fails"
Claude: [Uses Zapier to create integration between CI/CD and Slack]
```

### Digma ⭐

**Purpose**: Runtime observability and performance insights

**Installation**:
```bash
claude mcp add digma npx -- -y digma-mcp-server
```

**Use Cases**:
- Code review with performance data
- Identify runtime bottlenecks
- Test flakiness detection
- Refactoring guidance based on real usage

### CoinGecko

**Purpose**: Cryptocurrency market data

**Installation**:
```bash
claude mcp add coingecko npx -- -y coingecko-mcp-server
```

**Use Cases**:
- Crypto price tracking
- Market analysis
- Portfolio management
- Trend analysis

### Claude Context

**Purpose**: Semantic code search for entire codebases

**Installation**:
```bash
# From zilliztech/claude-context
npm install -g @zilliztech/claude-context
claude mcp add claude-context npx -- @zilliztech/claude-context
```

**Use Cases**:
- Deep codebase understanding
- Semantic code search
- Cross-file relationship tracking
- Architecture comprehension

---

## Installation Examples

### Quick Start with Essential Servers

```bash
# Install Sequential Thinking
claude mcp add sequential-thinking npx -- -y @modelcontextprotocol/server-sequential-thinking

# Install GitHub
npx @composio/mcp@latest setup github --client claude

# Install Memory
npm install -g @modelcontextprotocol/server-memory
claude mcp add memory npx -- @modelcontextprotocol/server-memory

# Install Playwright
claude mcp add playwright npx -- -y playwright-mcp-server

# Install PostgreSQL (configure connection string)
claude mcp add postgres npx -- postgres-mcp-server \
  --env POSTGRES_URL=postgresql://user:pass@localhost:5432/dbname
```

### Project-Specific Configuration

Create `.mcp.json` in your repository:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@composio/mcp", "github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "postgres-mcp-server"],
      "env": {
        "POSTGRES_URL": "${DATABASE_URL}"
      }
    },
    "sentry": {
      "transport": "http",
      "url": "https://mcp.sentry.dev/mcp",
      "env": {
        "SENTRY_TOKEN": "${SENTRY_AUTH_TOKEN}"
      }
    }
  }
}
```

### Environment Variables Setup

Create `.env` file:
```bash
GITHUB_TOKEN=ghp_xxxxxxxxxxxxx
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb
SENTRY_AUTH_TOKEN=sntrys_xxxxxxxxxxxxx
ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxx
```

Add to `.gitignore`:
```
.env
.mcp.json.local
```

---

## Best Practices

### Security

✅ **DO**:
- Use environment variables for sensitive credentials
- Never commit API keys to version control
- Use `.gitignore` for `.env` and `.mcp.json.local`
- Audit third-party MCP servers before installation
- Use project scope for team configurations
- Implement least-privilege access for API tokens
- Rotate credentials regularly

❌ **DON'T**:
- Hardcode credentials in `.mcp.json`
- Share API keys in chat or documentation
- Install unverified third-party servers
- Use admin tokens when read-only suffices
- Skip security reviews for new servers

### Performance

✅ **DO**:
- Cache MCP server installations
- Use HTTP transport for remote services
- Monitor token consumption via `MAX_MCP_OUTPUT_TOKENS`
- Batch operations when possible
- Use local servers for frequent operations
- Configure appropriate timeouts

❌ **DON'T**:
- Install unnecessary servers
- Use stdio transport for remote services
- Ignore large output warnings
- Chain too many MCP operations
- Skip performance monitoring

### Organization

✅ **DO**:
- Document MCP servers in project README
- Use consistent naming conventions
- Group related servers by category
- Maintain `.mcp.json` in version control (without secrets)
- Share configurations with team
- Test servers after installation

❌ **DON'T**:
- Mix personal and project servers randomly
- Skip documentation of server purposes
- Use inconsistent server names
- Forget to update team on new servers

### Usage Patterns

✅ **DO**:
- Start with essential servers
- Add servers as needed
- Use resource references: `@servername:protocol://path`
- Leverage MCP prompts: `/mcp__servername__promptname`
- Check server status with `/mcp` command
- Authenticate OAuth servers properly

❌ **DON'T**:
- Install all servers at once
- Ignore authentication requirements
- Skip testing after configuration
- Forget to check server compatibility

### Troubleshooting

Common issues and solutions:

#### Server Not Found
```bash
# Check server list
claude mcp list

# Verify server name
claude mcp get <server-name>

# Reinstall if necessary
claude mcp remove <server-name>
claude mcp add <server-name> -- <command>
```

#### Authentication Failed
```bash
# Check environment variables
echo $API_KEY_NAME

# Re-authenticate via OAuth
/mcp  # In Claude Code

# Verify credentials in .env file
```

#### High Token Usage
```bash
# Set output limit
export MAX_MCP_OUTPUT_TOKENS=10000

# Use compressed output
claude -p "query" --uc

# Review MCP server configurations
claude mcp list
```

---

## Real-World Workflows

### Full-Stack Development

```bash
# Install development stack
claude mcp add github npx -- @composio/mcp github
claude mcp add postgres npx -- postgres-mcp-server
claude mcp add playwright npx -- -y playwright-mcp-server
claude mcp add sequential-thinking npx -- -y @modelcontextprotocol/server-sequential-thinking

# Workflow: Feature development
# 1. GitHub: Create issue and branch
# 2. Sequential Thinking: Plan implementation
# 3. Code implementation with Claude
# 4. PostgreSQL: Database migrations
# 5. Playwright: E2E tests
# 6. GitHub: Create PR and merge
```

### DevOps & Infrastructure

```bash
# Install DevOps stack
claude mcp add aws python -- -m aws_mcp_server
claude mcp add docker npx -- -y docker-mcp-server
claude mcp add sentry https://mcp.sentry.dev/mcp
claude mcp add cloudflare npx -- -y cloudflare-mcp-server

# Workflow: Deploy and monitor
# 1. Docker: Build containers
# 2. AWS: Deploy infrastructure
# 3. Cloudflare: Configure edge rules
# 4. Sentry: Monitor errors
```

### Data Engineering

```bash
# Install data stack
claude mcp add postgres npx -- postgres-mcp-server
claude mcp add clickhouse npx -- -y clickhouse-mcp-server
claude mcp add chroma npx -- -y chroma-mcp-server
claude mcp add greptimedb npx -- -y greptimedb-mcp-server

# Workflow: Data pipeline
# 1. PostgreSQL: Source data
# 2. Transform with Claude
# 3. ClickHouse: Analytics storage
# 4. Chroma: Vector embeddings
# 5. GreptimeDB: Metrics tracking
```

### Testing & Quality Assurance

```bash
# Install QA stack
claude mcp add playwright npx -- -y playwright-mcp-server
claude mcp add puppeteer npx -- -y puppeteer-mcp-server
claude mcp add sentry https://mcp.sentry.dev/mcp
claude mcp add digma npx -- -y digma-mcp-server

# Workflow: Comprehensive testing
# 1. Playwright: E2E test generation
# 2. Puppeteer: Browser automation
# 3. Digma: Performance insights
# 4. Sentry: Production monitoring
```

---

## MCP Server Comparison

### Database Servers

| Server | Best For | Complexity | Performance |
|--------|----------|------------|-------------|
| PostgreSQL | Relational data, SQL queries | Medium | High |
| ClickHouse | Time-series, analytics | Medium | Very High |
| Neo4j | Graph relationships | High | High |
| Supabase | Full-stack apps, real-time | Low | High |
| Chroma | Vector search, semantics | Low | High |

### Testing Servers

| Server | Best For | Learning Curve | Coverage |
|--------|----------|----------------|----------|
| Playwright | E2E testing, modern web | Low | Complete |
| Puppeteer | Browser automation | Medium | Good |
| Digma | Performance testing | Low | Focused |

### Cloud Providers

| Server | Ecosystem | Features | Enterprise Support |
|--------|-----------|----------|-------------------|
| AWS | Complete | Comprehensive | Official |
| Cloudflare | Edge/CDN | 16 specialized servers | Official |
| GCP | Google Cloud | Good | Community |
| Azure DevOps | Microsoft | CI/CD focus | Official |

---

## Resources

### Official Documentation
- **MCP Documentation**: [docs.claude.com/en/docs/mcp](https://docs.claude.com/en/docs/mcp)
- **Claude Code MCP Guide**: [code.claude.com/docs/en/mcp](https://code.claude.com/docs/en/mcp)
- **MCP Specification**: [modelcontextprotocol.io](https://modelcontextprotocol.io)

### MCP Server Directories
- **Official Servers**: [github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)
- **Awesome MCP Servers**: [github.com/wong2/awesome-mcp-servers](https://github.com/wong2/awesome-mcp-servers)
- **MCP Catalog**: [mcpservers.org](https://mcpservers.org)
- **MCPcat Guide**: [mcpcat.io/guides/best-mcp-servers-for-claude-code](https://mcpcat.io/guides/best-mcp-servers-for-claude-code/)

### Community Resources
- **GitHub MCP Server**: [github.com/github/github-mcp-server](https://github.com/github/github-mcp-server)
- **Claude Context**: [github.com/zilliztech/claude-context](https://github.com/zilliztech/claude-context)
- **MCP Topics**: [github.com/topics/claude-code-mcp](https://github.com/topics/claude-code-mcp)

### Getting Help
- **GitHub Discussions**: Community Q&A for specific servers
- **Discord**: MCP developer community
- **Stack Overflow**: Tagged questions with `model-context-protocol`

---

## Quick Reference Card

### Most Used Commands
```bash
# List all servers
claude mcp list

# Add stdio server
claude mcp add <name> npx -- -y <package-name>

# Add HTTP server
claude mcp add --transport http <name> <url>

# Add with environment variables
claude mcp add <name> npx -- -y <package> \
  --env KEY=value

# Remove server
claude mcp remove <name>

# Get server details
claude mcp get <name>

# Authenticate (in Claude Code)
/mcp
```

### Essential Environment Variables
```bash
ANTHROPIC_API_KEY      # Claude Code
GITHUB_TOKEN          # GitHub MCP
POSTGRES_URL          # PostgreSQL
DATABASE_URL          # Generic DB
SENTRY_AUTH_TOKEN     # Sentry
MAX_MCP_OUTPUT_TOKENS # Output limit (default: 25000)
```

### Server Categories Summary

| Category | Count | Examples |
|----------|-------|----------|
| **Essential** | 5 | Sequential Thinking, GitHub, Filesystem, Memory, Fetch |
| **Development** | 8+ | Git, Context7, GitLab, Docker, E2B |
| **Database** | 8+ | PostgreSQL, Supabase, Chroma, Neo4j |
| **Testing** | 4+ | Playwright, Puppeteer, Sentry, Digma |
| **Cloud** | 6+ | AWS, Cloudflare, GCP, Azure, Netlify, Vercel |
| **Business** | 6+ | Notion, Linear, Jira, Slack, Asana, Figma |
| **Total Available** | 100+ | Growing daily |

---

## Changelog

### 2025-11-10
- Initial comprehensive guide created
- 50+ MCP servers documented
- Installation examples and best practices added
- Real-world workflows included
- Quick reference card added

---

## Contributing

Found a useful MCP server not listed here? Please contribute:

1. Open an issue with server details
2. Submit a pull request with documentation
3. Share your workflows and use cases

---

**Last Updated**: 2025-11-10
**MCP Ecosystem Status**: 100+ servers, rapidly growing
**Maintainer**: Claude Code Community
