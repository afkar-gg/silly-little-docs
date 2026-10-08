# MoonVeil Macros & API Documentation

Sources:
- Macros & Directives: https://moonveil.cc/docs/macros/
- HTTP API Reference: https://moonveil.cc/docs/api

MoonVeil is an obfuscation and transformation suite designed for Luau. The system provides two primary interfaces:
1. **In-script compiler macros and comment directives** (`--!mv:...`) allowing selective, function-by-function control over obfuscation, virtualization, control flow flattening, and optimization passes.
2. **REST HTTP API (v2)** for programmatic obfuscation, minification, and code formatting.

---

## 1. Directive Paradigms: Comments vs Call-Style

MoonVeil macros can be declared in two styles:

### A. Comment Directives (`--!mv:...`)
Placed directly above local functions or function declarations.
```luau
--!mv:vm
local function verify(pw)
    return pw == "s3cret"
end
```
**Benefits**:
- Fully compliant with Luau language server (`luau-lsp`, `roblox-live-lsp`) without raising unknown global or typing errors.
- No SDK headers or unobfuscated shims required for typecheckers and linters.
- Plain source scripts can be executed and debugged directly in Luau/Roblox without modification.

### B. Call-Style Inline Macros (`MV_*(...)`)
Wrapped around expressions or anonymous function bodies.
```luau
local verify = MV_VM(function(pw)
    return pw == "s3cret"
end)
```
Macros are replaced and stripped during AST compilation.

---

## 2. Security Macros

### `MV_PRECHECK`
```luau
declare function MV_PRECHECK(fn: () -> ...any, ...keys: any): ()
```
Runs a precheck function before the script is decrypted and folds its return values into the script's decryption key. Each key argument must match the corresponding return value in order and type; on any mismatch (wrong value, wrong type, or missing return), key derivation fails and the script halts.

**Parameters**:
- `fn`: Anonymous function returning environment values being checked. Must be non-variadic and take no parameters. Globals are allowed, but cannot capture upvalues.
- `...keys`: Expected values matching returned values in order. Supported types: number (non-negative integer < 4294967295 / uint32), string, or boolean literal.

> **Note**: Only available with the `safeEnv` compile type.

```luau
MV_PRECHECK(function()
    return 0x1234
end, 0x1234)

-- Multiple keys checked in order
MV_PRECHECK(function()
    return true, "rosiej", 255
end, true, "rosiej", 255)

-- Inspecting global environment
MV_PRECHECK(function()
    return game.PlaceId
end, 25522223443)
```

---

### `MV_REWRITE`
```luau
declare function MV_REWRITE<T>(expr: T, preset?: "fast" | "strong" | "extreme", ...ctx: any): T
```
Rewrites an integer or word arithmetic expression into an equivalent Mixed Boolean Arithmetic (MBA) expression, hardening calculations against static and symbolic analysis.

**Supported Operators**:
- Arithmetic: `+`, `-`, `*`
- Lua 5.3 / Luau bitwise: `&`, `|`, `~`, unary `~`
- Bit shifts: `<<`, `>>`

**Restrictions**:
- Table and index accesses (`t.x` or `t[1]`) are **not** accepted directly inside the expression; localize them first.
- At most 8 distinct identifiers may be captured in a single expression.
- Designed for integer/word arithmetic, not arbitrary floating-point math.

**Parameters**:
- `expr`: Integer expression to rewrite.
- `preset?`: Rewrite effort: `"fast"`, `"strong"` (default), or `"extreme"`.
- `...ctx`: Up to 8 extra constants, locals, or globals the MBA generator may incorporate.

```luau
local x, y = 12345, 67890

local sum = MV_REWRITE(x + y)
local mixed = MV_REWRITE((x + y) * (x ~ y) - (x & y), "extreme")

-- Context injection
local aux = 111
local masked = MV_REWRITE(x & y, "fast", aux, 7)
```

---

### `MV_ENC_FUNC`
```luau
declare function MV_ENC_FUNC<A..., R...>(fn: (A...) -> R..., key: string, rtKey: string, vmType?: "fox" | "skid"): (A...) -> R...
```
Lifts and encrypts the function body at build time into the chosen VM implementation. `rtKey` must evaluate at runtime to match `key` for successful decryption. Referenced upvalues are captured.

**Parameters**:
- `fn`: The target function to protect.
- `key`: Constant string used for build-time encryption.
- `rtKey`: Runtime expression that must produce the matching key string.
- `vmType?`: Target VM architecture: `"fox"` or `"skid"`. Defaults to the compile-level VM option.

```luau
local getKey = MV_VM(function()
    return "pass" .. "word123"
end)

local load = MV_ENC_FUNC(function()
    return payload()
end, "buildKey", getKey())
```

---

