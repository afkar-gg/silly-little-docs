# Goofyscator Macros Documentation

This document is a reference for the **Goofyscator** Lua/Luau obfuscator macro system. Goofyscator macros allow developers to selectively balance performance and security within their scripts using compile-time transformations, virtualization controls, and cryptographic wrappers.

---

## Overview

Goofyscator macros are recognized at compile time. In development/unobfuscated code, macro shims should be provided so your script runs without error. During obfuscation, Goofyscator strips or replaces these macros with VM opcodes or optimized execution paths.

### Quick Reference

| Macro | Type | Description |
| :--- | :--- | :--- |
| `GUF_OBFUSCATED` | Boolean constant | Evaluates to `true` in obfuscated builds; used for dead code elimination. |
| `GUF_CRASH()` | Function call | Immediately corrupts the VM state and halts execution. |
| `GUF_NO_VIRTUALIZE(func)` | Function wrapper | Compiles function as native Lua (bypasses VM). **Warning:** Exposes logic. |
| `GUF_JIT(func)` | Function wrapper | Virtualized micro-VM execution with minimized AST transforms for high speed. |
| `GUF_JIT_MAX(func)` | Function wrapper | Maximum performance mode (bypasses AST transforms and string encryption). |
| `GUF_ENCFUNC(func, encKey, decKey)` | Function wrapper | Cryptographically encrypts function at build time; decrypted dynamically at runtime. |
| `GUF_ENCSTR(str)` | String wrapper | Applies intensive multi-layer cryptographic encryption to string constants. |
| `GUF_ENCNUM(num)` | Number wrapper | Encrypts numeric literals to prevent static memory scanning and tampering. |

---

## Macro API Reference

### `GUF_OBFUSCATED`
```lua
if not GUF_OBFUSCATED then
    -- Dev / fallback shims
end
```
- **Type:** Compile-time constant (`boolean`)
- **Description:** Evaluates to `true` when the script has passed through Goofyscator. When used in conditional checks (`if not GUF_OBFUSCATED then ... end`), the obfuscator completely removes unreachable branches during dead-code pruning.

---

### `GUF_CRASH()`
```lua
GUF_CRASH()
```
- **Description:** Immediately terminates script execution and crashes the virtual machine context.
- **Use Case:** Anticheat / anti-tamper detection response, integrity check failures, and unauthorized environment detection.

---

### `GUF_NO_VIRTUALIZE`
```lua
local myFastFunc = GUF_NO_VIRTUALIZE(function(a, b)
    return a + b
end)
```
- **Signature:** `function GUF_NO_VIRTUALIZE(const function func)`
- **Description:** Completely devirtualizes the passed function so that it executes natively in Luau/Lua without VM interpreter overhead.
- **⚠️ Warning:** The contents of this function are **not protected** by virtualization. Code will only be minified/stripped of comments and local variable names. Do **NOT** use this on sensitive security checks or secret remotes.

---

### `GUF_JIT`
```lua
local renderLoop = GUF_JIT(function()
    -- Fast loop or render step calculations
end)
```
- **Signature:** `function GUF_JIT(const function func)`
- **Description:** Optimizes function execution speed by omitting expensive AST transformations while keeping the bytecode fully virtualized inside the VM.
- **Use Case:** Hot loops, rendering loops (`RenderStepped`), vector calculations, and physics updates.

---

### `GUF_JIT_MAX`
```lua
local heavyMath = GUF_JIT_MAX(function()
    -- Heavy algorithmic computations
end)
```
- **Signature:** `function GUF_JIT_MAX(const function func)`
- **Description:** The most aggressive performance optimization wrapper. Bypasses AST mutation and string encryption overhead within the VM while still running inside virtualized bytecode.

---

### `GUF_ENCFUNC`
```lua
local secretFunction = GUF_ENCFUNC(function()
    -- Secret payload or sensitive logic
end, "64_CHAR_HEX_ENCRYPTION_KEY...", dynamicDecryptionKey)
```
- **Signature:** `function GUF_ENCFUNC(const function toEncrypt, const string encKey, string decKey)`
- **Aliases:** `GUF_FUNCENC`
- **Description:** Encrypts the wrapped function body at compile-time with a 64-character hexadecimal key. Decryption occurs only at runtime when supplied with the matching key.
- **Note:** If an incorrect key is provided at runtime, execution of that function fails. Always pass the decryption key as a runtime variable (e.g., from a server handshake or key validation response).

---

### `GUF_ENCSTR`
```lua
local apiKey = GUF_ENCSTR("https://api.foxname.top/auth")
```
- **Signature:** `string GUF_ENCSTR(const string str)`
- **Aliases:** `GUF_STRENC`
- **Description:** Encrypts sensitive string constants using an extra layer of cryptographic protection, making static memory inspection and raw byte dumping substantially more difficult.

---

### `GUF_ENCNUM`
```lua
local placeId = GUF_ENCNUM(116495829188952)
```
- **Signature:** `number GUF_ENCNUM(const number num)`
- **Aliases:** `GUF_NUMENC`
- **Description:** Encodes numeric constants with encryption algorithms, preventing magic numbers from being located via constant tables or static bytecode disassemblers.

---

## Development Shim Template

To allow your source code to run smoothly in standard environments and local test runners without crashing, include the following macro header at the top of your scripts:

```lua
if not GUF_OBFUSCATED then
    getgenv().GUF_OBFUSCATED = false
    getgenv().GUF_CRASH = function() while true do end end
    getgenv().GUF_NO_VIRTUALIZE = function(...) return ... end
    getgenv().GUF_JIT = function(...) return ... end
    getgenv().GUF_JIT_MAX = function(...) return ... end
    getgenv().GUF_ENCFUNC = function(func) return func end
    getgenv().GUF_FUNCENC = getgenv().GUF_ENCFUNC
    getgenv().GUF_ENCSTR = function(str) return str end
    getgenv().GUF_STRENC = getgenv().GUF_ENCSTR
    getgenv().GUF_ENCNUM = function(num) return num end
    getgenv().GUF_NUMENC = getgenv().GUF_ENCNUM
end
```