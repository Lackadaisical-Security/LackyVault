# LackyVault Architecture & Design
**Lackadaisical Security - Zero-Trust Crypto Wallet**

## Component Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    LackyVault.exe                           │
├─────────────────────────────────────────────────────────────┤
│  UI Layer (x86-64 ASM)                                     │
│  ├── Win32 Message Loop & Event Handling                   │
│  ├── GDI Drawing Engine (Neon/Cosmic Themes)              │
│  ├── Custom Widget System                                  │
│  └── Panic Mode Overlay                                    │
├─────────────────────────────────────────────────────────────┤
│  Core Logic (C)                                            │
│  ├── State Management & Configuration                      │
│  ├── Wallet Management & Key Derivation                    │
│  ├── Transaction Building & Signing                        │
│  └── Security Module (Anti-Debug, Zeroization)            │
├─────────────────────────────────────────────────────────────┤
│  Crypto Primitives (ASM + C)                               │
│  ├── XChaCha20-Poly1305 (ASM optimized)                   │
│  ├── AES-GCM with AES-NI (ASM)                            │
│  ├── Curve25519/Ed25519 (ASM)                             │
│  ├── secp256k1 ECDSA (ASM)                                │
│  └── Argon2id KDF (C with ASM loops)                      │
├─────────────────────────────────────────────────────────────┤
│  Network Layer (C)                                         │
│  ├── Custom TLS 1.3 Implementation                        │
│  ├── JSON-RPC Client                                      │
│  ├── Proxy Management (SOCKS5/HTTP)                       │
│  └── Onion Routing & Metadata Scrubbing                   │
├─────────────────────────────────────────────────────────────┤
│  Storage Layer (C)                                         │
│  ├── Encrypted VFS (In-Memory & Disk)                     │
│  ├── Wallet File Format                                   │
│  └── Configuration Management                             │
└─────────────────────────────────────────────────────────────┘
```

## State Machine Design

### UI Finite State Machine (ASM)
- **INIT**: Splash screen, anti-debug checks
- **AUTH**: Password entry, KDF computation
- **MAIN**: Main wallet interface
- **TRANSACTION**: Transaction building/signing
- **SETTINGS**: Configuration panel
- **PANIC**: Distress mode activated

### Core State Transitions
```
INIT → AUTH (successful initialization)
AUTH → MAIN (valid credentials)
MAIN ↔ TRANSACTION (user action)
MAIN ↔ SETTINGS (user action)
ANY → PANIC (Ctrl+Alt+D hotkey)
PANIC → INIT (dual-entry reauth)
```

## Thread Architecture

### Main UI Thread (ASM)
- Win32 message pump
- GDI rendering
- User input handling
- Theme switching

### Crypto Worker Thread (C)
- Key derivation (Argon2id)
- Transaction signing
- Envelope encryption/decryption

### Network Thread (C)
- JSON-RPC communication
- Proxy rotation
- TLS handshake management

### Background Security Thread (ASM)
- Anti-debug monitoring
- Memory protection
- Heartbeat checks

## Inter-Process Communication

### Custom Event Queue
```c
typedef struct {
    uint32_t event_type;
    uint32_t data_size;
    uint8_t data[MAX_EVENT_DATA];
    volatile uint32_t processed;
} lacky_event_t;
```

### Event Types
- `EVENT_UI_UPDATE`: UI state change
- `EVENT_CRYPTO_COMPLETE`: Crypto operation finished
- `EVENT_NETWORK_RESPONSE`: RPC response received
- `EVENT_PANIC_TRIGGER`: Emergency shutdown
- `EVENT_KEY_ZEROIZE`: Memory cleanup

## Security Architecture

### Memory Protection
- Stack canaries (compiler-generated)
- DEP (Data Execution Prevention)
- ASLR (Address Space Layout Randomization)
- CFI (Control Flow Integrity)

### Anti-Analysis Measures
```asm
; Anti-debug detection
check_debugger:
    call    IsDebuggerPresent
    test    eax, eax
    jnz     panic_exit
    
    ; PEB check
    mov     rax, gs:[60h]          ; PEB
    movzx   eax, byte ptr [rax+2]  ; BeingDebugged flag
    test    al, al
    jnz     panic_exit
    ret
```

### Code Obfuscation
- Control flow flattening
- Opaque predicates
- Dead code insertion
- String encryption

## Threat Model

### Attack Vectors

#### 1. Code Injection
**Threat**: Buffer overflow, ROP/JOP attacks
**Mitigation**: 
- Stack canaries in all functions
- CFI enforcement
- Input validation with ASM bounds checking

#### 2. RPC Man-in-the-Middle
**Threat**: Network interception, certificate pinning bypass
**Mitigation**:
- Custom TLS implementation
- Certificate transparency validation
- Onion routing with metadata scrubbing

#### 3. Reverse Engineering
**Threat**: Static/dynamic analysis, key extraction
**Mitigation**:
- Anti-debug hooks
- Code obfuscation
- VM detection
- Key material zeroization

#### 4. Side-Channel Attacks
**Threat**: Timing analysis, power analysis
**Mitigation**:
- Constant-time crypto primitives
- Uniform memory access patterns
- Blinding for scalar multiplication

#### 5. Physical Access
**Threat**: Memory dumps, cold boot attacks
**Mitigation**:
- Panic mode with instant zeroization
- Memory encryption
- Decoy wallets

### Trust Boundaries

1. **UI ↔ Core**: Validated event passing
2. **Core ↔ Crypto**: Secure parameter sanitization
3. **Network ↔ Core**: Input validation and sanitization
4. **Storage ↔ Core**: Authenticated encryption

## Performance Targets

- **UI Responsiveness**: < 16ms frame time (60 FPS)
- **Crypto Operations**: 
  - Key derivation: < 2 seconds (Argon2id)
  - Transaction signing: < 100ms
  - Encryption/Decryption: < 10ms per MB
- **Network Latency**: < 500ms additional overhead
- **Memory Usage**: < 50MB total footprint

## Compliance & Standards

- **FIPS 140-2 Level 2**: Crypto module compliance
- **Common Criteria EAL4+**: Security evaluation
- **NIST SP 800-57**: Key management practices
- **RFC 8446**: TLS 1.3 compliance
- **BIP32/39/44**: HD wallet standards
