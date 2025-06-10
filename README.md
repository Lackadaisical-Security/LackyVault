# LackyVault

<p align="center">
  <img src="../src/resources/icons/logo.png" alt="LackyVault Logo" width="200"/>
</p>

<p align="center">
  <strong>Zero-Dependency Crypto Wallet with Military-Grade Security</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/language-C-blue.svg" alt="Language C"/>
  <img src="https://img.shields.io/badge/assembly-x86--64-red.svg" alt="Assembly x86-64"/>
  <img src="https://img.shields.io/badge/platform-Windows-brightgreen.svg" alt="Platform Windows"/>
  <img src="https://img.shields.io/badge/dependencies-zero-green.svg" alt="Dependencies Zero"/>
  <img src="https://img.shields.io/badge/security-military--grade-orange.svg" alt="Security Military-Grade"/>
</p>

## Overview

LackyVault is a paranoid cryptocurrency wallet built with zero external dependencies, using only x86-64 Assembly and C with the Windows API. The implementation follows maximum-security principles with custom cryptographic primitives and an authentic 80s retro aesthetic.

## Key Features

### Zero-Dependency Architecture
- **No External Libraries**: Every component built from scratch
- **Custom Crypto**: Hand-crafted cryptographic primitives
- **Win32 API Only**: Minimal system dependencies
- **Static Linking**: Fully contained executable

### Military-Grade Security
- **Anti-Debugging Protection**: Advanced techniques to prevent analysis
- **Secure Memory Management**: Guard pages, zeroization, and ASLR
- **Side-Channel Resistance**: Constant-time operations for sensitive data
- **Hardware Security Integration**: TPM 2.0, hardware wallets, and RNG validation

### Multi-Chain Support
- **Bitcoin**: Full BIP32/39/44 implementation with Taproot/Schnorr
- **Ethereum**: Complete transaction support with EIP-1559
- **Monero**: RingCT privacy features
- **Lightning Network**: Payment channel support
- **Cross-Chain**: Atomic swap capability

### Retro Aesthetic
- **80s Cyberpunk Design**: Authentic retro visuals
- **Animated Grids**: Dynamic background effects
- **Neon Typography**: Multi-layer glow effects
- **Theme System**: Customizable visual styles

## Security Features

### Anti-Analysis Protection
- PEB-based debugger detection
- NtGlobalFlag analysis
- Hardware breakpoint detection
- RDTSC timing analysis
- Hypervisor detection (VMware, VirtualBox, Hyper-V, QEMU)
- IDT location analysis

### Cryptographic Security
- **AES-256-GCM**: Hardware-accelerated when available
- **ChaCha20-Poly1305**: Stream cipher with authentication
- **SHA-256/512**: Secure hash algorithms
- **Ed25519/Curve25519**: Elliptic curve cryptography
- **Argon2id**: Memory-hard password hashing
- **secp256k1**: Bitcoin-compatible signatures

### Network Security
- **Custom TLS 1.3**: Secure communication
- **Proxy Chaining**: SOCKS5/HTTP multi-layer routing
- **Tor Integration**: Onion routing for anonymity
- **DNS-over-HTTPS**: Censorship-resistant name resolution

### Hardware Integration
- **Ledger/Trezor Support**: Hardware wallet communication
- **TPM 2.0**: Secure key storage
- **Hardware RNG Validation**: Entropy verification
- **FIDO2/WebAuthn**: Universal second factor authentication

## Architecture

### Memory Model
- **Secure Heap**: VirtualAlloc with PAGE_GUARD protection
- **Stack Protection**: Compiler-generated canaries
- **ASLR/DEP**: Address space randomization and execution prevention
- **Secure Cleanup**: Multi-pass memory zeroization

### Threading Model
- **UI Thread**: Main message loop with GDI rendering
- **Crypto Thread**: Asynchronous cryptographic operations
- **Network Thread**: Non-blocking I/O with timeout management
- **Security Thread**: Continuous anti-analysis monitoring

### Event System
- **Lock-free Queues**: Atomic operations for high performance
- **Priority Scheduling**: Security events take precedence
- **Resource Management**: Automatic cleanup on shutdown

## Building from Source

### Prerequisites
- Windows 10/11 (x64)
- NASM 2.15+ (Assembly)
- GCC 11+ (MinGW-w64)
- WiX Toolset 3.11+ (Installer, optional)

