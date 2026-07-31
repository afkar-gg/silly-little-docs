# wYnFuscate Macros Documentation

**Note:** Any macro marked as **PREMIUM** means you need to pay to the obfuscation service (wYnFuscate) to access it.

Macros are compile-time constructs that allow you to write conditional code paths and optimize performance in your obfuscated scripts.

## Quick Reference

| Macro | Type | Purpose |
|---|---|---|
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
| `WYNF_GET_RNG_SEED` **PREMIUM** | function call | Per-run non-deterministic seed for RNG/anti-replay (security) |
| `WYNF_BEGIN_CLIENT_LINES` **PREMIUM** | statement | Reset line-info base for service/enterprise builds (debug) |

## WYNF_OBFUSCATED
boolean
A compile-time constant that evaluates to `true` in obfuscated builds. In plain Lua (non-obfuscated), it behaves like a normal global (typically `nil`/`false`). When used as an if condition, the compiler removes unreachable branches from the output entirely.

## WYNF_NO_VIRTUALIZE
performance
Marks a function to run as native Lua instead of inside the VM. This bypasses VM overhead for performance-critical code like hot loops or heavy math/table/string operations. Code wrapped with this is NOT protected through virtualization.

## WYNF_JIT
performance
Marks a function for Wynfuscate's JIT-style micro-VM execution path. The function remains virtualized (it still runs encrypted bytecode), but executes under a more performance-oriented interpreter template than the main VM loop.

## WYNF_JIT_MAX
performance
A higher-performance variant of `WYNF_JIT` with more aggressive caching and faster dispatch. 

## WYNF_INLINE
performance
`WYNF_INLINE` is a compile-time macro that removes a small helper function and expands its body directly at supported callsites during obfuscation.

## WYNF_CRASH
security
Immediately and irrecoverably terminates the script. Use this as a last-resort defense when tampering or unauthorized access is detected. Must be called directly.

## WYNF_IS_CALLER_WYNFUSCATE
security
Returns `true` if the current function was called from within your obfuscated code, and `false` if it was called from an external source like an exploit script.

## WYNF_ENC_STRING
security
Applies additional encryption layers to a string literal, providing extra protection for highly sensitive values like API keys, passwords, and encryption keys.

## WYNF_ENC_NUM
security
Protects a numeric literal by encoding it in an encrypted form, preventing magic numbers from being trivially found via memory scanning or static analysis.

## WYNF_LINE
debug
Expands to the current source line number at compile time.

## WYNF_NO_UPVALUES
compatibility
Creates a lightweight wrapper around your function for compatibility with certain environments or APIs that have issues with virtualized functions used as callbacks.

## WYNF_SECURE_CALL
security
Marks a function as VM-only callable. Calls to this function will only succeed when they originate from inside your obfuscated code.

## WYNF_SECURE_CALLBACK
security
Returns a wrapped callback that performs a caller gate before invoking your function. Intended for event handlers and callbacks that are called by external systems. Fail-quiet behavior.

## WYNF_ENC_FUNC
security
Encrypts a function body at build time and returns a callable wrapper that decrypts and executes the function at runtime using a key provided by your server.

## WYNF_ENC_FUNC_SEED
security
A numeric-key variant of `WYNF_ENC_FUNC`. Instead of a 64-character hex string, this macro uses a numeric seed for encryption/decryption.

## WYNF_GET_RNG_SEED (PREMIUM)
security
Returns a unique, non-deterministic integer seed on every script execution. Use it to initialize your own RNG, generate per-session nonces, or build anti-replay logic.

## WYNF_BEGIN_CLIENT_LINES (PREMIUM)
statement
A source-level line-info marker for enterprise builds that prepend their own loader, key system, or whitelist code above a customer's script.
