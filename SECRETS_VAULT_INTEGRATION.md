# Secrets Vault Integration Patterns

## 🎯 QUICK INTEGRATION PATTERNS

### Pattern 1: One-Command Setup (Most Popular)

**For new projects** - Get secrets management in 30 seconds:

```bash
# Clone secrets vault
git clone git@github.com:Khamel83/secrets-vault.git ~/github/secrets-vault

# Add to any project directory
echo 'include ~/github/secrets-vault/Makefile.integration' >> Makefile

# One-command setup
make setup-secrets-vault

# Done! Your project now has enterprise-grade secret management
```

### Pattern 2: Copy-Paste Integration

**For existing projects** - Add secret management instantly:

```bash
# 1. Add to Makefile
cat >> Makefile << 'EOF'

# Secrets Vault Integration
SECRETS_VAULT ?= ~/github/secrets-vault

setup-secrets:
	@echo "🔐 Setting up secrets vault..."
	@if [ ! -d "$(SECRETS_VAULT)" ]; then \
		git clone git@github.com:Khamel83/secrets-vault.git $(SECRETS_VAULT); \
	fi
	@if [ -f "$(SECRETS_VAULT)/secrets.env.encrypted" ]; then \
		sops --decrypt "$(SECRETS_VAULT)/secrets.env.encrypted" > .env; \
		echo "✅ Secrets loaded from vault"; \
	else \
		echo "⚠️  No secrets found in vault, creating .env.example"; \
		echo "# Add your secrets here" > .env.example; \
	fi

encrypt-secrets:
	@echo "🔐 Encrypting local secrets..."
	@if [ -f .env ]; then \
		sops --encrypt --age $$(age-keygen -y ~/.age/key.txt) .env > .env.encrypted; \
		rm .env; \
		echo "✅ Secrets encrypted to .env.encrypted"; \
	fi

decrypt-secrets:
	@echo "🔓 Decrypting secrets..."
	@if [ -f .env.encrypted ]; then \
		sops --decrypt .env.encrypted > .env; \
		echo "✅ Secrets decrypted to .env"; \
	fi
EOF

# 2. Add to .gitignore
echo ".env" >> .gitignore
echo "*.env.encrypted" >> .gitignore

# 3. Add to .sops.yaml
cat > .sops.yaml << EOF
creation_rules:
  - age: "$(age-keygen -y ~/.age/key.txt)"
EOF

# 4. Setup complete!
make setup-secrets
```

## 🚀 PROJECT-SPECIFIC INTEGRATIONS

### Python/Django Projects

```python
# settings/secrets.py
import os
import subprocess
from pathlib import Path

def get_secrets():
    """Load secrets from vault or environment"""

    # Try to decrypt from vault first
    vault_path = Path.home() / 'github' / 'secrets-vault' / 'secrets.env.encrypted'

    if vault_path.exists():
        try:
            result = subprocess.run(
                ['sops', '--decrypt', str(vault_path)],
                capture_output=True,
                text=True
            )
            if result.returncode == 0:
                secrets = {}
                for line in result.stdout.strip().split('\n'):
                    if '=' in line and not line.startswith('#'):
                        key, value = line.split('=', 1)
                        secrets[key] = value
                return secrets
        except Exception as e:
            print(f"Warning: Could not decrypt vault secrets: {e}")

    # Fallback to environment variables
    return {
        'DB_HOST': os.getenv('DB_HOST', 'localhost'),
        'DB_PASSWORD': os.getenv('DB_PASSWORD', ''),
        'SECRET_KEY': os.getenv('SECRET_KEY', ''),
        'DEBUG': os.getenv('DEBUG', 'False')
    }

# Load secrets
secrets = get_secrets()

# Django settings
SECRET_KEY = secrets['SECRET_KEY']
DEBUG = secrets['DEBUG'] == 'True'
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': secrets.get('DB_NAME', 'myapp'),
        'USER': secrets.get('DB_USER', 'postgres'),
        'PASSWORD': secrets['DB_PASSWORD'],
        'HOST': secrets['DB_HOST'],
        'PORT': secrets.get('DB_PORT', '5432'),
    }
}
```

