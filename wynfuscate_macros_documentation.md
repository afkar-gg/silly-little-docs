# wYnFuscate Macros Documentation

This document provides a comprehensive technical reference for the macro system in **wYnFuscate**. wYnFuscate macros are evaluated during compile time, enabling script developers to configure performance tuning, selective virtualization, caller authentication, cryptographic wrappers, and tamper response directly from source code.

> [!NOTE]
> Macros marked as **PREMIUM** require an active Pro or Enterprise subscription on the wYnFuscate platform.

---

## Table of Contents

1. [Overview](#overview)
2. [Quick Reference Table](#quick-reference-table)
3. [Control Flow & Environment Macros](#control-flow--environment-macros)
   - [`WYNF_OBFUSCATED`](#wynf_obfuscated)
   - [`WYNF_LINE`](#wynf_line)
   - [`WYNF_CRASH`](#wynf_crash)
4. [Performance & Optimization Macros](#performance--optimization-macros)
   - [`WYNF_NO_VIRTUALIZE`](#wynf_no_virtualize)
   - [`WYNF_JIT`](#wynf_jit)
   - [`WYNF_JIT_MAX`](#wynf_jit_max)
   - [`WYNF_INLINE`](#wynf_inline)
5. [Cryptographic Value & Function Macros](#cryptographic-value--function-macros)
   - [`WYNF_ENC_STRING`](#wynf_enc_string)
   - [`WYNF_ENC_NUM`](#wynf_enc_num)
   - [`WYNF_ENC_FUNC`](#wynf_enc_func)
   - [`WYNF_ENC_FUNC_SEED`](#wynf_enc_func_seed)
6. [Caller & Callback Security Macros](#caller--callback-security-macros)
   - [`WYNF_IS_CALLER_WYNFUSCATE`](#wynf_is_caller_wynfuscate)
   - [`WYNF_SECURE_CALL`](#wynf_secure_call)
   - [`WYNF_SECURE_CALLBACK`](#wynf_secure_callback)
   - [`WYNF_NO_UPVALUES`](#wynf_no_upvalues)
7. [Premium Enterprise Macros](#premium-enterprise-macros)
   - [`WYNF_GET_RNG_SEED`](#wynf_get_rng_seed)
   - [`WYNF_BEGIN_CLIENT_LINES`](#wynf_begin_client_lines)
8. [Development Shim Template](#development-shim-template)
9. [Best Practices](#best-practices)

---

## Overview

wYnFuscate macros are static directives processed ahead of bytecode generation:
- **Unobfuscated Development:** In raw source code, define shims inside an `if not WYNF_OBFUSCATED then` guard to permit execution in local test environments (e.g. `tests/run_test.js`) and IDEs.
- **Compilation:** When obfuscating, the compiler parses the AST, inlines or wraps target symbols, applies encryption layers, and strips unreachable branches conditioned on `WYNF_OBFUSCATED`.

---

## Quick Reference Table

| Macro | Signature / Syntax | Category | Tier | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| [`WYNF_OBFUSCATED`](#wynf_obfuscated) | `boolean` | Environment | Free | Detects obfuscation; gates dead-code pruning |
| [`WYNF_LINE`](#wynf_line) | `() -> number` | Debug | Free | Inlines source line number at compile time |
| [`WYNF_CRASH`](#wynf_crash) | `() -> ()` | Security | Free | Immediately terminates VM execution |
| [`WYNF_NO_VIRTUALIZE`](#wynf_no_virtualize) | `<T>(T) -> T` | Performance | Free | Devirtualizes function for native Lua speed |
| [`WYNF_JIT`](#wynf_jit) | `<T>(T) -> T` | Performance | Free | Micro-VM execution template (~1.5x–2.9x faster) |
| [`WYNF_JIT_MAX`](#wynf_jit_max) | `<T>(T) -> T` | Performance | Free | Aggressive VM execution template (~1.9x–5.7x faster) |
| [`WYNF_INLINE`](#wynf_inline) | `<T>(T) -> T` | Performance | Free | Expands function body directly at call sites |
| [`WYNF_ENC_STRING`](#wynf_enc_string) | `(string) -> string` | Cryptography | Free | Cryptographically encrypts string literals |
| [`WYNF_ENC_NUM`](#wynf_enc_num) | `(number) -> number` | Cryptography | Free | Encodes numeric constants against memory scanning |
| [`WYNF_ENC_FUNC`](#wynf_enc_func) | `<T>(T, string, any) -> T` | Cryptography | Free | Encrypts function body with 64-character hex key |
| [`WYNF_ENC_FUNC_SEED`](#wynf_enc_func_seed) | `<T>(T, number, any) -> T` | Cryptography | Free | Encrypts function body with 53-bit numeric seed |
| [`WYNF_IS_CALLER_WYNFUSCATE`](#wynf_is_caller_wynfuscate) | `() -> boolean` | Security | Free | Returns `true` if invoked from within obfuscated code |
| [`WYNF_SECURE_CALL`](#wynf_secure_call) | `<T>(T) -> T` | Security | Free | Gates execution to VM callers only (fail-closed) |
| [`WYNF_SECURE_CALLBACK`](#wynf_secure_callback) | `<T>(T) -> T` | Security | Free | Event callback wrapper with caller gate (fail-quiet) |
| [`WYNF_NO_UPVALUES`](#wynf_no_upvalues) | `<T>(T) -> T` | Compatibility | Free | 0-upvalue proxy wrapper for callback compatibility |
| [`WYNF_GET_RNG_SEED`](#wynf_get_rng_seed) | `() -> number` | Security | **Premium** | Per-execution non-deterministic entropy seed |
| [`WYNF_BEGIN_CLIENT_LINES`](#wynf_begin_client_lines) | `statement` | Debug | **Premium** | Resets source line number base for layered loaders |

---

## Control Flow & Environment Macros

### `WYNF_OBFUSCATED`

```luau
declare WYNF_OBFUSCATED: boolean
```

- **Type:** Compile-time boolean constant
- **Description:** Evaluates to `true` inside obfuscated builds. In unobfuscated code, it evaluates to `false` or `nil`.
- **Dead-Code Elimination:** When evaluated in `if not WYNF_OBFUSCATED then` conditional blocks, the compiler strips the false branch entirely from the output artifact.

```lua
if not WYNF_OBFUSCATED then
    -- Shims and test fixtures (stripped during compilation)
    print("Development environment")
end
```

---

### `WYNF_LINE`

```luau
declare function WYNF_LINE(): number
```

- **Type:** Compile-time value / callable macro
- **Description:** Replaced at compile time with the integer line number of the source file where it is referenced. Does not depend on runtime debug metadata.

```lua
print("Audit checkpoint reached at line:", WYNF_LINE())
```

---

### `WYNF_CRASH`

```luau
declare function WYNF_CRASH(): ()
```

- **Type:** Function call / Destructive statement
- **Description:** Irrecoverably terminates execution and corrupts the virtual machine state. No subsequent statements in the execution thread will run.
- **Use Case:** Anticheat responses, tamper detection, and environment validation failures.

```lua
if not checkEnvironmentIntegrity() then
    WYNF_CRASH()
    return
end
```

---

## Performance & Optimization Macros

### `WYNF_NO_VIRTUALIZE`

```luau
declare function WYNF_NO_VIRTUALIZE<T>(func: T): T
```

- **Type:** Function wrapper
- **Description:** Excludes the wrapped function from the virtual machine interpreter, compiling it as native Luau/Lua. Eliminates VM execution overhead.
- **Security Notice:** Logic within this function is **not** protected by the VM. Constants and control structures remain visible after minification.
- **Limitations:** Self-recursion, mutual recursion, and nested non-virtualized functions are not supported.

```lua
local computeMatrix = WYNF_NO_VIRTUALIZE(function(cf, offset)
    return cf * CFrame.new(offset)
end)
```

---

### `WYNF_JIT`

```luau
declare function WYNF_JIT<T>(func: T): T
```

- **Type:** Function wrapper
- **Description:** Compiles the function using a lightweight micro-VM interpreter template. The function remains fully virtualized, but runs approximately **1.5x to 2.9x faster** than the default VM loop by omitting expensive AST permutations.
- **Use Case:** Hot loops, mathematical routines, table transformations, and UI update listeners.

```lua
local stepSimulation = WYNF_JIT(function(particles, dt)
    for i = 1, #particles do
        particles[i].Position = particles[i].Position + particles[i].Velocity * dt
    end
end)
```

---

### `WYNF_JIT_MAX`

```luau
declare function WYNF_JIT_MAX<T>(func: T): T
```

- **Type:** Function wrapper
- **Description:** The highest-performance virtualized execution mode. Features aggressive opcode caching and streamlined instruction dispatch, yielding **1.9x to 5.7x faster** execution compared to the default VM.
- **Trade-off:** Increases memory usage and closure instantiation latency.

```lua
local pathfinder = WYNF_JIT_MAX(function(startNode, targetNode)
    return computeAStar(startNode, targetNode)
end)
```

---

### `WYNF_INLINE`

```luau
declare function WYNF_INLINE<T>(func: T): T
```

- **Type:** Function wrapper / Compile-time macro
- **Description:** Eliminates the function boundary by inlining the function body directly into its call sites during compilation. Eliminates function call frame overhead for small helper routines.

```lua
local clampValue = WYNF_INLINE(function(val, minVal, maxVal)
    if val < minVal then return minVal end
    if val > maxVal then return maxVal end
    return val
end)

local safeHealth = clampValue(currentHealth, 0, 100)
```

---

## Cryptographic Value & Function Macros

### `WYNF_ENC_STRING`

```luau
declare function WYNF_ENC_STRING(str: string): string
```

- **Type:** String wrapper
- **Description:** Applies multi-layer cryptographic encryption to a string constant at compile time. Prevents static string extraction and memory inspection.
- **Use Case:** Webhook URLs, endpoint paths, remote identifiers, and sensitive authentication headers.

```lua
local webhookUrl = WYNF_ENC_STRING("https://discord.com/api/webhooks/1234/abcd")
```

---

### `WYNF_ENC_NUM`

```luau
declare function WYNF_ENC_NUM(num: number): number
```

- **Type:** Number wrapper
- **Description:** Encrypts numeric literals into computed mathematical expressions, shielding magic constants against memory scanners and static disassembly.

```lua
local licenseKeyBitmask = WYNF_ENC_NUM(0x7F4C9B)
local targetExperienceId = WYNF_ENC_NUM(9876543210)
```

---

### `WYNF_ENC_FUNC`

```luau
declare function WYNF_ENC_FUNC<T>(func: T, encKey: string, decKeyExpr: any): T
```

- **Type:** Function wrapper
- **Parameters:**
  - `func`: Target function to encrypt.
  - `encKey`: A 64-character hexadecimal encryption key specified at compile time.
  - `decKeyExpr`: Runtime expression yielding the matching decryption key.
- **Description:** Encrypts the function bytecode at build time. The function body is dynamically decrypted into memory only when invoked with the matching decryption key.

```lua
local sensitiveRemoteHandler = WYNF_ENC_FUNC(function(payload)
    processTransaction(payload)
end, "f4a1c2d3e4b5a6789012345678abcdef0123456789abcdef0123456789abcdef", fetchServerSessionKey())
```

---

### `WYNF_ENC_FUNC_SEED`

```luau
declare function WYNF_ENC_FUNC_SEED<T>(func: T, seed: number, decSeedExpr: any): T
```

- **Type:** Function wrapper
- **Parameters:**
  - `func`: Target function to encrypt.
  - `seed`: A 53-bit integer seed value specified at build time.
  - `decSeedExpr`: Runtime expression yielding the matching numeric seed.
- **Description:** A numeric-key variant of `WYNF_ENC_FUNC`. Ideal for Roblox and Luau workflows where numeric keys integrate cleanly into integer-based handshake protocols.

```lua
local verifyAuth = WYNF_ENC_FUNC_SEED(function(data)
    return validateData(data)
end, 84920491823, getRuntimeSeed())
```

---

## Caller & Callback Security Macros

### `WYNF_IS_CALLER_WYNFUSCATE`

```luau
declare function WYNF_IS_CALLER_WYNFUSCATE(): boolean
```

- **Type:** Security function
- **Description:** Inspects the caller execution context and returns `true` if invoked from within the obfuscated script VM, and `false` if called from an external environment (e.g. an injector console, third-party hook, or unverified script).

```lua
local function internalDispatcher(...)
    if not WYNF_IS_CALLER_WYNFUSCATE() then
        warn("External caller detected; dropping invocation.")
        return nil
    end
    return executeCommand(...)
end
```

---

### `WYNF_SECURE_CALL`

```luau
declare function WYNF_SECURE_CALL<T>(func: T): T
```

- **Type:** Function wrapper
- **Description:** Enforces strict caller authentication (fail-closed). Invocations from outside the obfuscated VM are blocked immediately.
- **Use Case:** Internal utility functions, core state mutators, and protected module exports.

```lua
local setPlayerAdminLevel = WYNF_SECURE_CALL(function(player, level)
    rawAdminTable[player] = level
end)
```

---

### `WYNF_SECURE_CALLBACK`

```luau
declare function WYNF_SECURE_CALLBACK<T>(func: T): T
```

- **Type:** Function wrapper
- **Description:** Wraps an event listener or callback function with a silent caller authentication gate. If an unauthorized caller triggers the callback, execution returns `nil` silently (fail-quiet) without raising errors.
- **Use Case:** Roblox event connections (`RemoteEvent.OnClientEvent`, `BindableEvent.Event`).

```lua
local onServerSignal = WYNF_SECURE_CALLBACK(function(packet)
    handleServerSync(packet)
end)

networkEvent.OnClientEvent:Connect(onServerSignal)
```

---

### `WYNF_NO_UPVALUES`

```luau
declare function WYNF_NO_UPVALUES<T>(func: T): T
```

- **Type:** Function wrapper
- **Description:** Wraps the virtualized function in a proxy closure containing 0 upvalues. Eliminates compatibility issues with executor hook functions (`hookfunction`, `hookmetamethod`) and external C-closures that fail on functions with upvalues.

```lua
local protectedHookTarget = WYNF_NO_UPVALUES(function(...)
    return handleInterceptedCall(...)
end)
```

---

## Premium Enterprise Macros

### `WYNF_GET_RNG_SEED`

```luau
declare function WYNF_GET_RNG_SEED(): number
```

- **Type:** Value macro / Function call
- **Tier:** **Premium**
- **Aliases:** `WYNF_GET_RNG()`, `WYNF_GET_RNG_SEED()`
- **Description:** Produces a unique, non-deterministic integer seed on each execution run. Used to initialize custom pseudo-random number generators, derive per-session encryption nonces, and resist replay attacks.

```lua
local sessionNonce = WYNF_GET_RNG_SEED()
local customRandom = Random.new(sessionNonce)
```

---

### `WYNF_BEGIN_CLIENT_LINES`

```lua
WYNF_BEGIN_CLIENT_LINES
```

- **Type:** Source statement directive
- **Tier:** **Premium**
- **Description:** A compile-time line remapping marker for enterprise products that prepend custom loaders, hardware ID (HWID) checks, or licensing wrappers to customer scripts. Resets the reported source line counter so that line `1` starts immediately following this directive.

```lua
-- Enterprise Licensing Loader (Lines 1-50)
local function runLicenseCheck() ... end
runLicenseCheck()

WYNF_BEGIN_CLIENT_LINES
-- Customer script starts here as Line 1
print("Customer payload initialization")
```

---

## Development Shim Template

Paste the following header at the top of your scripts to allow clean local execution in IDEs, unit tests, and CLI mock runners (e.g. `tests/run_test.js`) without triggering undefined global errors:

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

## Best Practices

1. **Selective Performance Tuning**:
   - Use `WYNF_NO_VIRTUALIZE` for raw vector calculations or animation loops that do not contain secret logic.
   - Use `WYNF_JIT` for functions requiring virtualized protection with low runtime overhead.
   - Use `WYNF_JIT_MAX` for computationally expensive math routines (e.g. spatial indexing, A* pathfinding).
2. **Event & Hook Safety**:
   - Wrap Roblox network event handlers in `WYNF_SECURE_CALLBACK` to prevent exploiters from directly invoking event handlers via `getconnections`.
   - Apply `WYNF_NO_UPVALUES` to callback closures passed to third-party libraries or hooking systems.
3. **Dynamic Decryption**:
   - For `WYNF_ENC_FUNC` and `WYNF_ENC_FUNC_SEED`, ensure the runtime decryption key or seed is fetched dynamically from a secure server handshake rather than hardcoded in the script.
