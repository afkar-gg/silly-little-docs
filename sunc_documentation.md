# sUNC Documentation

Source: https://docs.sunc.io/ (Mirrors: https://docs.sunc.su/, https://github.com/sUNC-Utilities/docs)

sUNC (senS' Unified Naming Convention) is a standardized specification of Roblox executor API functions that are actively tested and validated by the official sUNC test suite.

> **Important:** sUNC covers **only** functions that are actively tested by the sUNC testing suite. Over time, sUNC has diverged from the legacy UNC standard to eliminate shallow spoofing and better reflect real-world executor development. Deprecated or insecure APIs (such as the legacy `crypt` library) have been removed, while function signatures, return values, and test behaviors have been tightened.

---

## Overview

The sUNC specification covers 81 functions and constructors organized across 13 core libraries:

1. **Closures** (11 functions) - Inspect, modify, hook, and wrap Luau closures
2. **Debug** (10 functions) - Access bytecode internals, stack frames, constants, and upvalues
3. **Drawing** (5 functions + Drawing classes) - 2D immediate-mode render primitives and render object reflection
4. **Encoding** (4 functions) - Base64 and LZ4 binary transformations (replaces legacy `crypt`)
5. **Environment** (5 functions) - Access executor globals, Roblox environments, and filtered garbage collection
6. **Filesystem** (11 functions) - Sandboxed virtual file system operations and custom asset generation
7. **Instances** (9 functions) - Safely reference, compare, and simulate interactions with Roblox instances
8. **Metatable** (5 functions) - Raw metatable inspection and manipulation
9. **Miscellaneous** (2 functions) - Executor identification and HTTP networking
10. **Reflection** (6 functions) - Hidden property access, property scriptability, and thread identity
11. **Scripts** (9 functions) - Script bytecode extraction, bytecode hashing, and script environment access
12. **Signals** (3 functions + Connection object) - Inspect, manipulate, fire, and replicate `RBXScriptSignal` connections
13. **WebSocket** (1 constructor + WebSocket client) - Persistent bidirectional client connections (with mandatory `wss://` support)

---

## The sUNC Test Suite

### Running the Test Script

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

As of **sUNC v2.0**, all verification tests must be conducted inside the official sUNC testing game (available via the sUNC Discord server).

### Selective Function Testing

The `sUNCDebug.functionstotest` configuration table allows running isolated tests or test groups:

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
- Certain test keys select entire functional groups (e.g., `filesys` tests all filesystem functions, `Drawing.new` tests all drawing primitives and methods).
- Functions not included in the filter are reported as untested in test summaries.

### Test Results and Verification

Upon test completion, sUNC outputs a short redirect link (`r.sunc.io/<id>`) pointing to **Numelon Rubiš** (`https://rubis.app`):
- Every result is cryptographically signed by the dedicated sUNC game server to prevent falsified reports.
- Tampered or synthetic results are flagged as unverified.
- Detailed failure diagnostics are output to the in-game Developer Console and the Rubiš results interface.

### sUNC Metadata API (SMA)

The sUNC documentation hosts machine-readable metadata endpoints at `https://docs.sunc.io/api`:
- **Mini Version** (`https://docs.sunc.io/api/mini.json`, ~10 KB): Lightweight map of function names to descriptions.
- **Jumbo Version** (`https://docs.sunc.io/api/jumbo.json`, ~45 KB): Enriched metadata containing type signatures, parameter descriptions, library overviews, and documentation anchors.

---

# Closures Library

The **Closures** library provides primitives to inspect, hook, clone, and wrap Luau closures and metamethods.

## Key Concepts & Constraints

- In Luau, every function is implemented as a closure (a function paired with its captured upvalues).
- **C Closures vs. Luau Closures:** Native C closures are opaque; their internal upvalues, protos, and constants cannot be extracted by bytecode inspection tools.
- `newcclosure` must wrap Luau closures into authentic C-level closures with zero upvalues and yieldability. Implementing `newcclosure` in pure Luau (e.g., via `coroutine.wrap`) fails verification.
- Hook functions must not carry more upvalues than target functions unless wrapped via `newcclosure`.

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `checkcaller` | `(): boolean` | Returns `true` if the current thread or function call originated from the executor environment. |
| `clonefunction` | `<A..., R...>(func: (A...) -> R...): (A...) -> R...` | Creates a new function closure with identical behavior and environment. Modifications or hooks to the original do not affect the clone. |
| `getfunctionhash` | `(func: function): string` | Returns the 96-character hexadecimal SHA-384 hash of a Luau function's bytecode instructions and constants. Throws an error on C closures. |
| `hookfunction` | `<A1..., R1..., A2..., R2...>(functionToHook: (A1...) -> R1..., hook: (A2...) -> R2...): (A1...) -> R1...` | Replaces `functionToHook` with `hook` and returns the original unhooked function. Supports L->L, L->C, C->L, and C->C pairs. |
| `hookmetamethod` | `(object: table \| userdata \| Object, metamethod: string, hook: function): function` | Hooks a specific metamethod on an object's metatable (e.g., `__index`, `__namecall`) using `hookfunction` and returns the original metamethod. |
| `iscclosure` | `(func: function): boolean` | Returns `true` if the specified function is a C closure. |
| `isexecutorclosure` | `(func: function): boolean` | Returns `true` if the closure was created by the executor (including scripts loaded via `loadstring` or `getscriptclosure`). |
| `islclosure` | `(func: function): boolean` | Returns `true` if the specified function is a Luau (Lua) closure. |
| `loadstring` | `<A...>(source: string, chunkname: string?): (((A...) -> any)?, string?)` | Compiles Luau source code into an executable closure. If compilation fails, returns `nil` and the syntax error message without throwing a runtime error. |
| `newcclosure` | `<A..., R...>(func: (A...) -> R...): (A...) -> R...` | Wraps a Luau closure into a C closure with zero upvalues. The wrapped function must be yieldable (e.g., capable of calling `task.wait`) and format errors as native C errors. |
| `restorefunction` | `(func: function): ()` | Restores a hooked function back to its initial original implementation, even across multiple hooks. Throws an error if the function is not currently hooked. |

---

# Debug Library

The **Debug** library exposes Luau VM bytecode inspection and modification capabilities, allowing direct access to function constants, upvalues, prototypes, and thread stack frames.

## Constraints

- All `debug.*` functions operate strictly on Luau closures. Calling them on native C closures (e.g., `print`) throws an error.
- All functions accepting a target function parameter `func` also accept an integer call stack `level: number`.
- Prototypes extracted with `debug.getproto` without `activated = true` are inactive and uncallable; attempting to call them is prohibited.

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `debug.getconstant` | `(func: function \| number, index: number): number \| string \| boolean \| nil` | Returns the constant value at `index` from the function or stack level. Returns `nil` if the index is out of bounds. |
| `debug.getconstants` | `(func: function \| number): { any }` | Returns a table containing all constants used in the function or stack level. |
| `debug.getproto` | `(func: function \| number, index: number, activated: boolean?): function \| { function }` | Returns the prototype at `index`. If `activated` is `true`, returns a table of active function closures instantiated from that proto. |
| `debug.getprotos` | `(func: function \| number): { function }` | Returns a table containing all inner function prototypes declared within the function. |
| `debug.getstack` | `(level: number, index: number?): any \| { any }` | Returns the local variable/register at `index` on stack frame `level` (where `1` is the caller). If `index` is omitted, returns all values at that stack level. |
| `debug.getupvalue` | `(func: function \| number, index: number): any` | Returns the upvalue at `index`. Throws an error if the index is invalid or if called on a C closure. |
| `debug.getupvalues` | `(func: function \| number): { [any]: any }` | Returns a key-value dictionary of all upvalues captured by the function. |
| `debug.setconstant` | `(func: function \| number, index: number, value: any): ()` | Overwrites the constant at `index` in the function bytecode with `value`. |
| `debug.setstack` | `(level: number, index: number, value: any): ()` | Overwrites the value at stack slot `index` on stack frame `level`. |
| `debug.setupvalue` | `(func: function \| number, index: number, value: any): ()` | Replaces the upvalue at `index` with `value`. Returns nothing. |

---

# Drawing Library

The `Drawing` library provides an immediate-mode 2D rendering interface for client-side visual overlays. Drawing objects are standalone graphical primitives that render directly to the game viewport and do not exist in the Roblox `DataModel`.

## Constructor

```lua
function Drawing.new(type: string): Drawing
```

Valid shape types: `"Line"`, `"Text"`, `"Image"`, `"Circle"`, `"Square"`, `"Quad"`, `"Triangle"`.

## Shared Properties & Methods

All drawing objects expose the following shared fields:

| Property | Type | Description |
| --- | --- | --- |
| `Visible` | `boolean` | Whether the object is rendered on screen (default `false`). |
| `ZIndex` | `number` | Render order depth; higher values render on top. |
| `Transparency` | `number` | Translucency factor, where `0` is fully opaque and `1` is fully transparent (see-through). |
| `Color` | `Color3` | Color tint of the drawing primitive. |
| `__OBJECT_EXISTS` | `boolean` | Read-only boolean indicating whether the underlying drawing primitive is alive. Becomes `false` after `:Destroy()`. |

| Method | Signature | Description |
| --- | --- | --- |
| `Destroy` | `(): ()` | Destroys the drawing primitive, removes it from the screen, and sets `__OBJECT_EXISTS = false`. |

## Shape Types and Properties

### Line
- `From: Vector2` - Start point coordinate.
- `To: Vector2` - End point coordinate.
- `Thickness: number` - Line width in pixels.

### Text
- `Text: string` - Rendered string content.
- `TextBounds: Vector2` - Computed read-only bounding box size.
- `Font: Drawing.Font` - Font enum (`UI`, `System`, `Plex`, `Monospace`).
- `Size: number` - Font point size.
- `Position: Vector2` - Top-left anchor coordinate.
- `Center: boolean` - Whether to center text horizontally around `Position`.
- `Outline: boolean` - Whether to render an outline border.
- `OutlineColor: Color3` - Color of the outline border.

### Image
- `Data: string` - Raw byte string of the image file (e.g. from `readfile("path.png")`).
- `Size: Vector2` - Dimensions of rendered image.
- `Position: Vector2` - Top-left coordinate.
- `Rounding: number` - Corner radius rounding.

### Circle
- `NumSides: number` - Number of polygon segments used to approximate the circle.
- `Radius: number` - Circle radius.
- `Position: Vector2` - Center point coordinate.
- `Thickness: number` - Border thickness (when `Filled = false`).
- `Filled: boolean` - Whether the circle interior is filled.

### Square
- `Size: Vector2` - Rectangle dimensions.
- `Position: Vector2` - Top-left coordinate.
- `Thickness: number` - Border thickness (when `Filled = false`).
- `Filled: boolean` - Whether the rectangle interior is filled.

### Quad
- `PointA: Vector2`, `PointB: Vector2`, `PointC: Vector2`, `PointD: Vector2` - Four vertices of the quadrilateral.
- `Thickness: number` - Border thickness (when `Filled = false`).
- `Filled: boolean` - Whether the quad interior is filled.

### Triangle
- `PointA: Vector2`, `PointB: Vector2`, `PointC: Vector2` - Three vertices of the triangle.
- `Thickness: number` - Border thickness (when `Filled = false`).
- `Filled: boolean` - Whether the triangle interior is filled.

## Drawing Utility Functions

| Function | Signature | Description |
| --- | --- | --- |
| `cleardrawcache` | `(): ()` | Destroys and removes all active drawing primitives created by `Drawing.new`. |
| `getrenderproperty` | `(drawing: Drawing, property: string): any` | Retrieves the value of a property from a `Drawing` object dynamically. |
| `isrenderobj` | `(object: any): boolean` | Returns `true` if the given value is an active, valid `Drawing` object. |
| `setrenderproperty` | `(drawing: Drawing, property: string, value: any): ()` | Assigns a value to a property on a `Drawing` object dynamically. |

---

# Encoding Library

The **Encoding** library provides high-speed binary data transformation utilities for Base64 and LZ4.

> **Note:** The legacy `crypt` library has been deprecated and removed in sUNC. sUNC triggers a warning if a `crypt` table is present in the environment. Cryptographic hashing is handled via specific functions (such as `getfunctionhash` and `getscripthash`), and data encoding functions exist as standalone globals.

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `base64decode` | `(data: string): string` | Decodes a Base64 encoded string back into raw binary or ASCII string data. |
| `base64encode` | `(data: string): string` | Encodes binary or text data into a Base64 representation. |
| `lz4compress` | `(data: string): string` | Compresses a string using the lossless LZ4 compression algorithm. |
| `lz4decompress` | `(data: string): string` | Decompresses an LZ4-compressed string back to uncompressed data. Unlike legacy UNC, specifying uncompressed size is not required. |

---

# Environment Library

The **Environment** library provides access to the executor global environment, the Roblox engine environment, the Luau registry, and garbage collection scanning.

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `filtergc` | `(filterType: "function" \| "table", filterOptions: table, returnOne: boolean?): any` | Scans and returns garbage-collected values matching specific filtering criteria. |
| `getgc` | `(includeTables: boolean?): { any }` | Returns a list of all living, non-dead garbage-collected objects (functions, userdatas, and optionally tables if `includeTables = true`). Defaults to `false`. |
| `getgenv` | `(): table` | Returns the executor's shared global environment table. |
| `getreg` | `(): { [any]: any }` | Returns the internal Luau registry table containing active threads, functions, and userdata references. |
| `getrenv` | `(): table` | Returns Roblox's native environment table. |

## `filtergc` Specifications

```luau
type FunctionFilterOptions = {
    Name: string?,            -- Function debug name to match
    IgnoreExecutor: boolean?, -- If true (default), filters out functions created by the executor
    Hash: string?,            -- Bytecode SHA-384 hash from getfunctionhash
    Constants: { any }?,      -- Must contain all specified constants
    Upvalues: { any }?,       -- Must contain all specified upvalues
}

type TableFilterOptions = {
    Keys: { any }?,           -- Table must contain all specified keys
    Values: { any }?,         -- Table must contain all specified values
    KeyValuePairs: { [any]: any }?, -- Table must contain all key-value entries
    Metatable: table?,        -- Raw metatable identity check
}
```

- When `returnOne` is `true`, `filtergc` returns the first matching object directly rather than a list.
- Bytecode filters (`Hash`, `Constants`, `Upvalues`) apply only to Luau closures and are ignored for C closures.

---

# Filesystem Library

The **Filesystem** library grants controlled file IO operations sandboxed within the executor's workspace directory. Path traversal outside the executor's sandboxed folder is prohibited.

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `appendfile` | `(path: string, data: string): ()` | Appends `data` to the end of the file at `path`. Creates the file if it does not exist. |
| `delfile` | `(path: string): ()` | Permanently deletes the file at `path`. |
| `delfolder` | `(path: string): ()` | Permanently deletes the directory at `path`. |
| `getcustomasset` | `(path: string): string` | Copies the local file to the game content directory and returns an `rbxasset://` URI for loading textures, audio, and meshes into Roblox engine objects. |
| `isfile` | `(path: string): boolean` | Returns `true` if `path` exists and points to a file. |
| `isfolder` | `(path: string): boolean` | Returns `true` if `path` exists and points to a folder. |
| `listfiles` | `(path: string): { string }` | Returns a table of file and directory paths located within `path`. |
| `loadfile` | `<A...>(path: string): (((A...) -> any)?, string?)` | Compiles Luau code from the file at `path`. Unlike `loadstring`, syntax errors throw a runtime Luau error. |
| `makefolder` | `(path: string): ()` | Creates a new directory at `path`. |
| `readfile` | `(path: string): string` | Reads and returns the entire contents of the file at `path` as a string. |
| `writefile` | `(path: string, data: string): ()` | Writes `data` to the file at `path`, replacing existing content or creating the file if absent. |

---

# Instances Library

The **Instances** library facilitates safe interaction, reference isolation, and interaction simulation with Roblox `Instance` objects.

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `cloneref` | `<T>(object: T & Instance): T` | Creates an isolated reference clone to an `Instance`. `object == cloneref(object)` evaluates to `false`, preventing detection via weak tables. |
| `compareinstances` | `(object1: Instance, object2: Instance): boolean` | Compares whether two instance references point to the same underlying Roblox object, returning `true` even when comparing `cloneref` instances. |
| `fireclickdetector` | `(detector: ClickDetector, distance: number?): ()` | Programmatically triggers a `ClickDetector` object with an optional distance parameter. |
| `fireproximityprompt` | `(prompt: ProximityPrompt): ()` | Instantly triggers a `ProximityPrompt`, bypassing its `HoldDuration` and distance constraints. |
| `firetouchinterest` | `(part1: BasePart, part2: BasePart, toggle: boolean \| number): ()` | Simulates a touch event between two parts. `toggle` accepts `true` or `0` for touch start, and `false` or `1` for touch end. |
| `getcallbackvalue` | `(object: Object, property: string): any \| nil` | Reads write-only callback properties on Roblox objects (such as `BindableFunction.OnInvoke`). Returns `nil` if unset. |
| `gethui` | `(): Instance` | Returns a hidden UI container parenting service protected from standard game script discovery. |
| `getinstances` | `(): { Instance }` | Returns a flat table containing all `Instance` objects currently tracked by the Roblox client. |
| `getnilinstances` | `(): { Instance }` | Returns all instances parented to `nil` that remain in client memory. |

---

# Metatable Library

The **Metatable** library provides access to raw metatables, allowing scripts to bypass metatable protection and freeze flags.

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `getnamecallmethod` | `(): string` | Returns the method name of the currently intercepted `__namecall` metamethod invocation. |
| `getrawmetatable` | `(object: table \| userdata): { [any]: any } \| nil` | Returns the raw metatable of `object`, ignoring any `__metatable` lock field. Returns `nil` if no metatable is attached. |
| `isreadonly` | `(table: table): boolean` | Returns `true` if `table` is protected by Luau's read-only memory lock. |
| `setrawmetatable` | `(object: table \| userdata, metatable: table): ()` | Sets the raw metatable of `object` directly. |
| `setreadonly` | `(table: table, readonly: boolean): ()` | Toggles the read-only memory lock on `table`. |

---

# Miscellaneous Library

The **Miscellaneous** library provides core executor identification and HTTP networking functions.

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `identifyexecutor` | `(): (string, string)` | Returns two strings: the name of the executor and its version (e.g., `"Potassium"`, `"1.0.0"`). |
| `request` | `(options: RequestOptions): Response` | Sends a synchronous HTTP request and yields until completion. |

## `request` Types & Details

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

### Automatic Headers

Compliant executors attach the following headers automatically to outgoing requests:
- `PREFIX-User-Identifier`: Unique persistent user ID across devices.
- `PREFIX-Fingerprint`: Hardware-bound identifier (HWID) of the client machine.
- `User-Agent`: Executor identity and build version.

---

# Reflection Library

The **Reflection** library enables manipulation of non-scriptable engine properties and Luau execution thread capabilities.

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `gethiddenproperty` | `(object: Object, property: string): (any, boolean)` | Retrieves the value of a hidden or non-scriptable property and a boolean indicating whether the property is hidden. |
| `getthreadidentity` | `(): number` | Returns the security permission identity level of the currently running Luau thread (0–8). |
| `isscriptable` | `(object: Object, property: string): boolean \| nil` | Returns whether the property is scriptable. Returns `nil` if the property does not exist on `object`. |
| `sethiddenproperty` | `(object: Object, property: string, value: any): boolean` | Writes `value` to a hidden or non-scriptable property. Returns `true` if the property was hidden and updated, or `false` if the property was not hidden. |
| `setscriptable` | `(object: Object, property: string, state: boolean): boolean \| nil` | Toggles the scriptability flag of `property`. Returns the previous scriptable state, or `nil` if the property does not exist. |
| `setthreadidentity` | `(identity: number): ()` | Elevates or sets the identity permissions of the current thread. |

---

# Scripts Library

The **Scripts** library allows scripts to retrieve bytecode, compute bytecode hashes, enumerate executing scripts, and access private script environments.

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `getcallingscript` | `(): (BaseScript \| ModuleScript)?` | Returns the script object responsible for invoking the current thread. |
| `getloadedmodules` | `(): { ModuleScript }` | Returns an array of all `ModuleScript` objects that have been loaded by the client. |
| `getrunningscripts` | `(): { BaseScript \| ModuleScript }` | Returns all active, currently running script objects. |
| `getscriptbytecode` | `(script: BaseScript \| ModuleScript): string \| nil` | Returns the raw compiled bytecode of a script. Returns `nil` if the script does not contain bytecode. |
| `getscriptclosure` | `(script: BaseScript \| ModuleScript): (((...any) -> any))?` | Generates a new closure compiled from the script's raw bytecode. Returns `nil` if no bytecode is present. |
| `getscriptfromthread` | `(thread: thread): (BaseScript \| ModuleScript)?` | Resolves the script associated with `thread` via internal weak thread references. Returns `nil` for executor threads or collected scripts. |
| `getscripthash` | `(script: BaseScript \| ModuleScript): string \| nil` | Returns the 96-character hex SHA-384 hash of the raw compressed/encrypted bytecode. Returns `nil` if the script has no bytecode. |
| `getscripts` | `(): { BaseScript \| ModuleScript }` | Returns all script instances present in the game hierarchy. |
| `getsenv` | `(script: BaseScript): table` | Returns the runtime environment table (`getfenv`) of a running `LocalScript`. |

---

# Signals Library

The **Signals** library exposes deep inspection, manual firing, and server replication for Roblox `RBXScriptSignal` connections.

## The `Connection` Object

Objects returned by `getconnections` represent active signal subscriptions with custom fields and controls:

| Field | Type | Description |
| --- | --- | --- |
| `Enabled` | `boolean` | Whether the connection is active and responds to signal triggers. |
| `ForeignState` | `boolean` | `true` if the connection was created in a separate Luau VM or foreign state (e.g. CoreScript). |
| `LuaConnection` | `boolean` | `true` if the connection callback is a Luau closure rather than a C callback. |
| `Function` | `((...any) -> any)?` | The bound callback function, or `nil` if the connection originates from a foreign state or C. |
| `Thread` | `thread?` | The thread that registered the connection, or `nil` in foreign or C contexts. |

| Method | Signature | Description |
| --- | --- | --- |
| `Connection:Fire` | `(...: any): ()` | Manually invokes the connected callback immediately with arguments. |
| `Connection:Defer` | `(...: any): ()` | Defers execution of the connected callback via `task.defer`. |
| `Connection:Disconnect` | `(): ()` | Unbinds and permanently disconnects the connection from the signal. |
| `Connection:Disable` | `(): ()` | Suspends the connection from receiving events while preserving its registration. |
| `Connection:Enable` | `(): ()` | Re-enables a previously disabled connection. |

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `firesignal` | `(signal: RBXScriptSignal, ...: any): ()` | Immediately fires all Luau connections bound to `signal`, bypassing `Workspace.SignalBehavior`. |
| `getconnections` | `(signal: RBXScriptSignal): { Connection }` | Returns a table of custom `Connection` objects subscribed to `signal`. |
| `replicatesignal` | `(signal: RBXScriptSignal, ...: any): ()` | Replicates a signal trigger to the server with matching engine arguments. Throws an error if arguments do not match signal structure. |

---

# WebSocket Library

The `WebSocket` library provides persistent, bidirectional WebSocket client connections.

> **Constraint:** sUNC requires WebSocket **client** support only (not servers). The test explicitly requires encrypted `wss://` protocol support.

## Constructor

```lua
function WebSocket.connect(url: string): WebSocket
```

Attempts a persistent connection to `url` (`ws://` or `wss://`).

## Events & Methods

| Event | Signature | Description |
| --- | --- | --- |
| `OnMessage` | `RBXScriptSignal(message: string)` | Fired whenever a text message is received from the server. |
| `OnClose` | `RBXScriptSignal()` | Fired when the WebSocket connection is closed by either peer. |

| Method | Signature | Description |
| --- | --- | --- |
| `Send` | `(message: string): ()` | Transmits a text message to the connected server. |
| `Close` | `(): ()` | Gracefully closes the active WebSocket session. |

---

# Quick Reference: All 81 Functions by Library

| Library | Function Count | Function List |
| --- | :---: | --- |
| **Closures** | 11 | `checkcaller`, `clonefunction`, `getfunctionhash`, `hookfunction`, `hookmetamethod`, `iscclosure`, `isexecutorclosure`, `islclosure`, `loadstring`, `newcclosure`, `restorefunction` |
| **Debug** | 10 | `debug.getconstant`, `debug.getconstants`, `debug.getproto`, `debug.getprotos`, `debug.getstack`, `debug.getupvalue`, `debug.getupvalues`, `debug.setconstant`, `debug.setstack`, `debug.setupvalue` |
| **Drawing** | 5 | `Drawing.new`, `cleardrawcache`, `getrenderproperty`, `isrenderobj`, `setrenderproperty` |
| **Encoding** | 4 | `base64decode`, `base64encode`, `lz4compress`, `lz4decompress` |
| **Environment** | 5 | `filtergc`, `getgc`, `getgenv`, `getreg`, `getrenv` |
| **Filesystem** | 11 | `appendfile`, `delfile`, `delfolder`, `getcustomasset`, `isfile`, `isfolder`, `listfiles`, `loadfile`, `makefolder`, `readfile`, `writefile` |
| **Instances** | 9 | `cloneref`, `compareinstances`, `fireclickdetector`, `fireproximityprompt`, `firetouchinterest`, `getcallbackvalue`, `gethui`, `getinstances`, `getnilinstances` |
| **Metatable** | 5 | `getnamecallmethod`, `getrawmetatable`, `isreadonly`, `setrawmetatable`, `setreadonly` |
| **Miscellaneous** | 2 | `identifyexecutor`, `request` |
| **Reflection** | 6 | `gethiddenproperty`, `getthreadidentity`, `isscriptable`, `sethiddenproperty`, `setscriptable`, `setthreadidentity` |
| **Scripts** | 9 | `getcallingscript`, `getloadedmodules`, `getrunningscripts`, `getscriptbytecode`, `getscriptclosure`, `getscriptfromthread`, `getscripthash`, `getscripts`, `getsenv` |
| **Signals** | 3 | `firesignal`, `getconnections`, `replicatesignal` |
| **WebSocket** | 1 | `WebSocket.connect` |
