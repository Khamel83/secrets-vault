# Emergency Recovery Procedures

## 🚨 CRITICAL: READ THIS BEFORE ANY EMERGENCY

**Your secrets are encrypted with SOPS + Age. Without the Age key, encrypted files CANNOT be recovered.**

**This document is your emergency manual. Save it offline and understand it BEFORE you need it.**

## 🔑 MASTER KEY RECOVERY

### Primary Age Key (KEEP SECURE)

```
AGE-SECRET-KEY-16PQX7DRJVAX79JSRL27TYERR5ATLNWYP8LV9GP88SM4VFAEA5UHQYVNMFY
```

**Public Key:** `age1a8cq06ufznc3kq5a2a4gk5ntqywqk6km8s8k2ntnp8l4kqy4ulqsx7dv5q`

### Emergency Key Locations

Store your Age key in multiple secure locations:

```
🖥️  Primary:   ~/.age/key.txt
💾  USB Drive:  /media/usb/emergency-key.txt
☁️  Cloud:      Password manager (encrypted note)
🏠  Physical:   Safe deposit box (printed copy)
📱  Mobile:     Encrypted note app
👥  Trusted:     Shared with 1-2 trusted contacts
```

### Key Backup Procedures

```bash
# 1. Create encrypted backup
age --encrypt -r age1a8cq06ufznc3kq5a2a4gk5ntqywqk6km8s8k2ntnp8l4kqy4ulqsx7dv5q \
    ~/.age/key.txt > key-backup.age

# 2. Split into multiple locations
cp key-backup.age /media/usb/emergency-key.age
cp key-backup.age ~/Dropbox/emergency-key.age
cp key-backup.age ~/emergency-key.age

# 3. Print physical copy (store securely)
age --decrypt key-backup.age > emergency-key.txt
lp emergency-key.txt  # Print to paper
shred emergency-key.txt  # Secure delete
```

## 🆘 EMERGENCY SCENARIOS

### Scenario 1: Lost Age Key (CRITICAL)

**Symptoms:**
- Cannot decrypt `*.encrypted` files
- SOPS shows "no matching keys found"
- All secrets are inaccessible

**IMMEDIATE ACTIONS:**

1. **DON'T PANIC** - Check all backup locations
2. **Search systematically:**

```bash
# Check all possible locations
find ~ -name "*key*" -type f 2>/dev/null
find /media -name "*key*" -type f 2>/dev/null
find /mnt -name "*key*" -type f 2>/dev/null

# Check cloud sync folders
find ~/Dropbox -name "*key*" 2>/dev/null
find ~/Google\ Drive -name "*key*" 2>/dev/null
find ~/OneDrive -name "*key*" 2>/dev/null
```

3. **Check password managers:**
   - 1Password: Look for "Age Key" or "Secrets Vault Key"
   - LastPass: Search vault for "AGE" or "secret"
   - Bitwarden: Check secure notes for key

4. **Check team members** (if you shared):
   ```bash
   # Ask collaborators to check their keys
   age-keygen -y ~/.age/key.txt  # Show them what to look for
   ```

**IF KEY IS PERMANENTLY LOST:**

❌ **BAD NEWS:** All encrypted secrets are permanently lost
✅ **RECOVERY PLAN:** Regenerate everything from scratch:

```bash
# 1. Generate new Age key
age-keygen -o ~/.age/key.txt

# 2. Update all .sops.yaml files with new public key
find ~/github -name ".sops.yaml" -exec sed -i "s/age1[^\"]*/$(age-keygen -y ~/.age/key.txt)/g" {} \;

# 3. Re-create all secrets from memory/documentation
# 4. Re-encrypt all files with new key
find . -name "*.encrypted" -exec rm {} \;  # Delete old encrypted files

# 5. Update all CI/CD, deployment scripts, team access
```

### Scenario 2: Corrupted Secrets File

**Symptoms:**
- SOPS decryption fails with file format errors
- Partial or corrupted secret values
- File size seems wrong

**RECOVERY STEPS:**

```bash
# 1. Check git history for working version
cd ~/github/secrets-vault
git log --oneline --follow secrets.env.encrypted
git show HEAD~1:secrets.env.encrypted > secrets.env.encrypted.backup

# 2. Try to decrypt backup version
sops --decrypt secrets.env.encrypted.backup > test-decrypt.txt

# 3. If backup works, restore it
cp secrets.env.encrypted.backup secrets.env.encrypted
git add secrets.env.encrypted
git commit -m "Emergency restore from git history"
```

**IF NO WORKING BACKUP:**

```bash
# 1. Check if partial decryption works
sops --decrypt secrets.env.encrypted 2>&1 | head -20

# 2. Try different SOPS versions
sops --version  # Check current version

# 3. Manual reconstruction from application configs
find ~/github -name "*.env.example" -o -name "config*.example" | xargs cat
```

### Scenario 3: Team Member Lost Access

**Symptoms:**
- Team member cannot decrypt shared secrets
- Git commits failing due to missing keys

