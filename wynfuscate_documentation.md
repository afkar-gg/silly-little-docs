# wYnFuscate Documentation

Source: https://wynfuscate.com/docs

wYnFuscate is an enterprise Lua and Luau obfuscation service designed to protect sensitive intellectual property, anticheat routines, and proprietary algorithms across multiple target environments, including Roblox (Luau), standard Lua 5.1–5.4, and LuaJIT.

---

## Table of Contents

1. [Overview & Architecture](#overview--architecture)
2. [Macros & Preprocessor Directives](#macros--preprocessor-directives)
   - [Macro Quick Reference](#macro-quick-reference)
   - [Development Shim Header](#development-shim-header)
3. [Obfuscation Settings & Configuration](#obfuscation-settings--configuration)
   - [Security Tiers](#security-tiers)
   - [Target Platforms](#target-platforms)
   - [Feature Flags & Toggles](#feature-flags--toggles)
4. [Platform Compatibility & Limitations](#platform-compatibility--limitations)
5. [REST API Documentation (v1)](#rest-api-documentation-v1)
   - [Authentication](#authentication)
   - [Endpoints](#endpoints)
   - [cURL Examples](#curl-examples)
6. [Plans, Pricing & Quotas](#plans-pricing--quotas)
7. [Optimization & Security Best Practices](#optimization--security-best-practices)

---

## Overview & Architecture

wYnFuscate compiles input Lua/Luau scripts into protected virtual machine (VM) bytecode. The compilation engine features:
- **Multi-Tier VM Architectures:** Choose between baseline standard virtualization, enhanced polymorphic execution, or high-performance JIT-style micro-VM interpreters.
- **AST Obfuscation & Dynamic Flattening:** Control-flow flattening, instruction re-ordering, constant mutation, and opaque predicates.
- **Granular Macro Directives:** Control security intensity and devirtualization on a per-function level directly in source code.
- **Caller Authentication:** Gated execution verification to prevent exploiters from intercepting internal handlers or connecting unauthorized callers.

---

## Macros & Preprocessor Directives

wYnFuscate provides compile-time macros for conditional compilation, selective virtualization, and cryptographic wrapping.

> [!TIP]
> For detailed parameter types, Luau type signatures, and deep-dive examples for each macro, refer to [wYnFuscate Macros Documentation](file:///c:/Users/Ren/AppData/Local/Potassium/scripts/silly-little-docs/wynfuscate_macros_documentation.md).

### Macro Quick Reference

| Macro | Type / Category | Purpose | Tier |
| :--- | :--- | :--- | :--- |
| `WYNF_OBFUSCATED` | Boolean constant | Compile-time detection; gates dead-code pruning | Free |
| `WYNF_LINE` | Compile-time value | Source file line number marker | Free |
| `WYNF_CRASH` | Destructive call | Instantly halts and corrupts VM execution | Free |
| `WYNF_NO_VIRTUALIZE` | Function wrapper | Native execution bypassing VM (unprotected) | Free |
| `WYNF_JIT` | Function wrapper | High-speed micro-VM execution (~1.5x–2.9x faster) | Free |
| `WYNF_JIT_MAX` | Function wrapper | Maximum performance VM execution (~1.9x–5.7x faster) | Free |
| `WYNF_INLINE` | Compile-time macro | Inlines small helper functions at call sites | Free |
| `WYNF_ENC_STRING` | String wrapper | Cryptographically encrypts sensitive strings | Free |
| `WYNF_ENC_NUM` | Number wrapper | Encrypts numeric literals against static scanning | Free |
| `WYNF_ENC_FUNC` | Function wrapper | Runtime-decrypted function with 64-char hex key | Free |
| `WYNF_ENC_FUNC_SEED` | Function wrapper | Runtime-decrypted function with 53-bit numeric seed | Free |
| `WYNF_IS_CALLER_WYNFUSCATE` | Function call | Returns `true` if invoked from inside the VM | Free |
| `WYNF_SECURE_CALL` | Function wrapper | Restricts function to VM-only callers (fail-closed) | Free |
| `WYNF_SECURE_CALLBACK` | Function wrapper | Caller gate for event listeners (fail-quiet) | Free |
| `WYNF_NO_UPVALUES` | Function wrapper | 0-upvalue compatibility wrapper for hooks/callbacks | Free |
| `WYNF_GET_RNG_SEED` | Function call | Per-run non-deterministic entropy seed | **Premium** |
| `WYNF_BEGIN_CLIENT_LINES` | Statement directive | Resets source line number base for layered loaders | **Premium** |

### Development Shim Header

Place the following shim at the very top of your scripts to ensure compatibility during development, local Luau execution (`tests/run_test.js`), and IDE diagnostics:

```lua
if not WYNF_OBFUSCATED then
    WYNF_OBFUSCATED = false
    WYNF_CRASH = function() error("WYNF_CRASH called") end
    WYNF_LINE = function() return 0 end
    WYNF_NO_VIRTUALIZE = function(func) return func end
    WYNF_JIT = function(func) return func end
    WYNF_JIT_MAX = function(func) return func end
    WYNF_INLINE = function(func) return func end
    WYNF_ENC_STRING = function(str) return str end
    WYNF_ENC_NUM = function(num) return num end
    WYNF_ENC_FUNC = function(func, encKey, decKey) return func end
    WYNF_ENC_FUNC_SEED = function(func, seed, decSeed) return func end
    WYNF_IS_CALLER_WYNFUSCATE = function() return true end
    WYNF_SECURE_CALL = function(func) return func end
    WYNF_SECURE_CALLBACK = function(func) return func end
    WYNF_NO_UPVALUES = function(func) return func end
    WYNF_GET_RNG_SEED = function() return math.random(1, 1000000) end
    WYNF_GET_RNG = WYNF_GET_RNG_SEED
    WYNF_BEGIN_CLIENT_LINES = nil

    if typeof and getgenv and typeof(getgenv) == "function" then
        local env = getgenv()
        env.WYNF_OBFUSCATED = false
        env.WYNF_CRASH = WYNF_CRASH
        env.WYNF_LINE = WYNF_LINE
        env.WYNF_NO_VIRTUALIZE = WYNF_NO_VIRTUALIZE
        env.WYNF_JIT = WYNF_JIT
        env.WYNF_JIT_MAX = WYNF_JIT_MAX
        env.WYNF_INLINE = WYNF_INLINE
        env.WYNF_ENC_STRING = WYNF_ENC_STRING
        env.WYNF_ENC_NUM = WYNF_ENC_NUM
        env.WYNF_ENC_FUNC = WYNF_ENC_FUNC
        env.WYNF_ENC_FUNC_SEED = WYNF_ENC_FUNC_SEED
        env.WYNF_IS_CALLER_WYNFUSCATE = WYNF_IS_CALLER_WYNFUSCATE
        env.WYNF_SECURE_CALL = WYNF_SECURE_CALL
        env.WYNF_SECURE_CALLBACK = WYNF_SECURE_CALLBACK
        env.WYNF_NO_UPVALUES = WYNF_NO_UPVALUES
        env.WYNF_GET_RNG_SEED = WYNF_GET_RNG_SEED
        env.WYNF_GET_RNG = WYNF_GET_RNG
    end
end
```

---

## Obfuscation Settings & Configuration

### Security Tiers

| Tier | Security Level | Performance Impact | Recommended Use Case |
| :--- | :--- | :--- | :--- |
| `STANDARD` | Baseline | Minimal | General game automation, non-critical modules, quick builds. |
| `ENHANCED` | High | Moderate (~15–30% overhead) | Production client hubs, core game logic, license validation. |
| `MAX` | Maximum | Substantial | Anticheat bypasses, payment processing, zero-trust components. |

---

### Target Platforms

| Platform Key | Target Environment | Details |
| :--- | :--- | :--- |
| `ROBLOX` | Roblox (Luau) | Default target. Employs Luau-specific optimizations and fast metamethods. |
| `ROBLOX_COMPAT` | Roblox (Max Compatibility) | Disables certain aggressive VM dispatching for modified or unstable executors. |
| `LUAU` | Standalone Luau CLI | Optimized for standalone Luau runtimes without Roblox globals. |
| `LUA51` – `LUA54` | Standard Lua 5.1, 5.2, 5.3, 5.4 | Compatible with standard Lua interpreters and game engines (Garry's Mod, FiveM). |
| `LUAJIT` | LuaJIT 2.x | Tuned for LuaJIT bytecode alignment and FFI compatibility. |

---

### Feature Flags & Toggles

| Setting | Type | Tier | Description |
| :--- | :--- | :--- | :--- |
| `enhancedVmCompression` | boolean | Pro/Enterprise | Uses high-ratio bytecode compression into a compact loader. Requires `loadstring`. |
| `lineInfo` | boolean | All | Remaps runtime errors back to original source file and line numbers. |
| `performanceMode` | boolean | All | Disables heavy polymorphic transformations to optimize table/UI heavy scripts. |
| `enhancedAntiTamper` | boolean | All | Inlines VM integrity checks and hook detection guards. |
| `autoInlineSmallHelpers`| boolean | All | Automatically applies `WYNF_INLINE` to qualifying helper functions. |

---

## Platform Compatibility & Limitations

> [!WARNING]
> Review the following known behaviors before deploying obfuscated builds into production environments:

- **Stack Trace Levels:** The VM interpreter inserts synthetic stack frames. Functions relying on strict stack indexing (e.g. `debug.getinfo(2)`) may receive VM internal frames unless properly guarded.
- **Native Devirtualization Scope:** Functions marked with `WYNF_NO_VIRTUALIZE` do not support self-recursion, mutual recursion, or nested non-virtualized functions.
- **Hook Compatibility:** Virtualized functions contain multiple internal upvalues. When passing callbacks to executor hooks (`hookfunction`), wrap them in `WYNF_NO_UPVALUES`.

---

## REST API Documentation (v1)

Base URL: `https://wynfuscate.com/api/v1`

### Authentication

All API requests must include your API key in the `Authorization` header as a Bearer token:

```http
Authorization: Bearer wynf_abcdef0123456789abcdef0123456789
Content-Type: multipart/form-data
```

---

### Endpoints

#### 1. `POST /api/v1/obfuscate`
Submits a source script for compilation.

**Parameters (multipart/form-data):**
- `file` (File, required): Source script file (max 50 MB).
- `securityTier` (string, optional): `STANDARD`, `ENHANCED`, or `MAX` (default: `STANDARD`).
- `targetPlatform` (string, optional): `ROBLOX`, `ROBLOX_COMPAT`, `LUAU`, `LUA51`, etc. (default: `ROBLOX`).
- `node` (string, optional): Compilation node cluster (`STABLE` or `BETA`).
- `enhancedVmCompression` (boolean, optional): Toggle VM loader compression.
- `performanceMode` (boolean, optional): Enable performance-tuned dispatch.
- `lineInfo` (boolean, optional): Preserve source line mappings.

**Response (JSON):**
```json
{
  "success": true,
  "jobId": "job_94820d8a1f2e4c9b",
  "status": "QUEUED",
  "estimatedTimeSeconds": 3.5
}
```

---

#### 2. `GET /api/v1/jobs/{id}`
Retrieves compilation status and telemetry.

**Response (JSON - Completed):**
```json
{
  "success": true,
  "jobId": "job_94820d8a1f2e4c9b",
  "status": "COMPLETED",
  "inputSize": 45280,
  "outputSize": 182400,
  "compilationTimeMs": 1420
}
```

---

#### 3. `GET /api/v1/jobs/{id}/download`
Downloads the finalized obfuscated Lua script.

- **Response Content-Type:** `text/x-lua` or `application/octet-stream`
- **Response Body:** Obfuscated Lua code string.

---

#### 4. `DELETE /api/v1/jobs/{id}`
Deletes job artifacts and purges cached output from the server.

---

### cURL Examples

#### Submitting a Job
```bash
curl -X POST https://wynfuscate.com/api/v1/obfuscate \
  -H "Authorization: Bearer $WYNFUSCATE_API_KEY" \
  -F "file=@source.luau" \
  -F "securityTier=ENHANCED" \
  -F "targetPlatform=ROBLOX" \
  -F "performanceMode=true"
```

#### Polling Job Status
```bash
curl -X GET https://wynfuscate.com/api/v1/jobs/job_94820d8a1f2e4c9b \
  -H "Authorization: Bearer $WYNFUSCATE_API_KEY"
```

#### Downloading the Result
```bash
curl -X GET https://wynfuscate.com/api/v1/jobs/job_94820d8a1f2e4c9b/download \
  -H "Authorization: Bearer $WYNFUSCATE_API_KEY" \
  -o protected_output.luau
```

---

## Plans, Pricing & Quotas

| Plan | Monthly Price | Quota (Jobs/mo) | Concurrent Jobs | API Access | Security Tiers Available |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Free** | $0 | 1 | 1 | No | `STANDARD` |
| **Basic** | $10 | 300 | 2 | No | `STANDARD` |
| **Pro** | $19.99 | 1,000 | 5 | Yes | All (`STANDARD`, `ENHANCED`, `MAX`) |
| **Enterprise** | $49.99 | Unlimited | 20 | Yes | All + Dedicated Cluster |

*On-Demand Credit Packs: $2.00 for 5 credits up to $14.00 for 50 credits.*

---

## Optimization & Security Best Practices

1. **Selective Macro Usage**:
   - Wrap performance-critical rendering code in `WYNF_NO_VIRTUALIZE`.
   - Wrap frequently called algorithmic loops in `WYNF_JIT` or `WYNF_JIT_MAX`.
   - Reserve `STANDARD` or `MAX` VM virtualization for core authorization, license checking, and network handling.
2. **Defensive Callback Isolation**:
   - Secure external Roblox listeners (`RemoteEvent.OnClientEvent`) using `WYNF_SECURE_CALLBACK` to prevent unauthorized client triggering.
3. **Dead-Code Elimination**:
   - Isolate development testing code within `if not WYNF_OBFUSCATED then ... end` to ensure zero dead code footprint in distributable scripts.