### Node.js/Express Projects

```javascript
// lib/secrets.js
const fs = require('fs');
const path = require('path');
const { execSync } = require('child_process');

class SecretsManager {
    constructor() {
        this.secrets = this.loadSecrets();
    }

    loadSecrets() {
        // Try to decrypt from vault
        const vaultPath = path.join(process.env.HOME, 'github', 'secrets-vault', 'secrets.env.encrypted');

        try {
            if (fs.existsSync(vaultPath)) {
                const decrypted = execSync(`sops --decrypt "${vaultPath}"`, { encoding: 'utf8' });
                const secrets = {};
                decrypted.split('\n').forEach(line => {
                    if (line.includes('=') && !line.startsWith('#')) {
                        const [key, value] = line.split('=', 2);
                        secrets[key] = value;
                    }
                });
                return secrets;
            }
        } catch (error) {
            console.warn('Warning: Could not load vault secrets:', error.message);
        }

        // Fallback to environment
        return {
            DB_HOST: process.env.DB_HOST || 'localhost',
            DB_PASSWORD: process.env.DB_PASSWORD || '',
            JWT_SECRET: process.env.JWT_SECRET || '',
            PORT: process.env.PORT || 3000
        };
    }

    get(key, defaultValue = '') {
        return this.secrets[key] || defaultValue;
    }

    all() {
        return this.secrets;
    }
}

module.exports = new SecretsManager();

// Usage in app.js
const secrets = require('./lib/secrets');

const app = express();
const PORT = secrets.get('PORT', 3000);

const dbConfig = {
    host: secrets.get('DB_HOST'),
    password: secrets.get('DB_PASSWORD'),
    // ... other config
};
```

