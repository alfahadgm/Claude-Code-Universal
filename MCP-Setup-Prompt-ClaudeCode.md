# Claude Code Full Stack Setup Prompt (v3.1 — Adaptive)

Copy everything below the line and paste it into Claude Code when you start a new or existing project. It auto-detects your project type, language, VCS provider, and infrastructure — then installs only what matches.

---

```
Set up this project with my full development stack — MCP servers, plugins, hooks, and CLAUDE.md rules. Auto-detect everything about this project and install only what's relevant. Do ALL of the following without asking me anything. Just execute silently and confirm when done.

---

## Step 0: Detect Environment

### 0a. Operating System & Tooling

```bash
OS=$(node -e "console.log(process.platform)" 2>/dev/null || echo "unknown")
echo "OS: $OS"
which gh 2>/dev/null && echo "gh: available" || echo "gh: not found"
which uvx 2>/dev/null && echo "uvx: available" || echo "uvx: not found"
which docker 2>/dev/null && echo "docker: available" || echo "docker: not found"
which pip 2>/dev/null && echo "pip: available" || echo "pip: not found"
which cargo 2>/dev/null && echo "cargo: available" || echo "cargo: not found"
which go 2>/dev/null && echo "go: available" || echo "go: not found"
which dotnet 2>/dev/null && echo "dotnet: available" || echo "dotnet: not found"
which flutter 2>/dev/null && echo "flutter: available" || echo "flutter: not found"
which swift 2>/dev/null && echo "swift: available" || echo "swift: not found"
```

Store the OS result. Use OS-specific commands throughout:

| OS | npx MCP format | Notes |
|----|---------------|-------|
| **macOS / Linux** | `claude mcp add <n> --scope user -- npx -y <pkg@version>` | Standard |
| **Windows (not WSL)** | `claude mcp add-json <n> '{"command":"cmd","args":["/c","npx","-y","<pkg@version>"]}'  --scope user` | MUST wrap with `cmd /c` or connections silently fail |
| **WSL2** | Same as Linux | Use mirrored networking in `.wslconfig` if MCP connections drop |

### 0b. Detect Project Type

Scan the project root and set detection flags. Run ALL of these checks:

```bash
echo "=== PROJECT DETECTION ==="

# --- Languages & Frameworks ---
[ -f "package.json" ] && echo "DETECTED: node" && cat package.json | head -80
[ -f "tsconfig.json" ] && echo "DETECTED: typescript"
[ -f "next.config.js" ] || [ -f "next.config.mjs" ] || [ -f "next.config.ts" ] && echo "DETECTED: nextjs"
[ -f "nuxt.config.ts" ] || [ -f "nuxt.config.js" ] && echo "DETECTED: nuxt"
[ -f "svelte.config.js" ] && echo "DETECTED: svelte"
[ -f "angular.json" ] && echo "DETECTED: angular"
[ -f "vite.config.ts" ] || [ -f "vite.config.js" ] && echo "DETECTED: vite"
[ -f "requirements.txt" ] || [ -f "pyproject.toml" ] || [ -f "setup.py" ] || [ -f "Pipfile" ] && echo "DETECTED: python"
[ -f "pyproject.toml" ] && grep -q "django" pyproject.toml 2>/dev/null && echo "DETECTED: django"
[ -f "requirements.txt" ] && grep -qi "flask\|fastapi\|django" requirements.txt 2>/dev/null && echo "DETECTED: python-web"
[ -f "Cargo.toml" ] && echo "DETECTED: rust"
[ -f "go.mod" ] && echo "DETECTED: go"
[ -f "*.csproj" ] 2>/dev/null || [ -f "*.sln" ] 2>/dev/null && echo "DETECTED: csharp"
[ -f "build.gradle" ] || [ -f "build.gradle.kts" ] || [ -f "pom.xml" ] && echo "DETECTED: java"
[ -f "pubspec.yaml" ] && echo "DETECTED: flutter"
[ -f "Package.swift" ] && echo "DETECTED: swift"
[ -f "Gemfile" ] && echo "DETECTED: ruby"
[ -f "mix.exs" ] && echo "DETECTED: elixir"
[ -f "composer.json" ] && echo "DETECTED: php"
[ -f "build.zig" ] && echo "DETECTED: zig"
[ -f "deno.json" ] || [ -f "deno.jsonc" ] && echo "DETECTED: deno"
[ -f "bun.lockb" ] && echo "DETECTED: bun"

