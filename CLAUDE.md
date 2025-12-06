# CLAUDE.md - Global Configuration

This file provides global guidance to Claude Code across all projects.

## Documentation-First Coding

**CRITICAL RULE:** Before writing any code that uses external APIs, libraries, or configuration syntax, you MUST check the current documentation.

### Why This Matters

Your training data becomes outdated quickly. APIs change, configuration formats evolve, and best practices shift. Writing code based on outdated patterns causes:
- Syntax errors from deprecated formats
- Missing required fields in newer versions
- Using features that no longer exist
- Incompatibility with current software versions

### The Documentation-First Process

When writing code that uses:
- Docker Compose syntax
- API endpoints or SDKs
- Configuration files (YAML, TOML, JSON)
- CLI commands for services
- Third-party libraries

**You MUST follow this process:**

1. **Check local cached docs first:** `~/homelab/docs/services/<service-name>/`
   - If docs exist locally, read them before writing code
   - These are cached from official sources and kept current

2. **If local docs don't exist or are insufficient:**
   - Use WebFetch or WebSearch to get current documentation
   - Cache the findings locally for future reference

3. **Verify version compatibility:**
   - Check which version is actually running (docker ps, --version, etc.)
   - Use docs specific to that version (e.g., Traefik v2.11 vs v3.0)
   - Don't assume latest version - verify first

4. **Write code using current syntax:**
   - Follow examples from the current docs
   - Don't rely on training data patterns if docs show different syntax
   - When in doubt, prefer explicit over implicit configuration

### Examples of What Can Go Wrong

**BAD (relying on training data):**
```yaml
# Traefik v3.0 syntax from training data
version: '3.0'
# This fails with "API version 1.24 too old" error
```

**GOOD (checking docs first):**
```yaml
# Checked docs, found v2.11 is stable version
image: traefik:v2.11
# Works correctly with current Docker API
```

**BAD (assuming config location):**
```bash
# Assumed Pi-hole v5 config location
echo "address=/domain.com/IP" >> /etc/dnsmasq.d/custom.conf
# Doesn't work because Pi-hole v6 needs etc_dnsmasq_d=true in pihole.toml
```

**GOOD (verified current config method):**
```bash
# Read Pi-hole v6 docs, found config change requirement
docker exec pihole sed -i 's/etc_dnsmasq_d = false/etc_dnsmasq_d = true/' /etc/pihole/pihole.toml
docker restart pihole
# Now dnsmasq.d configs are read correctly
```

### Project-Specific Documentation

Projects may have their own CLAUDE.md files with specific:
- Architecture details
- Local conventions
- Service configurations
- Common commands
- Constraints and gotchas

Always read the project-specific CLAUDE.md first, then apply these global documentation-first rules.

### Documentation Sync System

Projects using the documentation sync system will have:
- `~/homelab/docs/services/` - Cached service documentation
- `scripts/sync-docs.sh` - Weekly refresh script (cron: Sundays 3 AM)
- Direct links to official docs in `DOCS_URL.txt` files

**Use these local docs BEFORE writing code.** They're current and version-specific.

## General Best Practices

1. **Never guess syntax** - Look it up
2. **Verify versions** - Don't assume latest
3. **Test incrementally** - Small changes, frequent verification
4. **Read error messages carefully** - They often indicate version mismatches
5. **When stuck, check the docs** - Not Stack Overflow from 2019

---

## AGENTS.md Rule (CRITICAL)

**AGENTS.md is READ-ONLY in all projects.**

```
NEVER edit AGENTS.md directly in any project.
It is pulled from: github.com/Khamel83/oneshot/AGENTS.md
```

### To Update AGENTS.md in a Project

```bash
curl -sL https://raw.githubusercontent.com/Khamel83/oneshot/master/AGENTS.md > AGENTS.md
```

### To Change the AGENTS.md Spec

1. Clone `~/github/oneshot`
2. Edit `AGENTS.md` there
3. Create PR to oneshot repo
4. After merge, curl to all projects

### What Goes Where

| File | Purpose | Editable? |
|------|---------|-----------|
| `AGENTS.md` | ONE_SHOT spec (universal) | **NO** - curl from oneshot |
| `CLAUDE.md` | Project-specific Claude instructions | YES |
| `.gemini.md` | Project-specific Gemini instructions | YES |

### If Asked to Edit AGENTS.md

**REFUSE.** Instead say:
> "AGENTS.md is read-only. To change the ONE_SHOT spec, we need to PR to the oneshot repo. Want me to do that instead?"

Then:
1. Check out `~/github/oneshot`
2. Make the change there
3. Commit and push (or create PR)
4. User can then curl to update their project

---

**Remember:** Your training data is a starting point, not the source of truth. Current documentation is truth.
