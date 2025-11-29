# Secrets Vault - Cross-Project Integration Guide

## 🎯 OVERVIEW

The `secrets-vault` repository serves as your **centralized secret management system** across all GitHub projects. One Age key, universal access.

## 🔑 MASTER AGE KEY

Your primary Age key (ending NMFY) works across ALL projects:

```
AGE-SECRET-KEY-16PQX7DRJVAX79JSRL27TYERR5ATLNWYP8LV9GP88SM4VFAEA5UHQYVNMFY
```

**Public Key:** `age1a8cq06ufznc3kq5a2a4gk5ntqywqk6km8s8k2ntnp8l4kqy4ulqsx7dv5q`

## 🏗️ PROJECT ARCHITECTURE

### Repository Structure
```
secrets-vault/
├── .sops.yaml                    # SOPS configuration
├── secrets.env.encrypted         # Main secrets file
├── SOPS_SETUP.md                 # Complete setup guide
├── SECRETS_VAULT_GUIDE.md         # This file
├── SECRETS_VAULT_INTEGRATION.md   # Integration patterns
└── project-secrets/              # Individual project secrets (optional)
    ├── homelab.env.encrypted
    ├── atlas.env.encrypted
    ├── oneshot.env.encrypted
    └── ...
```

## 🔧 CROSS-PROJECT INTEGRATION

### Method 1: Single Central Secrets File

**Best for:** Small projects, shared secrets, quick setup

```bash
# In secrets-vault/secrets.env.encrypted
DB_HOST=localhost
DB_USER=shared_user
API_BASE_URL=https://api.example.com
JWT_SECRET=shared-jwt-secret

# In any project repository
git clone git@github.com:Khamel83/secrets-vault.git
cd secrets-vault
sops --decrypt secrets.env.encrypted > ../project-secrets/.env
```

### Method 2: Per-Project Secrets

**Best for:** Large projects, isolation requirements

```bash
# In secrets-vault/
sops --create project-secrets/homelab.env.encrypted
sops --create project-secrets/atlas.env.encrypted

# In homelab project
cd ~/github/homelab
ln -sf ~/github/secrets-vault/project-secrets/homelab.env.encrypted .env.encrypted
sops --decrypt .env.encrypted > .env
```

### Method 3: Makefile Integration

**Best for:** Automated workflows, team collaboration

```makefile
# Add to any project's Makefile
SECRETS_VAULT ?= $(HOME)/github/secrets-vault

.PHONY: setup-secrets pull-secrets push-secrets

setup-secrets:
	@echo "🔐 Setting up secrets vault integration..."
	@if [ ! -d "$(SECRETS_VAULT)" ]; then \
		git clone git@github.com:Khamel83/secrets-vault.git $(SECRETS_VAULT); \
	fi
	@echo "✅ Secrets vault ready at $(SECRETS_VAULT)"

pull-secrets: setup-secrets
	@echo "📥 Pulling secrets from vault..."
	@if [ -f "$(SECRETS_VAULT)/secrets.env.encrypted" ]; then \
		sops --decrypt "$(SECRETS_VAULT)/secrets.env.encrypted" > .env; \
		echo "✅ Secrets pulled from vault"; \
	else \
		echo "❌ No central secrets found in vault"; \
	fi

push-secrets: setup-secrets
	@echo "📤 Pushing secrets to vault..."
	@if [ -f .env ]; then \
		cd "$(SECRETS_VAULT)" && \
		git pull origin main && \
		sops --encrypt --age $(age-keygen -y ~/.age/key.txt) ../$(CURDIR)/.env > secrets.env.encrypted && \
		git add secrets.env.encrypted && \
		git commit -m "Update secrets from $(CURDIR)" && \
		git push origin main; \
		echo "✅ Secrets pushed to vault"; \
	fi
```

## 🚀 AUTOMATED WORKFLOWS

### Git Hooks Integration

Add to `.git/hooks/pre-commit`:

```bash
#!/bin/bash
# Pre-commit hook to ensure secrets are managed properly

# Check for unencrypted secrets
if git status --porcelain | grep -q "^M.*\.env$"; then
    echo "❌ ERROR: Unencrypted .env file detected!"
    echo "Please encrypt with: make encrypt-secrets"
    exit 1
fi

# Check if secrets need to be pushed to vault
if [ -f .env ]; then
    echo "⚠️  WARNING: .env file exists - consider running 'make push-secrets'"
fi
```

### CI/CD Integration

```yaml
# .github/workflows/deploy.yml
name: Deploy Application

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup secrets vault
        run: |
          git clone https://github.com/Khamel83/secrets-vault.git
          # Decrypt secrets using Age key stored in GitHub Secrets
          echo "${{ secrets.AGE_PRIVATE_KEY }}" > ~/.age/key.txt
          chmod 600 ~/.age/key.txt

      - name: Load application secrets
        run: |
          sops --decrypt secrets-vault/secrets.env.encrypted > .env
          source .env

      - name: Deploy application
        run: |
          # Your deployment commands here
          echo "Deploying with DB_HOST=${DB_HOST}"
```

