---
title: "Module 1: Setup, Skills & First Session"
weight: 2
updated: "April 2026"
sidebar:
  open: true
toc: true
---

**Estimated time: ~3 hours**

Welcome to the hands-on part of the guide. Module 0 laid the conceptual foundation, and now it is time to put that knowledge into practice. By the end of this module you will have a working agent connected to your codebase, you will have run your first real task through it, and you will understand enough about the setup to start using it in your daily work.

{{< callout type="info" >}}
**What you will learn in this module:**

- How to choose the right {{< tooltip "harness" "The environment the agent runs inside, like Claude Code, Cursor, or Aider. It provides file access, terminal, and UI." >}} and model combination for your team
- How subscriptions, API keys, and free tiers work in practice
- How {{< tooltip "agent modes" "The level of autonomy you give the agent for a session. Plan proposes first, ask approves each step, execute acts continuously." >}} (plan, ask, execute) work across harnesses and when to use each
- How to assess whether your codebase is ready for an agent
- How to write your first {{< tooltip "AGENTS.md" "A markdown rules file the agent reads at the start of every session, containing your project's build commands, conventions, and patterns." >}}, install community {{< tooltip "skills" "Reusable, task-scoped instruction sets (SKILL.md files) that the agent loads automatically when a task matches the trigger." >}}, and set up codebase context
- How to run a real task end to end and turn the experience into lasting team knowledge
{{< /callout >}}

---

## Choosing Your Setup

The first step is deciding which tools you will use and how you will pay for them. This section walks you through four questions that narrow the field, and the rest of the module builds directly on the answers you arrive at.

There is no single correct setup. What works best depends on how your team already works, what your codebase looks like, and what you are trying to accomplish. Think of your first choice as an experiment rather than a permanent decision, because you can always change it later.

- **Terminal or IDE?** Do you spend most of your time in a terminal or inside a code editor like VS Code or JetBrains? Module 0 introduced {{< tooltip "harnesses" "The environment the agent runs inside, like Claude Code, Cursor, or Aider. It provides file access, terminal, and UI." >}} as the environment your agent runs inside. Here are the main categories as of April 2026:

| Category | Examples | What it means for your workflow |
|---|---|---|
| Terminal native | Claude Code, Aider, Codex CLI, Gemini CLI, OpenCode, Warp | Full filesystem and shell access; fits naturally into scripts and CI pipelines |
| AI native IDEs | Cursor, Windsurf, Zed, Antigravity | Full editors rebuilt around AI, with inline diffs and agent modes built into the experience |
| IDE extensions | Cline, Roo Code, GitHub Copilot, Kilo Code, Continue | Adds agentic capabilities to your existing editor without switching tools |
| Cloud and autonomous | Devin, OpenHands, Manus, Amp | Runs in its own remote environment; you assign tasks and review output asynchronously |

  Choosing a category matters more than choosing a specific tool within it, because every tool in a category works similarly and you can always switch later. If you are not sure where to start, pick based on where you already spend your time.

- **Subscription or API key?** A subscription gives you a fixed monthly bill and a usage budget the harness manages for you, which is simpler to start with and often provides better value per token than raw API access. An API key gives you direct access where you pay only for what you use, which is more flexible and has no hard rate limits but requires more setup and cost discipline. Both paths are covered in detail in the next section.

- **Solo or team?** If you are an individual developer, you can configure freely using your own credentials. If you are part of a team, each developer needs to authenticate independently. Most provider subscriptions are individual plans, and sharing credentials across multiple developers is against terms of service. Some providers have been tolerant of minor violations while others have strictly enforced their terms with account suspensions.

- **Hosted model or open weight?** Most developers start with a {{< tooltip "hosted model" "A model that runs on the provider's servers, accessed via API. Examples include Claude, GPT, Gemini, DeepSeek, and Qwen." >}} where the model runs on the provider's servers and you pay per request. Providers include Anthropic (Claude), OpenAI (GPT), Google (Gemini), DeepSeek, Alibaba (Qwen), Moonshot (Kimi), and Zhipu (GLM). {{< tooltip "Open weight models" "Models whose trained weights are publicly released, allowing self-hosting or access through third-party APIs at lower cost." >}} are publicly available model files you can run yourself on your own machine or a server you control, so your prompts and code never leave your infrastructure. If cost, privacy, or data residency matters to your team, the open weight path is worth understanding before you commit.

Take a few minutes with these questions before continuing. You do not need to have perfect answers. The goal is to have a starting direction so the sections that follow make sense in context.

### The Combined Setup

Most experienced developers in 2026 use two tools together, running an IDE harness for day to day coding and a terminal agent for tasks that need deeper autonomous work.

An IDE harness keeps you in your editor where you write code, the agent suggests completions, helps with inline refactors, and answers questions about the codebase. The interaction is fast, immediate, and stays in your flow.

A terminal agent works differently. You give it a well scoped task, it reads your codebase, forms a plan, makes changes across multiple files, runs the tests, and iterates until the task is done or it needs your input. You do not watch every step. You let it run and review the result.

For developers who prefer visual interfaces but want terminal agent capabilities, there are hybrid options. Warp is a modern terminal with a built in file editor, syntax highlighting, and GUI elements like grouped command outputs. Cursor and Windsurf embed terminal panels directly in the editor, letting you run terminal agents without leaving your IDE. Claude Code also offers a VS Code extension that brings terminal agent workflows into a visual environment. The boundaries between categories are becoming more fluid.

It is worth noting that both terminal agents and IDE harnesses can now run tasks continuously with minimal supervision. The practical difference is less about autonomy and more about interface preference. Terminal agents hide the code while they work, and you review the final diff. IDE harnesses show you the code changing in real time, which some developers find reassuring and others find distracting.

```
New feature ticket
    │
    ├─ Small, contained, you want to watch changes in real time
    │       └─ IDE harness → inline help, quick edits, fast back and forth
    │
    └─ Multi file, repetitive, comfortable reviewing a final diff
            └─ Terminal agent → scope it well, let it run, review the diff
```

