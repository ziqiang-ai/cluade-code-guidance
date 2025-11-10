# Claude Code: Subagents & Hooks Complete Guide

## Table of Contents
- [Subagents](#subagents)
  - [What Are Subagents?](#what-are-subagents)
  - [Quick Start](#quick-start)
  - [Configuration](#configuration)
  - [Automatic Delegation](#automatic-delegation)
  - [Built-In Subagent Types](#built-in-subagent-types)
  - [Advanced Features](#advanced-features)
  - [Best Practices](#best-practices)
- [Hooks](#hooks)
  - [What Are Hooks?](#what-are-hooks)
  - [Hook Events](#hook-events)
  - [Configuration Structure](#configuration-structure)
  - [Hook Types](#hook-types)
  - [Common Use Cases](#common-use-cases)
  - [Hook Input/Output](#hook-inputoutput)
  - [Security Best Practices](#security-best-practices)
  - [Debugging](#debugging)

---

## SUBAGENTS

### What Are Subagents?

Subagents are specialized AI assistants that Claude Code delegates tasks to. Each operates with **its own isolated context window** separate from the main conversation.

**Key Benefits:**
- **Context Preservation** - Isolated contexts prevent main conversation pollution
- **Specialized Expertise** - Fine-tuned with detailed instructions for specific domains
- **Reusability** - Work across projects and can be shared with teams
- **Flexible Permissions** - Different tool access levels for security

### Quick Start

1. Run `/agents` command
2. Select "Create New Agent" (project or user-level)
3. Define the subagent with description, tools, and system prompt
4. Save and use automatically or invoke explicitly

### Configuration

#### Storage Locations

| Type | Location | Scope |
|------|----------|-------|
| Project subagents | `.claude/agents/` | Current project only |
| User subagents | `~/.claude/agents/` | All projects |

**Note:** Project-level subagents take precedence when name conflicts occur.

#### File Format

Subagents use Markdown with YAML frontmatter:

```markdown
---
name: identifier-name
description: Purpose and invocation context
tools: Tool1, Tool2 (optional)
model: sonnet (optional)
---

System prompt defining role and behavior...
```

#### Configuration Fields

- **name** (required): Lowercase identifier with hyphens (e.g., `code-reviewer`)
- **description** (required): Purpose and invocation guidance
- **tools** (optional): Comma-separated tool list; inherits all if omitted
- **model** (optional): `sonnet`, `opus`, `haiku`, or `'inherit'`

### Automatic Delegation

Claude Code intelligently invokes subagents based on:
- Task description in your request
- Description field in subagent configurations
- Current context and available tools

**Tip:** Include phrases like "use PROACTIVELY" in descriptions to encourage automatic delegation.

### Explicit Invocation

Request specific subagents directly:

```
"Use the code-reviewer subagent to check my recent changes"
"Have the debugger subagent investigate this error"
```

### Built-In Subagent Types

#### Plan Subagent
Used during plan mode for codebase research. Equipped with Sonnet model and read/search tools.

#### Common Custom Subagents

1. **Code Reviewer** - Evaluates quality, security, and maintainability
   - Tools: Read, Grep, Glob, Bash

2. **Debugger** - Specializes in root cause analysis for errors and test failures

3. **Data Scientist** - Handles SQL queries and BigQuery analysis operations

### Advanced Features

#### Chaining Subagents

Combine multiple subagents for complex workflows:

```
"Use code-analyzer first, then optimizer to fix performance issues."
```

#### Resumable Subagents

- Subagents receive unique IDs
- Resume previous conversations using the `resume` parameter with an `agentId`
- Preserves context for long-running analysis

#### CLI-Based Configuration

Define subagents dynamically using `--agents` flag with JSON format for quick testing or automation scripts.

### Best Practices

✅ **Generate Initial Subagents with Claude** - Let Claude create the structure, then customize

✅ **Design Focused Subagents** - Single responsibility per subagent

✅ **Write Detailed System Prompts** - Include specific instructions with examples

✅ **Restrict Tool Access** - Only provide necessary tools for security

✅ **Version Control Project Subagents** - Store in `.claude/agents/` for team collaboration

⚠️ **Performance Consideration** - Subagents preserve main conversation context but start fresh each invocation, potentially adding latency

---

## HOOKS

### What Are Hooks?

Hooks are **shell commands that execute at specific lifecycle points** in Claude Code. They enable deterministic control over the agent's behavior.

> "By encoding these rules as hooks rather than prompting instructions, you turn suggestions into app-level code that executes every time."

### Hook Events

| Event | Trigger | Supports Matcher |
|-------|---------|------------------|
| `PreToolUse` | Before tool execution | ✅ Yes |
| `PostToolUse` | After successful tool completion | ✅ Yes |
| `Notification` | System notifications sent | ❌ No |
| `UserPromptSubmit` | User submits prompt | ❌ No |
| `Stop` | Main agent finishes responding | ❌ No |
| `SubagentStop` | Subagent task completes | ❌ No |
| `PreCompact` | Before context compaction | ✅ Yes |
| `SessionStart` | Session begins or resumes | ✅ Yes |
| `SessionEnd` | Session terminates | ❌ No |

### Configuration Structure

Hooks are configured in settings files (user, project, or local) with this pattern:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Executing bash command' >> /tmp/claude.log"
          }
        ]
      }
    ]
  }
}
```

**Configuration Locations:**
- User settings: `~/.claude/settings.json`
- Project settings: `.claude/settings.json`
- Local settings: `.claude/settings.local.json`

**Matcher Patterns:**
- **Simple strings** match exactly (e.g., `"Bash"`)
- **Regex patterns** for multiple tools (e.g., `"Edit|Write"`)
- **Wildcard** `"*"` or empty `""` matches all tools
- **MCP tools** use pattern `mcp__<server>__<tool>`

### Hook Types

#### 1. Command Hooks (`type: "command"`)

Execute bash scripts with tool parameters and session data via stdin. Useful for deterministic validation and file operations.

```json
{
  "type": "command",
  "command": "your-shell-command-here"
}
```

#### 2. Prompt-Based Hooks (`type: "prompt"`)

Query an LLM (Haiku) for context-aware decisions. Currently supported for `Stop`, `SubagentStop`, and `UserPromptSubmit` events.

```json
{
  "type": "prompt",
  "prompt": "Should I allow this action? $ARGUMENTS"
}
```

**Expected JSON Response:**
```json
{
  "decision": "approve" | "block",
  "reason": "explanation",
  "continue": false,
  "stopReason": "message",
  "systemMessage": "warning"
}
```

### Common Use Cases

#### 1. Logging Bash Commands

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"$(date): $input\" >> /tmp/bash-commands.log"
          }
        ]
      }
    ]
  }
}
```

#### 2. Code Formatting (PostToolUse)

Automatically format TypeScript files after edits:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "if [[ \"$file_path\" =~ \\.tsx?$ ]]; then prettier --write \"$file_path\"; fi"
          }
        ]
      }
    ]
  }
}
```

#### 3. File Protection (PreToolUse)

Block modifications to sensitive files:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write|Delete",
        "hooks": [
          {
            "type": "command",
            "command": "if [[ \"$file_path\" =~ (\\.env|\\.git/|package-lock\\.json) ]]; then echo 'Protected file!' >&2; exit 2; fi"
          }
        ]
      }
    ]
  }
}
```

#### 4. Desktop Notifications

```json
{
  "hooks": {
    "Notification": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "notify-send 'Claude Code' 'Action completed'"
          }
        ]
      }
    ]
  }
}
```

#### 5. Markdown Formatting

Python script detecting language tags and fixing formatting:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "python scripts/format-markdown.py \"$file_path\""
          }
        ]
      }
    ]
  }
}
```

### Hook Input/Output

#### Hook Input (via stdin)

All hooks receive JSON via stdin:

```json
{
  "session_id": "string",
  "transcript_path": "path/to/session.jsonl",
  "cwd": "current/working/directory",
  "permission_mode": "default|plan|acceptEdits|bypassPermissions",
  "hook_event_name": "EventName",
  "tool_name": "ToolName",
  "tool_input": { /* tool-specific parameters */ }
}
```

**Event-Specific Fields:**
- `PreToolUse` / `PostToolUse`: Include `tool_name` and `tool_input`
- `UserPromptSubmit`: Includes `prompt` field
- `SessionStart`: Includes `CLAUDE_ENV_FILE` for persisting variables

#### Exit Code Behavior

- **Exit Code 0**: Success
  - Stdout shown in transcript mode
  - For `UserPromptSubmit`/`SessionStart`: stdout added as context

- **Exit Code 2**: Blocking error
  - Stderr fed to Claude for handling

- **Other Exit Codes**: Non-blocking error
  - Stderr shown to user

#### JSON Output

Hooks can return structured responses for advanced control:

```json
{
  "continue": true,
  "stopReason": "optional message",
  "suppressOutput": false,
  "systemMessage": "optional warning"
}
```

**Event-Specific JSON Fields:**

**PreToolUse:**
```json
{
  "permissionDecision": "allow" | "deny" | "ask",
  "permissionDecisionReason": "explanation",
  "updatedInput": { /* modified tool parameters */ }
}
```

**PostToolUse:**
```json
{
  "decision": "block" | undefined,
  "additionalContext": "extra information for Claude"
}
```

**UserPromptSubmit:**
```json
{
  "decision": "block" | undefined,
  "additionalContext": "context to add to prompt"
}
```

**Stop/SubagentStop:**
```json
{
  "decision": "block" | undefined,
  "reason": "required if blocking"
}
```

**SessionStart:**
```json
{
  "additionalContext": "context for session"
}
```

### Environment Variables

Available in all hooks:

- `CLAUDE_PROJECT_DIR`: Project root directory
- `CLAUDE_ENV_FILE`: File path for persisting environment variables (SessionStart only)
- `CLAUDE_CODE_REMOTE`: Indicates web environment (`"true"`) vs. local CLI

**SessionStart Context Persistence:**

Write environment variables to `$CLAUDE_ENV_FILE` to make them available for all subsequent bash commands:

```bash
echo 'export NODE_ENV=production' >> "$CLAUDE_ENV_FILE"
echo 'export API_URL=https://api.example.com' >> "$CLAUDE_ENV_FILE"
```

### Special Features

#### MCP Tool Support

Match MCP tools using pattern `mcp__<server>__<tool>`:

```json
{
  "matcher": "mcp__memory__create_entities",
  "hooks": [...]
}
```

#### Plugin Hooks

- Automatically merged from plugin `hooks/hooks.json` files
- Multiple hooks matching the same event execute in parallel
- Plugin directory referenced via `${CLAUDE_PLUGIN_ROOT}`

#### Execution Details

- **Timeout**: 60 seconds default; configurable per command
- **Parallelization**: All matching hooks run concurrently
- **Deduplication**: Identical commands automatically deduplicated
- **Environment**: Inherits Claude Code's environment with added variables

### Security Best Practices

⚠️ **CRITICAL WARNING**: Hooks run automatically during the agent loop with your current environment's credentials!

#### Essential Security Guidelines

✅ **Validate and Sanitize Input Data**
```bash
# Good: Validate before use
if [[ ! "$file_path" =~ ^[a-zA-Z0-9/_.-]+$ ]]; then
  echo "Invalid file path" >&2
  exit 2