### `MV_VM`
- **Comment Directive**: `--!mv:vm [vmType?]`
- **Call-Style Macro**:
```luau
declare function MV_VM<A..., R...>(fn: (A...) -> R..., vmType?: "fox" | "skid"): (A...) -> R...
```
Virtualizes the function, translating Luau bytecode into an abstracted instruction set architecture (ISA) interpreted by MoonVeil's embedded virtual machine runtime.

**Parameters**:
- `fn`: The function to virtualize.
- `vmType?`: Target VM architecture: `"fox"` | `"skid"`. Defaults to the selected project VM option.

```luau
-- Comment directive
--!mv:vm skid
local function factorial(n)
    if n <= 0 then return 1 end
    return n * factorial(n - 1)
end

-- Call-style
local verify = MV_VM(function(pw)
    return pw == "s3cret"
end, "fox")
```

---

### `MV_CFF`
- **Comment Directive**: `--!mv:cff [decompose?] [mangleExpr?] [cfManglePercent?]`
- **Call-Style Macro**:
```luau
declare function MV_CFF<A..., R...>(
    fn: (A...) -> R...,
    decompose?: boolean,
    mangleExpr?: boolean,
    cfManglePercent?: number
): (A...) -> R...
```
Applies Control Flow Flattening (CFF), converting execution graphs into an opaque state machine dispatched within a central switch/loop construct.

**Parameters**:
- `fn`: The function to flatten.
- `decompose?`: Decomposes control flow into a state machine (`boolean`, default: `false`).
- `mangleExpr?`: Mangles table indices, strings, and globals within the function (`boolean`, default: `false`).
- `cfManglePercent?`: Percentage chance of inserting bogus/opaque control flow branches (`number`, default: `0`).

```luau
-- Comment directive
--!mv:cff true false 10
local function process(n)
    if n <= 0 then return 1 end
    return n * process(n - 1)
end

-- Call-style
local step = MV_CFF(function()
    for i = 1, 10 do
        print(i)
    end
end, true, true, 10)
```

---

### `MV_ENC_STR`
```luau
declare function MV_ENC_STR(str: string, key: string, rtKey: string): string
```
Encrypts a string literal at build time. The string is reconstructed in memory only when `rtKey` matches the build-time `key`.

```luau
local url = MV_ENC_STR("https://api.site/x", "buildKey", getKey())
```

---

### `MV_INDEX_TO_NUM`
```luau
declare function MV_INDEX_TO_NUM<T: {}>(tbl: T): T
```
Replaces table string field accesses and definitions with unique numeric constants throughout the code.

```luau
local input = MV_INDEX_TO_NUM({
    _mv_jump = 1,   -- all occurrences of obj._mv_jump are converted to numeric indices
    _mv_crouch = 2,
})
```

---

### `MV_OBFUSCATED`
```luau
declare MV_OBFUSCATED: boolean?
```
Constant identifier that resolves to `true` in obfuscated output, and `nil` (falsy) in uncompiled source. Unreachable branches guarded by `MV_OBFUSCATED` are pruned by dead-code elimination.

```luau
if not MV_OBFUSCATED then
    warn("Development mode")
else
    enforceSecurity()
end
```

---

### `MV_CRASH`
```luau
declare function MV_CRASH(): ()
```
Emits an expression that terminates execution and corrupts VM state.

```luau
if isDebuggerPresent() then
    MV_CRASH()
end
```

---

## 3. Optimization Macros

### `MV_OMIT`
- **Comment Directive**: `--!mv:omit`
- **Call-Style Macro**:
```luau
declare function MV_OMIT<T>(val: T): T
```
Omits the target function or expression from all obfuscation passes and virtualization, emitting plaintext code. Referenced upvalues are automatically patched. Essential for high-frequency runtime loops (`RunService.Heartbeat`, `RenderStepped`, animation ticks).

```luau
-- Comment directive
--!mv:omit
local function onRender(deltaTime)
    fpsCounter.Text = tostring(math.round(1 / deltaTime))
end

-- Call-style
RunService.PreRender:Connect(MV_OMIT(function(dt)
    label.Text = tostring(dt)
end))
```

---

### `MV_INLINE`
- **Comment Directive**: `--!mv:inline`
- **Call-Style Macro**:
```luau
declare function MV_INLINE<A..., R...>(fn: (A...) -> R...): (A...) -> R...
```
Inlines the function body directly at each call site rather than generating a function object.

**Limitations**:
- Cannot be variadic (`...`).
- Cannot tail-call or recurse.
- In call-style, must follow the pattern `local <var> = MV_INLINE(function(...) ... end)`.

```luau
-- Comment directive
--!mv:inline
local function halve(n)
    return n / 2
end
print(halve(10))

-- Call-style
local add = MV_INLINE(function(a, b)
    return a + b
end)
print(add(2, 3))
```

---

## 4. Luraph Drop-In Compatibility

