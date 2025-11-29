# Secrets Vault

Public repo: https://github.com/Khamel83/secrets-vault

## Universal Setup
Clone and decrypt with your private key from 1Password:
```bash
git clone https://github.com/Khamel83/secrets-vault.git ~/.secrets-vault
echo [YOUR_PRIVATE_KEY_FROM_1PASSWORD] > ~/.age/key.txt
export SOPS_AGE_KEY="[YOUR_PRIVATE_KEY_FROM_1PASSWORD]"
sops --decrypt ~/.secrets-vault/secrets.env.encrypted > .env
```

That's it. Works anywhere, anytime.
