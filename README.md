# vesper-sm

A secure, portable command-line secrets management tool built in Go. Store, manage, and sync encrypted secrets across devices with military-grade encryption and a developer-friendly CLI.

## Features

- 🔐 **Military-grade encryption**: AES-256-GCM with Argon2id key derivation
- 🗂️ **Multi-vault support**: Manage multiple encrypted vaults with XOR key wrapping
- 💾 **Flexible storage**: JSON files for personal use, SQLite for teams (coming soon)
- 🔄 **Cloud sync**: Dropbox integration with more providers planned
- 🖥️ **CLI-first design**: Fast, scriptable, and intuitive commands
- 🔒 **Security focused**: Encryption at rest, minimal memory exposure, safe key handling

## Installation

```bash
go install github.com/david22573/vesper-sm/cmd/vsm@latest
```

Or build from source:

```bash
git clone https://github.com/david22573/vesper-sm.git
cd vesper-sm
go build -o vsm cmd/vsm/main.go
```

## Quick Start

```bash
# Create a new vault
vsm init my-vault

# Unlock vault for use
vsm unlock my-vault

# Add a secret
vsm add github_token ghp_xxxxxxxxxxxx

# Retrieve a secret
vsm get github_token

# List all keys
vsm list

# Lock vault when done
vsm lock my-vault
```

## Usage

### Vault Management

```bash
# Create a new vault with custom settings
vsm init <vault-name> [--path /custom/path]

# Unlock a vault (prompts for password)
vsm unlock <vault-name>

# Lock a vault immediately
vsm lock <vault-name>

# List all vaults
vsm vaults

# Delete a vault (requires confirmation)
vsm delete <vault-name>
```

### Secret Operations

```bash
# Add or update a secret
vsm add <key> <value>
vsm add <key>  # Prompts for value (hidden input)

# Get a secret
vsm get <key>
vsm get <key> --copy  # Copy to clipboard instead of stdout

# Delete a secret
vsm rm <key>

# List all keys in current vault
vsm list
vsm list --verbose  # Show metadata

# Search for keys
vsm search <pattern>
```

### Backup & Export

```bash
# Create encrypted backup
vsm backup <vault-name> --output backup.vault

# Export to JSON (decrypted - use with caution!)
vsm export <vault-name> --format json > secrets.json

# Import from backup
vsm import backup.vault --as restored-vault
```

### Sync (Coming Soon)

```bash
# Configure Dropbox sync
vsm sync setup dropbox

# Push local changes
vsm sync push

# Pull remote changes
vsm sync pull

# Auto-sync mode
vsm sync --auto
```

## Architecture

### Project Structure

```
vesper-sm/
├── cmd/                 # CLI entry points
│   └── vsm/            # Main binary
├── internal/
│   ├── vault/          # Core vault operations
│   │   ├── encryption/ # Crypto implementations
│   │   ├── storage/    # Storage backends
│   │   └── manager.go  # Multi-vault management
│   ├── sync/           # Sync providers
│   ├── cli/            # Command handling
│   └── config/         # Configuration
├── pkg/                # Public APIs
└── tests/              # Integration tests
```

### Security Design

- **Key Derivation**: Argon2id with configurable parameters
- **Encryption**: AES-256-GCM for all stored data
- **Key Wrapping**: XOR-based wrapping for multi-vault scenarios
- **Memory Safety**: Secrets zeroed after use, minimal exposure time
- **File Permissions**: Restrictive permissions on vault files (0600)

### Storage Formats

#### JSON Vault (Default)
```json
{
  "version": "1.0",
  "salt": "base64_encoded_salt",
  "nonce": "base64_encoded_nonce",
  "encrypted_data": "base64_encoded_ciphertext",
  "metadata": {
    "created": "2024-01-01T00:00:00Z",
    "modified": "2024-01-01T00:00:00Z",
    "algorithm": "aes-256-gcm"
  }
}
```

## Development Roadmap

### Phase 1: Core Vault ✅
- [x] Vault creation and management
- [x] Argon2id key derivation
- [x] AES-GCM encryption
- [x] XOR key wrapping
- [x] JSON storage backend

### Phase 2: CLI 🚧
- [x] Basic CRUD operations
- [x] Multi-vault support
- [ ] Clipboard integration
- [ ] Environment variable injection
- [ ] Shell completions

### Phase 3: Storage Backends
- [x] JSON file vaults
- [ ] SQLite for structured storage
- [ ] Redis for distributed caching
- [ ] Vault (HashiCorp) integration

### Phase 4: Sync Providers
- [ ] Dropbox API
- [ ] Git-backed sync
- [ ] AWS S3
- [ ] Google Drive
- [ ] WebDAV

### Phase 5: Advanced Features
- [ ] Auto-lock with timeout
- [ ] Password rotation/rekeying
- [ ] Audit logging
- [ ] Hardware key support (YubiKey, TPM)
- [ ] Secret sharing (Shamir's algorithm)
- [ ] Team/workspace management

## Configuration

Default configuration location: `~/.config/vesper-sm/config.yaml`

```yaml
default_vault: "personal"
vault_dir: "~/.vesper-sm/vaults"
auto_lock_timeout: 300  # seconds
clipboard_timeout: 45   # seconds
encryption:
  algorithm: "aes-256-gcm"
  argon2:
    time: 1
    memory: 64*1024
    threads: 4
```

## Security Considerations

1. **Never commit vault files** to version control
2. **Use strong, unique passwords** for each vault
3. **Enable auto-lock** for unattended terminals
4. **Regular backups** to secure locations
5. **Audit access logs** in team environments

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Development Setup

```bash
# Clone repository
git clone https://github.com/david22573/vesper-sm.git
cd vesper-sm

# Install dependencies
go mod download

# Run tests
go test ./...

# Run with race detector
go test -race ./...

# Build for all platforms
make build-all
```

## Testing

```bash
# Unit tests
go test ./internal/...

# Integration tests
go test ./tests/integration/...

# Security tests
go test ./tests/security/...

# Benchmark encryption
go test -bench=. ./internal/vault/encryption/
```

## License

MIT License - see [LICENSE](LICENSE) file for details

## Acknowledgments

- Argon2 for password hashing
- AES-GCM for authenticated encryption
- Cobra for CLI framework
- SQLite for structured storage

## Support

- **Issues**: [GitHub Issues](https://github.com/david22573/vesper-sm/issues)
- **Discussions**: [GitHub Discussions](https://github.com/david22573/vesper-sm/discussions)
- **Security**: Report vulnerabilities to security@david22573.dev

---

**⚠️ Disclaimer**: This tool provides strong encryption but is not a substitute for professional security auditing. Use at your own risk for sensitive data.
