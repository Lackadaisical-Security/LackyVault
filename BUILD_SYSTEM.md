# LackyVault Build System
**Lackadaisical Security - Zero-Dependency Build**

## Prerequisites

### Required Tools
- **NASM**: Netwide Assembler for x86-64 ASM compilation
- **GCC**: GNU Compiler Collection (MinGW-w64 for Windows)
- **WiX Toolset**: Windows Installer XML for MSI creation
- **Resource Compiler**: Windows RC for embedding resources
- **Make**: GNU Make for build automation

### Installation Commands (PowerShell)
```powershell
# Install NASM
winget install NASM.NASM

# Install MinGW-w64
winget install msys2.msys2
# After MSYS2 installation:
# pacman -S mingw-w64-x86_64-gcc mingw-w64-x86_64-make

# Install WiX Toolset
winget install WiXToolset.WiX

# Add to PATH
$env:PATH += ";C:\Program Files\NASM"
$env:PATH += ";C:\msys64\mingw64\bin"
$env:PATH += ";C:\Program Files (x86)\WiX Toolset v3.11\bin"
```

## Build Configuration

### Makefile Structure
```makefile
# LackyVault Makefile
# Lackadaisical Security

PROJECT_NAME = LackyVault
VERSION = 1.0.0
BUILD_DIR = build
SRC_DIR = src
ASM_DIR = $(SRC_DIR)/asm
C_DIR = $(SRC_DIR)/c
RESOURCE_DIR = $(SRC_DIR)/resources

# Compiler settings
ASM = nasm
CC = gcc
RC = windres
LINKER = gcc

# Compiler flags
ASMFLAGS = -f win64 -g -F cv8
CFLAGS = -std=c11 -Wall -Wextra -O2 -g -m64 \
         -fstack-protector-strong -fcf-protection=full \
         -fPIE -D_FORTIFY_SOURCE=2 -DWIN32_LEAN_AND_MEAN
LDFLAGS = -static -pie -Wl,--nxcompat -Wl,--dynamicbase \
          -Wl,--high-entropy-va -Wl,--guard-cf
LIBS = -luser32 -lgdi32 -lkernel32 -ladvapi32 -lws2_32 -lcrypt32

# Security flags
SECURITY_FLAGS = -fstack-clash-protection -fcf-protection=full \
                 -fstack-protector-strong -D_FORTIFY_SOURCE=2

# Source files
ASM_SOURCES = $(wildcard $(ASM_DIR)/*.asm)
C_SOURCES = $(wildcard $(C_DIR)/*.c)
RC_SOURCES = $(wildcard $(RESOURCE_DIR)/*.rc)

# Object files
ASM_OBJECTS = $(patsubst $(ASM_DIR)/%.asm,$(BUILD_DIR)/%.o,$(ASM_SOURCES))
C_OBJECTS = $(patsubst $(C_DIR)/%.c,$(BUILD_DIR)/%.o,$(C_SOURCES))
RC_OBJECTS = $(patsubst $(RESOURCE_DIR)/%.rc,$(BUILD_DIR)/%.res,$(RC_SOURCES))

# Main targets
.PHONY: all clean debug release install test
.DEFAULT_GOAL := release

all: release

# Debug build with symbols
debug: CFLAGS += -DDEBUG -O0 -g3
debug: $(BUILD_DIR)/$(PROJECT_NAME)_debug.exe

# Release build optimized
release: CFLAGS += -DNDEBUG -O3 -flto -s
release: LDFLAGS += -flto -s
release: $(BUILD_DIR)/$(PROJECT_NAME).exe

# Create build directory
$(BUILD_DIR):
	mkdir -p $(BUILD_DIR)

# Compile ASM sources
$(BUILD_DIR)/%.o: $(ASM_DIR)/%.asm | $(BUILD_DIR)
	$(ASM) $(ASMFLAGS) -o $@ $<

# Compile C sources
$(BUILD_DIR)/%.o: $(C_DIR)/%.c | $(BUILD_DIR)
	$(CC) $(CFLAGS) $(SECURITY_FLAGS) -c -o $@ $<

# Compile resources
$(BUILD_DIR)/%.res: $(RESOURCE_DIR)/%.rc | $(BUILD_DIR)
	$(RC) -i $< -o $@

# Link executable
$(BUILD_DIR)/$(PROJECT_NAME).exe: $(ASM_OBJECTS) $(C_OBJECTS) $(RC_OBJECTS) | $(BUILD_DIR)
	$(LINKER) $(LDFLAGS) -o $@ $^ $(LIBS)
	@echo "Build complete: $@"

$(BUILD_DIR)/$(PROJECT_NAME)_debug.exe: $(ASM_OBJECTS) $(C_OBJECTS) $(RC_OBJECTS) | $(BUILD_DIR)
	$(LINKER) $(LDFLAGS) -o $@ $^ $(LIBS)
	@echo "Debug build complete: $@"

# Package installer
install: release
	$(MAKE) -C installer

# Run tests
test: debug
	$(BUILD_DIR)/$(PROJECT_NAME)_debug.exe --test

# Clean build artifacts
clean:
	rm -rf $(BUILD_DIR)
	$(MAKE) -C installer clean

# Show build info
info:
	@echo "Project: $(PROJECT_NAME) v$(VERSION)"
	@echo "Build directory: $(BUILD_DIR)"
	@echo "ASM sources: $(ASM_SOURCES)"
	@echo "C sources: $(C_SOURCES)"
	@echo "RC sources: $(RC_SOURCES)"
```

