# SOPS (Secrets OPerationS) Setup Guide

## 🎯 OVERVIEW

SOPS + Age provides enterprise-grade secret management for all your projects. This guide walks through setting up encrypted secrets that work across all your GitHub repositories.

## 🔐 WHAT THIS PROVIDES

- **Encrypted secrets in Git** - Store secrets safely in version control
- **Cross-project compatibility** - One Age key works for all repositories
- **Auto-decryption on startup** - Secrets automatically available when needed
- **Professional security** - End-to-end encryption with no plaintext exposure

## 🚀 QUICK START

### Prerequisites

```bash
# Install SOPS and Age
# Ubuntu/Debian:
sudo apt update && sudo apt install sops age -y

# macOS:
brew install sops age

# Verify installation
sops --version
age --version
```

### Age Key Setup

```bash
# Generate Age key (if you don't have one)
age-keygen -o ~/.age/key.txt

# View your key (keep this secure!)
cat ~/.age/key.txt

# Your key should end with: NMFY
# Example: AGE-SECRET-KEY-16PQX7DRJVAX79JSRL27TYERR5ATLNWYP8LV9GP88SM4VFAEA5UHQYVNMFY
```

### SOPS Configuration

Create `.sops.yaml` in any project:

```yaml
creation_rules:
  - age: "age1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
    # Replace with your actual Age public key
    # Get public key with: age-keygen -y ~/.age/key.txt
```

### Encrypt Your First Secret

```bash
# Create .env file with secrets
cat > .env << EOF
# Database credentials
DB_HOST=localhost
DB_USER=myapp
DB_PASSWORD=supersecretpassword123
DB_NAME=mydatabase

# API keys
API_KEY=sk-1234567890abcdef
JWT_SECRET=your-super-secret-jwt-key-here
EOF

# Encrypt the .env file
sops --encrypt --age $(age-keygen -y ~/.age/key.txt) .env > .env.encrypted

# Remove unencrypted version
rm .env

# Verify encryption worked
ls -la .env*
```

### Decrypt When Needed

```bash
# Decrypt to use secrets
sops --decrypt .env.encrypted > .env

# Load secrets in your application
source .env

# Remove unencrypted version after use
rm .env
```

## 🔧 AUTOMATED WORKFLOW

### Makefile Integration

Add to your `Makefile`:

```makefile
# SOPS integration
.PHONY: decrypt-encrypt clean-secrets setup-secrets

setup-secrets:
	@echo "🔐 Setting up SOPS encryption..."
	@if [ ! -f ~/.age/key.txt ]; then \
		age-keygen -o ~/.age/key.txt; \
		echo "✅ Age key generated at ~/.age/key.txt"; \
		echo "🔑 Your public key: $$(age-keygen -y ~/.age/key.txt)"; \
		echo "⚠️  Keep this key secure!"; \
	fi
	@if [ ! -f .sops.yaml ]; then \
		echo "Creating .sops.yaml..."; \
		echo "creation_rules:" > .sops.yaml; \
		echo "  - age: \"$$(age-keygen -y ~/.age/key.txt)\"" >> .sops.yaml; \
		echo "✅ .sops.yaml created"; \
	fi

decrypt-secrets:
	@echo "🔓 Decrypting secrets..."
	@if [ -f .env.encrypted ]; then \
		sops --decrypt .env.encrypted > .env; \
		echo "✅ Secrets decrypted to .env"; \
	else \
		echo "❌ No .env.encrypted file found"; \
	fi

encrypt-secrets:
	@echo "🔐 Encrypting secrets..."
	@if [ -f .env ]; then \
		sops --encrypt --age $$(age-keygen -y ~/.age/key.txt) .env > .env.encrypted; \
		rm .env; \
		echo "✅ Secrets encrypted to .env.encrypted"; \
	else \
		echo "❌ No .env file found"; \
	fi

clean-secrets:
	@echo "🧹 Cleaning unencrypted secrets..."
	@rm -f .env .env.*.backup .env.*.tmp
	@echo "✅ Unencrypted secrets removed"

# Auto-decrypt on service start
up: decrypt-secrets
	@echo "🚀 Starting services with decrypted secrets..."
	# Your service start commands here
	docker-compose up -d

# Auto-encrypt on service stop
down: encrypt-secrets
	@echo "🛑 Stopping services and encrypting secrets..."
	docker-compose down
```

