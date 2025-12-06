# SKILLS.md — ONE_SHOT Skills Companion

**Version:** 2.3
**Companion To:** `ONE_SHOT.md v3.1`
**URL:** `https://raw.githubusercontent.com/Khamel83/secrets-vault/master/SKILLS.md`
**Last Updated:** 2024-12-06
**Purpose:** Central registry of reusable Claude Code skills for ONE_SHOT projects.

This file is the **skills appendix**: everything here is *optional*, but when a task matches a skill's trigger, agents should **route to the appropriate skill** instead of reinventing workflows.

---

# THE BUILD ALGORITHM

**The core insight: 21 modular skills can build almost ANY project.**

Instead of reinventing workflows, compose skills like LEGO blocks. This section is a **mini ONE_SHOT** specifically for skill-based builds.

## The Universal Build Formula

```
BUILD = Initialize → Plan → Implement → Test → Document → Deploy
```

Every project follows this pattern. The only variables are:
- Which skills to use at each phase
- How deep to go (micro vs heavy)

## Skill Composition Matrix

| Phase | Skills Used | Output |
|-------|-------------|--------|
| **1. Initialize** | `project-initializer`, `secrets-vault-manager` | Skeleton + secrets |
| **2. Plan** | `feature-planner`, `api-designer` | PRD + feature plans |
| **3. Implement** | `designer`, `database-migrator`, `docker-composer` | Working code |
| **4. Test** | `test-runner`, `debugger`, `code-reviewer` | Passing tests |
| **5. Document** | `documentation-generator` | README + LLM-OVERVIEW |
| **6. Deploy** | `ci-cd-setup`, `git-workflow` | CI pipeline + PR |
| **Ongoing** | `refactorer`, `performance-optimizer`, `dependency-manager`, `oneshot-doctor` | Maintenance |

## The Algorithm: BUILD_WITH_SKILLS

```yaml
algorithm: BUILD_WITH_SKILLS
version: "1.0"
input: "User request (vague or detailed)"
output: "Working project with docs and CI"

steps:
  # ─────────────────────────────────────────────────────────────
  # STEP 0: TRIAGE (What does the user actually need?)
  # ─────────────────────────────────────────────────────────────
  - id: triage
    action: "Classify user intent"
    options:
      build_new: "Go to step 1"
      fix_existing: "Skip to debugger skill"
      add_feature: "Skip to feature-planner skill"
      understand: "Just read and explain, no skills needed"
      quick_task: "Single skill, no orchestration"

  # ─────────────────────────────────────────────────────────────
  # STEP 1: INITIALIZE (5 min)
  # ─────────────────────────────────────────────────────────────
  - id: initialize
    skills: [project-initializer, secrets-vault-manager]
    questions:
      - "What are we building? (one sentence)"
      - "What problem does it solve?"
      - "CLI, API, Web, or Script?"
    outputs:
      - "Directory skeleton"
      - ".oneshot/checkpoint.yaml"
      - ".env from secrets-vault"
    checkpoint: "Phase 0 complete"

  # ─────────────────────────────────────────────────────────────
  # STEP 2: PLAN (10 min)
  # ─────────────────────────────────────────────────────────────
  - id: plan
    skills: [feature-planner, api-designer]
    questions:
      - "What are the 3-5 core features?"
      - "What does 'done' look like for v1?"
    outputs:
      - "PRD.md with feature breakdown"
      - "API spec (if applicable)"
      - "Ordered task list"
    checkpoint: "PRD approved"
    gate: "User must say 'PRD approved' before proceeding"

  # ─────────────────────────────────────────────────────────────
  # STEP 3: IMPLEMENT (autonomous - hours)
  # ─────────────────────────────────────────────────────────────
  - id: implement
    skills: [designer, database-migrator, docker-composer]
    mode: "autonomous"
    loop:
      for_each: "task in PRD.tasks"
      do:
        - "Implement task"
        - "Write tests for task"
        - "Update checkpoint"
        - "Commit with git-workflow"
    outputs:
      - "Working code in src/"
      - "Tests in tests/"
      - "Docker setup (if needed)"
    checkpoint: "Core implementation complete"

  # ─────────────────────────────────────────────────────────────
  # STEP 4: VALIDATE (autonomous)
  # ─────────────────────────────────────────────────────────────
  - id: validate
    skills: [test-runner, debugger, code-reviewer]
    loop:
      - "Run test-runner"
      - "If failures: use debugger to fix"
      - "Run code-reviewer on changed files"
      - "Fix any critical issues"
      - "Repeat until all tests pass"
    gate: "All tests must pass before proceeding"
    checkpoint: "Tests passing"

  # ─────────────────────────────────────────────────────────────
  # STEP 5: DOCUMENT (autonomous)
  # ─────────────────────────────────────────────────────────────
  - id: document
    skills: [documentation-generator]
    outputs:
      - "README.md updated"
      - "LLM-OVERVIEW.md current"
      - "ADRs for major decisions"
    checkpoint: "Docs complete"

  # ─────────────────────────────────────────────────────────────
  # STEP 6: SHIP (autonomous)
  # ─────────────────────────────────────────────────────────────
  - id: ship
    skills: [ci-cd-setup, git-workflow, oneshot-doctor]
    outputs:
      - ".github/workflows/ci.yml"
      - "Clean git history"
      - "Health check passing"
    final_gate: "User reviews and merges PR"

  # ─────────────────────────────────────────────────────────────
  # ONGOING: MAINTAIN
  # ─────────────────────────────────────────────────────────────
  - id: maintain
    trigger: "User returns with changes"
    skills: [refactorer, performance-optimizer, dependency-manager, oneshot-doctor]
    note: "These skills keep the project healthy over time"
```

## Quick Reference: Which Skills When?

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        SKILL SELECTION FLOWCHART                        │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  "I want to build something new"                                        │
│   └─→ project-initializer → feature-planner → [implement loop]          │
│                                                                         │
│  "It's broken / not working"                                            │
│   └─→ debugger → test-runner → git-workflow                             │
│                                                                         │
│  "Add this feature"                                                     │
│   └─→ feature-planner → [implement] → test-runner → documentation       │
│                                                                         │
│  "Make it look good"                                                    │
│   └─→ designer                                                          │
│                                                                         │
│  "Set up the database"                                                  │
│   └─→ database-migrator                                                 │
│                                                                         │
│  "Dockerize this"                                                       │
│   └─→ docker-composer                                                   │
│                                                                         │
│  "Set up CI/CD"                                                         │
│   └─→ ci-cd-setup                                                       │
│                                                                         │
│  "Review this code"                                                     │
│   └─→ code-reviewer                                                     │
│                                                                         │
│  "Update the docs"                                                      │
│   └─→ documentation-generator                                           │
│                                                                         │
│  "Clean up / refactor"                                                  │
│   └─→ refactorer                                                        │
│                                                                         │
│  "It's slow"                                                            │
│   └─→ performance-optimizer                                             │
│                                                                         │
│  "Update dependencies"                                                  │
│   └─→ dependency-manager                                                │
│                                                                         │
│  "Check project health"                                                 │
│   └─→ oneshot-doctor                                                    │
│                                                                         │
│  "Commit / PR"                                                          │
│   └─→ git-workflow                                                      │
│                                                                         │
│  "Set up secrets"                                                       │
│   └─→ secrets-vault-manager                                             │
│                                                                         │
│  "Design the API"                                                       │
│   └─→ api-designer                                                      │
│                                                                         │
│  "Create a new skill"                                                   │
│   └─→ skill-creator                                                     │
│                                                                         │
│  "Find a skill for X"                                                   │
│   └─→ marketplace-browser                                               │
│                                                                         │
│  "Deploy to cloud" / "Host this publicly"                               │
│   └─→ push-to-cloud                                                     │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

## Build Recipes: Common Project Types

