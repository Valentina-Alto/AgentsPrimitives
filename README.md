# Agentic Primitives & Context Engineering Demo

> A hands-on reference repository showcasing **agentic primitives** — the building blocks that shape how AI coding agents understand and interact with your codebase.

## What Are Agentic Primitives?

Modern AI coding agents like GitHub Copilot are only as effective as the context they receive. **Agentic primitives** are the structured, composable units of context that developers embed directly in their repositories to guide agent behavior. Rather than relying on generic model knowledge, these primitives let you _engineer_ the context an agent operates within — turning a general-purpose LLM into a domain-aware, project-specific collaborator.

This approach is often called **context engineering**: the deliberate practice of designing and curating the information an AI agent receives so it produces more accurate, consistent, and useful outputs. Instead of prompt-hacking in a chat window, you declare intent and domain knowledge _as code_, versioned alongside your source.

### The Five Agentic Primitives

| Primitive | Purpose | Location |
|-----------|---------|----------|
| **Instructions** | Project-wide rules and conventions the agent always follows | `.github/copilot-instructions.md` |
| **Prompts** | Reusable, parameterized task templates for common workflows | `.github/prompts/*.prompt.md` |
| **Agents** | Specialized personas with defined expertise, tools, and scope | `.github/agents/*.agent.md` |
| **Skills** | Domain knowledge documents the agent consults on demand | `.github/skills/*/SKILL.md` |
| **MCP Servers** | External tool integrations that extend what the agent can _do_ | `.vscode/mcp.json` |

Together, these primitives form a layered context system:

```
┌─────────────────────────────────────────┐
│             MCP Servers                 │  ← External capabilities (APIs, tools)
├─────────────────────────────────────────┤
│              Agents                     │  ← Specialized personas with tools
├─────────────────────────────────────────┤
│              Skills                     │  ← On-demand domain knowledge
├─────────────────────────────────────────┤
│             Prompts                     │  ← Reusable task templates
├─────────────────────────────────────────┤
│           Instructions                  │  ← Always-on project rules
└─────────────────────────────────────────┘
```

---

## Primitives in This Repository

### 1. Instructions — `copilot-instructions.md`

**File:** `.github/copilot-instructions.md`

Instructions are the foundation layer. They are **always injected** into every Copilot interaction within this project, providing persistent, project-wide context the agent cannot ignore.

This repository's instructions define:

- **Project type** — A vanilla web application using only HTML, CSS, and JavaScript.
- **Folder structure** — `index.html`, `style.css`, `script.js`, and `README.md`.
- **Coding standards** — Semicolons required, single quotes for strings, arrow functions for callbacks.
- **UI guidelines** — Light/dark mode toggle, modern and clean design.
- **Skills routing** — Directs the agent to check the `skills/` folder before answering domain-specific questions.

**Why it matters:** Without instructions, the agent might suggest TypeScript, React, or other frameworks. With them, every response stays aligned with the project's actual constraints.

---

### 2. Prompts — Technology Stack Blueprint Generator

**File:** `.github/prompts/technology-stack-blueprint-generator.prompt.md`

Prompts are **reusable, on-demand task templates** that you invoke when needed. Unlike instructions (which are always active), prompts are triggered explicitly to perform a specific workflow.

This repository includes a **Technology Stack Blueprint Generator** prompt that:

- **Auto-detects** the technology stack by scanning project files, dependencies, and configurations.
- **Supports multiple platforms** — .NET, Java, JavaScript, React, Python, and more.
- **Is highly configurable** via template variables:
  - `DEPTH_LEVEL` — Basic, Standard, Comprehensive, or Implementation-Ready
  - `INCLUDE_VERSIONS`, `INCLUDE_LICENSES`, `INCLUDE_DIAGRAMS` — Toggle sections on/off
  - `OUTPUT_FORMAT` — Markdown, JSON, YAML, or HTML
- **Generates a complete blueprint** covering naming conventions, code organization, API patterns, data access patterns, and implementation templates.

**Why it matters:** Instead of manually explaining your stack every time, this prompt produces a consistent, thorough architectural document that can onboard new developers or guide the agent during code generation.

---

### 3. Agents — Green Coding Optimizer

**File:** `.github/agents/green-coding-optimizer.agent.md`

Agents are **specialized personas** with a defined identity, expertise areas, and tool access. When invoked, the agent assumes its role and operates within its declared scope — it won't stray into unrelated tasks.

This repository includes a **Green Coding Optimizer** agent that:

- **Has explicit tool access** — `read`, `edit`, `search`, and Azure MCP search.
- **Covers six expertise areas:**
  1. Energy efficiency optimization (algorithm complexity, caching, batch processing)
  2. Carbon footprint reduction (SCI measurement, payload compression, geographic awareness)
  3. Code performance & efficiency (loop optimization, memory management, async patterns)
  4. Database & data access (N+1 detection, connection pooling, query optimization)
  5. Frontend optimization (bundle size, code splitting, rendering efficiency)
  6. Architecture & design patterns (microservices, event-driven, API design)
- **Tracks KPIs** — Software Carbon Intensity (SCI), energy per transaction, CPU utilization, memory efficiency.
- **Follows a structured process** — Profile → Understand Context → Prioritize → Recommend.
- **Has language-specific strategies** for Python, JavaScript, Java, Go, and C#/.NET.
- **Defines clear boundaries** — What it does and doesn't do, anti-patterns to flag, and success criteria.

**Why it matters:** A general-purpose agent doesn't know your sustainability goals. This agent carries deep domain expertise and a clear mandate, so every interaction is focused on measurable environmental impact.

---

### 4. Skills — Unit Testing Fundamentals

**File:** `.github/skills/unit-testing/SKILL.md`

Skills are **reference documents** the agent consults when a user asks about a specific topic. They provide deep, project-specific knowledge that goes beyond the agent's general training data.

This repository includes a **Unit Testing Fundamentals** skill that:

- **Is tailored to this project** — Covers testing for vanilla JavaScript (no frameworks or build tools).
- **Provides step-by-step setup** — npm initialization, Jest configuration, Babel setup, and `package.json` scripts.
- **Includes ready-to-use examples:**
  - Testing theme toggle functions
  - Mocking `localStorage`
  - Testing event listeners
  - Testing scroll behavior
- **Documents common assertions** — `toBe`, `toEqual`, `toContain`, `toThrow`, and more.
- **Covers best practices** — Refactoring for testability, mocking browser APIs, aiming for 80% coverage.
- **Includes troubleshooting** — Solutions for common issues like "document is not defined" and module resolution errors.

**Why it matters:** When you ask "how do I write tests for this project?", the agent doesn't give generic Jest advice — it gives advice calibrated to your vanilla JS setup, your file structure, and your existing code patterns.

---

### 5. MCP Servers — GitHub Integration

**File:** `.vscode/mcp.json`

MCP (Model Context Protocol) servers **extend the agent's capabilities** beyond reading and editing code. They connect the agent to external tools and APIs, enabling it to take real-world actions.

This repository configures:

- **GitHub MCP Server** — Connects to `https://api.githubcopilot.com/mcp/` via HTTP, giving the agent access to GitHub operations like creating issues, opening pull requests, searching code, managing branches, and more.

**Why it matters:** Without MCP, the agent can only suggest actions. With it, the agent can _execute_ — creating a branch, pushing code, and opening a PR as part of a single workflow.

---

## How They Work Together

These primitives don't exist in isolation — they compose into a coherent system:

1. **Instructions** ensure every interaction respects project conventions (vanilla JS, semicolons, single quotes).
2. **Skills** provide deep, on-demand knowledge (how to set up Jest for _this specific project_).
3. **Prompts** automate complex, repeatable tasks (generate a full technology blueprint with one command).
4. **Agents** bring focused expertise to specialized domains (optimize code for sustainability with measurable KPIs).
5. **MCP Servers** connect the agent to external systems (push optimized code directly to GitHub).

A typical workflow might look like:

```
Developer: "@green-coding-optimizer review my script.js for energy efficiency"

→ Agent loads its persona (agent primitive)
→ Reads project rules from instructions (instruction primitive)
→ Consults unit testing skill if tests are needed (skill primitive)
→ Creates a branch and PR via GitHub MCP (MCP primitive)
```

## Getting Started

1. **Clone this repository** and open it in VS Code with GitHub Copilot enabled.
2. **Explore the primitives** — Each file in `.github/` and `.vscode/` shapes how the agent behaves.
3. **Try invoking the agent** — Use `@green-coding-optimizer` in Copilot Chat.
4. **Run the prompt** — Open the command palette and run the Technology Stack Blueprint Generator.
5. **Ask about testing** — Ask Copilot how to write tests, and watch it reference the unit testing skill.

## Further Reading

- [Enhancing AI Workflows: Agentic Primitives & Context Engineering](https://blockchain.news/news/enhancing-ai-workflows-agentic-primitives-context-engineering)
- [GitHub Copilot Documentation — Customizing Copilot](https://docs.github.com/en/copilot/customizing-copilot)
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/)