### Docker/Docker Compose Projects

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    environment:
      - DB_HOST=${DB_HOST}
      - DB_PASSWORD=${DB_PASSWORD}
      - JWT_SECRET=${JWT_SECRET}
    env_file:
      - .env  # This gets created from vault

  database:
    image: postgres:13
    environment:
      - POSTGRES_DB=${DB_NAME}
      - POSTGRES_USER=${DB_USER}
      - POSTGRES_PASSWORD=${DB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

```bash
# scripts/start.sh
#!/bin/bash
set -euo pipefail

echo "🚀 Starting application with secrets..."

# Decrypt secrets from vault
VAULT_SECRETS="$HOME/github/secrets-vault/secrets.env.encrypted"

if [ -f "$VAULT_SECRETS" ]; then
    echo "📥 Loading secrets from vault..."
    sops --decrypt "$VAULT_SECRETS" > .env
    echo "✅ Secrets loaded"
else
    echo "⚠️  No vault secrets found, using environment variables"
    touch .env
fi

# Start application
docker-compose up -d

echo "🎉 Application started successfully!"
```

### Go Projects

```go
// pkg/secrets/secrets.go
package secrets

import (
    "bufio"
    "fmt"
    "os"
    "os/exec"
    "path"
    "strings"
)

type Secrets struct {
    data map[string]string
}

func NewSecrets() (*Secrets, error) {
    s := &Secrets{
        data: make(map[string]string),
    }

    // Try to load from vault
    if err := s.loadFromVault(); err != nil {
        fmt.Printf("Warning: Could not load vault secrets: %v\n", err)
        s.loadFromEnvironment()
    }

    return s, nil
}

func (s *Secrets) loadFromVault() error {
    home, _ := os.UserHomeDir()
    vaultPath := path.Join(home, "github", "secrets-vault", "secrets.env.encrypted")

    cmd := exec.Command("sops", "--decrypt", vaultPath)
    output, err := cmd.Output()
    if err != nil {
        return fmt.Errorf("failed to decrypt vault secrets: %w", err)
    }

    scanner := bufio.NewScanner(strings.NewReader(string(output)))
    for scanner.Scan() {
        line := scanner.Text()
        if strings.Contains(line, "=") && !strings.HasPrefix(line, "#") {
            parts := strings.SplitN(line, "=", 2)
            if len(parts) == 2 {
                s.data[parts[0]] = parts[1]
            }
        }
    }

    return nil
}

func (s *Secrets) loadFromEnvironment() {
    // Fallback to environment variables
    s.data["DB_HOST"] = os.Getenv("DB_HOST")
    s.data["DB_PASSWORD"] = os.Getenv("DB_PASSWORD")
    s.data["JWT_SECRET"] = os.Getenv("JWT_SECRET")
}

func (s *Secrets) Get(key, defaultValue string) string {
    if value, exists := s.data[key]; exists {
        return value
    }
    return defaultValue
}

// Usage
// secrets, _ := NewSecrets()
// dbHost := secrets.Get("DB_HOST", "localhost")
```

## 🔧 AUTOMATION TEMPLATES

### GitHub Actions Integration

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

      - name: Setup Age and SOPS
        run: |
          sudo apt-get update
          sudo apt-get install -y age sops
          echo "${{ secrets.AGE_PRIVATE_KEY }}" > ~/.age/key.txt
          chmod 600 ~/.age/key.txt

      - name: Clone Secrets Vault
        run: |
          git clone https://github.com/Khamel83/secrets-vault.git
          cd secrets-vault
          echo "${{ secrets.AGE_PRIVATE_KEY }}" > ~/.age/key.txt
          chmod 600 ~/.age/key.txt

      - name: Load Secrets
        run: |
          sops --decrypt secrets-vault/secrets.env.encrypted > .env

      - name: Run Tests
        run: |
          npm test

      - name: Deploy
        run: |
          echo "Deploying with secrets..."
          # Your deployment commands here
```

### GitLab CI Integration

```yaml
# .gitlab-ci.yml
variables:
  SECRETS_VAULT: "https://github.com/Khamel83/secrets-vault.git"

before_script:
  - apt-get update && apt-get install -y age sops git
  - echo "$AGE_PRIVATE_KEY" > ~/.age/key.txt
  - chmod 600 ~/.age/key.txt
  - git clone $SECRETS_VAULT
  - sops --decrypt secrets-vault/secrets.env.encrypted > .env

deploy:
  stage: deploy
  script:
    - echo "Deploying with loaded secrets..."
    - source .env
    - echo "Database host: $DB_HOST"
    # Your deployment commands here
  only:
    - main
```

### Systemd Service Integration

```ini
# /etc/systemd/system/myapp.service
[Unit]
Description=My Application
After=network.target

[Service]
Type=simple
User=myapp
WorkingDirectory=/opt/myapp

# Load secrets from vault
ExecStartPre=/bin/bash -c ' \
    sops --decrypt /home/myapp/github/secrets-vault/secrets.env.encrypted > /opt/myapp/.env \
    && chown myapp:myapp /opt/myapp/.env \
    && chmod 600 /opt/myapp/.env \
'

ExecStart=/usr/bin/node app.js
EnvironmentFile=/opt/myapp/.env
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

## 🚀 QUICK START TEMPLATES

### Template 1: New Python Project

```bash
# setup.sh
#!/bin/bash
set -euo pipefail

PROJECT_NAME="$1"
mkdir "$PROJECT_NAME"
cd "$PROJECT_NAME"

# Initialize git repo
git init

# Setup secrets management
cat > Makefile << 'EOF'
SECRETS_VAULT ?= ~/github/secrets-vault

setup-secrets:
	@if [ ! -d "$(SECRETS_VAULT)" ]; then \
		git clone git@github.com:Khamel83/secrets-vault.git $(SECRETS_VAULT); \
	fi
	@if [ -f "$(SECRETS_VAULT)/secrets.env.encrypted" ]; then \
		sops --decrypt "$(SECRETS_VAULT)/secrets.env.encrypted" > .env; \
		echo "✅ Secrets loaded"; \
	fi

run: setup-secrets
	python app.py
EOF

cat > requirements.txt << 'EOF'
flask
python-decouple
EOF

cat > app.py << 'EOF'
from flask import Flask
from decouple import config

app = Flask(__name__)

@app.route('/')
def home():
    db_host = config('DB_HOST', default='localhost')
    return f"Hello World! DB Host: {db_host}"

if __name__ == '__main__':
    app.run(debug=True)
EOF

echo ".env" >> .gitignore

echo "✅ Python project '$PROJECT_NAME' created with secrets management"
```

### Template 2: New Node.js Project

```bash
# setup-node.sh
#!/bin/bash
set -euo pipefail

PROJECT_NAME="$1"
mkdir "$PROJECT_NAME"
cd "$PROJECT_NAME"

# Initialize project
npm init -y

# Setup secrets management
npm install dotenv

cat > package.json << 'EOF'
{
  "name": "myapp",
  "version": "1.0.0",
  "scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js",
    "setup": "./setup-secrets.sh"
  },
  "dependencies": {
    "dotenv": "^16.0.0"
  }
}
EOF

cat > setup-secrets.sh << 'EOF'
#!/bin/bash
SECRETS_VAULT="$HOME/github/secrets-vault"

if [ -f "$SECRETS_VAULT/secrets.env.encrypted" ]; then
    sops --decrypt "$SECRETS_VAULT/secrets.env.encrypted" > .env
    echo "✅ Secrets loaded from vault"
else
    echo "⚠️ No vault secrets found, creating .env.example"
    echo "DB_HOST=localhost" > .env.example
    echo "DB_PASSWORD=" >> .env.example
fi
EOF

chmod +x setup-secrets.sh

cat > app.js << 'EOF'
require('dotenv').config();

const express = require('express');
const app = express();

app.get('/', (req, res) => {
    const dbHost = process.env.DB_HOST || 'localhost';
    res.send(`Hello World! DB Host: ${dbHost}`);
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
});
EOF

echo ".env" >> .gitignore

npm run setup

echo "✅ Node.js project '$PROJECT_NAME' created with secrets management"
```

## 📋 INTEGRATION CHECKLIST

### Before Integration

- [ ] Age key available and backed up
- [ ] secrets-vault repository cloned
- [ ] SOPS and Age tools installed
- [ ] Project has .gitignore configured

### During Integration

- [ ] Makefile targets added (setup-secrets, encrypt-secrets, decrypt-secrets)
- [ ] .sops.yaml configured with Age key
- [ ] Git hooks for secret validation (optional)
- [ ] CI/CD pipeline updated (if applicable)

### After Integration

- [ ] Secrets can be loaded from vault
- [ ] Application runs with decrypted secrets
- [ ] Git commits don't expose unencrypted secrets
- [ ] Team members can access shared secrets

## 🔧 TROUBLESHOOTING GUIDE

### Common Integration Issues

**Error:** "sops: command not found"
```bash
# Solution: Install SOPS
sudo apt install sops age  # Ubuntu/Debian
brew install sops age     # macOS
```

**Error:** "no matching keys found"
```bash
# Solution: Check Age key setup
age-keygen -y ~/.age/key.txt  # Should show your public key
ls -la ~/.age/key.txt         # Should exist with 600 permissions
```

**Error:** "secrets file not found"
```bash
# Solution: Check vault status
ls -la ~/github/secrets-vault/secrets.env.encrypted
cd ~/github/secrets-vault && git status
```

**Error:** "permission denied on secrets"
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
sops --decrypt secrets.env.encrypted | head -3

# Test Age key
age-keygen -y ~/.age/key.txt | grep age1

# Test SOPS configuration
sops --encrypt --age $(age-keygen -y ~/.age/key.txt) test.txt > test.txt.encrypted
```

## 🎯 SUCCESS METRICS

Your integration is successful when:

✅ **One command setup** works (`make setup-secrets`)
✅ **Secrets automatically loaded** on application start
✅ **No unencrypted secrets** in git repository
✅ **Team collaboration** works seamlessly
✅ **CI/CD integration** loads secrets automatically
✅ **Zero manual secret management** required

---

**🎉 Your project now has enterprise-grade secret management!**

All your applications can now securely access centralized secrets with zero manual configuration.

---

*For support, check the main secrets-vault repository documentation.*