### Recipe: CLI Tool
```yaml
skills_sequence:
  1. project-initializer (CLI mode)
  2. feature-planner
  3. [implement core logic]
  4. test-runner
  5. documentation-generator
  6. git-workflow
time: "1-4 hours"
```

### Recipe: REST API
```yaml
skills_sequence:
  1. project-initializer (API mode)
  2. api-designer
  3. database-migrator
  4. [implement endpoints]
  5. test-runner
  6. docker-composer
  7. ci-cd-setup
  8. documentation-generator
time: "4-8 hours"
```

### Recipe: Static Website
```yaml
skills_sequence:
  1. project-initializer (Web mode)
  2. designer
  3. [implement pages]
  4. ci-cd-setup (deploy to GitHub Pages)
  5. documentation-generator
time: "1-2 hours"
```

### Recipe: Full-Stack Web App
```yaml
skills_sequence:
  1. project-initializer (Heavy mode)
  2. feature-planner
  3. api-designer
  4. database-migrator
  5. designer (frontend)
  6. [implement backend + frontend]
  7. test-runner
  8. docker-composer
  9. ci-cd-setup
  10. documentation-generator
  11. secrets-vault-manager
time: "8-24 hours"
```

### Recipe: Bug Fix
```yaml
skills_sequence:
  1. debugger (find root cause)
  2. [apply fix]
  3. test-runner (verify fix)
  4. code-reviewer (self-review)
  5. git-workflow (commit + PR)
time: "15 min - 2 hours"
```

### Recipe: Refactor Session
```yaml
skills_sequence:
  1. oneshot-doctor (health check first)
  2. test-runner (ensure coverage)
  3. refactorer (apply patterns)
  4. test-runner (verify no regression)
  5. code-reviewer
  6. documentation-generator (update if API changed)
  7. git-workflow
time: "1-4 hours"
```

## The Meta-Rule

> **When in doubt, ask: "Which skill handles this?"**
>
> If none fit → you're doing something novel → document it → maybe create a new skill.

---

## 0. HOW AGENTS SHOULD USE THIS FILE

### 0.1 Routing Layer

When a user asks for help inside a ONE_SHOT project:

1. **Run TRIAGE** (Section 0.0 of `ONE_SHOT.md`).
2. If TRIAGE result is **build_new / modify / continue_work / fix_existing**, then:
   - Check whether the request matches any **skill trigger** below.
   - If yes:
     - Announce internally: "Using skill: `[SKILL_NAME]`".
     - Follow that skill's **Workflow** and **Output Contract**.
   - If no:
     - Fall back to the core ONE_SHOT spec.

### 0.2 Skill Metadata Convention

Each skill defines:

| Field | Description |
|-------|-------------|
| `name` | Canonical identifier |
| `intent` | What this skill is for |
| `when_to_use` | Trigger phrases / situations |
| `allowed_tools` | Claude Code tools this skill may call |
| `inputs` | What the skill expects from user / repo |
| `outputs` | What the skill must produce |
| `workflow` | Step-by-step protocol |
| `anti_patterns` | Things this skill must NOT do |

### 0.3 Priority vs ONE_SHOT Core

- **ONE_SHOT.md** governs:
  - Intake → PRD → Autonomous build
  - Triage, reality check, storage discipline, etc.
- **SKILLS.md** governs:
  - *How* to execute recurring patterns once overall flow is chosen.

**If there is a conflict, ONE_SHOT.md wins.**

---

## 1. GLOBAL SKILL CONVENTIONS

All skills in this file follow these rules:

1. **Idempotence where possible**
   Running the same skill twice should not corrupt the repo. If effects aren't idempotent (e.g., git push), that must be explicit.

2. **Minimal surface area**
   Skills should:
   - Use the smallest necessary toolset.
   - Limit scope to files and operations directly relevant to the task.

3. **Explicit outputs**
   Each skill documents exactly what it returns or writes:
   - e.g., "Creates/updates `PRD.md`", "Returns markdown plan", "Commits with message …".

4. **No hidden side effects**
   If a skill modifies files, commits, or runs migrations, this must be spelled out.

5. **ONE_SHOT awareness**
   Skills should:
   - Respect mode (Micro / Tiny / Normal / Heavy).
   - Respect storage rules (no surprise Postgres).
   - Update `LLM-OVERVIEW.md` and `.oneshot/checkpoint.yaml` when relevant.

---

## Quick Reference: All Skills

| # | Skill | Purpose | Trigger Keywords |
|---|-------|---------|------------------|
| 2 | **project-initializer** | Bootstrap new projects | "new project", "initialize", "start fresh" |
| 3 | **feature-planner** | Break down features into tasks | "plan feature", "break down", "create plan" |
| 4 | **git-workflow** | Conventional commits & PRs | "commit", "PR", "conventional commits" |
| 5 | **code-reviewer** | Code quality & security review | "review code", "security check", "PR review" |
| 6 | **documentation-generator** | READMEs, ADRs, API docs | "generate docs", "README", "ADR" |
| 7 | **secrets-vault-manager** | SOPS + Age secrets management | "secrets", "API keys", "environment variables" |
| 8 | **skill-creator** | Create new Claude Code skills | "create skill", "new skill", "automate workflow" |
| 9 | **marketplace-browser** | Find & install community skills | "find skill", "browse marketplace" |
| 10 | **oneshot-doctor** | Project health checks | "health check", "audit", "doctor" |
| 11 | **designer** | Create beautiful web designs | "design", "website", "UI", "landing page" |
| 12 | **debugger** | Systematic debugging | "debug", "not working", "error", "fix bug" |
| 13 | **test-runner** | Run and analyze tests | "run tests", "test coverage", "pytest", "jest" |
| 14 | **api-designer** | Design REST/GraphQL APIs | "design API", "endpoints", "REST", "GraphQL" |
| 15 | **database-migrator** | Schema changes & migrations | "migration", "schema change", "add column" |
| 16 | **performance-optimizer** | Profile and optimize | "slow", "performance", "optimize", "profile" |
| 17 | **dependency-manager** | Update and audit deps | "update deps", "security audit", "outdated" |
| 18 | **docker-composer** | Docker & compose setup | "dockerize", "container", "compose" |
| 19 | **ci-cd-setup** | GitHub Actions / CI pipelines | "CI", "GitHub Actions", "pipeline", "deploy" |
| 20 | **refactorer** | Code refactoring patterns | "refactor", "clean up", "extract", "rename" |
| 21 | **push-to-cloud** | Deploy to OCI-Dev cloud | "deploy", "push to cloud", "host this" |

---

# 2. SKILL: PROJECT-INITIALIZER

```yaml
name: project-initializer
intent: "Initialize a new ONE_SHOT project skeleton"
allowed_tools: [Bash, Read, Write, Git, Glob]
```

## 2.1 When To Use

- New repo or empty directory where user says:
  - "Initialize a new ONE_SHOT project"
  - "Set this up with ONE_SHOT"
  - "Scaffold the project according to ONE_SHOT"
- TRIAGE state = `build_new` + `new_project` / `existing_oneshot_fresh`.

## 2.2 Inputs

From Core Questions Q0–Q13 in ONE_SHOT.md:
- Mode: Tiny / Normal / Heavy
- Project type: Q6 (A–G)
- Naming: Q13
- Storage & scale: Q8–Q9
- Interface shape: Q11

## 2.3 Outputs

- Directory skeleton under `projects_root`:
  - `ONE_SHOT.md` (existing file – not rewritten)
  - `LLM-OVERVIEW.md`
  - `PRD.md` (initial version)
  - `README.md`
  - `.oneshot/` with `checkpoint.yaml` + `decisions.log`
  - `scripts/` with `setup.sh`, `start.sh`, `stop.sh`, `status.sh`
  - Language-specific files (e.g., `pyproject.toml` or `package.json`)
- Git is initialized with an initial commit:
  - Commit message: `chore(init): bootstrap ONE_SHOT project skeleton`

## 2.4 Workflow

