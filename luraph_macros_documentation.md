# Luraph Documentation (v15 & Reference)

Source: https://lura.ph/dashboard/documents

This document is a comprehensive, consolidated Markdown reference for **Luraph v15** and the Luraph Obfuscator ecosystem. It contains complete documentation for the new **`LPH_ATTRIBUTES`** system, v15 macros, configuration settings, performance optimizations, limitations, REST API specifications, FAQs, and a migration guide from v14.

---

## Table of Contents

1. [Overview & Architecture](#overview--architecture)
2. [Migrating from v14 to v15](#migrating-from-v14-to-v15)
3. [The `LPH_ATTRIBUTES` System](#the-lph_attributes-system)
   - [Attribute Properties & Inheritance](#attribute-properties--inheritance)
   - [Function Attributes](#function-attributes)
   - [Loop Attributes](#loop-attributes)
   - [Transformations (AST Obfuscation)](#transformations-ast-obfuscation)
4. [Macro API Reference](#macro-api-reference)
   - [Macro Rules & Shimming](#macro-rules--shimming)
   - [Callable Macros](#callable-macros)
   - [Value Macros](#value-macros)
   - [Quick Reference Table](#quick-reference-table)
5. [Configuration & Settings](#configuration--settings)
   - [Standard Settings](#standard-settings)
   - [Premium Settings](#premium-settings)
   - [Dependent Settings](#dependent-settings)
   - [Platform Recommendations](#platform-recommendations)
6. [Performance & Optimization Guide](#performance--optimization-guide)
7. [Limitations & Incompatibilities](#limitations--incompatibilities)
8. [REST API Documentation](#rest-api-documentation)
   - [Authentication](#authentication)
   - [API Endpoints](#api-endpoints)
   - [Official SDKs & Extensions](#official-sdks--extensions)
9. [Frequently Asked Questions (FAQs)](#frequently-asked-questions-faqs)
10. [Appendix: Legacy Luraph v14 Reference](#appendix-legacy-luraph-v14-reference)

---

## Overview & Architecture

Luraph v15 introduces a completely overhauled hybrid-architecture compilation pipeline. The codebase has been rewritten from the ground up to address previous architectural limitations, allowing for granular function-level configuration, advanced code inlining, Mixed Boolean Arithmetic (MBA) rewriting, and multi-tier virtual machine engines.

### Key Architectural Highlights in v15

- **Granular Attribute Control (`LPH_ATTRIBUTES`)**: Obfuscation options, presets, and security features can be selectively applied to individual functions, file scopes, or loops rather than forcing global flags across the entire codebase.
- **Multiple VM Engines**:
  - `OPAL`: Luraph's default virtual machine balancing execution speed and security.
  - `ONYX`: A high-security VM with a distinct Instruction Set Architecture (ISA) that prioritizes security over execution speed.
  - `NONE`: Non-virtualized execution mode for performance-critical functions while still removing comments, identifiers, and optionally applying AST transformations.
- **Novel AST Transformation Pipeline**: Non-virtualized code can receive lightweight obfuscation passes (`EXTRACT`, `CONTROL_FLOW`, `REWRITE_NAMECALLS`) without the performance overhead of full VM virtualization.
- **True Function Inlining (`INLINE`) & Loop Unrolling (`UNROLL`)**: Full support for variadic returns, multi-value assignments, mutable/immutable upvalues, and safe rewriting without illegal `goto` statements.
- **Extended Cryptographic Macro Suite**: Introduces `LPH_ENCBUF` for Luau buffer objects, and supports custom user-supplied runtime keys across strings, buffers, and numbers.
- **Stack Register Allocation (`LPH_STACKALLOC`)**: Allocates registers directly on the VM stack to eliminate heap table allocations.
- **Precheck Integrity Hooks (`LPH_PRECHECK`)**: Integrates custom environment, anti-tamper, and whitelist checks directly into Luraph's loader ahead of main bytecode decryption.

---

## Migrating from v14 to v15

Luraph v15 replaces several global settings and macro wrapper functions with the unified `LPH_ATTRIBUTES` syntax.

### Changed Settings

| v14 Setting | v15 Replacement | Description |
| :--- | :--- | :--- |
| `OPTIMIZATION_LEVEL` | `LPH_ATTRIBUTES(OPTIMIZE(n))` | Replaced with a per-function attribute. Apply to top of file for global behavior. |
| `DISABLE_LINE_INFORMATION` | `LPH_ATTRIBUTES(ERROR_HANDLING(false))` | Replaced with per-function attribute. Disabling increases performance by 2–3x. |
| `ENABLE_GC_FIXES` | *Removed* | Permanently removed in v15. Collection of objects placed on the stack is undefined behavior. |

#### Example: Setting Drop-In Replacement at File Scope

```lua
-- Top of script
if not LPH_OBFUSCATED then
    -- Shims for local development
end

LPH_ATTRIBUTES(OPTIMIZE(3), ERROR_HANDLING(false))

print("Script running under v15 configuration")
```

---

### Changed Macros

| v14 Macro | v15 Attribute Replacement |
| :--- | :--- |
| `LPH_ENCFUNC(func, key, expr)` | `function() LPH_ATTRIBUTES(ENCRYPT(key, expr)) ... end` |
| `LPH_JIT(func)` / `LPH_JIT_MAX(func)` | `function() LPH_ATTRIBUTES(PRESET(FAST)) ... end` |
| `LPH_NO_VIRTUALIZE(func)` | `function() LPH_ATTRIBUTES(VM(NONE)) ... end` |
| `LPH_NO_UPVALUES(func)` | `function() LPH_ATTRIBUTES(NO_UPVALUES) ... end` |

#### Comparison Examples

##### 1. Function Encryption
```lua
-- v14 Syntax
local f = LPH_ENCFUNC(function()
    sensitiveLogic()
end, "6c7572617068203e20616c6c212023316c7572617068203e20616c6c21202331", getKey())

-- v15 Syntax
local f = function() 
    LPH_ATTRIBUTES(ENCRYPT("6c7572617068203e20616c6c212023316c7572617068203e20616c6c21202331", getKey()))
    sensitiveLogic()
end
```

##### 2. Performance / Non-Virtualization
```lua
-- v14 Syntax
local renderLoop = LPH_NO_VIRTUALIZE(function()
    render()
end)

-- v15 Syntax
local renderLoop = function()
    LPH_ATTRIBUTES(VM(NONE))
    render()
end
```

---

## The `LPH_ATTRIBUTES` System

Attributes are specialized macros that enforce properties, set security levels, or apply optimizations to an entire block.

### Placement Rules
`LPH_ATTRIBUTES(...)` **must always be the first statement** in a block body:

```lua
-- File Scope
LPH_ATTRIBUTES(...)

-- Local / Global Functions
local function f() 
    LPH_ATTRIBUTES(...) 
end

-- Anonymous Functions
local g = function() 
    LPH_ATTRIBUTES(...) 
end

-- Method Declarations
function Class:Method() 
    LPH_ATTRIBUTES(...) 
end
```

> **Note on Obfuscation Guards**: The only exception to the first-statement rule is at the outer-most file scope, where a single `if not LPH_OBFUSCATED then ... end` guard is permitted before `LPH_ATTRIBUTES(...)` to allow macro shimming.

---

### Attribute Properties & Inheritance

- **`VM`**: The attribute can only be used inside virtualized functions.
- **`AST`**: The attribute can only be used inside non-virtualized (`VM(NONE)`) functions.
- **`Inherited`**: Child functions automatically inherit the attribute value from their parent/enclosing function unless explicitly overridden.

```lua
local function parent()
    LPH_ATTRIBUTES(ERROR_HANDLING(false), PRESET(FAST))

    local function childA()
        -- Automatically inherits ERROR_HANDLING(false) and PRESET(FAST)
    end

    local function childB()
        LPH_ATTRIBUTES(ERROR_HANDLING(true), PRESET(SECURE))
        -- Explicitly overrides parent attributes
    end
end
```

---

### Function Attributes

#### 1. `ENCRYPT`
*Properties: `VM`*

```lua
function() LPH_ATTRIBUTES(ENCRYPT(key, expression)) end
```

Cryptographically encrypts the virtualized function body with a compile-time key and decrypts it at runtime using the result of `expression`. Any non-virtualized child functions (`VM(NONE)`) within the body are excluded from encryption.

- **Key Formats Accepted**:
  - **Integer**: e.g., `0xDEADBEEF`. *(Note: smaller key size provides lesser security compared to 64-character hex keys)*.
  - **64-character Hex String**: e.g., `"6c7572617068203e20616c6c212023316c7572617068203e20616c6c21202331"`.
  - **Hex Table-String `"{...}"`**: e.g., `"{6c75...2331}"`. When wrapped in braces, the runtime `expression` is expected to return a 32-entry byte array: `{ 0x6c, 0x75, ..., 0x31 }`.

```lua
local function sensitiveService()
    LPH_ATTRIBUTES(ENCRYPT("6c7572617068203e20616c6c212023316c7572617068203e20616c6c21202331", fetchLicenseKey()))
    executeProtectedPayload()
end
```

---

#### 2. `VM`
*Properties: `Inherited`*

```lua
function() LPH_ATTRIBUTES(VM(type)) end
```

Specifies which virtual machine architecture compiles the function.

- **Options**:
  - `OPAL`: Luraph's default balanced VM engine.
  - `ONYX`: High-security VM featuring a distinct ISA, prioritizing security over raw execution speed.
  - `NONE`: Devirtualized execution mode. Variables, comments, and debug info are stripped. Supports AST transformations.

```lua
local function criticalAuth()
    LPH_ATTRIBUTES(VM(ONYX), PRESET(SECURE))
    -- Compiled with the hardened ONYX engine
end

local function mathCalculation()
    LPH_ATTRIBUTES(VM(NONE))
    -- Executes natively for zero VM overhead
end
```

---

#### 3. `PRESET`
*Properties: `VM`, `Inherited`*

```lua
function() LPH_ATTRIBUTES(PRESET(preset)) end
```

Controls the intensity of bytecode obfuscation and security passes applied to the virtualized function.

- **Options**:
  - `FAST`: Low security, high performance. Direct replacement for legacy `LPH_JIT` / `LPH_JIT_MAX`.
  - `BALANCED`: Default setting providing a strong balance of security and speed.
  - `SECURE`: Maximum bytecode security passes, lower execution speed.

---

#### 4. `OPTIMIZE`
*Properties: `Inherited`*

```lua
function() LPH_ATTRIBUTES(OPTIMIZE(level)) end
```

Specifies compiler optimization levels for constant folding, dead code elimination, and instruction scheduling. Does not alter the security intensity (use `PRESET` for security intensity). Defaults to maximum optimization.

---

#### 5. `TRANSFORM`
*Properties: `AST`, `Inherited`*

```lua
function() LPH_ATTRIBUTES(TRANSFORM(...)) end
```

Applies code rewriting passes to non-virtualized (`VM(NONE)`) functions. See [Transformations (AST Obfuscation)](#transformations-ast-obfuscation).

```lua
local function handleData()
    LPH_ATTRIBUTES(VM(NONE), TRANSFORM(CONTROL_FLOW, EXTRACT(GLOBALS)))
    processNetworkPackets()
end
```

---

#### 6. `ERROR_HANDLING`
*Properties: `VM`, `Inherited`*

```lua
function() LPH_ATTRIBUTES(ERROR_HANDLING(enabled)) end
```

Controls whether the virtual machine emits the error-handling routine that embeds original line information into runtime errors.
- `true` (default): Errors report original source lines, utilizing additional stack frames.
- `false`: Error routines and extra call-stack levels are omitted. **Increases runtime performance by 2–3x**. Highly recommended for production builds.

```lua
-- Top of production script
LPH_ATTRIBUTES(ERROR_HANDLING(false))
```

---

#### 7. `INLINE`
*Properties: Applies to all function types*

```lua
function() LPH_ATTRIBUTES(INLINE(checked?)) end
```

Inlines the function into all call sites.
- When all call sites are inlined, the original function definition is completely eliminated from bytecode.
- Fully supports all function signatures, multi-return values, variadics (`...`), and mutable/immutable upvalues without duplicating child functions.
- Functions inlined into non-virtualized code are safely restructured without generating illegal `goto` statements.
- By default (`checked = true`), compilation errors if any call site cannot be resolved for inlining. Passing `INLINE(false)` permits optional inlining without failing if the function escapes.

```lua
local function calculateIndex(base, offset)
    LPH_ATTRIBUTES(INLINE)
    return (base * 32) + (offset & 0x1F)
end

local idx = calculateIndex(x, y) -- Body inlined directly at compile time
```

---

#### 8. `NO_UPVALUES`
*Properties: Compatibility*

```lua
function() LPH_ATTRIBUTES(NO_UPVALUES) end
```

Wraps the function in a 0-upvalue proxy wrapper. Used for compatibility with platform hook functions (`hookfunction`, `hookmetamethod`) that fail when handling functions with high upvalue counts.

---

### Loop Attributes

#### `UNROLL`

```lua
for i = start, limit, step do 
    LPH_ATTRIBUTES(UNROLL(checked?)) 
    -- loop body
end
```

Unrolls numeric for-loops whose iteration bounds (`start`, `limit`, `step`) can be statically computed during compilation.
- Supports `break` and `continue` statements.
- Does not duplicate nested function definitions.
- Works in both virtualized and non-virtualized (`VM(NONE)`) code without side effects.
- Passing `UNROLL(false)` allows optional unrolling if bounds cannot be statically determined.

```lua
for i = 1, 4 do
    LPH_ATTRIBUTES(UNROLL)
    state[i] = state[i] ~ roundKey[i]
end
```

---

### Transformations (AST Obfuscation)

Transformations add obfuscation layers to non-virtualized (`VM(NONE)`) functions without requiring VM virtualization overhead.

- **`EXTRACT`**: Extracts constants and globals out of the function into local lookups.
  - `EXTRACT(GLOBALS)`: Extracts only global identifiers.
  - `EXTRACT(CONSTANTS)`: Extracts only literal constants.
  - `EXTRACT`: Extracts both globals and constants (default).
- **`CONTROL_FLOW`**: Applies control flow flattening across basic blocks of the non-virtualized function.
- **`REWRITE_NAMECALLS`**: Rewrites method invocations `object:method(...)` into `object.method(object, ...)`.

---

## Macro API Reference

### Macro Rules & Shimming

1. **Reserved Prefix**: All identifiers beginning with `LPH_` are reserved for Luraph. Using undefined identifiers starting with `LPH_` causes compilation errors.
2. **Global Declaration**: Macros must be accessed as globals. Declaring macros as local variables (`local LPH_ENCSTR = ...`) will prevent macro replacement.
3. **Unobfuscated Shimming**: When running your script in a development environment without obfuscating, define passthrough shim functions inside an `if not LPH_OBFUSCATED then` block at the top of your script.

```lua
if not LPH_OBFUSCATED then
    LPH_ENCSTR = function(s) return s end
    LPH_ENCBUF = function(s) return (buffer and buffer.fromstring and buffer.fromstring(s) or s) end
    LPH_ENCNUM = function(n) return n end
    LPH_CRASH = function() error("LPH_CRASH called") end
    LPH_STACKALLOC = function(size, base) return {} end
    LPH_PRECHECK = function(fn, expected) return end
    LPH_REWRITE = function(expr, opts) return expr end
    LPH_ATTRIBUTES = function(...) end
    LPH_LINE = 0
    LPH_OBFUSCATED = false
    -- v15 attribute symbol stubs
    VM = function(x) return x end
    PRESET = function(x) return x end
    ERROR_HANDLING = function(x) return x end
    OPTIMIZE = function(x) return x end
    TRANSFORM = function(...) end
    ENCRYPT = function(...) end
    INLINE = function(...) end
    UNROLL = function(...) end
    NONE = "NONE"
    OPAL = "OPAL"
    ONYX = "ONYX"
    FAST = "FAST"
    BALANCED = "BALANCED"
    SECURE = "SECURE"
    NO_UPVALUES = "NO_UPVALUES"
    GLOBALS = "GLOBALS"
    CONSTANTS = "CONSTANTS"
    CONTROL_FLOW = "CONTROL_FLOW"
    REWRITE_NAMECALLS = "REWRITE_NAMECALLS"
end
```

---

### Callable Macros

#### 1. `LPH_ENCSTR`
*String Constant Encryption*

```luau
declare function LPH_ENCSTR(toEncrypt: string): string
declare function LPH_ENCSTR(toEncrypt: string, key: (number | string), expression: any): string
```

Encrypts a string constant using Luraph's cryptographic algorithm.

- **With Custom Decryption Key**:
  - `key`: An integer, 64-character hex string, or `"{hex}"` byte table string.
  - `expression`: Runtime expression that provides the matching decryption key.

```lua
-- Standard compile-time encryption
local secretDomain = LPH_ENCSTR("https://api.internal.service/v1")

-- Keyed encryption bound to runtime license value
local apiKey = LPH_ENCSTR("secret_token_value", 0xCAFEBABE, getAuthKey())
local token = LPH_ENCSTR("payload", "{6c757261...2331}", getByteKeyTable())
```

---

#### 2. `LPH_ENCBUF`
*Luau Buffer Constant Encryption*

```luau
declare function LPH_ENCBUF(toEncrypt: string): buffer
declare function LPH_ENCBUF(toEncrypt: string, key: (number | string), expression: any): buffer
```

Encrypts a string constant and returns a native Luau `buffer` object at runtime. Only supported on Luau platforms. Supports the same optional key and expression parameters as `LPH_ENCSTR`.

```lua
local headerBuffer = LPH_ENCBUF("\x01\x00\x80\xFF\x5A\x3C")
local keyedBuffer = LPH_ENCBUF("RawNetworkData", 0x1337BEEF, getSessionKey())
```

---

#### 3. `LPH_ENCNUM`
*Number Constant Encryption*

```luau
declare function LPH_ENCNUM(toEncrypt: number): number
declare function LPH_ENCNUM(toEncrypt: number, keyTable: { [any]: number }): number
```

Encrypts a number constant (floating-point double or integer), strictly preserving data types.

- **Multi-Key Table Binding (`keyTable`)**:
  - Accepts a table constructor mapping runtime expressions to expected integer keys: `{ [runtimeExpr] = expectedInteger }`.
  - Supports binding up to **8** distinct runtime keys. All keys must evaluate to their expected values at runtime for the number to decrypt properly.

```lua
-- Standard encryption
local maxLimit = LPH_ENCNUM(5000)

-- Multi-key binding
local baseRate = LPH_ENCNUM(100, {
    [getLicenseSlot()] = 42,
    [getSessionState()] = 7
})
```

---

#### 4. `LPH_CRASH`
*VM Destruction & Context Corruption*

```luau
declare function LPH_CRASH(): ()
```

Securely terminates the virtual machine, corrupts internal VM structures, and prevents further execution or state inspection.

```lua
if detectedDebugger() then
    LPH_CRASH()
    return
end
```

---

#### 5. `LPH_STACKALLOC`
*Virtual Register Stack Allocation*

```luau
declare function LPH_STACKALLOC(size: number, zeroOrOne: (0 | 1)?): { [number]: any }
```

Allocates `size` registers directly on the VM stack without creating a Lua table object on the heap.
- `zeroOrOne`: Optional index base (default `1` for 1-based indexing, `0` for 0-based indexing).
- Variable must not escape its local scope (though can be captured by child functions).
- Supports length operator (`#t`), static index access (`t[1]`), dynamic index access (`t[idx]`), and proxy helper methods:
  - `t:pack(i?, j?)`: Packs array elements into a table with an `n` field.
  - `t:unpack(i?, j?)`: Unpacks elements into multiple return values.
  - `t:clear(i?, j?)`: Resets range to `nil`.

```lua
local registers = LPH_STACKALLOC(32)

registers[1] = 10
registers[2] = 20
registers[getIndex()] = 30

assert(#registers == 32)
local output = registers:pack(1, 10)
```

---

#### 6. `LPH_PRECHECK`
*Loader-Level Anti-Tamper & Whitelist Precheck*

```luau
declare function LPH_PRECHECK(check: () -> (number | { number }), expectedKey: (number | { number })): ()
```

Inlines a validation function directly into the Luraph loader prior to the decryption of the main script bytecode.
- Decryption of the main bytecode mathematically requires `check()` to return `expectedKey`.
- `check` must be an anonymous function with zero parameters and zero upvalues (access to `_ENV` globals only).
- Multiple `LPH_PRECHECK` statements can be chained in sequence.

```lua
-- Validate environment PlaceId and UserId before decrypting script
LPH_PRECHECK(function()
    return { game.PlaceId, game:GetService("Players").LocalPlayer.UserId }
end, { 1234567890, 987654321 })

-- Validate response from authentication endpoint
LPH_PRECHECK(function()
    return tonumber(game:HttpGet("https://auth.mysite.com/challenge"))
end, 0x5F3759DF)
```

---

#### 7. `LPH_REWRITE`
*Mixed Boolean Arithmetic (MBA) Expression Obfuscation*

```luau
declare function LPH_REWRITE(expression: number, options: (string | { preset: string?, budget: string?, context: { any }? })?): number
```

Obfuscates arithmetic and bitwise expressions using Mixed Boolean Arithmetic transformations.
- Supported operators: `+`, `-`, `*`, `&`, `|`, `~`, `<<`, `>>`. (Division, modulus, and exponentiation are not supported due to MBA mathematical constraints).
- Supports constants, locals, upvalues, and globals (up to 8 identifiers per expression).
- **Options**:
  - `preset`: `"fast"`, `"standard"` (default), `"strong"`, `"extreme"`.
  - `budget`: `"small"`, `"medium"` (default), `"large"`.
  - `context`: Array of variable identifiers mixed into the transformation. When rewriting purely constant expressions, at least `2` context variables must be supplied.

```lua
local a, b, c = 10, 20, 5

-- Rewriting dynamic arithmetic
local val = LPH_REWRITE((a + b) - c, {
    preset = "strong",
    budget = "large"
})

-- Rewriting with bitwise operations
local mixed = LPH_REWRITE((a << 2) ~ (b & 0xFF), "fast")

-- Rewriting a constant with external context variables
local salt, seed = 1337, 42
local key = LPH_REWRITE(0xCAFE, {
    preset = "standard",
    context = { salt, seed }
})
```

---

### Value Macros

#### 1. `LPH_OBFUSCATED`
*Compile-Time Obfuscation Guard*

```luau
declare LPH_OBFUSCATED: boolean
```

A boolean constant evaluating to `true` during compilation. Branches conditioned on `LPH_OBFUSCATED` are resolved at compile time, and dead branches are entirely removed from the output script.

```lua
if LPH_OBFUSCATED then
    runProductionSecurity()
else
    runLocalDevTesting() -- Stripped completely from obfuscated output
end
```

> **Scoping Note**: When declaring variables inside an `if LPH_OBFUSCATED then` block, the inner block scope is preserved. To export variables to the outer scope, initialize them outside the conditional block first:
> ```lua
> local apiEndpoint
> if LPH_OBFUSCATED then
>     apiEndpoint = "https://live.server.com"
> else
>     apiEndpoint = "http://localhost:8080"
> end
> ```

---

#### 2. `LPH_LINE`
*Compile-Time Source Line Number*

```luau
declare LPH_LINE: number
```

Replaced at compile time with the integer line number of the source file where the macro is called. Does not require error handling or line info to be enabled.

```lua
print("Execution checkpoint at line:", LPH_LINE)
```

---

### Quick Reference Table

| Macro / Attribute | Classification | Target Scope | Description |
| :--- | :--- | :--- | :--- |
| `LPH_ATTRIBUTES(...)` | Attribute Call | Function / File / Loop | Sets attributes, VM architectures, and presets. |
| `ENCRYPT` | Attribute | Virtualized Functions | Cryptographically encrypts function with runtime decryption key. |
| `VM` | Attribute | Functions | Selects `OPAL`, `ONYX`, or `NONE` VM engine. |
| `PRESET` | Attribute | Virtualized Functions | Sets bytecode security intensity (`FAST`, `BALANCED`, `SECURE`). |
| `OPTIMIZE` | Attribute | Functions | Sets compiler optimization level. |
| `TRANSFORM` | Attribute | Non-Virtualized (`VM(NONE)`) | Applies `EXTRACT`, `CONTROL_FLOW`, `REWRITE_NAMECALLS`. |
| `ERROR_HANDLING` | Attribute | Virtualized Functions | Toggles runtime line error formatting (disable for 2–3x speed). |
| `INLINE` | Attribute | Functions | Inlines function directly at all call sites. |
| `NO_UPVALUES` | Attribute | Virtualized Functions | Creates a 0-upvalue wrapper for hook compatibility. |
| `UNROLL` | Attribute | Numeric `for` Loops | Unrolls loops with computable bounds at compile time. |
| `LPH_ENCSTR` | Callable Macro | Expression | Encrypts string constant with optional custom runtime key. |
| `LPH_ENCBUF` | Callable Macro | Expression | Encrypts constant into native Luau buffer. |
| `LPH_ENCNUM` | Callable Macro | Expression | Encrypts number constant with optional multi-key binding. |
| `LPH_CRASH` | Callable Macro | Statement / Expression | Securely destroys the VM and corrupts VM state. |
| `LPH_STACKALLOC` | Callable Macro | Statement | Allocates stack registers without heap table allocations. |
| `LPH_PRECHECK` | Callable Macro | Statement | Inlines anti-tamper/whitelist checks into loader before decryption. |
| `LPH_REWRITE` | Callable Macro | Expression | Obfuscates integer math with Mixed Boolean Arithmetic (MBA). |
| `LPH_OBFUSCATED` | Value Macro | Constant (`boolean`) | Evaluates to `true` with dead code elimination. |
| `LPH_LINE` | Value Macro | Constant (`number`) | Evaluates to the current compile-time line number. |

---

## Configuration & Settings

Luraph provides standard, premium, and dependent configuration options that control global obfuscation behavior.

### Standard Settings
*Available across all subscription tiers.*

- **`Target Version`**: Specifies the target Lua version or host platform runtime. Sets compiler rules, integer widths, instruction sets, and platform compatibility shims.
- **`Static Environment`**: Asserts that function environment references will never change at runtime, disabling internal `getfenv`/`setfenv` calls and preventing Luau environment deoptimization.
- **`Enable Compatibility Mode`**: Disables extended syntax features and certain internal security passes to maximize compatibility on heavily modified or custom Lua engines.
- **`Use Debug Library`**: Employs the host environment's `debug` library for enhanced VM anti-tampering and hook-detection protections. Requires access to the complete standard `debug` library.

---

### Premium Settings
*Available to Luraph Now, Premium, Commercial, and Enterprise tiers.*

- **`Intense VM Structure`**: Generates complex multi-layer VM dispatch structures to resist static decompilation and control-flow recovery tools.
- **`VM Compression`**: Compresses the obfuscated output using high-ratio compression into a compact self-extracting loader. Requires `load` or `loadstring` in the target environment.

---

### Dependent Settings
*Options enabled conditionally based on other setting prerequisites.*

- **`Enable FFI Library`**: Enables FFI-specific features (such as 64-bit integer literals `0ULL` in LuaJIT). *Requires Target Version to be set to `LuaJIT`.* (Must not be used on Garry's Mod).
- **`Hardcode Globals`**: Embeds global reads/writes directly into the VM for speed and environment compatibility. Global names remain in readable plaintext. *Requires a platform with `getfenv` support (`Lua 5.1`, `LuaJIT`, `Luau`, `Roblox`).*

---

### Platform Recommendations

| Platform | Recommended Target Version | Key Settings & Attributes |
| :--- | :--- | :--- |
| **Roblox (Server & Client)** | `Roblox` or `Luau` | `Static Environment = true`, `ERROR_HANDLING(false)` in production. |
| **FiveM** | `FiveM` (or `Lua 5.4`) | Ensure `lua54 'yes'` in `fxmanifest.lua`. Apply `VM(NONE)` to high-frequency Citizen threads. |
| **LuaJIT (Standalone)** | `LuaJIT` | Enable `FFI Library` if utilizing C types. |
| **Garry's Mod (GLua)** | `Garry's Mod` | `Enable FFI Library = false`. |
| **Multi-Theft Auto (MTA)** | `Lua 5.1` | `Hardcode Globals = true`, `Static Environment = true`. |
| **World of Warcraft / WC3** | `World of Warcraft` / `Warcraft 3` | Target platform-specific profiles for 32-bit integer alignment. |
| **Standard Lua 5.1 – 5.4** | Matching `Lua 5.x` | Select exact minor version matching the host engine. |

---

## Performance & Optimization Guide

Obfuscation inevitably introduces runtime overhead. Following these best practices ensures peak execution performance:

### 1. Disable Error Handling in Production
Leaving error handling enabled wraps VM instructions in protected frames to reconstruct source line numbers. Calling `LPH_ATTRIBUTES(ERROR_HANDLING(false))` at the file scope removes this overhead, yielding an immediate **2x to 3x performance increase**.

### 2. Isolate Performance-Critical Hot Paths
Code executed frequently (e.g., per-frame render hooks, physics loops, high-frequency network tickers) should not be virtualized:
- Apply `LPH_ATTRIBUTES(VM(NONE))` to hot-path functions.
- If lightweight security is desired on devirtualized code, attach AST transformations: `LPH_ATTRIBUTES(VM(NONE), TRANSFORM(CONTROL_FLOW, EXTRACT))`.

#### Roblox Hot-Path Example
```lua
local RunService = game:GetService("RunService")

-- Fast per-frame rendering with no VM overhead
local onRenderStep = function(deltaTime)
    LPH_ATTRIBUTES(VM(NONE))
    updateLocalCamera(deltaTime)
end

RunService.RenderStepped:Connect(onRenderStep)
```

#### FiveM Hot-Path Example
```lua
-- Intensive tick thread
Citizen.CreateThread(function()
    LPH_ATTRIBUTES(VM(NONE))
    while true do
        Citizen.Wait(0)
        drawHudOverlay()
    end
end)
```

### 3. Use `INLINE` and `UNROLL` for Small Helper Routines
Inlining short mathematical helper functions eliminates function call overhead and register shuffling. Unrolling fixed loops avoids loop counter branch evaluation.

### 4. Cache Table Lookups and Global Accesses
Chained table lookups (`a.b.c.d.e()`) generate dozens of VM instructions per dereference. Always localize hot table lookups:

```lua
-- Unoptimized
for i = 1, 1000 do
    workspace.CurrentCamera.CFrame = calculate(i)
end

-- Optimized
local camera = workspace.CurrentCamera
for i = 1, 1000 do
    camera.CFrame = calculate(i)
end
```

---

## Limitations & Incompatibilities

| Incompatibility | Cause | Mitigation / Workaround |
| :--- | :--- | :--- |
| **`debug` Library Functions** | `debug.getinfo`, `debug.getlocal`, `debug.getupvalue`, and `debug.traceback` return internal VM constructs. | Avoid relying on runtime debug reflection inside protected scripts. |
| **Stack Level Discrepancies** | VM error-handling routines add stack frames. | Use `LPH_ATTRIBUTES(ERROR_HANDLING(false))` or pass function objects directly to `setfenv` instead of stack levels. |
| **High Upvalue Counts** | Virtualized functions naturally require more upvalues. Hook utilities (`hookfunction`) may fail. | Apply `LPH_ATTRIBUTES(NO_UPVALUES)` or `LPH_ATTRIBUTES(VM(NONE))`. |
| **`string.dump`** | Luraph VM routines define upvalues outside the returned function closure. | Do not use `string.dump` on virtualized Luraph functions. |
| **Luau Iteration Variables** | Supporting Luau's `__iter` metamethod imposes an artificial limit of 10 variables per `for ... in` statement. | Do not exceed 10 loop variables in a single iterator loop statement. |
| **Table Length with `nil` Elements** | Cross-platform inconsistencies with `#` on sparse arrays. | Do not rely on `#` on tables containing intermediate `nil` values. |
| **Constructor Assignment Order** | Duplicate keys in mixed table constructors are assigned sequentially in source order. | Avoid declaring duplicate keys in literal table constructors. |
| **File Size Limit** | 50 MB input limit per obfuscation job. | Modularize or bundle scripts below 50 MB. |
| **No `__namecall` inside VM** | Colon method calls (`:`) inside the VM resolve to `__index`. | Prevent infinite recursion if hooking `__index` with colon calls, or mark function `VM(NONE)`. |
| **Lua 5.4 `<close>` & FiveM `defer`** | Scope closing attributes on variables are not supported by the VM. | Use standard `pcall` / `xpcall` cleanup handlers instead. |

---

## REST API Documentation

Luraph provides a RESTful API (`https://api.lura.ph/v1`) for integrating automated obfuscation pipelines, CI/CD workflows, and licensing bots.

### Authentication
Include your secret API key in the `Luraph-API-Key` HTTP header with all requests:
```http
Luraph-API-Key: YOUR_API_KEY_HERE
Content-Type: application/json
```

---

### API Endpoints

#### 1. `GET /v1/obfuscate/nodes`
Returns available obfuscation cluster nodes, versions, CPU loads, and supported option schemas.

**Response Structure:**
```json
{
  "recommendedId": "main-1",
  "nodes": {
    "main-1": {
      "version": "15.0.0",
      "cpuUsage": 18.4,
      "options": {
        "TARGET_VERSION": {
          "name": "Target Version",
          "description": "Platform target version",
          "tier": "CUSTOMER_ONLY",
          "type": "DROPDOWN",
          "required": true,
          "choices": ["Roblox", "Luau", "Lua 5.1", "Lua 5.2", "Lua 5.3", "Lua 5.4", "LuaJIT", "FiveM"],
          "dependencies": {}
        },
        "STATIC_ENVIRONMENT": {
          "name": "Static Environment",
          "description": "Asserts static environment references",
          "tier": "CUSTOMER_ONLY",
          "type": "CHECKBOX",
          "required": false,
          "choices": [],
          "dependencies": {}
        }
      }
    }
  }
}
```

---

#### 2. `POST /v1/obfuscate/new`
Submits a script for obfuscation. Accepts JSON payloads or `multipart/form-data`.

**Request Body (JSON):**
```json
{
  "fileName": "main_script.lua",
  "node": "main-1",
  "script": "cHJpbnQoIkhlbGxvIGZyb20gTHVyYXBoIik=",
  "options": {
    "TARGET_VERSION": "Roblox",
    "STATIC_ENVIRONMENT": true
  },
  "useTokens": false,
  "enforceSettings": true
}
```

*Parameters:*
- `fileName` (string, max 255 chars): Script filename.
- `node` (string): Target node identifier from `/nodes`.
- `script` (string, max 50MB): Base64-encoded script content.
- `options` (object): Map of option keys to selected values.
- `useTokens` (boolean): Whether to consume pay-per-use tokens instead of subscription quota.
- `enforceSettings` (boolean, default `true`): Validates that all options match node schema.

**Response:**
```json
{
  "jobId": "3fa85f64-5717-4562-b3fc-2c963f66afa6"
}
```

---

#### 3. `GET /v1/obfuscate/status/:jobId`
Polls or blocks until the obfuscation job completes (timeout: 180 seconds).

**Response (Success):**
```json
{}
```

**Response (Compilation Error):**
```json
{
  "error": "Luraph:14: unexpected symbol near '='"
}
```

---

#### 4. `GET /v1/obfuscate/download/:jobId`
Downloads the obfuscated Lua file. Results are preserved for **24 hours** before automatic purging.

- Response Header: `Content-Type: text/x-lua`
- Response Body: Complete obfuscated Lua code.

---

### Official SDKs & Extensions

- **Node.JS SDK**: [npm: `luraph`](https://npmjs.com/package/luraph) ([GitHub](https://github.com/Luraph/luraph-node))
- **.NET SDK**: [NuGet: `luraph.api`](https://www.nuget.org/packages/luraph.api) ([GitHub](https://github.com/Luraph/luraph-dotnet))
- **VS Code Extension**: [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=luraph.luraph-vscode) ([GitHub](https://github.com/Luraph/luraph-vscode))
- **Macro SDK Shims**: [GitHub: `Luraph/macrosdk`](https://github.com/Luraph/macrosdk)

---

## Frequently Asked Questions (FAQs)

### Does Luraph store or log my script?
**No.** Luraph does not store the source code of your scripts under any circumstances. Obfuscated output files are permanently purged from servers within 24 hours. Only cryptographic hashes may be recorded for platform integrity verification.

### What versions of Lua does Luraph support?
Luraph guarantees support for the latest patch releases of Lua 5.1 through 5.4, Luau, LuaJIT, and customized game engines (Roblox, FiveM, Garry's Mod, Warcraft 3, World of Warcraft, MTA).

### How do I resolve FiveM "Detected use of unauthorized Lua obfuscators"?
Ensure your script is obfuscated using current Luraph releases, select the `FiveM` or `Lua 5.4` target version, and ensure `lua54 'yes'` is defined in your resource's `fxmanifest.lua`.

### Where can I get support or report bugs?
Join the official [Luraph Discord Community](https://discord.lura.ph/) to open support tickets or discuss integration strategies.

---

## Appendix: Legacy Luraph v14 Reference

For developers maintaining scripts targeting Luraph v14:

### Legacy v14 Macros
```lua
-- Function Encryption (v14)
LPH_ENCFUNC(function() ... end, "64_HEX_KEY", runtimeKey)

-- String / Number Encryption (v14)
local s = LPH_ENCSTR("constant_string")
local n = LPH_ENCNUM(12345)

-- Performance Optimization (v14)
local fastFn = LPH_JIT(function() ... end)
local maxFastFn = LPH_JIT_MAX(function() ... end)

-- Non-Virtualization (v14)
local devirtualizedFn = LPH_NO_VIRTUALIZE(function() ... end)

-- Upvalue Stripping (v14)
local noUpvalFn = LPH_NO_UPVALUES(function() ... end)

-- Obfuscation Guard & Line Info (v14)
local isObf = LPH_OBFUSCATED
local lineNum = LPH_LINE
LPH_CRASH()
```