## 🔐 SECURITY PATTERNS

### 1. Key Segmentation

```bash
# Different keys for different security levels
# Production secrets: age1prod-key-here...
# Development secrets: age1dev-key-here...
# Testing secrets: age1test-key-here...
```

### 2. Access Control

```yaml
# .sops.yaml with team-based access
creation_rules:
  # Production secrets
  - path_regex: "production/.*"
    age: "age1prod-key,age1admin1-key,age1admin2-key"

  # Development secrets
  - path_regex: "development/.*"
    age: "age1dev-key,age1admin1-key"

  # Common secrets
  - age: "age1common-key,age1all-team-members..."
```

### 3. Rotation Strategy

```bash
# Monthly secret rotation script
#!/bin/bash
NEW_KEY=$(age-keygen | grep "AGE-SECRET-KEY" | cut -d' ' -f3)

# Update all encrypted files with new key
find . -name "*.encrypted" -exec sops \
  --encrypt --age "$NEW_KEY" {} {} \;

# Update vault with new key
cd secrets-vault
git add .
git commit -m "Rotate encryption keys"
git push origin main
```

## 📋 PROJECT-SPECIFIC SETUP

### For Homelab Projects

```bash
# In homelab/
make setup-secrets
make pull-secrets
make up  # Auto-decrypts and starts services
```

### For Python Projects

```python
# Add to requirements.txt
# python-decouple
# python-sops

# In settings.py
from decouple import config
import sops

# Decrypt and load secrets
with sops.open('secrets.env.encrypted', 'r') as f:
    # Load decrypted environment variables
    pass

DB_HOST = config('DB_HOST')
DB_PASSWORD = config('DB_PASSWORD')
```

### For Node.js Projects

```javascript
// Add to package.json
{
  "scripts": {
    "setup": "sops -d secrets.env.encrypted > .env && npm install",
    "start": "node -r dotenv/config app.js",
    "cleanup": "rm .env"
  },
  "devDependencies": {
    "dotenv": "^16.0.0",
    "@sops/node": "^0.1.0"
  }
}
```

## 🔧 TROUBLESHOOTING

### Common Issues

**Issue:** "no matching keys found"
```bash
# Solution: Check your Age key
age-keygen -y ~/.age/key.txt  # Should show your public key
ls -la ~/.age/key.txt          # Should exist and have 600 permissions
```

**Issue:** "secrets not found in vault"
```bash
# Solution: Check vault status
cd ~/github/secrets-vault
git status
ls -la *.encrypted
```

**Issue:** "permission denied on secrets file"
```bash
# Solution: Fix permissions
chmod 600 ~/.age/key.txt
chmod 640 *.encrypted
```

### Debug Commands

```bash
# Test vault connectivity
cd ~/github/secrets-vault && git fetch origin

# Test secret decryption
sops --decrypt secrets.env.encrypted | head -5

# Test key validity
age-keygen -y ~/.age/key.txt | grep age1
```

## 📊 MONITORING & AUDITING

### Access Logging

```bash
# Log all secret accesses
export SOPS_LOG_LEVEL=info

# Audit trail
grep "sops" /var/log/auth.log | tail -20
```

### Secret Rotation Tracking

```bash
# Track secret age
find . -name "*.encrypted" -exec stat -c "%Y %n" {} + | \
  awk '{print strftime("%Y-%m-%d", $1), $2}' | \
  sort -k1,1
```

## 🎯 SUCCESS METRICS

Your secrets vault integration is successful when:

✅ **Single Age key** works across all projects
✅ **Secrets automatically decrypted** on service startup
✅ **Git hooks prevent** unencrypted secrets in commits
✅ **Team members can access** shared secrets securely
✅ **Automated workflows** handle secret management
✅ **Monitoring and auditing** is in place

## 🚀 ADVANCED FEATURES

### 1. Environment-Specific Secrets

```bash
# secrets-vault/
├── production.env.encrypted
├── staging.env.encrypted
├── development.env.encrypted
└── testing.env.encrypted
```

### 2. Secret Templates

```bash
# templates/database.env.template
DB_HOST={{DB_HOST}}
DB_USER={{DB_USER}}
DB_PASSWORD={{DB_PASSWORD}}
DB_NAME={{DB_NAME}}

# Generate from template
sops --encrypt --template database.env.template > database.env.encrypted
```

### 3. API Integration

```python
# secrets-api.py
import sops
import json

def get_secret(key):
    with sops.open('secrets.env.encrypted', 'r') as f:
        secrets = dict(line.strip().split('=', 1) for line in f if '=' in line)
    return secrets.get(key)

# Usage
db_password = get_secret('DB_PASSWORD')
```

## 📚 REFERENCE MATERIALS

- [SOPS Documentation](https://github.com/mozilla/sops)
- [Age Documentation](https://github.com/FiloSottile/age)
- [Git Hooks Guide](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

---

**🎉 Congratulations! You now have enterprise-grade cross-project secret management!**

Your secrets vault provides secure, automated, and scalable secret management across all your GitHub projects.

---

*For technical support, check the main secrets-vault repository or create an issue.*