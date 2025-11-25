# Claude Code + Skills 实战指南：从入门到精通

> 基于 Substack 社区实践经验的完整教程

## 目录

1. [核心概念](#核心概念)
2. [Claude Code 环境准备](#claude-code-环境准备)
3. [Skills 创建完整流程](#skills-创建完整流程)
4. [实战案例分析](#实战案例分析)
5. [Skills 可靠性优化](#skills-可靠性优化)
6. [最佳实践与技巧](#最佳实践与技巧)
7. [常见问题排查](#常见问题排查)
8. [进阶主题](#进阶主题)

---

## 核心概念

### Claude Code + Skills 是什么?

Claude Code 是 Anthropic 推出的命令行 AI 助手,Skills 则是一种可重用的工作流模块。两者结合后,你可以:

1. **一次定义,多处使用** - 将重复性工作流打包成 Skill
2. **自动加载** - Claude Code 根据上下文自动调用相关 Skills
3. **跨平台共享** - Skills 可在 Claude.ai、Claude Code、API 中使用
4. **代码执行能力** - Skills 可包含 Python、Node.js、Bash 脚本

### Claude 产品生态对比

| 功能 | Projects | Skills | MCP | Slash Commands | Sub-agents |
|------|----------|--------|-----|----------------|------------|
| 用途 | 协作伙伴 | 执行专家 | 外部连接 | 快捷命令 | 并行处理 |
| 触发方式 | 手动选择 | 自动/手动 | 被动集成 | 手动输入 | 手动调用 |
| 上下文持久化 | ✅ | ❌ | ❌ | ❌ | ✅ (独立) |
| 代码执行 | ❌ | ✅ | ❌ | ❌ | ❌ |
| 跨会话可用 | ❌ | ✅ | ✅ | ✅ | ❌ |
| 适用场景 | 长期项目 | 重复工作流 | 数据接入 | 简单任务 | 复杂任务 |

**关键区别** (来自 Wyndo 的总结):

- **Projects = Claude 与你共事** (collaborative co-worker)
- **Skills = Claude 为你执行** (execution specialist)
- **MCP = Claude 连接外界** (external integration)
- **Slash Commands = 快捷触发** (shortcuts)
- **Sub-agents = 独立智能** (parallel AI instances)

---

## Claude Code 环境准备

### 1. 安装 Claude Code

```bash
# macOS/Linux
brew install anthropic/claude/claude-code

# 或使用 npm
npm install -g @anthropic/claude-code

# 验证安装
claude-code --version
```

### 2. 配置 API 密钥

```bash
# 设置环境变量
export ANTHROPIC_API_KEY=sk-ant-api03-your-key-here

# 或在 ~/.bashrc / ~/.zshrc 中永久配置
echo 'export ANTHROPIC_API_KEY=sk-ant-api03-xxx' >> ~/.zshrc
source ~/.zshrc
```

### 3. 初始化项目

```bash
# 在项目根目录创建 .claude 文件夹
mkdir -p .claude/skills
mkdir -p .claude/hooks

# 创建配置文件
touch .claude/settings.json
```

### 4. 基础配置文件

`.claude/settings.json`:

```json
{
  "capabilities": {
    "skills": {
      "enabled": true,
      "autoActivate": true
    }
  },
  "hooks": {
    "UserPromptSubmit": []
  }
}
```

---

## Skills 创建完整流程

### 方法一:使用 Claude Code 自动生成

最简单的方式是让 Claude Code 帮你创建 Skill:

```bash
# 在 Claude Code 中运行
claude-code

# 然后输入提示
> Help me build a new Claude skill for SEO content optimization
```

Claude Code 会:
1. 询问 Skill 的详细需求
2. 生成 `SKILL.md` 文件
3. 创建必要的脚本和资源
4. 打包成 ZIP 文件

### 方法二:手动创建 (完全掌控)

#### Step 1: 创建 Skill 文件夹结构

```bash
# 创建 Skill 文件夹
mkdir -p .claude/skills/seo-optimizer

# 创建核心文件
touch .claude/skills/seo-optimizer/SKILL.md
```

#### Step 2: 编写 SKILL.md

`.claude/skills/seo-optimizer/SKILL.md`:

```markdown
---
name: "seo-optimizer"
description: "Optimize blog posts for SEO including URL slugs, keywords, meta descriptions, headings, internal links, and alt text. Use when the user wants to improve SEO or optimize content for search engines."
---

# SEO Content Optimizer

## Instructions

当用户请求 SEO 优化时,执行以下完整工作流:

### 1. 内容分析
- 提取文章 URL 或内容
- 识别核心主题和目标受众
- 分析当前 SEO 状态

### 2. 关键词研究
使用 web search 工具研究 9 种高意图关键词类型:
- Primary keywords (核心关键词)
- Long-tail keywords (长尾关键词)
- Question-based keywords (问题型关键词)
- Comparison keywords (对比型关键词)
- Location-based keywords (地域关键词)
- Seasonal keywords (季节性关键词)
- Product/service keywords (产品服务关键词)
- Intent-based keywords (意图关键词)
- LSI keywords (语义关键词)

### 3. URL 优化
- 创建简洁的 URL slug
- 包含主关键词
- 使用连字符分隔
- 避免特殊字符

### 4. 标题优化
- H1 包含主关键词(仅一个 H1)
- H2/H3 结构化组织内容
- 自然融入次要关键词
- 保持层级逻辑清晰

### 5. Meta 信息优化
- Meta title: 50-60 字符,包含主关键词
- Meta description: 150-160 字符,行动号召
- 保持品牌声音和语气

### 6. 内部链接建议
根据内容长度提供链接建议:
- <1000 字: 2-3 个内部链接
- 1000-2000 字: 4-6 个内部链接
- >2000 字: 7-10 个内部链接

### 7. 图片 Alt Text
- 描述性文本
- 自然包含关键词
- 保持简洁(100-125 字符)

### 8. 内容质量保证
- 保持原有写作风格和语气
- 避免关键词堆砌
- 确保可读性和用户体验

## 输出格式

```markdown
# SEO 优化报告

## URL Slug
建议: /your-optimized-slug

## 关键词策略
**主关键词**: keyword1
**次要关键词**: keyword2, keyword3
**长尾关键词**: long tail phrase

## Meta 信息
**Title**: Your Optimized Title (55 字符)
**Description**: Your compelling meta description with CTA (158 字符)

## 标题结构
# H1: Main Title
## H2: Section 1
### H3: Subsection 1.1
## H2: Section 2

## 内部链接建议
1. [Anchor text 1](相关文章链接) - 在第2段
2. [Anchor text 2](相关文章链接) - 在第4段

## 图片 Alt Text
1. hero-image.jpg: "Descriptive alt text with keyword"
2. screenshot.png: "Another descriptive alt text"

## 优化建议
- 改进点 1
- 改进点 2
- 后续优化方向
```

## 示例

**输入**: "Optimize my blog post about AI automation tools"

**输出**:
- 完整的 SEO 优化报告
- 关键词研究数据
- Meta 信息建议
- 结构化改进方案
```

#### Step 3: 添加代码脚本 (可选)

对于需要复杂计算或外部工具调用的 Skill,可以添加脚本:

`.claude/skills/seo-optimizer/scripts/keyword_analyzer.py`:

```python
#!/usr/bin/env python3
"""
SEO 关键词分析脚本
用于分析关键词竞争度和搜索量
"""

import sys
import json

def analyze_keywords(keywords):
    """分析关键词列表"""
    results = []
    for keyword in keywords:
        # 这里可以调用外部 API (如 Google Keyword Planner API)
        # 示例数据
        result = {
            "keyword": keyword,
            "search_volume": "估算值",
            "competition": "medium",
            "difficulty": 45
        }
        results.append(result)
    return results

if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Usage: keyword_analyzer.py 'keyword1,keyword2,keyword3'")
        sys.exit(1)

    keywords = sys.argv[1].split(',')
    results = analyze_keywords(keywords)
    print(json.dumps(results, indent=2, ensure_ascii=False))
```

#### Step 4: 打包和上传

**方式 A: 本地使用 (Claude Code)**

Claude Code 会自动发现 `.claude/skills/` 下的 Skills,无需额外操作。

**方式 B: Web/Desktop 使用**

```bash
# 打包成 ZIP
cd .claude/skills
zip -r seo-optimizer.zip seo-optimizer/

# 在 Claude.ai 中上传
# Settings > Capabilities > Upload Skill
```

**方式 C: API 使用**

```python
from anthropic import Anthropic

client = Anthropic()

# 上传 Skill
with open(".claude/skills/seo-optimizer/SKILL.md", "rb") as f:
    skill = client.beta.skills.create(
        display_title="SEO Optimizer",
        files=[{"path": "SKILL.md", "content": f.read()}]
    )

# 在对话中使用
container = client.beta.messages.create_container(
    skills={skill.id: "latest"}
)

resp = client.beta.messages.create(
    container_id=container.id,
    messages=[{
        "role": "user",
        "content": "Optimize this blog post for SEO: [content]"
    }]
)

print(resp.content)
```

---

## 实战案例分析

### 案例 1: Wyndo 的 SEO 优化 Skill

**背景**: Wyndo 每周需要花 2 小时手动优化 SEO,包括关键词研究、Meta 信息、内部链接等。

**解决方案**: 创建 SEO Optimizer Skill

**效果**:
- **时间节省**: 2 小时 → 15 分钟 (88% 提升)
- **一致性**: 每次都遵循完整的 SEO 检查清单
- **质量**: 不会遗漏任何优化步骤

**关键实现**:

```markdown
---
name: "seo-optimizer"
description: "Create structured, SEO-optimized content with URL slug, keyword research, headings optimization, meta descriptions, internal linking, and alt text. Use when optimizing content for search engines."
---

# SEO Content Optimizer

## Core Workflow

1. **内容提取**: 通过 URL 或直接输入获取内容
2. **关键词研究**: 9 种高意图关键词类型 (使用 web search)
3. **URL 优化**: 内容类型公式化方法
4. **内部链接**: 基于内容长度的智能推荐
5. **图片优化**: Alt text 生成
6. **标题结构**: 层级优化
7. **Meta 信息**: 标题和描述优化
8. **语气保持**: 保留原作者声音,避免关键词堆砌

## 触发方式

**隐式触发**:
- "优化这篇文章的 SEO"
- "帮我改进搜索排名"
- "分析 SEO 表现"

**显式触发**:
- "Use the SEO Optimizer skill on this post"
- "Run SEO optimization workflow"
```

**使用示例**:

```
# 在 Claude Code 中
> Optimize this newsletter for SEO: https://example.com/my-post

# Claude 自动:
# 1. 检测到 "optimize" + "SEO" 关键词
# 2. 加载 seo-optimizer Skill
# 3. 执行完整工作流
# 4. 返回优化报告
```

### 案例 2: Newsletter 内容重用 Skill

**背景**: 将一篇 newsletter 转换成多平台内容(Twitter、LinkedIn、Substack Notes)

**Skill 结构**:

```markdown
---
name: "newsletter-repurpose"
description: "Repurpose newsletter content into platform-specific formats: Twitter threads, LinkedIn posts, Substack Notes. Use when user wants to adapt content for social media."
---

# Newsletter Repurpose Skill

## Platform-Specific Rules

### Twitter/X Threads
- Hook: 强烈的开场(280 字符内)
- Structure: 8-12 条推文
- 每条推文 <280 字符
- 使用 2-3 个相关 hashtags
- 最后一条包含 CTA

### LinkedIn Posts
- Professional tone
- 1300-2000 字符
- 开头 150 字符吸引眼球
- 3-5 个要点
- 行业相关 hashtags

### Substack Notes
- 简短精炼(150-300 字)
- 包含原文链接
- 提取 1-2 个核心观点
- 鼓励互动的结尾

## Workflow

1. 提取核心论点和关键信息
2. 识别平台特定的受众需求
3. 适配内容长度和格式
4. 保持品牌声音
5. 添加平台特定的 CTA

## 示例输出

**输入**: [Newsletter URL]

**输出**:

### Twitter Thread (10 tweets)
1/10 🧵 Thread hook here...
2/10 Main point 1...
...
10/10 CTA and link

### LinkedIn Post
Professional opener...
• Key point 1
• Key point 2
• Key point 3
Call to action

### Substack Notes (10 variations)
1. Short insight 1 + link
2. Short insight 2 + link
...
```

**实际效果**:
- **1 篇 Newsletter** → **3 Twitter 线程 + 5 LinkedIn 帖子 + 10 Substack Notes**
- 时间: 手动 3 小时 → Skill 执行 20 分钟
- 保持品牌声音和各平台优化

### 案例 3: SvelteKit 开发 Skills (Scott Spence)

**背景**: SvelteKit 开发中有大量重复性模式(路由创建、表单处理、数据加载等)

**创建的 4 个 Skills**:

1. **svelte5-runes**: Svelte 5 响应式系统(`$state`, `$derived`, `$effect`)
2. **sveltekit-data-flow**: 数据加载模式、表单 actions、服务器函数
3. **sveltekit-structure**: 文件路由、布局、错误边界、SSR
4. **sveltekit-remote-functions**: 类型安全的远程函数模式

**Skills 定义示例**:

```markdown
---
name: "sveltekit-data-flow"
description: "SvelteKit data loading patterns, form actions, server vs universal load functions, fail(), redirect(), and serialization. Use when working with SvelteKit data fetching or forms."
---

# SvelteKit Data Flow Skill

## Load Functions

### Universal Load (+page.js)
```javascript
// Runs on both server and client
export async function load({ fetch, params }) {
  const response = await fetch(`/api/posts/${params.id}`);
  return {
    post: await response.json()
  };
}
```

### Server Load (+page.server.js)
```javascript
// Only runs on server, can access DB directly
export async function load({ params, locals }) {
  const post = await db.posts.get(params.id);
  return { post };
}
```

## Form Actions

```javascript
// +page.server.js
export const actions = {
  create: async ({ request, locals }) => {
    const data = await request.formData();
    const title = data.get('title');

    // Validation
    if (!title) {
      return fail(400, { title, missing: true });
    }

    // Save to DB
    const post = await db.posts.create({ title });

    // Redirect on success
    throw redirect(303, `/posts/${post.id}`);
  }
};
```

## Progressive Enhancement

```svelte
<script>
  import { enhance } from '$app/forms';
</script>

<form method="POST" action="?/create" use:enhance>
  <input name="title" />
  <button>Create</button>
</form>
```

## When to use this skill

- Creating data fetching logic
- Implementing forms with server actions
- Handling validation and errors
- Server-side redirects
- Progressive enhancement patterns
```

**挑战与解决方案**:

Skill 自动激活不可靠(仅 50% 成功率),Scott 通过测试框架发现了解决方案(见后文)。

---

## Skills 可靠性优化

### 问题: Skills 自动激活失败

**现象**: Claude Code 经常忽略相关 Skills,即使提示明确匹配 Skill 描述

**原因**: Claude 的"渐进式披露"(progressive disclosure)机制不够激进

**测试数据** (Scott Spence 的 200+ 次测试):

| Hook 类型 | 整体成功率 | 成本/测试 | 平均延迟 | 特点 |
|-----------|-----------|----------|---------|------|
| 无 Hook | 10-20% | $0.0058 | 6.7s | ❌ 基线,不可靠 |
| Simple Hook | 20% | $0.0058 | 6.7s | ❌ 仍然是抛硬币 |
| **Forced Eval Hook** | **84%** | $0.0067 | 7.2s | ✅ 最一致 |
| **LLM Eval Hook** | **80%** | $0.0061 | 6.0s | ✅ 更快更便宜 |

### 解决方案 1: Forced Eval Hook (推荐)

**原理**: 强制 Claude 在执行前显式评估每个 Skill

**实现步骤**:

1. 创建 Hook 脚本:

`.claude/hooks/skill-forced-eval-hook.sh`:

```bash
#!/bin/bash

cat << 'EOF'
🎯 MANDATORY SKILL EVALUATION PROTOCOL

You MUST follow this 3-step process BEFORE any implementation:

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
STEP 1 - EVALUATE EACH SKILL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For EVERY available skill, explicitly state:
- Skill name
- YES/NO decision
- Brief reason (one sentence)

Format:
✓ skill-name: YES - reason
✗ skill-name: NO - reason

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
STEP 2 - ACTIVATE SKILLS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For each YES skill:
- Use Skill(skill-name) tool NOW
- Do NOT proceed without activation

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
STEP 3 - IMPLEMENT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Only AFTER skills are activated, proceed with implementation.

⚠️ CRITICAL: The evaluation is WORTHLESS unless you ACTIVATE the skills.
⚠️ If you skip Step 2, you FAILED the task.

EOF
```

2. 设置权限:

```bash
chmod +x .claude/hooks/skill-forced-eval-hook.sh
```

3. 配置到 `.claude/settings.json`:

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/skill-forced-eval-hook.sh"
          }
        ]
      }
    ]
  }
}
```

**效果示例**:

```
User: Create a new SvelteKit route with form handling

Claude:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
STEP 1 - EVALUATE EACH SKILL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✓ sveltekit-structure: YES - Need to create route structure
✓ sveltekit-data-flow: YES - Form handling requires form actions
✓ svelte5-runes: YES - Need reactive state for form
✗ sveltekit-remote-functions: NO - Not using remote functions

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
STEP 2 - ACTIVATE SKILLS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Activating Skill(sveltekit-structure)...
Activating Skill(sveltekit-data-flow)...
Activating Skill(svelte5-runes)...

[Skills loaded successfully]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
STEP 3 - IMPLEMENT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Now creating the route...
```

**优点**:
- ✅ **84% 成功率** (vs 20% baseline)
- ✅ 最一致,不会完全失败
- ✅ 无需外部 API
- ✅ 纯客户端解决方案

**缺点**:
- ⚠️ 输出冗长(显示所有 Skill 评估)
- ⚠️ 稍慢(+1 秒延迟)
- ⚠️ 略贵(+$0.0009/次)

### 解决方案 2: LLM Eval Hook

**原理**: 在 Claude Code 处理提示前,先用 API 预评估需要哪些 Skills

**实现**:

`.claude/hooks/skill-llm-eval-hook.sh`:

```bash
#!/bin/bash

# 读取用户提示
USER_PROMPT=$(cat)

# 获取可用 Skills 列表
AVAILABLE_SKILLS=$(cat << 'EOF'
1. sveltekit-structure: File routing, layouts, error boundaries
2. sveltekit-data-flow: Data loading, form actions, server functions
3. svelte5-runes: Reactive state with $state, $derived, $effect
4. sveltekit-remote-functions: Type-safe remote function calls
EOF
)

# 调用 Claude API 评估
EVALUATION=$(curl -s https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d @- << EOF | jq -r '.content[0].text'
{
  "model": "claude-haiku-4.5",
  "max_tokens": 500,
  "messages": [{
    "role": "user",
    "content": "User prompt: \"$USER_PROMPT\"\n\nAvailable skills:\n$AVAILABLE_SKILLS\n\nWhich skills should activate? Return ONLY skill names, one per line."
  }]
}
EOF
)

# 生成激活指令
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "🤖 Pre-evaluated Skills to Activate:"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "$EVALUATION"
echo ""
echo "INSTRUCTION: Activate these skills using Skill() tool BEFORE implementation."
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
```

**优点**:
- ✅ **80% 成功率**
- ✅ 10% 更便宜
- ✅ 17% 更快
- ✅ 有时候更"聪明"(添加相关 Skill)

**缺点**:
- ⚠️ 可能完全失败(某些提示 0% 成功)
- ⚠️ 需要 API 密钥
- ⚠️ 每次提示都调用外部 API
- ⚠️ 更多变,不够一致

### 选择建议

| 场景 | 推荐 Hook | 理由 |
|------|-----------|------|
| 需要最高一致性 | Forced Eval | 84% 成功率,不会完全失败 |
| 成本敏感 | LLM Eval | 便宜 10%,快 17% |
| 简单单 Skill 场景 | LLM Eval | 足够可靠,性能更好 |
| 复杂多 Skill 场景 | Forced Eval | 更可靠,不会遗漏 |
| 无法访问外部 API | Forced Eval | 纯本地方案 |
| 不介意冗长输出 | Forced Eval | 明确显示思考过程 |

---

## 最佳实践与技巧

### 1. Description 是关键

**Bad**:
```yaml
description: "Processes documents"
```

**Good**:
```yaml
description: "Extracts text and tables from PDF files. Use when working with PDF documents, when user mentions PDFs, forms, document extraction, or needs to parse PDF content."
```

**要点**:
- ✅ 明确说明 Skill 做什么
- ✅ 列出触发关键词
- ✅ 描述使用场景
- ✅ 包含相关术语和同义词

### 2. 提供具体示例

在 `SKILL.md` 中包含输入输出示例:

```markdown
## Example Input
"Create a Q3 business review presentation"

## Example Output
A 15-slide PowerPoint with:
- Executive summary (slides 1-2)
- Key metrics dashboard (slide 3)
- Performance by segment (slides 4-7)
- Challenges and opportunities (slides 8-10)
- Q4 roadmap (slides 11-13)
- Appendix with detailed data (slides 14-15)
```

### 3. 模块化大型 Skills

当 `SKILL.md` 变得过长时,拆分成多个文件:

```
financial-modeling-skill/
├── SKILL.md              # 核心指令
├── DCF-MODELS.md         # DCF 方法论详细说明
├── VALIDATION-RULES.md   # 验证框架
├── scripts/
│   ├── calculate.py      # 计算脚本
│   └── validate.py       # 验证脚本
└── examples/
    └── sample-model.xlsx # 示例文件
```

### 4. 测试多种变体

不要只测试一次,尝试:
- ✅ 相同请求的不同表达方式
- ✅ 边缘情况
- ✅ 与其他 Skills 组合
- ✅ 隐式和显式触发

**测试清单**:

```bash
# 显式触发
"Use the SEO Optimizer skill on this post"

# 隐式触发
"Optimize this for search engines"
"Improve SEO performance"
"Make this rank better on Google"

# 边缘情况
"Quick SEO check" (是否触发完整工作流?)
"SEO and content repurposing" (是否触发多个 Skills?)
```

### 5. 版本管理

使用 Git 管理 Skills 的版本:

```bash
# .gitignore
.claude/skills/*.zip

# 版本化源文件
git add .claude/skills/*/SKILL.md
git add .claude/skills/*/scripts/
git commit -m "feat(skills): add SEO optimizer v1.0"
git tag skill-seo-v1.0
```

### 6. 安全第一

**警告**: Skills 可以执行代码!

**安全检查清单**:
- [ ] 只使用可信来源的 Skills
- [ ] 阅读整个 `SKILL.md` 文件
- [ ] 检查所有脚本是否有可疑操作
- [ ] 警惕外部 URL 获取
- [ ] 验证工具权限请求
- [ ] 检查意外的网络调用

**危险信号**:
- ❌ 执行未知命令
- ❌ 读取敏感文件(`~/.ssh`, `~/.aws`)
- ❌ 网络请求到未知服务器
- ❌ 修改系统配置
- ❌ 要求过多权限

**来源可信度**:
1. ✅ **你创建的** Skill
2. ✅ **Anthropic 官方** Skill
3. ⚠️ **知名开发者** Skill (审查后使用)
4. ❌ **社交媒体推荐** Skill (极度谨慎)
5. ❌ **匿名来源** Skill (不要使用)

### 7. 性能优化

**避免 Skill 过大**:
- ❌ 不要在 Skill 中包含大文件
- ❌ 避免嵌入大量数据
- ✅ 使用外部数据源(运行时加载)
- ✅ 保持 `SKILL.md` 简洁(<2000 行)

**渐进式披露优化**:

```markdown
---
name: "data-processor"
description: "Process CSV/JSON data with validation and transformation"
---

# Data Processor

## Quick Start (Level 1 - Always Loaded)
Basic instructions for simple tasks...

## Advanced Options (Level 2 - Load on demand)
Detailed configurations...
Refer to VALIDATION-RULES.md for complex scenarios.

## Resources (Level 3 - Load when needed)
- schemas/data-schema.json
- scripts/transform.py
```

Claude 会:
1. 始终加载 name + description
2. 需要时加载 Quick Start
3. 仅在明确需要时加载 Advanced Options 和 Resources

---

## 常见问题排查

### 问题 1: Skill 不触发

**症状**: Claude 忽略相关 Skill,即使提示明确匹配

**诊断步骤**:

1. **检查 description 质量**:
   ```bash
   # 好的 description 包含:
   # - 清晰的功能说明
   # - 使用场景
   # - 触发关键词
   ```

2. **验证 SKILL.md 格式**:
   ```bash
   # 确保 YAML frontmatter 正确
   head -5 .claude/skills/your-skill/SKILL.md

   # 应该看到:
   # ---
   # name: "skill-name"
   # description: "..."
   # ---
   ```

3. **测试不同触发方式**:
   ```
   # 显式触发
   "Use the skill-name skill to..."

   # 隐式触发
   "Do the thing that skill-name does..."
   ```

4. **启用 Forced Eval Hook**:
   ```bash
   # 按照前文配置 Forced Eval Hook
   # 观察 Claude 是否评估了该 Skill
   ```

**解决方案**:

| 原因 | 解决方法 |
|------|---------|
| Description 太模糊 | 重写,添加具体关键词和场景 |
| Name 中有大写字母 | 改为全小写 `skill-name` |
| YAML 格式错误 | 运行 YAML linter 检查 |
| Skill 未正确安装 | 重新上传或检查路径 |
| 渐进式披露未触发 | 使用 Forced Eval Hook |

### 问题 2: Skill 执行不完整

**症状**: Skill 激活了,但只执行部分步骤

**原因**:
- 指令不够明确
- 缺少示例
- 步骤之间逻辑不清晰

**解决方案**:

```markdown
# Bad - 模糊指令
## Instructions
Optimize the content for SEO.

# Good - 清晰步骤
## Instructions

Execute ALL of the following steps in order:

### Step 1: Keyword Research (MANDATORY)
- Use web search tool
- Research 9 keyword types
- Prioritize by search intent

### Step 2: URL Optimization (MANDATORY)
- Create slug following formula: [topic]-[modifier]-[format]
- Max 60 characters
- Include primary keyword

### Step 3: ...
...

## Validation Checklist
Before returning results, confirm:
- [ ] All 8 steps completed
- [ ] Keyword research performed
- [ ] URL slug created
- [ ] Meta info optimized
- ...
```

### 问题 3: 脚本执行失败

**症状**: Skill 中的 Python/Node.js 脚本报错

**常见错误**:

1. **权限问题**:
   ```bash
   chmod +x .claude/skills/your-skill/scripts/*.py
   ```

2. **Python 依赖缺失**:
   ```bash
   # 在 Skill 文件夹中添加 requirements.txt
   echo "requests==2.31.0" > requirements.txt

   # Claude Code 会自动安装
   # 或手动安装:
   pip install -r requirements.txt
   ```

3. **路径问题**:
   ```python
   # Bad - 相对路径
   with open('data.json') as f:

   # Good - 使用 __file__ 获取绝对路径
   import os
   script_dir = os.path.dirname(__file__)
   data_path = os.path.join(script_dir, '../data/data.json')
   with open(data_path) as f:
   ```

### 问题 4: Skill 冲突

**症状**: 多个 Skills 同时激活,产生混乱输出

**解决方案**:

1. **明确 Skill 边界**:
   ```yaml
   # Skill A
   description: "Handle data loading ONLY. Use for +page.js and +page.server.js load functions."

   # Skill B
   description: "Handle form actions ONLY. Use for form submissions and validation."
   ```

2. **使用互斥关键词**:
   ```yaml
   # SEO Skill
   description: "... Use when optimizing for search engines or SEO."

   # Content Repurpose Skill
   description: "... Use when adapting content for social media platforms, NOT for SEO."
   ```

3. **Skill 协调指令**:
   ```markdown
   ## Skill Coordination

   This skill works with:
   - sveltekit-structure: Use AFTER structure is created
   - svelte5-runes: Use TOGETHER for reactive forms

   Do NOT use with:
   - sveltekit-remote-functions: Conflicting patterns
   ```

### 问题 5: ChatGPT 中使用 Skills

**症状**: 上传 Skill ZIP 到 ChatGPT 后不工作

**原因**: ChatGPT 不像 Claude 那样自动识别 Skills

**解决方案**:

```
# 在 ChatGPT 中明确指令
User: [上传 seo-optimizer.zip]

"I've uploaded a Claude Skill file. Please:
1. Extract and read the SKILL.md file
2. Follow the instructions in the YAML frontmatter and markdown body
3. Execute the complete workflow as defined
4. Use the examples as reference

Now, use this skill to optimize the following content for SEO:
[paste content]
"
```

**额外配置**:
- ✅ 启用 ChatGPT 的"深度思考"模式
- ✅ 明确告诉它遵循 SKILL.md 中的所有步骤
- ✅ 如果跳过步骤,重新提示"完成所有步骤"

---

## 进阶主题

### 1. Skills 组合模式

多个 Skills 可以协同工作:

**场景**: 完整的内容发布工作流

```
User: "Write a blog post about AI agents, optimize it for SEO, and create social media content"

Claude:
1. 激活 `content-writer` Skill → 撰写博客
2. 激活 `seo-optimizer` Skill → 优化 SEO
3. 激活 `newsletter-repurpose` Skill → 生成社交媒体内容
4. (可选) 激活 `wordpress-publisher` MCP → 发布到 WordPress
```

**最佳实践**:
- 在每个 Skill 的 description 中说明与其他 Skills 的关系
- 使用明确的工作流顺序
- 避免功能重叠

### 2. Skills + Projects 协同

**模式**: Projects 提供长期上下文,Skills 提供可执行工作流

```
Project: "Marketing Content Hub"
- 项目说明: 品牌指南、语气、目标受众
- 知识库: 过往文章、产品信息

Skills:
- seo-optimizer
- newsletter-repurpose
- content-calendar

使用场景:
User: "Create next week's content"

Claude:
1. 从 Project 获取品牌上下文
2. 使用 content-calendar Skill 生成主题
3. 使用 seo-optimizer Skill 优化
4. 使用 newsletter-repurpose Skill 创建社交内容
```

### 3. Skills + MCP 集成

**完整自动化工作流**:

```
User: "Publish today's blog post"

Skill: blog-publisher
├── Step 1: 使用 seo-optimizer Skill 优化
├── Step 2: 使用 wordpress MCP 发布到 blog
├── Step 3: 使用 twitter MCP 发送推文
├── Step 4: 使用 linkedin MCP 创建帖子
└── Step 5: 使用 analytics MCP 设置跟踪
```

**实现示例**:

`.claude/skills/blog-publisher/SKILL.md`:

```markdown
---
name: "blog-publisher"
description: "Complete blog publishing workflow: SEO optimization, multi-platform publishing, analytics setup. Use when user wants to publish content."
---

# Blog Publisher Skill

## Prerequisites
Required MCPs:
- wordpress-mcp (for blog publishing)
- social-media-mcp (for Twitter/LinkedIn)
- analytics-mcp (for tracking setup)

Required Skills:
- seo-optimizer (for content optimization)

## Workflow

### Phase 1: Content Preparation
1. Activate Skill(seo-optimizer)
2. Apply SEO optimization
3. Generate meta information
4. Create URL slug

### Phase 2: Publishing
1. Use wordpress-mcp to:
   - Create draft post
   - Upload featured image
   - Set categories/tags
   - Publish

### Phase 3: Social Distribution
1. Use social-media-mcp to:
   - Create Twitter thread (3-5 tweets)
   - Create LinkedIn post
   - Schedule posts

### Phase 4: Analytics
1. Use analytics-mcp to:
   - Set up UTM tracking
   - Create conversion goals
   - Initialize dashboards

## Output Format

```
✅ Blog Published Successfully

📝 Post Details:
- URL: https://blog.example.com/your-slug
- Published: 2025-11-25 10:30 AM
- Status: Live

📊 SEO Score: 85/100
- Primary Keyword: "AI automation"
- Meta Title: 55 characters ✓
- Meta Description: 158 characters ✓

🚀 Social Media:
- Twitter: https://twitter.com/you/status/123456
- LinkedIn: https://linkedin.com/posts/456789

📈 Analytics:
- Tracking Code: UA-123456-1
- Campaign: blog-launch
- Dashboard: https://analytics.example.com/blog-launch
```
```

### 4. 动态 Skills 生成

**概念**: 根据用户需求动态创建临时 Skills

**实现**:

```python
from anthropic import Anthropic

client = Anthropic()

def create_custom_skill(user_request):
    """根据用户请求动态生成 Skill"""

    # 使用 Claude 生成 Skill 定义
    skill_design = client.messages.create(
        model="claude-sonnet-4.5",
        messages=[{
            "role": "user",
            "content": f"""Design a Claude Skill for: {user_request}

Generate a complete SKILL.md file with:
1. Appropriate name and description
2. Clear step-by-step instructions
3. Examples
4. Output format

Return ONLY the markdown content for SKILL.md"""
        }]
    )

    skill_content = skill_design.content[0].text

    # 创建临时 Skill
    skill = client.beta.skills.create(
        display_title=f"Custom: {user_request[:50]}",
        files=[{"path": "SKILL.md", "content": skill_content.encode()}]
    )

    return skill.id

# 使用
skill_id = create_custom_skill("Analyze competitor pricing strategies")

container = client.beta.messages.create_container(
    skills={skill_id: "latest"}
)

# 使用生成的 Skill
response = client.beta.messages.create(
    container_id=container.id,
    messages=[{
        "role": "user",
        "content": "Analyze these competitor prices: [data]"
    }]
)
```

### 5. Skills 测试框架

**自动化测试 Skill 质量**:

```bash
# 项目结构
tests/
├── fixtures/
│   ├── test-inputs.json
│   └── expected-outputs.json
├── test-skills.js
└── results/
    └── test-results.db

# test-skills.js
import Anthropic from '@anthropic/sdk';
import sqlite3 from 'sqlite3';

const db = new sqlite3.Database('./results/test-results.db');

// 测试配置
const TESTS = [
  {
    skill: 'seo-optimizer',
    input: 'Optimize this blog post for SEO: [content]',
    expectations: {
      hasKeywordResearch: true,
      hasMetaInfo: true,
      hasURLSlug: true,
      completedAllSteps: true
    }
  },
  // ... 更多测试
];

async function testSkill(skillName, input, expectations) {
  const client = new Anthropic();

  const start = Date.now();
  const response = await client.messages.create({
    model: 'claude-sonnet-4.5',
    messages: [{ role: 'user', content: input }]
  });
  const latency = Date.now() - start;

  // 验证结果
  const results = {
    skillActivated: response.content[0].text.includes(`Skill(${skillName})`),
    hasKeywordResearch: response.content[0].text.includes('Keyword Research'),
    hasMetaInfo: response.content[0].text.includes('Meta Title'),
    // ... 更多验证
  };

  // 存储到数据库
  db.run(`
    INSERT INTO test_results
    (skill, input, latency, success, details)
    VALUES (?, ?, ?, ?, ?)
  `, [skillName, input, latency, results.success, JSON.stringify(results)]);

  return results;
}

// 运行所有测试
for (const test of TESTS) {
  await testSkill(test.skill, test.input, test.expectations);
}
```

**测试报告**:

```sql
-- 查询成功率
SELECT
  skill,
  COUNT(*) as total_tests,
  SUM(CASE WHEN success = 1 THEN 1 ELSE 0 END) as passed,
  ROUND(AVG(latency), 2) as avg_latency_ms,
  ROUND(100.0 * SUM(CASE WHEN success = 1 THEN 1 ELSE 0 END) / COUNT(*), 2) as success_rate
FROM test_results
GROUP BY skill;
```

---

## 社区资源

### 官方资源

1. **Anthropic Skills 仓库**: https://github.com/anthropics/skills
   - 官方示例 Skills
   - 最佳实践文档
   - 社区贡献 Skills

2. **Claude Code 文档**: https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview
   - Skills 完整 API 文档
   - 创建指南
   - 故障排除

### 社区工具

1. **claude-skills-cli** (Scott Spence)
   ```bash
   npm install -g claude-skills-cli

   # 生成新 Skill
   claude-skills-cli create

   # 添加 Hook
   claude-skills-cli add-hook
   ```

2. **Awesome Claude Skills** (ComposioHQ)
   - https://github.com/ComposioHQ/awesome-claude-skills
   - 精选 Skills 列表
   - 各类别最佳实践

3. **Claude Code Superpowers**
   - https://github.com/obra/superpowers
   - 高级 Skills 集合
   - 开发者工作流优化

### 学习资源

**Substack 文章**:
- Wyndo (AI Maker): SEO 优化、内容重用实战案例
- Jonathan (Limited Edition): Skills 完整对比指南
- Scott Spence: SvelteKit 开发 Skills、可靠性测试框架

**视频教程**:
- Anthropic 官方: Skills 介绍和基础用法
- Sid Bharath: Skills 深度解析,从概念到实现
- 社区案例分享: YouTube "Claude Skills Tutorial"

---

## 总结与下一步

### 核心要点回顾

1. **Skills 是什么**: 可重用的 AI 工作流模块,一次定义多处使用
2. **与 Claude Code 集成**: 自动发现、渐进式加载、跨平台共享
3. **创建 Skill**: SKILL.md + YAML frontmatter + 可选脚本
4. **可靠性优化**: Forced Eval Hook (84% 成功率) 或 LLM Eval Hook (80%)
5. **最佳实践**:
   - 精确的 description
   - 具体的示例
   - 模块化设计
   - 安全审查
   - 充分测试

### 从这里开始

**第一步**: 识别重复工作
```
思考: 我每周重复做什么任务?
- SEO 优化?
- 内容重用?
- 代码审查?
- 数据分析?
- 报告生成?
```

**第二步**: 创建第一个 Skill
```bash
# 使用最简单的模板
mkdir -p .claude/skills/my-first-skill
```

**第三步**: 测试和迭代
```
- 测试 5 次以上
- 不同表达方式
- 边缘情况
- 记录失败原因
```

**第四步**: 优化可靠性
```bash
# 添加 Forced Eval Hook
cp skill-forced-eval-hook.sh .claude/hooks/
chmod +x .claude/hooks/skill-forced-eval-hook.sh
```

**第五步**: 扩展到团队
```
- 分享 Skills 到团队仓库
- 建立 Skill 库
- 文档化使用指南
- 收集团队反馈
```

### 进阶路线

1. **个人效率**: 3-5 个核心 Skills 覆盖日常工作
2. **团队协作**: Skills 共享库、版本管理、质量标准
3. **系统集成**: Skills + MCP + Projects 完整自动化
4. **生态贡献**: 开源优秀 Skills、分享实践经验

---

## 附录

### A. SKILL.md 完整模板

```markdown
---
name: "skill-name"  # 全小写,连字符分隔
description: "Clear description of what this skill does and when to use it. Include keywords: keyword1, keyword2, scenario1, scenario2."
---

# Skill Display Name

## Purpose
Brief explanation of the skill's purpose and value.

## Prerequisites
- Required tools/MCPs
- Required dependencies
- Environment setup

## Instructions

### Step 1: [Step Name]
- Detailed sub-step 1
- Detailed sub-step 2
- Expected outcome

### Step 2: [Step Name]
...

### Step N: Validation
Checklist before completion:
- [ ] All steps executed
- [ ] Output format correct
- [ ] Quality checks passed

## Input Format
Expected input structure and examples.

## Output Format
Expected output structure and examples.

## Example

### Input
```
Example user request
```

### Output
```
Example skill output
```

## Edge Cases
- Edge case 1 and handling
- Edge case 2 and handling

## Error Handling
- Error type 1: Resolution
- Error type 2: Resolution

## Integration
- Works with Skills: skill-a, skill-b
- Works with MCPs: mcp-x, mcp-y
- Conflicts with: skill-c (explain why)

## Changelog
- v1.0.0 (2025-11-25): Initial release
- v1.1.0 (2025-12-01): Added feature X
```

### B. Hook 脚本库

所有 Hook 脚本可在以下仓库找到:

- **Forced Eval Hook**: https://github.com/spences10/claude-skills-cli
- **LLM Eval Hook**: https://github.com/spences10/claude-skills-cli
- **Simple Hook**: https://github.com/spences10/svelte-claude-skills

### C. 测试 Prompts 集合

**SEO 优化类**:
```
1. "Optimize this blog post for SEO"
2. "Improve search engine ranking for this article"
3. "Make this content more discoverable on Google"
4. "SEO analysis and recommendations"
5. "Keywords research and optimization"
```

**内容重用类**:
```
1. "Turn this newsletter into social media posts"
2. "Repurpose this article for Twitter, LinkedIn, and Substack"
3. "Create platform-specific content from this blog"
4. "Adapt this for multiple channels"
5. "Generate social content from newsletter"
```

**代码开发类** (SvelteKit):
```
1. "Create a new route with form handling"
2. "Add data loading to this page"
3. "Implement server actions for contact form"
4. "Build a counter using Svelte 5 runes"
5. "Setup remote function for user profile"
```

### D. 常用 YAML Frontmatter 字段

虽然 Anthropic 官方只支持 `name` 和 `description`,但可以添加自定义字段用于文档:

```yaml
---
# 官方字段 (必需)
name: "skill-name"
description: "Skill description with keywords"

# 自定义字段 (可选,仅用于文档)
version: "1.0.0"
author: "Your Name"
created: "2025-11-25"
updated: "2025-11-25"
category: "productivity"  # seo, content, development, data, automation
difficulty: "beginner"    # beginner, intermediate, advanced
tags:
  - seo
  - content-optimization
  - automation
dependencies:
  - web-search-tool
  - file-read-tool
platforms:
  - claude-code
  - claude-web
  - claude-api
license: "MIT"
---
```

### E. 调试技巧

**启用详细日志**:

```bash
# Claude Code 调试模式
export CLAUDE_DEBUG=1
claude-code

# 查看 Skills 加载过程
export CLAUDE_SKILLS_DEBUG=1
```

**检查 Skill 是否被识别**:

```bash
# 列出所有可用 Skills
claude-code skills list

# 检查特定 Skill 详情
claude-code skills info seo-optimizer
```

**监控 Skills 激活**:

在 Forced Eval Hook 输出中查看评估过程:

```
✓ seo-optimizer: YES - User mentioned "SEO" and "optimize"
✗ content-writer: NO - Not a content creation request
✓ web-search: YES - Need keyword research
```

---

## 结语

Claude Code + Skills 的组合代表了 AI 辅助工作流的新范式。从 Wyndo 的 SEO 自动化(2 小时→15 分钟),到 Scott 的开发 Skills(84% 激活成功率),社区实践证明了这套系统的价值。

关键在于:
1. **找到自己的重复性工作** - 每个人都有独特的工作流
2. **精心设计 description** - 这决定了 Skill 能否被正确激活
3. **充分测试和迭代** - 第一版不会完美,但可以逐步优化
4. **善用 Hooks 提升可靠性** - Forced Eval Hook 是目前最佳方案
5. **关注安全** - 只使用可信来源的 Skills

Skills 不是魔法,但如果使用得当,确实能大幅提升效率。希望这份指南能帮助你构建属于自己的 AI 工作流自动化系统。

**祝你 Skill 开发愉快! 🚀**

---

**文档信息**:
- 版本: 1.0.0
- 更新: 2025-11-25
- 作者: 基于 Substack 社区实践整理
- 来源: Wyndo (AI Maker), Scott Spence, Sid Bharath, Jonathan 等的实战经验

**参考资源**:
- Anthropic Skills 官方文档
- claude-skills-cli 工具
- svelte-claude-skills 测试框架
- 社区 Substack 文章集合
