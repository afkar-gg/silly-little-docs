# wYnFuscate Documentation

Source: https://wynfuscate.com/docs

wYnFuscate is an advanced Lua obfuscation technology with broad platform compatibility.

---

## 1. Macros

Macros are compile-time constructs that allow you to write conditional code paths and optimize performance in your obfuscated scripts.

### Quick Reference
| Macro | Type | Purpose |
| --- | --- | --- |
| `WYNF_OBFUSCATED` | boolean | Detect if script is obfuscated |
| `WYNF_NO_VIRTUALIZE` | function wrapper | Mark function for native execution (unprotected) |
| `WYNF_JIT` | function wrapper | Faster VM execution for hot functions (performance) |
| `WYNF_JIT_MAX` | function wrapper | Maximum performance VM for extremely hot functions (performance) |
| `WYNF_INLINE` | compile-time macro | Inline small helper functions to avoid call overhead (performance) |
| `WYNF_CRASH` | function call | Immediately terminate the script (destructive) |
| `WYNF_IS_CALLER_WYNFUSCATE` | function call | Detect external callers (security) |
| `WYNF_ENC_STRING` | string wrapper | Extra encryption for sensitive strings (security) |
| `WYNF_ENC_NUM` | number wrapper | Extra encryption for sensitive numbers (security) |
| `WYNF_LINE` | function call | Get source line number at compile time (debug) |
| `WYNF_NO_UPVALUES` | function wrapper | Compatibility wrapper for callbacks (compatibility) |
| `WYNF_SECURE_CALL` | function wrapper | Restrict function to VM-only callers (security) |
| `WYNF_SECURE_CALLBACK` | function wrapper | Secure wrapper for event callbacks (security) |
| `WYNF_ENC_FUNC` | function wrapper | Runtime-decrypted function with key server support (security) |
| `WYNF_ENC_FUNC_SEED` | function wrapper | Runtime-decrypted function with numeric seed key (security) |
| `WYNF_GET_RNG_SEED` | function call | [PREMIUM] Per-run non-deterministic seed for RNG/anti-replay (security) |
| `WYNF_BEGIN_CLIENT_LINES` | statement | [PREMIUM] Reset line-info base for service/enterprise builds (debug) |

### `WYNF_OBFUSCATED`
A **compile-time** constant that evaluates to `true` in obfuscated builds. In plain Lua (non-obfuscated), it behaves like a normal global (typically `nil`/`false`).
* **Dead Code Pruning:** When used as an `if` condition, the compiler **removes unreachable branches** from the output entirely. This means dev-only stubs and fallback code gated by `WYNF_OBFUSCATED` are not present in the obfuscated bytecode at all.

### `WYNF_NO_VIRTUALIZE`
Marks a function to run as **native Lua** instead of inside the VM. This bypasses VM overhead for performance-critical code like hot loops or heavy math/table/string operations.
* **WARNING:** Code wrapped with `WYNF_NO_VIRTUALIZE` is **NOT protected** through virtualization. Your code will be minified, but raw strings and logic will still be exposed.
* **Limitations:** Self-recursion, Nesting, and Mutual recursion are not supported.

### `WYNF_JIT`
Marks a function for Wynfuscate's JIT-style **micro-VM** execution path. The function remains **virtualized**, but executes under a more performance-oriented interpreter template. Good for math, string operations, and loops (~1.5x - 2.9x faster).

### `WYNF_JIT_MAX`
A higher-performance variant of `WYNF_JIT` with more aggressive caching and faster dispatch. The function remains **virtualized**, but uses additional optimizations for maximum speed (~1.9x - 5.7x faster). Trades off memory and closure creation speed for execution speed.

### `WYNF_INLINE`
`WYNF_INLINE` is a compile-time macro that removes a small helper function and expands its body directly at supported callsites during obfuscation. This avoids the call boundary cost for tiny helpers.

### `WYNF_CRASH`
Immediately and irrecoverably terminates the script. Must be called directly. No code after `WYNF_CRASH()` will execute.

### `WYNF_IS_CALLER_WYNFUSCATE`
Returns `true` if the current function was called from within your obfuscated code, and `false` if it was called from an external source like an exploit script. Protects RemoteEvent handlers and Module functions.

### `WYNF_ENC_STRING`
Applies additional encryption layers to a string literal, providing extra protection for highly sensitive values like API keys, passwords, and encryption keys.

### `WYNF_ENC_NUM`
Protects a numeric literal by encoding it in an encrypted form, preventing magic numbers from being trivially found via memory scanning or static analysis.

### `WYNF_LINE`
Expands to the current source line number at compile time. This provides a stable line marker for logs and error messages without relying on VM debug info.

### `WYNF_NO_UPVALUES`
Creates a lightweight wrapper around your function for compatibility with certain environments or APIs that have issues with virtualized functions used as callbacks.