---

## How Access and Pricing Works

### Subscriptions

A subscription is the simplest entry point. You pay a flat monthly fee, authenticate once, and the harness manages usage internally. The agent uses the model tier associated with your plan and returns an error when you have hit your limit for the period. Subscriptions typically provide better value per token than raw API access, making them economical for regular use.

The important thing to understand is that subscription limits use {{< tooltip "rolling windows" "A rate limit mechanism where capacity resets continuously based on usage over a moving time period, not on a fixed calendar date." >}}. Your available capacity at any moment depends on what you consumed over the past several hours or days. A heavy morning session can leave you throttled by early afternoon with no obvious explanation, and when developers say the agent stopped working and they do not know why, this is almost always what happened.

{{< callout type="info" >}}
**What a rolling window looks like in practice:** Suppose your plan allows 500 requests over a 5-hour window. You burn through 400 requests between 9am and 11am on a deep refactor. At 11:30am you try to start a new task and get rate limited, even though you have only used your subscription for two and a half hours today. Your capacity will not fully recover until 2pm, when those early morning requests start falling off the trailing edge of the window. The fix is to spread heavy sessions across the day or switch to an API key for burst work.
{{< /callout >}}

Subscriptions are also **individual plans** intended for one person. Sharing an account across multiple developers, or routing your credentials through a tool the provider did not build, violates the terms of service. Some providers have been tolerant while others have strictly enforced their policies with account suspensions since early 2026.

### API Keys

An API key is a credential string, something like `sk-ant-api03-...`, that you paste into your harness's configuration file. When the agent sends a request to the model provider, it sends this key, the provider verifies it, and charges your account for the tokens consumed. There is no monthly fee, no rolling window, and no sharing restriction because each developer uses their own key.

The tradeoff with API keys is cost predictability. A subscription caps your maximum monthly spend, but an API key does not. A long session using a frontier reasoning model on a large codebase can easily cost more than a month of subscription access.

{{< callout type="warning" >}}
**Before running your first session with an API key, set a monthly spend cap in the provider's billing settings.** Every major provider offers this and it takes two minutes. Without it, a single unattended overnight session can produce a bill that is genuinely uncomfortable.
{{< /callout >}}

{{< callout type="info" >}}
**Start conservative.** You can always raise the cap after your first week once you understand your actual usage patterns. A $20 to $50 monthly cap is reasonable for learning; production workloads vary widely.
{{< /callout >}}

### Open Weight Models

Every hosted model (Claude, GPT, Gemini, DeepSeek, Qwen) runs on the provider's infrastructure and you pay per request. Open weight models work differently. The model files are publicly released, similar to how open source software is released, so anyone can run them and your prompts and code stay on your own infrastructure.

Three practical reasons make this worth understanding.

**Cost.** Open weight models accessed through aggregator APIs like OpenRouter cost a fraction of hosted frontier models. Capable coding models are available at $0.14 to $0.30 per million tokens, compared to $3 to $25 for Claude or GPT.

**Privacy.** If your codebase contains sensitive business logic, regulated data, or IP you are not comfortable sending to a third party server, running a model locally or in your own cloud removes that concern entirely.

**Regional cost difference.** For developers in countries where USD API pricing is significant relative to local salaries, open weight models accessed through local providers or shared infrastructure change the economics meaningfully.

Any harness that supports a custom API base URL can route to an open weight model, and most harnesses do, including Aider, OpenCode, Cline, Kilo Code, and Zed. The tradeoff is that the best open weight models today are generally somewhat less capable than Claude Opus or GPT 5.4 on complex multi step reasoning tasks, though the gap has been narrowing rapidly throughout 2026. 

Current examples worth knowing include DeepSeek V3.2, Qwen 3.5, Kimi K2.5, Llama 4 Code, Qwen3 Coder Next for fast local inference on modest hardware, and Gemma 4 (released April 2026 under Apache 2.0 license with models ranging from 2B edge to 31B dense, specifically optimized for agentic workflows).

### Free Tiers

Several harnesses and providers offer free access that is genuinely usable rather than just a trial. These are worth knowing before you commit to anything paid.

