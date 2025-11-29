# Secrets Vault

**Your API keys are encrypted in this repo. Only you can decrypt them.**

## 🚀 Universal Setup

```bash
# 1. Get your private key from 1Password
echo "[YOUR_PRIVATE_KEY]" > ~/.age/key.txt
export SOPS_AGE_KEY="[YOUR_PRIVATE_KEY]"

# 2. Clone and decrypt
git clone https://github.com/Khamel83/secrets-vault.git ~/.secrets-vault
sops --decrypt ~/.secrets-vault/secrets.env.encrypted > .env

# 3. Done! All your API keys are in .env
cat .env
```

## 📋 What you need to know

1. **Save this URL:** `https://github.com/Khamel83/secrets-vault`
2. **Save your private key** in 1Password
3. **Copy-paste the commands** above to any project

That's it. Works anywhere.