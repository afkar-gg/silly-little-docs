# Executors & sUNC Unified API Documentation

Scripting API reference for Luau executors, combining sUNC with Potassium, Madium, Volt, and Kawaii.

| Source | Official Documentation | Scope & Notes | Last Synced |
| :--- | :--- | :--- | :--- |
| sUNC | https://docs.sunc.io/ (Mirrors: https://docs.sunc.su/, https://github.com/sUNC-Utilities/docs) | 81 actively tested standard functions across 13 core libraries, sUNC v2.0 Test Suite, Rubiš verification, Metadata API, and Embeddable Widget | 2026-11-19 |
| Potassium | https://docs.potassium.pro/ | Full UNC/sUNC implementation + Actors, DrawingImmediate, GPU HLSL Shaders, OTH, `PsmSignal`, RakNet, Regex, Binary Reflection | 2026-11-19 |
| Madium | https://getmadium.net/docs/ | 527 documented functions across 26 libraries including 217 ImGui bindings, Async Filesystem, File Dialogs, Directory Watcher, Hook Filters, Time (`Duration`/`Stopwatch`/`Timer`), `MadiumSignal`, RakNet, MCP Server | 2026-11-19 |
| Volt | https://docs.voltbz.net/docs (Mirror: https://docs.volt.rs/docs) | sUNC-aligned scripting environment + Async SSA/AST Decompiler, `ProtoProxy`, `trampoline_call`, `LuaStateProxy`, `VoltSignal`, `DrawFont.Register`, settings-gated `bit` & `raknet`, Account Manager | 2026-11-19 |
| Kawaii | https://docs.paramore.su/ | 396 functions and 254 aliases across 31 categories including Voice Chat (WebRTC/RTP), JSON (20 APIs), Zstandard encoding, Hook Filters, File Dialogs, ImGui, Bit, RakNet, and Synapse compatibility | 2026-11-19 |

---

## Table of Contents

