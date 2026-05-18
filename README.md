<div align="center">

<br/>

<h1>AURORA</h1>

### Autonomous Development System

*One prompt. Four agents. Zero human intervention. A working application.*

<br/>

[![Claude Code](https://img.shields.io/badge/Built_with-Claude_Code-7A52CC?style=flat-square&logo=anthropic&logoColor=white)](https://claude.ai/code)&nbsp;
[![Next.js](https://img.shields.io/badge/Next.js-14.2.15-000000?style=flat-square&logo=next.js&logoColor=white)](https://nextjs.org)&nbsp;
[![TypeScript](https://img.shields.io/badge/TypeScript-Strict-3178C6?style=flat-square&logo=typescript&logoColor=white)](https://typescriptlang.org)&nbsp;
[![Prisma](https://img.shields.io/badge/Prisma-SQLite-2D3748?style=flat-square&logo=prisma&logoColor=white)](https://prisma.io)&nbsp;
[![Context7](https://img.shields.io/badge/MCP-Context7-00E5FF?style=flat-square)](https://github.com/AKSINGH-0704/aurora-autonomous-dev-system)&nbsp;
[![Self-Healing](https://img.shields.io/badge/Validation-Self--Healing_Loop-10B981?style=flat-square)](https://github.com/AKSINGH-0704/aurora-autonomous-dev-system)

<br/>

| Phase | Agent | Status |
|:-----:|:------|:------:|
| 01 | Planner — Blueprint generation | `● Operational` |
| 02 | Builder — Application scaffolding | `● Operational` |
| 03 | Validator — Self-healing build loop | `● Operational` |
| 04 | Finalizer — Seed, verify, package | `● Operational` |

<br/>

[System Flow](#system-flow) &nbsp;·&nbsp; [Agent Pipeline](#agent-pipeline) &nbsp;·&nbsp; [What Makes It Different](#what-makes-aurora-different) &nbsp;·&nbsp; [Run It](#usage)

<br/>

</div>

---

<br/>

## The Problem With AI Code Generators

Most AI coding tools operate on a "generate and hope" model. They output thousands of lines of code, hallucinate dependencies, produce files that reference imports that don't exist, and leave the user to debug the mess. The code looks right. It just doesn't run.

AURORA treats every line of generated code as untrusted until proven by the compiler. The system doesn't consider a task complete until `npm run build` exits with code 0. If it doesn't — it reads the error, opens the specific broken file, applies a targeted fix, and tries again. Completion is earned, not declared.

<br/>

## System Flow

```mermaid
graph TD
    A["Single User Prompt"] --> B["PHASE 1 — PLAN\nPlanner Agent"]
    B --> C["BLUEPRINT.md\nBinding contract for all phases"]
    C --> D["PHASE 2 — BUILD\nBuilder Agent"]
    D --> E["app/ — Full Next.js application\nTypeScript · Prisma · Tailwind"]
    E --> F["PHASE 3 — VALIDATE\nValidator Agent"]
    F --> G{"Build passes?"}
    G -->|Yes| H["PHASE 4 — FINALIZE\nFinalizer Agent"]
    G -->|"No — retry up to 3x"| I["Read stack trace\nFix specific file\nRe-run command"]
    I --> G
    G -->|"Still failing after 3x"| J["KNOWN_ISSUES.md\nLog and proceed"]
    J --> H
    H --> K["Seeded · Documented · Verified\nWorking Application"]

    style A fill:#0d1117,color:#c9d1d9,stroke:#30363d
    style B fill:#0d1117,color:#6366F1,stroke:#4F46E5
    style C fill:#0d1117,color:#D97706,stroke:#B45309
    style D fill:#0d1117,color:#6366F1,stroke:#4F46E5
    style E fill:#0d1117,color:#c9d1d9,stroke:#30363d
    style F fill:#0d1117,color:#6366F1,stroke:#4F46E5
    style G fill:#0d1117,color:#D29922,stroke:#9E6A03
    style H fill:#0d1117,color:#6366F1,stroke:#4F46E5
    style I fill:#0d1117,color:#F85149,stroke:#DA3633
    style J fill:#0d1117,color:#8B949E,stroke:#30363d
    style K fill:#0d1117,color:#3FB950,stroke:#238636
```

<br/>

## Agent Pipeline

Each phase operates as an isolated agent. Outputs from one phase become the structured inputs for the next. No agent reinterprets prior decisions — it reads the artifact and executes against it exactly.

<div align="center">

| Phase | Agent | Consumes | Produces |
|:-----:|:------|:---------|:---------|
| 01 | **Planner** | User prompt | `BLUEPRINT.md` — schema, routes, pages, file tree |
| 02 | **Builder** | `BLUEPRINT.md` | `app/` — full Next.js app, all routes and components |
| 03 | **Validator** | `app/` source tree | Verified build OR `KNOWN_ISSUES.md` |
| 04 | **Finalizer** | Validated build | Seed data, `app/README.md`, completion summary |

</div>

**The binding contract:** `BLUEPRINT.md` is the single source of truth. If a feature is not in the blueprint, it does not get built — no additions, no omissions, no improvisation between phases.

<br/>

## Self-Healing Validation Loop

```mermaid
sequenceDiagram
    participant V as Validator Agent
    participant B as Build System
    participant F as File System

    V->>B: Run build command
    B-->>V: Result

    alt Build passes
        V->>V: Proceed to Phase 4
    else Build fails
        V->>B: Read error stack trace
        B-->>V: Specific file + line reference
        V->>F: Open only the broken file
        F-->>V: File contents
        V->>F: Apply surgical fix
        V->>B: Re-run failed command
        Note over V,B: Max 3 attempts per command
    end

    alt Still failing after 3 attempts
        V->>F: Write KNOWN_ISSUES.md
        V->>V: Proceed to Phase 4
    end
```

The validator reads only the file named in the error trace — not the full codebase. It fixes the specific issue without touching surrounding code. After 3 failed attempts per command, errors are logged to `KNOWN_ISSUES.md` and execution continues. The system never loops indefinitely — it acknowledges failure and moves forward.

<br/>

## What Makes AURORA Different

**Blueprint as binding contract**
The Planner generates a structured `BLUEPRINT.md` defining every database table, API route, frontend page, and file path before a single line of code is written. The Builder implements it exactly. This eliminates hallucinated features, mismatched schemas, and missing dependencies between planning and implementation.

**Self-healing validation**
The Validator doesn't report errors — it fixes them. It reads `stderr`, identifies the exact file and line from the stack trace, opens only that file, applies the minimal fix, and re-runs. Most systems crash on first error. AURORA corrects itself.

**Stateless shell awareness**
Claude Code runs each bash command in an isolated subshell where `cd` does not persist between calls. AURORA is engineered around this constraint — every command is explicitly chained with `cd app &&` to guarantee correct execution regardless of shell state.

**Domain-aware planning**
The Planner integrates Context7 MCP to research domain-specific patterns before generating the blueprint. When building a medical or financial application, it looks up best practices first — it doesn't guess.

**Graceful failure acknowledgment**
If validation cannot resolve an issue after 3 attempts, the error is logged to `KNOWN_ISSUES.md` and the pipeline proceeds. The system completes predictably — it never hangs, never silently fails, and never pretends an unresolved issue doesn't exist.

<br/>

## Dual-Layer Architecture

AURORA operates across two distinct environments.

<div align="center">

| Dimension | Orchestration Layer | Generated Target Layer |
|:----------|:-------------------|:----------------------|
| What it is | The AURORA agent pipeline | The application being built |
| Environment | Claude Code agent runtime | Next.js 14 App Router |
| Language | Agent instructions + bash | TypeScript (strict mode) |
| State | Stateless — chained commands | Persistent — SQLite via Prisma |
| Validation target | Phase handshake completion | `npm run build` exit code 0 |
| Failure handling | Retry + `KNOWN_ISSUES.md` | TypeScript compiler errors |
| Artifact produced | `BLUEPRINT.md`, phase outputs | `app/` — working application |

</div>

<br/>

## Operational Constraints

<div align="center">

| Constraint | Purpose |
|:-----------|:--------|
| No placeholder code | Every generated line must be functional — no `// TODO`, no stubs |
| No validation skipping | Each phase must complete before the next begins |
| No uncontrolled file generation | Only files in `BLUEPRINT.md` get created |
| No persistent server execution | Prevents pipeline deadlock from hanging processes |
| No inter-phase reinterpretation | Agents consume artifacts — they do not re-derive requirements |
| Bounded retry execution | 3 attempts per command — no runaway correction loops |
| Stateless shell handling | All commands use explicit `cd app &&` path chaining |

</div>

<br/>

## What This System Is Not

<div align="center">

| Not designed for | Reason |
|:----------------|:-------|
| Infinite autonomous execution | Execution is bounded by design — not by limitation |
| Unrestricted file generation | Blueprint contract enforced at every phase |
| Production deployment | Focus is application generation and build verification |
| Self-modification | Deterministic behavior requires stable orchestration instructions |
| General-purpose agent | Stack and patterns are fixed for reproducibility |

</div>

<br/>

## What AURORA Produces

<div align="center">

| Output | Description |
|:-------|:------------|
| `BLUEPRINT.md` | Structured app plan — schema, routes, pages, file tree |
| `app/` | Complete Next.js 14 application, scaffolded and wired |
| `app/prisma/schema.prisma` | Database schema matching blueprint exactly |
| `app/src/app/api/` | All API route handlers |
| `app/src/app/` + `components/` | All frontend pages and components |
| `app/prisma/seed.ts` | Realistic seed data — minimum 5 records per table |
| `app/README.md` | Project documentation with exact setup commands |
| `KNOWN_ISSUES.md` | Honest log of unresolved build issues (if any) |

</div>

<br/>

## Usage

```bash
git clone https://github.com/AKSINGH-0704/aurora-autonomous-dev-system.git
cd aurora-autonomous-dev-system
claude
```

Then enter your prompt:

```
Build a role-based telemedicine portal where doctors can manage appointments
and patients can securely submit medical histories, with a dashboard
showing appointment statistics and status tracking.
```

AURORA handles everything from here. Typical execution: ~9 minutes from prompt to working application. Do not interrupt. When complete, run the startup command from the completion summary:

```bash
cd app && npm install && npx prisma db push && npx prisma db seed && npm run dev
```

<br/>

## Engineering Takeaways

- Artifact contracts between phases eliminated the most common failure mode in LLM code generation — context drift causing mismatched assumptions between planning and implementation
- Bounded retry semantics with explicit failure logging produced more reliable pipeline completion than open-ended correction loops
- Stateless shell execution was a non-obvious constraint requiring a specific architectural solution — not all agentic environments behave like persistent terminals
- TypeScript strict mode functions as a secondary validator — type errors surfaced in Phase 3 that would have been silent runtime failures in a loosely-typed setup
- Fixing the tech stack per-run reduced error entropy significantly — the validator encounters the same error patterns across projects, making corrections more accurate

<br/>

## Project Structure

<details>
<summary>View repository structure</summary>

```
aurora-autonomous-dev-system/
│
├── claude.md                        # Master orchestrator — pipeline logic and hard constraints
├── README.md
│
└── .claude/
    ├── settings.json                # Permissions, auto-approvals, MCP configuration
    └── skills/
        ├── 01-planner/SKILL.md      # Requirement analysis → BLUEPRINT.md
        ├── 02-builder/SKILL.md      # Full-stack code generation from blueprint
        ├── 03-validator/SKILL.md    # Self-healing build validation loop
        └── 04-finalizer/SKILL.md    # Seed data, documentation, final verification
```

No application code lives in this repository. Every application is generated autonomously at runtime.

</details>

<br/>

---

<div align="center">

<br/>

Built by **[AKSINGH-0704](https://github.com/AKSINGH-0704)**

*AURORA doesn't generate code. It engineers software.*

<br/>

![MIT License](https://img.shields.io/badge/License-MIT-30363d?style=flat-square)

**Built for:** Claude Builders Club × APOGEE 2026

<br/>

</div>
