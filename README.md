# vesper-sm

A lightweight command-line secrets manager written in Go.  
The goal is simple: keep your secrets safe, portable, and easy to use without dragging in heavyweight infrastructure.

## Features

- AES-256-GCM encryption with Argon2id key derivation
- Support for multiple vaults
- JSON-based vaults (default), with SQLite backend planned
- Basic key/value operations via a CLI (`add`, `get`, `list`, `rm`)
- Sync support starting with Dropbox (more providers later)

## Install

```bash
go install github.com/david22573/vesper-sm/cmd/vsm@latest
```

Or build manually:

```bash
git clone https://github.com/david22573/vesper-sm.git
cd vesper-sm
go build -o vsm ./cmd/vsm
```

## Quick Start

```bash
# Create a new vault
vsm init my-vault

# Unlock vault (prompts for password)
vsm unlock my-vault

# Add a secret
vsm add github_token ghp_xxxxxxxxxxxx

# Retrieve a secret
vsm get github_token

# List all keys
vsm list

# Lock the vault when done
vsm lock my-vault
```

## Roadmap

- [ ] Vault creation and encryption
- [ ] CRUD operations for secrets
- [ ] Multi-vault support
- [ ] Clipboard + environment variable helpers
- [ ] SQLite backend
- [ ] Dropbox sync

*(Future plans like other cloud providers, YubiKey support, and team features will live in [ROADMAP.md](ROADMAP.md).)*

## Project Layout

```
vesper-sm/
├── cmd/vsm/           # CLI entry point
├── internal/vault/    # Core vault operations
│   ├── encryption/    # Crypto primitives
│   ├── storage/       # Storage backends
│   └── manager.go     # Vault manager
├── internal/sync/     # Sync providers
└── tests/             # Integration tests
```

## Security Notes

- Uses Argon2id for password-based key derivation  
- AES-256-GCM for all stored data  
- Vault files default to `0600` permissions  
- Secrets are cleared from memory after use as best as possible in Go  

⚠️ **Disclaimer**: While the project follows good cryptographic practices, it has not been independently audited. Use at your own risk for sensitive data.

## Contributing

Bug reports, feature ideas, and PRs are welcome.  
See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## License

MIT — see [LICENSE](LICENSE).
