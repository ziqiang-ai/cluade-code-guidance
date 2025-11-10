# Claude Code CI/CD Integration Guide

Complete guide for integrating Claude Code into your continuous integration and deployment pipelines.

---

## Table of Contents

1. [Overview](#overview)
2. [GitLab CI/CD Integration](#gitlab-cicd-integration)
3. [GitHub Actions Integration](#github-actions-integration)
4. [Use Cases](#use-cases)
5. [Best Practices](#best-practices)
6. [Enterprise Considerations](#enterprise-considerations)
7. [Resources](#resources)

---

## Overview

Claude Code can be integrated into CI/CD pipelines to automate development workflows, including code reviews, documentation generation, issue resolution, and more. The integration is built on the Claude Code CLI and SDK, enabling programmatic use in your automation workflows.

### Key Capabilities

- **Automated Code Reviews**: Analyze test failures, security scans, and code quality
- **Documentation Generation**: Auto-generate release notes, changelogs, and API docs
- **Issue → Code Automation**: Convert issues into working merge requests
- **Test Analysis**: Diagnose and fix test failures automatically
- **Internationalization**: Translate and validate language files

---

## GitLab CI/CD Integration

### Status: **Beta** (Production-Ready)

The official GitLab integration transforms workflows through AI-powered automation with event-driven orchestration.

### Core Features

- **Instant MR Creation**: Describe requirements → Claude creates complete MR with changes and explanation
- **Automated Implementation**: Convert issues to functional code with single commands
- **Project-Aware**: Respects `CLAUDE.md` guidelines and existing code patterns
- **Enterprise-Ready**: Supports Claude API, AWS Bedrock, Google Vertex AI
- **Secure-by-Default**: Runs within GitLab runners with branch protection enforcement

### How It Works

1. **Event-Driven Orchestration**: GitLab triggers jobs via comments mentioning `@claude`
2. **Context Collection**: Automatically gathers repository context
3. **Task Execution**: Claude Code executes requested tasks
4. **Provider Abstraction**: Choose between SaaS (Claude API) or enterprise cloud (AWS/Google)
5. **Sandboxed Execution**: Isolated containers with workspace-scoped permissions
6. **MR Flow**: All changes flow through merge requests for review

### Quick Setup

#### Minimal Configuration

1. Add masked `ANTHROPIC_API_KEY` variable in GitLab CI/CD settings
2. Add this job to `.gitlab-ci.yml`:

```yaml
claude:
  stage: ai
  image: node:24-alpine3.21
  before_script:
    - apk add --no-cache git curl bash
    - npm install -g @anthropic-ai/claude-code
  script:
    - claude -p "Your instruction here" --permission-mode acceptEdits
  variables:
    ANTHROPIC_API_KEY: $ANTHROPIC_API_KEY  # Masked CI/CD variable
```

#### Enterprise Setup with AWS Bedrock

Use OIDC authentication to eliminate long-lived credentials:

```yaml
claude:
  stage: ai
  image: node:24-alpine3.21
  id_tokens:
    AWS_ID_TOKEN:
      aud: https://gitlab.com
  before_script:
    - apk add --no-cache git curl bash aws-cli
    - npm install -g @anthropic-ai/claude-code
    # Configure AWS credentials via OIDC
    - export AWS_WEB_IDENTITY_TOKEN_FILE=$AWS_ID_TOKEN
    - export AWS_ROLE_ARN=$AWS_ROLE_ARN
  script:
    - claude -p "Your instruction here" --permission-mode acceptEdits
  variables:
    AWS_ROLE_ARN: "arn:aws:iam::ACCOUNT:role/ROLE_NAME"
    AWS_DEFAULT_REGION: "us-east-1"
```

#### Enterprise Setup with Google Vertex AI

```yaml
claude:
  stage: ai
  image: node:24-alpine3.21
  id_tokens:
    GCP_ID_TOKEN:
      aud: https://gitlab.com
  before_script:
    - apk add --no-cache git curl bash
    - npm install -g @anthropic-ai/claude-code google-auth-library
  script:
    - claude -p "Your instruction here" --permission-mode acceptEdits
  variables:
    GOOGLE_APPLICATION_CREDENTIALS: $GCP_ID_TOKEN
    GCP_PROJECT_ID: "your-project-id"
```

### Usage Examples

#### Automated MR from Issue

```yaml
# Triggered by @claude mention in issue comment
claude_implement:
  stage: ai
  image: node:24-alpine3.21
  before_script:
    - apk add --no-cache git curl bash
    - npm install -g @anthropic-ai/claude-code
  script:
    - |
      claude -p "Implement the feature described in issue #${CI_COMMIT_REF_NAME}" \
        --permission-mode acceptEdits \
        --output-format json
  only:
    - merge_requests
  when: manual
```

#### Automated Release Notes

```yaml
generate_release_notes:
  stage: deploy
  image: node:24-alpine3.21
  before_script:
    - apk add --no-cache git curl bash
    - npm install -g @anthropic-ai/claude-code
  script:
    - |
      claude -p "Generate release notes for changes between ${CI_COMMIT_BEFORE_SHA} and ${CI_COMMIT_SHA}" \
        --permission-mode acceptEdits \
        > RELEASE_NOTES.md
  artifacts:
    paths:
      - RELEASE_NOTES.md
  only:
    - tags
```

#### Test Failure Analysis

```yaml
analyze_failures:
  stage: test
  image: node:24-alpine3.21
  before_script:
    - apk add --no-cache git curl bash
    - npm install -g @anthropic-ai/claude-code
  script:
    - |
      claude -p "Analyze the test failures and propose fixes" \
        --permission-mode acceptEdits
  when: on_failure
  dependencies:
    - test
```

---

## GitHub Actions Integration

### Status: **Community-Driven** (No Official Integration Yet)

While GitHub Actions is not officially documented, you can integrate Claude Code CLI manually.

### Basic Workflow Example

```yaml
name: Claude Code CI

on:
  issue_comment:
    types: [created]
  pull_request:
    types: [opened, synchronize]

jobs:
  claude-analyze:
    runs-on: ubuntu-latest
    if: contains(github.event.comment.body, '@claude') || github.event_name == 'pull_request'

    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '24'

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Run Claude Code
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude -p "Analyze this pull request and suggest improvements" \
            --permission-mode acceptEdits
```

### Automated Code Review

```yaml
name: AI Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '24'

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Review Changes
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          REVIEW=$(claude -p "Review the changes in this PR for security, performance, and code quality issues" --permission-mode acceptEdits)
          echo "$REVIEW" >> review.md

      - name: Post Review
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const review = fs.readFileSync('review.md', 'utf8');
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: review
            });
```

### Generate Documentation

```yaml
name: Generate Docs

on:
  push:
    branches: [main]
    paths:
      - 'src/**'

jobs:
  docs:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '24'

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Generate API Docs
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude -p "Update API documentation based on recent code changes" \
            --permission-mode acceptEdits

      - name: Commit Documentation
        run: |
          git config --local user.email "claude@anthropic.com"
          git config --local user.name "Claude Code"
          git add docs/
          git commit -m "docs: Update API documentation [skip ci]" || exit 0
          git push
```

---

## Use Cases

### 1. Automated Code Reviews

**Purpose**: Analyze code changes for quality, security, and performance issues

**Implementation**:
```yaml
claude -p "Review this code for:
- Security vulnerabilities
- Performance bottlenecks
- Code quality issues
- Best practice violations
Provide specific recommendations" --permission-mode acceptEdits
```

**Benefits**:
- Consistent review standards
- 24/7 availability
- Catches common issues early
- Reduces reviewer burden

### 2. Documentation Generation

**Purpose**: Keep documentation in sync with code changes

**Types**:
- Release notes from commits
- API documentation updates
- Changelog generation
- README updates

**Implementation**:
```yaml
claude -p "Generate release notes for version ${VERSION} including:
- New features
- Bug fixes
- Breaking changes
- Migration guide" --permission-mode acceptEdits
```

### 3. Issue → Code Automation

**Purpose**: Convert issue descriptions into working code

**Workflow**:
1. User creates issue with feature description
2. Mention `@claude` in comment
3. Claude analyzes requirements
4. Creates MR with implementation
5. Human review and merge

**Implementation**:
```yaml
claude -p "Implement the feature described in issue #${ISSUE_NUMBER}.
Follow existing code patterns and create comprehensive tests." \
--permission-mode acceptEdits
```

### 4. Test Failure Analysis

**Purpose**: Diagnose and fix failing tests automatically

**Implementation**:
```yaml
claude -p "Analyze the test failures in the CI output.
Identify root causes and propose fixes.
Update tests if requirements changed." --permission-mode acceptEdits
```

**Triggers**:
- Test job failure
- Manual trigger on failed builds
- Scheduled analysis of flaky tests

### 5. Internationalization

**Purpose**: Automate translation and validation

**Implementation**:
```yaml
claude -p "Translate new entries in en.json to:
- es.json (Spanish)
- fr.json (French)
- de.json (German)
Maintain formatting and context." --permission-mode acceptEdits
```

### 6. Security Scanning

**Purpose**: Analyze security scan results and propose fixes

**Implementation**:
```yaml
claude -p "Review the security scan results.
For each vulnerability:
- Explain the risk
- Propose a fix
- Update affected code" --permission-mode acceptEdits
```

---

## Best Practices

### Security

#### ✅ DO

- **Always use masked CI/CD variables** for API keys
- **Leverage OIDC authentication** for AWS/Google (eliminates static credentials)
- **Enforce branch protection** - all Claude changes go through MR review
- **Use sandboxed execution** with workspace-scoped permissions only
- **Audit Claude's actions** through your CI/CD logs
- **Rotate credentials regularly**
- **Use least-privilege access** for API keys

#### ❌ DON'T

- Store API keys in code or configuration files
- Allow direct commits to main/protected branches
- Skip code review for AI-generated changes
- Use root/admin credentials
- Commit secrets in Claude-generated code

### Cost Management

#### ✅ DO

- **Be explicit with `@claude` commands** to avoid unnecessary runs
- **Set appropriate timeouts** for CI jobs (e.g., 5-10 minutes)
- **Monitor token consumption** via Anthropic dashboard
- **Cache dependencies** on runners for faster execution
- **Use conditional triggers** - only run when needed
- **Limit concurrency** to prevent cost spikes
- **Set budget alerts** in your cloud provider

#### ❌ DON'T

- Run Claude on every commit without filters
- Use overly broad prompts that generate excessive tokens
- Skip caching - wastes time and money
- Run multiple concurrent jobs without limits

### Performance

#### ✅ DO

- **Keep configuration concise** - minimal pipeline definitions
- **Provide detailed issue descriptions** for better first-time results
- **Use dependency caching** on runners
- **Enable parallel execution** where possible
- **Optimize Docker images** - use Alpine variants
- **Pre-install Claude Code** in custom images for faster startup
- **Use artifacts** to pass data between jobs

#### ❌ DON'T

- Reinstall Claude Code on every run (use caching)
- Use large base images (node:latest → node:24-alpine3.21)
- Run sequential jobs that could be parallel
- Fetch entire git history when shallow clone suffices

### Configuration

#### ✅ DO

- **Create `CLAUDE.md`** at repository root with:
  - Coding standards
  - Architecture patterns
  - Testing requirements
  - Documentation style
- **Define project conventions** clearly
- **Document expected behavior** for Claude
- **Use `.claudeignore`** to exclude sensitive files
- **Version control Claude configurations**
- **Test prompts locally** before CI integration

#### ❌ DON'T

- Rely on implicit conventions
- Skip documentation of project-specific patterns
- Include generated files in Claude's context
- Hardcode environment-specific values

### Prompt Engineering for CI/CD

#### ✅ DO

- **Be specific and actionable**:
  ```
  Good: "Review security scan results and fix CVE-2024-1234 by upgrading library X to version Y"
  Bad: "Fix security issues"
  ```

- **Provide context**:
  ```
  Good: "Generate release notes for v2.0.0 including features from issues #45, #67, #89"
  Bad: "Make release notes"
  ```

- **Set clear boundaries**:
  ```
  Good: "Update API documentation for the User service only. Don't modify implementation."
  Bad: "Update docs"
  ```

- **Include acceptance criteria**:
  ```
  Good: "Implement feature X with unit tests achieving >80% coverage and following existing REST patterns"
  Bad: "Add feature X"
  ```

#### ❌ DON'T

- Use vague prompts expecting Claude to guess
- Omit important constraints or requirements
- Forget to specify output format when needed
- Skip validation criteria

---

## Enterprise Considerations

### Provider Options

| Provider | Best For | Authentication | Cost Model |
|----------|----------|----------------|------------|
| **Claude API** | Startups, small teams | API key | Pay-per-token |
| **AWS Bedrock** | AWS-native enterprises | OIDC, IAM roles | Pay-per-token + AWS fees |
| **Google Vertex AI** | GCP-native enterprises | OIDC, service accounts | Pay-per-token + GCP fees |

### Governance & Compliance

#### Access Control
- **Principle of Least Privilege**: Grant minimal permissions needed
- **Role-Based Access**: Define roles for different CI/CD operations
- **Audit Logging**: Track all Claude Code invocations
- **Secret Management**: Use dedicated secret managers (HashiCorp Vault, AWS Secrets Manager)

#### Code Quality Gates
- All changes require MR/PR review (no direct commits)
- Branch protection rules enforced
- Required approvals from human reviewers
- Automated testing must pass
- Security scans must pass
- Code coverage thresholds maintained

#### Compliance Tracking
- **Full audit trail** through GitLab/GitHub
- **Cost transparency** via your own credentials
- **Data residency** control through provider choice
- **Retention policies** for AI-generated artifacts
- **Compliance reports** from CI/CD logs

### Scalability Considerations

#### Rate Limiting
```yaml
# GitLab example
claude:
  script:
    - claude -p "$PROMPT" --permission-mode acceptEdits
  retry:
    max: 2
    when:
      - api_failure
      - runner_system_failure
  timeout: 10m
```

#### Concurrency Control
```yaml
# Limit concurrent Claude jobs
claude:
  resource_group: claude-ai
  script:
    - claude -p "$PROMPT" --permission-mode acceptEdits
```

#### Cost Optimization
- **Caching Strategy**: Cache Claude Code installation and common dependencies
- **Conditional Execution**: Only run Claude when specific files change
- **Job Deduplication**: Skip redundant runs for identical requests
- **Token Budgets**: Set per-job token limits

### Multi-Region Deployment

#### AWS Bedrock
```yaml
claude_us:
  variables:
    AWS_REGION: us-east-1
  script:
    - claude -p "$PROMPT"
  only:
    - branches@us-repo

claude_eu:
  variables:
    AWS_REGION: eu-west-1
  script:
    - claude -p "$PROMPT"
  only:
    - branches@eu-repo
```

### Disaster Recovery

#### Backup Strategies
- **Configuration Backup**: Version control all CI/CD configs
- **Fallback Providers**: Configure multiple AI providers
- **Manual Override**: Always allow manual execution
- **Health Checks**: Monitor Claude API availability

#### Rollback Procedures
```yaml
claude_safe:
  script:
    - |
      if ! claude -p "$PROMPT" --permission-mode acceptEdits; then
        echo "Claude failed, falling back to manual process"
        exit 1
      fi
  allow_failure: true
```

---

## Advanced Patterns

### Multi-Stage Pipelines

```yaml
stages:
  - analyze
  - implement
  - test
  - document
  - deploy

analyze_code:
  stage: analyze
  script:
    - claude -p "Analyze codebase for improvement opportunities" > analysis.json
  artifacts:
    paths:
      - analysis.json

implement_changes:
  stage: implement
  script:
    - claude -p "Implement improvements from analysis.json" --permission-mode acceptEdits
  dependencies:
    - analyze_code

run_tests:
  stage: test
  script:
    - npm test

update_docs:
  stage: document
  script:
    - claude -p "Update documentation reflecting changes" --permission-mode acceptEdits
  dependencies:
    - implement_changes
```

### Feedback Loops

```yaml
iterative_improvement:
  script:
    - |
      MAX_ITERATIONS=3
      for i in $(seq 1 $MAX_ITERATIONS); do
        echo "Iteration $i"
        claude -p "Improve code based on test results" --permission-mode acceptEdits
        npm test && break || continue
      done
```

### Context-Aware Prompts

```yaml
context_aware:
  script:
    - |
      CONTEXT=$(cat CLAUDE.md)
      CHANGES=$(git diff main...HEAD)

      claude -p "Context: ${CONTEXT}

      Changes:
      ${CHANGES}

      Task: Review these changes for compliance with our standards" \
      --permission-mode acceptEdits
```

---

## Troubleshooting

### Common Issues

#### Issue: API Rate Limiting
**Symptoms**: 429 errors, failed jobs
**Solution**:
```yaml
retry:
  max: 3
  when: api_failure
  exponential_backoff: true
```

#### Issue: High Token Usage
**Symptoms**: Unexpected costs
**Solution**:
- Use `--uc` flag for compressed output
- Limit context with `.claudeignore`
- Be more specific in prompts
- Set token budgets

#### Issue: Slow Pipeline Execution
**Symptoms**: Long CI/CD runs
**Solution**:
- Cache Claude Code installation
- Use Alpine images
- Enable parallel jobs
- Pre-build custom images

#### Issue: Inconsistent Results
**Symptoms**: Different outputs for same prompt
**Solution**:
- Improve prompt specificity
- Add validation steps
- Use `CLAUDE.md` for standards
- Set random seed if available

### Debug Mode

```yaml
debug_claude:
  script:
    - |
      export DEBUG=claude:*
      claude -p "$PROMPT" --permission-mode acceptEdits --verbose
  artifacts:
    when: on_failure
    paths:
      - debug.log
```

---

## Monitoring & Observability

### Metrics to Track

1. **Usage Metrics**
   - Jobs per day/week/month
   - Token consumption
   - Cost per job
   - API latency

2. **Quality Metrics**
   - MR acceptance rate
   - Code review feedback
   - Test pass rate
   - Bug introduction rate

3. **Performance Metrics**
   - Job duration
   - Queue time
   - Success rate
   - Retry rate

### Logging Best Practices

```yaml
claude_with_logging:
  script:
    - |
      START_TIME=$(date +%s)
      PROMPT="Your prompt here"

      echo "Starting Claude job at $(date)"
      echo "Prompt: $PROMPT"

      RESULT=$(claude -p "$PROMPT" --permission-mode acceptEdits 2>&1 | tee claude.log)
      EXIT_CODE=$?

      END_TIME=$(date +%s)
      DURATION=$((END_TIME - START_TIME))

      echo "Job completed in ${DURATION}s with exit code ${EXIT_CODE}"

      # Send metrics to monitoring system
      curl -X POST $METRICS_ENDPOINT \
        -d "duration=${DURATION}&exit_code=${EXIT_CODE}&tokens=${TOKENS}"
  artifacts:
    paths:
      - claude.log
    expire_in: 1 week
```

---

## Migration Guide

### From Manual to Automated

#### Phase 1: Pilot (Week 1-2)
- Install Claude Code in CI
- Test on non-critical workflows
- Gather feedback from team

#### Phase 2: Expansion (Week 3-4)
- Add documentation generation
- Enable code review assistance
- Monitor costs and performance

#### Phase 3: Production (Week 5-6)
- Enable for all repositories
- Set up governance policies
- Train team on best practices

#### Phase 4: Optimization (Ongoing)
- Refine prompts based on results
- Optimize for cost and speed
- Expand use cases

---

## Resources

### Official Documentation
- **GitLab CI/CD Integration**: [code.claude.com/docs/en/gitlab-ci-cd](https://code.claude.com/docs/en/gitlab-ci-cd)
- **Claude Code Overview**: [docs.anthropic.com/en/docs/claude-code/overview](https://docs.anthropic.com/en/docs/claude-code/overview)

### Code & Tools
- **GitHub Repository**: [github.com/anthropics/claude-code](https://github.com/anthropics/claude-code)
- **NPM Package**: [@anthropic-ai/claude-code](https://www.npmjs.com/package/@anthropic-ai/claude-code)

### Community Resources
- **GitHub Discussions**: Community Q&A and examples
- **GitLab CI/CD Templates**: Starter configurations
- **Example Workflows**: Real-world implementations

### Cost Estimation
- **Anthropic Pricing**: [anthropic.com/pricing](https://www.anthropic.com/pricing)
- **Token Calculator**: Estimate costs before implementation

### Security
- **OIDC Setup (AWS)**: [docs.aws.amazon.com/bedrock/](https://docs.aws.amazon.com/bedrock/)
- **OIDC Setup (GCP)**: [cloud.google.com/vertex-ai/](https://cloud.google.com/vertex-ai/)

---

## Quick Reference

### Essential Commands

```bash
# Install Claude Code
npm install -g @anthropic-ai/claude-code

# Run with prompt
claude -p "Your instruction here"

# Accept edits automatically (CI mode)
claude -p "Your instruction" --permission-mode acceptEdits

# Compressed output (save tokens)
claude -p "Your instruction" --uc

# Verbose debug mode
claude -p "Your instruction" --verbose
```

### Environment Variables

```bash
ANTHROPIC_API_KEY          # Claude API key (required)
AWS_REGION                 # AWS Bedrock region
AWS_ROLE_ARN              # AWS IAM role for OIDC
GOOGLE_APPLICATION_CREDENTIALS  # GCP service account
GCP_PROJECT_ID            # GCP project identifier
```

### Permission Modes

- `acceptEdits`: Auto-accept all changes (for CI)
- `ask`: Prompt for confirmation (default)
- `readOnly`: No file modifications

---

## Changelog

### 2025-11-10
- Initial guide created
- GitLab CI/CD integration documented
- GitHub Actions examples added
- Best practices established

---

## Contributing

Found an issue or have a suggestion? Please contribute:

1. Open an issue describing the problem/suggestion
2. Submit a pull request with improvements
3. Share your CI/CD workflows and examples

---

**Last Updated**: 2025-11-10
**Status**: Active Development
**Maintainer**: Claude Code Community