- **Gemini CLI** offers 1,000 free requests per day using Gemini models, with no credit card required. This is the most generous free tier for a capable terminal agent as of April 2026.
- **GitHub Copilot Free** gives 2,000 code completions and 50 agent mode requests per month at no cost.
- **Antigravity** (Google's agent IDE) is in free public preview with access to Gemini 3 Pro, Claude Sonnet 4.6, and Claude Opus 4.6, rate limited but usable for learning and prototyping.
- **OpenRouter free tier** provides access to 29+ models at zero cost, including Qwen3 Coder 480B, Llama 3.3 70B, and GPT OSS 120B, at 200 requests per day. Any harness that accepts a custom API base URL can use it.
- **Aider, OpenCode, Cline, and Goose** are open source and free to run. You pay only for the model API calls. Combined with OpenRouter's free tier, these tools cost nothing to start.

Free tiers are enough to complete this module's exercises. The limits will feel tight once you start doing real daily work, and that is when it makes sense to move to a paid plan.

{{< callout type="warning" >}}
**Free tier details change frequently.** The information above is based on what was available as of April 2026. Check each provider's current pricing page before making decisions, as limits, model availability, and terms can change without notice.
{{< /callout >}}

---

## Agent Modes: Plan, Ask, and Execute

Every harness gives the agent a way to operate at different levels of autonomy. **Choosing the right mode before each session is one of the most important habits to build in the first week.** The wrong mode will either slow you down unnecessarily or allow the agent to make changes you did not anticipate.

### Plan

In plan mode, the agent explores the codebase and writes a detailed proposal **before touching any file**. It reads files, searches for patterns, asks clarifying questions when it needs to, and produces a structured plan showing which files it would change, what changes it would make in each, and why. You review this plan, adjust scope if anything looks wrong, and only then approve execution.

Use plan mode for any task that touches more than two or three files, for unfamiliar parts of the codebase, for anything that is hard to reverse like database migrations or infrastructure changes, and whenever you want to understand the agent's approach before it acts.

{{< callout type="warning" >}}
**The plan then new session pattern** runs counter to instinct but consistently outperforms trying to plan and execute in the same long conversation. Write a plan in plan mode, then open a completely fresh session and paste the plan as the opening prompt to execute. A clean context at execution time removes all the planning back and forth and lets the agent focus entirely on doing the work.
{{< /callout >}}

### Ask

In ask mode, the agent prompts you to approve each significant action before it runs, including file edits, terminal commands, and network requests. You have complete visibility into every step. Use it when you are learning how an agent works, or when you are in a sensitive area of the codebase where an unreviewed change would be costly.

### Execute

Execute mode lets the agent act without pausing at each step. How safe this is depends on which harness you use. Some implement a **classifier** that reviews each action before it runs and blocks anything out of scope, giving you automation speed without removing oversight entirely. Others offer a **full bypass** where the agent has unrestricted access. Full bypass exists for automated environments like Docker containers and CI pipelines and is not intended for everyday development sessions.

{{< callout type="warning" >}}
**Do not use full bypass mode as a daily default.** Teams that tried this early on produced documented incidents before understanding the risk. An agent with no permission checks in a directory that also contains cloud credentials or production database access can cause significant damage from a single misunderstood prompt. Keep bypass mode in isolated automated environments, not in your working directory.
{{< /callout >}}

```
You give a task
       │
       ▼
┌─────────────────────────────────────────────────────────┐
│                    Which mode?                          │
└─────────────────────────────────────────────────────────┘
       │                 │                    │
       ▼                 ▼                    ▼
  PLAN MODE           ASK MODE          EXECUTE MODE
  ─────────           ────────          ────────────
  Agent reads +       Agent acts,       Agent acts
  proposes plan       pauses for        continuously
       │              approval each     (with classifier
  You review          step              or full bypass)
       │
  You approve
       │
  Execution runs
```

The table below shows what each harness calls these modes, since the terminology varies even when the concept is identical.

| Harness | Plan mode | Ask mode | Execute mode | Full bypass |
|---|---|---|---|---|
| Claude Code | `--permission-mode plan` | Default | `auto` (Team and above) | `bypassPermissions` |
| Aider | `--ask` flag | Default | `--yes` flag | `--yes` flag |
| Cursor | Prompt phrasing | Default | YOLO mode | YOLO mode |
| Antigravity | Planning mode | Default | Turbo mode | Turbo mode |
| Kiro | Spec first by default | After spec approval | Executes after approval | Configurable |
| OpenCode | `/plan` command | Default | `--auto` flag | `--auto` flag |

### Configuring What the Agent Can Do Without Asking

Beyond the three main modes, most harnesses let you preconfigure which specific actions the agent can take automatically and which ones require your approval. This is more granular than the mode setting and follows a similar pattern across tools: you define an allowlist of safe operations, an optional denylist of blocked operations, and everything else prompts for confirmation.

**Cursor** uses Settings → Features → Agent to configure allowlists and denylists. Commands matching the allowlist run automatically in YOLO mode.

```json
{
  "allowedCommands": [
    "npm run test",
    "npm run lint",
    "npx tsc --noEmit"
  ]
}
```

**Claude Code** uses `.claude/settings.json` with allow, ask, and deny categories.

```json
{
  "permissions": {
    "allow": ["Read", "Glob", "Grep"],
    "ask": ["Bash(npm install:*)", "Bash(git push:*)"],
    "deny": ["Bash(rm -rf:*)"]
  }
}
```

**Aider** uses command line flags rather than configuration files. The `--yes` flag enables auto approval for most operations, with `--no-auto-commits` to prevent automatic git commits.

**Antigravity** provides three permission tiers in settings. Off requires explicit approval for everything. Auto uses an internal safety model to judge command risk. Turbo auto approves most operations.

Setting this up before your first real session prevents the most common interruptions like the agent asking to run lint on a single file while keeping genuinely risky actions under your review.

{{< callout type="info" >}}
**A good starting permission set for most projects:** Allow read-only operations (file reads, searches, glob) automatically. Allow your test and lint commands automatically. Require approval for anything that installs packages, writes to files outside the project, or runs destructive commands. Deny `rm -rf`, `git push --force`, and any command that touches production infrastructure. You can loosen these as you build trust with your specific setup.
{{< /callout >}}

---

## Codebase Readiness

### Agent Ready Is Different from Human Readable

Before connecting an agent to your codebase, there is a mental shift worth making. Making a codebase **agent ready** is not the same as making it **human readable**, even though the two overlap significantly.

A human developer compensates for gaps automatically. If the build process requires Slack messages and tribal knowledge, a new team member will ask and get helped. An agent cannot adapt in these ways and relies entirely on what is machine verifiable and explicitly written down. If the build command is not documented, it guesses and its guesses are often wrong. If tests are slow or flaky, the agent's feedback loop becomes slow and expensive.

{{< callout type="warning" >}}
A codebase scoring nine out of ten for human developer experience can score two out of ten for agent performance. A beautifully architected system with no test suite, no documented setup steps, and no linter is excellent for an experienced engineer and nearly unusable for an agent.
{{< /callout >}}

### What Agents Need That Humans Take for Granted

Here is a concrete comparison of two patterns that affect agent performance differently.

**Agent friendly**
```
my-api/
├── AGENTS.md                ← first thing the agent reads
├── .env.example             ← documents every required variable
├── package.json             ← npm test, npm run lint, npm run build
├── src/
│   ├── routes/              ← one file per resource, clear naming
│   ├── services/            ← business logic separated from routing
│   └── db/queries/          ← all database access in one place
└── tests/                   ← mirrors src/ layout, runs in <15 seconds
```
The agent can read the AGENTS.md, follow the documented structure, run the tests after every change, and verify its own work without asking you anything.

**Agent hostile**
```
enterprise-monolith/
├── README.md                ← "see wiki for setup"
├── src/
│   ├── core/
│   │   └── ... (200+ files, no clear naming)
│   ├── legacy/
│   │   └── ... (300+ files from 2018 migration)
│   ├── utils/
│   │   └── ... (150+ helper files, many duplicated)
│   └── features/
│       └── ... (400+ feature files with inconsistent patterns)
└── test/
    └── ... (tests require 4 microservices running locally)
```
The agent struggles here for three reasons. There is no single source of truth for patterns, so it does not know which implementation to follow when creating something new. Tests require extensive external setup, so the agent cannot verify its own work. The same functionality is implemented differently in different places, so the agent duplicates code instead of reusing what exists.

AI agents do not search your codebase before generating code; they generate what looks correct based on the prompt and whatever context fits in the window. If your shared email validator lives three directories away from where the agent is working, it does not exist as far as the agent is concerned. GitClear's analysis found that copy paste code rose from 8.3% to 12.3% of all changed lines in AI assisted development, while refactoring dropped from 25% to under 10%.

### Running the Readiness Assessment

Several open source CLI tools evaluate your codebase for agent readiness. The `agent-readiness` CLI evaluates across eight dimensions and gives you a prioritized list of what to fix first. Other tools include `context-evaluator` from Packmind (which compares your documentation coverage to your actual codebase) and `cocoindex-code` (which builds semantic search indexes to help agents navigate large codebases).

```bash
npm install -g agent-readiness
agent-readiness .
```

Example output:
```
Agent Readiness Score: 54/100 (Developing)

✓ Test foundation       72/100   Tests exist, run in 12s
✗ Feedback loops        22/100   No pre-commit hooks; CI runs only on push
✗ Documentation         18/100   No AGENTS.md; setup requires human help
~ Type safety           55/100   Partial TypeScript; no strict mode
✓ Code consistency      78/100   ESLint configured; Prettier enforced
✗ Dev environment       30/100   .env.example missing 4 required variables

Priority fixes (highest impact first):
1. Create AGENTS.md with build + test commands       ~30 min
2. Add missing variables to .env.example             ~20 min
3. Add husky + lint-staged pre-commit hooks          ~45 min
```

Address the top three before your first session and the agent's output quality will improve noticeably.

---

## Connecting to Your Real Codebase

### Initialization Commands Across Harnesses

Most harnesses offer an initialization command that reads your project and generates a starter {{< tooltip "rules file" "A markdown document the agent reads at the start of every session containing your project's conventions and standards." >}}. Running this before the first session gives the agent a baseline understanding of your project structure.

| Harness | Command | What it generates |
|---|---|---|
| Claude Code | `/init` | Analyzes the codebase and produces a draft `CLAUDE.md` |
| Codex CLI | `/init` | Produces a draft `AGENTS.md` by analyzing project structure |
| Aider | `/ask` then describe the project | Generates a suggested rules file on request |
| Cline | Open settings → Memory Bank → Initialize | Creates a structured memory bank for the project |
| Cursor | Codebase indexing is automatic; create `.cursor/rules/` manually | No single init command; indexing happens in the background |
| Kilo Code | Memory Bank → initialize | Creates persistent memory files covering the project |

The generated file is a starting point rather than a finished document. It typically captures build commands and folder structure well, but misses your team's conventions, the decisions behind unusual patterns, and anything that requires institutional knowledge to understand. Plan to spend 15 to 30 minutes refining it after the initial generation.

### Helping the Agent Find What It Needs

Most harnesses automatically index your codebase using {{< tooltip "embeddings" "Vector representations of your code that enable semantic search without loading all files into context." >}} stored locally, so the agent can search for relevant code without you loading every file manually. Warp, Cursor, and Claude Code all support this, and the indexed data stays on your machine.

Beyond indexing, two simple practices help in Module 1:

- **Reference files explicitly in your prompts.** Telling the agent "read `src/routes/tasks.ts` before making changes" is more reliable than hoping it finds the right file on its own.
- **Keep your AGENTS.md current.** The agent reads it at the start of every session. If it points to the right reference files and documents your conventions, the agent starts with the context it needs most.

### Monorepos and Large Codebases

Monorepos present a unique challenge. When a repository contains multiple applications sharing common packages, the agent needs to understand which part it is working in and what rules apply there.

The most effective setup is a hierarchy of rules files, with one at the root covering organization wide standards and one inside each application covering that application's specific conventions.

```
my-platform/
├── AGENTS.md                  ← org wide: repo structure, commit format,
│                                  shared tooling, cross app patterns
├── packages/
│   └── ui/                    ← shared design system
├── apps/
│   ├── web/
│   │   ├── AGENTS.md          ← web specific: Next.js patterns, CSS modules,
│   │   │                          Storybook setup, frontend test commands
│   │   └── src/
│   ├── api/
│   │   ├── AGENTS.md          ← api specific: Express patterns, database
│   │   │                          query conventions, integration test setup
│   │   └── src/
│   └── mobile/
│       ├── AGENTS.md          ← mobile specific: React Native patterns,
│       │                          platform specific code, EAS build commands
│       └── src/
└── .env.example               ← documents all variables across all apps
```

When you start a session in `apps/api/`, the agent reads both the root `AGENTS.md` and `apps/api/AGENTS.md`, merging them into its context. This means the root file stays short and general while each application file stays specific and actionable.

Most major harnesses support this cascading pattern. Codex CLI, Claude Code, Cursor, and Windsurf all discover nested AGENTS.md files and apply them based on which files the agent is currently working with.

---

## Writing Your First AGENTS.md

### The Rules File Ecosystem

Different harnesses use different filenames for their rules file, but they all read the same content. **AGENTS.md has emerged as the universal cross harness standard that all major tools now support, making it the safest choice for your primary file.** Some harnesses also have their own default filename that they look for first.

| Harness | Default rules file | Notes |
|---|---|---|
| Claude Code | `CLAUDE.md` | Claude Code looks for this first; also reads `AGENTS.md` if present |
| Codex CLI | `AGENTS.md` | First class support; loads from root and subdirectories |
| Cursor | `.cursor/rules/` or `AGENTS.md` | Supports conditional loading via MDC format |
| Aider | `.aider.conf.yml` or `AGENTS.md` | Supports both formats |
| Cline | `AGENTS.md` | Reads at project root |
| GitHub Copilot | `AGENTS.md` | Server side processing since August 2025 |
| Windsurf | `AGENTS.md` | Subdirectory scoping supported |

If you use only one harness, you can use its default filename directly. If your team uses multiple harnesses, or if new team members might bring different tools, maintaining `AGENTS.md` as the source of truth is the safer choice. You can symlink the tool specific filename to your single authoritative file so updates happen in one place.

```bash
# Keep AGENTS.md as the source of truth
# Create a symlink for Claude Code
ln -s AGENTS.md CLAUDE.md
```

### What to Put In

A useful AGENTS.md covers five things.

- Build and test commands, exactly how to build the project, run the full test suite, and run a single test file from the command line, starting from a clean state
- Code conventions that are specific to this codebase and would not be obvious to an outside reader
- Reference implementations, which existing files to use as templates when creating new code of a specific type
- Patterns to avoid, approaches that look reasonable but should not be used, with a brief reason for each
- Known issues and gotchas, including anything the agent got wrong in a previous session

### What to Leave Out

The rules file is read at the start of every session and consumes context window space. Every unnecessary line has a real cost.

- Anything the agent can infer from reading the code, including the language, the framework, and test library choices obvious from imports
- Generic best practices that apply to all codebases
- Detailed architecture explanations (a brief project structure note is fine; a full design document belongs in a separate file)
- Instructions so obvious they would insult a human reader
- Duplicate content that already exists in a well written README

A good rules file reads like a condensed briefing for a competent contractor who knows the stack but not this specific project, kept under **200 lines** with nothing redundant and everything actionable.

### AGENTS.md Examples

#### Example 1: TypeScript Express REST API

A backend API with PostgreSQL, Jest, ESLint, and Prettier.

```markdown
# AGENTS.md

## Build and test
npm run build        # compiles TypeScript to dist/
npm test             # runs Jest (requires DATABASE_URL in .env.test)
npm run lint         # runs ESLint + Prettier check
npm run lint:fix     # auto-fixes formatting issues

## Environment
Copy .env.example to .env before running locally.
DATABASE_URL must point to a running Postgres 16 instance.
The test suite uses a separate database defined in .env.test.
Do not modify .env.example. Add new variables there with
inline explanations.

## Code structure
Route handlers live in src/routes/, one file per resource.
Business logic lives in src/services/, not in route handlers.
Database queries live in src/db/queries/ using parameterized
statements only. Raw SQL inside route handlers is not acceptable.
Tests live in tests/ and mirror the src/ folder structure.

## Patterns to follow
When adding a new route, reference src/routes/tasks.ts.
It includes error handling, Zod input validation, and the
response shape the frontend expects.

When writing a service, reference src/services/taskService.ts
for the transaction pattern we use with the database pool.

## Patterns to avoid
Use src/utils/logger.ts, not console.log. The logger formats
output for our observability stack.
Check existing dependencies before installing new packages.
lodash, date-fns, and zod are already available.

## Known issues
Integration tests in tests/integration/ are slow because they
hit a real database. Prefer unit tests that mock the db layer.
Adding integration tests requires team sign-off.
```

---

#### Example 2: React Native + Expo Mobile App

A cross platform mobile app using Expo, TypeScript, and React Navigation.

```markdown
# AGENTS.md

## Build and test
npm install                    # install dependencies
npx expo start                 # start Expo dev server
npm test                       # run Jest tests
npm run lint                   # run ESLint

## Running on devices
npx expo start --ios           # iOS simulator
npx expo start --android       # Android emulator
npx expo run:ios               # native iOS build (requires Xcode)
npx expo run:android           # native Android build

## Environment
Copy .env.example to .env for local development.
API_URL must point to a running backend instance.
For production builds, environment variables are set in eas.json.

## Code structure
Screens live in src/screens/, one file per screen.
Reusable components live in src/components/.
Navigation is configured in src/navigation/. Use typed routes.
API calls go through src/api/ using the typed client.
State management uses Zustand stores in src/stores/.

## Patterns to follow
When adding a new screen, reference src/screens/TaskListScreen.tsx.
It includes proper TypeScript typing for navigation props,
loading states, error handling, and pull to refresh.

When adding a new API endpoint, reference src/api/tasks.ts
for the query/mutation pattern using React Query.

## Patterns to avoid
Do not use inline styles. Use the styled components in
src/theme/ or add new ones there.
Do not call APIs directly in components. Use the hooks
in src/api/ that wrap React Query.
Do not use platform checks inline. Use src/utils/platform.ts
for any platform specific logic.

## Known issues
The Android emulator is slow on M1 Macs. Use a physical
device or the iOS simulator for faster iteration.
Hot reload sometimes fails after navigation changes.
Restart the Expo server if navigation state gets stuck.
```

---

#### Example 3: Monorepo with Multiple Applications

A pnpm workspace monorepo with a web app, mobile app, and shared packages.

```markdown
# AGENTS.md

## Build and test
pnpm install                   # install all workspace dependencies
pnpm dev                       # start all apps in development mode
pnpm test                      # run tests across all packages
pnpm lint                      # lint all packages
pnpm build                     # build all packages for production

## Running individual apps
pnpm dev --filter @myorg/web   # start only the web app
pnpm dev --filter @myorg/mobile # start only the mobile app
pnpm test --filter @myorg/ui   # test only the UI package

## Environment
Each app has its own .env.example file.
Copy apps/web/.env.example to apps/web/.env.local
Copy apps/mobile/.env.example to apps/mobile/.env
Shared environment variables are documented in .env.example
at the repo root.

## Structure
apps/web/                      # Next.js web application
apps/mobile/                   # React Native + Expo mobile app
packages/ui/                   # shared component library
packages/api-client/           # typed API client used by both apps
packages/config/               # shared ESLint, TypeScript configs

## Patterns to follow
Shared components go in packages/ui/ and must work on both
web and mobile. Reference packages/ui/src/Button.tsx for the
pattern using react-native-web for cross platform rendering.

New API endpoints should be added to packages/api-client/ so
both apps can use them. Reference packages/api-client/src/tasks.ts.

## Patterns to avoid
Do not import directly between apps. All sharing goes through
packages/. If apps/web/ needs something from apps/mobile/, it
belongs in a shared package.
Do not add dependencies to the root package.json unless they
are workspace tooling. App dependencies go in app package.json.

## Known issues
Turborepo caching occasionally gets stale after branch switches.
Run pnpm clean if you see unexplained build errors after checkout.
The mobile app requires the API to be running for most screens.
Start the API with pnpm dev --filter @myorg/api before mobile dev.
```

---

## Skills: Teaching the Agent What It Cannot Learn From Your Code

**This section is the most important in the module because {{< tooltip "skills" "Structured instruction files (SKILL.md) that teach the agent domain knowledge or behavioral patterns, activating automatically when a task matches the trigger." >}} are what transform a generic agent into one that knows how your team works.** Skills encode team knowledge so that every session, on every developer's machine, starts with the same baseline of expertise.

### What Skills Are

Think of skills as **npm packages for your agent**. Just as npm packages add capabilities to your Node application, skills add domain knowledge and behavioral patterns to your agent. Just as you install an npm package once and it becomes available throughout your project, you install a skill once and the agent loads it automatically whenever a task matches.

The difference is what they contain: an npm package is executable code, while a skill is a structured instruction file called `SKILL.md` that teaches the agent how to approach a specific category of work. Skills can cover areas like frontend design patterns, video generation frameworks, security review checklists, debugging protocols, browser automation, or code review practices.

```
Without skills:

 Task ──► Agent reads code ──► Makes assumptions ──► Output may be
           hoping to infer        about style,           inconsistent,
           conventions            patterns               misses pitfalls

With skills:

 Task ──► Matching skill ──► Skill instructions ──► Output follows
           loads               guide the              known patterns,
           automatically       agent's work           avoids mistakes
```

### Where to Find Skills

The skills ecosystem as of April 2026 has several active hubs.

| Resource | URL | What's there |
|---|---|---|
| Anthropic official | [github.com/anthropics/skills](https://github.com/anthropics/skills) | Reference implementations |
| VoltAgent awesome skills | [github.com/VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) | Largest curated collection with official skills from Vercel, Cloudflare, Remotion, Google, GSAP, DuckDB |
| Composio awesome skills | [github.com/ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) | 49,500+ stars with strong coverage of productivity, office docs, and integrations |
| SkillHub | [skillhub.club](https://skillhub.club) | AI evaluated quality ratings with 7,000+ searchable skills |
| skills.sh | [skills.sh](https://skills.sh) | Canonical URL format for referencing specific skills |

### How to Install a Skill

Skills are plain folders containing a `SKILL.md` file. Installing one takes about thirty seconds.

{{< callout type="error" >}}
**Not all skills are safe.** Some community skills may contain malicious code or instructions that cause the agent to take harmful actions on your system. Before installing any skill, read the full `SKILL.md` file yourself, check the repository's star count and recent activity, and look at whether the maintainer is a known entity. Treat installing a skill with the same caution you would treat installing an npm package from an unknown author.
{{< /callout >}}

**Step 1: Browse and pick a skill**

Browse one of the resources listed above. Read the trigger description in the frontmatter to understand when the skill activates, and read the first few lines of the `SKILL.md` to understand what it does. A skill with a precise, well written trigger is far more valuable than a clever skill with a vague one.

**Step 2: Install it**

The universal location for skills is `.skills/` in your project root. Claude Code also recognizes `.claude/skills/`.

```bash
# Option A: Clone into the universal skills directory
git clone https://github.com/anthropics/skills .skills/frontend-design

# Option B: Clone a single skill from a larger repo
mkdir -p .skills/remotion
curl -o .skills/remotion/SKILL.md \
  https://raw.githubusercontent.com/remotion-dev/skills/main/SKILL.md

# Option C: For global installation (available in all projects)
# Universal location
git clone https://github.com/anthropics/skills ~/.config/agent-skills/frontend-design
# Claude Code specific
git clone https://github.com/anthropics/skills ~/.claude/skills/frontend-design
```

**Step 3: Restart the agent**

The agent discovers skills on startup by scanning the skills directory. Restart your terminal session or reload the harness.

**Step 4: Verify**

Ask the agent directly what skills it has available. It should list the skills it found. If a skill is not listed, check that the folder path is correct and that the `SKILL.md` file exists inside the folder.

### Popular Skills Worth Knowing

The ecosystem has thousands of skills. Some widely used ones include:

- **Frontend Design** from Anthropic, for making UI look designed rather than generated
- **Remotion** from the Remotion team, for programmatic video generation
- **OWASP Security**, for structured security review against current standards
- **Systematic Debugging**, for methodical bug isolation
- **Browser Use / Playwright**, for interacting with live web applications
- **Code Reviewer**, for self review before you see the output

Browse the hubs listed above and install the ones that match your workflow. The skills that matter most are the ones you build yourself for your specific codebase, which is covered next.

---

### Skills in Practice

**Example A: Setting up a cron job without and with an infrastructure skill**

```
WITHOUT SKILL:

Task: "Set up a cron job to clean up expired sessions every night"
    │
    ▼
Agent generates code immediately
    │
    ▼
Output: Creates a standalone script,
adds it to crontab with `crontab -e`,
no logging, no error handling,
no integration with existing infra
    │
    ▼
Result: Works but does not follow
team patterns for scheduled jobs


WITH SKILL:

Task: "Set up a cron job to clean up expired sessions every night"
    │
    ▼
Infrastructure skill loads (triggers on cron/scheduled/job keywords)
    │
    ▼
Skill instructions specify: use existing scheduler service,
add to config/schedules.yml, include monitoring hooks,
follow retry pattern in src/jobs/base.ts
    │
    ▼
Output: Job added to existing scheduler config,
inherits logging/monitoring/retry behavior,
follows team patterns exactly
    │
    ▼
Result: Consistent with all other scheduled jobs
```

---

**Example B: Building a landing page without and with the frontend design skill**

```
WITHOUT SKILL:

Task: "Build a landing page for a developer tool"
    │
    ▼
Agent generates code immediately
    │
    ▼
Output: Inter font, purple gradient,
three feature cards in a grid, CTA button
    │
    ▼
Result: Technically correct,
immediately recognizable as AI generated


WITH SKILL:

Task: "Build a landing page for a developer tool"
    │
    ▼
Skill loads automatically (triggers on frontend/UI work)
    │
    ▼
Agent commits to a visual direction first
(brutalist, editorial, retro futuristic, etc.)
    │
    ▼
Agent executes the chosen direction with intention
    │
    ▼
Output: Monospace font + bold slab display,
high contrast dark background, single sharp accent,
asymmetric layout, staggered entrance animations
    │
    ▼
Result: Looks designed, not generated
```

---

**Example C: Security review without and with the OWASP skill**

```
WITHOUT SKILL:

Task: "Review the authentication code"
    │
    ▼
Agent reads the function
    │
    ▼
General assessment: "bcrypt is used,
password length isn't validated,
implementation looks reasonable"
    │
    ▼
Result: 0 specific issues flagged


WITH SKILL:

Task: "Review the authentication code"
    │
    ▼
Skill loads automatically (triggers on security/auth review)
    │
    ▼
Agent works through OWASP categories systematically
    │
    ├─ Injection: SQL parameterized ✓
    ├─ Broken auth: Sessions not invalidated on password change ✗
    ├─ Sensitive data: Reset token stored in plaintext ✗
    ├─ Broken access control: No user verification on endpoint ✗
    └─ Misconfiguration: Rate limiting commented out ✗
    │
    ▼
Result: 4 issues flagged, 1 critical
```

The skill does not make the agent smarter. It makes the agent systematic, which is often more valuable.

---

### Writing Your Own Skill

After using a few community skills, you will start noticing patterns in your own workflow that would benefit from the same treatment. If you have explained the same context to the agent three or more times across different sessions, that context belongs in a skill.

A skill file has a frontmatter section defining when it activates and an instructions body defining what the agent should do when it is active.

```markdown
---
name: "task-api-endpoint"
description: "Use when creating a new REST API endpoint in
  this project. Includes error handling pattern, Zod validation
  structure, and response shape the frontend expects."
version: "1.0.0"
---

# Task API Endpoint Skill

When creating a new endpoint:

1. Reference src/routes/tasks.ts as the structural template.
   Copy the error handling middleware, Zod schema pattern,
   and the response envelope shape.

2. Place business logic in src/services/, not in the route
   handler. The handler should parse input, call the service,
   and return the response.

3. Validation errors use src/utils/errors.ts formatZodError().
   Do not implement custom error formatting.

4. Run npm run lint and npm test src/routes/ after creating
   the endpoint, before considering the task complete.
```

Place this in `.skills/task-api-endpoint/SKILL.md` and the agent will load it automatically whenever you describe creating a new endpoint in this project. For Claude Code, you can also use `.claude/skills/task-api-endpoint/SKILL.md`. The skill stops you from having to explain your patterns every session.

---

## Installing a Starter Framework

A starter framework is a set of slash commands and skill files that structure how the agent approaches a full project. The difference between a framework and individual skills is scope. Skills teach the agent how to do a specific type of work, while a framework teaches it how to structure an entire development workflow from planning through shipping.

The ecosystem has several well maintained options as of April 2026. This is an actively evolving space where new frameworks appear weekly and existing ones ship significant updates regularly.

| Framework | Philosophy | Harness support | Best for |
|---|---|---|---|
| [**TaskMaster**](https://github.com/eyaltoledano/claude-task-master) | PRD to dependency aware task graph via MCP | Cursor (first class), Claude Code, Windsurf, VS Code | Teams with complex dependency chains |
| **GSD** | Execution first, fresh context per phase, minimal ceremony | Claude Code, Gemini CLI, OpenCode, Cursor, Codex, Windsurf | Solo devs and small teams who want output without overhead |
| [**Vibe**](https://github.com/prashishh/vibe) | Spec and epic driven, sprint planning as the first artifact | All major harnesses | Teams who want planning outputs that persist across sessions |
| [**gstack**](https://github.com/garrytan/gstack) | Sprint shaped roles from Think through Ship | Claude Code | Individual developers and small teams |
| [**BMAD**](https://github.com/bmadcode/BMAD-METHOD) | Documentation first with 21 specialized agent roles | Claude Code, Cursor, and most major harnesses | Teams building larger systems needing consistent quality |

All five are open source, free, and installable in under five minutes. The right framework for your team might be a newer one or one you build yourself. Treat any framework as a starting point rather than a permanent commitment.

---

## Your First Real Task End to End

Everything up to this point has been setup. Now it is time to run a real session and see how it all comes together. This section walks through it step by step, including the moments where things typically go sideways and what to do when they do.

### Picking the Right First Task

The goal of the first session is not to ship something impressive. It is to experience the full loop and learn how the agent behaves on your specific codebase. Pick a task where you already know the right answer so you can evaluate the agent's output against your own understanding.

Good first tasks include a bug you already know the fix for, a test file for a module you wrote recently, or extracting a utility function that is copy pasted in three places.

Tasks that are too big for first sessions include any new feature that requires architectural decisions, anything that requires changing the database schema, or refactoring a large file that has mixed concerns.

{{< callout type="info" >}}
**Start with a task you already know the answer to.** You are not trying to get value from the agent yet, you are calibrating. When you know the correct fix, you can focus entirely on evaluating the agent's process and build a baseline before trusting it with tasks where you do not know the answer.
{{< /callout >}}

### Walkthrough: Adding Input Validation to an API Endpoint

The walkthrough below uses a concrete example from the TypeScript Express API described in the AGENTS.md examples. If your stack is different, the pattern transfers directly and you can swap the specific commands for yours.

The task is that the `POST /tasks` endpoint accepts any request body, and a missing `title` returns a database error instead of a clean 400 response.

**Step 1: Commit first**

```bash
git checkout -b agent/add-task-validation
git status  # working tree should be clean
```

Starting on a fresh branch with a clean tree means any mistake is one command away from being undone with `git checkout main`. Make this automatic before every session.

**Step 2: Frame the task precisely**

```
Add input validation to the POST /tasks endpoint in src/routes/tasks.ts.

Required fields:
- title: non-empty string
- userId: UUID

Optional fields:
- priority: must be one of "low", "medium", or "high"

Use the existing Zod dependency. Return a 400 with a structured
error message if validation fails. Format errors using the
formatZodError helper in src/utils/errors.ts.

Follow the pattern used for PATCH /tasks/:id in the same file.
```

Notice what this prompt contains: the exact file, the validation rules, the library to use, the error format, and the existing pattern to follow. Notice what it leaves out: it does not explain how Zod works, because the agent can read that from the codebase. Your job is to provide the context the code itself does not make obvious.

**Step 3: Start in plan mode**

Before approving any execution, read the plan the agent produces. A correct plan for this task names exactly one file to change and a few new test cases. If the plan mentions anything outside `src/routes/`, something is being misread.

{{< callout type="warning" >}}
**Common plan review issues and what they mean:**

- The plan lists src/db/schema.ts → The agent thinks validation requires a schema change. Clarify that you want application level validation only.
- The plan lists package.json → The agent thinks a new package is needed. Clarify that Zod is already installed.
- The plan mentions 15+ files → The scope is being interpreted much broader than intended. Restart with a tighter, more explicit prompt.

If you see any of these, do not approve. Interrupt the agent, add the missing context to your prompt, and ask for a revised plan. This costs a few minutes now and saves significant cleanup later.
{{< /callout >}}

**Step 4: Watch the execution**

As the agent works, you will see it read `src/routes/tasks.ts` and the reference pattern for PATCH, read `src/utils/errors.ts` to understand `formatZodError`, write the validation schema and add it to the route handler, read the existing test file, and write two or three new test cases.

This tool use sequence is normal and healthy. If you see the agent reading files outside `src/routes/` and `src/utils/`, that is worth watching because it may be about to make a change broader than what you asked for.

**Step 5: Review**

```bash
git diff               # what actually changed
npm test               # does everything still pass?
npm run lint           # any new lint errors?
```

Read the diff against the plan. The change should be small, consisting of a Zod schema, a validation call near the top of the route handler, and an early return with a 400 response. If the diff is significantly larger, examine what was added and why.

Run the **full test suite**, not just the new tests. The agent writes tests to validate its own output and they will almost always pass. The tests written by humans before this session are the independent check.

**Step 6: Debrief as a team**

After the session, work through these questions together:

- **What did the agent do that nobody expected?** This is not necessarily bad, because sometimes the agent catches something you missed.
- **Where did the prompt need to be corrected mid session?** This points to scope clarity you can improve next time.
- **What would you phrase differently?** Revise the prompt in writing so it is available for similar tasks.
- **What now belongs in the AGENTS.md that was not there before?** For this task, a line noting that validation errors use `formatZodError` from `src/utils/errors.ts`.

```
Session debrief → AGENTS.md addition

Before: agent implemented its own error formatter
After debrief addition:
"Zod validation errors use formatZodError from
src/utils/errors.ts. Do not implement custom error formatting."

Result: the same mistake never happens again across
any session on this codebase.
```

Each debrief produces one or two AGENTS.md entries or skill files. Over time, your agent will know your codebase well enough that new team members can be productive with it on day one.

The first few sessions will feel slower than writing the code yourself, and that is expected. Most developers find they are moving noticeably faster by the second or third week, particularly on tasks that touch many files or involve patterns that repeat across the codebase.

---

## Glossary Additions

| Term | Definition |
|---|---|
| **Hosted model** | A model that runs on the provider's servers, accessed via API; examples include Claude, GPT, Gemini, DeepSeek, and Qwen |
| **Open weight model** | A model whose trained weights are publicly released, allowing self hosting or access through third party APIs at lower cost |
| **Rolling window** | A rate limit mechanism where capacity resets continuously based on usage over a moving time period, not on a fixed calendar date |
| **Plan mode** | A harness mode where the agent proposes a full approach before making any changes |
| **Ask mode** | A harness mode where the agent prompts for approval before each significant action |
| **Execute mode** | A harness mode where the agent acts without pausing at each step |
| **Agent readiness** | A measure of how well a codebase supports autonomous agent work, distinct from human readability |
| **Skill** | A structured instruction file (SKILL.md) that teaches the agent domain knowledge or behavioral patterns, activating automatically when a task matches the trigger |
| **Starter framework** | A set of slash commands and skill files that structure how an agent works through a full project lifecycle |
| **Embeddings based indexing** | A technique where the harness creates vector representations of your codebase to enable semantic search without loading all files into context |
