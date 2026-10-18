# Centurion Macros Documentation

Source: https://docs.centurion.best/ (index: https://docs.centurion.best/llms.txt)

---

## Overview

Macros are special identifiers you can place directly in your Lua source. Centurion recognizes them at compile time and replaces them with generated code. They let you control obfuscation behavior at specific points in your script, from keeping a line number to hard-crashing on tamper detection.

Most macros need no separate option; just write them in your source and obfuscate as normal. **Beta-only macros** require `"node": "beta"` when [choosing a node](/api-reference/nodes.md). [`CTN_HTTP_ENCRYPT_FUNCTION`](/macros/ctn-http-encrypt-function.md) also requires **Enterprise** access and activation for your API key. The [`CTN_ENCRYPT_FUNCTION`](/macros/ctn-encrypt-function.md) wrapper syntax documented here is Beta only; Main uses an older syntax.

### Available macros

| Macro                                                               | Form           | Availability                   | Purpose                                                                                               |
| ------------------------------------------------------------------- | -------------- | ------------------------------ | ----------------------------------------------------------------------------------------------------- |
| [`CTN_LINE`](/macros/ctn-line.md)                                   | expression     | Main and Beta                  | Evaluates to the current source line number.                                                          |
| [`CTN_OBFUSCATED`](/macros/ctn-obfuscated.md)                       | expression     | Main and Beta                  | Evaluates to `true` in obfuscated output (absent in raw source).                                      |
| [`CTN_VERSION`](/macros/ctn-version.md)                             | expression     | Main and Beta                  | Evaluates to the Centurion engine version that built the output.                                      |
| [`CTN_CRASH`](/macros/ctn-crash.md)                                 | call           | Main and Beta                  | Hard-crashes the VM at that point.                                                                    |
| [`CTN_CLEAR_STACK`](/macros/ctn-clear-stack.md)                     | call           | Main and Beta                  | Nils out the calling function's locals so secrets can't be dumped.                                    |
| [`CTN_PRECHECK`](/macros/ctn-precheck.md)                           | call statement | **Beta only**                  | Requires a startup check to return the expected integer or integer array before the main script runs. |
| [`CTN_NO_VIRTUALIZE`](/macros/ctn-no-virtualize.md)                 | call           | Main and Beta                  | Runs a function natively instead of through the VM.                                                   |
| [`CTN_JIT`](/macros/ctn-jit.md)                                     | call           | **Beta only**                  | Marks a virtualized function for faster execution with reduced protection.                            |
| [`CTN_ENCRYPT_FUNCTION`](/macros/ctn-encrypt-function.md)           | call           | **Beta only (wrapper syntax)** | Encrypts a function's body with a key you supply, decrypted at run time with a key you fetch.         |
| [`CTN_HTTP_ENCRYPT_FUNCTION`](/macros/ctn-http-encrypt-function.md) | call           | **Enterprise only; Beta only** | Encrypts a function's body with a Centurion-generated key returned with the build.                    |
| [`CTN_INLINE`](/macros/ctn-inline.md)                               | call           | Main and Beta                  | Inlines an immediately called function when possible; otherwise produces a callable closure.          |
| [`CTN_ENCRYPT_STRING`](/macros/ctn-encrypt-string.md)               | call           | Main and Beta                  | Returns a string literal behind an extra per-string encryption layer.                                 |
| [`CTN_ENCRYPT_NUMBER`](/macros/ctn-encrypt-number.md)               | call           | Main and Beta                  | Returns a number literal behind an extra encryption layer.                                            |

### Two forms

* **Expression macros** (`CTN_LINE`, `CTN_OBFUSCATED`, `CTN_VERSION`) are written as a bare identifier wherever a value is expected. They must be used as an expression, not called.
* **Call macros** (`CTN_CRASH`, `CTN_CLEAR_STACK`, `CTN_PRECHECK`, `CTN_NO_VIRTUALIZE`, `CTN_JIT`, `CTN_ENCRYPT_FUNCTION`, `CTN_HTTP_ENCRYPT_FUNCTION`, `CTN_INLINE`, `CTN_ENCRYPT_STRING`, `CTN_ENCRYPT_NUMBER`) are written as a direct function call, for example `CTN_CRASH()`. `CTN_PRECHECK` must be a standalone statement and produces no value.

The three expression macros are **safe to read in raw source** - there they're undefined globals, so they evaluate to `nil`. The call macros are not: calling `CTN_CRASH()` or `CTN_CLEAR_STACK()` in un-obfuscated source raises "attempt to call a nil value".