MoonVeil includes compatibility aliases for Luraph (`LPH_*`) macros, enabling existing scripts to build on MoonVeil without modification:

| Luraph Macro | MoonVeil Replacement / Alias | Behavior |
| :--- | :--- | :--- |
| `LPH_OBFUSCATED` | `MV_OBFUSCATED` | Resolves `true` in obfuscated code |
| `LPH_LINE` | `MV_LINE` | Resolves to source line number integer |
| `LPH_CRASH` | `MV_CRASH` | Halts VM state |
| `LPH_NO_VIRTUALIZE` | `MV_OMIT` | Leaves function in plaintext |
| `LPH_ENCFUNC` | `MV_ENC_FUNC` | Encrypts function constant |
| `LPH_PRECHECK` | `MV_PRECHECK` | Inlines precheck key folding |
| `LPH_REWRITE` | `MV_REWRITE` | MBA arithmetic rewriting |
| `LPH_ENCSTR` | *Ignored Stub* | Safely ignored; no-op |
| `LPH_ENCNUM` | *Ignored Stub* | Safely ignored; no-op |
| `LPH_JIT` | *Ignored Stub* | Safely ignored; no-op |
| `LPH_NO_UPVALUES`| *Ignored Stub* | Safely ignored; no-op |

---

## 5. Miscellaneous Macros

### `MV_LINE`
```luau
declare MV_LINE: number
```
Resolves to a number constant representing the source code line number where the macro sits.

```luau
error("Assertion failed at line " .. MV_LINE)
```

### `MV_COMPRESS`
```luau
declare function MV_COMPRESS(value: string): string
```
Flags string constants or blocks for compression. (Currently a passthrough in recent versions).

---

## 6. Unobfuscated Development Shim

Include this header at the beginning of development source files to enable local Luau CLI execution (`luau.exe`, mock tests) and prevent IDE type/global warnings:

```luau
if not MV_OBFUSCATED then
    MV_OBFUSCATED = false
    MV_LINE = 0
    MV_CRASH = function() error("MV_CRASH called") end
    MV_OMIT = function(fn) return fn end
    MV_VM = function(fn) return fn end
    MV_CFF = function(fn) return fn end
    MV_INLINE = function(fn) return fn end
    MV_ENC_STR = function(s) return s end
    MV_ENC_FUNC = function(fn) return fn end
    MV_INDEX_TO_NUM = function(t) return t end
    MV_COMPRESS = function(s) return s end
    MV_PRECHECK = function(fn, ...) return end
    MV_REWRITE = function(expr, ...) return expr end

    -- Luraph Drop-in compatibility
    LPH_OBFUSCATED = MV_OBFUSCATED
    LPH_LINE = MV_LINE
    LPH_CRASH = MV_CRASH
    LPH_NO_VIRTUALIZE = MV_OMIT
    LPH_ENCFUNC = MV_ENC_FUNC
    LPH_PRECHECK = MV_PRECHECK
    LPH_REWRITE = MV_REWRITE
    LPH_ENCSTR = function(s) return s end
    LPH_ENCNUM = function(n) return n end
    LPH_JIT = function(fn) return fn end
    LPH_NO_UPVALUES = function(fn) return fn end
end
```

---

## 7. MoonVeil HTTP API Reference (v2)

### Base URL & Protocol
```text
https://moonveil.cc/api
```
All transformation endpoints are versioned under `/v2`.

### Authentication
Authenticate using a Bearer token in the `Authorization` HTTP header:
```http
Authorization: Bearer <MOONVEIL_API_KEY>
```
*(Browser dashboard sessions use the `token` cookie).*

### Status Codes & Errors
Successful responses return transformed scripts with `Content-Type: text/plain`.
Errors return JSON payloads:
```json
{
  "error": "Error description message"
}
```

| HTTP Status | Meaning |
| :--- | :--- |
| `200 OK` | Request succeeded. Response body is transformed Luau script (`text/plain`). |
| `400 Bad Request` | Malformed JSON, unknown configuration key, or script exceeds account character limit. |
| `401 Unauthorized` | Missing or invalid API key. |
| `403 Forbidden` | Requested compile mode or option is not permitted under the current plan. |
| `429 Too Many Requests` | Daily obfuscation quota exceeded or short-window rate limit hit (`Retry-After` header included). |
| `500 Server Error` | Internal compiler / backend error. |

---

### Endpoints

#### 1. Account Details
```http
GET /api/account
```
Retrieves account plan limits, feature flags, and current day quota consumption.

**Response Structure (JSON)**:
```json
{
  "id": "217039...",
  "email": "user@example.com",
  "username": "discord_username",
  "avatar": null,
  "opLevel": 0,
  "hasBilling": true,
  "plan": {
    "name": "Basic",
    "maxScriptChars": 5000000,
    "dailyQuota": 30,
    "allowedOptions": {
      "vms": ["fox", "skid"],
      "compileTypes": ["cff", "vm", "safeEnv"],
      "compress": true
    }
  },
  "usage": {
    "used": 8,
    "quota": 30,
    "obfuscationCount": 142,
    "resetsAt": "2026-06-18T03:21:00Z"
  }
}
```