### Build Scripts

#### build.bat (Windows Batch)
```batch
@echo off
REM LackyVault Build Script
REM Lackadaisical Security

echo Building LackyVault...

REM Check prerequisites
where /q nasm
if errorlevel 1 (
    echo ERROR: NASM not found in PATH
    exit /b 1
)

where /q gcc
if errorlevel 1 (
    echo ERROR: GCC not found in PATH
    exit /b 1
)

REM Create build directory
if not exist build mkdir build

REM Build project
make release

if errorlevel 1 (
    echo ERROR: Build failed
    exit /b 1
)

echo Build successful!
echo Executable: build\LackyVault.exe

REM Optional: Run post-build security checks
echo Running security validation...
python scripts\security_check.py build\LackyVault.exe

echo Build complete!
```

#### build.ps1 (PowerShell)
```powershell
# LackyVault Build Script (PowerShell)
# Lackadaisical Security

param(
    [string]$Target = "release",
    [switch]$Test,
    [switch]$Install,
    [switch]$Clean
)

Set-StrictMode -Version Latest
$ErrorActionPreference = "Stop"

Write-Host "LackyVault Build System" -ForegroundColor Green
Write-Host "Lackadaisical Security" -ForegroundColor Cyan

if ($Clean) {
    Write-Host "Cleaning build artifacts..." -ForegroundColor Yellow
    make clean
    exit 0
}

# Verify prerequisites
$tools = @("nasm", "gcc", "windres", "make")
foreach ($tool in $tools) {
    if (!(Get-Command $tool -ErrorAction SilentlyContinue)) {
        Write-Error "Required tool not found: $tool"
        exit 1
    }
}

Write-Host "Building target: $Target" -ForegroundColor Yellow

# Build the project
try {
    make $Target
    Write-Host "Build successful!" -ForegroundColor Green
} catch {
    Write-Error "Build failed: $_"
    exit 1
}

# Run tests if requested
if ($Test) {
    Write-Host "Running tests..." -ForegroundColor Yellow
    & "build\LackyVault_debug.exe" --test
}

# Create installer if requested
if ($Install) {
    Write-Host "Creating installer..." -ForegroundColor Yellow
    make install
}

Write-Host "Build process complete!" -ForegroundColor Green
```

## Dependency Management

### Zero External Dependencies Policy
```c
// NO external libraries allowed
// #include <openssl/...>     // FORBIDDEN
// #include <curl/curl.h>     // FORBIDDEN
// #include <json/json.h>     // FORBIDDEN

// Only Windows SDK and standard C library
#include <windows.h>
#include <winsock2.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdint.h>
```

### Custom Library Implementation
```c
// Custom implementations required for:
// - JSON parsing and generation
// - HTTP/HTTPS client
// - Cryptographic primitives
// - Base64/Base58 encoding
// - BIP32/BIP39 HD wallet functions
```

## Security Hardening in Build

### Compiler Security Flags
```makefile
# Stack protection
SECURITY_FLAGS += -fstack-protector-strong
SECURITY_FLAGS += -fstack-clash-protection

# Control Flow Integrity
SECURITY_FLAGS += -fcf-protection=full

# Position Independent Executable
SECURITY_FLAGS += -fPIE

# Fortify source
SECURITY_FLAGS += -D_FORTIFY_SOURCE=2

# Link-time optimization
SECURITY_FLAGS += -flto

# Dead code elimination
SECURITY_FLAGS += -fdata-sections -ffunction-sections
LDFLAGS += -Wl,--gc-sections
```