1. **Environment detection**
   - Detect language/runtime based on project type + user preference:
     - CLI default: Python
     - Web default: FastAPI + Python
     - Static site: HTML/CSS/JS

2. **Create base directories**
   - At minimum: `src/`, `tests/`, `scripts/`, `.oneshot/`, `docs/`

3. **Write baseline files**
   - `LLM-OVERVIEW.md` using template in Section 17 of ONE_SHOT.md.
   - `PRD.md` using answers to Q0–Q13.
   - `README.md`: One-line description, Quick Start (≤5 commands), Current Tier + Upgrade triggers.
   - `.oneshot/checkpoint.yaml` initialized as in Section 7.1.
   - `.oneshot/decisions.log` with initial creation note.

4. **Language-specific init**
   - Python: `pyproject.toml` or `requirements.txt` with minimal deps.
   - Node: `package.json`, set up scripts, etc.

5. **Git initialization**
   - If `.git` not present:
     - `git init`
     - Create `.gitignore` (language-appropriate; includes `.env`).
     - `git add . && git commit -m "chore(init): bootstrap ONE_SHOT project skeleton"`

6. **Checkpoint update**
   - Update `.oneshot/checkpoint.yaml`:
     - `current_phase: "Phase 0: Repo & Skeleton"`
     - `completion_percentage` set appropriately (e.g., 80–100% for Phase 0).

## 2.5 Anti-Patterns

- Do not overwrite an existing non-empty `src/` without explicit instruction.
- Do not install heavy optional deps (Postgres, Redis, Celery, etc.) unless demanded by PRD.
- Do not create Kubernetes manifests for Normal/Tiny projects.

---

# 3. SKILL: FEATURE-PLANNER

```yaml
name: feature-planner
intent: "Turn a feature request into a concrete, ordered implementation plan"
allowed_tools: [Read, Write, Glob]
```

## 3.1 When To Use

- User says:
  - "Plan this feature"
  - "Break this down"
  - "Can you create a plan for X?"
- TRIAGE intent = `modify_existing` or `build_new` with a specific feature.
- Scope = small, medium, or large, not a trivial micro change.

## 3.2 Inputs

- Natural language feature request from user.
- Existing: `PRD.md`, `LLM-OVERVIEW.md`, any relevant code in `src/`.

## 3.3 Outputs

- A markdown feature plan appended to:
  - Either `docs/features/[feature-name].md`, or
  - A `## Feature Plan: [Feature Name]` section in `PRD.md`.
- Contains: Overview, components, ordered tasks, dependencies, risks, tests, success criteria.

## 3.4 Workflow

1. **Parse request** - Extract: user persona, problem, expected behavior, constraints.
2. **Check PRD** - Confirm feature fits existing scope. If it expands scope materially, update PRD first.
3. **Decompose feature** - Components: API, storage, UI, background jobs, etc. Tasks: break down into 0.5–2 hr chunks.
4. **Order by dependencies** - Data model → storage → core logic → interface → tests → docs.
5. **Risk + testing** - Identify risks (security, data migration). Define test strategy.
6. **Write plan** - Use canonical template.
7. **Checkpoint** - Add note to `.oneshot/checkpoint.yaml` with feature name, status: planned, pointer to plan.

## 3.5 Output Template

```markdown
# Feature Plan: [Feature Name]

## Overview
[What this feature does and why]

## Components
1. [Component 1]
2. [Component 2]

## Tasks (ordered)
- [ ] Task 1 (prerequisite for 2, 3)
- [ ] Task 2 (depends on 1)
- [ ] Task 3 (depends on 1)

## Dependencies
- External: [APIs, libraries]
- Internal: [Other features, services]

## Risks
- Risk 1: [Description] → Mitigation: [How to handle]

## Testing Strategy
- Unit tests for [X]
- Integration tests for [Y]
- Manual testing for [Z]

## Success Criteria
- [ ] Criterion 1
- [ ] Criterion 2
```

## 3.6 Anti-Patterns

- Turning this into a full implementation.
- Skipping PRD update when feature crosses boundaries (e.g., adding auth).

---

# 4. SKILL: GIT-WORKFLOW

```yaml
name: git-workflow
intent: "Handle branches, commits, and PR prep using conventional commits"
allowed_tools: [Bash, Git]
```

## 4.1 When To Use

- User asks:
  - "Commit these changes"
  - "Prepare a PR"
  - "Clean up my git history"
- Prior to merging non-trivial work.

## 4.2 Inputs

- Git repository with changes.
- Optional user summary of what changed.

## 4.3 Outputs

- Git branch created/updated as needed.
- Commits with conventional commit messages.
- Optional PR description in markdown (not auto-submitted, just generated).

## 4.4 Workflow

1. **Status & diff** - Run `git status` and `git diff`. Group changes logically (docs vs code vs tests).
2. **Branch strategy** - If on main or master, create feature branch: `feature/<short-desc>` or `fix/<short-desc>`.
3. **Stage changes** - Stage by concern: code, tests, docs, config, etc.
4. **Commit types** - Map changes to types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `ci`, `perf`.
5. **Create commits** - Use appropriate messages, e.g.:
   - `feat(core): add schedule parsing for custody calendar`
   - `fix(api): handle empty payload in /import endpoint`
6. **PR template** (if requested) - Generate markdown: Summary, changes, testing, screenshots, risks.

## 4.5 Conventional Commits Reference

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**Types:**
| Type | Description |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, no code change |
| `refactor` | Code change without feature/fix |
| `test` | Adding tests |
| `chore` | Maintenance tasks |
| `perf` | Performance improvement |
| `ci` | CI/CD changes |

## 4.6 Anti-Patterns

- Single giant commit labeled `misc`.
- Rewriting public history without explicit request.

---

# 5. SKILL: CODE-REVIEWER

```yaml
name: code-reviewer
intent: "Perform a structured code review (quality + security) before merge"
allowed_tools: [Read, Glob]
```

## 5.1 When To Use

- User says:
  - "Review this change/PR"
  - "Is this safe / okay to merge?"
- There is a non-trivial diff affecting logic, security, or data flow.

## 5.2 Inputs

- Diff or list of modified files.
- Context from `PRD.md` and `LLM-OVERVIEW.md` for intent.

## 5.3 Outputs

- Markdown review with: Critical issues, Important suggestions, Minor improvements, Positive notes.

## 5.4 Workflow

1. **Context load** - Identify what the change intends to do. Cross-check with PRD / feature plan.
2. **Pass 1: Safety & correctness** - Look for: off-by-one, unhandled None, error paths, data leaks, unsafe logs, injection risks, auth bypasses.
3. **Pass 2: Design & maintainability** - Over-engineering vs under-structuring. Duplicated logic. Violation of project conventions.
4. **Pass 3: Tests & docs** - Are there tests? Do they cover critical paths? Are docs updated?
5. **Write review** - Use structured template.

## 5.5 Review Checklist

**Code Quality:**
- [ ] DRY - No repeated code blocks
- [ ] KISS - Simplest solution that works
- [ ] YAGNI - No speculative features

**Security (OWASP Top 10):**
- [ ] A01: Broken Access Control
- [ ] A02: Cryptographic Failures
- [ ] A03: Injection (SQL, Command, XSS)
- [ ] A04: Insecure Design
- [ ] A05: Security Misconfiguration
- [ ] A06: Vulnerable Components
- [ ] A07: Authentication Failures
- [ ] A08: Software/Data Integrity Failures
- [ ] A09: Security Logging Failures
- [ ] A10: SSRF

## 5.6 Output Template

```markdown
## Code Review: [File/PR Name]

### Critical Issues
- **[Location]**: [Issue description]
  - **Why it matters**: [Impact]
  - **Fix**: [How to resolve]

### Important Suggestions
- **[Location]**: [Suggestion]
  - **Benefit**: [Why this helps]

### Minor Improvements
- [Improvement 1]
- [Improvement 2]

### What's Good
- [Positive observation 1]
- [Positive observation 2]
```

## 5.7 Anti-Patterns