fi
```

✅ **Quote Shell Variables Properly**
```bash
# Good
prettier --write "$file_path"

# Bad - vulnerable to injection
prettier --write $file_path
```

✅ **Block Path Traversal Attempts**
```bash
# Check for dangerous patterns
if [[ "$file_path" =~ \.\. ]]; then
  echo "Path traversal detected!" >&2
  exit 2
fi
```

✅ **Use Absolute Paths**
```bash
# Good
"$CLAUDE_PROJECT_DIR/scripts/validate.sh" "$file_path"

# Risky - depends on current directory
scripts/validate.sh "$file_path"
```

✅ **Exclude Sensitive Files**
```bash
# Protect critical files
if [[ "$file_path" =~ (\.env|\.git/|credentials|\.key|\.pem) ]]; then
  echo "Cannot modify sensitive file!" >&2
  exit 2
fi
```

✅ **Review All Commands Before Deployment**
- Test in isolated environments first
- Verify command behavior thoroughly
- Document hook purposes and behavior

✅ **Test Thoroughly in Safe Environments**
- Use non-production data for testing
- Verify error handling works correctly
- Check permission behaviors

### Debugging

#### Verification Commands

**View Registered Hooks:**
```
/hooks
```

**Debug Mode:**
```bash
claude --debug
```

Shows:
- Which hooks executed
- Exit codes
- Command output
- Execution timing

#### Common Issues

**1. Unescaped Quotes in JSON**
```json
// Bad
"command": "echo "hello""