---

#### 2. Obfuscate Script
```http
POST /api/v2/obf
```
Performs the full MoonVeil obfuscation pipeline.

**Request Body (JSON)**:
| Field | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `script` | `string` | *(Required)* | Source Luau script to obfuscate. |
| `options` | `object` | `{}` | Pipeline configuration settings. |
| `options.compileType` | `"cff" \| "vm" \| "safeEnv"` | `"cff"` | Top-level compile mode for the root chunk. |
| `options.vmType` | `"fox" \| "skid"` | `"skid"` | VM architecture implementation. |
| `options.safeEnvLock` | `"luau" \| "rbx"` | `"luau"` | When `compileType = "safeEnv"`, locks target environment. |
| `options.cff` | `object` | `none` | Control Flow Flattening options. |
| `options.cff.decompose` | `boolean` | `false` | Decompose control flow into flattened state machine. |
| `options.cff.mangleNext` | `boolean` | `false` | Mangle dispatch and successor indices. |
| `options.cff.mangleStrings` | `boolean` | `false` | Mangle string constants within CFF functions. |
| `options.cff.mangleGlobals` | `boolean` | `false` | Mangle global table lookups. |
| `options.cff.mangleCfPercent` | `number` | `0` | Percentage rate of fake control branches (higher = slower). |
| `options.compress` | `boolean` | `false` | Wrap output in loadstring decompressor (paid plans only). |
| `options.envWrapper` | `boolean` | `false` | Resolves globals through sandboxed environment wrapper instead of `getfenv`. Enables runtimes without `getfenv` (e.g., Lute) and protects globals from external read. |

**Example Request**:
```json
{
  "script": "print('Hello MoonVeil')",
  "options": {
    "compileType": "safeEnv",
    "vmType": "fox",
    "safeEnvLock": "rbx",
    "cff": {
      "decompose": true,
      "mangleStrings": true
    },
    "compress": true,
    "envWrapper": true
  }
}
```

**Response**:
`text/plain` attachment (`obfuscated.lua`) containing the compiled output.

---

#### 3. Prettify Script
```http
POST /api/v2/prettify
```
Reformats messy, minified, or deobfuscated Luau code into structured, indented, human-readable source.

**Request Body (JSON)**:
```json
{
  "script": "local a=1;function f(x)return x+a end;f(2)"
}
```

**Response**:
`text/plain` formatted code.

---

#### 4. Minify Script
```http
POST /api/v2/minify
```
Applies AST-level optimization passes (constant folding, dead-code elimination, symbol shortening) and removes unnecessary whitespace and comments.

**Request Body (JSON)**:
```json
{
  "script": "local function add(a, b) return a + b end; print(add(10, 20))"
}
```

**Response**:
`text/plain` minified code.

---

### Implementation Examples

#### Node.js / TypeScript Example
```typescript
const MOONVEIL_API_KEY = process.env.MOONVEIL_KEY!;

async function obfuscateScript(sourceCode: string): Promise<string> {
  const response = await fetch("https://moonveil.cc/api/v2/obf", {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${MOONVEIL_API_KEY}`,
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      script: sourceCode,
      options: {
        compileType: "vm",
        vmType: "fox",
        compress: true,
      },
    }),
  });

  if (!response.ok) {
    const errorData = await response.json().catch(() => ({ error: response.statusText }));
    throw new Error(`Obfuscation failed (${response.status}): ${errorData.error}`);
  }

  return await response.text();
}
```

#### cURL Example
```bash
curl -X POST https://moonveil.cc/api/v2/obf \
  -H "Authorization: Bearer $MOONVEIL_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "script": "local a = 1; print(a)",
    "options": {
      "compileType": "cff",
      "cff": { "decompose": true }
    }
  }' \
  --output obfuscated.lua
```

#### Pure Luau Executor Request Example
```luau
local HttpService = game:GetService("HttpService")
local request = (syn and syn.request) or (http and http.request) or request

local function obfuscateRemote(code: string, apiKey: string): string
    local response = request({
        Url = "https://moonveil.cc/api/v2/obf",
        Method = "POST",
        Headers = {
            ["Authorization"] = "Bearer " .. apiKey,
            ["Content-Type"] = "application/json",
        },
        Body = HttpService:JSONEncode({
            script = code,
            options = {
                compileType = "cff",
                vmType = "skid",
                cff = { decompose = true },
            },
        }),
    })

    if response.StatusCode == 200 then
        return response.Body
    else
        error("Obfuscation error: " .. response.Body)
    end
end
```
