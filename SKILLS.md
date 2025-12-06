# ONE_SHOT SKILLS REFERENCE

**Version**: 2.1
**Source**: ONE_SHOT Specification
**URL**: `https://raw.githubusercontent.com/Khamel83/secrets-vault/master/SKILLS.md`
**Last Updated**: 2024-12-06

---

## How to Use This File

This file contains all ONE_SHOT skills in a single reference document. Agents can:

1. **Fetch directly**: `WebFetch` this URL when skills are needed
2. **Reference inline**: Copy relevant skill sections into project context
3. **Install locally**: Download to `.claude/skills/` for project-specific use

```yaml
skills_reference:
  url: "https://raw.githubusercontent.com/Khamel83/secrets-vault/master/SKILLS.md"
  usage: "Fetch when skill is needed, don't memorize entire file"
  update_frequency: "Check for updates when starting new projects"
```

---

## Quick Reference: All Skills

| Skill | Purpose | Trigger Keywords |
|-------|---------|------------------|
| **project-initializer** | Bootstrap new projects | "new project", "initialize", "start fresh" |
| **feature-planner** | Break down features into tasks | "plan feature", "break down", "create plan" |
| **git-workflow** | Conventional commits & PRs | "commit", "PR", "conventional commits" |
| **code-reviewer** | Code quality & security review | "review code", "security check", "PR review" |
| **documentation-generator** | READMEs, ADRs, API docs | "generate docs", "README", "ADR" |
| **secrets-vault-manager** | SOPS + Age secrets management | "secrets", "API keys", "environment variables" |
| **skill-creator** | Create new Claude Code skills | "create skill", "new skill", "automate workflow" |
| **marketplace-browser** | Find & install community skills | "find skill", "browse marketplace", "is there a skill for" |
| **designer** | Create beautiful web designs | "design", "website", "UI", "landing page" |

---

# SKILL 1: PROJECT-INITIALIZER

**Name**: `project-initializer`
**Purpose**: Bootstraps new projects with ONE_SHOT standards

## When to Use
- User says "initialize new project" or "start new project"
- User asks to "set up project with ONE_SHOT standards"
- Starting any new development effort

## What It Does

1. **Gathers project information**: Name, type, stack, purpose, scope
2. **Creates directory structure** based on project type
3. **Initializes git** with proper `.gitignore`
4. **Creates CLAUDE.md** with project-specific guidance
5. **Creates README.md** with Quick Start
6. **Creates LLM-OVERVIEW.md** for AI context
7. **Sets up secrets management** (SOPS integration)
8. **Configures quality tools** (linting, formatting, testing)
9. **Creates documentation structure** (`docs/architecture/`, ADR template)
10. **Makes initial commit**

## Directory Structures by Project Type

**Web Application**:
```
project-name/
├── .claude/
│   ├── CLAUDE.md
│   └── skills/
├── src/
│   ├── components/
│   ├── pages/
│   └── utils/
├── tests/
├── docs/
├── LLM-OVERVIEW.md
├── README.md
└── package.json
```

**CLI Tool**:
```
project-name/
├── .claude/
│   └── CLAUDE.md
├── cmd/
├── internal/
├── tests/
├── docs/
├── scripts/
├── LLM-OVERVIEW.md
├── README.md
└── Makefile
```

**API/Backend Service**:
```
project-name/
├── .claude/
│   └── CLAUDE.md
├── cmd/
├── internal/
├── api/
├── tests/
├── docs/
│   └── api/
├── LLM-OVERVIEW.md
├── README.md
└── docker-compose.yml
```

---

# SKILL 2: FEATURE-PLANNER

**Name**: `feature-planner`
**Purpose**: Breaks down feature requests into implementable plans

## When to Use
- User requests "plan this feature"
- User describes a new feature or enhancement
- Complex feature needs decomposition
- User says "break this down" or "create a plan"

## Planning Methodology

1. **Understand the feature**: What problem does it solve? Who uses it?
2. **Break down into components**: Frontend, backend, database, infrastructure, testing, documentation
3. **Create task list**: Ordered by dependencies
4. **Identify dependencies**: What must be done first?
5. **Estimate complexity**: Simple / Medium / Complex
6. **Risk assessment**: What could go wrong?
7. **Testing strategy**: How to verify it works?

