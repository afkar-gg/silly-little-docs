# sUNC Documentation

Source: https://docs.sunc.su/

sUNC (senS' Unified Naming Convention) is a standardized set of executor API functions that are tested and documented. This documentation covers only functions that are actively tested by the sUNC test script.

> **Note:** sUNC has largely diverged from the original UNC to become better shaped by executor developers and users. Some functions have been deprecated/removed and others may have been added.

## Overview

sUNC provides a comprehensive API organized into the following libraries:

- **Closures** - Inspect, modify, and create Luau closures
- **Debug** - Access bytecode-level function internals
- **Drawing** - Render 2D graphics on screen
- **Encoding** - Base64 and LZ4 data transformations
- **Environment** - Access executor and Roblox environments
- **Filesystem** - Virtual file system operations
- **Instances** - Manipulate Roblox Instance objects
- **Metatable** - Advanced metatable interaction
- **Miscellaneous** - HTTP requests and executor identification
- **Reflection** - Access hidden properties and thread identities
- **Scripts** - Inspect and interact with script objects
- **Signals** - Manipulate RBXScriptSignal connections
- **WebSocket** - WebSocket client connections

## The sUNC Test Script

```lua
getgenv().sUNCDebug = {
    ["printcheckpoints"] = false,
    ["delaybetweentests"] = 0,
    ["printtesttimetaken"] = false,
}
loadstring(game:HttpGet("https://script.sunc.su/"))()
```

> As of sUNC v2.0, the test only runs inside the official testing game. The latest one may be retrieved from the sUNC Discord server.

---

# Closures Library

The **Closures** library enables the inspection, modification and creation of Luau closures with precise control.

## What is a closure?

The term "closure" comes from functional programming and refers to a function **plus** the environment it carries (its **upvalues**). In Luau, **every function is implemented as a closure** implicitly, even if it doesn't capture anything.

## Capabilities

- **Hook** existing functions or metamethods with `hookfunction` and `hookmetamethod`
- **Restore** hooked functions with `restorefunction`
- **Check** whether the current execution is from your script using `checkcaller`
- **Clone** a function while keeping the same behavior with `clonefunction`
- **Wrap** a Luau closure into a C closure using `newcclosure`
- **Check** a function's closure type with `iscclosure`, `islclosure` or `isexecutorclosure`
- **Hash** a function with `getfunctionhash`
- **Compile** and run code at runtime using `loadstring`

## Limitations

- You cannot inspect **true C closures' internals** - they are opaque by design
- Attempting to implement `newcclosure` in Luau (e.g. via `coroutine.wrap`) **will fail sUNC verification**

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `checkcaller` | `(): boolean` | Returns whether the current call originated from the executor |
| `clonefunction` | `(func: function): function` | Creates a new closure from a function's bytecode |
| `getfunctionhash` | `(func: function): string` | Returns the hash of a function |
| `hookfunction` | `(func: function, hook: function): function` | Replaces a function with a hook and returns the original |
| `hookmetamethod` | `(object: any, method: string, hook: function): function` | Hooks a metamethod and returns the original |
| `iscclosure` | `(func: function): boolean` | Returns whether a function is a C closure |
| `isexecutorclosure` | `(func: function): boolean` | Returns whether a function was created by the executor |
| `islclosure` | `(func: function): boolean` | Returns whether a function is a Luau closure |
| `loadstring` | `(source: string, chunkname: string?): function?, string?` | Compiles Luau source into a function |
| `newcclosure` | `(func: function): function` | Wraps a Luau function in a C-closure-style wrapper |
| `restorefunction` | `(func: function): ()` | Restores a hooked function to its original state |

---

# Debug Library

The **Debug** library offers powerful tools for inspecting and modifying Luau functions at a bytecode level.

## Capabilities

- **Inspect** constants with `debug.getconstants` and `debug.getconstant`
- **Modify** constants using `debug.setconstant`
- **Access** upvalues using `debug.getupvalues` and `debug.getupvalue`
- **Replace** upvalues with `debug.setupvalue`
- **Read** or **write** values from a stack frame using `debug.getstack` and `debug.setstack`
- **List** or **retrieve** function prototypes using `debug.getprotos` and `debug.getproto`

## Limitations

- You cannot access C closures with this library (e.g., `print`)

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `debug.getconstant` | `(func: function, index: number): any` | Returns a constant at the given index |
| `debug.getconstants` | `(func: function): table` | Returns all constants from a function |
| `debug.getproto` | `(func: function, index: number, active: boolean?): function` | Returns a proto (inner function) at the given index |
| `debug.getprotos` | `(func: function): table` | Returns all protos from a function |
| `debug.getstack` | `(level: number?, index: number?): any` | Returns values from the stack |
| `debug.getupvalue` | `(func: function, index: number): any, string` | Returns an upvalue at the given index |
| `debug.getupvalues` | `(func: function): table` | Returns all upvalues from a function |
| `debug.setconstant` | `(func: function, index: number, value: any): ()` | Sets a constant at the given index |
| `debug.setstack` | `(level: number, index: number, value: any): ()` | Sets a value in the stack |
| `debug.setupvalue` | `(func: function, index: number, value: any): any, string` | Sets an upvalue at the given index |

---

# Drawing Library

The `Drawing` class represents a renderable 2D object that appears on the user's screen. Drawing objects are **not** instances - they are client-only graphical primitives.

## Constructor

```lua
function Drawing.new(type: string): Drawing
```

The type must be one of: `Line`, `Text`, `Image`, `Circle`, `Square`, `Quad`, or `Triangle`.

## Shared Properties

| Property | Type | Description |
| --- | --- | --- |
| `Visible` | `boolean` | Whether the object is rendered (default: `false`) |
| `ZIndex` | `number` | Render order; higher values appear on top |
| `Transparency` | `number` | `0` = fully opaque, `1` = fully transparent |
| `Color` | `Color3` | The color of the drawing |
| `__OBJECT_EXISTS` | `boolean` | Whether the drawing object exists |

## Methods

| Method | Description |
| --- | --- |
| `Destroy()` | Permanently removes the drawing from view |

## Shape-Specific Types

### Line

| Property | Type | Description |
| --- | --- | --- |
| `From` | `Vector2` | Start position of the line |
| `To` | `Vector2` | End position of the line |
| `Thickness` | `number` | Width of the line |

### Text

| Property | Type | Description |
| --- | --- | --- |
| `Text` | `string` | The text content to render |
| `TextBounds` | `Vector2` | Computed text size (read-only) |
| `Font` | `Drawing.Font` | Font to use |
| `Size` | `number` | Size of the text |
| `Position` | `Vector2` | Top-left corner of the text |
| `Center` | `boolean` | Horizontally center the text |
| `Outline` | `boolean` | Whether to draw an outline |
| `OutlineColor` | `Color3` | Outline color |

### Image

| Property | Type | Description |
| --- | --- | --- |
| `Data` | `string` | Raw image byte string |
| `Size` | `Vector2` | Size of the rendered image |
| `Position` | `Vector2` | Top-left corner of the image |
| `Rounding` | `number` | Amount of corner rounding |

### Circle

| Property | Type | Description |
| --- | --- | --- |
| `NumSides` | `number` | Number of sides used to approximate the circle |
| `Radius` | `number` | Radius of the circle |
| `Position` | `Vector2` | Center point of the circle |
| `Thickness` | `number` | Outline thickness (if not filled) |
| `Filled` | `boolean` | Whether the circle is filled |

### Square

| Property | Type | Description |
| --- | --- | --- |
| `Size` | `Vector2` | Size of the rectangle |
| `Position` | `Vector2` | Top-left corner |
| `Thickness` | `number` | Outline thickness (if not filled) |
| `Filled` | `boolean` | Whether the square is filled |

### Quad

| Property | Type | Description |
| --- | --- | --- |
| `PointA` | `Vector2` | First point |
| `PointB` | `Vector2` | Second point |
| `PointC` | `Vector2` | Third point |
| `PointD` | `Vector2` | Fourth point |
| `Thickness` | `number` | Outline thickness (if not filled) |
| `Filled` | `boolean` | Whether the quad is filled |

### Triangle

| Property | Type | Description |
| --- | --- | --- |
| `PointA` | `Vector2` | First point |
| `PointB` | `Vector2` | Second point |
| `PointC` | `Vector2` | Third point |
| `Thickness` | `number` | Outline thickness (if not filled) |
| `Filled` | `boolean` | Whether the triangle is filled |

## Examples

```lua
-- Creating a circle
local Camera = game.Workspace.CurrentCamera
local Viewport = Camera.ViewportSize
local Position = Vector2.new(Viewport.X / 2, Viewport.Y / 2)

local circle = Drawing.new("Circle")
circle.Radius = 50
circle.Color = Color3.fromRGB(255, 0, 0)
circle.Filled = true
circle.NumSides = 150
circle.Position = Position
circle.Transparency = 0
circle.Visible = true

print(circle.__OBJECT_EXISTS) -- Output: true
circle:Destroy()
print(circle.__OBJECT_EXISTS) -- Output: false
```

---

# Encoding Library

The **Encoding** library provides functions for common binary transformation operations such as Base64 and LZ4.

> **Note:** The old `crypt` library has been fully deprecated. sUNC will warn you if a `crypt` table exists.

## Capabilities

- **Encode and decode** binary or text data into Base64
- **Compress and decompress** data efficiently using the LZ4 algorithm

## What are Base64 and LZ4?

- **Base64** is a binary-to-text encoding scheme that represents binary data using ASCII characters
- **LZ4** is a lossless data compression algorithm known for its high speed and low latency

## Limitations

- These functions are designed for data transformation only and are **not** encryption methods

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `base64decode` | `(data: string): string` | Decodes Base64 into bytes |
| `base64encode` | `(data: string): string` | Encodes bytes into Base64 |
| `lz4compress` | `(data: string): string` | Compresses data using LZ4 |
| `lz4decompress` | `(data: string, uncompressedSize: number): string` | Decompresses LZ4 data |

---

# Environment Library

The **Environment** library allows access and inspection to executor and Roblox's environment.

## Capabilities

- **Access and modify executor globals** using `getgenv`
- **Modify the game global environment** using `getrenv`
- **View collectable and living values** using `getgc`
- **Filter** garbage-collected values with fine-tuned criteria using `filtergc`

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `getgc` | `(include_tables: boolean?): table` | Returns all garbage-collected values |
| `getgenv` | `(): table` | Returns the executor's global environment |
| `getreg` | `(): table` | Returns the Lua registry |
| `getrenv` | `(): table` | Returns the Roblox environment |
| `filtergc` | `(filter_type: string, options: table): table` | Filters garbage-collected values |

---

# Filesystem Library

The **Filesystem** library provides access to an executor's virtual file system.

## Capabilities

- **Write** data to a file using `writefile`
- **Read** file contents with `readfile`
- **Append** content using `appendfile`
- **List** files and folders via `listfiles`
- **Delete** files with `delfile` and folders using `delfolder`
- **Check** if a path is a file or folder using `isfile` or `isfolder`
- **Create** folders with `makefolder`
- **Dynamically load** code from a file via `loadfile`
- **Use** local assets in Roblox via `getcustomasset`

## Limitations

- You cannot access files outside the executor's workspace/sandboxed storage
- You cannot use file IO to interact with the real disk
- You cannot escape the executor's `workspace` folder

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `appendfile` | `(path: string, data: string): ()` | Appends data to a file |
| `delfile` | `(path: string): ()` | Deletes a file |
| `delfolder` | `(path: string): ()` | Deletes a folder |
| `getcustomasset` | `(path: string): string` | Returns a custom asset ID for local files |
| `isfile` | `(path: string): boolean` | Checks if a path is a file |
| `isfolder` | `(path: string): boolean` | Checks if a path is a folder |
| `listfiles` | `(path: string): table` | Lists files and folders in a directory |
| `loadfile` | `(path: string, chunkname: string?): function?, string?` | Loads and compiles a file |
| `makefolder` | `(path: string): ()` | Creates a folder |
| `readfile` | `(path: string): string` | Reads file contents |
| `writefile` | `(path: string, data: string): ()` | Writes data to a file |

---

# Instances Library

The **Instances** library provides direct access to and manipulation of Instance objects in the game world.

## Capabilities

- **List all objects** tracked by the client using `getinstances`
- **List nil-parented objects** using `getnilinstances`
- **Compare two objects** using `compareinstances`
- **Safely clone instance references** using `cloneref`
- **Access hidden UI containers** using `gethui`
- **Inspect function-based properties** with `getcallbackvalue`
- **Simulate interactions** using `fireclickdetector`, `fireproximityprompt`, and `firetouchinterest`

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `cloneref` | `(object: Instance): Instance` | Returns a cloned reference to an instance |
| `compareinstances` | `(a: Instance, b: Instance): boolean` | Compares two instances |
| `fireclickdetector` | `(detector: ClickDetector, distance: number?): ()` | Fires a ClickDetector |
| `fireproximityprompt` | `(prompt: ProximityPrompt): ()` | Fires a ProximityPrompt |
| `firetouchinterest` | `(part: BasePart, root: BasePart, toggle: number): ()` | Fires a TouchInterest |
| `getcallbackvalue` | `(object: Instance, callback: string): any` | Gets a callback value |
| `gethui` | `(): Instance` | Returns the hidden UI container |
| `getinstances` | `(): table` | Returns all instances |
| `getnilinstances` | `(): table` | Returns nil-parented instances |

---

# Metatable Library

The **Metatable** library enables advanced interaction with metatables in Luau.

## What is a metatable?

A metatable in Luau is a hidden table that can change the behavior of another table. Metatables allow you to define custom behaviors like operator overloading, default values, or interception of table reads and writes.

## Capabilities

- **Retrieve** hidden metatables using `getrawmetatable`
- **Replace** a table or object's metatable with `setrawmetatable`
- **Control** whether a table is locked using `setreadonly`
- **Check** whether a table is locked with `isreadonly`

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `getnamecallmethod` | `(): string` | Returns the namecall method being used |
| `getrawmetatable` | `(object: any): table` | Returns the raw metatable of an object |
| `isreadonly` | `(table: table): boolean` | Checks if a table is read-only |
| `setrawmetatable` | `(object: any, metatable: table): ()` | Sets the raw metatable of an object |
| `setreadonly` | `(table: table, readonly: boolean): ()` | Sets whether a table is read-only |

---

# Miscellaneous Library

The **Miscellaneous** library contains functions that do not belong to a specific category.

## Capabilities

- **Identify** the current executor and version using `identifyexecutor`
- **Send HTTP requests** and receive full responses with `request`

## What does `request` support?

- Custom HTTP methods (`GET`, `POST`, `PATCH`, `PUT`, `DELETE`)
- Request and response headers
- Request body and cookies
- Built-in executor identification headers

## Limitations

- `request` is **synchronous** and yields until the server responds
- Some executors may restrict access to external domains
- `identifyexecutor` should never be relied on as a secure identifier

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `identifyexecutor` | `(): string, string` | Returns executor name and version |
| `request` | `(options: table): table` | Sends an HTTP request |

---

# Reflection Library

The **Reflection** library allows access to and manipulation of hidden or non-scriptable properties of Instances and internal execution context.

## Capabilities

- **Read** hidden instance properties using `gethiddenproperty`
- **Write** to hidden instance properties with `sethiddenproperty`
- **Toggle and check** scriptability of properties using `setscriptable` and `isscriptable`
- **Elevate** thread permissions with `setthreadidentity`
- **Query** the current thread's permission level with `getthreadidentity`

## Limitations

- `setscriptable` is detectable by games that check for unusual access
- Using these methods irresponsibly may break core game logic

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `gethiddenproperty` | `(object: Instance, property: string): any, boolean` | Gets a hidden property |
| `getthreadidentity` | `(): number` | Returns the current thread identity |
| `isscriptable` | `(object: Instance, property: string): boolean` | Checks if a property is scriptable |
| `sethiddenproperty` | `(object: Instance, property: string, value: any): boolean` | Sets a hidden property |
| `setscriptable` | `(object: Instance, property: string, scriptable: boolean): boolean` | Sets property scriptability |
| `setthreadidentity` | `(identity: number): ()` | Sets the thread identity |

---

# Scripts Library

The **Scripts** library provides advanced functions for inspecting and interacting with script objects.

## Capabilities

- **Read compiled bytecode** from scripts using `getscriptbytecode`
- **Get the bytecode hash** of a script using `getscripthash`
- **Create a new closure based off of the script's bytecode** using `getscriptclosure`
- **Access script environments** with `getsenv`
- **Enumerate script instances** using `getscripts` and `getrunningscripts`
- **List loaded modules** via `getloadedmodules`
- **Determine the current script** executing via `getcallingscript`
- **Resolve the script associated with a thread** using `getscriptfromthread`

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `getcallingscript` | `(): BaseScript` | Returns the currently executing script |
| `getloadedmodules` | `(): table` | Returns all loaded modules |
| `getrunningscripts` | `(): table` | Returns all running scripts |
| `getscriptbytecode` | `(script: BaseScript): string` | Returns the bytecode of a script |
| `getscriptclosure` | `(script: BaseScript): function` | Returns a closure from script bytecode |
| `getscriptfromthread` | `(thread: thread): BaseScript` | Returns the script associated with a thread |
| `getscripthash` | `(script: BaseScript): string` | Returns the hash of a script's bytecode |
| `getscripts` | `(): table` | Returns all script instances |
| `getsenv` | `(script: BaseScript): table` | Returns the environment of a script |

---

# Signals Library

The **Signals** library provides functions for inspecting and manipulating `RBXScriptSignal` and `RBXScriptConnection` objects.

## What is a signal?

A signal in Roblox is an event emitter - an object that can have functions connected to it, which are triggered when something happens. Signals are backed by objects called **connections**.

## Capabilities

- **Get all connections** attached to a signal using `getconnections`
- **Fire** a signal's Luau connections using `firesignal`
- **Replicate signals to the server** with `replicatesignal`, if supported

## The Connection Object

| Property | Type | Description |
| --- | --- | --- |
| `Connected` | `boolean` | Whether the connection is connected |

| Method | Description |
| --- | --- |
| `Disconnect()` | Disconnects the connection |
| `Fire(...)` | Fires the connection's callback |

## Functions

| Function | Signature | Description |
| --- | --- | --- |
| `firesignal` | `(signal: RBXScriptSignal, ...: any): ()` | Fires all Luau connections of a signal |
| `getconnections` | `(signal: RBXScriptSignal): table` | Returns all connections to a signal |
| `replicatesignal` | `(signal: RBXScriptSignal): ()` | Replicates a signal to the server |

---

# WebSocket Library

The `WebSocket` class provides a lightweight interface for establishing and working with WebSocket connections.

## Constructor

```lua
function WebSocket.connect(url: string): WebSocket
```

The URL must be a valid WebSocket server URL, typically starting with `ws://` (unsecure) or `wss://` (secure). The sUNC test explicitly checks for `wss` (secure) support.

## Events

| Event | Description |
| --- | --- |
| `OnMessage(message: string)` | Triggered when a message is received |
| `OnClose()` | Triggered when the connection closes |

## Methods

| Method | Description |
| --- | --- |
| `Send(message: string)` | Sends a message over the connection |
| `Close()` | Closes the WebSocket connection |

## Examples

```lua
-- Using OnMessage and Send
local ws = WebSocket.connect("wss://ws.postman-echo.com/raw")
ws.OnMessage:Connect(function(message)
    print(message)
end)
ws:Send("Hello") -- Output: Hello

-- Using OnClose and Close
local ws = WebSocket.connect("wss://ws.postman-echo.com/raw")
ws.OnClose:Connect(function()
    print("Closed")
end)
ws:Close() -- Output: Closed
```

---

# Quick Reference

## All Functions by Library

| Library | Functions |
| --- | --- |
| **Closures** | `checkcaller`, `clonefunction`, `getfunctionhash`, `hookfunction`, `hookmetamethod`, `iscclosure`, `isexecutorclosure`, `islclosure`, `loadstring`, `newcclosure`, `restorefunction` |
| **Debug** | `debug.getconstant`, `debug.getconstants`, `debug.getproto`, `debug.getprotos`, `debug.getstack`, `debug.getupvalue`, `debug.getupvalues`, `debug.setconstant`, `debug.setstack`, `debug.setupvalue` |
| **Drawing** | `Drawing.new`, `cleardrawcache`, `getrenderproperty`, `isrenderobj`, `setrenderproperty` |
| **Encoding** | `base64decode`, `base64encode`, `lz4compress`, `lz4decompress` |
| **Environment** | `getgc`, `getgenv`, `getreg`, `getrenv`, `filtergc` |
| **Filesystem** | `appendfile`, `delfile`, `delfolder`, `getcustomasset`, `isfile`, `isfolder`, `listfiles`, `loadfile`, `makefolder`, `readfile`, `writefile` |
| **Instances** | `cloneref`, `compareinstances`, `fireclickdetector`, `fireproximityprompt`, `firetouchinterest`, `getcallbackvalue`, `gethui`, `getinstances`, `getnilinstances` |
| **Metatable** | `getnamecallmethod`, `getrawmetatable`, `isreadonly`, `setrawmetatable`, `setreadonly` |
| **Miscellaneous** | `identifyexecutor`, `request` |
| **Reflection** | `gethiddenproperty`, `getthreadidentity`, `isscriptable`, `sethiddenproperty`, `setscriptable`, `setthreadidentity` |
| **Scripts** | `getcallingscript`, `getloadedmodules`, `getrunningscripts`, `getscriptbytecode`, `getscriptclosure`, `getscriptfromthread`, `getscripthash`, `getscripts`, `getsenv` |
| **Signals** | `firesignal`, `getconnections`, `replicatesignal` |
| **WebSocket** | `WebSocket.connect` |
