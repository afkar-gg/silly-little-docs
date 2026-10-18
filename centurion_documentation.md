# Centurion Documentation

Source: https://docs.centurion.best/ (index: https://docs.centurion.best/llms.txt)

---

## Centurion

Centurion is a Lua obfuscation service that protects your scripts from reverse engineering. It compiles your source into a custom bytecode and generates a **unique virtual machine for every script and every build** - so cracking one output tells an attacker nothing about the next.

{% hint style="info" %}
**Website:** [centurion.best](https://centurion.best) · **API:** [api.centurion.best](https://api.centurion.best) · **Discord:** [Join the server](https://discord.com/invite/GmKxNkPzGr)
{% endhint %}

### Why Centurion

Most obfuscators run every script through the same virtual machine. Crack that VM once and you've cracked everything it has ever protected - or ever will. Centurion takes a different approach: it generates a fresh, structurally unique VM per script, per build, so analysing one output does not provide a reusable map of every other output.

* **Unique VM per build** - no shared runtime to reverse once and reuse forever.
* **Anti-tamper + anti-dump** baked into the output itself, not bolted on afterward.
* **Minimal size overhead** - output stays close to your original file size.
* **Behavior-identical execution** - your script runs exactly as it did before.
* **Dashboard and full REST API** for manual use and automated pipelines.

### Supported platforms

Centurion targets **Roblox**, **Luau**, **Lua 5.4**, **Lua 5.5**, and **FiveM**. See [Supported Platforms](/features/platforms.md) for the details of each.

### Where to start

* [**Quickstart**](/getting-started/quickstart.md) - obfuscate your first script in under a minute.
* [**Dashboard**](/getting-started/dashboard.md) - the web interface at centurion.best.
* [**API Reference**](/api-reference/obfuscate.md) - integrate Centurion into your build pipeline.
* [**Discord Bot**](/getting-started/discord-bot.md) - obfuscate straight from Discord.

### How the protection works

Curious what actually happens to your code? [How It Works](/features/how-it-works.md) walks through the virtualization, control-flow scrambling, encryption, and anti-analysis layers.

---

## Quickstart

There are three ways to obfuscate a script with Centurion. Pick whichever fits your workflow.

### 1. Dashboard (no setup)

The fastest way to try Centurion is the web dashboard.

1. Go to [centurion.best](https://centurion.best) and sign in.
2. Upload or paste your Lua/Luau script.
3. Choose your **target platform** (Roblox, Luau, Lua 5.4, Lua 5.5, or FiveM).
4. Adjust any [options](/features/options.md) - or leave the defaults.
5. Click **Obfuscate** and download the result.

See the [Dashboard guide](/getting-started/dashboard.md) for a full walkthrough.

### 2. API

For automated build pipelines, POST your script to the API:

```js
const response = await fetch("https://api.centurion.best/obfuscate", {
    method: "POST",
    headers: {
        "content-type": "application/json",
        "authorization": "YOUR_API_KEY",
    },
    body: JSON.stringify({
        source: Buffer.from(`print("Hello, world!")`, "utf8").toString("base64"),
        options: { platform: "Roblox" },
    }),
});

const data = await response.json();
fs.writeFileSync("output.lua", data.result, "utf8");
```

API access requires an active plan and an API key. See [Authentication & Quota](/api-reference/authentication.md) and the [POST /obfuscate](/api-reference/obfuscate.md) reference.

### 3. Discord bot

Obfuscate without leaving Discord. In the [Centurion server](https://discord.com/invite/GmKxNkPzGr), run `/obfuscate` and the bot walks you through an interactive flow in your DMs: pick your settings from menus, send your script, and get the protected file back. A linked Centurion account gives you a free daily build.

See the [Discord Bot guide](/getting-started/discord-bot.md) for the full walkthrough.

### Next steps

* Understand what the obfuscator does to your code - [How It Works](/features/how-it-works.md).
* Fine-tune the output - [Obfuscation Options](/features/options.md).
* Pick the right target - [Supported Platforms](/features/platforms.md).

---

## Dashboard

The Centurion dashboard at [centurion.best](https://centurion.best) is the primary way to obfuscate scripts by hand - no code or API key required.

### Signing in

Create an account or sign in from the homepage. Your account tracks your plan, remaining quota, token balance, and obfuscation history.

### Obfuscating a script

1. **Provide your source.** Upload a `.lua` / `.luau` file or paste your code directly. The maximum source size is **10 MB**.
2. **Choose a platform.** Select the environment your script targets - Roblox, Luau, Lua 5.4, Lua 5.5, or FiveM. This determines which VM templates and runtime checks are used. See [Supported Platforms](/features/platforms.md).
3. **Set your options.** Toggle features like intense VM generation, VM compression, and optimization level. Every option is explained in [Obfuscation Options](/features/options.md). The defaults are safe for most scripts.
4. **Obfuscate.** Centurion builds a unique VM for your script and returns the protected output for you to copy or download.

### Cost per build

Each successful obfuscation spends **one obfuscation** from your monthly plan quota (the amount depends on your plan) or one purchased token. **Failed builds are never charged.** See [Pricing](/more/pricing.md) for details on plans and tokens.

### Download expiry

Stored results are available for a limited time after a build. Download your output promptly - once the retention window passes, the stored copy is wiped and you'd need to re-obfuscate.

### History

Your dashboard keeps a record of past obfuscations - platform, node, size, and status - so you can review what you've built.

{% hint style="warning" %}
Centurion produces **behavior-identical** output, but always test your obfuscated script in its real environment before shipping. If something misbehaves, try toggling options (for example, disabling `vm_compression`) and report reproducible issues on the [Discord](https://discord.com/invite/GmKxNkPzGr).
{% endhint %}

---

## Discord Bot

The Centurion Discord bot lets you obfuscate scripts without leaving Discord. Join the [Centurion server](https://discord.com/invite/GmKxNkPzGr) to use it.

Unlike a plain slash command, `/obfuscate` runs an **interactive flow in your DMs**: you pick your settings from menus, send your script, confirm, and the bot replies with the protected file.

### Commands

| Command      | Where          | What it does                                                                      |
| ------------ | -------------- | --------------------------------------------------------------------------------- |
| `/obfuscate` | Server channel | Starts the obfuscation flow. The bot replies privately and continues in your DMs. |
| `/plan`      | DM only        | Shows your plan, remaining quota, token balance, and when your quota resets.      |
| `/status`    | Anywhere       | Confirms the bot is online.                                                       |
| `/website`   | Anywhere       | Links to [centurion.best](https://centurion.best).                                |
| `/help`      | Anywhere       | Lists the available commands.                                                     |

{% hint style="info" %}
In some servers `/obfuscate` is restricted to a dedicated channel. If so, the bot tells you which channel to use.
{% endhint %}

### Obfuscating a script

1. Run `/obfuscate` in the server. The bot replies privately and opens a DM with you.
2. In the DM, use the menus to choose your **platform**, **optimization level**, **node** (if more than one is available, paid plans only for beta), and any **toggle options**. Linked accounts also pick a **payment method** (plan quota or a token).
3. **Send your script** in the same DM - either attach a `.lua` / `.luau` file or paste your code inside a code block. Maximum **10 MB**.
4. Review the summary and press **Obfuscate** to confirm.
5. The bot replies with your obfuscated file attached, plus an embed showing the build **ID**, platform, node, build time, and input/output sizes.

The flow times out after **5 minutes** of inactivity at any step - just run `/obfuscate` again to restart.

#### Platforms

The bot targets **Roblox** and **FiveM**. For the other platforms - Luau, Lua 5.4, Lua 5.5 - use the [Dashboard](/getting-started/dashboard.md) or the [API](/api-reference/obfuscate.md).

#### Options

| Menu option              | Maps to                                                                                    | Availability |
| ------------------------ | ------------------------------------------------------------------------------------------ | ------------ |
| Optimization Level       | [`optimization_level`](/features/options.md#optimization_level) (`1`/`2`/`3`, default `2`) | Everyone     |
| VM Compression           | [`vm_compression`](/features/options.md#vm_compression)                                    | Premium      |
| Intense VM Generation    | [`intense_vm_generation`](/features/options.md#intense_vm_generation)                      | Premium      |
| Disable Line Information | [`disable_line_information`](/features/options.md#disable_line_information)                | Everyone     |
| Static Environment       | [`static_env`](/features/options.md#static_env)                                            | Roblox only  |

Premium options and the beta node are locked (shown with a 🔒) unless you are on a paid plan or pay for the build with a token. **Static Environment only appears for `Roblox`** - `FiveM` scripts reach globals through `_ENV`, so their environment is always static and there is nothing to toggle. See [Obfuscation Options](/features/options.md) for a full explanation of each, and [Nodes](/api-reference/nodes.md) for what the node menu selects.

### Access and quota

Your access depends on whether your Discord account is linked to a Centurion account:

| Tier                           | Daily / plan allowance      | Premium options & beta node       |
| ------------------------------ | --------------------------- | --------------------------------- |
| **Guest** (Discord not linked) | Account link required       | Locked                            |
| **Linked, free plan**          | 1 obfuscation per day       | Locked (unless paid with a token) |
| **Paid plan**                  | Your plan quota, or a token | Unlocked                          |

Link your Discord account from your [account settings](https://centurion.best/dashboard/account) to claim your free daily build and use your quota and tokens. Paying for a build with a **token** unlocks every premium option for that build. As on the dashboard and API, **failed builds are never charged**.

{% hint style="warning" %}
Centurion produces **behavior-identical** output, but always test your obfuscated script in its real environment before shipping. If a build fails, the bot returns the error and the build **ID** - include both when reporting the issue on the [Discord](https://discord.com/invite/GmKxNkPzGr).
{% endhint %}

---

## How It Works

Centurion doesn't just rename variables and shuffle lines - it **recompiles your script into a custom bytecode** and generates a purpose-built virtual machine to run it. Here's what each layer does.

### Virtualization

Your script is compiled into protected bytecode and executed by a virtual machine generated specifically for that script and build. This makes the output harder to recover with ordinary Lua decompilers.

**Every build is structurally different from the last.** Analysing one output does not provide a reusable map of every other output.

### Control-flow scrambling

Centurion aggressively restructures the execution flow of your script, introducing unpredictable branches and redirections. This breaks the linear, readable structure that static and dynamic analysis tools rely on, making it extremely difficult to follow what the code actually does.

### Encryption

Centurion encrypts your script's bytecode and constants to make them harder to inspect in the output. The protection varies between builds. Use the [encryption macros](/macros/overview.md) when you need additional protection for specific values or functions.

### Bytecode mutation & metadata stripping

Names and metadata that could aid reconstruction are removed or obscured. You can also [disable line information](/features/options.md#disable_line_information) to remove source line references from runtime errors.

### Anti-tamper & anti-dump

Protection is **baked into the output itself**, not bolted on afterward:

* **Anti-tamper** - environment and integrity checks that detect when the script is being run or modified in a way it shouldn't be.
* **Anti-dump / anti-beautify** - measures that frustrate attempts to dump the VM's internal state or reformat the output into something readable.

On the `Roblox` platform this includes a block of [engine environment checks](/features/platforms.md#roblox-environment-checks) that Centurion embeds automatically. They probe real Roblox APIs, so the output only runs on the real engine and not under a Luau emulator. The checks are universal - they work identically on the client and the server, with nothing to configure.

Want more of this? [`intense_vm_generation`](/features/options.md#intense_vm_generation) rebuilds the VM itself with a further layer of structural mutation.

### Compression

Centurion includes size-optimization passes. In many cases the obfuscated output is **no larger than your original script**. The optional [`vm_compression`](/features/options.md#vm_compression) flag compresses the generated VM itself for an additional size reduction.

### Behavior-identical execution

Despite all of the above, the obfuscated script runs exactly like your source - same behavior, same results. Centurion maintains a test suite to ensure obfuscated scripts remain correct and free of obscure bugs introduced by the obfuscation process.

{% hint style="info" %}
Want to control how aggressive these layers are? See [Obfuscation Options](/features/options.md).
{% endhint %}

---

## Supported Platforms

Centurion tailors the generated output and runtime checks to the platform your script targets. Always pick the platform that matches where your script will actually run.

| Platform | Value    | Notes                                                                                                                       |
| -------- | -------- | --------------------------------------------------------------------------------------------------------------------------- |
| Roblox   | `Roblox` | Luau on the Roblox engine. Roblox environment checks are embedded automatically.                                            |
| Luau     | `Luau`   | Standalone Luau (e.g. Lune and other Luau runtimes).                                                                        |
| Lua 5.5  | `Lua5.5` | Standard Lua 5.5.                                                                                                           |
| Lua 5.4  | `Lua5.4` | Standard Lua 5.4.                                                                                                           |
| FiveM    | `FiveM`  | FiveM (CfxLua). Bitwise operations compile to native operators; FiveM-specific anti-tamper environment checks are included. |

### Choosing a platform

* **Roblox scripts** - use `Roblox`. This embeds Roblox-specific environment checks (see below).
* **Luau outside Roblox** - use `Luau`.
* **Server-side / standalone Lua** - use `Lua5.4` or `Lua5.5` to match your interpreter.
* **FiveM resources** - use `FiveM` for the CfxLua-aware pipeline.

### Roblox environment checks

Selecting the `Roblox` platform automatically includes checks that require the real Roblox engine. If the environment does not meet those requirements, the script refuses to run.

The result is that a `Roblox` build is tied to the Roblox engine: it will not run under Luau emulators or reimplementations such as Lune, and it can't be lifted out of Roblox and executed elsewhere.

**The checks are universal.** Only APIs reachable from both the client and the server are used, so the same output runs unchanged in a `LocalScript`, a `Script`, or a `ModuleScript` loaded from either side. There is nothing to configure and no context to choose.

{% hint style="info" %}
These checks are specific to the `Roblox` platform. `Luau`, `Lua5.4`, `Lua5.5`, and `FiveM` builds carry their own platform-appropriate anti-tamper checks instead.
{% endhint %}

{% hint style="warning" %}
Because a `Roblox` build only runs on the real engine, test it inside Roblox (Studio or a live place). Running it through a standalone Luau interpreter will fail by design.
{% endhint %}

---

## Obfuscation Options

These options control how Centurion protects your script. They're available on the [dashboard](/getting-started/dashboard.md) and the [API](/api-reference/obfuscate.md) (as fields inside the `options` object). The defaults are safe for most scripts.

### platform

**Required.** Target platform: `Roblox`, `Luau`, `Lua5.5`, `Lua5.4`, or `FiveM`. Determines the VM templates and runtime checks used. See [Supported Platforms](/features/platforms.md).

Picking `Roblox` also embeds a set of Roblox environment checks automatically - see [Roblox environment checks](/features/platforms.md#roblox-environment-checks).

### intense\_vm\_generation

**Boolean, default `false`. Paid plans only.** Adds stronger protection to the generated virtual machine, at the cost of a larger output and a slower build.

{% hint style="info" %}
Free dashboard accounts can't enable this - the flag is ignored rather than rejected, so the build still succeeds without it. It's always available over the [API](/api-reference/obfuscate.md), since API access already requires an active plan.
{% endhint %}

### vm\_compression

**Boolean, default `false`. Paid plans only.** Compresses the generated VM into a separate compressed payload that an outer stub decompresses and loads at runtime. This reduces output size at the cost of a small amount of startup work, and it needs `loadstring`/`load` to be available in the target environment.

The saving is roughly a constant amount of the VM's own size, so it matters most on small scripts and fades on large ones.

Same plan rule as `intense_vm_generation` above: gated on the dashboard, always available over the API.

{% hint style="info" %}
The legacy name `compress_vm` is still accepted as an alias. Prefer `vm_compression` for new integrations.
{% endhint %}

{% hint style="info" %}
If an obfuscated script misbehaves, try disabling `vm_compression` first - it's the option most likely to interact with unusual runtime environments.
{% endhint %}

### optimization\_level

**Integer, default `2`.** Optimizer aggressiveness from `1` (least) to `3` (most aggressive).

* **`1`** - most conservative; safest transformations only.
* **`2`** (default) - balances runtime performance and build time.
* **`3`** - most aggressive optimization.

Higher levels generally produce faster output. If you hit a rare correctness issue, lowering the level is a good first thing to try.

### static\_env

**Boolean, default `false`. `Roblox` and `Luau` only.** Also accepted as `static_environment`. Resolves the globals your script uses once at startup instead of looking them up on every use. This is faster, but it means later changes to the environment aren't seen.

You can use either `static_env` or `static_environment` interchangeably in your configuration.

Leave it off if your script (or anything it runs alongside) replaces globals while running - for example hooking `print`, swapping a library table, or sandboxing the environment after load. On the non-Luau platforms this behaviour is always on and the option has no effect.

### disable\_line\_information

**Boolean, default `false`.** When `true`, omits line information from the output. This produces a smaller and faster result and removes a source of readable metadata; in exchange, runtime errors from the obfuscated script no longer carry useful line numbers.

Line information is **kept** by default on both the dashboard and the API. Send `disable_line_information: true` to strip it.

You can still keep a line reference at a specific point with the [`CTN_LINE`](/macros/ctn-line.md) macro, which works even when line stripping is enabled.

### node

**String, default `main`.** Selects the execution node. Use `beta` for features documented as Beta only, when that node is available to your account. An unknown or unavailable node name is rejected. See [Nodes](/api-reference/nodes.md).

Note that `node` is a top-level field on the API request, not a member of `options`.

***

{% hint style="info" %}
**Anti-tamper and minification** are always on for production builds and aren't exposed as toggles - the output is always minified and always ships with anti-tamper protections.
{% endhint %}

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

---

## Requirements

Before you can submit an obfuscation request to the Centurion API, make sure the following are in place. To check API availability, use [GET /status](/api-reference/status.md); it requires no plan or API key.

### An active plan

API access is a **subscription benefit**. A personal API key only works while your plan is active.

{% hint style="warning" %}
Purchased **credits do not grant API access** - they're for [dashboard](/getting-started/dashboard.md) use only. You need an active [plan](/more/pricing.md) to use the API.
{% endhint %}

### An API key

Every obfuscation request must include your key in the `authorization` header. See [Authentication & Quota](/api-reference/authentication.md) for how keys and quota work.

### A correctly formed request

| Requirement              | Detail                                               |
| ------------------------ | ---------------------------------------------------- |
| Method                   | `POST`                                               |
| Endpoint                 | `https://api.centurion.best/obfuscate`               |
| `content-type` header    | `application/json`                                   |
| `authorization` header   | Your API key                                         |
| `source` field           | Your script, **base64-encoded** (not raw text)       |
| `options.platform` field | One of `Roblox`, `Luau`, `Lua5.5`, `Lua5.4`, `FiveM` |

See [POST /obfuscate](/api-reference/obfuscate.md) for the full request body and every option.

### Checklist

* [ ] Plan is active.
* [ ] API key is sent in the `authorization` header.
* [ ] `content-type: application/json` is set.
* [ ] `source` is base64-encoded and within the [size limit](/api-reference/limits.md).
* [ ] `options.platform` is a valid platform value.
* [ ] You have [quota](/api-reference/limits.md#quota) remaining for the month.

---

## Authentication & Quota

The Centurion API lives at **`https://api.centurion.best`**. Obfuscation requests are authenticated with an API key. The public [GET /status](/api-reference/status.md) endpoint requires no authentication and does not consume quota.

### API keys

Pass your key in the `authorization` header on every obfuscation request:

```
authorization: YOUR_API_KEY
```

{% hint style="warning" %}
API access is a **subscription benefit**. A personal API key requires an **active plan** - purchased credits are for dashboard use only and do **not** grant API access.
{% endhint %}

### Quota

Each successful build spends **one obfuscation** from your monthly plan quota (standard plans include **1000 obfuscations per month**). Notes:

* **Failed builds are not charged** - only successful obfuscations count against your quota.
* Quota resets on a monthly cycle.
* When your quota is exhausted, the API returns [`402`](/api-reference/errors.md) until it resets (or you top up).

### Key types

| Key type            | Metered? | Notes                                                                                 |
| ------------------- | -------- | ------------------------------------------------------------------------------------- |
| Personal (plan) key | Yes      | Requires an active plan; each success spends one obfuscation from your monthly quota. |
| Global key          | No       | Not metered against the monthly quota.                                                |

### Rate limits

Requests are rate limited per key. If you exceed the limit, the API returns [`429`](/api-reference/errors.md) with a **`Retry-After`** header telling you how many seconds to wait before retrying. Respect that header in automated clients.

### Source size limit

Source files are capped at **10 MB** (base64-decoded). Larger sources are rejected with [`413`](/api-reference/errors.md).

### Next

* [POST /obfuscate](/api-reference/obfuscate.md) - the obfuscation endpoint.
* [Errors](/api-reference/errors.md) - full status-code reference.

---

## Limits

The API enforces the following limits. Design your integration to respect them.

### Source size

| Limit               | Value                      |
| ------------------- | -------------------------- |
| Maximum source size | **10 MB** (base64-decoded) |

Sources larger than this are rejected with [`413`](/api-reference/errors.md). If you're close to the limit, remove dead code and comments before obfuscating.

### Quota

| Limit                 | Value                                                  |
| --------------------- | ------------------------------------------------------ |
| Included obfuscations | Depends on your plan - see [Pricing](/more/pricing.md) |
| Reset                 | Monthly                                                |
| Charged on            | **Successful** builds only                             |

Each successful build spends one obfuscation from your monthly quota. **Failed builds are never charged.** When your quota is exhausted, the API returns [`402`](/api-reference/errors.md) until it resets, or until you buy a quota top-up. See [Pricing](/more/pricing.md).

{% hint style="info" %}
Global keys, where issued, are **not metered** against the monthly quota.
{% endhint %}

### Rate limit

Requests are rate limited per key over a rolling one-minute window. Exceeding it returns [`429`](/api-reference/errors.md) with a **`Retry-After`** header giving the number of seconds to wait.

Always honor `Retry-After` in automated clients rather than retrying immediately. A simple exponential backoff on `429` is the safest approach for high-volume pipelines.

### Build time

Obfuscation runs under a **server-side time limit of 600 seconds**. A build that exceeds it is aborted and returns [`504`](/api-reference/errors.md) rather than running indefinitely. Very large or unusually complex scripts are the usual cause; if a legitimate script times out consistently, report it on the [Discord](https://discord.com/invite/GmKxNkPzGr) with details.

### Summary

| Limit         | Value               | Exceeded response                                 |
| ------------- | ------------------- | ------------------------------------------------- |
| Source size   | 10 MB               | [`413`](/api-reference/errors.md)                 |
| Monthly quota | depends on plan     | [`402`](/api-reference/errors.md)                 |
| Rate limit    | per-key, per-minute | [`429`](/api-reference/errors.md) + `Retry-After` |
| Build time    | 600 seconds         | [`504`](/api-reference/errors.md)                 |

---

## GET /status

Check whether the Centurion API is responding. This endpoint is public: it requires no API key or active plan and does not consume obfuscation quota.

### Request

```http
GET https://api.centurion.best/status
```

No headers, query parameters, or request body are required.

```bash
curl https://api.centurion.best/status
```

### Response

**`200 OK`** with an `application/json` body:

```json
{
  "success": true,
  "message": "Centurion is up and running!"
}
```

This is a basic availability check; it does not run an obfuscation or check worker readiness.

---

## POST /obfuscate

Obfuscates a Lua source file.

```
POST https://api.centurion.best/obfuscate
```

### Headers

| Header          | Value                                                                                |
| --------------- | ------------------------------------------------------------------------------------ |
| `content-type`  | `application/json`                                                                   |
| `authorization` | Your API key (**required**). See [Authentication](/api-reference/authentication.md). |

### Body

| Field     | Type   | Required | Description                                                                                                                               |
| --------- | ------ | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `source`  | string | Yes      | Base64-encoded source file. Max **10 MB** decoded.                                                                                        |
| `options` | object | Yes      | Obfuscation options (see below).                                                                                                          |
| `node`    | string | No       | Execution node - `main` (default), or `beta` for Beta-only features when available to your account. See [Nodes](/api-reference/nodes.md). |

#### options

| Field                      | Type    | Default      | Description                                                                                   |
| -------------------------- | ------- | ------------ | --------------------------------------------------------------------------------------------- |
| `platform`                 | string  | - (required) | `Roblox`, `Luau`, `Lua5.5`, `Lua5.4`, or `FiveM`.                                             |
| `intense_vm_generation`    | boolean | `false`      | Extra VM structural mutation.                                                                 |
| `vm_compression`           | boolean | `false`      | Compress the generated VM.                                                                    |
| `optimization_level`       | integer | `2`          | Optimizer aggressiveness, `1`–`3`.                                                            |
| `static_env`               | boolean | `false`      | Resolve globals once at startup. `Roblox`/`Luau` only. Also accepted as `static_environment`. |
| `disable_line_information` | boolean | `false`      | Omit line info from the output.                                                               |

Every option is explained in detail in [Obfuscation Options](/features/options.md).

{% hint style="info" %}
Every option above is available on every API key. Some of them - `intense_vm_generation` and `vm_compression` - are restricted to paid plans elsewhere, but [API access is itself a subscription benefit](/api-reference/authentication.md#api-keys), so if you can call this endpoint you can use them.
{% endhint %}

{% hint style="info" %}
`disable_line_information` defaults to `false` - line information is kept unless you send `disable_line_information: true` to strip it.
{% endhint %}

{% hint style="info" %}
Both `static_env` and `static_environment` are accepted for the static environment option. `compress_vm` is still accepted as a legacy alias for `vm_compression`, and the legacy `intense_optimizations` boolean is still accepted when `optimization_level` is omitted (`true` → `2`, `false` → `1`). Prefer `vm_compression` and `optimization_level` for new integrations.
{% endhint %}

{% hint style="danger" %}
`platform_lock` and `platform_lock_context` have been **removed**. Roblox environment checks are now embedded automatically on every `Roblox` build and work on both the client and the server - see [Roblox environment checks](/features/platforms.md#roblox-environment-checks). Drop both fields from your integration.
{% endhint %}

### Examples

#### JavaScript (Node.js)

```js
const fs = require("fs");

function encode(input) {
    return Buffer.from(input, "utf8").toString("base64");
}

const response = await fetch("https://api.centurion.best/obfuscate", {
    method: "POST",
    headers: {
        "content-type": "application/json",
        "authorization": "YOUR_API_KEY",
    },
    body: JSON.stringify({
        node: "main",
        source: encode(`print("Hello, world!")`),
        options: {
            platform: "Roblox",
            vm_compression: true,
            optimization_level: 2,
            disable_line_information: false,
        },
    }),
});

const data = await response.json();

if (!data.success) {
    throw new Error(data.message);
}

fs.writeFileSync("output.lua", data.result, "utf8");
```

#### Python

```python
import base64, json, requests

with open("input.lua", "r", encoding="utf-8") as f:
    source = base64.b64encode(f.read().encode("utf-8")).decode("ascii")

response = requests.post(
    "https://api.centurion.best/obfuscate",
    headers={
        "content-type": "application/json",
        "authorization": "YOUR_API_KEY",
    },
    json={
        "node": "main",
        "source": source,
        "options": {
            "platform": "Roblox",
            "vm_compression": True,
            "optimization_level": 2,
            "disable_line_information": False,
        },
    },
)

response.raise_for_status()
result = response.json()

with open("output.lua", "w", encoding="utf-8") as f:
    f.write(result["result"])
```

#### cURL

```bash
SOURCE=$(base64 -w0 input.lua)

curl -sS https://api.centurion.best/obfuscate \
  -H "content-type: application/json" \
  -H "authorization: YOUR_API_KEY" \
  -d "{\"source\":\"$SOURCE\",\"options\":{\"platform\":\"Roblox\"}}"
```

### Response

**`200 OK`** with an `application/json` body:

```json
{
  "success": true,
  "message": "Obfuscation completed.",
  "id": "9f3c1ab47e02d85c",
  "time": 2.41,
  "result": "-- This file was obfuscated with Centurion | https://centurion.best\n\nreturn select(1,function(...)..."
}
```

| Field     | Type    | Description                                                                                                                                                        |
| --------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `success` | boolean | `true` when the build completed.                                                                                                                                   |
| `message` | string  | Human-readable status.                                                                                                                                             |
| `id`      | string  | 16-character hex build id. Quote it when reporting a problem.                                                                                                      |
| `time`    | number  | Build time in seconds.                                                                                                                                             |
| `result`  | string  | Your protected script, as Lua source text.                                                                                                                         |
| `keys`    | object  | Only present when the script uses [`CTN_HTTP_ENCRYPT_FUNCTION`](/macros/ctn-http-encrypt-function.md), an **Enterprise-only** macro in the Beta engine. See below. |

The body is ordinary JSON: parse it once. `result` is already the finished script, so write it straight to a file without decoding it further.

#### keys

A script that uses [`CTN_HTTP_ENCRYPT_FUNCTION`](/macros/ctn-http-encrypt-function.md) has its keys generated at build time. This macro is **Enterprise only**, must be enabled for your API key by request, and requires `"node": "beta"`. The keys come back in a `keys` object, indexed by the name you passed as the macro's second argument:

```json
{
  "success": true,
  "message": "Obfuscation completed.",
  "id": "9f3c1ab47e02d85c",
  "time": 3.08,
  "result": "-- This file was obfuscated with Centurion | https://centurion.best\n\nreturn select(1,function(...)...",
  "keys": {
    "admin": "7c4b91e05a2f8d36c1e94a70b5d82f13a6e0c97b4d215e8f3a6c0b9d72e14f85"
  }
}
```

{% hint style="danger" %}
Centurion hands each generated key over once, in this response, and never stores it. If you lose it, the protected function cannot be unlocked and you have to rebuild. Save it before you discard the response.
{% endhint %}

Save the keys on your own server and associate them with this build's output. Your running script must obtain the matching key from your server; the macro does not perform an HTTP request itself. Each new build generates new keys.

See [Errors](/api-reference/errors.md) for failure responses.

### Status codes

| Code  | Meaning                                                                                       |
| ----- | --------------------------------------------------------------------------------------------- |
| `200` | Obfuscation successful.                                                                       |
| `400` | Malformed request, invalid or missing options, an unknown `node`, or failed to decode source. |
| `401` | Missing or invalid API key.                                                                   |
| `402` | No plan quota remaining (user keys only).                                                     |
| `403` | Account banned.                                                                               |
| `413` | Source exceeds the 10 MB limit.                                                               |
| `429` | Rate limit exceeded - see the `Retry-After` header.                                           |
| `500` | Obfuscation failed.                                                                           |
| `504` | Obfuscation timed out - the build exceeded the server-side time limit (600 seconds).          |

Full details in [Errors](/api-reference/errors.md).

---

## Nodes

A **node** is an execution engine for the obfuscator. The `node` field selects which one runs your build.

| Node | Value  | Use it for                                                                                                                                     |
| ---- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| Main | `main` | Stable, production builds. This is the default.                                                                                                |
| Beta | `beta` | New engine features, including `CTN_PRECHECK` and the Enterprise-only `CTN_HTTP_ENCRYPT_FUNCTION`. Requires a paid plan and node availability. |

### Choosing a node

`main` is the default and is used whenever `node` is omitted. It's the engine you should build against for anything you ship.

Select `beta` for features documented as Beta only. Beta access requires a paid plan; [`CTN_HTTP_ENCRYPT_FUNCTION`](/macros/ctn-http-encrypt-function.md) additionally requires Enterprise access and activation for your API key. When a node isn't available, a request that targets it is **rejected** with an invalid-node error - the build never silently falls back to `main`.

### Setting the node

Pass `node` at the top level of the [POST /obfuscate](/api-reference/obfuscate.md) request body, for example `"node": "main"`. An unknown or unavailable node name is rejected with a [`400`](/api-reference/errors.md) and the list of nodes you can use.

The [Discord bot](/getting-started/discord-bot.md) exposes a node menu in its `/obfuscate` flow whenever more than one node is available (beta node access requires a paid plan).

---

## Errors

The [`POST /obfuscate`](/api-reference/obfuscate.md) endpoint returns standard HTTP status codes.

| Code  | Meaning                                                                                                                   | What to do                                                                                                                  |
| ----- | ------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `200` | **Success.**                                                                                                              | Read the `result` field for your obfuscated script.                                                                         |
| `400` | **Bad request.** Malformed request, invalid or missing options, an unknown `node`, or the source failed to base64-decode. | Check that `source` is valid base64, `options.platform` is one of the accepted values, and `node` (if sent) is a real node. |
| `401` | **Unauthorized.** Missing or invalid API key.                                                                             | Send a valid key in the `authorization` header. See [Authentication](/api-reference/authentication.md).                     |
| `402` | **No quota remaining** (user keys only).                                                                                  | Wait for your monthly quota to reset, top up, or upgrade your plan. See [Pricing](/more/pricing.md).                        |
| `403` | **Banned.** The account is banned.                                                                                        | Contact support on the [Discord](https://discord.com/invite/GmKxNkPzGr).                                                    |
| `413` | **Payload too large.** Source exceeds the 10 MB limit.                                                                    | Reduce the source size or split the script.                                                                                 |
| `429` | **Rate limited.** Too many requests.                                                                                      | Wait the number of seconds in the **`Retry-After`** response header, then retry.                                            |
| `500` | **Obfuscation failed.** The engine could not process the script.                                                          | Retry; if it persists, report it on the [Discord](https://discord.com/invite/GmKxNkPzGr) with the build ID.                 |
| `504` | **Timed out.** The build exceeded the server-side time limit of **600 seconds**.                                          | Simplify or split the script; very large or pathological inputs can exceed the limit.                                       |

### Error responses

Every failure returns an `application/json` body with `success: false` and a `message`:

```json
{
  "success": false,
  "message": "Missing or invalid API key"
}
```

A build that reaches the engine and then fails carries an `id` as well, so the failure can be traced:

```json
{
  "success": false,
  "message": "Obfuscation failed.",
  "id": "9f3c1ab47e02d85c"
}
```

A `429` adds a **`Retry-After`** response header giving the number of seconds to wait:

```json
{
  "success": false,
  "message": "Rate limit exceeded"
}
```

### Handling errors in code

* **Always check the status code** before parsing the body.
* On `429`, back off using the `Retry-After` header rather than retrying immediately.
* **Failed builds don't consume quota**, so a `500` won't cost you an obfuscation - but repeated failures on the same script usually mean an unsupported construct; report it with the returned `id`.

{% hint style="info" %}
When you report a failed build on the Discord, include the build **`id`** from the response - it lets the team trace exactly what happened.
{% endhint %}

---

## Pricing

Centurion offers subscription plans and pay-as-you-go tokens. Every paid plan unlocks the standard obfuscation options. Enterprise also offers additional features, including [`CTN_HTTP_ENCRYPT_FUNCTION`](/macros/ctn-http-encrypt-function.md), which is **Enterprise only** and enabled for your API key by request.

{% hint style="info" %}
Purchase from [centurion.best](https://centurion.best). See the website for live pricing and the current list of payment methods.
{% endhint %}

### Plans

| Plan           | Monthly    | Yearly     | Obfuscations / month | Extras                                                                                                       |
| -------------- | ---------- | ---------- | -------------------- | ------------------------------------------------------------------------------------------------------------ |
| **Basic**      | $5         | $35        | 1,000                | Standard obfuscation options + API access                                                                    |
| **Pro**        | $10        | $70        | 2,500                | Everything in Basic + quota top-ups                                                                          |
| **Enterprise** | Contact us | Contact us | 3,000+               | Everything in Pro + priority support + custom API limits and macros + `CTN_HTTP_ENCRYPT_FUNCTION` by request |

Yearly billing works out cheaper than paying month to month. Each successful build spends one obfuscation from your monthly quota, which resets each cycle. Every paid plan unlocks every obfuscation option; the tiers differ in quota and the extras above.

### Tokens

**Tokens** are one-off obfuscations you can buy without a subscription, from **$0.50 each**. They never expire and are spent after your monthly quota runs out - ideal for occasional use. Paying for a build with tokens also unlocks the premium obfuscation options.

{% hint style="warning" %}
Tokens are for **dashboard use only** - they do **not** grant [API access](/api-reference/authentication.md). API access requires an active plan.
{% endhint %}

### Quota top-ups

If you run out of monthly quota early, you can buy a **quota top-up** to add more obfuscations to the current cycle without changing your plan. Top-ups are available on the **Pro** and **Enterprise** plans.

### What counts against your quota

* **Successful builds** spend one obfuscation each.
* **Failed builds are never charged.**

### Payment

Centurion accepts **crypto and card** payments, and you can also pay from your account balance. See the [website](https://centurion.best) for the current list of methods and live pricing.

### API access

A personal [API key](/api-reference/authentication.md) requires an active plan. Global keys, where issued, are not metered against the monthly quota.

---

## FAQ

### Will obfuscation change how my script behaves?

No. Centurion produces **behavior-identical** output - your script runs exactly as it did before. A test suite guards against obscure bugs introduced by the obfuscation process. Still, always test the obfuscated result in its real environment before shipping.

### How is Centurion different from other obfuscators?

Most obfuscators run every script through the **same** virtual machine - crack it once and every output it has ever produced is exposed. Centurion generates a **unique VM per script, per build**, with a randomized layout and no shared bytecode format. Breaking one output tells an attacker nothing about the next. See [How It Works](/features/how-it-works.md).

### Which platforms are supported?

Roblox, Luau, Lua 5.4, Lua 5.5, and FiveM. See [Supported Platforms](/features/platforms.md).

### How big will the output be?

Centurion includes size-optimization passes, and in many cases the output is **no larger than your original script**. Enabling [`vm_compression`](/features/options.md#vm_compression) reduces it further.

`Roblox` builds are the exception: they carry an extra block of [engine environment checks](/features/platforms.md#roblox-environment-checks) that adds a fixed amount to every build, regardless of how large your script is. On a tiny script that overhead dominates; on a real one it barely registers.

### My obfuscated script isn't working - what should I try?

1. Confirm you selected the **correct platform** for where the script runs. A `Roblox` build only runs on the real Roblox engine - test it in Studio or a live place, not under a standalone Luau interpreter.
2. Try disabling [`vm_compression`](/features/options.md#vm_compression).
3. Lower the [`optimization_level`](/features/options.md#optimization_level).
4. If you enabled [`static_env`](/features/options.md#static_env), turn it off - it breaks scripts that change their environment while running.
5. If it still fails, report it on the [Discord](https://discord.com/invite/GmKxNkPzGr) with the build **ID**.

### Can I obfuscate without a plan?

Yes. Linking your Discord to a free Centurion account gives you **one free build per day** on the [Discord bot](/getting-started/discord-bot.md). Paid plans and tokens raise that further, unlock the beta node, and unlock premium options. The API, by contrast, always requires an active plan.

### Do I need a plan to use the API?

Yes. A personal API key requires an **active plan**. Purchased tokens work on the dashboard but do **not** grant API access. See [Authentication & Quota](/api-reference/authentication.md).

### Do failed builds cost me anything?

No. Only **successful** obfuscations count against your quota or tokens.

### What are the size limits?

Up to **10 MB** of source per build - on the [dashboard](/getting-started/dashboard.md), the [API](/api-reference/obfuscate.md), and the [Discord bot](/getting-started/discord-bot.md) alike.

### Where can I get help?

Join the [Centurion Discord](https://discord.com/invite/GmKxNkPzGr). When reporting a problem, include your build **ID** so the team can trace it.