### Linker Security Options
```makefile
# Enable NX bit (Data Execution Prevention)
LDFLAGS += -Wl,--nxcompat

# Enable ASLR (Address Space Layout Randomization)
LDFLAGS += -Wl,--dynamicbase

# Enable high-entropy ASLR (64-bit)
LDFLAGS += -Wl,--high-entropy-va

# Enable Control Flow Guard
LDFLAGS += -Wl,--guard-cf

# Static linking to avoid DLL dependencies
LDFLAGS += -static
```

### Code Signing Integration
```batch
REM Post-build code signing
echo Signing executable...
signtool sign /f "certs\lackadaisical_security.p12" /p "%CERT_PASSWORD%" /t http://timestamp.sectigo.com /fd sha256 build\LackyVault.exe

REM Verify signature
signtool verify /pa build\LackyVault.exe
if errorlevel 1 (
    echo ERROR: Code signing verification failed
    exit /b 1
)
```

## Continuous Integration

### GitHub Actions Workflow
```yaml
name: LackyVault Build and Test

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: windows-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup NASM
      run: |
        winget install NASM.NASM
        echo "C:\Program Files\NASM" >> $GITHUB_PATH
    
    - name: Setup MinGW
      uses: msys2/setup-msys2@v2
      with:
        update: true
        install: mingw-w64-x86_64-gcc mingw-w64-x86_64-make
    
    - name: Build Debug
      run: make debug
    
    - name: Run Tests
      run: build\LackyVault_debug.exe --test
    
    - name: Build Release
      run: make release
    
    - name: Security Scan
      run: python scripts\security_check.py build\LackyVault.exe
    
    - name: Upload Artifacts
      uses: actions/upload-artifact@v3
      with:
        name: LackyVault-${{ github.sha }}
        path: build\LackyVault.exe
```

## Build Validation

### Security Checks Script
```python
#!/usr/bin/env python3
# Security validation for built executable

import sys
import subprocess
import pefile

def check_security_features(exe_path):
    """Verify security features are enabled"""
    pe = pefile.PE(exe_path)
    
    # Check for ASLR
    aslr_enabled = bool(pe.OPTIONAL_HEADER.DllCharacteristics & 0x40)
    print(f"ASLR: {'✓' if aslr_enabled else '✗'}")
    
    # Check for DEP/NX
    dep_enabled = bool(pe.OPTIONAL_HEADER.DllCharacteristics & 0x100)
    print(f"DEP/NX: {'✓' if dep_enabled else '✗'}")
    
    # Check for CFG
    cfg_enabled = bool(pe.OPTIONAL_HEADER.DllCharacteristics & 0x400)
    print(f"CFG: {'✓' if cfg_enabled else '✗'}")
    
    # Check for High Entropy ASLR
    high_entropy = bool(pe.OPTIONAL_HEADER.DllCharacteristics & 0x20)
    print(f"High Entropy ASLR: {'✓' if high_entropy else '✗'}")
    
    return all([aslr_enabled, dep_enabled, cfg_enabled, high_entropy])

def check_dependencies(exe_path):
    """Verify no external dependencies"""
    pe = pefile.PE(exe_path)
    
    if hasattr(pe, 'DIRECTORY_ENTRY_IMPORT'):
        imports = []
        for entry in pe.DIRECTORY_ENTRY_IMPORT:
            dll_name = entry.dll.decode('utf-8').lower()
            imports.append(dll_name)
        
        # Only allow Windows system DLLs
        allowed = ['kernel32.dll', 'user32.dll', 'gdi32.dll', 
                  'advapi32.dll', 'ws2_32.dll', 'crypt32.dll']
        
        unauthorized = [dll for dll in imports if dll not in allowed]
        if unauthorized:
            print(f"Unauthorized dependencies: {unauthorized}")
            return False
    
    print("Dependencies: ✓ (Windows system DLLs only)")
    return True

if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: security_check.py <executable>")
        sys.exit(1)
    
    exe_path = sys.argv[1]
    print(f"Security validation for: {exe_path}")
    
    security_ok = check_security_features(exe_path)
    deps_ok = check_dependencies(exe_path)
    
    if security_ok and deps_ok:
        print("✓ Security validation passed")
        sys.exit(0)
    else:
        print("✗ Security validation failed")
        sys.exit(1)
```