- Nitpicking style when there are deeper correctness issues.
- Rewriting code in the review instead of commenting on it.

---

# 6. SKILL: DOCUMENTATION-GENERATOR

```yaml
name: documentation-generator
intent: "Generate or update project documentation (README, ADRs, API docs, LLM-OVERVIEW)"
allowed_tools: [Read, Write, Glob]
```

## 6.1 When To Use

- After major feature / milestone completion.
- User asks: "Update docs for this", "Write the README / ADR / API docs"
- Before handing off to another engineer/agent.

## 6.2 Inputs

- Current code base.
- Existing docs (if any).
- PRD + feature plan(s).

## 6.3 Outputs

- Updated:
  - `README.md` (Quick Start, features, limitations).
  - `LLM-OVERVIEW.md` (architecture, current state).
  - Optional new ADR(s) for decisions.

## 6.4 Workflow

1. **Scan project** - Identify main entry points. List main modules and responsibilities.
2. **Update README** - Ensure: One-line description, Problem / solution, Quick Start, Storage choice & upgrade trigger, Known limitations.
3. **Update LLM-OVERVIEW** - Ensure reality matches code: Status, current state, architecture decisions.
4. **ADRs for major decisions** - For each big decision (e.g., "we stayed on SQLite"): Write ADR using template.

## 6.5 ADR Template (Nygard Format)

```markdown
# ADR-[NUMBER]: [Title]

**Date**: YYYY-MM-DD
**Status**: [Proposed | Accepted | Deprecated | Superseded]

## Context
[What is the issue we're facing?]

## Decision
[What is the change we're proposing?]

## Rationale
[Why is this the best approach?]

## Consequences

### Positive
- [Benefit 1]

### Negative
- [Trade-off 1]

## Alternatives Considered
- Alternative 1: [Description] - Rejected because [reason]
```

## 6.6 Anti-Patterns

- Generating generic docs that don't match the code.
- Duplicating content instead of pointing to canonical sections.

---

# 7. SKILL: SECRETS-VAULT-MANAGER

```yaml
name: secrets-vault-manager
intent: "Handle SOPS + Age secrets for ONE_SHOT projects"
allowed_tools: [Bash, Read, Write]
```

## 7.1 When To Use

- Project needs API keys / secrets.
- User mentions: "Set up secrets-vault", "Decrypt / refresh secrets", "Add a new secret"

## 7.2 Inputs

- `oneshot_env` config from ONE_SHOT_CONTRACT header.
- Access to secrets-vault repo (if configured).

## 7.3 Outputs

- `.env` created or refreshed (gitignored).
- Documentation updated: README "Secrets" section.
- `secrets.env.encrypted` updated in vault when adding new secrets.

## 7.4 Workflow

1. **Detect vault** - From `oneshot_env.secrets_vault_path`. If missing, ask user if they want per-project SOPS instead.
2. **Decrypt for local use** - Run `sops --decrypt` into `.env`. Verify `.env` is gitignored.
3. **Add/modify secrets** (if requested) - Edit `secrets.env.encrypted` via `sops`. Commit & push to vault.
4. **Document** - In README: how to sync secrets into `.env`.

## 7.5 Common Operations

```bash
# Initial setup
sops --decrypt ~/github/secrets-vault/secrets.env.encrypted > .env
source .env

# Refresh secrets
sops --decrypt ~/github/secrets-vault/secrets.env.encrypted > .env

# Add new secret
cd ~/github/secrets-vault
sops secrets.env.encrypted
# Add new line: NEW_SECRET=value
# Save and exit
git add . && git commit -m "Add NEW_SECRET" && git push
```

## 7.6 Anti-Patterns

- Ever committing `.env` or raw secrets.
- Decrypting into tracked files.

---

# 8. SKILL: SKILL-CREATOR

```yaml
name: skill-creator
intent: "Design and scaffold new Claude Code skills for recurring workflows"
allowed_tools: [Read, Write, Glob]
```

## 8.1 When To Use

- You identify a workflow you repeat across repos.
- User says: "Make this a reusable skill", "I keep doing X; make a skill for it"

## 8.2 Inputs

- Description of workflow.
- One or two concrete examples.

## 8.3 Outputs

- New skill directory under `.claude/skills/<skill-name>/`:
  - `SKILL.md` with YAML frontmatter, When-to-use, Workflow, Output format
  - Optional templates or helper files.

## 8.4 Workflow

1. **Abstract workflow** - Identify: Invariants, Inputs, Outputs, Variable parts.
2. **Define clear trigger** - "When the user says…" or "When repository has pattern…"
3. **Write SKILL.md** - Using the canonical template.
4. **Optional: Self-test** - Run through the skill manually on a small example.

## 8.5 SKILL.md Template

```markdown
---
name: skill-name
description: Brief description of what this skill does
version: "1.0.0"
allowed-tools: [Bash, Read, Write, Grep, Glob]
---

# Skill Name

You are an expert at [domain].

## When to Use This Skill
- Trigger condition 1
- Trigger condition 2

## Workflow
1. Step 1
2. Step 2
3. Step 3

## Output Format
[Expected output format]

## Keywords
keyword1, keyword2, keyword3
```

## 8.6 Anti-Patterns

- Skills that are too vague: "do useful things with git."
- Skills that bake in project-specific paths or constants.

---

# 9. SKILL: MARKETPLACE-BROWSER

```yaml
name: marketplace-browser
intent: "Discover & install external Claude Code skills from public sources"
allowed_tools: [Bash, Read, Write]
```

## 9.1 When To Use

- User asks: "Is there a skill for X?", "Browse skills for Y"
- You suspect an existing community skill might be better.

## 9.2 Inputs

- Desired capability (e.g., "storybook scaffolding", "pytest runner").
- Constraints (security, license, FOSS only, etc.).

## 9.3 Outputs

- Short list of potential skills with: Name, source, description, safety notes.
- Optional: Installed skill under `.claude/skills/…` (if user approves).

## 9.4 Workflow

1. **Clarify capability** - What specifically should the skill do? How often will it run? Any security constraints?
2. **Search external sources** - Public skill registries / GitHub repos:
   - skillsmp.com
   - obra/claude-code-skills
   - levnikolaevich/claude-code-skills
   - mhattingpete/claude-skills
   - OneRedOak/claude-code-prompt-repo
   - WSHobson/claude-skill-builder
3. **Filter** - Prefer: Clear docs, Minimal permissions, Active maintenance (recent commits).
4. **Propose** - Present 2–5 candidate skills with pros/cons. Ask user before copying anything into repo.
5. **Install** (if approved) - Copy skill into `.claude/skills/…`. Add local CLAUDE.md note explaining usage.

## 9.5 Anti-Patterns

- Blindly installing random skills without explaining what they do.
- Mixing untrusted code into security-sensitive repos without flagging risk.

---

# 10. SKILL: ONESHOT-DOCTOR

```yaml
name: oneshot-doctor
intent: "Run the project health checks defined in ONE_SHOT and interpret results"
allowed_tools: [Bash, Read]
```

## 10.1 When To Use

- Before big refactors or deploys.
- After migrating or re-cloning the project.
- On user request: "Run a health check / audit the project."

## 10.2 Inputs

- Presence of `scripts/oneshot_doctor.sh`.

## 10.3 Outputs

- Executed doctor script.
- Interpreted summary (errors vs warnings vs OK).
- Suggested next actions.

## 10.4 Workflow

1. **Check for script** - If missing, offer to generate it from Section 5.3 in ONE_SHOT.md.
2. **Run script** - `./scripts/oneshot_doctor.sh`.
3. **Parse output** - Count errors/warnings. Identify missing core files (LLM-OVERVIEW, PRD, scripts, .oneshot).
4. **Write summary** - What passed. What failed. Concrete fix list.

## 10.5 Health Check Items