- [Overview & Executor Comparison Matrix](#overview--executor-comparison-matrix)
  - [Conventions & Execution Models](#conventions--execution-models)
- [sUNC Standard & Test Suite](#sunc-standard--test-suite)
  - [Running the sUNC v2.0 Test Script](#running-the-sunc-v20-test-script)
  - [Selective Function Testing](#selective-function-testing)
  - [Test Results & Rubiš Verification](#test-results--rubiš-verification)
  - [sUNC Integration Suite (Metadata API & Widget)](#sunc-integration-suite-metadata-api--widget)
  - [Quick Reference: All 81 sUNC Functions by Library](#quick-reference-all-81-sunc-functions-by-library)
- [Actors & Lua States Library](#actors--lua-states-library)
  - [`LuaStateProxy` & `ChannelReceiver` Interfaces](#luastateproxy--channelreceiver-interfaces)
- [Bit Library](#bit-library)
- [Cache Library](#cache-library)
- [Closures & Hooking Library](#closures--hooking-library)
  - [sUNC Closure Constraints](#sunc-closure-constraints)
  - [`trampoline_call` Specification](#trampoline_call-specification)
- [Hook Filters Library](#hook-filters-library)
- [Console Library](#console-library)
- [Encoding, Cryptography & JSON Libraries](#encoding-cryptography--json-libraries)
  - [Encoding Library (sUNC Standard + Zstandard)](#encoding-library-sunc-standard--zstandard)
  - [Crypt Library (Legacy / Extended)](#crypt-library-legacy--extended)
  - [JSON Library (Kawaii)](#json-library-kawaii)
- [Debug Library](#debug-library)
  - [`debug.getinfo` Table Fields](#debuggetinfo-table-fields)
  - [`ProtoProxy` (Volt)](#protoproxy-volt)
- [Drawing & DrawingImmediate Libraries](#drawing--drawingimmediate-libraries)
  - [Drawing Objects & Properties](#drawing-objects--properties)
  - [Shader Object & HLSL Pipeline (Potassium)](#shader-object--hlsl-pipeline-potassium)
  - [DrawingImmediate Library](#drawingimmediate-library)
- [ImGui Library](#imgui-library)
  - [Madium Dear ImGui API (217 Functions)](#madium-dear-imgui-api-217-functions)
  - [Kawaii ImGui API (13 Functions)](#kawaii-imgui-api-13-functions)
- [Environment Library](#environment-library)
  - [`filtergc` Specifications](#filtergc-specifications)
- [FileSystem, Directory Watcher & File Dialogs Libraries](#filesystem-directory-watcher--file-dialogs-libraries)
  - [FileSystem Library](#filesystem-library)
  - [Directory Watcher Library (Madium)](#directory-watcher-library-madium)
  - [File Dialogs Library (Madium & Kawaii)](#file-dialogs-library-madium--kawaii)
- [Input Library](#input-library)
- [Instances Library](#instances-library)
- [Metatable & Table Library](#metatable--table-library)
- [Miscellaneous, Runtime & SaveInstance Libraries](#miscellaneous-runtime--saveinstance-libraries)
  - [HTTP Request & Response Shapes](#http-request--response-shapes)
  - [SaveInstance & SavePlace Options](#saveinstance--saveplace-options)
- [Oth Library (Off-Thread Hooking)](#oth-library-off-thread-hooking)
- [Custom Signals (`PsmSignal`, `MadiumSignal`, `VoltSignal`, Kawaii)](#custom-signals-psmsignal-madiumsignal-voltsignal-kawaii)
- [RakNet Library](#raknet-library)
  - [`RakNetPacket` Object](#raknetpacket-object)
- [Reflection & Properties Library](#reflection--properties-library)
- [Regex Library](#regex-library)
- [Scripts & Decompiler Library](#scripts--decompiler-library)
  - [Volt Asynchronous Decompiler Pipeline](#volt-asynchronous-decompiler-pipeline)
- [Signals Library](#signals-library)
  - [`Connection` Object Shape](#connection-object-shape)
  - [Signal Whitelist Info](#signal-whitelist-info)
- [Time Library (Madium)](#time-library-madium)
- [Voice Chat Library (Kawaii)](#voice-chat-library-kawaii)
- [WebSocket Library](#websocket-library)
- [Compatibility & Platform Extras](#compatibility--platform-extras)
- [Security & Operational Notes](#security--operational-notes)

---

## Overview & Executor Comparison Matrix

Support tags used in function tables: `sUNC` (official sUNC v2.0 standard), `Pot` (Potassium), `Mad` (Madium), `Volt` (Volt), and `Kaw` (Kawaii).

| Library / Feature | sUNC | Potassium | Madium | Volt | Kawaii |
| :--- | :---: | :---: | :---: | :---: | :---: |
| Closures & Hooking | 11 funcs | Yes | 17 funcs | 18 funcs (`trampoline_call`) | 22 funcs |
| Debug | 10 funcs | 18 funcs | 19 funcs | 15 funcs + `ProtoProxy` | 21 funcs |
| Drawing (Retained) | 5 funcs | Yes + `Font`/`Shader` | Yes | Yes + `DrawFont.Register` | 21 funcs (combined) |
| DrawingImmediate | - | 12 funcs | 12 funcs | Yes | Yes |
| HLSL GPU Shaders | - | `Drawing.new("Shader")` | - | - | - |
| ImGui | - | - | 217 funcs + constants | - | 13 funcs |
| Encoding (Base64 / LZ4) | 4 funcs | Via `crypt`/aliases | Via `crypt`/globals | 4 funcs | 6 funcs (+ Zstandard) |
| Crypt (AES / Hash / HMAC) | Deprecated | 11 funcs | 11 funcs | 7 funcs | 7 funcs |
| JSON | - | - | - | - | 20 funcs |
| Bit (32-bit unsigned) | - | - | - | 16 funcs *(settings-gated)* | 16 funcs |
| Environment & GC | 5 funcs | 7 funcs | 19 funcs (+ refs/orig env) | 7 funcs (+ `getallthreads`) | 18 funcs |
| Filesystem | 11 funcs | 11 funcs (+ `dofile`) | 19 funcs (+ Async IO) | 12 funcs | 22 funcs |
| Directory Watcher | - | - | 2 funcs | - | - |
| File Dialogs | - | - | 13 funcs | - | 13 funcs |
| Hook Filters | - | - | 12 funcs | - | 10 funcs |
| Input (Keyboard / Mouse) | - | 13 funcs | 13 funcs | Yes | 20 funcs (+ clipboard) |
| Instances | 9 funcs | 9 funcs | In Environment/Cache | 11 funcs | 17 funcs |
| Cache | - | 5 funcs | 6 funcs | 3 funcs | 4 funcs |
| Metatable & Table | 5 funcs | 9 funcs | 15 funcs | 9 funcs | 14 funcs |
| Console (`rconsole*`) | - | 9 funcs | 13 funcs | 12 funcs | 14 funcs |
| Miscellaneous / Runtime | 2 funcs | 16 funcs | 19 funcs + 3 SaveInstance | 15 funcs | 7 Runtime + 2 HTTP |
| Off-Thread Hooking (`oth`) | - | 5 funcs | 5 funcs | 5 funcs | 5 funcs |
| Actors & Lua States | - | 8 funcs | 18 funcs (+ `LuaStateProxy`) | 10 funcs + `LuaStateProxy` | 10 Actors + 8 Lua states |
| Custom Signals | - | `PsmSignal` | `MadiumSignal` | `VoltSignal` | Custom signals (12 funcs) |
| Signals (RBXScriptSignal) | 3 funcs | 8 funcs | 18 funcs (+ `hooksignal`) | 7 funcs | 18 funcs |
| Reflection & Properties | 6 funcs | 13 funcs (+ `getbspval`/`getpcd`) | 14 funcs | 10 funcs | 10 funcs |
| Regex | - | 5 funcs | 5 funcs | - | 5 funcs |
| Scripts & Decompiler | 9 funcs | 13 funcs | 17 funcs | 15 funcs + SSA/AST pipeline | 14 funcs |
| RakNet | - | 5 funcs (send + recv) | 10 funcs (send + recv + schema) | 4 funcs (send, *settings-gated*) | 16 funcs |
| Time (`Duration`/`Stopwatch`) | - | - | 15 funcs | - | - |
| Voice Chat (WebRTC/RTP) | - | - | - | - | 18 funcs |
| WebSocket | 1 ctor (`wss://`) | Yes | 3 funcs | Yes (`websocket` alias) | 3 funcs |
| Synapse Compatibility | - | - | - | - | 10 funcs *(settings-gated)* |

### Conventions & Execution Models

Function signatures use typed Luau notation (for example, `readfile(path: string): string`) with `?` for optional parameters. Core functions live in `getgenv()`, while grouped APIs sit under namespaces including `bit`, `cache`, `crypt`, `debug`, `Drawing`, `DrawingImmediate`, `ImGui`, `oth`, `raknet`, `Regex`, `voicechat`, and `WebSocket`.

Three libraries are disabled by default and must be turned on in executor client settings before use: `raknet` (Potassium, Volt, Madium, Kawaii; check `raknet.is_enabled()` where available), `bit` (Volt), and the `Synapse` compatibility layer (Kawaii).

Kawaii tags every function by how it interacts with the calling coroutine:

| Execution Mode | Coroutine Behavior |
| :--- | :--- |
| Synchronous | Returns without yielding the Luau coroutine, though native work can still block briefly. |
| Asynchronous | May yield the calling coroutine until native or background work finishes (e.g., `request`, `decompile`, `rconsoleinput`, `readfileasync`, deferred `voicechat.incomingpacket:Connect` callbacks). |
| Conditional | May yield if an invoked callback or delegated engine call yields. |
| Background | Returns immediately without waiting for later work or callbacks. |

---

## sUNC Standard & Test Suite

Source: https://docs.sunc.io/ (Mirrors: https://docs.sunc.su/, https://github.com/sUNC-Utilities/docs)

sUNC (*senS' Unified Naming Convention*) specifies the executor API functions verified by the official sUNC test suite.

> [!IMPORTANT]
> sUNC covers only functions actively tested by the official sUNC script. Unlike the legacy UNC standard, which became prone to shallow spoofing, sUNC tests real-world behavior, tightens signatures and return types, and removes deprecated namespaces such as `crypt`.

### Running the sUNC v2.0 Test Script

```lua
getgenv().sUNCDebug = {
    ["printcheckpoints"] = false,
    ["delaybetweentests"] = 0,
    ["printtesttimetaken"] = false,
}

loadstring(game:HttpGet("https://script.sunc.io/"))()
```

*Official mirror fallback:*
`https://gitlab.com/sens3/nebunu/-/raw/main/HummingBird8's_sUNC_yes_i_moved_to_gitlab_because_my_github_acc_got_brickedd/sUNCm0m3n7.lua`

> [!NOTE]
> As of sUNC v2.0, verification tests only run inside the official sUNC testing game (distributed via the sUNC Discord server).

### Selective Function Testing

Use `sUNCDebug.functionstotest` to isolate specific functions or dependent test groups:

```lua
getgenv().sUNCDebug = {
    ["printcheckpoints"] = false,
    ["delaybetweentests"] = 0,
    ["printtesttimetaken"] = false,
    ["functionstotest"] = {
        ["cloneref"] = true,
        ["filesys"] = true,
        ["Drawing.new"] = true,
    },
}
```

- When `functionstotest` is empty or `nil`, the entire test suite runs.
- Certain keys select entire functional groups because their tests share dependencies (e.g., `"filesys"` selects all filesystem tests, `"Drawing.new"` selects all drawing primitives and utility functions, `"cloneref"` selects instance reference tests).
- Unselected functions are reported as *untested* in results.

### Test Results & Rubiš Verification

Upon completion, sUNC outputs a short redirect link (`r.sunc.io/<id>`) pointing to Numelon Rubiš (`https://rubis.app`):
- Every result is cryptographically signed by the dedicated sUNC game server to prevent falsified or spoofed reports.
- Tampered or synthetic results are flagged as unverified.
- Detailed failure diagnostics appear at the top of the in-game Developer Console and on the Rubiš results page.

### sUNC Integration Suite (Metadata API & Widget)

The sUNC documentation site (`https://docs.sunc.io/api/`) hosts JSON metadata and an embeddable status widget:

| Endpoint / Tool | URL | Description |
| :--- | :--- | :--- |
| Metadata API (Mini, ~10 KB) | `https://docs.sunc.io/api/mini.json` | JSON dictionary mapping function names to descriptions. |
| Metadata API (Jumbo, ~45 KB) | `https://docs.sunc.io/api/jumbo.json` | Full JSON dataset with library overviews, typed signatures, parameter descriptions, and doc anchors. |
| Embeddable Widget | `https://docs.sunc.io/About/Integration-Suite/Widget/` | Reusable status-page widget configurable via its browser `postMessage` API and typed Widget API messages. |

### Quick Reference: All 81 sUNC Functions by Library

| Library | Count | sUNC Standardized Functions |
| :--- | :---: | :--- |
| Closures | 11 | `checkcaller`, `clonefunction`, `getfunctionhash`, `hookfunction`, `hookmetamethod`, `iscclosure`, `isexecutorclosure`, `islclosure`, `loadstring`, `newcclosure`, `restorefunction` |
| Debug | 10 | `debug.getconstant`, `debug.getconstants`, `debug.getproto`, `debug.getprotos`, `debug.getstack`, `debug.getupvalue`, `debug.getupvalues`, `debug.setconstant`, `debug.setstack`, `debug.setupvalue` |
| Drawing | 5 | `Drawing.new`, `cleardrawcache`, `getrenderproperty`, `isrenderobj`, `setrenderproperty` |
| Encoding | 4 | `base64decode`, `base64encode`, `lz4compress`, `lz4decompress` |
| Environment | 5 | `filtergc`, `getgc`, `getgenv`, `getreg`, `getrenv` |
| Filesystem | 11 | `appendfile`, `delfile`, `delfolder`, `getcustomasset`, `isfile`, `isfolder`, `listfiles`, `loadfile`, `makefolder`, `readfile`, `writefile` |
| Instances | 9 | `cloneref`, `compareinstances`, `fireclickdetector`, `fireproximityprompt`, `firetouchinterest`, `getcallbackvalue`, `gethui`, `getinstances`, `getnilinstances` |
| Metatable | 5 | `getnamecallmethod`, `getrawmetatable`, `isreadonly`, `setrawmetatable`, `setreadonly` |
| Miscellaneous | 2 | `identifyexecutor`, `request` |
| Reflection | 6 | `gethiddenproperty`, `getthreadidentity`, `isscriptable`, `sethiddenproperty`, `setscriptable`, `setthreadidentity` |
| Scripts | 9 | `getcallingscript`, `getloadedmodules`, `getrunningscripts`, `getscriptbytecode`, `getscriptclosure`, `getscriptfromthread`, `getscripthash`, `getscripts`, `getsenv` |
| Signals | 3 | `firesignal`, `getconnections`, `replicatesignal` |
| WebSocket | 1 | `WebSocket.connect` |

---

## Actors & Lua States Library

Functions for managing Roblox Parallel Luau `Actor` instances, isolated Lua states (`LuaStateProxy`), inter-actor communication channels, and cross-state execution.

| Function | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `create_comm_channel` | `(): (number, BindableEvent \| ChannelReceiver)` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Creates a cross-actor communication channel and returns its numeric ID and receiver/event object. |
| `get_comm_channel` | `(id: number): BindableEvent \| ChannelReceiver` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Retrieves an existing communication channel by its numeric ID. |
| `getactors` | `(): { Actor }` | `get_actors` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns all active `Actor` instances currently tracked by the client (excluding deleted actors). |
| `getdeletedactors` | `(): { Actor }` | `get_deleted_actors` | `Pot`, `Mad` | Returns a table of actors that were deleted during runtime. |
| `get_current_actor` | `(): Actor?` | - | `Mad`, `Volt`, `Kaw` | Returns the `Actor` associated with the current Lua state, or `nil` on the main state. |
| `getactorfromthread` | `(thread: thread): Actor?` | - | `Mad` | Resolves the `Actor` owning the specified Luau thread. |
| `getactorthreads` | `(): { thread \| userdata }` | `get_actor_threads` | `Pot`, `Mad` | Returns all Luau threads associated with active actors. |
| `getactorstates` | `(): { LuaStateProxy }` | - | `Mad`, `Volt`, `Kaw` | Returns `LuaStateProxy` handles for all active actor Lua states. |
| `getgamestate` | `(): LuaStateProxy` | - | `Mad`, `Volt`, `Kaw` | Returns the `LuaStateProxy` for the default main game Lua state. |
| `getluastate` | `(owner: (Actor \| LuaSourceContainer)?): LuaStateProxy?` | `get_lua_state` | `Mad`, `Volt`, `Kaw` | Returns the `LuaStateProxy` for the current state, or for the given `Actor` or script. |
| `is_parallel` | `(): boolean` | `isparallel`, `checkparallel` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if the current code is executing in parallel within an actor context. (`Pot`: `is_parallel`, `Volt`: `isparallel`, `Mad`: `checkparallel`.) |
| `on_actor_state_created` | `VoltSignal<LuaStateProxy>` | - | `Volt` | Read-only signal fired whenever a new Actor Lua state becomes available (does not expose `:Fire`). |
| `run_on_actor` | `(actor: Actor, script: string, ...: any): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Executes Luau source code inside the target `Actor`'s state, forwarding extra arguments to `...`. |
| `run_on_thread` | `(thread: thread \| userdata, script: string, ...: any): ()` | `runonthread` | `Pot`, `Mad`, `Kaw` | Executes Luau source code on a specific actor thread. (`Pot`: `run_on_thread`, `Mad`: `runonthread`.) |

### `LuaStateProxy` & `ChannelReceiver` Interfaces

Supported in Volt, Madium, and Kawaii (8 functions in Kawaii's Lua states category):

| Member | Type / Signature | Description |
| :--- | :--- | :--- |
| `LuaStateProxy.new()` | `(): LuaStateProxy` | Creates a `LuaStateProxy` representing the current Lua state. |
| `proxy.Id` | `number` | Unique numeric identifier for this Lua state (Volt). |
| `proxy.IsActorState` | `boolean` | `true` if this state was created for parallel Actors (Volt). |
| `proxy.Event` | `VoltSignal` | Generic cross-state signal for communicating between Lua states (Volt). |
| `proxy:GetActors()` | `(): { Actor }` | Returns all `Actor` instances belonging to this Lua state. |
| `proxy:Execute(code, ...)` | `(source: string, ...: any): ()` | Compiles and executes Luau code inside this Lua state. |
| `ChannelReceiver:Connect(fn)` | `(callback: function): Connection` | Connects a listener to a Madium comm channel receiver. |
| `ChannelReceiver:Fire(...)` | `(...: any): ()` | Dispatches payload data across a Madium comm channel. |

#### Example: Cross-Actor Communication

```lua
local id, channel = create_comm_channel()
channel.Event:Connect(function(data)
    print("Received from actor:", data)
end)

run_on_actor(getactors()[1], [[
    local id = ...
    local channel = get_comm_channel(id)
    channel:Fire("Hello from Actor!")
]], id)
```

---

## Bit Library

32-bit unsigned integer bitwise and arithmetic library provided by Volt (disabled by default; enable Bit Library in Volt's client settings) and Kawaii (16 functions). Results are truncated to 32 bits; variadic functions in Volt skip arguments that cannot be converted to unsigned integers.

| Function | Signature | Support | Description |
| :--- | :--- | :--- | :--- |
| `bit.badd` | `(...: number): number` | `Volt`, `Kaw` | 32-bit wrapping unsigned addition. |
| `bit.bsub` | `(...: number): number` | `Volt`, `Kaw` | 32-bit wrapping unsigned subtraction. |
| `bit.bmul` | `(...: number): number` | `Volt`, `Kaw` | 32-bit wrapping unsigned multiplication. |
| `bit.bdiv` | `(...: number): number` | `Volt`, `Kaw` | 32-bit unsigned integer division. |
| `bit.band` | `(...: number): number` | `Volt`, `Kaw` | Bitwise AND across all arguments. |
| `bit.bor` | `(...: number): number` | `Volt`, `Kaw` | Bitwise OR across all arguments. |
| `bit.bxor` | `(...: number): number` | `Volt`, `Kaw` | Bitwise XOR across all arguments. |
| `bit.bnot` | `(value: number): number` | `Volt`, `Kaw` | Bitwise NOT (ones' complement) of `value`. |
| `bit.bswap` | `(value: number): number` | `Volt`, `Kaw` | Reverses the byte order (endianness) of a 32-bit integer. |
| `bit.lshift` | `(value: number, count: number): number` | `Volt`, `Kaw` | Logical left shift by `count` bits. |
| `bit.rshift` | `(value: number, count: number): number` | `Volt`, `Kaw` | Logical right shift by `count` bits (zero-fill). |
| `bit.arshift` | `(value: number, count: number): number` | `Volt`, `Kaw` | Arithmetic right shift by `count` bits (sign-extending). |
| `bit.rol` | `(value: number, count: number): number` | `Volt`, `Kaw` | Bitwise rotate left by `count` bits. |
| `bit.ror` | `(value: number, count: number): number` | `Volt`, `Kaw` | Bitwise rotate right by `count` bits. |
| `bit.tobit` | `(value: number): number` | `Volt`, `Kaw` | Normalizes a number into a signed 32-bit integer range. |
| `bit.tohex` | `(value: number, digits: number?): string` | `Volt`, `Kaw` | Converts a 32-bit integer to its hexadecimal string representation. |

---

## Cache Library

Operates on the Roblox/executor internal `Instance` userdata reference cache.

| Function | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `cache.invalidate` | `(instance: Instance): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Removes `instance` from the internal reference cache; future lookups create a distinct wrapper (`old == new` becomes `false`). |
| `cache.iscached` | `(instance: Instance): boolean` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `instance` is currently present in the reference cache. |
| `cache.replace` | `(target: Instance, replacement: Instance): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Replaces the cached reference for `target` with `replacement`. |
| `getinstancecache` | `(): { [any]: Instance }` | - | `Mad`, `Volt`, `Kaw` | Returns a snapshot/table of cached instance references. |
| `cloneref` | `<T>(object: T & Instance): T` | `clonereference` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Creates an isolated reference clone to an `Instance` (`object == cloneref(object)` is `false`). |
| `compareinstances` | `(a: Instance, b: Instance): boolean` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if two references point to the same underlying Roblox engine object, even across `cloneref`. |

---

## Closures & Hooking Library

Primitives to inspect, clone, wrap, hook, restore, and invoke Luau and C closures, metamethods, and nested prototypes.

### sUNC Closure Constraints

- Every Luau function is a closure (bytecode prototype + captured upvalues). Native C closures are opaque; bytecode inspection (`debug.*`, `getfunctionhash`) must throw an error on C closures.
- `newcclosure` must wrap Luau closures into C-level closures with zero upvalues, full yieldability (e.g., `task.wait()`), and native C error formatting. Pure-Luau wrappers (such as `coroutine.wrap`) fail sUNC verification.
- Hook callbacks passed to `hookfunction` must not carry more upvalues than the target function unless wrapped via `newcclosure`.
- `loadstring` must not throw on invalid syntax; it returns `(nil, errorMessage)` and marks the calling environment unsafe.

### Functions

| Function | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `checkcaller` | `(): boolean` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if the current thread/call originated from the executor environment. |
| `clonefunction` | `<A..., R...>(func: (A...) -> R...): (A...) -> R...` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Clones a closure with identical behavior and environment; subsequent hooks to the original do not affect the clone. |
| `comparefunctions` | `(a: function, b: function): boolean` | - | `Mad`, `Kaw` | Compares whether two closures refer to the same underlying function/proto. |
| `getfunctionhash` | `(func: function): string` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the 96-character hex SHA-384 hash of a Luau closure's bytecode instructions and constants. Errors on C closures. |
| `hookfunction` | `<A1..., R1..., A2..., R2...>(target: (A1...) -> R1..., hook: (A2...) -> R2...): (A1...) -> R1...` | `replaceclosure` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Replaces `target` with `hook` and returns the original unhooked function. Must support `L->L`, `L->C`, `C->L`, and `C->C` pairs. |
| `hookmetamethod` | `(object: table \| userdata \| Object, metamethod: string, hook: function): function` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Hooks a metamethod (e.g., `"__index"`, `"__newindex"`, `"__namecall"`) on `object`'s metatable and returns the original metamethod. |
| `hookproto` | `(proto: function \| ProtoProxy, hook: function): function` | - | `Mad`, `Volt`, `Kaw` | Hooks a nested Luau prototype directly so future and active closures sharing that proto redirect to `hook`. |
| `iscclosure` | `(func: function): boolean` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `func` is a native C closure. |
| `isexecutorclosure` | `(func: function): boolean` | `checkclosure`, `isourclosure` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `func` was created by the executor (including `loadstring` and `getscriptclosure` outputs). |
| `isfunctionhooked` | `(target: function): boolean` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `target` is currently hooked. |
| `islclosure` | `(func: function): boolean` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `func` is a Luau closure. |
| `isnewcclosure` | `(func: function): boolean` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `func` was created via `newcclosure`. |
| `isourthread` | `(thread: thread): boolean` | `isexecutorthread` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `thread` was created by the executor. |
| `isprotohooked` | `(proto: function \| ProtoProxy): boolean` | - | `Mad`, `Volt`, `Kaw` | Returns `true` if the specified proto is currently hooked via `hookproto`. |
| `isstackhidden` | `(func: function \| number): boolean` | - | `Mad`, `Kaw` | Returns `true` if a function or stack frame is hidden from call-stack inspection. |
| `loadstring` | `<A...>(source: string, chunkname: string?): (((A...) -> any)?, string?)` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Compiles Luau source code into a callable closure; returns `(nil, err)` on compile failure. |
| `newcclosure` | `<A..., R...>(func: (A...) -> R..., name: string?): (A...) -> R...` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Wraps a Luau closure into a zero-upvalue yieldable C closure. |
| `newlclosure` | `(func: function, name: string?): function` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Wraps a function (C or Luau) into a new Luau closure. |
| `restorefunction` | `(func: function): ()` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Restores a hooked function back to its initial original implementation (even across chained hooks). Errors if not hooked. |
| `restoreproto` | `(proto: function \| ProtoProxy): ()` | - | `Mad`, `Volt`, `Kaw` | Restores a prototype previously hooked via `hookproto`. |
| `setstackhidden` | `(func: function \| number, hidden: boolean): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Hides or unhides a function or call-stack level from `debug.info` / call-stack detections. |
| `trampoline_call` | `(func: function, callstack: {TrampolineCallFrame}, thread_options: TrampolineThreadOptions, ...: any): (boolean, ...any)` | - | `Mad`, `Volt`, `Kaw` | Invokes `func` through a synthetic trampoline with spoofed call-stack frames and thread context. |

### `trampoline_call` Specification

Supported in Volt, Madium, and Kawaii:

```luau
type TrampolineCallFrame = {
    func: (...any) -> ...any, -- Sets frame name, source, and debug details (not executed)
    currentline: number,      -- Required even for C frames
}

type TrampolineThreadOptions = {
    script: Instance?,        -- LuaSourceContainer context returned by getcallingscript()
    identity: number?,        -- Thread identity while func runs
    env: { [any]: any }?,     -- Global environment table used by the call
    thread: thread?,          -- Parent thread; overrides script, identity, and env when set
}
```

- Both `callstack` and `thread_options` are required tables (pass `{}` when unneeded).
- Returns `(true, ...results)` on success or `(false, errorValue)` if `func` errors.
- `callstack` must be a dense 1-based array where index `1` is closest to `func`. Note: `debug.getcallstack()` omits `currentline` for C frames, so filter or supply a line number before forwarding.

```lua
local fake_script = Instance.new("LocalScript")
local function test()
    print(getcallingscript() == fake_script) -- true
    print(debug.info(2, "nl"))               -- fake_caller, 10
end
local function fake_caller() end

trampoline_call(
    test,
    { { func = fake_caller, currentline = 10 } },
    { script = fake_script }
)
```

---

## Hook Filters Library

Declarative call-matching rules for hooks that run natively, skipping Luau callbacks on non-matching calls. Supported by Madium (12 functions) and Kawaii (10 filter constructors).

> Filters are evaluated when the wrapped or hooked function runs; constructing a filter does not invoke either branch.

| Function | Signature | Support | Description |
| :--- | :--- | :--- | :--- |
| `AllFilter.new` | `(filters: { Filter }): AllFilter` | `Mad`, `Kaw` | Logical AND: matches only if all child filters match. |
| `AnyFilter.new` | `(filters: { Filter }): AnyFilter` | `Mad`, `Kaw` | Logical OR: matches if at least one child filter matches. |
| `NotFilter.new` | `(filter: Filter): NotFilter` | `Mad`, `Kaw` | Logical NOT: negates a child filter. |
| `ArgCountFilter.new` | `(count: number, ...): ArgCountFilter` | `Mad`, `Kaw` | Matches invocations by argument count. |
| `ArgumentFilter.new` | `(argument_index: integer, argument: any): ArgumentFilter` | `Mad`, `Kaw` | Matches when the 1-based argument at `argument_index` equals `argument`. |
| `CallerFilter.new` | `(invert: boolean?): CallerFilter` | `Mad`, `Kaw` | Matches based on whether the caller is an executor or game thread. |
| `InstanceNameFilter.new` | `(argument_index: integer, name: string): InstanceNameFilter` | `Mad`, `Kaw` | Matches when the argument at `argument_index` is an `Instance` with matching `.Name`. |
| `InstanceTypeFilter.new` | `(argument_index: integer, className: string): InstanceTypeFilter` | `Mad`, `Kaw` | Matches when the argument at `argument_index` is an `Instance` matching `IsA(className)`. |
| `NamecallFilter.new` | `(method: string): NamecallFilter` | `Mad`, `Kaw` | Matches `__namecall` invocations whose method name equals `method`. |
| `TypeFilter.new` | `(argument_index: integer, typeName: string): TypeFilter` | `Mad`, `Kaw` | Matches when `type(arg)` at `argument_index` equals `typeName`. |
| `UserdataTypeFilter.new` | `(argument_index: integer, typeofName: string): UserdataTypeFilter` | `Mad`, `Kaw` | Matches when `typeof(arg)` at `argument_index` equals `typeofName`. |
| `getfilter` | `(filter: Filter, onMatch: function, onOriginal: function): function` | `Mad` | Constructs a native-dispatched filtered wrapper/hook using the specified filter tree. |

---

## Console Library

Standalone OS console window for logging, color-coded diagnostics, and blocking user input. Supported by Potassium, Madium, Volt, and Kawaii (not part of sUNC).

| Function | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `rconsolecreate` | `(): ()` | `rconsoleshow`, `consolecreate` | `Pot`, `Mad`, `Volt`, `Kaw` | Allocates/opens and shows the console window. Only one console can exist at a time; prior output is preserved when re-shown. |
| `rconsolehide` | `(): ()` | - | `Mad`, `Volt`, `Kaw` | Hides the console window without destroying its buffered contents. |
| `rconsolehidden` | `(): boolean` | - | `Mad`, `Volt`, `Kaw` | Returns `true` if the console window is currently hidden or unavailable. |
| `rconsoletoggle` | `(): ()` | - | `Mad`, `Volt`, `Kaw` | Toggles console window visibility between shown and hidden. |
| `rconsoletop` | `(topmost: boolean): ()` | - | `Mad`, `Volt`, `Kaw` | Sets whether the console window stays pinned above other windows. |
| `rconsoledestroy` | `(): ()` | `rconsoleclose` | `Pot`, `Mad`, `Kaw` | Closes and destroys the console window, clearing output and canceling pending `rconsoleinput`. |
| `rconsoleclear` | `(): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Clears all text from the console window. |
| `rconsoleprint` | `(text: string, async: boolean?): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Prints plain text to the console (no automatic trailing newline; use `\n`). Volt accepts optional `async` (default `true`). |
| `rconsoleinfo` | `(text: any, async: boolean?): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Prints an `[INFO]` informational message to the console. |
| `rconsolewarn` | `(text: any, async: boolean?): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Prints a warning message (yellow) to the console. |
| `rconsoleerror` | `(text: any, async: boolean?): ()` | `rconsoleerr` | `Pot`, `Mad`, `Volt`, `Kaw` | Prints an error message (red) to the console. (`Pot`: `rconsoleerror`; `Mad`/`Volt`: `rconsoleerr`.) |
| `rconsoleinput` | `(): string` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Yields the calling thread until a line of text is entered in the console, returning the input string. |
| `rconsolesettitle` | `(title: string): ()` | `rconsolename` | `Pot`, `Mad`, `Volt`, `Kaw` | Sets the title bar text of the console window. |

---

## Encoding, Cryptography & JSON Libraries

### Encoding Library (sUNC Standard + Zstandard)

Binary-to-text encoding and lossless compression functions.

> [!NOTE]
> sUNC deprecated and removed the `crypt` table because global cryptography hooks can be redefined by other scripts to intercept sensitive data. sUNC exposes Base64 and LZ4 as top-level globals, advises inlining pure-Luau cryptography when confidentiality is needed, and warns if a `crypt` table exists.

| Function | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `base64encode` | `(data: string): string` | `crypt.base64encode`, `base64.encode` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Encodes raw binary or ASCII string data into Base64. |
| `base64decode` | `(data: string): string` | `crypt.base64decode`, `base64.decode` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Decodes a Base64 string back into raw bytes. |
| `lz4compress` | `(data: string): string` | `crypt.lz4compress` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Compresses a string using lossless LZ4 compression. |
| `lz4decompress` | `(data: string, originalLength: number?): string` | `crypt.lz4decompress` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Decompresses LZ4 data. sUNC does not require `originalLength`, whereas Potassium and Volt require `size`/`originalLength` to be at least `#original` bytes. |
| *Zstandard compress/decompress* | *(Kawaii Encoding, 6 total functions)* | - | `Kaw` | Kawaii extends Base64 and LZ4 with Zstandard (zstd) compression and decompression functions. |

### Crypt Library (Legacy / Extended)

Provided by Potassium, Madium, Volt, and Kawaii for compatibility with legacy UNC scripts.

| Function | Signature | Support | Description |
| :--- | :--- | :--- | :--- |
| `crypt.encrypt` | `(data: string, key: string, iv: string?, mode: string?): (string, string)` | `Pot`, `Mad`, `Volt`, `Kaw` | AES-encrypts `data` using Base64 `key`, optional `iv`, and `mode` (`"CBC"` default, `"ECB"`, `"CTR"`, `"CFB"`, `"OFB"`, `"GCM"`). Returns `(ciphertextBase64, ivBase64)`. |
| `crypt.decrypt` | `(data: string, key: string, iv: string, mode: string): string` | `Pot`, `Mad`, `Volt`, `Kaw` | Decrypts Base64 AES ciphertext with matching `key`, `iv`, and `mode`. |
| `crypt.generatebytes` | `(amount: number): string` | `Pot`, `Mad`, `Volt`, `Kaw` | Generates `amount` cryptographically random bytes returned as Base64. |
| `crypt.generatekey` | `(keylength: number?): string` | `Pot`, `Mad`, `Volt`, `Kaw` | Generates a random 32-byte AES key returned as Base64. |
| `crypt.random` | `(amount: number): string` | `Pot`, `Mad`, `Volt`, `Kaw` | Generates `amount` raw random bytes (not Base64 encoded). |
| `crypt.hash` | `(data: string, algorithm: string): string` | `Pot`, `Mad`, `Volt`, `Kaw` | Computes a digest using algorithms such as `md5`, `sha1`, `sha224`, `sha256`, `sha384`, `sha512`, `sha3-224`, `sha3-256`, `sha3-384`, `sha3-512`. |
| `crypt.hmac` | `(key: string, data: string, algorithm: string): string` | `Pot`, `Mad`, `Volt`, `Kaw` | Computes a Hash-based Message Authentication Code (HMAC). |

### JSON Library (Kawaii)

Kawaii provides a 20-function JSON category for converting between JSON documents and Luau values, validating input, and formatting saved JSON data natively without `HttpService:JSONEncode` or `JSONDecode`.

---

## Debug Library

Luau VM bytecode inspection and runtime stack/constant/upvalue/proto manipulation.

### Constraints (sUNC)

- All `debug.*` bytecode functions operate strictly on Luau closures and throw an error on native C closures (e.g., `print`).
- Functions accepting `func` also accept a numeric call-stack `level` (`1` = immediate caller).
- Prototypes returned by `debug.getproto` without `activated = true` are inactive (in Volt, a `ProtoProxy`) and must not be executed.
- Volt, Potassium, and Madium also register `debug.*` extensions as global aliases (`getconstant`, `getupvalue`, `getinfo`, `setstack`, etc.).

### Functions

| Function | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `debug.getconstant` | `(func: function \| number \| ProtoProxy, index: number): any` | `getconstant` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the constant at 1-based `index`, or `nil` if out of bounds. |
| `debug.getconstants` | `(func: function \| number \| ProtoProxy): { any }` | `getconstants` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns an array of all constants embedded in the closure's bytecode. |
| `debug.getproto` | `(func: function \| number \| ProtoProxy, index: number, activated: boolean?): function \| ProtoProxy \| { function }` | `getproto` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the nested prototype at `index` (inactive / `ProtoProxy`), or a table of active instantiated closures if `activated = true`. |
| `debug.getprotos` | `(func: function \| number \| ProtoProxy): { function \| ProtoProxy }` | `getprotos` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns all inner function prototypes declared within `func`. |
| `debug.getstack` | `(level: number, index: number?): any \| { any }` | `getstack` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns register `index` at stack frame `level`, or a table of all registers on that frame if `index` is omitted. |
| `debug.getupvalue` | `(func: function \| number, index: number): any` | `getupvalue` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the upvalue at 1-based `index`. Errors on invalid index or C closure. |
| `debug.getupvalues` | `(func: function \| number): { [any]: any }` | `getupvalues` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns a table of all upvalues captured by `func`. |
| `debug.setconstant` | `(func: function \| number \| ProtoProxy, index: number, value: any): ()` | `setconstant` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Overwrites the constant at `index` in the function bytecode. |
| `debug.setstack` | `(level: number, index: number, value: any): ()` | `setstack` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Overwrites register `index` on stack frame `level`. |
| `debug.setupvalue` | `(func: function \| number, index: number, value: any): ()` | `setupvalue` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Replaces upvalue at `index` with `value` (same type required on some executors). |
| `debug.getcallstack` | `(offsetOrThread: (number \| thread)?): { CallFrame }` | `getcallstack` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns visible call frames (`source`, `line`, `what`, `name`, `func`) for the current or specified thread. |
| `debug.getinfo` | `(func: function \| number \| ProtoProxy, what: string?): table` | `getinfo` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns Lua 5.1-style debug metadata table. Filter `what`: `"n"`, `"s"`, `"l"`, `"f"`, `"u"`. |
| `debug.setinfo` | `(func: function \| ProtoProxy, info: table): ()` | `setinfo` | `Pot`, `Mad`, `Volt`, `Kaw` | Mutates debug metadata fields on a function or proto. |
| `debug.setname` | `(func: function \| number, name: string): ()` | `setname` | `Pot`, `Kaw` | Renames a function in debug metadata. |
| `debug.getregistry` | `(): { [any]: any }` | `getregistry`, `getreg` | `Pot`, `Mad`, `Kaw` | Returns the global Luau registry table. |
| `debug.getmetatable` | `(object: any): table?` | `getrawmetatable` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns the raw metatable bypassing `__metatable`. |
| `debug.setmetatable` | `(object: any, meta: table?): any` | `setrawmetatable` | `Pot`, `Mad`, `Volt`, `Kaw` | Sets the raw metatable bypassing `__metatable`. |
| `debug.getsafeenv` | `(object: function \| table \| thread): boolean` | `debug.isuntouched` | `Pot` | Returns the Luau safe-environment flag for an object. |
| `debug.setsafeenv` | `(object: function \| table \| thread \| boolean, safe: boolean?): ()` | `debug.setuntouched` | `Pot` | Sets or modifies the Luau safe-environment flag. |
| `debug.isvalidlevel` | `(level: number): boolean` | `debug.validlevel`, `validlevel`, `isvalidlevel` | `Pot`, `Mad`, `Volt`, `Kaw` | Checks whether a call-stack level index is valid. |
| `getfenv` / `setfenv` | `(target: (function \| number)?): table` | - | `Mad`, `Kaw` *(standard Luau)* | Reads or mutates a function/stack level's environment table (marks environment unsafe). |

### `debug.getinfo` Table Fields

| Field | Type | Meaning |
| :--- | :--- | :--- |
| `source` | `string` | Source chunk / script definition path. |
| `short_src` | `string` | Truncated source path used in diagnostics. |
| `linedefined` | `number` | First source line of the function definition. |
| `what` | `string` | `"Lua"` for Luau closures, `"C"` for C closures, or `"main"` for top-level chunks. |
| `name` | `string` | Function debug name, if determinable. |
| `namewhat` | `string` | Scope context (`"global"`, `"local"`, `"method"`, `"field"`). |
| `nups` | `number` | Number of captured upvalues. |
| `func` | `function` | The closure itself. |
| `currentline` | `number?` | Currently executing line (when `what` includes `"l"`). |
| `numparams` | `number?` | Count of fixed parameters. |
| `is_vararg` | `number?` | Non-zero if the function accepts varargs (`...`). |

### `ProtoProxy` (Volt)

In Volt, `debug.getproto(fn, i)` (with `activated` omitted or `false`) and `debug.getprotos(fn)` return non-executable `ProtoProxy` userdatas (`typeof(proxy) == "ProtoProxy"`). Each proxy exposes `proxy.CodeHash: number`, a signed 32-bit non-cryptographic hash of the proto's instructions for comparing proxies within the same Volt build. Calling `proxy()` returns nothing and does not run the nested function.

`ProtoProxy` values work with `debug.getproto`, `debug.getprotos`, `debug.getconstant`, `debug.getconstants`, `debug.setconstant`, `debug.getinfo`, `debug.setinfo`, Luau's `debug.info`, `hookproto`, `restoreproto`, and `isprotohooked`.

---

## Drawing & DrawingImmediate Libraries

2D overlay rendering pipeline drawn directly over the game viewport outside the Roblox `DataModel`.

| Function | Signature | Support | Description |
| :--- | :--- | :--- | :--- |
| `Drawing.new` | `(type: string): DrawingObject` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Creates a persistent drawing object: `"Line"`, `"Text"`, `"Image"`, `"Circle"`, `"Square"`, `"Quad"`, `"Triangle"` (plus Potassium `"Font"` and `"Shader"`). |
| `Drawing.Fonts` | `{ UI = 0, System = 1, Plex = 2, Monospace = 3 }` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Built-in legacy font ID enum table. |
| `cleardrawcache` | `(): ()` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Destroys and removes all active `Drawing.new` primitives. |
| `getrenderproperty` | `(drawing: DrawingObject, property: string): any` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Dynamically reads a property from a drawing object. |
| `isrenderobj` | `(object: any): boolean` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `object` is an active, valid drawing object. |
| `setrenderproperty` | `(drawing: DrawingObject, property: string, value: any): ()` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Dynamically assigns a property on a drawing object. |
| `DrawFont.Register` | `(data: string, ...): DrawFont` | `Volt` | Registers a custom font directly from raw font bytes in memory for use in `Text.Font`. |

### Drawing Objects & Properties

#### Common Base Properties & Methods

| Member | Type | Notes |
| :--- | :--- | :--- |
| `Visible` | `boolean` | Default `false`. Whether the object renders on screen. |
| `ZIndex` | `number` | Stacking order; higher values render on top. |
| `Transparency` | `number` | Under sUNC, `0` is opaque and `1` is fully transparent (matching Roblox `Transparency`); legacy UNC and Potassium docs treat `Transparency` as opacity (`0` invisible, `1` opaque). Test on the target executor when writing cross-executor overlays. |
| `Color` | `Color3` | Color tint of the primitive. |
| `__OBJECT_EXISTS` | `boolean` *(read-only)* | `true` while alive; becomes `false` after `:Destroy()` / `:Remove()`. |
| `:Destroy()` / `:Remove()` | `(): ()` | Destroys the drawing object, releases native resources, and sets `__OBJECT_EXISTS = false`. |

#### Shape-Specific Properties

| Shape | Properties | Notes |
| :--- | :--- | :--- |
| `Line` | `From: Vector2`, `To: Vector2`, `Thickness: number` | 2D line segment. |
| `Text` | `Text: string`, `TextBounds: Vector2` *(read-only)*, `Font: Drawing.Fonts \| DrawFont \| number`, `Size: number`, `Position: Vector2`, `Center: boolean`, `Outline: boolean`, `OutlineColor: Color3` | Volt also aliases `Centered` to `Center`, `Outlined` to `Outline`, and adds `OutlineOpacity: number`. |
| `Image` | `Data: string`, `Size: Vector2`, `Position: Vector2`, `Rounding: number` | Raw PNG/JPG bytes (e.g., from `readfile`). Volt adds read-only `Loaded: boolean`. |
| `Circle` | `Position: Vector2`, `Radius: number`, `NumSides: number`, `Thickness: number`, `Filled: boolean` | Radial primitive centered at `Position`. |
| `Square` | `Position: Vector2`, `Size: Vector2`, `Thickness: number`, `Filled: boolean` | Axis-aligned rectangle anchored at top-left `Position`. |
| `Triangle` | `PointA: Vector2`, `PointB: Vector2`, `PointC: Vector2`, `Thickness: number`, `Filled: boolean` | 3-vertex polygon. |
| `Quad` | `PointA: Vector2`, `PointB: Vector2`, `PointC: Vector2`, `PointD: Vector2`, `Thickness: number`, `Filled: boolean` | 4-vertex polygon. |
| `Font` *(Potassium)* | `Data: string` | Raw TTF/OTF binary font data. |
| `Shader` *(Potassium)* | `Vertex: string`, `Pixel: string`, `Position: Vector2`, `Size: Vector2` + `:Create()` | DirectX HLSL GPU shader overlay. |

### Shader Object & HLSL Pipeline (Potassium)

Potassium supports custom GPU HLSL shaders via `Drawing.new("Shader")`:
- Entry points for both `Vertex` and `Pixel` must be named `main`.
- Call `shaderObject:Create()` every time `Vertex` or `Pixel` code is updated.
- Leaving `Size` as `Vector2.zero` renders full-screen across the game window.
- Constant buffer `b0` automatically receives frame uniforms:

```hlsl
cbuffer Params : register(b0)
{
    float time;
    float2 resolution;
    float2 mouse;
};
```

```lua
local shaderObject = Drawing.new("Shader")

shaderObject.Vertex = [[
struct VSOut
{
    float4 pos : SV_POSITION;
    float2 uv  : TEXCOORD0;
};

VSOut main(uint id : SV_VertexID)
{
    VSOut o;
    float2 pos[3] = { float2(-1.0, -1.0), float2(-1.0, 3.0), float2(3.0, -1.0) };
    float2 uv[3]  = { float2(0.0, 1.0),   float2(0.0, -1.0), float2(2.0, 1.0)  };
    o.pos = float4(pos[id], 0.0, 1.0);
    o.uv  = uv[id];
    return o;
}]]

shaderObject.Pixel = [[
cbuffer Params : register(b0)
{
    float time;
    float2 resolution;
    float2 mouse;
};

float4 main(float4 pos : SV_Position, float2 uv : TEXCOORD0) : SV_Target
{
    float2 p = (uv - 0.5) * 2.0;
    for (int i = 1; i < 5; i++)
    {
        float fi = (float)(i + 11);
        float2 freq  = float2(1.6, 1.1) * fi;
        float2 phase = time * (float)i * float2(3.4, 0.5) / 10.0;
        p += sin(p.yx * freq + phase) * 0.1;
    }
    float c = abs(sin(p.y) + sin(p.x)) * 0.5;
    return float4(c.xxx, 1.0);
}]]

shaderObject:Create()
```

### DrawingImmediate Library

Per-frame stateless immediate-mode rendering supported by Potassium, Madium, Volt, and Kawaii. All draw calls must execute inside a `DrawingImmediate.GetPaint(zIndex)` callback.

| Function | Signature | Description |
| :--- | :--- | :--- |
| `DrawingImmediate.GetPaint` | `(zIndex: int): Signal` | Returns a render-step signal for `zIndex` (lower values render first). |
| `DrawingImmediate.Line` | `(from: Vector2, to: Vector2, color: Color3, opacity: number, thickness: number): ()` | Draws an immediate line segment. |
| `DrawingImmediate.Circle` | `(center: Vector2, radius: number, color: Color3, opacity: number, num_sides: int, thickness: number): ()` | Draws an outline circle. |
| `DrawingImmediate.FilledCircle` | `(center: Vector2, radius: number, color: Color3, num_sides: int, opacity: number): ()` | Draws a filled circle. |
| `DrawingImmediate.Triangle` | `(a: Vector2, b: Vector2, c: Vector2, color: Color3, opacity: number, thickness: number): ()` | Draws a wireframe triangle. |
| `DrawingImmediate.FilledTriangle` | `(a: Vector2, b: Vector2, c: Vector2, color: Color3, opacity: number): ()` | Draws a filled triangle. |
| `DrawingImmediate.Rectangle` | `(top_left: Vector2, size: Vector2, color: Color3, opacity: number, rounding: number, thickness: number): ()` | Draws a rounded outline rectangle. |
| `DrawingImmediate.FilledRectangle` | `(top_left: Vector2, size: Vector2, color: Color3, opacity: number, rounding: number): ()` | Draws a rounded filled rectangle. |
| `DrawingImmediate.Quad` | `(a: Vector2, b: Vector2, c: Vector2, d: Vector2, color: Color3, opacity: number, thickness: number): ()` | Draws a wireframe quad. |
| `DrawingImmediate.FilledQuad` | `(a: Vector2, b: Vector2, c: Vector2, d: Vector2, color: Color3, opacity: number): ()` | Draws a filled quad. |
| `DrawingImmediate.Text` | `(pos: Vector2, font: number, font_size: number, color: Color3, opacity: number, text: string, center: boolean): ()` | Draws immediate text. |
| `DrawingImmediate.OutlinedText` | `(pos: Vector2, font: number, font_size: number, color: Color3, opacity: number, outline_color: Color3, outline_opacity: number, text: string, center: boolean): ()` | Draws immediate outlined text. |

```lua
local uis = game:GetService("UserInputService")
DrawingImmediate.GetPaint(1):Connect(function()
    DrawingImmediate.Circle(uis:GetMouseLocation(), 100, Color3.new(1, 1, 1), 1, 100, 1)
end)
```

---

## ImGui Library

Native immediate-mode GUI rendering directly inside the game viewport without Roblox `ScreenGui` instances.

### Madium Dear ImGui API (217 Functions)

Madium exposes a Dear ImGui binding (`ImGui.*`) along with ImGui flag and style constants. Register render callbacks with `ImGui.OnRender`:

| Category | Exposed `ImGui.*` Functions |
| :--- | :--- |
| Render loop & windows | `OnRender`, `Begin`, `End`, `BeginChild`, `EndChild`, `DockSpace`, `SetNextWindowPos`, `SetNextWindowSize`, `SetNextWindowSizeConstraints`, `SetNextWindowContentSize`, `SetNextWindowCollapsed`, `SetNextWindowFocus`, `SetNextWindowBgAlpha`, `SetNextWindowScroll`, `GetWindowPos`, `GetWindowSize`, `IsWindowAppearing`, `IsWindowCollapsed`, `IsWindowFocused`, `IsWindowHovered`, `GetContentRegionAvail`, `GetFrameCount` |
| Text & formatting | `Text`, `TextEx`, `TextColored`, `TextDisabled`, `TextWrapped`, `BulletText`, `Bullet`, `Separator`, `SeparatorText`, `CalcTextSize`, `GetFontSize`, `GetTextLineHeight`, `GetTextLineHeightWithSpacing`, `GetFrameHeight`, `GetFrameHeightWithSpacing`, `PushTextWrapPos`, `PopTextWrapPos`, `AlignTextToFramePadding` |
| Widgets & controls | `Button`, `ButtonEx`, `SmallButton`, `InvisibleButton`, `ArrowButton`, `ArrowButtonEx`, `Checkbox`, `RadioButton`, `ProgressBar`, `Image`, `ImageButton`, `SliderFloat`, `SliderInt`, `SliderAngle`, `DragFloat`, `DragInt`, `InputText`, `InputTextMultiline`, `InputTextWithHint`, `InputFloat`, `InputInt`, `StringBuffer`, `ColorEdit3`, `ColorEdit4`, `ColorPicker3`, `ColorPicker4`, `ColorButton`, `Combo`, `BeginCombo`, `EndCombo`, `ListBox`, `BeginListBox`, `EndListBox`, `Selectable`, `PlotLines`, `PlotHistogram` |
| Trees & tabs | `TreeNode`, `TreeNodeEx`, `TreePop`, `CollapsingHeader`, `SetNextItemOpen`, `BeginTabBar`, `EndTabBar`, `BeginTabItem`, `EndTabItem`, `TabItemButton`, `SetTabItemClosed` |
| Tables | `BeginTable`, `EndTable`, `TableNextRow`, `TableNextColumn`, `TableSetColumnIndex`, `TableSetupColumn`, `TableSetupScrollFreeze`, `TableHeadersRow`, `TableHeader`, `TableGetColumnCount`, `TableGetColumnIndex`, `TableGetRowIndex`, `TableGetColumnName`, `TableGetColumnFlags`, `TableSetColumnEnabled`, `TableSetBgColor` |
| Menus, popups & tooltips | `BeginMainMenuBar`, `EndMainMenuBar`, `BeginMenuBar`, `EndMenuBar`, `BeginMenu`, `EndMenu`, `MenuItem`, `BeginPopup`, `BeginPopupModal`, `EndPopup`, `OpenPopup`, `CloseCurrentPopup`, `IsPopupOpen`, `BeginPopupContextItem`, `BeginPopupContextWindow`, `BeginPopupContextVoid`, `BeginTooltip`, `BeginTooltipEx`, `BeginTooltipHidden`, `BeginItemTooltip`, `EndTooltip`, `SetTooltip`, `SetItemTooltip` |
| Layout, cursor & scrolling | `SameLine`, `NewLine`, `Spacing`, `Dummy`, `Indent`, `Unindent`, `BeginGroup`, `EndGroup`, `GetCursorPos`, `GetCursorPosX`, `GetCursorPosY`, `SetCursorPos`, `SetCursorPosX`, `SetCursorPosY`, `GetCursorStartPos`, `GetCursorScreenPos`, `SetCursorScreenPos`, `GetScrollX`, `GetScrollY`, `GetScrollMaxX`, `GetScrollMaxY`, `SetScrollX`, `SetScrollY`, `SetScrollHereX`, `SetScrollHereY`, `SetNextItemWidth`, `PushItemWidth`, `PopItemWidth` |
| Styling, fonts & IDs | `StyleColorsDark`, `StyleColorsLight`, `StyleColorsClassic`, `PushStyleColor`, `PopStyleColor`, `PushStyleVar`, `PopStyleVar`, `PushFont`, `PopFont`, `PushItemFlag`, `PopItemFlag`, `PushID`, `PopID`, `GetID`, `GetColorU32` |
| Drag & drop | `BeginDragDropSource`, `EndDragDropSource`, `SetDragDropPayload`, `BeginDragDropTarget`, `EndDragDropTarget`, `AcceptDragDropPayload` |
| Direct draw primitives | `DrawLine`, `DrawRect`, `DrawRectFilled`, `DrawCircle`, `DrawCircleFilled`, `DrawText` |
| Item & input state | `IsItemHovered`, `IsItemActive`, `IsItemFocused`, `IsItemClicked`, `IsItemVisible`, `IsItemEdited`, `IsItemActivated`, `IsItemDeactivated`, `IsItemDeactivatedAfterEdit`, `IsItemToggledOpen`, `IsAnyItemHovered`, `IsAnyItemActive`, `IsAnyItemFocused`, `GetItemRectMin`, `GetItemRectMax`, `GetItemRectSize`, `SetItemDefaultFocus`, `SetItemAllowOverlap`, `SetKeyboardFocusHere`, `SetNextItemShortcut`, `Shortcut`, `IsKeyDown`, `IsKeyPressed`, `IsKeyReleased`, `IsMouseDown`, `IsMouseClicked`, `IsMouseReleased`, `IsMouseDoubleClicked`, `IsMouseDragging`, `GetMousePos`, `GetMouseDragDelta`, `ResetMouseDragDelta`, `GetMouseCursor`, `SetMouseCursor`, `GetClipboardText`, `SetClipboardText` |

### Kawaii ImGui API (13 Functions)

Kawaii provides a 13-function ImGui API for building script windows with labels, buttons, sliders, and standard controls.

---

## Environment Library

Accesses executor and Roblox global environments, the Luau registry, thread lists, and garbage collector memory scanning.

| Function | Signature | Support | Description |
| :--- | :--- | :--- | :--- |
| `getgenv` | `(): { [string]: any }` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the executor's shared persistent global environment table. |
| `getrenv` | `(): { [string]: any }` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns Roblox's native global environment table inherited by game scripts. |
| `getreg` | `(): { [any]: any }` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the internal Luau registry table (values stored here are strongly referenced). |
| `getgc` | `(includeTables: boolean?): { any }` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns living garbage-collected objects (functions, userdatas, and optionally tables when `includeTables = true`; defaults to `false`). |
| `filtergc` | `(filterType: "function" \| "table", filterOptions: table, returnOne: boolean?): { any } \| any?` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Scans GC objects matching typed filter criteria. Returns first match directly when `returnOne = true`. |
| `getsenv` | `(script: BaseScript \| ModuleScript): { [any]: any }?` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the runtime environment table (`getfenv`) of a running script. |
| `gettenv` | `(thread: thread?): { [any]: any }` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns the global environment table of `thread` (defaults to current thread). |
| `getallthreads` | `(): { thread }` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns every live GC-visible Luau thread. |
| `getoriginalenv` | `(target: any?): any` | `Mad` | Returns unhooked versions of the environment, a table, or an object bypassing hooks installed after Madium initialized. |
| `getfunctionreferences` | `(func: function): { any }` | `Mad` | Returns every live table, function, and thread holding a reference to `func`. |
| `getobjectreferences` | `(value: any): { any }` | `Mad` | Returns every live table, function, and thread holding a reference to `value`. |

### `filtergc` Specifications

```luau
type FunctionFilterOptions = {
    Name: string?,                  -- Function debug name to match
    IgnoreExecutor: boolean?,       -- If true (default), excludes executor closures
    Hash: string?,                  -- Bytecode SHA-384 hash from getfunctionhash
    Constants: { any }?,            -- Must contain all specified constants
    Upvalues: { any }?,             -- Must contain all specified upvalues
}

type TableFilterOptions = {
    Keys: { any }?,                 -- Table must contain all specified keys
    Values: { any }?,               -- Table must contain all specified values
    KeyValuePairs: { [any]: any }?, -- Table must contain all key-value pairs
    Metatable: table?,              -- Raw metatable identity match
}
```

- Bytecode criteria (`Hash`, `Constants`, `Upvalues`) apply only to Luau closures and are ignored for C closures.

---

## FileSystem, Directory Watcher & File Dialogs Libraries

### FileSystem Library

Sandboxed file I/O confined strictly to the executor's workspace folder. Path traversal escaping the workspace root is prohibited (sUNC forbids accessing real disk paths such as `C:/` or `/Library`; Volt allows explicit symlinks placed inside workspace).

| Function | Signature | Support | Description |
| :--- | :--- | :--- | :--- |
| `readfile` | `(path: string): string` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Reads and returns the entire contents of `path` as a string. |
| `writefile` | `(path: string, data: string): ()` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Creates or overwrites `path` with `data`. |
| `appendfile` | `(path: string, data: string): ()` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Appends `data` to `path` (creates the file if absent). |
| `delfile` | `(path: string): ()` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Permanently deletes the file at `path`. |
| `makefolder` | `(path: string): ()` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Creates a directory at `path`. |
| `delfolder` | `(path: string): ()` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Permanently deletes the folder (and contents) at `path`. |
| `isfile` | `(path: string): boolean` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `path` exists and is a file. |
| `isfolder` | `(path: string): boolean` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `path` exists and is a folder. |
| `listfiles` | `(path: string): { string }` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns paths of all files and directories inside `path` (`""` lists workspace root). |
| `loadfile` | `<A...>(path: string, chunkname: string?): (((A...) -> any)?, string?)` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Compiles Luau source from `path`. Note: per sUNC, syntax errors throw a runtime error (unlike `loadstring`). |
| `getcustomasset` | `(path: string): string` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Copies a workspace file into Roblox's content directory and returns an `rbxasset://` URI for textures, audio, or meshes. |
| `dofile` | `(path: string): ()` | `Pot`, `Mad`, `Volt`, `Kaw` | Loads, compiles, and executes a workspace Luau file. |
| `readfileasync` | `(path: string): string` | `Mad`, `Kaw` | Asynchronous yielding file read off the main thread. |
| `writefileasync` | `(path: string, data: string): ()` | `Mad`, `Kaw` | Asynchronous yielding file write. |
| `appendfileasync` | `(path: string, data: string): ()` | `Mad`, `Kaw` | Asynchronous yielding file append. |
| `loadfileasync` | `(path: string, chunkname: string?): (function?, string?)` | `Mad`, `Kaw` | Asynchronous yielding file compilation. |
| `getfilesize` | `(path: string): number` | `Mad`, `Kaw` | Returns the byte length of the file at `path`. |
| `movefile` | `(source: string, destination: string): ()` | `Mad`, `Kaw` | Moves or renames a file within the workspace sandbox. |
| `writecustomasset` | `(path: string, data: string): string` | `Mad` | Writes binary asset data and directly returns a usable `rbxasset://` URI. |

### Directory Watcher Library (Madium)

Monitors workspace directories for live filesystem changes (useful for hot-reloading configs/scripts):

| Function | Signature | Support | Description |
| :--- | :--- | :--- | :--- |
| `DirectoryWatcher.new` | `(path: string, ...): DirectoryWatcher` | `Mad` | Starts watching a workspace directory for file/folder modifications. |
| `DirectoryWatcher:Destroy` | `(): ()` | `Mad` | Stops watching and releases OS directory watcher handles. |

### File Dialogs Library (Madium & Kawaii)

Opens native OS file and folder picker dialogs so users can grant a script access to selected files or folders outside the workspace sandbox (13 functions in Madium and 13 in Kawaii):

| Function / Method | Support | Description |
| :--- | :--- | :--- |
| `openfiledialog` | `Mad`, `Kaw` | Opens a single-file OS picker and returns a dialog item handle (or `nil` if canceled). |
| `openfilesdialog` | `Mad`, `Kaw` | Opens a multi-file OS picker and returns an array of selected item handles. |
| `openfolderdialog` | `Mad`, `Kaw` | Opens a folder OS picker and returns a folder item handle. |
| `savefiledialog` | `Mad`, `Kaw` | Opens a Save-As OS picker and returns a writable item handle. |
| `MadiumFileDialogItem` methods | `Mad` *(analogous object in `Kaw`)* | `:GetName()`, `:IsFile()`, `:IsFolder()`, `:Read()`, `:Write(data)`, `:Append(data)`, `:Delete()`, `:List()`, `:MakeFolder(name)` |

---

## Input Library

Simulates OS keyboard and mouse inputs. Inputs only dispatch while the Roblox client window has focus.

| Function | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `isrbxactive` | `(): boolean` | `isgameactive`, `iswindowactive` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if the Roblox window currently has focus. |
| `keypress` | `(keycode: number): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Presses down a Windows virtual key code (e.g., `0x48` for `'H'`). |
| `keyrelease` | `(keycode: number): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Releases a Windows virtual key code. |
| `keytap` | `(keycode: number): ()` | `keyclick` | `Pot`, `Mad`, `Volt`, `Kaw` | Presses and immediately releases a virtual key code. (`Pot`: `keytap`; `Mad`: `keyclick`.) |
| `mouse1click` | `(): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Left mouse button click (down + up). |
| `mouse1press` | `(): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Left mouse button down. |
| `mouse1release` | `(): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Left mouse button release. |
| `mouse2click` | `(): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Right mouse button click (down + up). |
| `mouse2press` | `(): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Right mouse button down. |
| `mouse2release` | `(): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Right mouse button release. |
| `mousemoveabs` | `(x: number, y: number): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Moves cursor to absolute client-area coordinates `(x, y)`. |
| `mousemoverel` | `(dx: number, dy: number): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Moves cursor by relative pixel delta `(dx, dy)`. |
| `mousescroll` | `(scroll: number): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Sends a mouse-wheel scroll delta. |

---

## Instances Library

Inspects, isolates, and triggers interactions on Roblox `Instance` objects.

| Function | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `cloneref` | `<T>(object: T & Instance): T` | `clonereference` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns a distinct reference wrapper (`obj == cloneref(obj)` is `false`) to evade weak-table detections. |
| `compareinstances` | `(a: Instance, b: Instance): boolean` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Checks whether two references point to the same underlying `Instance`. |
| `fireclickdetector` | `(detector: ClickDetector, distance: number?, signal: string?): ()` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Triggers a `ClickDetector` (default `"MouseClick"`, optional `"RightMouseClick"` / hover signals in Potassium). |
| `fireproximityprompt` | `(prompt: ProximityPrompt): ()` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Instantly fires a `ProximityPrompt`, bypassing `HoldDuration` and distance limits. |
| `firetouchinterest` | `(part1: BasePart, part2: BasePart, toggle: boolean \| number): ()` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Simulates touch begin (`true` or `0`) or touch end (`false` or `1`) between two `BasePart`s. |
| `getcallbackvalue` | `(object: Object, property: string): function?` | `getcallbackmember` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Reads write-only callback properties (e.g., `BindableFunction.OnInvoke`). Returns `nil` if unset. |
| `gethui` | `(): Instance` | `get_hidden_gui` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns a hidden UI parent container shielded from standard game script discovery. |
| `getinstances` | `(): { Instance }` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns all `Instance` objects currently tracked by the client. |
| `getnilinstances` | `(): { Instance }` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns all client instances in memory whose `Parent` is `nil`. |
| `getrendersteppedlist` | `(): { RenderSteppedEntry \| function }` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Returns callbacks bound via `RunService:BindToRenderStep`. In Potassium, entries expose `.Function`, `.Thread`, `.Priority`, and `.Name`. |

---

## Metatable & Table Library

Raw metatable manipulation and table mutability control, bypassing `__metatable` locks and `table.freeze`.

| Function | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `getnamecallmethod` | `(): string?` | `get_namecall_method` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the active `__namecall` method name (case-sensitive; `nil` outside `__namecall`). |
| `setnamecallmethod` | `(method: string): ()` | `set_namecall_method` | `Pot`, `Mad`, `Volt`, `Kaw` | Overrides the method name returned by `getnamecallmethod()`. |
| `getrawmetatable` | `(object: table \| userdata): table?` | `debug.getmetatable` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the true metatable of `object`, ignoring `__metatable`. |
| `setrawmetatable` | `(object: table \| userdata, metatable: table?): table?` | `debug.setmetatable` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Directly assigns `object`'s raw metatable even if locked. |
| `isreadonly` | `(tbl: table): boolean` | `is_readonly` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `tbl` is frozen/read-only. |
| `setreadonly` | `(tbl: table, readonly: boolean): ()` | `set_readonly` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Toggles read-only memory protection on `tbl`. |
| `iswritable` | `(tbl: table): boolean` | `iswriteable` | `Mad`, `Volt`, `Kaw` | Returns `true` if `tbl` is writable (`not isreadonly(tbl)`). (`Volt`: `iswritable`; `Mad`: `iswriteable`.) |
| `makereadonly` | `(tbl: table): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Equivalent to `setreadonly(tbl, true)`. |
| `makewritable` | `(tbl: table): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Equivalent to `setreadonly(tbl, false)`. |
| `getmetafield` | `(object: any, field: string): any` | - | `Mad`, `Kaw` | Directly retrieves a single raw metamethod/field from `object`'s metatable. |
| `hasmetafield` | `(object: any, field: string): boolean` | - | `Mad`, `Kaw` | Checks whether `object`'s raw metatable contains `field`. |
| `hasmetatable` | `(object: any): boolean` | - | `Mad`, `Kaw` | Checks whether `object` has a metatable attached. |
| `isprotected` | `(object: any): boolean` | - | `Mad`, `Kaw` | Checks whether `object` has a `__metatable` lock field. |
| `isuntouched` | `(tbl: table): boolean` | `debug.getsafeenv` | `Pot`, `Mad`, `Kaw` | Checks whether a table/environment still has its safe-env/untouched flag intact. |
| `setuntouched` | `(tbl: table, state: boolean): ()` | `debug.setsafeenv` | `Pot`, `Mad`, `Kaw` | Sets the untouched/safe-env flag on a table. |

---

## Miscellaneous, Runtime & SaveInstance Libraries

Executor identity, HTTP networking, clipboard, Fast Flags, FPS cap, teleport queues, process IDs, and place/model serialization.

| Function | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `identifyexecutor` | `(): (string, string)` | `getexecutorname` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns `(executorName, versionString)` (informational only; not a secure check). |
| `request` | `(options: RequestOptions): Response` | `http_request`, `http.request` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Sends a synchronous (yielding) HTTP request and returns a `Response` table. |
| `httpget` | `(self: any?, url: string): string` | `HttpGet` | `Pot`, `Mad`, `Kaw` | Performs an HTTP GET request and returns the response body (`self` is optional legacy `game` argument). |
| `httppost` | `(url: string, data: string, ...): string` | `HttpPost` | `Mad` | Performs an HTTP POST request and returns the response body. |
| `getobjects` | `(self: any?, asset: string): { Instance }` | - | `Pot`, `Mad` | Emulates `game:GetObjects(asset)` to load instances from a Roblox asset URI. |
| `gethwid` | `(): string` | `get_hwid`, `get_user_identifier` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns the client's hardware/external identifier hash. |
| `getprocessidentifier` | `(): number` | `get_process_identifier` | `Mad`, `Volt`, `Kaw` | Returns the OS process ID (PID) of the running Roblox client. |
| `getfpscap` | `(): number` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Returns the current client FPS limit. |
| `setfpscap` | `(cap: number): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Sets the client FPS limit (`0` removes the cap). |
| `getfflag` | `(fflag: string): string` | `getfastflag` | `Pot`, `Mad`, `Volt`, `Kaw` | Reads the current value of a Roblox Fast Flag. |
| `setfflag` | `(fflag: string, value: any): ()` | `setfastflag` | `Pot`, `Mad`, `Volt`, `Kaw` | Overrides a Roblox Fast Flag value. |
| `getfflagtype` | `(fflag: string): string` | `getfastflagtype` | `Pot`, `Mad` | Returns the type string of a Fast Flag (e.g., `"flag"`, `"int"`, `"string"`). |
| `setclipboard` | `(text: string): ()` | `toclipboard` | `Pot`, `Mad`, `Volt`, `Kaw` | Copies `text` to the OS clipboard. |
| `getclipboard` | `(): string` | - | `Mad`, `Kaw` | Reads plain text from the OS clipboard. |
| `setrbxclipboard` | `(data: string): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Writes `.rbxm`/`.rbxmx` model data into the Roblox Studio clipboard. |
| `messagebox` | `(text: string, caption: string, flags: number): number` | `messageboxasync` | `Pot`, `Mad`, `Volt`, `Kaw` | Displays a native Windows `MessageBoxA` dialog (yields) and returns the Win32 button result ID. |
| `queueonteleport` | `(code: string): ()` | `queue_on_teleport` | `Pot`, `Mad`, `Volt`, `Kaw` | Queues Luau code to run automatically after the client teleports to another place. |
| `clearteleportqueue` | `(): ()` | `clear_teleport_queue`, `clearqueueonteleport` | `Pot`, `Mad`, `Volt`, `Kaw` | Removes all pending scripts from the teleport execution queue. |
| `saveinstance` | `(root: (Instance \| {Instance})?, options: (table \| SaveInstanceOptions)?): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Serializes an instance tree (or `game`) to `.rbxl` / `.rbxm`. |
| `saveplace` | `(options: SaveInstanceOptions?): ()` | - | `Mad`, `Volt`, `Kaw` | Serializes the current place (`game`) to a `.rbxl` file. |

### HTTP Request & Response Shapes

```luau
type RequestOptions = {
    Url: string,
    Method: "GET" | "HEAD" | "POST" | "PUT" | "DELETE" | "OPTIONS" | "PATCH",
    Body: string?,
    Headers: { [string]: string }?,
    Cookies: { [string]: string }?,
}

type Response = {
    Success: boolean,
    Body: string,
    StatusCode: number,
    StatusMessage: string,
    Headers: { [string]: string },
}
```

Under sUNC, compliant executors attach three automatic request headers: `PREFIX-User-Identifier` (persistent cross-device user ID), `PREFIX-Fingerprint` (machine HWID), and `User-Agent` (executor identity and version). Some executors block requests to Roblox API domains for account safety.

### SaveInstance & SavePlace Options

> [!IMPORTANT]
> Potassium accepts a plain Lua table for `options`. Volt and Madium require a `SaveInstanceOptions` userdata created with `SaveInstanceOptions.new()` and reject plain tables.

| Potassium Plain-Table Option | Default | Volt / Madium `SaveInstanceOptions.new()` Property | Default | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| `FileName: string` | Varies | `FilePath: string` | `""` | Output path (`.rbxl` / `.rbxm` auto-appended in Volt). |
| `Decompile: boolean` | `false` | `DecompileScripts: boolean` | `true` | Decompile client scripts during export. |
| `NilInstances: boolean` | `false` | `SaveNilInstances: boolean` | `false` | Include nil-parented instances. |
| `RemovePlayerCharacters: boolean` | `true` | `SavePlayerCharacters: boolean` | `false` | Control whether player character models are exported. |
| `SavePlayers: boolean` | `false` | `SavePlayers: boolean` | `false` | Include `Player` instances. |
| `DecompileTimeout: number` | `10` | *(Via `DecompilerOptions`)* | Default | Per-script decompiler timeout / pass config. |
| `MaxThreads: number` | `3` | *(Auto CPU-core scheduler)* | Auto | Worker thread count. |
| `DecompileIgnore: {string}` | `{"Chat","CoreGui","CorePackages"}` | `IgnoreList: {Instance}` | `{}` | Excluded services/instances (assign full table in Volt). |
| `ShowStatus: boolean` | `true` | `IgnoreArchivable: boolean` | `false` | Status output (Pot) / ignore `Archivable` flag (Volt). |
| `IgnoreDefaultProps: boolean` | `true` | `DisableCompression: boolean` | `false` | Size vs compression control. |
| `IsolateStarterPlayer: boolean` | `true` | `SaveNonCreatable: boolean` / `CopyToClipboard: boolean` | `false` | Non-creatable folders & Studio clipboard export (Volt). |

---

## Oth Library (Off-Thread Hooking)

Supported in Potassium, Madium, Volt, and Kawaii. Off-Thread Hooking (`oth`) hooks native C closures by running Luau hook callbacks on a separate thread, avoiding call-stack and C-closure detections triggered by `hookfunction`.

| Function | Signature | Support | Description |
| :--- | :--- | :--- | :--- |
| `oth.hook` | `(target: function, hook: function): function` | `Pot`, `Mad`, `Volt`, `Kaw` | Installs an off-thread hook on a C closure and returns a callable handle to the original function. |
| `oth.unhook` | `(target: function): ()` | `Pot`, `Mad`, `Volt`, `Kaw` | Removes an `oth.hook` from `target`. |
| `oth.get_root_callback` | `(): function` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns a callable reference to the underlying unhooked root callback from inside an OTH hook (even across stacked hooks). |
| `oth.is_hook_thread` | `(): boolean` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if the current thread is executing inside an OTH hook handler. |
| `oth.get_original_thread` | `(): thread?` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns the original thread that called into the hook, or `nil` outside an OTH hook thread. |

```lua
-- Bypass nil-parented anti-cheat scans on game.GetService
oth.hook(game.GetService, function(self, service)
    local thread = oth.get_original_thread()
    local callerScript = getscriptfromthread(thread)

    if callerScript and callerScript.Parent == nil then
        return task.wait(9e9)
    end

    return oth.get_root_callback()(self, service)
end)
```

---

## Custom Signals (`PsmSignal`, `MadiumSignal`, `VoltSignal`, Kawaii)

Script-created signal classes implemented natively in each executor. Potassium and Madium also expose the `Signal.new` alias.

| Executor | Constructor | Connection Type & Members | Signal Methods |
| :--- | :--- | :--- | :--- |
| Potassium | `PsmSignal.new()` *(alias `Signal.new`)* | `PsmConnection`: `:Disconnect()` | `:Connect(fn)`, `:Once(fn)`, `:Wait()`, `:Fire(...)` |
| Madium | `MadiumSignal.new()` *(alias `Signal.new`)* | `MadiumConnection`: `:Disconnect()` | `:Connect(fn)`, `:Once(fn)`, `:Wait()`, `:Fire(...)` |
| Volt | `VoltSignal.new()` | `VoltConnection`: `.Connected` *(read-only)*, `:Disconnect()` | `:Connect(fn)`, `:Once(fn)`, `:Wait()`, `:Fire(...)` *(omitted on read-only internal signals like `on_actor_state_created`)* |
| Kawaii | Custom Signals *(12 functions)* | Connection object methods | Signal creation, connection, waiting, and dispatch |

```lua
local signal = (Signal or PsmSignal or MadiumSignal or VoltSignal).new()
signal:Once(function(msg)
    print("Fired:", msg)
end)
signal:Fire("Hello!")
```

---

## RakNet Library

Low-level networking library for inspecting, modifying, blocking, and sending raw RakNet packets between client and server. Supported by Potassium, Madium, Volt, and Kawaii.

> [!WARNING]
> `raknet` is disabled by default and must be turned on in executor client settings before use. Malformed payloads, invalid packet IDs, or protocol desyncs can cause disconnects, broken replication, or account bans.

| Function | Signature | Support | Description |
| :--- | :--- | :--- | :--- |
| `raknet.is_enabled` | `(): boolean` | `Mad`, `Volt`, `Kaw` | Returns whether RakNet manipulation is currently enabled in settings (safe to call while disabled). |
| `raknet.add_send_hook` | `(callback: (packet: RakNetPacket) -> ()): ()` | `Pot`, `Mad`, `Volt`, `Kaw` | Intercepts outgoing client-to-server packets before transmission. |
| `raknet.remove_send_hook` | `(callback: function): ()` | `Pot`, `Mad`, `Volt`, `Kaw` | Unregisters a previously added send hook (pass exact function reference). |
| `raknet.add_receive_hook` | `(callback: (packet: RakNetPacket) -> ()): ()` | `Pot`, `Mad`, `Kaw` | Intercepts incoming server-to-client packets before engine processing. *(Not exposed on Volt, which is outgoing-only.)* |
| `raknet.remove_receive_hook` | `(callback: function): ()` | `Pot`, `Mad`, `Kaw` | Unregisters a previously added receive hook. |
| `raknet.send` | `(data: buffer \| string \| {number}, priority: number?, reliability: number?, orderingChannel: number?): ()` | `Pot`, `Mad`, `Volt`, `Kaw` | Sends a raw packet payload. Volt defaults: `priority` (0 to 3, default `2`), `reliability` (0 to 7, default `3`), `ordering_channel` (0 to 31). |
| `raknet.receive` | `(data: buffer \| string \| {number}, ...): ()` | `Mad`, `Kaw` | Injects a synthetic incoming packet into the client receive pipeline. |
| `raknet.get_schema` | `(...): table` | `Mad` | Inspects RakNet packet schema metadata. |

### `RakNetPacket` Object

*Only valid for the duration of its hook callback.*

| Member | Type | Description |
| :--- | :--- | :--- |
| `PacketId` | `number` | Numeric RakNet packet ID (e.g., `0x1B` for `ID_TIMESTAMP`). |
| `Size` | `number` | Payload byte length. |
| `Priority` | `number` | Transmission priority (`0` to `3`). |
| `Reliability` | `number` | RakNet reliability mode (`0` to `7`). |
| `OrderingChannel` | `number` | Ordered stream channel (`0` to `31`). |
| `AsBuffer` | `buffer` | Packet bytes as a native Luau `buffer`. |
| `AsArray` | `{ number }` | Packet bytes as a numeric byte array. |
| `AsString` | `string` | Packet bytes as a raw binary string. |
| `:SetData(data)` | `(buffer \| string \| {number}): ()` | Replaces the packet payload before delivery. |
| `:Block()` | `(): ()` | Drops/cancels the packet completely. |

```lua
-- Example: Block large outgoing packets
local function blockLarge(packet)
    if packet.Size > 512 then
        packet:Block()
    end
end
raknet.add_send_hook(blockLarge)
```

---

## Reflection & Properties Library

Accesses hidden/non-scriptable engine properties, binary string descriptors, physics network ownership, simulation radius, and Luau thread capability identities.

| Function | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `gethiddenproperty` | `(object: Object, property: string): (any, boolean)` | `gethiddenprop` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns `(value, wasHidden)` for a hidden or non-scriptable property. |
| `sethiddenproperty` | `(object: Object, property: string, value: any): boolean` | `sethiddenprop` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Sets a hidden property and returns whether the property was hidden. |
| `isscriptable` | `(object: Object, property: string): boolean?` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns whether `property` is scriptable, or `nil` if the property does not exist. |
| `setscriptable` | `(object: Object, property: string, state: boolean): boolean?` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Toggles scriptability of `property` and returns its previous state (or `nil` if nonexistent). |
| `getthreadidentity` | `(): number` | `getidentity`, `getthreadcontext`, `get_thread_identity`, `getthreadcaps` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the current Luau thread's security identity level (`0` to `8`). |
| `setthreadidentity` | `(identity: number): ()` | `setidentity`, `setthreadcontext`, `set_thread_identity` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Sets the security identity permission level of the current thread. |
| `getproperties` | `(instance: Instance): { [string]: any }` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Returns a dictionary of all readable properties on `instance` (including hidden ones). |
| `gethiddenproperties` | `(instance: Instance): { [string]: any }` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Returns a dictionary of all readable non-scriptable properties on `instance`. |
| `getbspval` | `(instance: Instance, property: string, base64: boolean): string` | - | `Pot` | Reads `BinaryString` properties such as `Terrain.SmoothGrid`, `PartOperation.PhysicsData`, and `BinaryStringValue.Value`. |
| `getpcd` | `(meshPart: Instance): (string, string)` | `getpcdprop` | `Pot` | Returns the 16-byte hash and binary data for a `TriangleMeshPart`'s `PhysicalConfigData`. |
| `getproximitypromptduration` | `(prompt: ProximityPrompt): number` | - | `Pot` | Reads a `ProximityPrompt`'s hold duration. |
| `setproximitypromptduration` | `(prompt: ProximityPrompt, duration: number): ()` | - | `Pot` | Overrides a `ProximityPrompt`'s hold duration. |
| `getsimulationradius` | `(): number` | - | `Pot` | Returns the `LocalPlayer`'s physics simulation radius. |
| `setsimulationradius` | `(radius: number, maxRadius: number?): ()` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Overrides the client physics simulation radius. |
| `isnetworkowner` | `(part: BasePart): boolean` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `LocalPlayer` currently owns network physics for `part`. |
| `getcallbackthread` | `(instance: Instance, property: string): thread?` | - | `Mad`, `Kaw` | Returns the thread associated with a write-only callback member. |
| `setcallbackvalue` | `(instance: Instance, property: string, callback: function?): ()` | - | `Mad`, `Kaw` | Directly assigns a callback member on an `Instance`. |
| `geteventmember` | `(instance: Instance, event: string): any` | - | `Mad`, `Kaw` | Retrieves an internal event descriptor/member from an `Instance`. |

---

## Regex Library

Native regular expression engine supported by Potassium, Madium, and Kawaii (Java/ECMAScript-style syntax).

| Function / Method | Signature | Support | Description |
| :--- | :--- | :--- | :--- |
| `Regex.new` | `(pattern: string): Regex` | `Pot`, `Mad`, `Kaw` | Compiles a regular expression pattern into a `Regex` object. |
| `Regex.Escape` | `(contents: string): string` | `Pot`, `Mad`, `Kaw` | Escapes regex metacharacters in a literal string. |
| `Regex:Match` | `(contents: string): string? \| match?` | `Pot`, `Mad`, `Kaw` | Returns the first match in `contents`, or `nil`. |
| `Regex:MatchMany` | `(contents: string): { string \| match }` | `Pot`, `Mad`, `Kaw` | Returns an array of all matches in `contents`. |
| `Regex:Replace` | `(contents: string, replacement: string): string` | `Pot`, `Mad`, `Kaw` | Replaces the first match with `replacement` (supports capture groups like `$1`). |

---

## Scripts & Decompiler Library

Enumerates scripts and modules, extracts/hashes bytecode, resolves script-thread associations, and decompiles Luau bytecode.

| Function | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `getcallingscript` | `(): (BaseScript \| ModuleScript)?` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the script responsible for invoking the current thread. |
| `getloadedmodules` | `(): { ModuleScript }` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns all `ModuleScript` instances currently loaded by the client. |
| `getmodules` | `(): { ModuleScript }` | - | `Mad`, `Volt`, `Kaw` | Returns all cached `ModuleScript` instances (whether loaded or not). |
| `getrunningscripts` | `(): { BaseScript \| ModuleScript }` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns all currently executing scripts. |
| `getscripts` | `(): { BaseScript \| ModuleScript }` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns all client-visible script instances in the game. |
| `getscriptbytecode` | `(script: BaseScript \| ModuleScript): string?` | `dumpstring` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns raw compiled Luau bytecode for `script`, or `nil` if unavailable. |
| `getscriptclosure` | `(script: BaseScript \| ModuleScript): (((...any) -> any))?` | `getscriptfunction` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Compiles and returns a new main closure from `script`'s bytecode. |
| `getscriptfromthread` | `(thread: thread): (BaseScript \| ModuleScript)?` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Resolves the script associated with `thread`, or `nil` for executor/GC'd threads. |
| `getscripthash` | `(script: BaseScript \| ModuleScript): string?` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the 96-character hex SHA-384 hash of `script`'s bytecode, or `nil`. |
| `getscriptthread` | `(script: BaseScript): thread?` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Returns the live root thread associated with `script`. |
| `getsenv` | `(script: BaseScript): table` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns the runtime global environment of a running `LocalScript`. |
| `dumpbytecode` | `(func: function): string` | `getfunctionbytecode` | `Pot`, `Mad`, `Volt`, `Kaw` | Extracts raw Luau bytecode from a Luau closure (errors on C closures). (`Pot`: `dumpbytecode`; `Mad`/`Volt`: `getfunctionbytecode`.) |
| `decompile` | `(target: LuaSourceContainer \| function \| string, options: DecompilerOptions?): string` | - | `Pot`, `Mad`, `Volt`, `Kaw` | Decompiles a script, closure, or bytecode string into readable Luau source. |
| `iscorescript` | `(script: Instance): boolean` | - | `Mad`, `Kaw` | Returns `true` if `script` is a Roblox `CoreScript`. |
| `isexecutorthread` | `(thread: thread): boolean` | `isourthread` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns `true` if `thread` belongs to the executor. |
| `require` | `(module: ModuleScript \| number): any` | - | `Mad`, `Kaw` *(enhanced)* | Invokes engine `require` while permitting mixed executor/game script contexts. |

### Volt Asynchronous Decompiler Pipeline

Volt runs a multi-threaded background decompiler that prioritizes semantic correctness over raw speed and can decompile scripts with 100k+ lines. `decompile`, `saveinstance`, and `saveplace` schedule background jobs across a CPU worker pool that stays dormant when idle, yielding the calling Luau thread until results are ready.

Bytecode passes through `CFG + SSA IR`, SSA passes, CFG flattening, AST passes, and formatting to produce Luau source. Two option types control the pipeline:

- `DecompilerOptions.ConditionalStructurer` (SSA pass) rewrites certain `if`/`else` patterns into `and`/`or` expressions; turning it off can speed up decompilation.
- AST passes include `SmartVariableRenamer`, `FunctionDeclarations`, `GuardClauses`, and `DoBlockInsertionThreshold`, which wraps scopes in `do ... end` blocks to stay under Luau's local register limit when recompiling. Setting `DoBlockInsertionThreshold` to `0` disables block insertion for cleaner read-only analysis.
- `DecompilerFormatter` controls output formatting (about 30 ms on a 100k-line script).

---

## Signals Library

Inspects, toggles, fires, hooks, and replicates Roblox `RBXScriptSignal` connections.

| Function | Signature | Support | Description |
| :--- | :--- | :--- | :--- |
| `getconnections` | `(signal: RBXScriptSignal): { Connection }` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Returns all active `Connection` wrappers subscribed to `signal`. |
| `getconnection` | `(signal: RBXScriptSignal, index: number): Connection` | `Pot` | Returns a single `Connection` wrapper at 1-based `index`. |
| `firesignal` | `(signal: RBXScriptSignal, ...: any): ()` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Immediately invokes all Luau connections on `signal`, bypassing `Workspace.SignalBehavior`. |
| `cfiresignal` | `(signal: RBXScriptSignal, ...: any): ()` | `Mad`, `Kaw` | Fires every engine (C++) and script connection through the underlying event descriptor. |
| `replicatesignal` | `(signal: RBXScriptSignal, ...: any): ()` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Replicates a whitelisted signal directly to the server with matching argument types (errors on mismatch). |
| `cansignalreplicate` | `(signal: RBXScriptSignal): boolean` | `Pot`, `Mad`, `Volt`, `Kaw` | Checks whether `signal` supports client-to-server replication. |
| `getsignalarguments` | `(signal: RBXScriptSignal): { string }` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns expected parameter type names for `signal`. |
| `getsignalargumentsinfo` | `(signal: RBXScriptSignal): { ArgumentInfo }` | `Pot`, `Mad`, `Volt`, `Kaw` | Returns detailed argument layout metadata for signal replication. |
| `getsignalwhitelist` | `(): { SignalWhitelistInfo }` | `Pot`, `Mad`, `Volt`, `Kaw` | Lists all engine signals whitelisted for client-to-server replication. |
| `hooksignal` | `(signal: RBXScriptSignal, hook: function): ()` | `Mad`, `Kaw` | Intercepts Luau connections firing on `signal`. |
| `restoresignal` | `(signal: RBXScriptSignal): ()` | `Mad`, `Kaw` | Removes interception installed by `hooksignal`. |
| `issignalhooked` | `(signal: RBXScriptSignal): boolean` | `Mad`, `Kaw` | Returns `true` if `signal` is currently hooked via `hooksignal`. |
| `getconnectionfunction` | `(conn: Connection): function?` | `Mad`, `Kaw` | Retrieves the callback bound to a connection. |
| `getconnectionthread` | `(conn: Connection): thread?` | `Mad`, `Kaw` | Retrieves the Luau thread associated with a connection. |
| `isconnectionenabled` | `(conn: Connection): boolean` | `Mad`, `Kaw` | Returns whether a connection is currently enabled. |
| `setconnectionenabled` | `(conn: Connection, enabled: boolean): ()` | `Mad`, `Kaw` | Enables or disables a connection. |
| `isgamescriptconnection` | `(conn: Connection): boolean` | `Mad`, `Kaw` | Returns `true` if the connection was registered by a game script. |
| `isluaconnection` | `(conn: Connection): boolean` | `Mad`, `Kaw` | Returns `true` if the connection uses a Luau callback rather than native C++. |
| `iswaitingconnection` | `(conn: Connection): boolean` | `Mad`, `Kaw` | Returns `true` if the connection belongs to a thread yielded on `signal:Wait()`. |

### `Connection` Object Shape

| Field / Method | Type / Signature | Description |
| :--- | :--- | :--- |
| `Enabled` | `boolean` | Whether the connection currently receives events. |
| `ForeignState` | `boolean` | `true` if created in a foreign Luau VM/state (e.g., CoreScript). |
| `LuaConnection` | `boolean` | `true` if callback is a Luau closure rather than engine C++. |
| `Function` | `((...any) -> any)?` | Bound callback (`nil` for foreign state or C callbacks). |
| `Thread` | `thread?` | Thread that registered the connection (`nil` for foreign/C). |
| `Script` | `LuaSourceContainer?` | Script that registered the connection *(Potassium)*. |
| `:Fire(...)` | `(...: any): ()` | Immediately invokes the connected callback. |
| `:Defer(...)` | `(...: any): ()` | Defers callback invocation via `task.defer`. |
| `:Disconnect()` | `(): ()` | Permanently unbinds the connection. |
| `:Disable()` | `(): ()` | Suspends event delivery while preserving registration. |
| `:Enable()` | `(): ()` | Re-enables a disabled connection. |

### Signal Whitelist Info

Each entry returned by `getsignalwhitelist()` has two fields: `Parent` (`string`, class name such as `"ClickDetector"`) and `Event` (`string`, replicated event name such as `"MouseClick"`).

---

## Time Library (Madium)

Nanosecond-resolution time, duration, stopwatch, and timer utilities (15 functions in Madium). Durations are stored internally in nanoseconds, so unit conversions on very large values can lose floating-point precision.

| Function / Method | Signature | Description |
| :--- | :--- | :--- |
| `Duration.FromNanoseconds` | `(n: number): Duration` | Constructs a `Duration` from nanoseconds. |
| `Duration.FromMicroseconds` | `(n: number): Duration` | Constructs a `Duration` from microseconds. |
| `Duration.FromMilliseconds` | `(n: number): Duration` | Constructs a `Duration` from milliseconds. |
| `Duration.FromSeconds` | `(n: number): Duration` | Constructs a `Duration` from seconds. |
| `Duration.FromMinutes` | `(n: number): Duration` | Constructs a `Duration` from minutes. |
| `Duration.FromHours` | `(n: number): Duration` | Constructs a `Duration` from hours. |
| `Duration.FromDays` | `(n: number): Duration` | Constructs a `Duration` from days. |
| `Duration.FromMonths` | `(n: number): Duration` | Constructs a `Duration` from months. |
| `Duration.FromYears` | `(n: number): Duration` | Constructs a `Duration` from years. |
| `Duration.TimeSinceEpoch` | `(): Duration` | Returns elapsed `Duration` since the Unix epoch. |
| `Stopwatch.new` | `(): Stopwatch` | Creates a high-resolution stopwatch. |
| `Stopwatch:Start` / `:Stop` / `:Reset` | `(): ()` | Starts, pauses, or resets stopwatch elapsed timing. |
| `Timer.new` | `(): Timer` | Creates a new timer object. |

---

## Voice Chat Library (Kawaii)

Kawaii exposes an 18-function `voicechat` library for Roblox Voice Chat playback, microphone controls, communication channels, and decrypted WebRTC RTP / data packet inspection and injection.

| Function / Method | Description |
| :--- | :--- |
| `voicechat.hasvoicechat()` | Checks whether voice chat is active/available in the current session. |
| `voicechat.getstatus()` | Returns the current voice chat runtime status. |
| `voicechat.getcomchannel()` | Returns the active voice communication channel identifier. |
| `voicechat.joincomchannel(...)` | Joins a specified voice communication channel. |
| `voicechat.play(...)` | Plays an audio source/file into the voice chat stream. |
| `voicechat.pause()` / `voicechat.unpause()` / `voicechat.stop()` | Controls playback state of injected voice audio. |
| `voicechat.setvolume(volume)` | Sets voice playback volume. |
| `voicechat.setspeed(speed)` | Sets voice playback speed/rate. |
| `voicechat.setmicmuted(muted)` | Mutes or unmutes the local microphone. |
| `voicechat.sendaudiopackets(...)` | Sends raw audio packets over the voice stream. |
| `voicechat.senddatapacket(...)` | Sends a data-channel packet over voice chat. |
| `voicechat.incomingpacket:Connect(cb)` | *(Asynchronous)* Subscribes to inbound culling messages and decrypted WebRTC RTP packets on a deferred Luau task; returns `VoicePacketConnection`. |
| `voicechat.incomingpacket:Once(cb)` | Subscribes for a single inbound `VoicePacket`. |
| `voicechat.outgoingpacket:Connect(cb)` | Subscribes to outbound voice packets; returns `VoicePacketConnection`. |
| `voicechat.outgoingpacket:Once(cb)` | Subscribes for a single outbound `VoicePacket`. |
| `VoicePacketConnection:Disconnect()` | Unsubscribes a voice packet observer (sets `.Connected = false`). |

### `VoicePacket` & `VoicePacketConnection` Shapes

```luau
type VoicePacket = {
    Data: buffer,                       -- Owned buffer snapshot (observer does not mutate/block)
    Channel: "data" | "rtp",
    Direction: "outgoing" | "incoming",
    Injected: boolean,
    Accepted: boolean,
}

type VoicePacketConnection = {
    Connected: boolean,
    Disconnect: (self: VoicePacketConnection) -> (),
}
```

```lua
local conn = voicechat.incomingpacket:Connect(function(packet)
    print(packet.Channel, buffer.len(packet.Data))
end)
-- conn:Disconnect()
```

---

## WebSocket Library

Persistent bidirectional WebSocket client connections.

> [!NOTE]
> sUNC requires WebSocket client support only (no server required) and encrypted `wss://` support. Volt also exposes the lowercase `websocket` global as an alias to the same read-only table.

| Function / Member | Signature | Aliases | Support | Description |
| :--- | :--- | :--- | :--- | :--- |
| `WebSocket.connect` | `(url: string): WebSocket` | `WebSocket.new`, `websocket.connect` | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Establishes a persistent `ws://` or `wss://` connection. |
| `ws:Send` | `(message: string): ()` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Transmits a text message to the server. |
| `ws:Close` | `(): ()` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Gracefully closes the WebSocket connection. |
| `ws.OnMessage` | `RBXScriptSignal(message: string)` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Fires when a text message arrives from the server. |
| `ws.OnClose` | `RBXScriptSignal()` | - | `sUNC`, `Pot`, `Mad`, `Volt`, `Kaw` | Fires when the connection closes from either side. |

---

## Compatibility & Platform Extras

### Kawaii Synapse Compatibility

Enabling the Synapse setting in Kawaii exposes 10 compatibility functions matching legacy Synapse APIs.

### Volt Account Manager

Volt 1.3 added a desktop Account Manager alongside Game Explorer and Script Viewer. It stores multiple Roblox accounts and supports multi-instance launching, auto-relaunch, account groups, private servers, and live session monitoring.

### Madium MCP Server

Madium documents an MCP server at `https://getmadium.net/docs/` so AI tools such as Claude, Cursor, and Codex can query its API reference and interact with the running game.

---

## Security & Operational Notes

- Use `oth.hook` instead of `hookfunction` when hooking Roblox engine C methods (`game.GetService`, `game.FindService`, `Instance.new`) so hook handlers run on a separate thread and avoid call-stack detections.
- Do not depend on `crypt` in cross-executor scripts. sUNC deprecated `crypt` and warns when the table exists, so use `base64encode`, `base64decode`, `lz4compress`, and `lz4decompress`, and inline pure-Luau crypto when confidentiality is needed.
- Pass the uncompressed byte length as the second argument to `lz4decompress(compressed, #original)` if your script targets Volt or Potassium as well as sUNC.
- Check whether `SaveInstanceOptions` exists before calling `saveinstance`, because Potassium expects a plain Lua table while Volt and Madium require `SaveInstanceOptions.new()`.
- Validate signal names and parameter schemas with `cansignalreplicate`, `getsignalwhitelist`, and `getsignalargumentsinfo` before calling `replicatesignal` to avoid server kicks.
- Keep `raknet` send and receive hooks minimal and verify packet schemas before modifying payloads; malformed traffic can disconnect the client or trigger account bans.
- Use `DrawingImmediate` (or `ImGui.OnRender` on Madium) instead of retained `Drawing.new` objects for HUD and ESP elements that update every frame.
