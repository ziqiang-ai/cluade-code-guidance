# Firecrawl MCP Server: Complete Usage Guide for Claude Code

## Overview

Firecrawl MCP Server is a powerful integration that brings intelligent web scraping and data extraction capabilities to AI assistants like Claude Code. By implementing the Model Context Protocol (MCP), it acts as a bridge between Large Language Models (LLMs) and the live web, effectively giving AI agents "eyes and hands" to interact with internet content.

## What is Firecrawl MCP?

**Firecrawl** is an AI-powered web crawler that transforms websites into structured, LLM-ready data. The **Firecrawl MCP Server** extends this capability by:

- Enabling natural language commands for web scraping tasks
- Converting unstructured web content into clean, structured data
- Handling JavaScript-heavy websites and dynamic content
- Providing real-time streaming results using Server-Sent Events (SSE)
- Managing rate limiting and automatic retries intelligently

## Key Features

### Core Capabilities
- **Single-page scraping**: Extract content from individual URLs
- **Site crawling**: Recursively crawl entire websites
- **Batch processing**: Handle multiple URLs simultaneously
- **Web search**: Perform searches and extract results
- **Structured extraction**: Pull specific data in JSON format based on prompts
- **Deep research**: Comprehensive content indexing and analysis

### Technical Advantages
- JavaScript rendering support for modern web apps
- Automatic retries with exponential backoff
- Built-in rate limiting for efficient batch operations
- Credit usage monitoring and optimization
- Support for both cloud and self-hosted Firecrawl instances
- Mobile/desktop viewport emulation
- Smart content filtering and metadata extraction

## Installation Guide

### Prerequisites