## Output Format

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

---

# SKILL 3: GIT-WORKFLOW

**Name**: `git-workflow`
**Purpose**: Automates git operations with conventional commits

## When to Use
- User asks to commit changes
- User wants to create a PR
- User mentions conventional commits
- Git workflow automation needed

## Conventional Commits Format

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**Types**:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Formatting, no code change
- `refactor`: Code change without feature/fix
- `test`: Adding tests
- `chore`: Maintenance tasks
- `perf`: Performance improvement
- `ci`: CI/CD changes

## Commit Workflow

1. **Review changes**: `git status`, `git diff`
2. **Stage files**: `git add <files>` (be selective)
3. **Create commit**: Use conventional format
4. **Push changes**: `git push`

## Branch Naming

```
feature/[ticket-id]-short-description
fix/[ticket-id]-short-description
docs/update-readme
chore/update-dependencies
```

## PR Template

```markdown
## Summary
[2-3 bullet points]

## Changes
- Change 1
- Change 2

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing done

## Screenshots (if UI changes)
[Before/After]
```

---

# SKILL 4: CODE-REVIEWER

**Name**: `code-reviewer`
**Purpose**: Comprehensive code review including quality and security

## When to Use
- User asks to review code
- User mentions code review or PR review
- User asks about security issues
- Before merging significant changes

## Review Checklist

**Code Quality**:
- [ ] DRY - No repeated code blocks
- [ ] KISS - Simplest solution that works
- [ ] YAGNI - No speculative features

**Security (OWASP Top 10)**:
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

**Best Practices**:
- [ ] Error handling is appropriate
- [ ] Logging is useful but not excessive
- [ ] Tests cover critical paths
- [ ] Documentation is updated

## Output Format

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

---

# SKILL 5: DOCUMENTATION-GENERATOR

**Name**: `documentation-generator`
**Purpose**: Generates READMEs, API docs, ADRs, and project guides

## When to Use
- User asks to generate documentation
- User mentions README, API docs, or ADR
- New project needs documentation structure
- Documentation updates needed after changes

## Documentation Types

1. **README.md**: User-facing, getting started
2. **LLM-OVERVIEW.md**: AI context file
3. **ADR (Architecture Decision Record)**: Why we chose X
4. **API Documentation**: Endpoints, request/response
5. **Developer Guide**: How to contribute
6. **CLAUDE.md**: AI assistant guidance

## ADR Template (Nygard Format)

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

---

# SKILL 6: SECRETS-VAULT-MANAGER

**Name**: `secrets-vault-manager`
**Purpose**: Automates secrets management with SOPS + Age

## When to Use
- User mentions secrets, environment variables, or API keys
- User asks to set up secrets-vault
- New project initialization needing secrets
- User mentions decrypting secrets

## Setup Workflow

1. **Verify Age key exists**: `~/.age/key.txt`
2. **Clone vault**: `git clone git@github.com:Khamel83/secrets-vault.git`
3. **Set up project**: Decrypt secrets to `.env`
4. **Add to .gitignore**: Ensure `.env` is ignored
5. **Verify setup**: Source `.env` and check variables
6. **Document usage**: Add secrets section to README

## Common Operations

**Initial setup**:
```bash
sops --decrypt ~/github/secrets-vault/secrets.env.encrypted > .env
source .env
```

**Refresh secrets**:
```bash
sops --decrypt ~/github/secrets-vault/secrets.env.encrypted > .env
```

**Add new secret**:
```bash
cd ~/github/secrets-vault
sops secrets.env.encrypted
# Add new line: NEW_SECRET=value
# Save and exit
git add . && git commit -m "Add NEW_SECRET" && git push
```

---

# SKILL 7: SKILL-CREATOR

**Name**: `skill-creator`
**Purpose**: Creates well-structured Claude Code skills

## When to Use
- User explicitly asks to create a skill
- User describes a repetitive workflow to automate
- User wants to package domain knowledge for reuse

## Skill Creation Workflow

1. **Gather requirements**: What should the skill do? When should it be used?
2. **Create directory structure**: `.claude/skills/skill-name/`
3. **Write SKILL.md**: YAML frontmatter + instructions
4. **Create supporting files**: Templates, examples, scripts
5. **Test the skill**: Verify it works as expected
6. **Document**: Keywords, examples, edge cases

