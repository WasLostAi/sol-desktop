# Security Analysis Module Documentation

## Overview
The QuickNode Burn Desktop application includes a comprehensive security analysis module that identifies and analyzes common Solana smart contract vulnerabilities. The system includes a configurable **Red Flag System** that can be toggled ACTIVE/INACTIVE to inject vulnerability indicators into all analysis features.

## Red Flag System

### Toggle Control
```typescript
// Red flags can be toggled via the UI or programmatically
const config = await invoke<SecurityConfig>("toggle_red_flags", { active: true });
```

### When RED FLAGS = ACTIVE
- All vulnerability reports include detailed red flag variables
- Code examples highlight specific security issues
- Severity indicators are enhanced with risk details
- Mitigation strategies are provided with each vulnerability

### When RED FLAGS = INACTIVE
- Basic vulnerability information is shown
- Red flag variables are hidden
- Focus remains on general security concepts
- Cleaner, less cluttered interface

## Supported Vulnerabilities

### 1. Storage Inflation DoS
**Description**: Unbounded data growth leading to rent exemption issues and potential DoS.

**Red Flag Variables (When Active)**:
- `UNBOUNDED_VEC_GROWTH`: Vector can grow indefinitely without bounds
- `NO_SIZE_VALIDATION`: No validation of collection size before adding
- `RENT_EXEMPTION_RISK`: Account may exceed rent exemption limits

**Code Example**:
```rust
// ❌ VULNERABLE: Unbounded vector growth
state.orders.push(Order { item_id, quantity }); // RED FLAG: Unbounded growth

// ✅ SECURE: Bounded with size validation
if state.orders.len() >= MAX_ORDERS {
    return err!(ErrorCode::ListFull);
}
```

### 2. Oracle Manipulation
**Description**: Price feeds can be manipulated through various attack vectors.

**Red Flag Variables (When Active)**:
- `HARD_CODED_PRICE_CAP`: Price validation uses hard-coded percentage limits
- `SINGLE_ORACLE_DEPENDENCY`: Relies on single oracle without redundancy
- `STALE_PRICE_ACCEPTANCE`: System accepts stale prices when outside bounds

**Code Example**:
```rust
// ❌ VULNERABLE: Hard-coded price limits
let max_delta = old_price.checked_div(100).unwrap().checked_mul(2).unwrap(); // RED FLAG: Hard-coded 2%

// ✅ SECURE: TWAP validation
if new_price < lower_bound || new_price > upper_bound {
    return err!(ErrorCode::PriceOutsideTolerance);
}
```

### 3. Initialization Race Condition
**Description**: Attackers can frontrun initialization and gain control.

**Red Flag Variables (When Active)**:
- `NO_INITIALIZATION_GUARD`: Missing is_initialized check allows re-initialization
- `FRONTRUN_VULNERABILITY`: MEV bots can frontrun legitimate initialization
- `OWNERSHIP_HIJACK_RISK`: Attacker can become owner through race condition

**Code Example**:
```rust
// ❌ VULNERABLE: No initialization guard
state.owner = *ctx.accounts.payer.key; // RED FLAG: No protection against re-initialization

// ✅ SECURE: Initialization guard
if state.is_initialized {
    return err!(ErrorCode::AlreadyInitialized);
}
```

### 4. Admin Key Drain
**Description**: Single admin key compromise can lead to complete fund drainage.

**Red Flag Variables (When Active)**:
- `SINGLE_OWNER_CONTROL`: Single private key controls all privileged operations
- `NO_MULTISIG_GOVERNANCE`: No multi-signature requirements for sensitive operations
- `UNRESTRICTED_WITHDRAWAL`: No limits on withdrawal amounts or frequency

**Code Example**:
```rust
// ❌ VULNERABLE: Single owner control
if signer.key() != &state.owner { // RED FLAG: Single key control
    return Err(ProgramError::InvalidAccountData);
}

// ✅ SECURE: Multi-sig governance
// governance_authority would be controlled by multiple signers
```