### `WYNF_SECURE_CALL`
Marks a function as **VM-only callable**. Calls to this function will only succeed when they originate from inside your obfuscated code (fail-closed).

### `WYNF_SECURE_CALLBACK`
Returns a wrapped callback that performs a caller gate before invoking your function. Intended for **event handlers and callbacks**. Uses **fail-quiet** behavior (returns `nil` if external).

### `WYNF_ENC_FUNC`
Encrypts a function body at **build time** and returns a callable wrapper that decrypts and executes the function at **runtime** using a 64-character hex key provided by your server.

### `WYNF_ENC_FUNC_SEED`
A **numeric-key variant** of `WYNF_ENC_FUNC`. Uses a 53-bit numeric seed for encryption/decryption. Ideal for Roblox/Luau workflows where numeric keys blend in better.

### `WYNF_GET_RNG_SEED` (Premium)
Returns a unique, non-deterministic integer seed on every script execution. Used for anti-replay, tokens, and seeding custom RNGs. (Alias: `WYNF_GET_RNG()`).

### `WYNF_BEGIN_CLIENT_LINES` (Premium)
A source-level line-info marker for enterprise builds that prepend their own loader or key system. Resets the displayed source line base so the next physical line is reported as client line `1`.

---

## 2. Obfuscation Settings

### Security Tiers
* **Standard:** Baseline protection with fast processing. Recommended for most scripts.
* **Enhanced:** Additional protection layers with moderate overhead.
* **Max:** Maximum security with all protection features enabled. Comes with a performance trade-off.

### Enhanced VM Compression
Premium toggle. Greatly reduces the overall file size of the obfuscated output at the cost of a slight increase in initialization speed. Target platform must support `loadstring`.

### Line Info
When enabled, unhandled runtime errors from obfuscated code are remapped to the original source file and line number (format: `source.lua:<line>: WYNF: <error body>`).

### Performance Mode
Prioritizes runtime speed for large scripts, especially method/table/callback-heavy UI scripts. Reduces some polymorphic and runtime hardening choices.

### Enhanced Anti-Tamper
Additional anti-tamper measures applied to the protected output. May not be compatible with all environments.

### Auto Inline Small Helpers
Automatically applies the `WYNF_INLINE` macro to eligible small helper functions before obfuscation.

### Target Platforms
* **Roblox:** Optimized for Roblox (Luau) - Default.
* **Roblox (Max Compatibility):** For custom environments that fail with standard Roblox target.
* **Luau:** Standalone Luau CLI.
* **Lua 5.1 - Lua 5.4:** Standard Lua environments.
* **LuaJIT:** LuaJIT 2.x.

---

## 3. Known Limitations

* **Stack Level Differences:** The VM adds additional stack frames. Code relying on specific stack levels (e.g., `debug.getinfo`) may behave differently.
* **Upvalue Handling:** Access patterns may differ from native Lua.
* **Performance Overhead:** Obfuscated code has overhead due to VM interpretation.

---

## 4. API Reference (REST v1)

Base URL: `https://wynfuscate.com/api/v1`

Authentication: Bearer token using your API key (format: `wynf_` + 32 alphanumeric chars).
```bash
Authorization: Bearer $WYN_KEY
```

### Endpoints
* `POST /obfuscate` - Submit a file for obfuscation.
* `GET /jobs/{id}` - Get job status and details.
* `GET /jobs/{id}/download` - Download obfuscated output.
* `DELETE /jobs/{id}` - Delete job and free storage.

**Example `POST /obfuscate` request:**
```bash
curl -X POST https://wynfuscate.com/api/v1/obfuscate \
  -H "Authorization: Bearer $WYN_KEY" \
  -F "file=@script.lua" \
  -F "securityTier=ENHANCED" \
  -F "targetPlatform=ROBLOX" \
  -F "node=STABLE"
```

---

## 5. Plans & Pricing

* **Free:** $0 - 1 obfuscation/month, 1 concurrent job, Standard tier.
* **Basic:** $10/mo - 300 obfuscations/month, 2 concurrent jobs, Standard tier.
* **Pro:** $19.99/mo - 1,000 obfuscations/month, 5 concurrent jobs, All tiers, API access.
* **Enterprise:** $49.99/mo - Unlimited obfuscations, 20 concurrent jobs, All tiers, API access, Dedicated support.
* **On-Demand Credits:** $2.00 for 5 credits up to $14.00 for 50 credits.

---

## 6. Optimization Tips

### Performance
- Minimize hot loop complexity
- Cache frequently accessed values
- Use local variables where possible

### Security
- Use macros for sensitive paths
- Avoid string-based function calls
- Implement integrity checks

### Best Practices
- Test obfuscated output thoroughly
- Keep original source versioned
- Monitor for issues in production