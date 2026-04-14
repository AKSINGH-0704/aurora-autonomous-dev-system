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

AURORA is a multi-agent system built on Claude Code that takes a single prompt like *"Build me a task management app with auth and a dashboard"* and autonomously plans, codes, tests, fixes, and delivers a working full-stack application. No hand-holding, no back-and-forth.

The key part: it doesn't just write code and walk away. It actually runs `npm run build`, reads the errors when things break (and they will), opens the exact file that's broken, patches it, and retries. It keeps doing this until the build passes clean.

---

## 🛑 Why This Exists

Here's the thing about most AI code generators: they write code that *looks* correct. Nice file structure, clean syntax, proper naming. But when you actually try to run it? Missing imports, wrong types, broken references everywhere. You end up spending more time fixing the AI's output than it would've taken to write it yourself.

## 🛡️ What AURORA Does Differently

AURORA doesn't trust its own output. Every line of generated code is assumed broken until the compiler says otherwise. The system won't mark anything as "done" until `npm run build` returns exit code 0. If it fails, the Validator reads the error, finds the file, fixes the problem, and runs again. That's the whole point.

---

## ⚙️ The 4-Phase Pipeline

| Phase | Agent | What It Does |
|:------|:------|:-------------|
| **01: PLAN** | Planner | Reads the prompt, researches the domain using Context7 MCP if needed, and writes a `BLUEPRINT.md` that locks down every database table, API route, page, and file path. |
| **02: BUILD** | Builder | Takes the blueprint and generates the entire app. Next.js 14, TypeScript, Prisma, Tailwind. If the blueprint says 5 routes and 3 pages, you get exactly 5 routes and 3 pages. |
| **03: VALIDATE** | Validator | Runs `npm install`, `prisma generate`, `prisma db push`, `npm run build`. When something fails (and it usually does on first try), it reads the stack trace, opens that specific file, fixes that specific line, and reruns. Max 3 attempts per command. |
| **04: FINALIZE** | Finalizer | Seeds the database with realistic-looking data, writes a README with setup instructions, and does one last build check to make sure nothing broke. |

---

## 🔥 What Actually Makes This Work

**The Blueprint Contract.** Before any code is written, the Planner creates a `BLUEPRINT.md` that defines the entire app: tables, columns, routes, pages, components, file tree. The Builder follows this exactly. No creative improvisation, no hallucinated features. If it's not in the blueprint, it doesn't exist.

**The Self-Healing Loop.** This is the part that matters most. The Validator runs the build, and when it fails, it doesn't just dump the error and give up. It parses the `stderr`, finds the file and line number, opens just that file, makes the smallest possible fix, and tries again. Most AI systems break on the first compilation error. AURORA actually recovers.

**Stateless Shell Handling.** Claude Code runs every bash command in a fresh subshell, so `cd app` in one command is forgotten by the next. AURORA works around this by chaining every command as `cd app && npm run build` so it always executes in the right directory. Sounds simple, but most agent systems don't account for this and crash randomly.

**Domain-Aware Planning.** The Planner has access to Context7 MCP, which lets it look up real documentation and best practices before writing the blueprint. So when you ask for something domain-specific (medical, financial, logistics), it researches first instead of guessing.

---

## 📂 Repository Structure

```
├── CLAUDE.md                        ← The brain. Pipeline rules, constraints, phase definitions.
├── README.md                        ← You're reading it.
└── .claude/
    ├── settings.json                ← Permissions, MCP config, auto-approved commands
    └── skills/
        ├── 01-planner/SKILL.md      ← Analyzes prompt, writes BLUEPRINT.md
        ├── 02-builder/SKILL.md      ← Generates the full app from blueprint
        ├── 03-validator/SKILL.md    ← Runs build, catches errors, fixes them
        └── 04-finalizer/SKILL.md    ← Seeds data, writes docs, final check
```

There's no application code in this repo. Everything gets generated from scratch when you run it.

---

## 🚀 How to Use It

```bash
git clone https://github.com/YOUR_USERNAME/aurora-autonomous-dev-system.git
cd aurora-autonomous-dev-system
claude
```

Then just type what you want:

```
Build a role-based telemedicine portal where doctors can manage appointments
and patients can securely submit medical histories, with a dashboard
showing appointment statistics and status tracking.
```

AURORA takes it from there. In our test run, it went from prompt to working app in about 9 minutes.

---

## 🧠 Design Principles

**Execution over theory.** Every decision is made to produce a build that actually passes. Not a pretty architecture diagram.

**Constraints over randomness.** Fixed tech stack, pinned framework version, strict blueprint adherence. We want the same result every time.

**Surgical fixes over brute force.** When something breaks, fix that one line. Don't regenerate 50 files because one import was wrong.

**Graceful degradation over crashing.** If the Validator can't fix something after 3 tries, it logs the issue in `KNOWN_ISSUES.md` and moves on. The system never gets stuck in an infinite loop.

---

**Built for:** Claude Builders Club × APOGEE 2026 Hackathon, Round 2: Agentic Development

---

<div align="center">
  <i>AURORA doesn't generate code. It engineers software.</i>
</div>