Before you begin, ensure you have:
1. **Node.js and npm** installed on your system
2. **A Firecrawl API Key** (obtain from [Firecrawl's official website](https://firecrawl.dev))
3. **An MCP-compatible environment** (Claude Desktop, Cursor, or similar)

### Method 1: Quick Setup with npx (Recommended)

The fastest way to get started is using `npx`:

```bash
# Unix/Linux/macOS
env FIRECRAWL_API_KEY=fc-YOUR_API_KEY npx -y firecrawl-mcp

# Windows (PowerShell)
$env:FIRECRAWL_API_KEY="fc-YOUR_API_KEY"
npx -y firecrawl-mcp
```

### Method 2: Manual Installation

For more control over the installation:

```bash
# Install globally
npm install -g firecrawl-mcp

# Set environment variable (Unix/Linux/macOS)
export FIRECRAWL_API_KEY=fc-YOUR_API_KEY

# Set environment variable (Windows)
set FIRECRAWL_API_KEY=fc-YOUR_API_KEY

# Run the server
firecrawl-mcp
```

### Method 3: Using Smithery with VeryaX

For a managed deployment with runtime orchestration:

```bash
npx -y @smithery/cli@latest install @VeyraX/veyrax-mcp --client claude
```

This method provides:
- Automated configuration management
- Runtime orchestration through VeryaX dashboard
- Simplified API key management
- Easy integration with Claude Desktop

## Configuration for Claude Desktop

### Basic Configuration

Add the following to your Claude Desktop configuration file:

**Location:**
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`

**Configuration:**
```json
{
  "mcpServers": {
    "firecrawl-mcp": {
      "command": "npx",
      "args": [
        "-y",
        "firecrawl-mcp"
      ],
      "env": {
        "FIRECRAWL_API_KEY": "fc-YOUR_API_KEY"
      }
    }
  }
}
```

### Advanced Configuration Options

For more control, you can add optional environment variables:

```json
{
  "mcpServers": {
    "firecrawl-mcp": {
      "command": "npx",
      "args": ["-y", "firecrawl-mcp"],
      "env": {
        "FIRECRAWL_API_KEY": "fc-YOUR_API_KEY",
        "FIRECRAWL_API_URL": "https://api.firecrawl.dev",
        "FIRECRAWL_TIMEOUT": "30000",
        "FIRECRAWL_MAX_RETRIES": "3",
        "FIRECRAWL_VIEWPORT": "desktop"
      }
    }
  }
}
```

**Environment Variables:**
- `FIRECRAWL_API_KEY` (required): Your Firecrawl API key
- `FIRECRAWL_API_URL` (optional): Custom API URL for self-hosted instances
- `FIRECRAWL_TIMEOUT`: Request timeout in milliseconds (default: 30000)
- `FIRECRAWL_MAX_RETRIES`: Maximum retry attempts (default: 3)
- `FIRECRAWL_VIEWPORT`: Viewport mode - "desktop" or "mobile"

### Testing the Connection

After configuration:

1. **Restart Claude Desktop** completely
2. **Verify the connection** by looking for the MCP server indicator
3. **Test with a simple command**: "Use Firecrawl to scrape the homepage of example.com"

## Configuration for Cursor IDE

Add to your Cursor MCP configuration file:

```json
{
  "mcpServers": {
    "firecrawl-mcp": {
      "command": "npx",
      "args": ["-y", "firecrawl-mcp"],
      "env": {
        "FIRECRAWL_API_KEY": "fc-YOUR_API_KEY"
      }
    }
  }
}
```

## Available Tools

The Firecrawl MCP Server provides several powerful tools:

### 1. `firecrawl_scrape`
Extract content from a single URL.

**Use case:** "Scrape the main content from https://example.com/article"

**Features:**
- Extracts text, metadata, and links
- Handles JavaScript-rendered content
- Returns structured markdown or JSON

### 2. `firecrawl_crawl`
Recursively crawl entire websites.

**Use case:** "Crawl the entire documentation site at https://docs.example.com"

**Features:**
- Discovers and follows internal links
- Configurable depth and URL patterns
- Batch processing capabilities

### 3. `firecrawl_search`
Perform web searches and extract results.

**Use case:** "Search for recent articles about AI web scraping"

**Features:**
- Query-based web searching
- Structured result extraction
- Relevance ranking

### 4. `firecrawl_extract`
Pull structured data based on natural language prompts.

**Use case:** "Extract all product names, prices, and ratings from this e-commerce page"

**Features:**
- LLM-powered data extraction
- Custom schema definition via prompts
- Returns structured JSON output

## Practical Usage Examples

### Example 1: Single Page Scraping

**Prompt to Claude:**
```
Use Firecrawl to scrape the content from https://news.ycombinator.com
and give me the top 10 post titles with their URLs.
```

### Example 2: Batch Processing

**Prompt to Claude:**
```
I have a list of 20 blog URLs. Use Firecrawl to scrape all of them
and extract the title, author, publication date, and main content
from each. Then create a summary table.
```

### Example 3: Competitive Analysis

**Prompt to Claude:**
```
Crawl the pricing pages of these 5 competitor websites:
1. https://competitor1.com/pricing
2. https://competitor2.com/pricing
...

Extract all pricing tiers, features, and costs into a comparison table.
```

### Example 4: Documentation Indexing

**Prompt to Claude:**
```
Crawl the entire documentation site at https://docs.framework.com
and create an index of all pages with their titles and main topics.
```

### Example 5: Real-time Data Extraction

**Prompt to Claude:**
```
Search for the latest information about "Claude Code MCP servers"
and extract the top 5 most recent articles with summaries.
```

## Best Practices

### Optimization Tips

1. **API Key Security**: Never commit API keys to version control
2. **Rate Limiting**: Respect website rate limits to avoid blocks
3. **Batch Operations**: Group multiple URLs for efficient processing
4. **Viewport Selection**: Use appropriate viewport for mobile/desktop content
5. **Timeout Configuration**: Adjust timeouts based on site complexity

### Performance Considerations

- **Caching**: Firecrawl implements intelligent caching to reduce redundant requests
- **Parallel Processing**: Batch operations run in parallel for efficiency
- **Retry Logic**: Automatic retries handle transient failures gracefully
- **Credit Management**: Monitor your API credit usage for cost optimization

### Error Handling

Common issues and solutions:

**API Key Invalid:**
- Verify your API key is correct
- Check for extra spaces or quotes
- Ensure environment variable is set properly

**Timeout Errors:**
- Increase `FIRECRAWL_TIMEOUT` value
- Check if the target website is slow or down
- Reduce crawl depth for large sites

**Rate Limit Exceeded:**
- Implement delays between requests
- Reduce concurrent batch size
- Upgrade your Firecrawl plan if needed

## Advanced Use Cases

### Integration with Claude Code Workflows

#### Automated Research Pipeline
```
1. Use Firecrawl to search for latest research papers on [topic]
2. Extract key findings from each paper
3. Synthesize information into a comprehensive report
4. Generate citations and references
```

#### Content Aggregation System
```
1. Crawl multiple news sources daily
2. Extract articles matching specific criteria
3. Categorize and summarize content
4. Generate a curated newsletter
```

#### Competitive Intelligence
```
1. Monitor competitor websites for changes
2. Extract pricing, features, and announcements
3. Track changes over time
4. Generate competitive analysis reports
```

## Self-Hosted Firecrawl

For organizations requiring data privacy or custom configurations:

### Setup
```bash
# Set custom API URL
export FIRECRAWL_API_URL=https://your-firecrawl-instance.com/api/v1

# Run with custom endpoint
npx -y firecrawl-mcp
```

### Benefits
- Complete data control and privacy
- Custom rate limiting and policies
- No cloud API costs
- Integration with internal tools

## Troubleshooting Guide

### Connection Issues

**Problem:** Claude Desktop doesn't recognize the MCP server

**Solutions:**
1. Verify configuration file syntax (valid JSON)
2. Check file location is correct for your OS
3. Restart Claude Desktop completely
4. Review logs for error messages

### Performance Issues

**Problem:** Slow scraping or timeouts

**Solutions:**
1. Increase timeout settings
2. Reduce crawl depth
3. Use batch processing for multiple URLs
4. Check network connectivity

### Data Quality Issues

**Problem:** Incomplete or incorrect data extraction

**Solutions:**
1. Verify the target website is accessible
2. Check if JavaScript rendering is needed
3. Refine extraction prompts for better accuracy
4. Use appropriate viewport settings

## Integration with SuperClaude Framework

When using Firecrawl with the SuperClaude framework:

### Persona Recommendations
- **Analyzer**: For systematic web research and data extraction
- **Scribe**: For documentation and content aggregation
- **Architect**: For competitive analysis and system design research

### Command Integration
- `/analyze --focus research`: Use Firecrawl for comprehensive research
- `/document`: Aggregate and structure web content into documentation
- `/task`: Long-term web monitoring and data collection projects

### Flag Combinations
- `--think`: Deep analysis of scraped content
- `--uc`: Compressed output for large scraping operations
- `--seq`: Structured multi-step web research workflows

## Conclusion

Firecrawl MCP Server transforms Claude Code into a powerful web research and data extraction tool. By following this guide, you can:

- Set up Firecrawl MCP in minutes
- Perform sophisticated web scraping with natural language
- Extract structured data from any website
- Automate research and content aggregation workflows
- Build intelligent data pipelines with AI assistance

The combination of Firecrawl's robust scraping capabilities with Claude Code's intelligence creates a powerful platform for web data analysis, research automation, and content aggregation.

## Resources

- **Official Firecrawl Website**: [https://firecrawl.dev](https://firecrawl.dev)
- **MCP Documentation**: [https://modelcontextprotocol.io](https://modelcontextprotocol.io)
- **GitHub Repository**: Search for "firecrawl-mcp-server" on GitHub
- **Community Support**: Join MCP community forums for assistance

---

**Last Updated**: November 2025
**Version**: 1.0
**Author**: Claude Code with SuperClaude Framework