# --- Infrastructure ---
[ -f "Dockerfile" ] || [ -f "docker-compose.yml" ] || [ -f "docker-compose.yaml" ] && echo "DETECTED: docker"
[ -f "terraform.tf" ] || [ -d ".terraform" ] || ls *.tf 2>/dev/null | head -1 && echo "DETECTED: terraform"
[ -f "pulumi.yaml" ] && echo "DETECTED: pulumi"
[ -d "k8s" ] || [ -f "skaffold.yaml" ] || ls **/kustomization.yaml 2>/dev/null | head -1 && echo "DETECTED: kubernetes"

# --- Database ---
[ -d "supabase" ] || [ -f "supabase/config.toml" ] && echo "DETECTED: supabase"
[ -d "prisma" ] || [ -f "prisma/schema.prisma" ] && echo "DETECTED: prisma"
[ -f "drizzle.config.ts" ] || [ -f "drizzle.config.js" ] && echo "DETECTED: drizzle"
grep -rli "postgres\|postgresql\|pg_" .env* 2>/dev/null | head -1 && echo "DETECTED: postgresql"
grep -rli "mysql\|mariadb" .env* 2>/dev/null | head -1 && echo "DETECTED: mysql"
grep -rli "mongodb\|mongo_uri" .env* 2>/dev/null | head -1 && echo "DETECTED: mongodb"
[ -f "*.db" ] 2>/dev/null || [ -f "*.sqlite" ] 2>/dev/null && echo "DETECTED: sqlite"

# --- Testing ---
[ -f "playwright.config.ts" ] || [ -f "playwright.config.js" ] && echo "DETECTED: playwright"
[ -f "cypress.config.ts" ] || [ -f "cypress.config.js" ] && echo "DETECTED: cypress"
[ -f "jest.config.ts" ] || [ -f "jest.config.js" ] && echo "DETECTED: jest"
[ -f "vitest.config.ts" ] && echo "DETECTED: vitest"
[ -f "pytest.ini" ] || [ -f "conftest.py" ] && echo "DETECTED: pytest"

# --- VCS Provider ---
git remote -v 2>/dev/null | head -2
git remote -v 2>/dev/null | grep -qi "github.com" && echo "DETECTED: github"
git remote -v 2>/dev/null | grep -qi "gitlab" && echo "DETECTED: gitlab"
git remote -v 2>/dev/null | grep -qi "bitbucket" && echo "DETECTED: bitbucket"
git remote -v 2>/dev/null | grep -qi "dev.azure.com\|visualstudio.com" && echo "DETECTED: azure-devops"

# --- Monorepo ---
[ -f "lerna.json" ] || [ -f "pnpm-workspace.yaml" ] || [ -f "nx.json" ] || [ -f "turbo.json" ] && echo "DETECTED: monorepo"
ls */package.json 2>/dev/null | wc -l | xargs -I{} [ {} -gt 2 ] 2>/dev/null && echo "DETECTED: monorepo"