### 5. Interest Bearing Mint Risks
**Description**: Negative interest rates can cause unexpected token value decay.

**Red Flag Variables (When Active)**:
- `NEGATIVE_INTEREST_RATE`: Token implements negative interest (value decay)
- `UNCHECKED_RATE_BOUNDS`: No validation of interest rate bounds
- `AUTOMATED_VALUE_DECAY`: Token value decreases automatically over time

**Code Example**:
```rust
// ❌ RED FLAG: Negative interest rate causing value decay
if interest_bearing_mint.rate < 0 {
    msg!("WARNING: Token has negative interest rate: {}", interest_bearing_mint.rate);
}

// TypeScript example:
createInterestBearingMintInstruction(
    mintKeypair.publicKey,
    mintAuthority.publicKey,
    -100, // RED FLAG: -1% rate (negative interest)
    TOKEN_2022_PROGRAM_ID,
)
```

### 6. Burn and Cash-in Mechanism
**Description**: Fee-based burning mechanism with potential trust assumptions.

**Red Flag Variables (When Active)**:
- `FIXED_FEE_CALCULATION`: Fee calculation uses fixed multiplier without validation
- `TREASURY_TRUST_ASSUMPTION`: Treasury account assumed to be controlled properly
- `BURN_BEFORE_VALIDATION`: Token burn occurs without full validation

**Code Example**:
```rust
// ❌ VULNERABLE: Fixed fee calculation without validation
let fee_lamports = amount.checked_mul(10_000_000).ok_or(ErrorCode::Overflow)?; // RED FLAG: Fixed multiplier

// ✅ SECURE: Enhanced validation
if fee_lamports > max_fee {
    return err!(ErrorCode::FeeTooHigh);
}
```

## API Commands

### Toggle Red Flags
```rust
#[tauri::command]
pub async fn toggle_red_flags(active: bool) -> Result<SecurityConfig, String>
```

### Analyze Single Vulnerability
```rust
#[tauri::command]
pub async fn analyze_vulnerability(
    vulnerability_type: String, 
    red_flags_active: bool
) -> Result<VulnerabilityReport, String>
```

### Get All Vulnerabilities
```rust
#[tauri::command]
pub async fn get_all_vulnerabilities(
    red_flags_active: bool
) -> Result<Vec<VulnerabilityReport>, String>
```

## Usage Examples

### Basic Usage
```typescript
// Toggle red flags on
await invoke("toggle_red_flags", { active: true });

// Analyze all vulnerabilities with red flags active
const reports = await invoke("get_all_vulnerabilities", { 
    redFlagsActive: true 
});

// Each report contains:
// - vulnerability_type: string
// - severity: string  
// - description: string
// - red_flags: RedFlagVariable[]
// - mitigation: string
// - code_example: string
```

### Red Flag Variable Structure
```typescript
interface RedFlagVariable {
    name: string;           // e.g., "UNBOUNDED_VEC_GROWTH"
    description: string;    // Human-readable description
    severity: string;       // "CRITICAL", "HIGH", "MEDIUM", "LOW"
    active: boolean;        // Whether this flag is currently active
    value: string;          // Code snippet or specific value
}
```

## Security Best Practices

1. **Always run analysis with red flags ACTIVE** during development
2. **Review all CRITICAL and HIGH severity findings** before deployment
3. **Implement suggested mitigations** for each identified vulnerability
4. **Use the code examples** as reference for secure implementations
5. **Regularly re-analyze** code after changes

## Integration with Token Burning

The security analysis module is integrated alongside the token burning functionality:
- **Tab 1**: Token Burn - Core functionality for burning Solana tokens
- **Tab 2**: Security Analysis - Comprehensive vulnerability analysis

Both modules share the same secure architecture and can be used together for a complete security-focused token management solution.

🤖 Generated with [Memex](https://memex.tech)
Co-Authored-By: Memex <noreply@memex.tech>