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

Centurion encrypts your script's bytecode and constants to make them harder to inspect in the output. The protection varies between builds. Use the [encryption macros](centurion_macros_documentation.md) when you need additional protection for specific values or functions.

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

You can still keep a line reference at a specific point with the [`CTN_LINE`](centurion_macros_documentation.md) macro, which works even when line stripping is enabled.

### node

**String, default `main`.** Selects the execution node. Use `beta` for features documented as Beta only, when that node is available to your account. An unknown or unavailable node name is rejected. See [Nodes](/api-reference/nodes.md).

Note that `node` is a top-level field on the API request, not a member of `options`.

***

{% hint style="info" %}
**Anti-tamper and minification** are always on for production builds and aren't exposed as toggles - the output is always minified and always ships with anti-tamper protections.
{% endhint %}

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
| `keys`    | object  | Only present when the script uses [`CTN_HTTP_ENCRYPT_FUNCTION`](centurion_macros_documentation.md), an **Enterprise-only** macro in the Beta engine. See below. |

The body is ordinary JSON: parse it once. `result` is already the finished script, so write it straight to a file without decoding it further.

#### keys

A script that uses [`CTN_HTTP_ENCRYPT_FUNCTION`](centurion_macros_documentation.md) has its keys generated at build time. This macro is **Enterprise only**, must be enabled for your API key by request, and requires `"node": "beta"`. The keys come back in a `keys` object, indexed by the name you passed as the macro's second argument:

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

Select `beta` for features documented as Beta only. Beta access requires a paid plan; [`CTN_HTTP_ENCRYPT_FUNCTION`](centurion_macros_documentation.md) additionally requires Enterprise access and activation for your API key. When a node isn't available, a request that targets it is **rejected** with an invalid-node error - the build never silently falls back to `main`.

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

Centurion offers subscription plans and pay-as-you-go tokens. Every paid plan unlocks the standard obfuscation options. Enterprise also offers additional features, including [`CTN_HTTP_ENCRYPT_FUNCTION`](centurion_macros_documentation.md), which is **Enterprise only** and enabled for your API key by request.

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