**RECOVERY PROCEDURE:**

```bash
# 1. Check current encryption recipients
sops --decrypt secrets.env.encrypted --print-pgp

# 2. Add missing team member's key to .sops.yaml
cat > .sops.yaml << EOF
creation_rules:
  - age: "$(age-keygen -y ~/.age/key.txt),age1teammate-key-here"
EOF

# 3. Re-encrypt with updated recipients
sops --encrypt --age "$(age-keygen -y ~/.age/key.txt),age1teammate-key-here" \
    secrets.env.encrypted > secrets.env.encrypted.new

# 4. Replace old file
mv secrets.env.encrypted.new secrets.env.encrypted
git add .sops.yaml secrets.env.encrypted
git commit -m "Add team member to secrets access"
```

### Scenario 4: CI/CD Pipeline Cannot Access Secrets

**Symptoms:**
- Deployments failing with secret access errors
- GitHub Actions/GitLab CI not loading secrets

**IMMEDIATE FIX:**

```bash
# 1. Check CI/CD secret storage
gh secret list  # GitHub
gitlab-ci variable list  # GitLab

# 2. Update CI/CD with Age key
# GitHub:
gh secret set AGE_PRIVATE_KEY --body "$(cat ~/.age/key.txt)"

# GitLab:
gitlab-ci variable set AGE_PRIVATE_KEY "$(cat ~/.age/key.txt)" --masked

# 3. Update pipeline configuration
# GitHub Actions (.github/workflows/deploy.yml):
- name: Setup Age key
  run: |
    echo "${{ secrets.AGE_PRIVATE_KEY }}" > ~/.age/key.txt
    chmod 600 ~/.age/key.txt

# 4. Test pipeline manually
gh workflow run "Deploy Application"
```

### Scenario 5: Accidentally Committed Unencrypted Secrets

**Symptoms:**
- Real secrets in git history
- Security breach detected

**EMERGENCY RESPONSE:**

```bash
# 1. IMMEDIATE DAMAGE CONTROL
git log --all --full-history -- *.env
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch *.env' \
  --prune-empty --tag-name-filter cat -- --all

# 2. Force push to remove from history
git push origin --force --all
git push origin --force --tags

# 3. Rotate ALL compromised secrets
# Generate new passwords, API keys, tokens

# 4. Re-encrypt with new values
sops secrets.env.encrypted  # Edit to update values

# 5. Update all services/applications with new secrets
# This may require application restarts and configuration updates
```

## 🛠️ RECOVERY TOOLS

### Emergency Decryption Script

```bash
#!/bin/bash
# emergency-decrypt.sh - Emergency secret recovery

set -euo pipefail

AGE_KEY_LOCATIONS=(
    "$HOME/.age/key.txt"
    "$HOME/emergency-key.txt"
    "/media/usb/emergency-key.txt"
    "$HOME/Dropbox/emergency-key.age"
)

SECRET_FILES=(
    "$HOME/github/secrets-vault/secrets.env.encrypted"
    "$HOME/github/homelab/.env.encrypted"
)

echo "🚨 EMERGENCY SECRET RECOVERY"
echo "==============================="

# Try to find Age key
AGE_KEY=""
for location in "${AGE_KEY_LOCATIONS[@]}"; do
    if [ -f "$location" ]; then
        if [[ "$location" == *.age ]]; then
            # Encrypted backup file
            echo "Found encrypted key at: $location"
            read -p "Enter decryption password: " -s password
            if echo "$password" | age --decrypt -o /tmp/key.txt "$location" 2>/dev/null; then
                AGE_KEY="/tmp/key.txt"
                break
            fi
        else
            # Direct key file
            echo "Found Age key at: $location"
            AGE_KEY="$location"
            break
        fi
    fi
done

if [ -z "$AGE_KEY" ]; then
    echo "❌ NO AGE KEY FOUND IN ANY LOCATION"
    echo "❌ SECRETS CANNOT BE RECOVERED"
    exit 1
fi

echo "✅ Age key found: $AGE_KEY"

# Try to decrypt each secret file
for secret_file in "${SECRET_FILES[@]}"; do
    if [ -f "$secret_file" ]; then
        echo "🔓 Attempting to decrypt: $secret_file"

        output_file="${secret_file%.encrypted}.decrypted"

        if sops --decrypt "$secret_file" > "$output_file" 2>/dev/null; then
            echo "✅ Successfully decrypted: $output_file"
        else
            echo "❌ Failed to decrypt: $secret_file"
        fi
    fi
done

echo "🎯 Emergency recovery completed"
```

### Key Verification Tool