### Basic Build

```powershell
# Clone repository
git clone https://github.com/LackadaisicalSecurity/LackyVault.git
cd LackyVault

# Build release version
.\build_enhanced.ps1 -Release

# Run tests
.\build_enhanced.ps1 -Test
```

### Advanced Build Options

```powershell
# Build everything (release, debug, tests, installer)
.\build_enhanced.ps1 -All

# Build debug version with enhanced logging
.\build_enhanced.ps1 -Debug

# Create installer
.\build_enhanced.ps1 -Installer

# Run performance benchmarks
.\build_enhanced.ps1 -Benchmark
```

### Manual Build (Makefile)

```bash
# Build release version
make release

# Build debug version
make debug

# Run tests
make test

# Create installer
make installer
```

## Usage Guide

### Wallet Creation
1. Launch LackyVault
2. Select "Create New Wallet"
3. Choose wallet type (Bitcoin, Ethereum, Monero)
4. Set strong password (minimum 12 characters recommended)
5. Backup mnemonic phrase securely (offline)
6. Verify recovery phrase

### Transaction Signing
1. Navigate to "Send" screen
2. Enter recipient address
3. Specify amount and fee
4. Review transaction details
5. Confirm with password
6. Transaction will be signed and broadcast

### Security Features
- **Auto-Lock**: Wallet locks after configurable idle period
- **Panic Mode**: Press Ctrl+Alt+D to instantly clear all sensitive data
- **Decoy Wallets**: Configure alternate passwords for decoy wallets
- **Proxy Routing**: Configure multi-hop proxy chain for anonymity

## Performance

| Operation | Speed (ops/sec) | Memory Usage |
|-----------|-----------------|--------------|
| Startup | < 500ms | < 50MB |
| SHA-256 (1KB) | 110,000 | < 1KB |
| AES-256-GCM (1KB) | 75,000 | < 1KB |
| Ed25519 Sign | 12,000 | < 1KB |
| BTC Transaction | 5,000 | < 10KB |
| ETH Transaction | 4,500 | < 10KB |

## Project Structure

```
LackyVault/
├── build/                  # Build artifacts
├── config/                 # Configuration files
├── docs/                   # Documentation
│   ├── IMPLEMENTATION_STATUS.md
│   └── README.md
├── include/                # Header files
├── installer/              # MSI installer files
├── scripts/                # Utility scripts
├── src/
│   ├── asm/                # Assembly implementations
│   │   ├── crypto_optimized.asm
│   │   └── security.asm
│   ├── c/                  # C source code
│   │   ├── blockchain/     # Blockchain implementations
│   │   ├── core/           # Core application logic
│   │   ├── crypto/         # Cryptographic primitives
│   │   ├── hardware/       # Hardware integration
│   │   ├── network/        # Network communication
│   │   ├── storage/        # Secure storage
│   │   └── ui/             # User interface
│   └── resources/          # Application resources
│       ├── icons/          # Application icons
│       └── themes/         # Theme resources
├── tests/                  # Test suite
├── themes/                 # Theme configuration
├── build.bat               # Basic build script
├── build_enhanced.ps1      # Advanced build script
└── Makefile                # GNU Make build system
```

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Contribution Guidelines
- Follow existing code style and conventions
- Add comprehensive tests for new features
- Maintain the zero-dependency philosophy
- Provide detailed documentation
- Ensure all security features remain intact

## Security Considerations

LackyVault is designed with paranoid security in mind, but please consider these important points:

1. **Secure Environment**: Use only on trusted hardware in secure locations
2. **Offline Storage**: Keep significant funds in cold storage
3. **Regular Updates**: Keep the software up to date with latest security patches
4. **Strong Passwords**: Use high-entropy passwords (20+ characters recommended)
5. **Backup**: Maintain secure backups of wallet files and recovery phrases

## License

This project is licensed under the BSD 3-Clause License - see the LICENSE file for details.

## Acknowledgements

- The crypto community for advancing security practices
- The retro aesthetics of the 1980s cyberpunk culture
- Security researchers who have provided valuable feedback

---

<p align="center">
  <strong>Built with ♥ by Lackadaisical Security</strong><br>
  <em>"Because sometimes the best security is built from scratch"</em>
</p> 