```bash
# Required files
ONE_SHOT.md, README.md, LLM-OVERVIEW.md

# Required directories
scripts/, .oneshot/

# Required scripts
scripts/setup.sh, scripts/start.sh, scripts/stop.sh, scripts/status.sh

# Git checks
.gitignore exists, .env is gitignored, no secrets in tracked files

# Checkpoint
.oneshot/checkpoint.yaml exists and is valid YAML
```

## 10.6 Anti-Patterns

- Treating warnings as ignorable without explanation.
- Modifying code directly as part of this skill—doctor is diagnostics, not surgery.

---

# 11. SKILL: DESIGNER

```yaml
name: designer
intent: "Create beautiful, modern web designs with clean HTML/CSS"
allowed_tools: [Read, Write]
```

## 11.1 When To Use

- User asks to design a website, landing page, or UI
- User wants to create a portfolio, blog, or marketing page
- User mentions "make it look good" or "professional design"
- User needs a quick static site

## 11.2 Design Philosophy

**Core Principles:**
1. **Clean, minimal aesthetics** - Whitespace is your friend
2. **Mobile-first responsive** - Works on all devices
3. **Fast loading** - No heavy frameworks unless needed
4. **Accessibility** - Semantic HTML, proper contrast, keyboard navigation
5. **Modern CSS** - Flexbox, Grid, custom properties

## 11.3 Design System Defaults

```css
:root {
  /* Colors */
  --primary: #2563eb;
  --primary-dark: #1d4ed8;
  --text: #1f2937;
  --text-muted: #6b7280;
  --bg: #ffffff;
  --bg-alt: #f9fafb;
  --border: #e5e7eb;

  /* Typography */
  --font-sans: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
  --font-mono: 'JetBrains Mono', 'Fira Code', monospace;

  /* Spacing */
  --space-xs: 0.25rem;
  --space-sm: 0.5rem;
  --space-md: 1rem;
  --space-lg: 2rem;
  --space-xl: 4rem;

  /* Sizing */
  --max-width: 1200px;
  --content-width: 720px;

  /* Shadows */
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.05);
  --shadow-md: 0 4px 6px rgba(0,0,0,0.1);
  --shadow-lg: 0 10px 15px rgba(0,0,0,0.1);

  /* Transitions */
  --transition: 150ms ease;
}
```

## 11.4 Color Palette Presets

| Palette | Primary | Accent |
|---------|---------|--------|
| Professional Blue | `#2563eb` | `#3b82f6` |
| Warm Orange | `#ea580c` | `#f97316` |
| Forest Green | `#059669` | `#10b981` |
| Royal Purple | `#7c3aed` | `#8b5cf6` |
| Dark Mode | `--bg: #0f172a` | `--text: #f1f5f9` |

## 11.5 Common Components

**Hero Section:**
```html
<section class="hero">
  <div class="container">
    <h1 class="hero-title">Your Headline Here</h1>
    <p class="hero-subtitle">A compelling subtitle</p>
    <div class="hero-cta">
      <a href="#" class="btn btn-primary">Get Started</a>
      <a href="#" class="btn btn-secondary">Learn More</a>
    </div>
  </div>
</section>
```

**Feature Grid:**
```html
<section class="features">
  <div class="container">
    <h2 class="section-title">Features</h2>
    <div class="feature-grid">
      <div class="feature-card">
        <div class="feature-icon">🚀</div>
        <h3>Feature One</h3>
        <p>Description of this amazing feature.</p>
      </div>
    </div>
  </div>
</section>
```

## 11.6 Output Format

When creating designs, output:
1. **Single HTML file** with embedded CSS (for simple pages)
2. **Or separate files**: `index.html`, `styles.css`, `script.js`
3. **Include**: Responsive meta tags, Semantic HTML5, CSS custom properties, Smooth scroll

---

# 12. SKILL: DEBUGGER

```yaml
name: debugger
intent: "Systematically debug issues using isolation and evidence-based diagnosis"
allowed_tools: [Bash, Read, Glob, Grep]
```

## 12.1 When To Use

- User says: "This is broken", "Not working", "Getting an error", "Debug this"
- TRIAGE intent = `fix_existing`
- There's a reproducible issue to investigate

## 12.2 Inputs

- Error message or symptom description
- Steps to reproduce (if known)
- Recent changes (if known)

## 12.3 Outputs

- Root cause identification
- Fix applied or recommended
- Explanation of what went wrong

## 12.4 Workflow

1. **Gather symptoms**
   - What error message (exact text)?
   - What behavior is expected vs actual?
   - When did it last work?
   - What changed since then?

2. **Reproduce the issue**
   - Run the failing command/action
   - Capture full error output
   - Note environment details

3. **Isolate the layer**
   ```
   Network → Process → Config → Dependencies → Data → Code
   ```
   - Can you reach the service?
   - Is the process running?
   - Any recent config changes?
   - All dependencies up?
   - Database accessible?
   - Logic error in code?

4. **Form hypothesis**
   - Based on evidence, what's the most likely cause?
   - What's the simplest test to confirm/refute?

5. **Test hypothesis**
   - Make minimal change to test
   - Observe result
   - If wrong, return to step 3

6. **Apply fix**
   - Make the smallest change that fixes the issue
   - Do NOT refactor while debugging

7. **Verify and document**
   - Confirm fix works
   - Add to `.oneshot/decisions.log`: what broke and why

## 12.5 Debug Commands Cheatsheet

```bash
# Check if service is running
docker ps | grep <service>
systemctl status <service>

# Check logs
docker logs <container> --tail 100
journalctl -u <service> -n 100

# Check network
curl -v http://localhost:<port>/health
nc -zv <host> <port>

# Check disk
df -h
du -sh *

# Check memory
free -m
top -bn1 | head -20
```

## 12.6 Anti-Patterns