```bash
#!/bin/bash
# verify-key.sh - Verify Age key and secret access

set -euo pipefail

echo "🔑 AGE KEY VERIFICATION"
echo "======================"

# Check if Age key exists
if [ ! -f ~/.age/key.txt ]; then
    echo "❌ Age key not found at ~/.age/key.txt"
    exit 1
fi

echo "✅ Age key found"

# Verify key format
if grep -q "AGE-SECRET-KEY-1" ~/.age/key.txt; then
    echo "✅ Key format is valid"
else
    echo "❌ Invalid key format"
    exit 1
fi

# Show public key
PUBLIC_KEY=$(age-keygen -y ~/.age/key.txt)
echo "📤 Public key: $PUBLIC_KEY"

# Test encryption/decryption
TEST_DATA="emergency-test-$(date +%s)"
echo "$TEST_DATA" | age --encrypt -r "$PUBLIC_KEY" | age --decrypt -o /tmp/test.txt

if [ "$(cat /tmp/test.txt)" = "$TEST_DATA" ]; then
    echo "✅ Key encryption/decryption working"
else
    echo "❌ Key encryption/decryption failed"
    exit 1
fi

rm -f /tmp/test.txt

# Test with actual secrets
if [ -f ~/github/secrets-vault/secrets.env.encrypted ]; then
    if sops --decrypt ~/github/secrets-vault/secrets.env.encrypted > /tmp/secrets-test.txt 2>/dev/null; then
        echo "✅ Can decrypt secrets vault"
        echo "📋 Found $(wc -l < /tmp/secrets-test.txt) lines of secrets"
        rm -f /tmp/secrets-test.txt
    else
        echo "❌ Cannot decrypt secrets vault"
        exit 1
    fi
else
    echo "⚠️  Secrets vault not found"
fi

echo "🎉 Key verification completed successfully"
```

## 📋 PREVENTION CHECKLIST

### Daily Maintenance

- [ ] Age key backed up in multiple locations
- [ ] Test decryption of one secret file
- [ ] Verify backup copies are accessible
- [ ] Check git has no unencrypted secrets

### Weekly Checks

- [ ] Verify team member key access
- [ ] Test CI/CD pipeline with secrets
- [ ] Rotate critical passwords/API keys
- [ ] Update emergency documentation

### Monthly Reviews

- [ ] Review who has access to secrets
- [ ] Update emergency contact information
- [ ] Test full disaster recovery procedures
- [ ] Audit secret usage and access logs

### Immediate Actions After Any Change

```bash
# After adding new secrets
make encrypt-secrets && git add .env.encrypted && git commit -m "Add new secrets"

# After team member changes
git pull origin main && make decrypt-secrets && verify-access.sh

# After system changes
test-secret-access.sh && verify-key.sh
```

## 🚨 CONTACT PROCEDURES

### Emergency Contacts

1. **Primary System Administrator:** [Name and contact]
2. **Backup System Administrator:** [Name and contact]
3. **Security Team:** [Email/phone]
4. **Management:** [Name and contact]

### Emergency Response Protocol

1. **Assess Impact Level:**
   - **Low:** Single user, non-critical service
   - **Medium:** Multiple users, important service
   - **High:** Production systems, customer data
   - **Critical:** Entire infrastructure, security breach

2. **Immediate Communication:**
   - Alert appropriate contacts based on impact level
   - Document all actions taken
   - Timeline of events

3. **Recovery Execution:**
   - Follow documented procedures
   - Test at each step
   - Document successes and failures

4. **Post-Incident Review:**
   - Root cause analysis
   - Process improvements
   - Update emergency procedures

## 📚 REFERENCE MATERIALS

### Command Quick Reference

```bash
# Age key operations
age-keygen -o key.txt                    # Generate new key
age-keygen -y key.txt                    # Show public key
age --decrypt encrypted.txt > plain.txt  # Decrypt file
age --encrypt -r publicKey file.txt > encrypted.txt  # Encrypt file

# SOPS operations
sops --encrypt file.txt > file.txt.encrypted    # Encrypt file
sops --decrypt file.txt.encrypted > file.txt     # Decrypt file
sops --rotate file.txt.encrypted                 # Rotate encryption
sops file.txt.encrypted                          # Edit encrypted file

# Git history cleanup
git filter-branch --force --index-filter 'git rm --cached file.txt' --prune-empty --tag-name-filter cat -- --all

# Emergency key search
find ~ -name "*age*" -type f 2>/dev/null
find /media -name "*key*" -type f 2>/dev/null
```

### File Locations Reference

```
Age Keys:
  Primary: ~/.age/key.txt
  Backup: ~/emergency-key.txt
  USB: /media/usb/emergency-key.age
  Cloud: ~/Dropbox/emergency-key.age

Secrets Files:
  Main: ~/github/secrets-vault/secrets.env.encrypted
  Project: ~/github/[project]/.env.encrypted
  Config: ~/github/[project]/.sops.yaml

Emergency Scripts:
  Decrypt: ~/github/secrets-vault/emergency-decrypt.sh
  Verify: ~/github/secrets-vault/verify-key.sh
  Backup: ~/github/secrets-vault/backup-secrets.sh
```

---

**⚠️ IMPORTANT: Print this document and store it securely with your Age key backup. Digital access may not be available during emergencies.**

**🔒 SECURITY: This document contains sensitive information. Store it with the same security level as your secrets.**

---

*For updates or questions, maintain this document as part of your secrets vault repository.*