# --- Data Science / ML ---
[ -f "*.ipynb" ] 2>/dev/null || ls **/*.ipynb 2>/dev/null | head -1 && echo "DETECTED: jupyter"
grep -rli "torch\|tensorflow\|keras\|sklearn\|pandas" requirements.txt pyproject.toml 2>/dev/null | head -1 && echo "DETECTED: ml"

echo "=== END DETECTION ==="
```

Store ALL detected flags. They drive every decision below.

---

## Step 1: Install MCP Servers (conditional, skip any already installed)

Check existing servers:
```bash
claude mcp list
```

**Always pin package versions. Only install what the project needs.**

### 1a. VCS Server (pick ONE based on detected VCS)

**If DETECTED: github** — Install GitHub MCP (remote HTTP, cross-platform):
```bash
claude mcp add github \
  --scope user \
  --transport http \
  --url https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer $(gh auth token)"
```
> Requires `gh` CLI authenticated. Use minimal PAT scopes (repo, read:org) if `gh` unavailable.

**If DETECTED: gitlab** — No equivalent MCP server yet. Note in report. Use Claude Code's built-in GitLab CI/CD integration instead (`claude` in pipelines with `--permission-mode acceptEdits`).

**If DETECTED: bitbucket or azure-devops** — No MCP server available. Note in report. Use shell commands (`git`, API calls) as fallback.

### 1b. Sequential Thinking (ALWAYS install — language agnostic)

**macOS / Linux:**
```bash
claude mcp add sequential-thinking --scope user -- npx -y @modelcontextprotocol/server-sequential-thinking@0.6.4
```
**Windows (native):**
```bash
claude mcp add-json sequential-thinking "{\"command\":\"cmd\",\"args\":[\"/c\",\"npx\",\"-y\",\"@modelcontextprotocol/server-sequential-thinking@0.6.4\"]}" --scope user
```

### 1c. Playwright MCP (conditional)

**Install ONLY if** any of: `DETECTED: playwright`, `DETECTED: nextjs`, `DETECTED: nuxt`, `DETECTED: svelte`, `DETECTED: angular`, `DETECTED: vite`, or the project has a web frontend (check for `src/pages/`, `src/app/`, `public/index.html`, or web framework deps in package.json).

**macOS / Linux:**
```bash
claude mcp add playwright --scope user -- npx -y @playwright/mcp@0.0.30
```
**Windows (native):**
```bash
claude mcp add-json playwright "{\"command\":\"cmd\",\"args\":[\"/c\",\"npx\",\"-y\",\"@playwright/mcp@0.0.30\"]}" --scope user
```

**Skip if**: pure backend, CLI tool, library, mobile, embedded, data science, or infrastructure project.

### 1d. Database MCP (conditional — pick ONE based on detection)

**If DETECTED: supabase:**
```bash
claude mcp add supabase --scope local -- npx -y supabase@latest -- mcp
```
> Uses OAuth — no PAT needed. Scoped to project (local) since DB config is project-specific.

**If DETECTED: postgresql OR mysql OR sqlite** (and NOT supabase):

**macOS / Linux:**
```bash
claude mcp add dbhub --scope local -- npx -y @bytebase/dbhub@latest
```
**Windows (native):**
```bash
claude mcp add-json dbhub "{\"command\":\"cmd\",\"args\":[\"/c\",\"npx\",\"-y\",\"@bytebase/dbhub@latest\"]}" --scope local
```
> DBHub provides a unified gateway for PostgreSQL, MySQL, SQLite, and DuckDB. Connection string configured per-project.

**If no database detected** — skip entirely.

### 1e. Docker MCP Toolkit (conditional)

**Install ONLY if** `DETECTED: docker` AND Docker Desktop v4.48+ is available:
```bash
claude mcp add docker -- docker run --rm -i --pull=always ghcr.io/docker/mcp-toolkit
```
> Provides 300+ containerized MCP servers with credential management. Strongest cross-platform consistency option.

**Skip if**: Docker not detected or Docker Desktop version is too old.

### Servers deliberately NOT installed

| Server | Reason |
|--------|--------|
| `@modelcontextprotocol/server-memory` | Built-in Auto Memory (v2.1.59+) is native and sufficient |
| `@modelcontextprotocol/server-fetch` | Claude Code has built-in web fetching |
| `@modelcontextprotocol/server-filesystem` | Claude Code already has file access |
| Context7 MCP | Installed as plugin (adds auto-trigger + agent + `/context7:docs`) |
| Serena MCP | Native LSP plugins cover navigation for 10 languages |

> **Context window budget**: Each MCP server consumes context tokens for tool descriptions. Target 2-4 servers max. MCP Tool Search auto-activates when descriptions exceed 10% of context.

---

## Step 2: Install Plugins (conditional, skip any already installed)

Check currently installed plugins first.

### 2a. Core workflow plugins (ALWAYS install — language agnostic)

```
/plugin install feature-dev@claude-plugins-official
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
/plugin marketplace add upstash/context7
/plugin install context7-plugin@context7-marketplace
/plugin install code-simplifier
/plugin install pr-review-toolkit@claude-plugins-official
/plugin install security-guidance@claude-plugins-official
```

### 2b. LSP Plugin (install based on detected language — max 2 for polyglot)

| Detected | Plugin | Install Command |
|----------|--------|----------------|
| `typescript` or `node` (with TS) | TypeScript LSP | `/plugin install lsp-typescript@claude-plugins-official` |
| `node` (JS only, no TS) | TypeScript LSP | `/plugin install lsp-typescript@claude-plugins-official` (handles JS too) |
| `python` | Pyright LSP | `/plugin install lsp-pyright@claude-plugins-official` |
| `rust` | rust-analyzer | `/plugin install lsp-rust-analyzer@claude-plugins-official` |
| `go` | gopls | `/plugin install lsp-gopls@claude-plugins-official` |
| `csharp` | C# LSP | `/plugin install lsp-csharp-ls@claude-plugins-official` |
| `java` | jdtls | `/plugin install lsp-jdtls@claude-plugins-official` |
| `swift` | SourceKit LSP | `/plugin install lsp-sourcekit-lsp@claude-plugins-official` |
| `php` | phpactor | `/plugin install lsp-phpactor@claude-plugins-official` |
| `lua` | Lua LS | `/plugin install lsp-lua-language-server@claude-plugins-official` |

**Languages without official LSP plugins** (use grep-based fallback):
Ruby, Elixir, Kotlin, Dart/Flutter, Scala, Haskell, Zig, Deno, Bun

> If the project is polyglot (e.g., TypeScript frontend + Python backend), install up to 2 LSP plugins. Beyond 2, context bloat outweighs benefit.

### 2c. Conditional plugins

```
# Only if not already present:
/plugin install skill-creator
```

---

## Step 3: Configure Security Hooks

Create or merge into `.claude/settings.local.json` (gitignored):

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "command": "bash -c 'TOOL_INPUT=\"$CLAUDE_TOOL_INPUT\"; if echo \"$TOOL_INPUT\" | grep -qiE \"(curl|wget).*\\|.*(sh|bash)|rm\\s+-rf\\s+/|> /etc/|chmod\\s+777\"; then echo \"BLOCKED: Dangerous command pattern detected\" >&2; exit 2; fi'"
      }
    ]
  },
  "deny": [
    "Bash(cat ~/.ssh/*)",
    "Bash(cat ~/.aws/*)",
    "Bash(cat .env*)",
    "Bash(echo $*KEY*)",
    "Bash(echo $*SECRET*)",
    "Bash(echo $*TOKEN*)",
    "Bash(echo $*PASSWORD*)"
  ]
}
```

> Hooks are deterministic — exit code 2 hard-blocks actions. CLAUDE.md rules are advisory. Use hooks for security; use CLAUDE.md for judgment.

---

## Step 4: Create or Update CLAUDE.md

**Target: under 200 lines total** (including project-specific rules the user adds later). Anthropic's guidance: longer files cause uniform instruction degradation.

If `CLAUDE.md` exists, append after a `---` separator. Otherwise create it.

**Generate the CLAUDE.md dynamically based on what was detected and installed.** Use this template but ONLY include sections for tools that were actually installed:

```markdown
# Project Configuration

<!-- ⚠️ DO NOT DELETE. Append or edit — never remove unless explicitly asked. -->

## Project Profile

- **Type**: [auto-fill: web app / API / CLI / library / mobile / data science / infrastructure / monorepo]
- **Languages**: [auto-fill from detection]
- **Framework**: [auto-fill if detected]
- **Database**: [auto-fill if detected, or "none"]
- **VCS**: [auto-fill: GitHub / GitLab / Bitbucket / Azure DevOps]
- **Test runner**: [auto-fill: jest / vitest / pytest / cargo test / go test / etc.]
- **Build command**: [leave blank — ask user or detect from package.json scripts]
- **Lint command**: [leave blank — ask user or detect from package.json scripts]

## Tool Usage Rules

### Context7 — ALWAYS before writing library code
- Before generating code that imports ANY library, fetch live docs via Context7
- Use `/context7:docs` for manual lookups
- Never rely on training data for API signatures or config options

### Sequential Thinking — for architectural decisions
- Use for system design, trade-off evaluation, complex debugging
- Skip for straightforward single-file changes

### LSP — for all code navigation
- Use go-to-definition, find-all-references, hover for type info
- Prefer LSP over grep for symbol navigation
- Falls back to grep for non-code files or if LSP is unavailable

[ONLY if GitHub MCP installed:]
### GitHub MCP — for PR/issue workflows
- Use for creating PRs, reviewing diffs, searching code, managing issues
- Prefer over shell `gh` commands for multi-step operations

[ONLY if Playwright MCP installed:]
### Playwright — for E2E testing
- Write specs first, review plans, then execute
- Use accessibility tree queries over CSS selectors

[ONLY if database MCP installed:]
### Database — for schema and query work
- Use MCP for schema inspection, query testing, migrations review
- Never run destructive operations (DROP, TRUNCATE) without explicit user confirmation
- Always use read-only mode for exploration

[ONLY if Docker MCP installed:]
### Docker — for container workflows
- Use for building, running, inspecting containers
- Prefer MCP over raw docker CLI for multi-step operations

## Workflow Order (non-trivial work)

feature-dev → Brainstorm → Plan → Implement (TDD) → Simplify → Review → Commit

1. **feature-dev**: 7-phase guided workflow for new features
2. **superpowers**: Brainstorm before coding, TDD red-green-refactor
3. **code-simplifier**: After implementation, before review
4. **pr-review-toolkit**: After simplification, before merge
5. **Pre-commit checks**: Final gate (see below)

Skip full workflow for trivial changes (typos, config tweaks, single-line edits).

## Pre-Commit Checklist

When user says "commit", "push", "create PR", or "ship it":
- Check for: debug statements, console.log, hardcoded secrets, .env files staged
- Run tests: [auto-fill test command or ask user]
- Run linting: [auto-fill lint command or ask user]
- Check for unintended file changes or large binaries
- Block and warn — do NOT auto-fix

## Memory

- Built-in Auto Memory handles persistence — no external server needed
- Store architectural decisions and corrections when significant
- Review memory at session start; summarize at session end
- Never store secrets, API keys, or credentials

## Coding Principles

- YAGNI — don't build what isn't needed
- Evidence over assumptions — verify before claiming
- Tests must fail before implementation (TDD)
- Prefer semantic navigation (LSP) over text search (grep)
- Check library docs (Context7) before writing integration code
```

[ONLY if DETECTED: monorepo — add this section:]
```markdown

## Monorepo Rules

This is a monorepo. Use path-scoped rules:
- Root CLAUDE.md: shared conventions only
- Create `.claude/rules/` with per-package rules if needed
- Each subdirectory can have its own CLAUDE.md for package-specific context
- When working in a subdirectory, respect its local rules over root rules
```

**Remove any `[ONLY if ...]` markers — they are instructions to you, not content for the file.** Only include sections where the corresponding tool was actually installed. The final file should read cleanly with no conditional markers.

---

## Step 5: Initialize Auto Memory

```bash
mkdir -p ~/.claude/projects/$(basename $(pwd))/memory 2>/dev/null
echo "Auto Memory directory ready. Will populate as Claude works on this project."
```

---

## Step 6: Detect and Report Build/Test/Lint Commands

Attempt to auto-detect the project's key commands and backfill into CLAUDE.md:

```bash
# Node projects
[ -f "package.json" ] && echo "Scripts found:" && node -e "const p=require('./package.json'); console.log(Object.keys(p.scripts||{}).join(', '))"

# Python projects
[ -f "pyproject.toml" ] && grep -A5 "\[tool.pytest" pyproject.toml 2>/dev/null
[ -f "Makefile" ] && echo "Makefile targets:" && grep -E "^[a-zA-Z_-]+:" Makefile | head -10

# Rust
[ -f "Cargo.toml" ] && echo "Rust: cargo test, cargo clippy, cargo build"

# Go
[ -f "go.mod" ] && echo "Go: go test ./..., go vet ./..., go build ./..."
```

If build/test/lint commands are detected, update the CLAUDE.md Pre-Commit Checklist with the actual commands. If not detected, leave placeholders and note in the report that the user should fill them in.

---

## Step 7: Verify and Report

After completing all steps:

1. Run `claude mcp list` — list all installed servers
2. List installed plugins
3. Confirm `.claude/settings.local.json` exists with deny rules and hook
4. Confirm `CLAUDE.md` exists and reflects the detected project type
5. Report any failures or missing tools

**Output a summary in this exact format:**

### Detection Results
```
OS: [detected]
Project type: [web app / API / CLI / library / etc.]
Languages: [detected list]
Framework: [if any]
Database: [if any]
VCS: [GitHub / GitLab / etc.]
Monorepo: [yes/no]
```

### Installation Summary

| Component | Status | Scope | Why |
|-----------|--------|-------|-----|
| GitHub MCP | ✅ / ❌ / ⏭️ Skipped | user/local | [reason] |
| Sequential Thinking | ✅ / ❌ | user | Always installed |
| Playwright MCP | ✅ / ⏭️ Skipped | user | [web project / not web] |
| Database MCP | ✅ / ⏭️ Skipped | local | [which DB / none detected] |
| Docker MCP | ✅ / ⏭️ Skipped | user | [Docker found / not found] |
| feature-dev plugin | ✅ / ❌ | — | Core workflow |
| superpowers plugin | ✅ / ❌ | — | Core workflow |
| context7 plugin | ✅ / ❌ | — | Core workflow |
| code-simplifier | ✅ / ❌ | — | Core workflow |
| pr-review-toolkit | ✅ / ❌ | — | Core workflow |
| security-guidance | ✅ / ❌ | — | Security hook |
| LSP: [language] | ✅ / ⏭️ N/A | — | [matched / no plugin available] |
| Security hooks | ✅ / ❌ | — | .claude/settings.local.json |
| CLAUDE.md | ✅ / ❌ | — | [created / updated] |

### Manual Steps Required
- [ ] [List anything the user needs to do: authenticate gh, install uv, fill in build commands, etc.]

### Languages Without LSP Support
- [ ] [List any detected languages that have no official LSP plugin, with note that grep fallback will be used]

Do not ask me questions. Just do it all and confirm.
```