## SKILL.md Structure

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

---

# SKILL 8: MARKETPLACE-BROWSER

**Name**: `marketplace-browser`
**Purpose**: Discovers and installs skills from community sources

## When to Use
- User asks to find or search for skills
- User wants to browse the marketplace
- User describes a task and asks "is there a skill for that?"

## Skill Discovery Sources

1. **skillsmp.com**: Official Skills Marketplace
2. **Anthropic official**: Built-in Claude Code skills
3. **Community repositories**:
   - obra/claude-code-skills
   - levnikolaevich/claude-code-skills
   - mhattingpete/claude-skills
   - OneRedOak/claude-code-prompt-repo
   - WSHobson/claude-skill-builder

## Installation Methods

**From marketplace**:
```bash
# Browse available skills
open https://skillsmp.com

# Download skill to local
curl -o .claude/skills/skill-name/SKILL.md https://skillsmp.com/skills/skill-name
```

**From GitHub**:
```bash
# Clone skill repository
git clone https://github.com/author/skill-repo
cp -r skill-repo/skills/* .claude/skills/
```

---

# SKILL 9: DESIGNER

**Name**: `designer`
**Purpose**: Creates beautiful, modern web designs with clean HTML/CSS

## When to Use
- User asks to design a website, landing page, or UI
- User wants to create a portfolio, blog, or marketing page
- User mentions "make it look good" or "professional design"
- User needs a quick static site

## Design Philosophy

**Core Principles**:
1. **Clean, minimal aesthetics** - Whitespace is your friend
2. **Mobile-first responsive** - Works on all devices
3. **Fast loading** - No heavy frameworks unless needed
4. **Accessibility** - Semantic HTML, proper contrast, keyboard navigation
5. **Modern CSS** - Flexbox, Grid, custom properties

## Design System Defaults

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

## Common Components

### Hero Section
```html
<section class="hero">
  <div class="container">
    <h1 class="hero-title">Your Headline Here</h1>
    <p class="hero-subtitle">A compelling subtitle that explains your value proposition</p>
    <div class="hero-cta">
      <a href="#" class="btn btn-primary">Get Started</a>
      <a href="#" class="btn btn-secondary">Learn More</a>
    </div>
  </div>
</section>
```

### Feature Grid
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
      <!-- More cards -->
    </div>
  </div>
</section>
```

### Responsive Navigation
```html
<nav class="nav">
  <div class="container nav-container">
    <a href="/" class="nav-logo">Brand</a>
    <button class="nav-toggle" aria-label="Toggle menu">
      <span></span>
    </button>
    <ul class="nav-menu">
      <li><a href="#features">Features</a></li>
      <li><a href="#pricing">Pricing</a></li>
      <li><a href="#contact" class="btn btn-primary">Contact</a></li>
    </ul>
  </div>
</nav>
```

## Output Format

When creating designs, output:

1. **Single HTML file** with embedded CSS (for simple pages)
2. **Or separate files**: `index.html`, `styles.css`, `script.js`
3. **Include**:
   - Responsive meta tags
   - Semantic HTML5 structure
   - CSS custom properties for easy theming
   - Smooth scroll behavior
   - Basic JavaScript for interactivity (if needed)

## Color Palette Presets

**Professional Blue**:
```css
--primary: #2563eb;
--accent: #3b82f6;
```

**Warm Orange**:
```css
--primary: #ea580c;
--accent: #f97316;
```

**Forest Green**:
```css
--primary: #059669;
--accent: #10b981;
```

**Royal Purple**:
```css
--primary: #7c3aed;
--accent: #8b5cf6;
```

**Dark Mode**:
```css
--bg: #0f172a;
--bg-alt: #1e293b;
--text: #f1f5f9;
--text-muted: #94a3b8;
```

---

# END OF SKILLS REFERENCE

**ONE_SHOT Skills v2.1** - All skills in one file for easy agent access.

**To use**: Fetch this URL when a skill is needed. Don't memorize - reference on demand.

**URL**: `https://raw.githubusercontent.com/Khamel83/secrets-vault/master/SKILLS.md`