### Docker Integration

For Docker Compose projects:

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    env_file:
      - .env  # This gets created from .env.encrypted
    # Your service configuration...

# In your scripts:
# Run: make decrypt-secrets && docker-compose up -d
# Stop: make encrypt-secrets && docker-compose down
```

## 🔐 SECURITY BEST PRACTICES

### Key Management

```bash
# Backup your Age key securely
cp ~/.age/key.txt ~/.age/key.txt.backup

# Set proper permissions
chmod 600 ~/.age/key.txt
chmod 600 ~/.age/key.txt.backup

# Add to .gitignore
echo "key.txt" >> ~/.gitignore
echo ".env" >> .gitignore
echo ".env.*.backup" >> .gitignore
echo ".env.*.tmp" >> .gitignore
```

### Team Sharing

```bash
# Share your public key with team members
age-keygen -y ~/.age/key.txt

# Add multiple recipients to .sops.yaml
cat > .sops.yaml << EOF
creation_rules:
  - age: "age1your-key-here,age1teammate-key-here,age1another-key-here"
EOF
```

### Git Safety

```bash
# Always ensure secrets are encrypted before committing
if git status --porcelain | grep -q "^M.*\.env$"; then
    echo "❌ ERROR: Unencrypted .env file detected!"
    echo "Please encrypt with: sops --encrypt --age \$(age-keygen -y ~/.age/key.txt) .env > .env.encrypted"
    exit 1
fi
```

## 🚨 EMERGENCY PROCEDURES

### Lost Age Key

```bash
# If you lose your Age key, encrypted files cannot be recovered
# This is why backup is critical!

# Emergency backup procedure:
1. Store Age key in password manager
2. Print key and store in safe deposit box
3. Share with trusted team member
4. Use multiple encryption recipients
```

### Key Rotation

```bash
# Generate new key
age-keygen -o ~/.age/key.new.txt

# Update .sops.yaml with new public key
# Re-encrypt all files with new key
find . -name "*.encrypted" -exec sops --encrypt --age $(age-keygen -y ~/.age/key.new.txt) {} {} \;

# Update all systems to use new key
mv ~/.age/key.txt ~/.age/key.old.txt
mv ~/.age/key.new.txt ~/.age/key.txt
```

## 🔧 TROUBLESHOOTING

### Common Issues

```bash
# Error: "no matching keys found"
# Solution: Check your Age key path and permissions
ls -la ~/.age/key.txt
chmod 600 ~/.age/key.txt

# Error: "file is not encrypted"
# Solution: Ensure file was properly encrypted with sops
sops --encrypt --age $(age-keygen -y ~/.age/key.txt) input.txt > output.txt

# Error: "could not decrypt"
# Solution: Verify you have the correct Age key
age-keygen -y ~/.age/key.txt  # Should show the public key
```

### Debug Commands

```bash
# Test SOPS installation
sops --version
age --version

# Test key generation
age-keygen -y ~/.age/key.txt

# Test encryption
echo "test" | sops --encrypt --age $(age-keygen -y ~/.age/key.txt) /dev/stdin

# Test decryption
echo "test" | sops --decrypt /dev/stdin
```

## 🎯 SUCCESS CRITERIA

You have successfully set up SOPS when:

✅ **Age key generated** and backed up securely
✅ **.sops.yaml configured** in your projects
✅ **Secrets encrypted** with `*.encrypted` extension
✅ **.env files** never committed to Git
✅ **Auto-decryption** working on service startup
✅ **Auto-encryption** working on service stop
✅ **Team sharing** capability (if needed)

## 📚 NEXT STEPS

1. **Set up secrets-vault** as your centralized secret management repository
2. **Add cross-project integration** for all your GitHub repositories
3. **Implement backup procedures** for your Age key
4. **Train your team** on SOPS workflow
5. **Set up monitoring** for secret access and rotation

---

**🎉 Congratulations! You now have enterprise-grade secret management!**

Your secrets are now securely encrypted in Git, automatically decrypted when needed, and professionally managed across all your projects.

---

*For support or questions, refer to the main secrets-vault repository documentation.*