- Making multiple changes at once (can't isolate which fixed it)
- Refactoring while debugging
- Assuming the cause without evidence
- Ignoring error messages

---

# 13. SKILL: TEST-RUNNER

```yaml
name: test-runner
intent: "Run tests, analyze results, and improve coverage"
allowed_tools: [Bash, Read, Write]
```

## 13.1 When To Use

- User says: "Run tests", "Check if tests pass", "What's the coverage?"
- Before commits or PRs
- After implementing a feature

## 13.2 Inputs

- Test framework in use (pytest, jest, go test, etc.)
- Optional: specific test files or patterns

## 13.3 Outputs

- Test results summary
- Coverage report (if requested)
- List of failing tests with analysis

## 13.4 Workflow

1. **Detect test framework**
   ```
   pytest.ini / pyproject.toml → pytest
   package.json with jest → jest
   *_test.go → go test
   Cargo.toml → cargo test
   ```

2. **Run tests**
   ```bash
   # Python
   pytest -v --tb=short

   # Node
   npm test

   # Go
   go test ./...

   # Rust
   cargo test
   ```

3. **Analyze failures**
   - For each failure: file, test name, assertion, expected vs actual
   - Categorize: logic error, missing mock, flaky test, environment issue

4. **Coverage** (if requested)
   ```bash
   # Python
   pytest --cov=src --cov-report=term-missing

   # Node
   npm test -- --coverage
   ```

5. **Report**
   - Total: X passed, Y failed, Z skipped
   - Coverage: X% (target: 80%)
   - Failures needing attention: [list]

## 13.5 Anti-Patterns

- Ignoring flaky tests
- Deleting failing tests instead of fixing them
- Testing implementation details instead of behavior

---

# 14. SKILL: API-DESIGNER

```yaml
name: api-designer
intent: "Design RESTful or GraphQL APIs with proper conventions"
allowed_tools: [Read, Write]
```

## 14.1 When To Use

- User says: "Design the API", "What endpoints do we need?", "Create API spec"
- New feature requires API endpoints
- Refactoring existing API

## 14.2 Inputs

- Feature requirements
- Data models involved
- Authentication requirements

## 14.3 Outputs

- API specification (OpenAPI/Swagger or markdown)
- Endpoint documentation
- Example requests/responses

## 14.4 REST Conventions

| Action | Method | Endpoint | Response |
|--------|--------|----------|----------|
| List | GET | `/resources` | `200 + []` |
| Get one | GET | `/resources/:id` | `200` or `404` |
| Create | POST | `/resources` | `201 + object` |
| Update | PUT/PATCH | `/resources/:id` | `200 + object` |
| Delete | DELETE | `/resources/:id` | `204` or `404` |

## 14.5 Workflow

1. **Identify resources** - What entities does this feature involve?
2. **Define operations** - CRUD? Custom actions?
3. **Design URLs** - Use nouns, not verbs. Nest appropriately.
4. **Define request/response** - Fields, types, required vs optional
5. **Error handling** - Standard error format, appropriate status codes
6. **Document** - OpenAPI spec or markdown

## 14.6 Standard Error Format

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": [
      {"field": "email", "message": "Invalid email format"}
    ]
  }
}
```

## 14.7 Anti-Patterns

- Verbs in URLs (`/getUser`, `/createOrder`)
- Inconsistent response formats
- Missing pagination on list endpoints
- No versioning strategy

---

# 15. SKILL: DATABASE-MIGRATOR

```yaml
name: database-migrator
intent: "Handle schema changes and data migrations safely"
allowed_tools: [Bash, Read, Write]
```

## 15.1 When To Use

- Adding/removing/modifying database columns
- Creating new tables
- Data transformations needed
- User says: "Add this field", "Change the schema"

## 15.2 Inputs

- Current schema
- Desired change
- Data preservation requirements

## 15.3 Outputs

- Migration file(s)
- Rollback strategy
- Updated schema documentation

## 15.4 Workflow

1. **Analyze change**
   - Is this additive (safe) or destructive (risky)?
   - Any data to preserve or transform?
   - Downtime required?

2. **Generate migration**
   ```bash
   # Alembic (Python)
   alembic revision --autogenerate -m "add user preferences"

   # Prisma
   npx prisma migrate dev --name add_preferences

   # Raw SQL
   # Create timestamped file: migrations/20241206_add_preferences.sql
   ```

3. **Review migration**
   - Check generated SQL
   - Verify rollback works
   - Consider indexes

4. **Test locally**
   ```bash
   # Apply
   alembic upgrade head

   # Rollback
   alembic downgrade -1
   ```

5. **Document**
   - Update ERD if exists
   - Note in LLM-OVERVIEW.md

## 15.5 Safe Migration Patterns

| Change | Safe? | Notes |
|--------|-------|-------|
| Add nullable column | ✅ | No data change needed |
| Add column with default | ✅ | Backfills automatically |
| Remove unused column | ⚠️ | Verify no code references |
| Rename column | ❌ | Requires code coordination |
| Change column type | ❌ | May lose data |

## 15.6 Anti-Patterns

- Migrations without rollback plan
- Destructive changes without backup
- Long-running migrations during peak hours
- Not testing migrations locally first

---

# 16. SKILL: PERFORMANCE-OPTIMIZER

```yaml
name: performance-optimizer
intent: "Profile and optimize slow code, queries, or systems"
allowed_tools: [Bash, Read, Write, Glob]
```

## 16.1 When To Use

- User says: "This is slow", "Optimize this", "Performance issues"
- Response times exceed thresholds
- Resource usage is high

## 16.2 Inputs

- Slow operation or endpoint
- Current performance metrics (if available)
- Acceptable performance target

## 16.3 Outputs

- Profile results
- Identified bottlenecks
- Optimization recommendations or implementations

## 16.4 Workflow

1. **Measure baseline**
   - Current response time / throughput
   - Resource usage (CPU, memory, I/O)
   - Identify the specific slow operation

2. **Profile**
   ```bash
   # Python
   python -m cProfile -s cumtime script.py
   py-spy top --pid <pid>

   # Node
   node --prof app.js

   # Database
   EXPLAIN ANALYZE <query>;
   ```

3. **Identify bottleneck**
   - CPU bound? → Algorithm optimization
   - I/O bound? → Caching, batching, async
   - Memory bound? → Data structure changes
   - Database? → Query optimization, indexes

4. **Optimize**
   - Apply ONE change at a time
   - Measure after each change
   - Stop when target met

5. **Document**
   - What was slow and why
   - What fixed it
   - New performance baseline

## 16.5 Common Optimizations

| Problem | Solution |
|---------|----------|
| N+1 queries | Eager loading, JOINs |
| Full table scans | Add index |
| Repeated calculations | Caching |
| Synchronous I/O | Async/await, batching |
| Large payloads | Pagination, compression |

## 16.6 Anti-Patterns

- Premature optimization
- Optimizing without measuring
- Making multiple changes at once
- Sacrificing readability for micro-gains

---

# 17. SKILL: DEPENDENCY-MANAGER

```yaml
name: dependency-manager
intent: "Update, audit, and manage project dependencies"
allowed_tools: [Bash, Read, Write]
```

## 17.1 When To Use

- User says: "Update dependencies", "Security audit", "Check for outdated packages"
- Dependabot/Renovate alerts
- Before major releases

## 17.2 Inputs

- Package manifest (package.json, requirements.txt, Cargo.toml, etc.)
- Security requirements
- Update scope (patch/minor/major)

## 17.3 Outputs

- Updated lock file
- Security audit report
- Breaking change notes

## 17.4 Workflow

1. **Check current state**
   ```bash
   # Node
   npm outdated
   npm audit

   # Python
   pip list --outdated
   pip-audit

   # Rust
   cargo outdated
   cargo audit
   ```

2. **Categorize updates**
   - Security fixes → update immediately
   - Patch versions → safe to update
   - Minor versions → review changelog
   - Major versions → requires migration plan

3. **Update carefully**
   ```bash
   # Node - update one at a time
   npm update <package>

   # Python
   pip install --upgrade <package>
   ```

4. **Test after each update**
   - Run test suite
   - Check for deprecation warnings

5. **Document**
   - Note major version changes in CHANGELOG
   - Update compatibility notes

## 17.5 Anti-Patterns

- Updating all deps at once
- Ignoring security alerts
- Pinning to exact versions without reason
- Not reading changelogs for major updates

---

# 18. SKILL: DOCKER-COMPOSER

```yaml
name: docker-composer
intent: "Create and manage Docker and Docker Compose configurations"
allowed_tools: [Bash, Read, Write]
```

## 18.1 When To Use

- User says: "Dockerize this", "Add to compose", "Container setup"
- New service needs containerization
- Development environment setup

## 18.2 Inputs

- Application type and runtime
- Dependencies (databases, caches, etc.)
- Environment requirements

## 18.3 Outputs

- Dockerfile
- docker-compose.yml
- .dockerignore
- Documentation

## 18.4 Dockerfile Best Practices

```dockerfile
# Use specific version tags
FROM python:3.12-slim

# Set working directory
WORKDIR /app

# Copy dependency files first (cache layer)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Use non-root user
RUN useradd -m appuser && chown -R appuser:appuser /app
USER appuser

# Expose port
EXPOSE 8000

# Health check
HEALTHCHECK --interval=30s --timeout=10s \
  CMD curl -f http://localhost:8000/health || exit 1

# Run application
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

## 18.5 Docker Compose Template

```yaml
version: "3.8"

services:
  app:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/dbname
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=dbname
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d dbname"]
      interval: 5s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
```

## 18.6 Anti-Patterns

- Using `latest` tag in production
- Running as root
- Storing secrets in Dockerfile
- No health checks
- Massive image sizes (use slim/alpine)

---

# 19. SKILL: CI-CD-SETUP

```yaml
name: ci-cd-setup
intent: "Set up GitHub Actions or other CI/CD pipelines"
allowed_tools: [Read, Write]
```

## 19.1 When To Use

- User says: "Set up CI", "GitHub Actions", "Automate testing/deployment"
- New project needs CI/CD
- Adding deployment automation

## 19.2 Inputs

- Test command
- Build command
- Deployment target (if any)
- Branch strategy

