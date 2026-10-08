# Goofyscator Macros Documentation

This document is a comprehensive reference for the **Goofyscator** Lua/Luau obfuscator macro system. Goofyscator macros allow script authors to selectively balance runtime execution performance and bytecode security using compile-time transformations, selective virtualization, and cryptographic wrappers.

---

## Table of Contents

1. [Overview](#overview)
2. [Quick Reference Table](#quick-reference-table)
3. [Macro API Reference](#macro-api-reference)
   - [`GUF_OBFUSCATED`](#guf_obfuscated)
   - [`GUF_CRASH`](#guf_crash)
   - [`GUF_NO_VIRTUALIZE`](#guf_no_virtualize)
   - [`GUF_JIT`](#guf_jit)
   - [`GUF_JIT_MAX`](#guf_jit_max)
   - [`GUF_ENCFUNC` / `GUF_FUNCENC`](#guf_encfunc--guf_funcenc)
   - [`GUF_ENCSTR` / `GUF_STRENC`](#guf_encstr--guf_strenc)
   - [`GUF_ENCNUM` / `GUF_NUMENC`](#guf_encnum--guf_numenc)
4. [Development Shim Template](#development-shim-template)
5. [Performance & Security Best Practices](#performance--security-best-practices)
6. [Known Limitations](#known-limitations)

---

## Overview

Goofyscator macros are evaluated and resolved statically during compile time:
- In **development and unobfuscated environments**, macro definitions must be shimmed so that scripts run without unresolved global errors.
- During **obfuscation**, Goofyscator detects these macro calls and replaces them with specialized VM opcodes, eliminates dead branches, or routes functions into native Lua dispatch paths.

---

## Quick Reference Table

| Macro | Type / Classification | Purpose | Security | Performance |
| :--- | :--- | :--- | :--- | :--- |
| [`GUF_OBFUSCATED`](#guf_obfuscated) | Boolean constant | Compile-time obfuscation detection & dead-code elimination | Low | N/A |
| [`GUF_CRASH()`](#guf_crash) | Statement / Function | Halts execution and corrupts VM internal state | High | N/A |
| [`GUF_NO_VIRTUALIZE(fn)`](#guf_no_virtualize) | Function wrapper | Emits native Luau/Lua bytecode bypassing the VM interpreter | None | Maximum |
| [`GUF_JIT(fn)`](#guf_jit) | Function wrapper | Micro-VM execution skipping heavy AST mutations | High | Fast (~1.5–2.5x) |
| [`GUF_JIT_MAX(fn)`](#guf_jit_max) | Function wrapper | Maximum-speed VM mode bypassing string encryption & AST passes | Moderate | Fastest VM (~2–5x) |
| [`GUF_ENCFUNC(fn, enc, dec)`](#guf_encfunc--guf_funcenc) | Function wrapper | Cryptographically encrypts function; decrypted dynamically at runtime | Maximum | Moderate |
| [`GUF_ENCSTR(str)`](#guf_encstr--guf_strenc) | String wrapper | Cryptographically encrypts string constants | High | Fast |
| [`GUF_ENCNUM(num)`](#guf_encnum--guf_numenc) | Number wrapper | Encrypts numeric literals against static bytecode scanning | High | Fast |

---

## Macro API Reference

### `GUF_OBFUSCATED`

```luau
declare GUF_OBFUSCATED: boolean
```

- **Type:** Compile-time constant (`boolean`)
- **Description:** Evaluates to `true` when the script has been processed by Goofyscator. When used inside a conditional check (`if not GUF_OBFUSCATED then ... end`), the compiler prunes the unreachable branch completely from the obfuscated output.
- **Use Case:** Encapsulating local development shims, debugging utilities, and test suites.

```lua
if not GUF_OBFUSCATED then
    print("Running in unobfuscated local test harness")
else
    print("Running inside Goofyscator VM")
end
```

---

### `GUF_CRASH()`

```luau
declare function GUF_CRASH(): ()
```

- **Type:** Statement / Function call
- **Description:** Immediately corrupts virtual machine registers, invalidates the execution context, and halts the script.
- **Use Case:** Anticheat responses, tamper detection traps, failed integrity assertions (`check()` / `__ZSA_CHECK()`), and unauthorized debugger detection.

```lua
if detectedTampering() then
    GUF_CRASH()
    return
end
```

---

### `GUF_NO_VIRTUALIZE`

```luau
declare function GUF_NO_VIRTUALIZE<T>(func: T): T
```

- **Type:** Function wrapper
- **Description:** Completely devirtualizes the wrapped function so that it executes natively in Luau/Lua without VM interpreter overhead.
- **Security Notice:** The function body is **not** protected by the virtual machine. Local identifiers are minified and comments are removed, but logic flow and constants remain visible.
- **Use Case:** Extremely tight loops (e.g. per-frame `RenderStepped` or physics calculations) where maximum raw speed is critical.

```lua
local calculateVector = GUF_NO_VIRTUALIZE(function(a, b, t)
    return a + (b - a) * t
end)
```

---

### `GUF_JIT`

```luau
declare function GUF_JIT<T>(func: T): T
```

- **Type:** Function wrapper
- **Description:** Routes the function through a high-performance micro-VM execution path. Skips expensive AST transformations while keeping bytecode fully virtualized and protected.
- **Use Case:** Animation updates, distance calculations, state polling, and frequent game-loop helpers.

```lua
local updatePositions = GUF_JIT(function(entities)
    for _, entity in ipairs(entities) do
        entity.Position = entity.Position + entity.Velocity
    end
end)
```

---

### `GUF_JIT_MAX`

```luau
declare function GUF_JIT_MAX<T>(func: T): T
```

- **Type:** Function wrapper
- **Description:** The most aggressive performance optimization wrapper. Bypasses AST mutations, complex control-flow flattening, and string encryption passes within the VM template for maximum throughput while preserving virtualization.
- **Use Case:** Pathfinding algorithms, procedural generation, raycasting loops, and heavy mathematical routines.

```lua
local raycastScan = GUF_JIT_MAX(function(origin, directions)
    local results = {}
    for i, dir in ipairs(directions) do
        results[i] = workspace:Raycast(origin, dir)
    end
    return results
end)
```

---

### `GUF_ENCFUNC` / `GUF_FUNCENC`

```luau
declare function GUF_ENCFUNC<T>(func: T, encKey: string, decKey: any): T
declare function GUF_FUNCENC<T>(func: T, encKey: string, decKey: any): T
```

- **Type:** Function wrapper
- **Aliases:** `GUF_ENCFUNC`, `GUF_FUNCENC`
- **Parameters:**
  - `func`: Target function to encrypt.
  - `encKey`: A 64-character hexadecimal encryption key specified at build time.
  - `decKey`: A runtime expression that yields the matching decryption key.
- **Description:** Cryptographically encrypts the function body at compile time. The function is decrypted into memory only when invoked with the matching decryption key.
- **Use Case:** Sensitive remotes, license validation handshakes, payment handling, and anti-tamper callbacks.

```lua
local executePayload = GUF_ENCFUNC(function(token)
    sendProtectedPayload(token)
end, "a1b2c3d4e5f60718293a4b5c6d7e8f90123456789abcdef0123456789abcdef0", getServerAuthKey())
```

---

### `GUF_ENCSTR` / `GUF_STRENC`

```luau
declare function GUF_ENCSTR(str: string): string
declare function GUF_STRENC(str: string): string
```

- **Type:** String wrapper / Callable macro
- **Aliases:** `GUF_ENCSTR`, `GUF_STRENC`
- **Description:** Encrypts string literals with multi-layered cryptographic obfuscation, preventing literal string extraction via static memory scanners or raw bytecode inspection.
- **Use Case:** Remote names, secret API URLs, webhook links, and decryption keys.

```lua
local remoteName = GUF_ENCSTR("SecureNetworkDispatcher")
local apiEndpoint = GUF_STRENC("https://api.foxname.top/v1/verify")
```

---

### `GUF_ENCNUM` / `GUF_NUMENC`

```luau
declare function GUF_ENCNUM(num: number): number
declare function GUF_NUMENC(num: number): number
```

- **Type:** Number wrapper / Callable macro
- **Aliases:** `GUF_ENCNUM`, `GUF_NUMENC`
- **Description:** Encodes numeric literals into mathematical formulas or encrypted constant structures, preventing magic numbers from appearing in constant pools.
- **Use Case:** Game place IDs, product IDs, protocol opcodes, and balance check constants.

```lua
local targetPlaceId = GUF_ENCNUM(116495829188952)
local protocolOpcode = GUF_NUMENC(0x7F)
```

---

## Development Shim Template

Include the following header at the top of scripts to ensure clean execution during local testing, mock execution (e.g., `tests/run_test.js`), and development in both standard Luau and executor environments:

```lua
if not GUF_OBFUSCATED then
    GUF_OBFUSCATED = false
    GUF_CRASH = function() error("GUF_CRASH called") end
    GUF_NO_VIRTUALIZE = function(func) return func end
    GUF_JIT = function(func) return func end
    GUF_JIT_MAX = function(func) return func end
    GUF_ENCFUNC = function(func, encKey, decKey) return func end
    GUF_FUNCENC = GUF_ENCFUNC
    GUF_ENCSTR = function(str) return str end
    GUF_STRENC = GUF_ENCSTR
    GUF_ENCNUM = function(num) return num end
    GUF_NUMENC = GUF_ENCNUM

    if typeof and getgenv and typeof(getgenv) == "function" then
        local env = getgenv()
        env.GUF_OBFUSCATED = false
        env.GUF_CRASH = GUF_CRASH
        env.GUF_NO_VIRTUALIZE = GUF_NO_VIRTUALIZE
        env.GUF_JIT = GUF_JIT
        env.GUF_JIT_MAX = GUF_JIT_MAX
        env.GUF_ENCFUNC = GUF_ENCFUNC
        env.GUF_FUNCENC = GUF_FUNCENC
        env.GUF_ENCSTR = GUF_ENCSTR
        env.GUF_STRENC = GUF_STRENC
        env.GUF_ENCNUM = GUF_ENCNUM
        env.GUF_NUMENC = GUF_NUMENC
    end
end
```

---

## Performance & Security Best Practices

1. **Selective Virtualization**:
   - Use `GUF_NO_VIRTUALIZE` strictly for non-sensitive, high-frequency routines (e.g. UI interpolation, matrix transforms).
   - Use `GUF_JIT` for functions requiring protection with fast execution.
   - Retain standard VM virtualization for business logic, credentials, and security checks.
2. **Dynamic Decryption Keys**:
   - For `GUF_ENCFUNC`, always derive `decKey` dynamically (e.g. from a server handshake or mathematical challenge) rather than passing a static literal.
3. **Dead-Code Elimination**:
   - Wrap all local debug flags and mocks inside `if not GUF_OBFUSCATED then ... end` to ensure zero footprint in production builds.

---

## Known Limitations

- **Devirtualization Exposure**: Code wrapped with `GUF_NO_VIRTUALIZE` is completely exposed to static analysis aside from variable renaming and minification.
- **Key Mismatch**: Invoking a `GUF_ENCFUNC`-wrapped function with an invalid runtime decryption key results in unrecoverable execution failure.
- **Stack Trace Differences**: Virtualized functions create modified call stack levels compared to standard Luau execution.