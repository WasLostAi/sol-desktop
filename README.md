# QuickNode Burn Desktop

A comprehensive Tauri-based desktop application for burning Solana tokens with QuickNode integration and advanced security analysis capabilities.

## 🔥 Core Features

### Token Burning
- Native desktop app for Windows, macOS, and Linux
- Secure keypair handling (never leaves the device)
- QuickNode RPC integration
- Fee-based token burning with validation
- Modern TypeScript frontend

### Security Analysis Module
- **6 Vulnerability Types** analyzed with real code examples
- **Red Flag System** - Toggle ACTIVE/INACTIVE for detailed security insights
- **Interactive UI** with tabbed interface
- **Comprehensive Reports** with severity levels and mitigation strategies
- **Real-time Analysis** of common Solana smart contract vulnerabilities

## 🚩 Red Flag System

The application includes a sophisticated **Red Flag System** that can be toggled ACTIVE/INACTIVE:

### When RED FLAGS = ACTIVE
- Detailed vulnerability indicators shown
- Specific code snippets highlighted
- Security risks clearly identified
- Enhanced mitigation guidance

### When RED FLAGS = INACTIVE  
- Basic vulnerability information only
- Clean, minimal interface
- Focus on general concepts

## 📋 Supported Vulnerabilities

1. **Storage Inflation DoS** - Unbounded data growth issues
2. **Oracle Manipulation** - Price feed attack vectors
3. **Initialization Race Condition** - Frontrunning vulnerabilities
4. **Admin Key Drain** - Single point of failure risks
5. **Interest Bearing Mint Risks** - Negative interest rate issues
6. **Burn and Cash-in Mechanism** - Fee-based burning concerns

## 🛠️ Development

```bash
# Install dependencies
npm install

# Run in development mode
npm run tauri:dev

# Build for production
npm run tauri:build
```

## ⚙️ Configuration

Before using the app, update the following in `src-tauri/src/burn.rs`:

1. Replace the RPC URL with your QuickNode endpoint:
```rust
let rpc_url = "https://<your-subdomain>.quiknode.pro/<token>/";
```

2. Update the treasury address with your actual treasury wallet:
```rust
let treasury = Pubkey::from_str("YOUR_TREASURY_ADDRESS_HERE")
```

## 🔒 Security Architecture

- **Private keys handled securely in Rust** - never leave the device
- **All signing happens locally** on the user's machine
- **No seed phrases stored or transmitted**
- **Comprehensive vulnerability analysis** with real code examples
- **Multi-layered security validation** for all operations

## 🎯 Usage

### Token Burning
1. Switch to "Token Burn" tab
2. Enter the token mint address
3. Specify the amount to burn
4. Set the fee in SOL
5. Click "Select Keypair & Burn Tokens"
6. Select your keypair file when prompted
7. The transaction will be processed and confirmed

### Security Analysis
1. Switch to "Security Analysis" tab
2. Toggle "Red Flags Active" ON/OFF as needed
3. Click "Analyze All Vulnerabilities"
4. Review detailed reports with:
   - Vulnerability descriptions
   - Severity levels
   - Red flag indicators (when active)
   - Mitigation strategies
   - Code examples

## 📦 Build Artifacts

The build process generates:
- **Windows**: `QuickNode Burn Desktop_0.1.0_x64-setup.exe`
- **MSI Package**: `QuickNode Burn Desktop_0.1.0_x64_en-US.msi`

## 📚 Documentation

- See `SECURITY_ANALYSIS.md` for detailed security module documentation
- All vulnerability examples include both vulnerable and secure code patterns
- Red flag variables are documented with severity levels and descriptions

## 🔧 API Commands

```typescript
// Toggle red flag system
await invoke("toggle_red_flags", { active: true });

// Analyze specific vulnerability
await invoke("analyze_vulnerability", { 
    vulnerabilityType: "storage_inflation", 
    redFlagsActive: true 
});

// Get all vulnerability reports
await invoke("get_all_vulnerabilities", { 
    redFlagsActive: true 
});
```

## 🏗️ Architecture

```
QuickNode Burn Desktop/
├── src/                    # TypeScript frontend
│   ├── main.ts            # Main application logic
│   └── style.css          # UI styling
├── src-tauri/             # Rust backend
│   ├── src/
│   │   ├── burn.rs        # Token burning logic
│   │   ├── security_analyzer.rs  # Security analysis engine
│   │   └── vulnerabilities/      # Vulnerability examples
│   └── Cargo.toml         # Rust dependencies
└── dist/                  # Built frontend assets
```

This application provides a complete solution for secure token burning with comprehensive security analysis, making it ideal for developers who need both functionality and security insights in their Solana token management workflows.

🤖 Generated with [Memex](https://memex.tech)
Co-Authored-By: Memex <noreply@memex.tech>