## 19.3 Outputs

- `.github/workflows/ci.yml`
- Optional: deployment workflow
- Documentation

## 19.4 Basic CI Workflow Template

```yaml
name: CI

on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
          cache: "pip"

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest --cov=src --cov-report=xml

      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          file: coverage.xml
```

## 19.5 Workflow Stages

| Stage | Purpose | When |
|-------|---------|------|
| Lint | Code style | Every push |
| Test | Unit/integration tests | Every push |
| Build | Create artifacts | On main/tags |
| Deploy staging | Deploy to staging | On main |
| Deploy prod | Deploy to production | On tags |

## 19.6 Anti-Patterns

- No caching (slow builds)
- Secrets in workflow files
- No branch protection
- Missing status checks before merge

---

# 20. SKILL: REFACTORER

```yaml
name: refactorer
intent: "Apply systematic refactoring patterns to improve code quality"
allowed_tools: [Read, Write, Glob, Grep]
```

## 20.1 When To Use

- User says: "Refactor this", "Clean up", "Extract this", "Rename"
- Code smells identified during review
- Before adding features to messy code

## 20.2 Inputs

- Code to refactor
- Specific refactoring goal (if any)
- Test coverage status

## 20.3 Outputs

- Refactored code
- Updated tests (if needed)
- Brief explanation of changes

## 20.4 Common Refactorings

| Refactoring | When to Use |
|-------------|-------------|
| Extract Function | Long function, reusable logic |
| Extract Class | Class doing too much |
| Rename | Unclear naming |
| Inline | Over-abstracted code |
| Move | Wrong location |
| Replace Conditional with Polymorphism | Complex type switching |

## 20.5 Workflow

1. **Ensure tests exist**
   - If no tests, write them FIRST
   - Refactoring without tests is risky

2. **Identify smell**
   - Long method
   - Large class
   - Duplicate code
   - Feature envy
   - Data clumps

3. **Apply refactoring**
   - One refactoring at a time
   - Run tests after each change
   - Commit after each successful refactoring

4. **Verify**
   - Tests still pass
   - Behavior unchanged
   - Code is cleaner

## 20.6 Anti-Patterns

- Refactoring without tests
- Multiple refactorings in one commit
- Changing behavior during refactoring
- Refactoring while debugging (separate concerns)

---

# 21. SKILL: PUSH-TO-CLOUD

```yaml
name: push-to-cloud
intent: "Deploy a project to OCI-Dev cloud instance with full automation"
allowed_tools: [Read, Write, Bash, Glob]
```

## 21.1 When To Use

- User says: "Deploy this to cloud", "Push to OCI", "Run this externally", "Host this publicly"
- Project needs to run 24/7 outside homelab
- Lightweight Python/Node service needs cloud hosting

## 21.2 Prerequisites

- Tailscale access to oci-dev (100.126.13.70)
- SSH key configured for ubuntu@100.126.13.70
- Cloudflare API token (for DNS)
- Project has requirements.txt or package.json

## 21.3 Target Machine

| Property | Value |
|----------|-------|
| **Hostname** | oci-dev (instance-first) |
| **Tailscale IP** | 100.126.13.70 |
| **Public IP** | 141.148.146.79 |
| **OS** | Ubuntu 24.04 LTS (ARM64) |
| **Free Storage** | ~77GB |
| **Free RAM** | ~22GB |

### OCI Always Free Tier Limits

This is an Oracle Cloud Always Free ARM instance. These limits are FIXED and cannot be exceeded:

| Resource | Hard Limit | Notes |
|----------|-----------|-------|
| **vCPUs** | 4 OCPU (ARM) | Shared across all Always Free ARM instances |
| **RAM** | 24 GB | Fixed allocation |
| **Storage** | 200 GB boot volume | This instance uses ~50GB |
| **Outbound Data** | 10 TB/month | Generous for most apps |
| **Public IPs** | 2 reserved | 1 in use |

### What CAN Be Deployed

- Lightweight Python APIs (FastAPI, Flask)
- Static sites / simple web apps
- Background workers / cron jobs
- Webhook receivers
- Chat bots (Telegram, Discord)
- Small databases (SQLite, Redis)
- Proxy services

### What Should NOT Be Deployed

- Heavy ML models (use homelab GPU instead)
- Large databases (PostgreSQL with big datasets)
- High-traffic production apps
- Anything requiring >4GB RAM per process
- Docker-heavy stacks (use homelab instead)

### Resource Check Before Deploy

```bash
# Check current resource usage on oci-dev
ssh ubuntu@100.126.13.70 << 'RESOURCES'
echo "=== Current Resource Usage ==="
echo ""
echo "CPU (4 OCPU max):"
top -bn1 | head -3

echo ""
echo "Memory (24GB max):"
free -h

echo ""
echo "Disk (200GB max):"
df -h /

echo ""
echo "Running services:"
systemctl list-units --type=service --state=running | grep -v systemd | head -20
RESOURCES
```

## 21.4 Infrastructure Prerequisites

Before deploying, verify oci-dev has required tooling installed. Run this check first:

```bash
# Infrastructure check script - run on oci-dev
ssh ubuntu@100.126.13.70 << 'CHECK'
echo "=== OCI-Dev Infrastructure Check ==="

# Python environment
echo -n "Python 3: "
python3 --version 2>/dev/null || echo "❌ MISSING"

echo -n "pip: "
pip3 --version 2>/dev/null || echo "❌ MISSING"

echo -n "venv: "
python3 -c "import venv" 2>/dev/null && echo "✅ OK" || echo "❌ MISSING"

# Node environment (optional)
echo -n "Node.js: "
node --version 2>/dev/null || echo "⚠️ Not installed (optional)"

echo -n "npm: "
npm --version 2>/dev/null || echo "⚠️ Not installed (optional)"

# System tools
echo -n "systemd: "
systemctl --version | head -1 2>/dev/null || echo "❌ MISSING"

echo -n "curl: "
curl --version | head -1 2>/dev/null || echo "❌ MISSING"

# Directory structure
echo -n "~/dev directory: "
[ -d ~/dev ] && echo "✅ Exists" || echo "⚠️ Will create"

# Disk space
echo -n "Free disk: "
df -h / | awk 'NR==2 {print $4 " available"}'

# Memory
echo -n "Free RAM: "
free -h | awk '/Mem:/ {print $4 " available"}'
CHECK
```

### Auto-Fix Missing Prerequisites

If any prerequisites are missing, run this setup:

```bash
ssh ubuntu@100.126.13.70 << 'SETUP'
echo "=== Installing OCI-Dev Prerequisites ==="

# Update package lists
sudo apt-get update

# Python essentials
sudo apt-get install -y python3 python3-pip python3-venv

# Node.js (via NodeSource for LTS)
if ! command -v node &> /dev/null; then
  curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
  sudo apt-get install -y nodejs
fi

# Essential tools
sudo apt-get install -y curl wget git htop

# Create dev directory
mkdir -p ~/dev

echo "✅ Prerequisites installed"
SETUP
```

## 21.5 Deployment Workflow