// Good
"command": "echo \"hello\""
```

**2. Case-Sensitive Matcher Mismatches**
```json
// Matcher is case-sensitive
"matcher": "bash"  // Won't match "Bash" tool
"matcher": "Bash"  // Correct
```

**3. Scripts Requiring Executable Permissions**
```bash
# Make script executable
chmod +x ~/.claude/scripts/my-hook.sh
```

**4. JSON Parsing Errors**
```bash
# Test JSON validity
echo '{"your": "json"}' | jq .
```

#### Troubleshooting Workflow

1. Run `/hooks` to verify hook is registered
2. Use `claude --debug` to see execution details
3. Check exit codes and error messages
4. Verify file permissions and paths
5. Test hooks in isolation before deploying

---

## Additional Resources

- **Official Documentation**: [https://code.claude.com/docs/](https://code.claude.com/docs/)
- **Subagents Reference**: [https://code.claude.com/docs/en/sub-agents](https://code.claude.com/docs/en/sub-agents)
- **Hooks Reference**: [https://code.claude.com/docs/en/hooks](https://code.claude.com/docs/en/hooks)
- **Common Workflows**: [https://code.claude.com/docs/en/common-workflows](https://code.claude.com/docs/en/common-workflows)

---

## Summary

**Subagents** provide specialized, context-isolated AI assistance for specific domains, enabling:
- Parallel task execution
- Domain expertise
- Reusable workflows
- Team collaboration

**Hooks** provide deterministic control over Claude Code's behavior through:
- Automated validation
- Code formatting
- Security enforcement
- Custom workflows

Together, they enable powerful, customizable AI-assisted development workflows while maintaining security and control.
