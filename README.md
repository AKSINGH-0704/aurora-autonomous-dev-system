<div align="center">
  <h1>AURORA</h1>
  <p><b>Autonomous Self-Correcting Development System</b></p>
  <p><i>One prompt. Four agents. Zero human intervention. A working application.</i></p>
  <br/>

  [![Built with Claude Code](https://img.shields.io/badge/Built_with-Claude_Code-7A52CC?style=for-the-badge&logo=anthropic&logoColor=white)]()
  [![Next.js 14](https://img.shields.io/badge/Next.js-14.2.15-000000?style=for-the-badge&logo=next.js&logoColor=white)]()
  [![TypeScript](https://img.shields.io/badge/TypeScript-Strict-3178C6?style=for-the-badge&logo=typescript&logoColor=white)]()
  [![Prisma + SQLite](https://img.shields.io/badge/Prisma-SQLite-2D3748?style=for-the-badge&logo=prisma&logoColor=white)]()
  [![MCP Context7](https://img.shields.io/badge/MCP-Context7-00E5FF?style=for-the-badge)]()
</div>

---

## What is AURORA?

AURORA is a multi-agent autonomous development system built on Claude Code. Give it a single high-level prompt and it will autonomously plan the architecture, generate full-stack code, run the build, detect and fix its own errors, seed the database, and deliver a working application — all without any intermediate human interaction.

It doesn't just generate code — it compiles, validates, and repairs it until it runs successfully.

---

## 🛑 The Problem With AI Code Generators

Most AI coding tools operate on a "generate and hope" model. They output thousands of lines of code, hallucinate dependencies, produce files that reference imports that don't exist, and leave the user to debug the mess. The code looks right. It just doesn't run.

## 🛡️ How AURORA Solves This

AURORA treats every line of generated code as untrusted until proven by the compiler. The system doesn't consider a task complete until `npm run build` exits with code 0. If it doesn't, AURORA reads the error, opens the specific broken file, applies a targeted fix, and tries again.

---

## ⚙️ The 4-Phase Pipeline

| Phase | Agent | What It Does |
|:------|:------|:-------------|
| **01: PLAN** | Planner | Analyzes the prompt, researches the domain via Context7 MCP, and outputs a structured `BLUEPRINT.md` — a binding contract defining every table, route, page, and file. |
| **02: BUILD** | Builder | Reads the blueprint and generates the complete application. Next.js 14, TypeScript, Prisma, Tailwind. Every file, every route, every component — exactly as specified. Nothing more, nothing less. |
| **03: VALIDATE** | Validator | Runs `npm install` → `prisma generate` → `prisma db push` → `npm run build`. If any command fails: reads the stack trace, opens the broken file, applies a surgical fix, re-runs. Up to 3 attempts per command. |
| **04: FINALIZE** | Finalizer | Seeds the database with realistic relational data, generates a README with setup instructions, and runs one final build to confirm stability. |

---

## 🔥 What Makes AURORA Different

**Blueprint Contract** — The Planner generates a structured `BLUEPRINT.md` that specifies every database table, API route, frontend page, and file path before a single line of code is written. The Builder implements it exactly. If it's not in the blueprint, it doesn't get built. This eliminates hallucinated features and missing dependencies.

**Self-Healing Validation** — The Validator doesn't just run the build and report errors. It reads the `stderr` output, identifies the exact file and line number from the stack trace, opens only that file, applies the minimal fix, and re-runs. Most systems crash on first error. AURORA fixes itself.

**Stateless Shell Awareness** — Claude Code runs each bash command in an isolated subshell where directory state is lost between calls. AURORA is engineered around this constraint, explicitly chaining commands with `cd app &&` to ensure correct execution regardless of shell state.

**Domain-Aware Planning** — The Planner integrates Context7 MCP to research domain-specific best practices before generating the blueprint. When asked to build a medical or financial application, it doesn't guess — it looks up the patterns first.

---

## 📂 Repository Structure

```
├── CLAUDE.md                        ← Master orchestrator (pipeline logic & hard constraints)
├── README.md                        ← You are here
└── .claude/
    ├── settings.json                ← Permissions, auto-approvals, MCP configuration
    └── skills/
        ├── 01-planner/SKILL.md      ← Requirement analysis → BLUEPRINT.md
        ├── 02-builder/SKILL.md      ← Full-stack code generation from blueprint
        ├── 03-validator/SKILL.md    ← Self-healing build validation loop
        └── 04-finalizer/SKILL.md    ← Seed data, documentation, final verification
```

No application code is included. 100% of the application is generated autonomously at runtime.

---

## 🚀 Usage

```bash
git clone https://github.com/YOUR_USERNAME/aurora-autonomous-dev-system.git
cd aurora-autonomous-dev-system
claude
```

Then enter a prompt:

```
Build a role-based telemedicine portal where doctors can manage appointments
and patients can securely submit medical histories, with a dashboard
showing appointment statistics and status tracking.
```

AURORA handles the rest. Typical execution: ~9 minutes from prompt to working application.

---

## 🧠 Design Principles

**Execution over theory** — Every design decision optimizes for a working build, not an impressive diagram.

**Constraints over randomness** — Fixed tech stack, locked framework versions, strict blueprint adherence. Determinism is the goal.

**Surgical fixes over brute force** — The Validator reads one error, opens one file, fixes one issue. It doesn't regenerate the entire codebase on failure.

**Graceful degradation over silent failure** — If validation can't resolve an issue after 3 attempts, it logs the error in `KNOWN_ISSUES.md` and proceeds. The system never hangs.

---

**Built For:** Claude Builders Club × APOGEE 2026 Hackathon — Round 2: Agentic Development

---

<div align="center">
  <i>AURORA doesn't generate code. It engineers software.</i>
</div>