```yaml
steps:
  # Step 0: Check/Install prerequisites
  - id: prereq
    action: "Verify oci-dev has required tools"
    checks:
      - python3 --version
      - pip3 --version
      - systemctl --version
    on_fail: "Run auto-fix script above"

  # Step 1: Validate project structure
  - id: validate
    action: "Check project has required files"
    required:
      - requirements.txt OR package.json
      - Main entry point (app.py, main.py, index.js, etc.)
    optional:
      - .env.example (will prompt for values)
      - Dockerfile (ignored - we use systemd)

  # Step 2: Push code to oci-dev
  - id: push
    action: "rsync project to oci-dev"
    command: |
      rsync -avz --exclude='.venv' --exclude='node_modules' \
        --exclude='__pycache__' --exclude='.git' \
        ./ ubuntu@100.126.13.70:~/dev/${PROJECT_NAME}/

  # Step 3: Setup environment
  - id: setup
    action: "SSH and install dependencies"
    commands:
      python: |
        cd ~/dev/${PROJECT_NAME}
        python3 -m venv venv
        source venv/bin/activate
        pip install -r requirements.txt
      node: |
        cd ~/dev/${PROJECT_NAME}
        npm install --production

  # Step 4: Create systemd service
  - id: systemd
    action: "Create and enable systemd service"
    template: |
      [Unit]
      Description=${PROJECT_NAME}
      After=network.target

      [Service]
      Type=simple
      User=ubuntu
      WorkingDirectory=/home/ubuntu/dev/${PROJECT_NAME}
      ExecStart=${EXEC_COMMAND}
      Restart=always
      RestartSec=5
      Environment=PORT=${PORT}

      [Install]
      WantedBy=multi-user.target

  # Step 5: Configure DNS + Traefik (if domain requested)
  - id: dns
    action: "Add Cloudflare DNS record"
    condition: "if domain requested"
    api: |
      curl -X POST "https://api.cloudflare.com/client/v4/zones/${ZONE_ID}/dns_records" \
        -H "Authorization: Bearer ${CF_TOKEN}" \
        -d '{"type":"A","name":"${SUBDOMAIN}","content":"100.112.130.100","ttl":1,"proxied":false}'

  # Step 6: Add Traefik config
  - id: traefik
    action: "Create Traefik routing config"
    file: ~/github/homelab/services/traefik/config/${PROJECT_NAME}.yml
    template: |
      http:
        routers:
          ${PROJECT_NAME}:
            rule: "Host(`${SUBDOMAIN}.${DOMAIN}`)"
            entryPoints:
              - websecure
            tls:
              certResolver: cloudflare
            service: ${PROJECT_NAME}
        services:
          ${PROJECT_NAME}:
            loadBalancer:
              servers:
                - url: "http://100.126.13.70:${PORT}"

  # Step 7: Restart Traefik
  - id: restart
    action: "Apply Traefik config"
    command: docker restart traefik
```

## 21.6 Inputs Required

| Input | Description | Example |
|-------|-------------|---------|
| `PROJECT_NAME` | Directory name on oci-dev | `my-api` |
| `PORT` | Port the app listens on | `8080` |
| `SUBDOMAIN` | (Optional) Subdomain for public access | `api` |
| `DOMAIN` | (Optional) Domain | `khamel.com` |

## 21.7 Outputs

- Project deployed to `~/dev/${PROJECT_NAME}/` on oci-dev
- Systemd service running and enabled
- (Optional) DNS record created
- (Optional) Traefik config for HTTPS routing

## 21.8 Quick Deploy Script

For Claude to execute in one shot:

```bash
# Variables (fill in)
PROJECT_NAME="my-project"
PORT="8080"
SUBDOMAIN="myapp"  # Leave empty for no public access
DOMAIN="khamel.com"
ENTRY_POINT="app.py"  # main.py, server.py, etc.

# 0. Pre-flight checks
echo "🔍 Running pre-flight checks..."
ssh ubuntu@100.126.13.70 << 'PREFLIGHT'
# Check Python
if ! command -v python3 &> /dev/null; then
  echo "❌ Python3 not found. Installing..."
  sudo apt-get update && sudo apt-get install -y python3 python3-pip python3-venv
fi

# Check pip
if ! command -v pip3 &> /dev/null; then
  echo "❌ pip3 not found. Installing..."
  sudo apt-get install -y python3-pip
fi

# Ensure dev directory exists
mkdir -p ~/dev

echo "✅ Prerequisites OK"
PREFLIGHT

# 1. Check port availability
echo "🔍 Checking port ${PORT}..."
PORT_CHECK=$(ssh ubuntu@100.126.13.70 "ss -tlnp | grep :${PORT} || true")
if [ -n "$PORT_CHECK" ]; then
  echo "⚠️  Port ${PORT} is in use:"
  echo "$PORT_CHECK"
  echo "Choose a different port or stop the conflicting service."
  exit 1
fi
echo "✅ Port ${PORT} available"

# 2. Push code
echo "📦 Pushing code to oci-dev..."
rsync -avz --exclude='.venv' --exclude='node_modules' --exclude='__pycache__' --exclude='.git' \
  ./ ubuntu@100.126.13.70:~/dev/${PROJECT_NAME}/

# 3. Setup on remote
echo "🔧 Setting up environment..."
ssh ubuntu@100.126.13.70 << REMOTE
cd ~/dev/${PROJECT_NAME}

# Create/update venv
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

# Create service
sudo tee /etc/systemd/system/${PROJECT_NAME}.service > /dev/null << SERVICE
[Unit]
Description=${PROJECT_NAME}
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/dev/${PROJECT_NAME}
ExecStart=/home/ubuntu/dev/${PROJECT_NAME}/venv/bin/python ${ENTRY_POINT}
Restart=always
RestartSec=5
Environment=PORT=${PORT}

[Install]
WantedBy=multi-user.target
SERVICE

sudo systemctl daemon-reload
sudo systemctl enable ${PROJECT_NAME}
sudo systemctl restart ${PROJECT_NAME}

# Verify it started
sleep 2
if systemctl is-active --quiet ${PROJECT_NAME}; then
  echo "✅ Service started successfully"
else
  echo "❌ Service failed to start. Checking logs..."
  sudo journalctl -u ${PROJECT_NAME} -n 20 --no-pager
  exit 1
fi
REMOTE

echo "✅ Deployed ${PROJECT_NAME} to oci-dev:${PORT}"
```

## 21.9 Anti-Patterns

- Deploying without testing locally first
- Hardcoding secrets (use environment variables)
- Not checking if port is already in use
- Forgetting to restart Traefik after config change
- Deploying large apps (use homelab for heavy workloads)

## 21.10 Rollback

```bash
# Stop and disable
ssh ubuntu@100.126.13.70 "sudo systemctl stop ${PROJECT_NAME} && sudo systemctl disable ${PROJECT_NAME}"

# Remove service file
ssh ubuntu@100.126.13.70 "sudo rm /etc/systemd/system/${PROJECT_NAME}.service && sudo systemctl daemon-reload"

# Remove project
ssh ubuntu@100.126.13.70 "rm -rf ~/dev/${PROJECT_NAME}"

# Remove DNS (if created)
# curl -X DELETE "https://api.cloudflare.com/client/v4/zones/${ZONE_ID}/dns_records/${RECORD_ID}"

# Remove Traefik config
rm ~/github/homelab/services/traefik/config/${PROJECT_NAME}.yml
docker restart traefik
```

---

# 22. EXTENDING SKILLS.md

When you discover a pattern you repeat across projects:

1. **Ask**: Is this:
   - Project-specific? → Keep in `docs/`
   - Reusable across multiple projects? → Turn into a new skill here

2. **For new core skills**:
   - Add a new section following the pattern:
     - name, intent, allowed_tools, when_to_use, inputs, outputs, workflow, anti_patterns
   - Optionally create a concrete `.claude/skills/<name>/SKILL.md`

3. **Keep this file focused on**:
   - Skills used in > 2 projects, or
   - Skills that meaningfully reduce cognitive load

---

# END OF SKILLS.md

---

**Version:** 2.3
**Skills Count:** 21 skills
**Companion To:** ONE_SHOT v3.1

**Changelog:**
- **v2.3**: Added push-to-cloud skill (#21) for OCI-Dev deployment with infrastructure validation
- **v2.1**: Added The Build Algorithm, BUILD_WITH_SKILLS orchestration, Skill Selection Flowchart, 6 Build Recipes

**URL:** `https://raw.githubusercontent.com/Khamel83/secrets-vault/master/SKILLS.md`

**Usage:**
1. Start with THE BUILD ALGORITHM at the top
2. Pick the right recipe for your project type
3. Compose skills as LEGO blocks
4. Reference individual skills on-demand

**The insight:** 21 modular skills can build almost ANY project.
