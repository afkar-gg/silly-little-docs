# Potassium Roblox Executor API Documentation

Source: https://docs.potassium.pro/

This document is a comprehensive reference for the Potassium executor scripting API, synchronized directly with the official Potassium documentation. Potassium implements a Luau executor environment following the [UNC (Unified Naming Convention)](https://github.com/unified-naming-convention/NamingStandard) standard alongside specialized extensions including Actor multi-threading, Drawing Immediate rendering, HLSL shaders, Off-Thread Hooking (`oth`), low-level RakNet packet interception, custom signals (`PsmSignal`), and binary reflection helpers.

---

## Table of Contents

- [Overview & Conventions](#overview--conventions)
- [Actor Library](#actor-library)
- [Cache Library](#cache-library)
- [Closure Library](#closure-library)
- [Console Library](#console-library)
- [Crypt Library](#crypt-library)
- [Debug Library](#debug-library)
- [Drawing Library](#drawing-library)
  - [Drawing Objects & Properties](#drawing-objects--properties)
  - [Shader Object & HLSL Pipeline](#shader-object--hlsl-pipeline)
- [DrawingImmediate Library](#drawingimmediate-library)
- [Environment Library](#environment-library)
- [FileSystem Library](#filesystem-library)
- [Input Library](#input-library)
- [Instance Library](#instance-library)
- [Metatable Library](#metatable-library)
- [Miscellaneous Library](#miscellaneous-library)
  - [HTTP Request & Response Shapes](#http-request--response-shapes)
  - [SaveInstance Options](#saveinstance-options)
- [Oth Library (Off-Thread Hooking)](#oth-library-off-thread-hooking)
- [PsmSignal Library](#psmsignal-library)
- [RakNet Library](#raknet-library)
  - [RakNetPacket Object](#raknetpacket-object)
- [Reflection Library](#reflection-library)
- [Regex Library](#regex-library)
- [Script Library](#script-library)
- [Signal Library](#signal-library)
  - [Connection Object Shape](#connection-object-shape)
  - [Signal Whitelist Info](#signal-whitelist-info)
- [WebSocket Library](#websocket-library)

---

## Overview & Conventions

- **Type Signatures**: Signatures use typed Luau notation (e.g. `function readfile(file: string): string`).
- **Optional Parameters**: Indicated with `?` (e.g. `chunkname: string?`).
- **Global Availability**: Most core functions are exposed in the global environment (`getgenv()`), while others reside in namespaces such as `cache`, `crypt`, `debug`, `Drawing`, `DrawingImmediate`, `oth`, `PsmSignal`, `raknet`, `Regex`, and `WebSocket`.
- **UNC Compliance**: Follows standard UNC naming conventions with aliases provided for cross-executor compatibility.

---

## Actor Library

Functions for managing Roblox parallel Luau actors, communication channels, and actor-owned threads.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `create_comm_channel` | `function create_comm_channel(): (number, BindableEvent)` | — | Creates a communication channel and returns its numeric ID plus the associated `BindableEvent` for one-way communication between actors. |
| `get_comm_channel` | `function get_comm_channel(id: number): BindableEvent` | — | Returns the `BindableEvent` for an existing communication channel by ID. |
| `getactors` | `function getactors(): {Actor}` | `get_actors` | Returns a table of all active `Actor` instances in the game client (excluding deleted ones). |
| `getactorthreads` | `function getactorthreads(): {userdata}` | `get_actor_threads` | Returns a table of all threads connected to actors. |
| `getdeletedactors` | `function getdeletedactors(): {Actor}` | `get_deleted_actors` | Returns a table of actors that were deleted during runtime. |
| `is_parallel` | `function is_parallel(): boolean` | — | Returns `true` if the current code is running in parallel within an actor context. |
| `run_on_actor` | `function run_on_actor(actor: Actor, script: string, ...: any): ()` | — | Executes Luau source code on the target `Actor`. |
| `run_on_thread` | `function run_on_thread(thread: userdata, script: string, ...: any): ()` | — | Executes Luau source code on a specific actor thread. |

---

## Cache Library

Functions that operate on the executor's internal `Instance` reference cache.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `cache.invalidate` | `function cache.invalidate(instance: Instance): ()` | — | Removes an instance from the reference cache. Future references create a new wrapper, making equality (`==`) return `false`. |
| `cache.iscached` | `function cache.iscached(instance: Instance): boolean` | — | Checks whether an instance currently exists within the reference cache. |
| `cache.replace` | `function cache.replace(target: Instance, replacement: Instance): ()` | — | Replaces an instance with another instance in the internal reference cache. |
| `cloneref` | `function cloneref(instance: Instance): Instance` | `clonereference` | Returns a copied reference to an instance that bypasses equality checks against the original reference. |
| `compareinstances` | `function compareinstances(a: Instance, b: Instance): boolean` | — | Checks whether two instance references point to the same underlying Roblox engine instance. |

---

## Closure Library

Functions to inspect, hook, wrap, restore, and query Luau and C closures.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `getfunctionhash` | `function getfunctionhash(func: function): string` | — | Returns the cryptographic hash of a Luau function's bytecode. |
| `hookfunction` | `function hookfunction(target: function, hook: function): function` | — | Hooks a Lua or C function, redirecting calls to `hook` and returning a copy of the original function. |
| `iscclosure` | `function iscclosure(func: function): boolean` | — | Returns `true` if the function is a C closure. |
| `isexecutorclosure` | `function isexecutorclosure(func: function): boolean` | — | Returns `true` if the function was created by Potassium. |
| `isfunctionhooked` | `function isfunctionhooked(target: function): boolean` | — | Returns `true` if the function has been hooked. |
| `islclosure` | `function islclosure(func: function): boolean` | — | Returns `true` if the function is a Luau closure. |
| `isnewcclosure` | `function isnewcclosure(func: function): boolean` | — | Returns `true` if the function was created via `newcclosure`. |
| `isourthread` | `function isourthread(thread: thread): boolean` | — | Returns `true` if the specified thread was created by Potassium. |
| `loadstring` | `function loadstring(source: string, chunkname: string?): (function?, string?)` | — | Compiles source code into an executable chunk running in the global environment. Returns `nil` and an error string on failure. |
| `newcclosure` | `function newcclosure(func: function, name: string?): function` | — | Wraps a Luau function in a C-closure wrapper to conceal call traces. |
| `newlclosure` | `function newlclosure(func: function, name: string?): function` | — | Creates a new Luau closure wrapping `func`. |
| `restorefunction` | `function restorefunction(target: function): function` | — | Restores a previously hooked function back to its original implementation. |
| `setstackhidden` | `function setstackhidden(func: function \| number, hidden: boolean): ()` | — | Hides a function or call-stack level from call-stack inspection and detection vectors. |

---

## Console Library

Functions for managing the executor's standalone console window.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `rconsoleclear` | `function rconsoleclear(): ()` | — | Clears all text currently displayed in the console. |
| `rconsolecreate` | `function rconsolecreate(): ()` | — | Allocates and opens the console window. Only one console instance can exist. |
| `rconsoledestroy` | `function rconsoledestroy(): ()` | — | Destroys and closes the console window, clearing output. |
| `rconsoleerror` | `function rconsoleerror(text: string): ()` | — | Prints a red error message to the console (creates console if not already open). |
| `rconsoleinfo` | `function rconsoleinfo(text: string): ()` | — | Prints a dark blue informational message to the console. |
| `rconsoleinput` | `function rconsoleinput(): string` | — | Yields the calling thread until input is entered into the console, returning the string. |
| `rconsoleprint` | `function rconsoleprint(text: string): ()` | — | Prints text to the console without an automatic trailing newline. |
| `rconsolesettitle` | `function rconsolesettitle(title: string): ()` | `rconsolename` | Sets the title of the console window. |
| `rconsolewarn` | `function rconsolewarn(text: string): ()` | — | Prints a yellow warning message to the console. |

---

## Crypt Library

Cryptographic routines for hashing, HMAC, AES encryption/decryption, random byte generation, and compression.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `crypt.base64decode` | `function crypt.base64decode(data: string): string` | `base64decode` | Decodes a Base64 string into raw bytes. |
| `crypt.base64encode` | `function crypt.base64encode(data: string): string` | `base64encode` | Encodes raw data into a Base64 string. |
| `crypt.decrypt` | `function crypt.decrypt(data: string, key: string, iv: string, mode: string): string` | — | Decrypts Base64 ciphertext using AES encryption with the specified key, IV, and mode (`CBC`, `ECB`, `CTR`, `CFB`, `OFB`, `GCM`). |
| `crypt.encrypt` | `function crypt.encrypt(data: string, key: string, iv: string?, mode: string?): (string, string)` | — | Encrypts raw data using AES encryption. Returns the Base64 ciphertext and IV. Default mode is `CBC`. |
| `crypt.generatebytes` | `function crypt.generatebytes(amount: number): string` | — | Generates `amount` random bytes returned as a Base64 string. |
| `crypt.generatekey` | `function crypt.generatekey(keylength: number?): string` | — | Generates a random cryptographic key returned as Base64. |
| `crypt.hash` | `function crypt.hash(data: string, algorithm: string): string` | — | Hashes data using algorithms: `sha1`, `sha384`, `sha512`, `md5`, `sha256`, `sha3-224`, `sha3-256`, `sha3-512`. |
| `crypt.hmac` | `function crypt.hmac(key: string, data: string, algorithm: string): string` | — | Generates an HMAC (Hash-based Message Authentication Code). |
| `crypt.lz4compress` | `function crypt.lz4compress(data: string): string` | `lz4compress` | Compresses a string using the LZ4 algorithm. |
| `crypt.lz4decompress` | `function crypt.lz4decompress(data: string, originallength: number): string` | `lz4decompress` | Decompresses an LZ4 compressed string given its original uncompressed length. |
| `crypt.random` | `function crypt.random(amount: number): string` | — | Generates `amount` raw random bytes (not Base64 encoded). |

---

## Debug Library

Deep inspection and modification routines for the Luau VM, stack frames, constants, protos, and upvalues.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `debug.getcallstack` | `function debug.getcallstack(offset: number): table` | `getcallstack` | Returns an array describing the current call stack. Each entry contains: `source`, `line`, `what` (`"Lua"`/`"C"`), `name`, and `func`. |
| `debug.getconstant` | `function debug.getconstant(func: function \| number, index: number): any` | `getconstant` | Returns the constant at `index` from a function or stack level. |
| `debug.getconstants` | `function debug.getconstants(func: function \| number): {any?}` | `getconstants` | Returns an array of all constants in a function or stack level. |
| `debug.getinfo` | `function debug.getinfo(func: function \| number, what: string?): table` | `getinfo` | Returns metadata about a function or stack level. Filter options for `what`: `"n"` (name), `"s"` (source/lines), `"l"` (current line), `"f"` (function object). |
| `debug.getproto` | `function debug.getproto(func: function \| number, index: number, active: boolean?): function \| {function}` | `getproto` | Returns the nested proto function at `index`, or all active protos if `active` is `true`. |
| `debug.getprotos` | `function debug.getprotos(func: function \| number): {function}` | `getprotos` | Returns an array of all cloned proto functions inside `func`. |
| `debug.getregistry` | `function debug.getregistry(): {[any]: any}` | `getregistry` | Returns the global Lua registry table. Objects stored here are strongly held and never garbage collected. |
| `debug.getsafeenv` | `function debug.getsafeenv(object: function \| table \| thread): boolean` | `debug.isuntouched` | Returns the safe-environment flag for an object. |
| `debug.getstack` | `function debug.getstack(level: number, index: number?): any \| {any}` | `getstack` | Returns a single register value or the entire stack frame table at `level`. |
| `debug.getupvalue` | `function debug.getupvalue(func: function \| number, index: number): any` | `getupvalue` | Returns the upvalue at `index` from a function's upvalue list. |
| `debug.getupvalues` | `function debug.getupvalues(func: function \| number): {any}` | `getupvalues` | Returns an array of all upvalues belonging to a function. |
| `debug.isvalidlevel` | `function debug.isvalidlevel(level: number): boolean` | `debug.validlevel` | Validates whether a stack level index exists. |
| `debug.setconstant` | `function debug.setconstant(func: function \| number, index: number, replacement: any): ()` | `setconstant` | Overwrites a constant at `index` with a new value. |
| `debug.setinfo` | `function debug.setinfo(func: function, info: table): ()` | `setinfo` | Mutates debug info metadata surrounding `func`. |
| `debug.setname` | `function debug.setname(func: function \| number, name: string): ()` | `setname` | Renames the specified function in debugger metadata. |
| `debug.setsafeenv` | `function debug.setsafeenv(object: function \| table \| thread \| boolean, safe: boolean?): ()` | `debug.setuntouched` | Sets or marks the safe-environment flag for an object. |
| `debug.setstack` | `function debug.setstack(level: number, index: number, replacement: any): ()` | `setstack` | Overwrites a register on the stack at `level` and `index`. |
| `debug.setupvalue` | `function debug.setupvalue(func: function \| number, index: number, replacement: any): ()` | `setupvalue` | Overwrites an upvalue at `index`. |

### `debug.getinfo` Table Fields

| Field | Type | Meaning |
| :--- | :--- | :--- |
| `source` | `string` | Source chunk / script definition path. |
| `short_src` | `string` | Truncated source string used for diagnostic messages. |
| `linedefined` | `number` | First source line of the function definition. |
| `what` | `string` | `"Lua"` for Luau functions, `"C"` for C functions, or `"main"` for top-level chunks. |
| `name` | `string` | Name of the function, if determinable. |
| `namewhat` | `string` | Scope or context of the function name (`"global"`, `"local"`, `"method"`, `"field"`). |
| `nups` | `number` | Number of upvalues referenced by the closure. |
| `func` | `function` | The closure itself. |
| `currentline` | `number?` | Current executing line (when query includes `"l"`). |
| `numparams` | `number?` | Number of fixed parameters accepted by the closure. |
| `is_vararg` | `number?` | Flag indicating if the function accepts variable arguments (`...`). |

---

## Drawing Library

Retained-mode 2D drawing pipeline providing visual primitives overlaid on the Roblox game view.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `Drawing.new` | `function Drawing.new(type: string): DrawingObject` | — | Instantiates a new drawing object of the given type (`"Line"`, `"Text"`, `"Image"`, `"Circle"`, `"Square"`, `"Triangle"`, `"Quad"`, `"Font"`, `"Shader"`). |
| `Drawing.Fonts` | `table` | — | Constant table mapping font names to IDs: `UI = 0`, `System = 1`, `Plex = 2`, `Monospace = 3`. |
| `cleardrawcache` | `function cleardrawcache(): ()` | — | Destroys and clears all cached rendered drawing objects. |
| `getrenderproperty` | `function getrenderproperty(drawing: DrawingObject, property: string): any` | — | Directly reads an internal property value from a drawing object. |
| `isrenderobj` | `function isrenderobj(object: any): boolean` | — | Returns `true` if the passed value is a valid `DrawingObject`. |
| `setrenderproperty` | `function setrenderproperty(drawing: DrawingObject, property: string, value: any): ()` | — | Directly updates an internal property value on a drawing object. |

### Drawing Objects & Properties

#### Common Base Properties (Inherited by all Drawing Objects)
| Property | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Visible` | `boolean` | `false` | Whether the primitive is rendered. |
| `ZIndex` | `number` | `1` | Stacking order of the primitive. |
| `Transparency` | `number` | `1` | Opacity from `0` (invisible) to `1` (fully opaque). |
| `Color` | `Color3` | `Color3.new(0, 0, 0)` | Primary color tint of the primitive. |

#### Methods on all Drawing Objects
- `Destroy(): ()` — Destroys the drawing object and releases native resources.
- `Remove(): ()` — Alias for `Destroy()`.

#### Specific Primitive Properties
| Object | Properties | Defaults | Notes |
| :--- | :--- | :--- | :--- |
| **`Line`** | `From: Vector2`<br>`To: Vector2`<br>`Thickness: number` | `Vector2.zero`<br>`Vector2.zero`<br>`1` | 2D line segment between two coordinates. |
| **`Text`** | `Text: string`<br>`Size: number`<br>`Center: boolean`<br>`Outline: boolean`<br>`OutlineColor: Color3`<br>`Position: Vector2`<br>`Font: number`<br>`TextBounds: Vector2` | `""`<br>`18`<br>`false`<br>`false`<br>`Color3.new(0,0,0)`<br>`Vector2.zero`<br>`0`<br>*(Read Only)* | Text string rendered with specified font ID. `TextBounds` returns rendered dimensions. |
| **`Image`** | `Data: string`<br>`Size: Vector2`<br>`Position: Vector2`<br>`Rounding: number` | `""`<br>`Vector2.new(0,0)`<br>`Vector2.new(0,0)`<br>`0` | Raw image byte data (PNG/JPG). Supports corner rounding. |
| **`Circle`** | `Position: Vector2`<br>`Radius: number`<br>`Thickness: number`<br>`Filled: boolean`<br>`NumSides: number` | `Vector2.zero`<br>`0`<br>`1`<br>`false`<br>`250` | Radial primitive. Higher `NumSides` gives smoother perimeter. |
| **`Square`** | `Position: Vector2`<br>`Size: Vector2`<br>`Thickness: number`<br>`Filled: boolean` | `Vector2.zero`<br>`Vector2.zero`<br>`1`<br>`false` | Axis-aligned rectangle. |
| **`Triangle`** | `PointA: Vector2`<br>`PointB: Vector2`<br>`PointC: Vector2`<br>`Thickness: number`<br>`Filled: boolean` | `Vector2.zero`<br>`Vector2.zero`<br>`Vector2.zero`<br>`1`<br>`false` | Three-vertex polygon primitive. |
| **`Quad`** | `PointA: Vector2`<br>`PointB: Vector2`<br>`PointC: Vector2`<br>`PointD: Vector2`<br>`Thickness: number`<br>`Filled: boolean` | `Vector2.zero`<br>`Vector2.zero`<br>`Vector2.zero`<br>`Vector2.zero`<br>`1`<br>`false` | Four-vertex arbitrary polygon. |
| **`Font`** | `Data: string` | `""` | Raw font binary data (TTF/OTF) used for custom typography. |
| **`Shader`** | `Vertex: string`<br>`Pixel: string`<br>`Position: Vector2`<br>`Size: Vector2` | `""`<br>`""`<br>`Vector2.zero`<br>`Vector2.zero` | Custom DirectX HLSL shader object. Call `:Create()` after updating shader code. |

### Shader Object & HLSL Pipeline

Potassium supports custom hardware HLSL shader rendering on the GPU via `Drawing.new("Shader")`.

- Entry points for both `Vertex` and `Pixel` shaders **must** be named `main`.
- `:Create()` must be invoked every time `Vertex` or `Pixel` code is modified.
- If `Size` is left as `Vector2.zero`, the shader covers the entire game window.
- The GPU exposes uniform parameters bound to constant buffer `b0`:

```c
cbuffer Params : register(b0)
{
    float time;
    float2 resolution;
    float2 mouse;
};
```

---

## DrawingImmediate Library

High-performance immediate-mode rendering system that executes per-frame render callbacks without retaining object state.

| Function | Signature | Description |
| :--- | :--- | :--- |
| `DrawingImmediate.GetPaint` | `function DrawingImmediate.GetPaint(zIndex: int): PsmSignal` | Returns an event that fires every render step for the specified `zIndex`. Lower `zIndex` events fire first. All `DrawingImmediate.*` primitives must be called inside this callback. |
| `DrawingImmediate.Line` | `function DrawingImmediate.Line(from: Vector2, to: Vector2, color: Color3, opacity: number, thickness: number): ()` | Renders an immediate line segment. |
| `DrawingImmediate.Circle` | `function DrawingImmediate.Circle(center: Vector2, radius: number, color: Color3, opacity: number, num_sides: int, thickness: number): ()` | Renders an immediate outline circle. |
| `DrawingImmediate.FilledCircle` | `function DrawingImmediate.FilledCircle(center: Vector2, radius: number, color: Color3, num_sides: int, opacity: number): ()` | Renders a filled circle. |
| `DrawingImmediate.Triangle` | `function DrawingImmediate.Triangle(a: Vector2, b: Vector2, c: Vector2, color: Color3, opacity: number, thickness: number): ()` | Renders an immediate wireframe triangle. |
| `DrawingImmediate.FilledTriangle` | `function DrawingImmediate.FilledTriangle(a: Vector2, b: Vector2, c: Vector2, color: Color3, opacity: number): ()` | Renders an immediate filled triangle. |
| `DrawingImmediate.Rectangle` | `function DrawingImmediate.Rectangle(topLeft: Vector2, size: Vector2, color: Color3, opacity: number, rounding: number, thickness: number): ()` | Renders a rounded outline rectangle. |
| `DrawingImmediate.FilledRectangle` | `function DrawingImmediate.FilledRectangle(topLeft: Vector2, size: Vector2, color: Color3, opacity: number, rounding: number): ()` | Renders a rounded filled rectangle. |
| `DrawingImmediate.Quad` | `function DrawingImmediate.Quad(a: Vector2, b: Vector2, c: Vector2, d: Vector2, color: Color3, opacity: number, thickness: number): ()` | Renders a wireframe quad. |
| `DrawingImmediate.FilledQuad` | `function DrawingImmediate.FilledQuad(a: Vector2, b: Vector2, c: Vector2, d: Vector2, color: Color3, opacity: number): ()` | Renders a filled quad. |
| `DrawingImmediate.Text` | `function DrawingImmediate.Text(pos: Vector2, font: number, fontSize: number, color: Color3, opacity: number, text: string, center: boolean): ()` | Renders immediate text. |
| `DrawingImmediate.OutlinedText` | `function DrawingImmediate.OutlinedText(pos: Vector2, font: number, fontSize: number, color: Color3, opacity: number, outlineColor: Color3, outlineOpacity: number, text: string, center: boolean): ()` | Renders immediate outlined text. |

---

## Environment Library

Functions for accessing and inspecting global environments, the garbage collector, and script threads.

| Function | Signature | Description |
| :--- | :--- | :--- |
| `filtergc` | `function filtergc(filterType: string, filterOptions: table, filterOne: boolean): {any} \| any?` | Filters objects from the garbage collection list matching specified criteria (`"type"`, `"function"`, `"table"`, etc.). If `filterOne` is true, returns the first match. |
| `getgc` | `function getgc(includeTables: boolean?): {any}` | Returns an array of objects tracked by the Luau garbage collector. Tables are excluded unless `includeTables` is `true`. |
| `getgenv` | `function getgenv(): {[string]: any}` | Returns Potassium's custom shared global environment table. |
| `getreg` | `function getreg(): {[any]: any}` | Returns the global Lua registry table. |
| `getrenv` | `function getrenv(): {[string]: any}` | Returns Roblox's native global environment table inherited by game scripts. |
| `getsenv` | `function getsenv(script: LocalScript \| ModuleScript): {[any]: any}?` | Returns the global environment table belonging to a `LocalScript` or `ModuleScript`. |
| `gettenv` | `function gettenv(thread: thread?): {[any]: any}` | Returns the environment table of the specified thread (or the current thread if omitted). |

---

## FileSystem Library

File I/O operations confined to the executor's isolated workspace sandbox.

| Function | Signature | Description |
| :--- | :--- | :--- |
| `appendfile` | `function appendfile(file: string, contents: string): ()` | Appends text to an existing file in the workspace directory. |
| `delfile` | `function delfile(file: string): ()` | Deletes a file at the specified path. |
| `delfolder` | `function delfolder(path: string): ()` | Deletes a folder and all its contents at the specified path. |
| `dofile` | `function dofile(file: string): ()` | Reads, compiles, and executes a file in a new thread. |
| `isfile` | `function isfile(path: string): boolean` | Returns `true` if the path points to an existing file. |
| `isfolder` | `function isfolder(path: string): boolean` | Returns `true` if the path points to an existing directory. |
| `listfiles` | `function listfiles(path: string): {string}` | Returns an array of paths for all files and folders in a directory. |
| `loadfile` | `function loadfile(file: string, chunkname: string?): (function?, string?)` | Compiles a file's contents into a Luau function without executing it. |
| `makefolder` | `function makefolder(path: string): ()` | Creates a new folder at the specified path. |
| `readfile` | `function readfile(file: string): string` | Reads and returns the entire contents of a file as a string. |
| `writefile` | `function writefile(file: string, contents: string): ()` | Writes contents to a file, creating it if it does not exist or overwriting it. |

---

## Input Library

Simulates hardware keyboard and mouse inputs. **Inputs are only dispatched if the Roblox game client window is currently focused/active.**

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `isrbxactive` | `function isrbxactive(): boolean` | `isgameactive`, `iswindowactive` | Returns `true` if the Roblox window is currently active and focused. |
| `keypress` | `function keypress(keycode: number): ()` | — | Simulates pressing down a virtual key code. |
| `keyrelease` | `function keyrelease(keycode: number): ()` | — | Simulates releasing a virtual key code. |
| `keytap` | `function keytap(keycode: number): ()` | `keyclick` | Simulates pressing and immediately releasing a virtual key code. |
| `mouse1click` | `function mouse1click(): ()` | — | Dispatches a left mouse button click (down and up). |
| `mouse1press` | `function mouse1press(): ()` | — | Dispatches a left mouse button down event. |
| `mouse1release` | `function mouse1release(): ()` | — | Dispatches a left mouse button release event. |
| `mouse2click` | `function mouse2click(): ()` | — | Dispatches a right mouse button click (down and up). |
| `mouse2press` | `function mouse2press(): ()` | — | Dispatches a right mouse button down event. |
| `mouse2release` | `function mouse2release(): ()` | — | Dispatches a right mouse button release event. |
| `mousemoveabs` | `function mousemoveabs(x: number, y: number): ()` | — | Moves the mouse cursor to absolute screen coordinates `(x, y)`. |
| `mousemoverel` | `function mousemoverel(x: number, y: number): ()` | — | Moves the mouse cursor relative to its current screen position. |
| `mousescroll` | `function mousescroll(scroll: number): ()` | — | Simulates mouse wheel scrolling by `scroll` pixels. |

---

## Instance Library

Core helpers for interacting with Roblox instances, prompts, clicks, and hidden UI containers.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `fireclickdetector` | `function fireclickdetector(clickdetector: ClickDetector, distance?: number, signal?: string): ()` | — | Triggers a `ClickDetector`'s click or hover signal at an optional simulated distance. |
| `fireproximityprompt` | `function fireproximityprompt(proximityprompt: ProximityPrompt): ()` | — | Instantly triggers a `ProximityPrompt`'s `Triggered` signal. |
| `firetouchinterest` | `function firetouchinterest(source: Instance, target: Instance, touch: boolean): ()` | — | Simulates a touch event between two parts (`touch = true` for `Touched`, `false` for `TouchEnded`). |
| `getcallbackvalue` | `function getcallbackvalue(instance: Instance, property: string): function?` | `getcallbackmember` | Returns the bound callback function assigned to an otherwise unindexable callback property. |
| `getcustomasset` | `function getcustomasset(filepath: string): string` | — | Registers a local workspace file as a Roblox content asset and returns an `rbxasset://` URI. |
| `gethui` | `function gethui(): Instance` | `get_hidden_gui` | Returns an internal CoreGui/RobloxGui container that is completely hidden from game script scans. |
| `getinstances` | `function getinstances(): {Instance}` | — | Returns an array of every instance currently residing in memory on the client. |
| `getnilinstances` | `function getnilinstances(): {Instance}` | — | Returns an array of instances in memory whose `Parent` is `nil`. |
| `getrendersteppedlist` | `function getrendersteppedlist(): {function}` | — | Returns a table of all callback functions currently bound via `RunService:BindToRenderStep`. |

---

## Metatable Library

Routines for inspecting, hooking, and modifying metatables while bypassing native `__metatable` protections.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `getnamecallmethod` | `function getnamecallmethod(): string?` | `get_namecall_method` | Returns the name of the method that invoked the current `__namecall` metamethod. |
| `getrawmetatable` | `function getrawmetatable(meta: table \| userdata): table?` | `debug.getmetatable` | Returns the actual metatable of a table or userdata, ignoring the `__metatable` protection field. |
| `hookmetamethod` | `function hookmetamethod(object: Instance \| table \| userdata, method: string, hook: function): function?` | — | Hooks a metamethod (e.g. `"__namecall"`, `"__index"`, `"__newindex"`) on `object` and returns the original metamethod closure. |
| `isreadonly` | `function isreadonly(table: table): boolean` | `is_readonly` | Checks whether a table is read-only / frozen. |
| `makereadonly` | `function makereadonly(table: table): ()` | — | Freezes a table to read-only mode while bypassing metatable locks. |
| `makewritable` | `function makewritable(table: table): ()` | — | Unfreezes a read-only table, allowing writes. |
| `setnamecallmethod` | `function setnamecallmethod(method: string): ()` | `set_namecall_method` | Sets the method string returned by `getnamecallmethod()`. |
| `setrawmetatable` | `function setrawmetatable(tbl: table \| userdata, meta: table): table?` | `debug.setmetatable` | Overwrites the metatable of a table or userdata even when protected by `__metatable`. |
| `setreadonly` | `function setreadonly(table: table, readonly: boolean): ()` | `set_readonly` | Toggles the read-only / frozen status of a table. |

---

## Miscellaneous Library

Utilities for executor identification, teleport queues, clipboard access, HTTP networking, place saving, and Fast Flags.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `clearteleportqueue` | `function clearteleportqueue(): ()` | `clear_teleport_queue`, `clearqueueonteleport` | Clears all code queued to execute upon teleporting. |
| `getfflag` | `function getfflag(fflag: string): string` | `getfastflag` | Retrieves the current value of a Roblox Fast Flag. |
| `getfflagtype` | `function getfflagtype(fflag: string): string` | `getfastflagtype` | Returns the value type string of a Roblox Fast Flag (`"bool"`, `"int"`, `"string"`). |
| `getfpscap` | `function getfpscap(): number` | — | Returns the currently configured FPS cap on the client. |
| `gethwid` | `function gethwid(): string` | `get_hwid`, `get_user_identifier` | Returns a unique hardware identification hash for the user's system. |
| `getobjects` | `function getobjects(self: any?, asset: string): {Instance}` | — | Emulates `game:GetObjects(asset)` by retrieving instances from a Roblox asset URL. |
| `httpget` | `function httpget(self: any?, url: string): string` | `HttpGet` | Performs an HTTP GET request to `url` and returns the body string. |
| `identifyexecutor` | `function identifyexecutor(): (string, string)` | `getexecutorname` | Returns the executor name (`"Potassium"`) and version string. |
| `messagebox` | `function messagebox(text: string, caption: string, flags: number): number` | `messageboxasync` | Displays a native Windows message box and returns the integer response code. |
| `queueonteleport` | `function queueonteleport(code: string): ()` | `queue_on_teleport` | Queues source code to execute immediately upon joining a new place via teleport. |
| `request` | `function request(options: HttpRequest): HttpResponse` | `http_request`, `http.request` | Performs an asynchronous HTTP request with customizable methods, headers, and cookies. |
| `saveinstance` | `function saveinstance(object: Instance?, options: table?): ()` | — | Serializes and saves an instance tree (defaulting to `game`) into an `.rbxl` or `.rbxm` file. |
| `setclipboard` | `function setclipboard(text: string): ()` | `toclipboard` | Copies text to the operating system clipboard. |
| `setfflag` | `function setfflag(fflag: string, value: any): ()` | `setfastflag` | Sets or overrides a Roblox Fast Flag value. |
| `setfpscap` | `function setfpscap(cap: number): ()` | — | Sets the maximum target frame rate (`0` disables the cap). |
| `setrbxclipboard` | `function setrbxclipboard(text: string): ()` | — | Sets `.rbxm`/`.rbxmx` XML model data into the Roblox Studio clipboard. |

### HTTP Request & Response Shapes

#### `HttpRequest` Table
| Field | Type | Description |
| :--- | :--- | :--- |
| `Url` | `string` | Target request endpoint. |
| `Method` | `string` | HTTP method: `"GET"`, `"POST"`, `"PUT"`, `"DELETE"`, `"PATCH"`, `"HEAD"`. |
| `Body` | `string?` | Optional request payload body. |
| `Headers` | `table?` | Key-value dictionary of HTTP request headers. |
| `Cookies` | `table?` | Key-value dictionary of cookies to send. |

#### `HttpResponse` Table
| Field | Type | Description |
| :--- | :--- | :--- |
| `Success` | `boolean` | `true` if the HTTP request completed without transport errors. |
| `StatusCode` | `number` | HTTP status code (e.g. `200`, `404`, `500`). |
| `StatusMessage` | `string` | HTTP status message string (e.g. `"OK"`, `"Not Found"`). |
| `Headers` | `table` | Dictionary of HTTP response headers. |
| `Body` | `string` | Raw response body text. |

### SaveInstance Options

| Option | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `FileName` | `string` | Varies | Name of the exported output file. |
| `Decompile` | `boolean` | `false` | Whether to decompile scripts during serialization. |
| `NilInstances` | `boolean` | `false` | Include instances parented to `nil`. |
| `RemovePlayerCharacters` | `boolean` | `true` | Exclude player avatar models from the save. |
| `SavePlayers` | `boolean` | `false` | Include player instances in `Players`. |
| `DecompileTimeout` | `number` | `10` | Maximum time allowed per script decompilation (seconds). |
| `MaxThreads` | `number` | `3` | Worker thread count allocated to script decompilation. |
| `DecompileIgnore` | `table` | `{"Chat", "CoreGui", "CorePackages"}` | Services whose descendant scripts are skipped. |
| `ShowStatus` | `boolean` | `true` | Output progress status messages during export. |
| `IgnoreDefaultProps` | `boolean` | `true` | Omit default engine property values to reduce file size. |
| `IsolateStarterPlayer` | `boolean` | `true` | Moves StarterPlayer descendants into dedicated folders. |

---

## Oth Library (Off-Thread Hooking)

Potassium provides an isolated **Off-Thread Hooking (`oth`)** architecture designed specifically for hooking C functions that trigger detections when altered via standard `hookfunction`. Hook handlers run on separate, dedicated Luau threads.

| Function | Signature | Description |
| :--- | :--- | :--- |
| `oth.get_original_thread` | `function oth.get_original_thread(): thread?` | Returns the original thread that initiated the call into the hook, or `nil` if the calling thread is not an OTH hook thread. |
| `oth.get_root_callback` | `function oth.get_root_callback(): function` | Retrieves a callable reference to invoke the original unhooked function within the context of the hook thread. |
| `oth.hook` | `function oth.hook(target: function, hook: function): function` | Hooks a target C/Lua function using off-thread execution. Returns a callable closure referencing the original target. |
| `oth.is_hook_thread` | `function oth.is_hook_thread(): boolean` | Checks whether the current thread is executing inside an OTH hook handler. |
| `oth.unhook` | `function oth.unhook(target: function): ()` | Unhooks a function previously hooked with `oth.hook`. |

### Example: Bypassing Anti-Cheat Services via OTH
```lua
oth.hook(game.GetService, function(self, service)
    local thread = oth.get_original_thread()
    local callerScript = getscriptfromthread(thread)

    -- Block nil-parented anti-cheat scripts from scanning services
    if callerScript and callerScript.Parent == nil then
        return task.wait(9e9)
    end

    return oth.get_root_callback()(self, service)
end)
```

---

## PsmSignal Library

A lightweight, high-performance custom signal implementation built into Potassium.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `PsmSignal.new` | `function PsmSignal.new(): PsmSignal` | `Signal.new` | Constructs a new `PsmSignal` object. |

### `PsmSignal` Methods
| Method | Description |
| :--- | :--- |
| `Connect(callback: function): PsmConnection` | Connects a callback that executes whenever the signal fires. Returns a `PsmConnection`. |
| `Once(callback: function): PsmConnection` | Connects a callback that disconnects itself after firing once. |
| `Wait(): ...any` | Yields the calling thread until the signal is fired, returning any arguments passed to `Fire`. |
| `Fire(...: any): ()` | Dispatches the signal, invoking all connected listeners with the provided arguments. |

### `PsmConnection` Methods
| Method | Description |
| :--- | :--- |
| `Disconnect(): ()` | Disconnects the connection listener from the signal. |

---

## RakNet Library

Low-level networking library for intercepting, modifying, spoofing, and blocking raw RakNet packets exchanged between the client and server.

> [!WARNING]
> In order for `raknet` functions to execute, RakNet packet manipulation must be enabled in the internal UI settings. Misuse can trigger server-side sanity kicks or bans.

| Function | Signature | Description |
| :--- | :--- | :--- |
| `raknet.add_receive_hook` | `function raknet.add_receive_hook(callback: (packet: RakNetPacket) -> nil): ()` | Registers a listener invoked whenever an incoming packet is received from the server before engine processing. |
| `raknet.add_send_hook` | `function raknet.add_send_hook(callback: (packet: RakNetPacket) -> nil): ()` | Registers a listener invoked before an outgoing packet is dispatched to the server. |
| `raknet.remove_receive_hook` | `function raknet.remove_receive_hook(callback: function): ()` | Removes a registered receive hook callback. |
| `raknet.remove_send_hook` | `function raknet.remove_send_hook(callback: function): ()` | Removes a registered send hook callback. |
| `raknet.send` | `function raknet.send(packet: buffer \| table \| string, priority: int, reliability: int, orderingChannel: int): ()` | Sends a raw packet directly over the RakNet connection. |

### RakNetPacket Object

#### Fields
| Field | Type | Description |
| :--- | :--- | :--- |
| `PacketId` | `number` | The numeric RakNet packet identifier (e.g. `0x1B` for `ID_TIMESTAMP`). |
| `Size` | `number` | Payload byte length. |
| `Priority` | `number` | Packet transmission priority. |
| `Reliability` | `number` | RakNet reliability mode (e.g. Unreliable, Reliable, Ordered). |
| `OrderingChannel` | `number` | Channel index used for ordered packet streams. |
| `AsBuffer` | `buffer` | Payload data represented as a native Luau `buffer`. |
| `AsArray` | `table` | Payload data represented as an array of byte numbers. |
| `AsString` | `string` | Payload data represented as a raw binary string. |

#### Methods
- `SetData(data: buffer | table | string | {number}): ()` — Overwrites the packet payload prior to sending/receiving.
- `Block(): ()` — Drops and cancels the packet, preventing it from reaching its destination.

---

## Reflection Library

Low-level Roblox reflection helpers for querying hidden properties, binary string descriptors, network ownership, and simulation limits.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `getbspval` | `function getbspval(instance: Instance, property: string, base64: boolean): string` | — | Reads conventionally unreadable `BinaryString` properties such as `Terrain.SmoothGrid`, `PartOperation.PhysicsData`, and `BinaryStringValue.Value`. |
| `gethiddenproperties` | `function gethiddenproperties(instance: Instance): {[string]: any}` | — | Returns a dictionary of all non-scriptable properties and their current values. |
| `gethiddenproperty` | `function gethiddenproperty(instance: Instance, property: string): (any, boolean)` | `gethiddenprop` | Returns the value of a property and a boolean indicating whether the property is hidden. |
| `getpcd` | `function getpcd(meshPart: Instance): (string, string)` | `getpcdprop` | Returns the 16-byte hash and binary data for a `TriangleMeshPart`'s `PhysicalConfigData` property. |
| `getproperties` | `function getproperties(instance: Instance): {[string]: any}` | — | Returns a dictionary of all properties on an instance, including hidden and non-scriptable entries. |
| `getproximitypromptduration` | `function getproximitypromptduration(prompt: ProximityPrompt): number` | — | Returns the hold duration value of a `ProximityPrompt`. |
| `getsimulationradius` | `function getsimulationradius(): number` | — | Returns the simulation radius of the `LocalPlayer`. |
| `isnetworkowner` | `function isnetworkowner(instance: Instance): boolean` | — | Returns `true` if the local player is currently the network physics owner of the specified part. |
| `isscriptable` | `function isscriptable(instance: Instance, property: string): boolean` | — | Checks whether a property is scriptable. |
| `sethiddenproperty` | `function sethiddenproperty(instance: Instance, property: string, value: any): boolean` | `sethiddenprop` | Sets the value of a hidden property and returns whether it was hidden. |
| `setproximitypromptduration` | `function setproximitypromptduration(prompt: ProximityPrompt, duration: number): ()` | — | Overrides the hold duration for a `ProximityPrompt`. |
| `setscriptable` | `function setscriptable(instance: Instance, property: string, scriptable: boolean): ()` | — | Toggles whether a property can be indexed by Luau scripts. |
| `setsimulationradius` | `function setsimulationradius(simulationRadius: number): ()` | — | Overrides the client simulation radius. |

---

## Regex Library

PCRE/ECMAScript regular expression engine built into Potassium.

| Function | Signature | Description |
| :--- | :--- | :--- |
| `Regex.Escape` | `function Regex.Escape(contents: string): string` | Escapes special regex characters within a string literal. |
| `Regex.new` | `function Regex.new(pattern: string): Regex` | Compiles a regular expression pattern into a `Regex` matcher object. |

### `Regex` Object Methods
| Method | Signature | Description |
| :--- | :--- | :--- |
| `Match` | `function Regex:Match(contents: string): match?` | Matches the string against the regex and returns the first match object. |
| `MatchMany` | `function Regex:MatchMany(contents: string): {match}` | Matches the string against the regex and returns an array of all matches. |
| `Replace` | `function Regex:Replace(contents: string, replacement: string): string` | Replaces the first matching substring with `replacement` (supports capturing groups such as `$1`). |

---

## Script Library

Functions for decompiling, disassembling bytecode, inspecting script environments, and setting thread security capabilities.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `decompile` | `function decompile(target: function \| LuaSourceContainer \| string): string` | — | Decompiles a script, closure, or bytecode string back into readable Luau pseudocode. |
| `dumpbytecode` | `function dumpbytecode(target: function): string` | — | Dumps a Luau function into raw Luau bytecode format (target cannot be a C function). |
| `getcallingscript` | `function getcallingscript(): BaseScript?` | — | Returns the script responsible for triggering the current function invocation. |
| `getloadedmodules` | `function getloadedmodules(): {ModuleScript}` | — | Returns a table of all `ModuleScript` instances loaded in the game. |
| `getrunningscripts` | `function getrunningscripts(): {BaseScript}` | — | Returns a table of all scripts currently running in the game. |
| `getscriptbytecode` | `function getscriptbytecode(script: BaseScript): string` | `dumpstring` | Extracts raw compiled Luau bytecode from a script. |
| `getscriptclosure` | `function getscriptclosure(script: BaseScript): function` | `getscriptfunction` | Returns the root main closure function of a script. |
| `getscriptfromthread` | `function getscriptfromthread(thread: thread): BaseScript?` | — | Retrieves the script instance associated with an active thread. |
| `getscripthash` | `function getscripthash(script: BaseScript): string` | — | Generates a SHA384 hash of the script's compiled bytecode. |
| `getscripts` | `function getscripts(): {BaseScript}` | — | Returns an array of every script instance in the game client. |
| `getscriptthread` | `function getscriptthread(script: BaseScript): thread?` | — | Retrieves the root thread associated with a script. |
| `getthreadidentity` | `function getthreadidentity(): number` | `getidentity`, `getthreadcontext`, `get_thread_identity`, `getthreadcaps` | Returns the security identity context of the current thread (e.g. `8`). |
| `setthreadidentity` | `function setthreadidentity(identity: number): ()` | `setidentity`, `setthreadcontext`, `set_thread_identity` | Changes the security identity context of the current thread. |

---

## Signal Library

Tools for inspecting, firing, deferring, and replicating Roblox signals and connections.

| Function | Signature | Description |
| :--- | :--- | :--- |
| `cansignalreplicate` | `function cansignalreplicate(signal: RBXScriptSignal): boolean` | Checks whether an `RBXScriptSignal` is configured to replicate events to the server. |
| `firesignal` | `function firesignal(signal: RBXScriptSignal, ...: any?): ()` | Fires all client connections attached to an `RBXScriptSignal`. |
| `getconnection` | `function getconnection(signal: RBXScriptSignal, index: number): Connection` | Returns a specific `Connection` object attached to a signal by index. |
| `getconnections` | `function getconnections(signal: RBXScriptSignal): {Connection}` | Returns an array of all `Connection` objects attached to an `RBXScriptSignal`. |
| `getsignalarguments` | `function getsignalarguments(signal: RBXScriptSignal): {string}` | Returns expected parameter types for a signal. |
| `getsignalargumentsinfo` | `function getsignalargumentsinfo(signal: RBXScriptSignal): {ArgumentInfo}` | Returns argument layout and metadata used for signal replication. |
| `getsignalwhitelist` | `function getsignalwhitelist(): {SignalWhitelistInfo}` | Returns all signal descriptors approved in Roblox's client-to-server replication whitelist. |
| `replicatesignal` | `function replicatesignal(signal: RBXScriptSignal, ...: any?): ()` | Replicates and fires a signal directly on the server when whitelisted. |

### Connection Object Shape

| Field | Type | Description |
| :--- | :--- | :--- |
| `Enabled` | `boolean` | Whether the connection receives fired events. |
| `ForeignState` | `boolean` | Whether the connection listener originates from an external Luau state (e.g. CoreScripts). |
| `LuaConnection` | `boolean` | Whether the connection was created via Luau code rather than internal engine C++ logic. |
| `Function` | `function?` | The callback closure bound to this connection (nil when `ForeignState` is true). |
| `Thread` | `thread?` | The thread that registered the connection (nil when `ForeignState` is true). |
| `Script` | `LuaSourceContainer?` | The script that registered the connection. |

#### Methods on Connection
| Method | Description |
| :--- | :--- |
| `Fire(...: any): ()` | Immediately invokes the connection handler with the given arguments. |
| `Defer(...: any): ()` | Defers invocation of the connection handler with the given arguments. |
| `Disconnect(): ()` | Disconnects the listener from the signal. |
| `Disable(): ()` | Temporarily suspends the connection without disconnecting it. |
| `Enable(): ()` | Restores a previously disabled connection. |

### Signal Whitelist Info

Each element returned by `getsignalwhitelist()` contains:
- `Parent`: Name of the parent class (e.g. `"ClickDetector"`).
- `Event`: Name of the replicated event (e.g. `"MouseClick"`).

---

## WebSocket Library

WebSocket client implementation for communicating with external servers, bots, and local tools.

| Function | Signature | Aliases | Description |
| :--- | :--- | :--- | :--- |
| `WebSocket.connect` | `function WebSocket.connect(url: string): WebSocket` | `WebSocket.new` | Opens a bidirectional WebSocket connection to the specified endpoint. |

### `WebSocket` Object Interface

#### Methods
- `Send(message: string): ()` — Transmits a text message over the active WebSocket connection.
- `Close(): ()` — Terminates and closes the WebSocket connection.

#### Events
- `OnMessage(message: string)` — Fires when an incoming message is received.
- `OnClose()` — Fires when the connection is terminated by the server or client.

---

## Security & Operational Notes

- **Off-Thread Hooking (`oth`)**: Prefer `oth.hook` over standard `hookfunction` when modifying sensitive Roblox engine C methods (e.g., `game.GetService`, `Instance.new`, `game.FindService`) to evade call-stack detection.
- **RakNet Safety**: Modifying packet timestamps or structure can trigger server desyncs or integrity violations. Keep modifications isolated to controlled testing.
- **Signal Replication**: Use `getsignalwhitelist()` and `getsignalargumentsinfo()` to verify parameter validity before invoking `replicatesignal` to prevent client kicks.
- **Immediate vs Retained Drawing**: For dynamic elements such as FOV rings, ESP boxes, and aim indicators that update every frame, `DrawingImmediate` inside `DrawingImmediate.GetPaint(zIndex)` avoids heap allocation and cache bloat associated with retaining `DrawingObject` primitives.
