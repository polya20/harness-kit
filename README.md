



<p align="center">
  <img src="logo.png" alt="Harness Kit Logo" width="100%" />
</p>

<p align="center">
  <strong>Prompt engineering is a suggestion. Harness Kit is a guarantee.</strong>
</p>

<p align="center">
  <a href="#quick-start">Quick Start</a> • 
  <a href="#the-problem">The Problem</a> • 
  <a href="#architecture">Architecture</a> • 
  <a href="#viral-features">Features</a> • 
  <a href="#benchmarks">Benchmarks</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square" alt="License" />
  <img src="https://img.shields.io/badge/Rust-Native_Core-E35F26.svg?style=flat-square" alt="Rust Core" />
  <img src="https://img.shields.io/badge/TypeScript-SDK-3178C6.svg?style=flat-square" alt="TS SDK" />
  <img src="https://img.shields.io/badge/Model-Agnostic-8A2BE2.svg?style=flat-square" alt="Model Agnostic" />
</p>

---

## 🛑 The 70% Reliability Trap

Every team building production AI agents rediscovers the exact same painful lessons independently. You build an agent. It works flawlessly in a 5-minute Slack demo. You put it in production, and:

- ❌ **Token Death Spirals:** The agent tries a `bash` command. It fails. It tries the *exact same command* 14 times in a row, burning $50 in tokens over the weekend.
- ❌ **Context Amnesia:** The context window fills up, compaction triggers, and the agent completely forgets what files it already edited, abandoning the feature halfway.
- ❌ **Rule Ignoring:** You write an exhaustive `AGENTS.md` telling it to "never use raw HTML." It creatively ignores you because LLMs treat prompts as suggestions, not constraints.
- ❌ **Over-tooling:** You give the agent 15 tools to be helpful. It gets confused, hallucinates arguments, and crashes.

**The hard truth:** You cannot fix non-deterministic models with more prompt engineering. **You need a mechanical operating system.**

## ⚡️ The Solution: Harness Kit

**Harness Kit** is the missing infrastructure layer between "I have an LLM" and "I have a reliable agent." 

It treats the LLM as a raw, unreliable CPU and acts as the strict Operating System—sandboxing its tools, mechanically enforcing its budgets, and persisting its memory outside the context window.

Built on production lessons from **Stripe's Minions, Anthropic, Vercel, and recent arXiv research**, Harness Kit is a high-performance Rust core wrapped in a seamless DX.

### 🏆 The Proof (Standard Coding Benchmark)

| Approach | Success Rate | Tokens Burned | Doom Loops |
| :--- | :---: | :---: | :---: |
| Raw Agent (GPT-4o / Opus) | 58% | 140k | ~15% |
| **With Harness Kit** | **96%** | **61k** | **0%** |

*Run `harness score --suite standard` to reproduce these numbers locally.*

---

## 🚀 Quick Start (Zero to Reliable in 30s)

You don't need to rewrite your agent. Drop Harness Kit into your existing repo.

```bash
# 1. Initialize Harness Kit in your repo
npx harness-kit init --analyze

# 2. Run a fully autonomous, reliable agent session
npx harness-kit run "Build a complete Stripe checkout flow and fix any test failures"
```

Watch it work. It initializes, writes a machine-readable feature manifest, runs coding sessions, mechanically halts on errors, commits progress to `git`, and tests each feature. **It just doesn't fail.**

### Share Your Agent's Brain 🧠

Ever wonder *why* your agent succeeded (or failed)? Run:

```bash
npx harness-kit replay
```
*Generates a shareable, visual timeline video of exactly what your agent did: which tools it called, where it got stuck, how it recovered, and the git diff at every step.*

---

## 🏗️ Architecture: 5 Mechanical Guardrails

Harness Kit isn't a wrapper. It is 5 distinct, highly-opinionated subsystems.

### 1. 🚦 Orchestrator (`BlueprintEngine`)
* **What it prevents:** Agents getting lost on complex tasks.
* **How:** Implements hybrid workflows (Stripe's Minions). Scaffolding (cloning, running linters, opening PRs) is done via deterministic, token-free code. Only the genuine creative work (writing code, fixing tests) is handed to the LLM. 

### 2. 🛑 Execution Runtime (`DoomLoopDetector` & `CIBudget`)
* **What it prevents:** $500 token-burning death spirals.
* **How:** Intra-session guardrails physically count identical tool failures. If an agent tries the same broken `bash` command 3 times, it triggers a hard interrupt and injects an error context. Limits CI test-fix loops to 2 max.

### 3. 🧠 Context & Knowledge (`KnowledgeAuditor`)
* **What it prevents:** The 3% performance drop caused by bloated `AGENTS.md` files (arXiv: 2602.11988).
* **How:** Scans your project instruction files and violently strips out redundant LLM-generated prose. It enforces that context files only contain concrete constraints (e.g., "Use `npm run test`, not `jest`"). Uses `ScopedContextInjector` to only feed the agent rules for the exact directory it is currently editing.

### 4. 🧰 Tools & Sandboxing (`Linux Namespace Sandbox`)
* **What it prevents:** Agents wiping your hard drive or hallucinating outdated APIs.
* **How:** Uses native Linux namespaces (`--net`, `--ipc`, `--pid`) to completely isolate `bash` executions. Implements a strict `ToolBudget`—hard-capping how many tools the agent can use per session. Includes native `LSP` support for semantic code search, reducing grep-based hallucinations.
* *Bonus:* Native integration with Andrew Ng's `context-hub` to fetch external API docs dynamically, preventing API hallucination.

### 5. 💾 State & Memory (`FeatureManifest` & `GitCheckpointer`)
* **What it prevents:** Agents hallucinating task completion or forgetting state.
* **How:** Splits memory into *Episodic* (cross-session persistent logs) and *Working* (current context window). Uses a strictly-typed, write-protected `FeatureManifest.json`—the agent can only flip flags to `true`, it cannot creatively rewrite your spec. Universal undo via `GitCheckpointer` on every failed loop.

---

## 🔌 Compatibility & Ecosystem

Harness Kit is **Model & Runtime Agnostic**. It does not replace your AI SDK; it wraps it.

```typescript
import { Harness } from 'harness-kit';
import { generateText } from 'ai'; // Vercel AI SDK, LangGraph, etc.

const harness = new Harness({
  model: 'claude-3-5-sonnet',
  budget: { maxTools: 4, maxCiRetries: 2 },
  tools: ['fs', 'bash', 'lsp', 'context-hub']
});

await harness.run("Migrate the database to PostgreSQL");
```

🤝 **Context-Hub Integration:** Works perfectly out-of-the-box with[`context-hub`](https://github.com/andrewyng/context-hub) to ensure your agents never write code against deprecated, hallucinated API endpoints.

---

## 📜 Design Principles

1. **Enforcement over Suggestion:** If a rule isn't mechanically enforced in the runtime, it doesn't exist.
2. **File System is Truth:** No external databases. Your state, manifests, and progress logs live in your repo and are git-versioned.
3. **Fewer Tools Win:** Vercel proved 3 tools beat 15. The library actively makes it harder to add unnecessary tools.
4. **Rippable by Design:** Plugs into LangChain, LangGraph, or native SDKs. As frontier models get smarter, eject any module you no longer need.

---

## 🤝 Contributing

We are actively porting advanced components into the native Rust core (`crates/runtime`). If you want to help build the future of reliable agentic infrastructure, see our [CONTRIBUTING.md](CONTRIBUTING.md).

If Harness Kit saved you from a token death spiral, consider giving us a ⭐.