If you also run the script unprotected, put call macros inside the **positive** branch - `if CTN_OBFUSCATED then ... end` - so they only ever run in a build where they exist. Putting one under `if not CTN_OBFUSCATED then` does the opposite of guarding it: that branch runs only in raw source, which is exactly where the macro is undefined.

`CTN_PRECHECK` runs during startup regardless of its surrounding branch or function. A positive `CTN_OBFUSCATED` guard avoids an undefined call in raw source; it does not make the precheck conditional in obfuscated output. See its [startup rules](/macros/ctn-precheck.md#when-it-runs).

{% hint style="warning" %}
All macro names are **reserved identifiers**. Don't use `CTN_LINE`, `CTN_OBFUSCATED`, `CTN_VERSION`, `CTN_CRASH`, `CTN_CLEAR_STACK`, `CTN_PRECHECK`, `CTN_NO_VIRTUALIZE`, `CTN_JIT`, `CTN_ENCRYPT_FUNCTION`, `CTN_HTTP_ENCRYPT_FUNCTION`, `CTN_INLINE`, `CTN_ENCRYPT_STRING`, or `CTN_ENCRYPT_NUMBER` for your own variables or globals, or they'll be interpreted as macros. The `CTN_` prefix as a whole is reserved for Centurion; use only the macros documented here.
{% endhint %}

### Quick example

```lua
if not CTN_OBFUSCATED then
    -- The script is running unprotected. Refuse to continue.
    -- A plain error(), not CTN_CRASH(): in raw source the call macros
    -- are still undefined globals, and this branch only runs there.
    error("Obfuscate this script before running it.")
end

print("centurion " .. CTN_VERSION)

local Fast = CTN_NO_VIRTUALIZE(function(A, B)
    return A * B + 1
end)

print(Fast(6, 7))
```

See each macro's page for the full details and more examples.

---

## CTN\_LINE

**Form:** expression

`CTN_LINE` evaluates to the **source line number** where it appears, as a plain number constant in the obfuscated output.

### Usage

Write it as a bare identifier anywhere a value is expected:

```lua
local Here = CTN_LINE
print("reached line " .. Here)
```

It must be used as an expression. It cannot be called like a function.

### Why use it

Centurion can strip line information from the output for a smaller, faster result (see [`disable_line_information`](/features/options.md#disable_line_information)). When you do that, you lose the line numbers that would otherwise appear in error messages.

`CTN_LINE` lets you keep a line reference exactly where you need one. Line information is retained at the points where `CTN_LINE` is used, even when line stripping is enabled elsewhere, so you can build your own diagnostics without paying for a full line table.

### Example

```lua
local function AssertPositive(N)
    if N <= 0 then
        error("bad value at line " .. CTN_LINE)
    end
    return N
end
```

In the obfuscated output, `CTN_LINE` becomes the literal line number of that expression, so your error message still points somewhere useful.

---

## CTN\_OBFUSCATED

**Form:** expression

`CTN_OBFUSCATED` evaluates to `true` in obfuscated output. In your original, un-obfuscated source it's just an undefined global, so it reads as `nil` (falsy).

That difference is the whole point: it lets a script tell whether it's running through Centurion or as raw source.

### Usage

Write it as a bare identifier wherever a value is expected:

```lua
if CTN_OBFUSCATED then
    print("running protected")
else
    print("running as plain source")
end
```

It must be used as an expression. It cannot be called like a function.

### The branch that can't run is never compiled

`CTN_OBFUSCATED` is resolved when your script is built, not while it runs. It becomes a literal `true`, and the branch that can no longer be reached is discarded before any code is generated for it.

So in the example above, the obfuscated output contains no `else` arm, no `"running as plain source"` text, and no condition left to patch. The code you fenced off is **absent from the artifact**, not merely unreachable inside it — it can't be recovered by dumping constants or by flipping a jump, because it was never compiled in the first place.

That makes `CTN_OBFUSCATED` the right place to keep anything you don't want to ship — development helpers, debug output, test fixtures, internal URLs:

```lua
if not CTN_OBFUSCATED then
    -- None of this reaches the artifact.
    LoadTestFixtures()
    print(INTERNAL_ENDPOINT)
end
```

This applies to `CTN_OBFUSCATED`, to `not CTN_OBFUSCATED`, and to `and`/`or` chains whose outcome the macro alone decides. A condition that also depends on a runtime value is kept, because it genuinely has to be tested while the script runs:

```lua
-- Kept: the macro can't decide this one on its own.
if CTN_OBFUSCATED and IntegrityFailed() then
    CTN_CRASH()
end
```

{% hint style="warning" %}
Because the dropped branch is never compiled, build errors inside it are never reported either. A mistake in the arm that gets discarded will build cleanly and silently. Keep running your raw source as well, so the unprotected path still gets exercised.
{% endhint %}

### Why use it

Use it to guard against your script being run before it's been obfuscated, to turn on protections that only make sense in a shipped build, or to keep code out of the artifact entirely.

### Example

Refuse to run as raw source:

```lua
if not CTN_OBFUSCATED then
    error("Obfuscate this script before running it.")
end

Main()
```

Use a plain `error` here rather than [`CTN_CRASH`](/macros/ctn-crash.md). This branch is the one that *only* ever runs un-obfuscated, and there every call macro is still an undefined global — `CTN_CRASH()` would raise "attempt to call a nil value" instead of crashing on purpose. In the obfuscated build the branch is dropped entirely, so it would never run at all.

---

## CTN\_VERSION

**Form:** expression

`CTN_VERSION` evaluates to the **version of the Centurion engine that built the output**, as a plain string constant - for example `"6.0"`.

In your original, un-obfuscated source it's just an undefined global, so it reads as `nil`.

### Usage

Write it as a bare identifier wherever a value is expected:

```lua
print("built with Centurion " .. CTN_VERSION)
```

It must be used as an expression. It cannot be called like a function.

The value is baked in at build time - nothing is looked up at runtime, and the macro compiles down to a plain string constant.

### Why use it

Use it to make your own builds traceable. When a user reports a problem with a shipped script, having the engine version inside the script itself tells you which build of Centurion produced it - useful when you're comparing behaviour across engine versions.

It also pairs naturally with [`CTN_OBFUSCATED`](/macros/ctn-obfuscated.md): one tells you *whether* the script was obfuscated, the other tells you *with what*.

### Example

Stamp a diagnostic banner that only appears in protected builds:

```lua
if CTN_OBFUSCATED then
    print(("build: centurion %s | line %d"):format(CTN_VERSION, CTN_LINE))
end
```

Or record it alongside your own version for support reports:

```lua
local BuildInfo = {
    App = "1.4.2",
    Engine = CTN_VERSION or "none",
}
```

In raw source `CTN_VERSION` is `nil`, so `or "none"` marks the script as un-obfuscated. In an obfuscated build it becomes the engine version string.

---

## CTN\_CRASH

**Form:** call

`CTN_CRASH()` emits code that **takes the VM down** at that point. Execution does not continue past it, and it's designed to be difficult to recover from or step over.

It is not a catchable error. A thread that reaches `CTN_CRASH()` stops making progress and never returns — `pcall` won't catch it and there is no message to read. On a host that runs your script on its own thread, such as Roblox, expect that thread to hang rather than to raise.

### Usage

Call it directly, with no arguments:

```lua
CTN_CRASH()
```

It must be called directly. It does not return a value, so don't try to assign or use its result.

### Why use it

Use it as the "fail" branch of a security check. When your own tamper, environment, or integrity check detects something wrong, `CTN_CRASH()` takes the script down hard instead of letting a modified script keep running.

### Example

Trip it on your own integrity check:

```lua
if GetScriptHash() ~= ExpectedHash then
    CTN_CRASH()
end
```

If you also run the script un-obfuscated, keep the call inside the **positive** [`CTN_OBFUSCATED`](/macros/ctn-obfuscated.md) branch, so it only ever runs in a build where the macro exists:

```lua
if CTN_OBFUSCATED and GetScriptHash() ~= ExpectedHash then
    CTN_CRASH()
end
```

Don't reach for `CTN_CRASH()` to refuse to run unprotected. Writing `if not CTN_OBFUSCATED then CTN_CRASH() end` looks like the obvious guard but does the opposite of one: that branch runs only in raw source, where `CTN_CRASH` is an undefined global and the call raises "attempt to call a nil value" — and in the obfuscated build the branch is dropped at compile time, so it never runs at all. Use a plain `error` for that case:

```lua
if not CTN_OBFUSCATED then
    error("Obfuscate this script before running it.")
end
```

{% hint style="info" %}
`CTN_CRASH` is a blunt instrument by design. Reach for it when you want a script to become unusable on tamper, not when you want to fail gracefully.
{% endhint %}

---

## CTN\_CLEAR\_STACK

**Form:** call

`CTN_CLEAR_STACK()` clears the locals of the **function it is called from**, setting every local that function has declared so far to `nil`.

Its purpose is to make secrets unrecoverable after use. Once a value has done its job, clearing the stack means it is no longer sitting in a register for an attacker to read out of a dump.

### Usage

Call it directly, as a statement of its own:

```lua
local Key = DeriveKey()
UseKey(Key)

CTN_CLEAR_STACK()
```

After that call, `Key` reads as `nil`.

### What it clears

* **Every local the enclosing function has declared** up to that point - including locals from blocks that have already closed.
* **Not the function's parameters.** Arguments survive, so a function can wipe its working values and still use or return what it was passed.
* **Not upvalues.** If a closure created *before* the call captured the local, that closure still sees the value; only the stack slot is cleared.
* **Not globals or table fields.** A value you stored in a table survives.

```lua
local function Sign(Message, Secret)
    local Digest = Hash(Message .. Secret)

    CTN_CLEAR_STACK()

    -- message and secret are parameters, so they survive.
    -- digest is a local, so it is now nil.
    return Message
end
```

### Rules

{% hint style="warning" %}
Anything you still need **after** the call must be a parameter, an upvalue, or stored somewhere off the stack. Reading a cleared local gives `nil`, which typically surfaces as an "attempt to index a nil value" error a few lines later - not at the macro.
{% endhint %}

**Write it as a bare statement.** Used that way it is also a **barrier for the optimizer**: Centurion will not sink a computation across it, so anything you computed before the call really is computed before the wipe. Buried inside a larger expression it is not treated as a barrier.

```lua
-- Correct.
CTN_CLEAR_STACK()

-- Not a barrier. Don't do this.
local X = (CTN_CLEAR_STACK())
```

**It runs only in obfuscated builds.** In raw source `CTN_CLEAR_STACK` is an undefined global, so calling it errors - the same as [`CTN_CRASH`](/macros/ctn-crash.md). Guard it with [`CTN_OBFUSCATED`](/macros/ctn-obfuscated.md) if you also run the script unprotected.

**It can't be used inside** [**`CTN_NO_VIRTUALIZE`**](/macros/ctn-no-virtualize.md)**.** That closure runs natively rather than on the VM stack, and the build fails with an error if you try.

### Example

Wipe key material as soon as it has been consumed, handing the result out through a table so it survives the clear:

```lua
local Out = {}

local function DecryptPayload(Blob, Out)
    local Key = FetchKey()
    local Nonce = Blob:sub(1, 12)

    Out.Data = XorDecrypt(Blob:sub(13), Key, Nonce)

    -- key and nonce are locals: gone after this line.
    -- out is a parameter and its fields live in a table, so both survive.
    CTN_CLEAR_STACK()
end

DecryptPayload(ReadBlob(), Out)
print(Out.Data)
```

The shape matters: a value you want to keep must not be sitting in a local of the clearing function when the call runs. Write it into a table you were handed, store it in a global or an upvalue, or let a closure created before the call capture it.

---

## CTN\_PRECHECK (Beta only)

**Form:** call statement

**Availability:** Beta only (`"node": "beta"`).

`CTN_PRECHECK(function() ... end, expected)` declares a check that runs during startup, before the main script executes. Its result must match the expected value for the main script to start.

This macro is available in the Beta engine. It needs no separate API option or function-encryption entitlement; select Beta when [choosing a node](/api-reference/nodes.md).

### Usage

Pass exactly two arguments: an anonymous function with no parameters, and either an integer literal or an array of integer literals. Write the call as its own statement. It produces no value, so you cannot assign it, return it, or save the macro in a variable.

For a single integer result:

```lua
if CTN_OBFUSCATED then
    CTN_PRECHECK(function()
        return math.abs(-71)
    end, 71)
end

print("Main script started.")
```

For several results, return one array and supply the expected values in the same order:

```lua
if CTN_OBFUSCATED then
    CTN_PRECHECK(function()
        local Text = "Centurion"
        return { #Text, string.byte(Text, 1), math.floor(12.75), -33 }
    end, { 9, 67, 12, -33 })
end

print("Main script started.")
```

These examples check values obtained from the runtime environment. In your own check, derive the result from the behavior or host state you want to require. Returning the expected literals directly supplies no meaningful environment check.

### When it runs

Every precheck runs once per output startup, in source order, before the main script. Build optimization does not remove startup checks.

Where you write the declaration does not control when or whether the check runs. A precheck inside `if false then ... end`, an uncalled function, or a loop still registers a startup check. A loop does not make it run repeatedly. There is no fixed macro limit on the number of prechecks or the length of an expected array; more checks and longer arrays add startup work and payload overhead.

The `if CTN_OBFUSCATED then` guard in the examples lets the same source run without obfuscation, where `CTN_PRECHECK` is an undefined global. It **does not make the check conditional in obfuscated output**: every declaration registers a startup check regardless of the surrounding branch.

### Function rules

* Argument 1 must be an anonymous `function() ... end` literal with no parameters or varargs. A named function, a variable containing a function, or a macro wrapper around the argument is rejected.
* The function cannot capture locals from outside its body, even through a nested function or an unreachable branch. A captured local `_ENV` is also rejected on Lua targets; global accesses must use the default environment.
* Locals declared inside the function, nested functions, and supported Centurion macros inside its body are allowed, subject to those macros' own rules.
* Globals and host APIs can be used, but they must already exist when the output starts. A helper assigned by the main script is not available yet, even if its assignment appears before the precheck declaration in your source.
* Only the first return value is used. To provide multiple integers, return a table such as `{ 11, 22 }`, rather than `return 11, 22`.

For example, this fails at build time because the check captures an outer local:

```lua
local Value = 71
CTN_PRECHECK(function()
    return Value
end, 71)
```

Declare working locals inside the check instead, or read state supplied by the host environment before the output runs.

### Expected values and runtime results

Argument 2 must be written directly as an integer literal, such as `71`, `-33`, or `0x12345678`, or an array constructor containing those literals, such as `{ 11, 22, -33 }`. Empty arrays are accepted. Explicit table keys, variables, arithmetic expressions, fractional notation (`1.0`), and exponent notation (`1e0`) are rejected at every optimization level.

This is rejected even though the expression evaluates to an integer:

```lua
CTN_PRECHECK(function()
    return math.abs(-71)
end, 70 + 1)
```

The integer range depends on the target:

| Target        | Accepted integer values                                                                                                                   |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Roblox / Luau | `-9007199254740991` through `9007199254740991` (the safe 53-bit integer range). Binary literals and numeric separators are also accepted. |
| FiveM / Lua   | Signed 64-bit integers. Hexadecimal literals follow the target's integer representation.                                                  |

For a scalar expected value, the function must return a number with that exact integer value. Strings, booleans, `nil`, fractional numbers, and values outside the target's integer range are invalid.

For an expected array of length `n`, the function must return a plain table with exactly the keys `1` through `n`, no holes or extra keys, and no metatable. Each element must be an integer with the expected value at that position. An empty expected array requires an empty table. `{ 71 }` and `71` require different return shapes, even though both contain one integer.

Malformed results, incorrect values, and errors raised by the check all abort startup. The main script does not execute. A `pcall` around the declaration in your source cannot catch this failure, because the check runs before the main script starts.

### What it protects

Every precheck must produce its expected result for the main script to start. Use it to require specific behavior or host state before your main code runs.

This remains an environment-dependent startup check, not proof of identity or authorization. An environment that reproduces the expected results can satisfy it. For a function body that requires a key obtained from your own server at runtime, see [`CTN_ENCRYPT_FUNCTION`](/macros/ctn-encrypt-function.md).

---

## CTN\_NO\_VIRTUALIZE

**Form:** call

`CTN_NO_VIRTUALIZE(function ... end)` takes a single function literal and produces a **native, non-virtualized** version of it. The wrapped function runs as ordinary Lua at full speed instead of being interpreted by the generated VM.

### Usage

Pass exactly one function literal as the only argument. The macro yields the native function, which you use like any other function value:

```lua
local Dot = CTN_NO_VIRTUALIZE(function(Ax, Ay, Bx, By)
    return Ax * Bx + Ay * By
end)

print(Dot(1, 2, 3, 4))
```

### Why use it

Virtualization is what makes your code hard to reverse, but it adds runtime overhead. For a small, hot piece of code, that overhead can matter. `CTN_NO_VIRTUALIZE` lets you opt a specific function out of the VM so it runs at native speed.

This is a deliberate trade-off: the wrapped function gets **less protection** in exchange for more speed. Use it only for performance-critical sections that don't contain sensitive logic.

{% hint style="info" %}
Non-virtualized functions receive additional obfuscation automatically while continuing to run natively. This protection is enabled by default and does not require an API field or dashboard option.
{% endhint %}

### Rules

* The argument must be a **single function literal** (`function(...) ... end`). Anything else is rejected at compile time.
* Other Centurion [macros](/macros/overview.md) cannot be used **inside** a `CTN_NO_VIRTUALIZE` function. Doing so is a compile error.

### Example

Keep a tight numeric loop native while the rest of the script stays virtualized:

```lua
local SumSquares = CTN_NO_VIRTUALIZE(function(N)
    local Total = 0
    for I = 1, N do
        Total = Total + I * I
    end
    return Total
end)

print(SumSquares(1000000))
```

---

## CTN\_JIT (Beta only)

**Form:** call

**Availability:** Beta only (`"node": "beta"`).

`CTN_JIT(function ... end)` marks a function for faster execution while keeping it inside Centurion's generated virtual machine.

### Usage

Pass exactly one function literal. The macro returns a callable function:

```lua
local SumSquares = CTN_JIT(function(N)
    local Total = 0
    for I = 1, N do
        Total = Total + I * I
    end
    return Total
end)

print(SumSquares(1000000))
```

The macro also applies to functions defined inside the marked function, at any nesting depth.

### When to use it

Use `CTN_JIT` for functions that account for a significant part of your script's execution time, such as frequently called loops or calculations.

The marked function remains virtualized, but receives less protection in exchange for faster execution. [`CTN_NO_VIRTUALIZE`](/macros/ctn-no-virtualize.md) makes a different tradeoff by running the function natively.

{% hint style="warning" %}
Performance gains depend on your script and build options. They may be more noticeable with `intense_vm_generation` enabled; small functions or scripts may see little improvement. Benchmark your own script before deciding which functions to mark.
{% endhint %}

### Rules

* Pass exactly one **function literal** (`function(...) ... end`). A reference to an existing function or any additional argument is rejected at compile time.
* The macro applies to the marked function and every function defined inside it.
* Mark only the functions that need the performance tradeoff; each marked function receives less protection.

### Example

```lua
local ProcessFrame = CTN_JIT(function(Entities)
    local Total = 0
    for I = 1, #Entities do
        Total = Total + Entities[I].Value
    end
    return Total
end)

print(ProcessFrame({ { Value = 10 }, { Value = 20 } }))
```

---

## CTN\_ENCRYPT\_FUNCTION (Beta wrapper)

**Form:** call

**Availability:** Beta only for the wrapper syntax documented here (`"node": "beta"`). Main uses an older syntax.

`CTN_ENCRYPT_FUNCTION(function ... end, "<64 hex>", keyExpr)` encrypts a function's body with a key you choose at build time. Your running script supplies the same key to unlock the function.

### Usage

Pass three arguments: a function literal, a 64-character hexadecimal string literal as the build-time key, and an expression that obtains that key at runtime:

```lua
local Protected = CTN_ENCRYPT_FUNCTION(function(A, B)
    return A * B + 1
end, "1a2b3c4d5e6f708192a3b4c5d6e7f8091a2b3c4d5e6f708192a3b4c5d6e7f809", FetchKeyFromWhitelistServer())

print(Protected(6, 7))
```

`FetchKeyFromWhitelistServer` is a helper you implement to obtain the key from your own server after performing your authorization checks. The third argument is evaluated when the macro call runs. The returned function can then be called normally.

The second argument supplies the build-time key and is consumed by the build.

{% hint style="danger" %}
Keep the runtime key out of the distributed script. Retrieve it from your own server. Embedding it in a string, an encrypted literal, or hardcoded pieces makes it recoverable from the output.
{% endhint %}

### When to use it

Use this macro when a function should require a key obtained from your own server before it can be used.

You choose the build-time key yourself. This macro requires no additional API option or Enterprise activation. For a key generated by Centurion and returned with the build, see [`CTN_HTTP_ENCRYPT_FUNCTION`](/macros/ctn-http-encrypt-function.md), which is **Enterprise only**.

### Rules

* Argument 1 must be a **single function literal** (`function(...) ... end`).
* Argument 2 must be a **string literal** containing exactly 64 hexadecimal characters (case-insensitive). Write it directly at the call site.
* Argument 3 must obtain the same key at runtime. A string literal or a direct `CTN_ENCRYPT_STRING`/`CTN_ENCRYPT_NUMBER` wrapper is rejected.
* The build-time key must not be embedded elsewhere in your script. Centurion rejects embedded copies it detects; you are still responsible for keeping the key on your server.
* A wrong key fails when the macro call runs, before the protected function can be used.
* Use the returned function directly; no separate decryption macro is needed.

### Example

```lua
local Protected = CTN_ENCRYPT_FUNCTION(function(UserId)
    return ComputeEntitlement(UserId)
end, "1a2b3c4d5e6f708192a3b4c5d6e7f8091a2b3c4d5e6f708192a3b4c5d6e7f809", RequestKeyFromWhitelistServer())

print(Protected(12345))
```

Implement `ComputeEntitlement` and `RequestKeyFromWhitelistServer` in your application.

---

## CTN\_HTTP\_ENCRYPT\_FUNCTION (Enterprise, Beta only)

**Form:** call

**Availability:** Enterprise only; Beta only (`"node": "beta"`).

`CTN_HTTP_ENCRYPT_FUNCTION(function ... end, "identifier", keyExpr)` encrypts a function's body with a key generated by Centurion. The build returns that key, and your running script obtains it from your own server to unlock the function.

{% hint style="info" %}
This is an **Enterprise-only** feature, enabled by request for your API key. Contact us on [Discord](https://discord.com/invite/GmKxNkPzGr) to arrange access. Select the `beta` node when building; there is no request option that enables this macro.
{% endhint %}

### Usage

Pass exactly three arguments: a function literal, a unique string literal identifying the function, and an expression that supplies its decryption key at runtime:

```lua
local Protected = CTN_HTTP_ENCRYPT_FUNCTION(function(A, B)
    return A * B + 1
end, "admin", FetchKeyFromWhitelistServer("admin"))

print(Protected(6, 7))
```

`FetchKeyFromWhitelistServer` is a helper you implement. It must return the key your server saved for this function and this build, after performing your authorization checks.

The macro does **not** make an HTTP request for you. Its third argument obtains the key using your own code. That expression is evaluated when the macro call runs, before the returned function is used.

### Build response

Centurion generates a new key for each protected function on each build. The [POST /obfuscate response](/api-reference/obfuscate.md#keys) includes a `keys` object indexed by the identifiers you supplied:

```json
{
  "keys": {
    "admin": "7c4b91e05a2f8d36c1e94a70b5d82f13a6e0c97b4d215e8f3a6c0b9d72e14f85"
  }
}
```

Each key is a 64-character hexadecimal string. The example above shows only the `keys` part of the response.

{% hint style="danger" %}
The keys are returned once with the build and are not stored by Centurion. Save them securely on your own server before discarding the response. If you lose a key, rebuild the script and save the new keys.
{% endhint %}

### Deployment workflow

1. Have the feature enabled for your Enterprise API key.
2. Obfuscate your source using `"node": "beta"` and save the response's `keys` object securely on your server.
3. Associate those keys with the matching output; rebuilding generates new keys.
4. Have your running script request the correct key from your server before using the protected function.

### Rules

* Argument 1 must be a **single function literal** (`function(...) ... end`).
* Argument 2 must be a **string literal**, unique across all uses of this macro in the script. It names the entry in the response's `keys` object.
* Argument 3 must supply the matching generated key at runtime. A string literal or a direct `CTN_ENCRYPT_STRING`/`CTN_ENCRYPT_NUMBER` wrapper is rejected.
* Keep the generated key out of the distributed source. Hiding it in an encrypted string or constructing it from hardcoded pieces does not keep it secret.
* A wrong key fails when the macro call runs, before the protected function can be used.
* Use the returned function directly; no separate decryption macro is needed.

If you want to supply your own build-time key, use [`CTN_ENCRYPT_FUNCTION`](/macros/ctn-encrypt-function.md), which does not require Enterprise access.

---

## CTN\_INLINE

**Form:** call

`CTN_INLINE` accepts one function literal. Calling its result immediately lets Centurion splice the body into the surrounding code when the result count is known. Storing, passing or returning the macro's result produces an ordinary callable Lua closure.

### Two forms

| Source shape                                   | Behaviour                                                                                                                             |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `CTN_INLINE(function(...) ... end)(arguments)` | Runs immediately. Inlines when the result count is known and the function is not vararg; otherwise uses an ordinary closure and call. |
| `local f = CTN_INLINE(function(...) ... end)`  | Stores a real function. Calls to `f` run normally; the body is not inlined.                                                           |

The trailing call is required to request inlining. It does not guarantee a splice: Centurion preserves arguments, side effects and multiple return values in either form.

### Usage

An assignment has a known result count, so this call binds the arguments and splices the body at the call site:

```lua
local Total = CTN_INLINE(function(A, B)
    return A + B
end)(2, 3)

print(Total) -- 5
```

Multiple results survive. A fixed number of locals supplies the result count:

```lua
local First, Second = CTN_INLINE(function()
    return "a", "b"
end)()

print(First, Second) -- a, b
```

### Why use it

Inlining removes a separate closure and call. Use the immediate form for code you want to run at that point in its surrounding function.

### Stored functions and callbacks

The stored form is a real Lua function. You can call it later, capture locals, return it from another function, or pass it as a callback, including to Roblox event connections such as `RunService.Heartbeat:Connect(f)`.

```lua
local Value = 40
local F = CTN_INLINE(function(Delta)
    Value = Value + Delta
    return Value
end)

print(F(2)) -- 42

local function Dispatch(Callback)
    Callback(3)
end

Dispatch(CTN_INLINE(function(Amount)
    print(Amount) -- 3
end))
```

This form is not inlined and adds no inlining benefit over a plain function literal. Use it when you want a function value.

### When an immediate call uses a closure

Inlining requires a known result count, as in an assignment, a fixed number of locals, or a non-final argument. An open result count uses a real closure and ordinary call instead. Examples include the final argument of a call, a return that spreads all results, and a call inside `select("#", ...)`. A vararg function literal also uses this fallback.

```lua
local Count = CTN_INLINE(function(...)
    return select("#", ...)
end)(7, 8)
print(Count) -- 2

print(select("#", CTN_INLINE(function()
    return 1, 2, 3
end)())) -- 3

local function Values()
    return CTN_INLINE(function()
        return "tail", "second"
    end)()
end
print(Values()) -- tail, second
```

These calls preserve all results. The fallback is automatic; you do not need to change the source to select it.

### Rules

* The argument must be a **single function literal** (`function() ... end`). A variable holding a function, a call that returns one, no argument, or more than one argument are all rejected at compile time.
* Parameters and arguments work in both forms. Varargs work through the ordinary-call fallback.
* Multiple return values survive in both forms. Surplus arguments and return expressions still have their side effects evaluated.
* A bare macro call in statement position, `CTN_INLINE(function() ... end)`, is a compile error. Add a trailing call to run it, or assign the result to keep the function.
* `return` inside the body leaves the inlined block only — not the function containing the macro. Code after the `CTN_INLINE(...)` call still runs.

### Example

```lua
local function Process(List)
    local First = CTN_INLINE(function()
        if #List == 0 then return nil end
        return List[1]
    end)()

    print("after")
    return First
end

print(Process({5})) -- after, then 5
print(Process({})) -- after, then nil
```

The inner function's body is emitted inline at the point of the `CTN_INLINE` call, and its result flows straight into `First`. Locals from the surrounding scope — `List` here — are read and written directly, because the body really is part of the enclosing function.

### Migrating older source

{% hint style="warning" %}
This is a breaking change. An old assignment such as `local total = CTN_INLINE(function() return 5 end)` now stores a function instead of receiving `5`. Add `()` after the macro call when you need the body's results. Bare statement-position uses also need a trailing call, or an assignment, to compile.
{% endhint %}

---

## CTN\_ENCRYPT\_STRING

**Form:** call

`CTN_ENCRYPT_STRING("...")` returns its string argument, but stores it behind an **extra, per-string encryption layer** that is decrypted at runtime. Use it to give a specific sensitive string stronger protection than the default constant encryption.

### Usage

Wrap a **string literal** and use the result like any other string value:

```lua
local Token = CTN_ENCRYPT_STRING("super-secret-token")
print(#Token)
```

The argument must be a single string literal - not a variable, a concatenation, or any other expression.

### When to use it

Every string in your script is already encrypted as part of the build. `CTN_ENCRYPT_STRING` adds a dedicated, per-string decryption routine on top, so a handful of especially sensitive strings - keys, endpoints, gate checks - cost an attacker more to recover. It adds a little runtime and size overhead, so reserve it for the strings that matter rather than wrapping everything.

### Rules

* Exactly **one argument**, and it must be a **string literal**. Anything else is a compile error.
* In raw, un-obfuscated source `CTN_ENCRYPT_STRING` is an undefined global, so calling it raises "attempt to call a nil value". Guard it with [`CTN_OBFUSCATED`](/macros/ctn-obfuscated.md) if you also run the script unprotected.

### Example

```lua
local Endpoint = CTN_ENCRYPT_STRING("https://api.example.com/secret")
HttpGet(Endpoint)
```

---

## CTN\_ENCRYPT\_NUMBER

**Form:** call

`CTN_ENCRYPT_NUMBER(123)` returns its numeric argument, but stores it behind an **extra encryption layer** that is decrypted at runtime, so the constant does not appear directly in the output. Use it for numbers you don't want an attacker to read off the build - magic values, thresholds, flags.

### Usage

Wrap a **number literal** and use the result like any other number:

```lua
local Threshold = CTN_ENCRYPT_NUMBER(1337)
if Score >= Threshold then
    Unlock()
end
```

The argument must be a single, finite number literal - not a variable, an arithmetic expression, or a non-finite value.

### When to use it

Reach for it when a specific constant is meaningful to an attacker - a licence check value, an opcode, a hardcoded limit. It carries a small runtime and size cost, so apply it to the numbers that matter rather than every literal.

### Rules

* Exactly **one argument**, and it must be a **finite number literal**. Anything else is a compile error.
* In raw, un-obfuscated source `CTN_ENCRYPT_NUMBER` is an undefined global, so calling it raises "attempt to call a nil value". Guard it with [`CTN_OBFUSCATED`](/macros/ctn-obfuscated.md) if you also run the script unprotected.

### Example

```lua
local MaxTries = CTN_ENCRYPT_NUMBER(3)
if Tries > MaxTries then
    CTN_CRASH()
end
```

