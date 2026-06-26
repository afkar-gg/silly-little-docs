# Potassium Roblox Executor API Documentation

Source: https://docs.potassium.pro/

This document is a consolidated Markdown reference for the Potassium executor scripting API. It is based on the public Potassium documentation and its indexed API pages. Use this reference only in environments where you have permission to run client-side Luau code, such as your own Roblox experiences, private test places, or controlled research environments. It is not a guide for bypassing Roblox protections, cheating in public experiences, or interfering with other users.

## Overview

Potassium documents a Luau executor environment that follows the UNC-style executor API shape with Potassium-specific additions. The API is organized into libraries for actor execution, closures, console output, cryptography, debug inspection, drawing, file access, input simulation, instance helpers, metatable access, miscellaneous utilities, scripts, and WebSocket communication.

The documentation assumes familiarity with Luau and Roblox client concepts such as `Instance`, `LocalScript`, `ModuleScript`, `RBXScriptSignal`, `Actor`, `BindableEvent`, `Vector2`, and `Color3`.

## API Conventions

- Signatures use angle-bracket type notation, for example `<string> readfile(<string> path)`.
- Optional parameters are marked with `?`, for example `<number?> distance`.
- Some library functions are also exposed globally. The debug page notes that debug functions can be used through the global table, for example `getconstant(...)`.
- Functions that inspect or modify closures, scripts, signals, instances, or hidden properties can be fragile across Roblox updates and should be treated as internal tooling, not stable platform APIs.

## Actor Library

Actor functions are used for interacting with Roblox parallel Luau actors and actor-owned threads.

| Function | Signature | Description |
| --- | --- | --- |
| `create_comm_channel` | `<number, BindableEvent> create_comm_channel()` | Creates a communication channel and returns its numeric ID plus the associated `BindableEvent`. |
| `get_comm_channel` | `<BindableEvent> get_comm_channel(<number> id)` | Returns an existing communication channel for the given ID, if it exists. |
| `run_on_actor` | `<nil> run_on_actor(<Actor> actor, <string> script, ...)` | Runs source code on an actor thread. |
| `getactors` | `<table<Actor>> getactors()` | Returns all actors that exist in the current game client. |
| `is_parallel` | `<boolean> is_parallel()` | Reports whether the current thread is running in an actor context. |
| `run_on_thread` | `<nil> run_on_thread(<thread> thread, <string> script, ...)` | Runs source code on a specified actor thread. |
| `getactorthreads` | `<table<thread>> getactorthreads()` | Returns all threads that belong to actors. |

## Cache Library

Cache functions operate on executor-side `Instance` references.

| Function | Signature | Description |
| --- | --- | --- |
| `cache.invalidate` | `<nil> cache.invalidate(<Instance> object)` | Deletes an object from the instance cache, invalidating that reference. |
| `cache.iscached` | `<boolean> cache.iscached(<Instance> object)` | Checks whether an object exists in the instance cache. |
| `cache.replace` | `<nil> cache.replace(<Instance> object, <Instance> newobject)` | Replaces one cached instance reference with another. |
| `cloneref` | `<Instance> cloneref(<Instance> object)` | Returns a copied reference to an instance. |
| `compareinstances` | `<boolean> compareinstances(<Instance> a, <Instance> b)` | Returns whether two references point to the same underlying instance. |

## Closure Library

Closure functions inspect, clone, wrap, or replace Luau and C closures.

| Function | Signature | Description |
| --- | --- | --- |
| `checkcaller` | `<boolean> checkcaller()` | Returns whether the current call originated from the executor. |
| `clonefunction` | `<function> clonefunction(<function> func)` | Creates a new closure from a function's bytecode. |
| `getcallingscript` | `<BaseScript> getcallingscript()` | Returns the script responsible for the current function call. |
| `hookfunction` | `<function> hookfunction(<function> func, <function> hook)` | Replaces a function with a hook and returns a callable reference to the original function. |
| `iscclosure` | `<boolean> iscclosure(<function> func)` | Returns whether a function is a C closure. |
| `islclosure` | `<boolean> islclosure(<function> func)` | Returns whether a function is a Luau closure. |
| `isexecutorclosure` | `<boolean> isexecutorclosure(<function> func)` | Returns whether a function was created by the executor. |
| `loadstring` | `<function?, string?> loadstring(<string> source, <string?> chunkname)` | Compiles Luau source into a function, returning an error string on failure. |
| `newcclosure` | `<function> newcclosure(<function> func)` | Wraps a Luau function in a C-closure-style wrapper. |

## Console Library

Console functions write to, clear, or manage the executor console. Some window-management functions are documented as disabled.

| Function | Signature | Description |
| --- | --- | --- |
| `rconsoleclear` | `<nil> rconsoleclear()` | Clears console output. |
| `rconsolecreate` | `<nil> rconsolecreate()` | Disabled in the documented API. |
| `rconsoledestroy` | `<nil> rconsoledestroy()` | Clears console output. |
| `rconsoleinput` | `<string> rconsoleinput()` | Disabled in the documented API. |
| `rconsoleprint` | `<nil> rconsoleprint(<string> text)` | Prints text without automatically appending a newline. |
| `rconsolewarn` | `<nil> rconsolewarn(<string> text)` | Prints warning text. |
| `rconsoleerror` | `<nil> rconsoleerror(<string> text)` | Prints error text. |
| `rconsolesettitle` | `<nil> rconsolesettitle(<string> title)` | Disabled in the documented API. |

## Crypt Library

Crypt functions provide Base64 encoding, AES encryption helpers, random-byte generation, key generation, and hashing.

| Function | Signature | Description |
| --- | --- | --- |
| `crypt.base64encode` | `<string> crypt.base64encode(<string> data)` | Encodes bytes into Base64. |
| `crypt.base64decode` | `<string> crypt.base64decode(<string> data)` | Decodes Base64 into bytes. |
| `crypt.encrypt` | `<string, string> crypt.encrypt(<string> data, <string> key, <string?> iv, <string?> mode)` | Encrypts raw data using AES and returns Base64 ciphertext plus the IV. Supported modes include `CBC`, `ECB`, `CTR`, `CFB`, `OFB`, and `GCM`; default is `CBC`. |
| `crypt.decrypt` | `<string> crypt.decrypt(<string> data, <string> key, <string> iv, <string> mode)` | Decrypts Base64-encoded encrypted data and returns the raw string. |
| `crypt.generatebytes` | `<string> crypt.generatebytes(<number> size)` | Generates random bytes and returns them Base64-encoded. |
| `crypt.generatekey` | `<string> crypt.generatekey()` | Generates a Base64-encoded 256-bit key. |
| `crypt.hash` | `<string> crypt.hash(<string> data, <string> algo)` | Hashes data with an algorithm such as `sha1`, `sha384`, `sha512`, `md5`, `sha256`, `sha3-224`, `sha3-256`, or `sha3-512`. |

## Debug Library

Debug functions inspect or modify Luau function internals, stack frames, registry data, constants, protos, and upvalues. These functions are powerful and should be limited to diagnostics and controlled testing.

| Function | Signature | Description |
| --- | --- | --- |
| `debug.isvalidlevel` | `<boolean> debug.isvalidlevel(<number> level)` | Returns whether a stack level is valid. |
| `debug.getregistry` | `<table<function \| thread>> debug.getregistry()` | Returns the Lua registry. |
| `debug.getconstant` | `<any> debug.getconstant(<function \| number> func, <number> index)` | Returns a constant from a function or stack level. |
| `debug.getconstants` | `<table<any>> debug.getconstants(<function \| number> func)` | Returns the constant table for a function or stack level. |
| `debug.getinfo` | `<DebugInfo> debug.getinfo(<function \| number> func)` | Returns debugger metadata for a function or stack level. |
| `debug.getproto` | `<function \| table<function>> debug.getproto(<function \| number> func, <number> index, <boolean?> active)` | Returns a nested proto or active proto functions. |
| `debug.getprotos` | `<table<function>> debug.getprotos(<function \| number> func)` | Returns all protos for a function or stack level. |
| `debug.getstack` | `<any \| table<any>> debug.getstack(<function \| number> func, <number?> index)` | Returns one stack value or an entire stack frame. |
| `debug.getupvalue` | `<any> debug.getupvalue(<function \| number> func, <number> index)` | Returns an upvalue from a function or stack level. |
| `debug.getupvalues` | `<table<any>> debug.getupvalues(<function \| number> func)` | Returns all upvalues for a function or stack level. |
| `debug.setconstant` | `<nil> debug.setconstant(<function \| number> func, <number> index, <any> value)` | Sets a function or stack-level constant. |
| `debug.setstack` | `<nil> debug.setstack(<function \| number> func, <number> index, <any> value)` | Sets a stack-frame register value. |
| `debug.setupvalue` | `<nil> debug.setupvalue(<function \| number> func, <number> index, <any> value)` | Sets an upvalue. |

### `DebugInfo` Fields

| Field | Type | Meaning |
| --- | --- | --- |
| `source` | `string` | Chunk that created the function. |
| `short_src` | `string` | Printable source name for errors. |
| `func` | `function` | The function itself. |
| `what` | `string` | `Lua` for Luau functions or `C` for C functions. |
| `currentline` | `number` | Current executing line. |
| `name` | `string` | Function name. |
| `nups` | `number` | Number of upvalues. |
| `numparams` | `number` | Number of parameters. |
| `is_vararg` | `number` | Whether the function accepts varargs. |

## Drawing Library

The Drawing library creates and manages client-side drawing objects.

| Function | Signature | Description |
| --- | --- | --- |
| `Drawing.new` | `<DrawingObject> Drawing.new(<string> type)` | Creates a drawing object of the requested type. |
| `Drawing.Fonts` | table | Maps font names to numeric IDs: `UI = 0`, `System = 1`, `Plex = 2`, `Monospace = 3`. |
| `cleardrawcache` | `<nil> cleardrawcache()` | Destroys all cached drawing objects. |
| `getrenderproperty` | `<any> getrenderproperty(<DrawingObject> drawing, <string> property)` | Gets a drawing property value. |
| `isrenderobj` | `<boolean> isrenderobj(<any> object)` | Returns whether a value is a valid drawing object. |
| `setrenderproperty` | `<nil> setrenderproperty(<DrawingObject> drawing, <string> property, <any> value)` | Sets a drawing property value. |

### Drawing Object Properties

| Object | Properties |
| --- | --- |
| Base object | `Visible`, `ZIndex`, `Transparency`, `Color`, `Destroy` |
| `Line` | `From`, `To`, `Thickness` |
| `Text` | `Text`, `TextBounds`, `Font`, `Size`, `Position`, `Center`, `Outline`, `OutlineColor` |
| `Image` | `Data`, `Size`, `Position`, `Rounding` |
| `Circle` | `NumSides`, `Radius`, `Position`, `Thickness`, `Filled` |
| `Square` | `Size`, `Position`, `Thickness`, `Filled` |
| `Quad` | `PointA`, `PointB`, `PointC`, `PointD`, `Thickness`, `Filled` |
| `Triangle` | `PointA`, `PointB`, `PointC`, `Thickness`, `Filled` |

## File System Library

File system functions read, write, list, create, delete, load, and execute files in the executor-accessible file area.

| Function | Signature | Description |
| --- | --- | --- |
| `readfile` | `<string> readfile(<string> path)` | Returns the contents of a file. |
| `listfiles` | `<table<string>> listfiles(<string> path)` | Returns full paths for files and folders in a directory. |
| `writefile` | `<nil> writefile(<string> path, <string> data)` | Writes data to a file path if it is not a folder. |
| `makefolder` | `<nil> makefolder(<string> path)` | Creates a folder if it does not exist. |
| `appendfile` | `<nil> appendfile(<string> path, <string> data)` | Appends data to a file, creating it if needed. |
| `isfile` | `<boolean> isfile(<string> path)` | Returns whether a path points to a file. |
| `isfolder` | `<boolean> isfolder(<string> path)` | Returns whether a path points to a folder. |
| `delfile` | `<nil> delfile(<string> path)` | Deletes a file. |
| `delfolder` | `<nil> delfolder(<string> path)` | Deletes a folder. |
| `loadfile` | `<function?, string?> loadfile(<string> path, <string?> chunkname)` | Compiles source from a file into a function or returns a compile error. |
| `dofile` | `<nil> dofile(<string> path)` | Loads and executes a file on a new thread. |

## Input Library

Input functions simulate keyboard and mouse state for the game window. The documented API states that the game window must be active for input functions to work.

| Function | Signature | Description |
| --- | --- | --- |
| `isrbxactive` | `<boolean> isrbxactive()` | Returns whether the game window is focused. |
| `mouse1click` | `<nil> mouse1click()` | Dispatches a left mouse click. |
| `mouse1press` | `<nil> mouse1press()` | Dispatches a left mouse button press. |
| `mouse1release` | `<nil> mouse1release()` | Dispatches a left mouse button release. |
| `mouse2click` | `<nil> mouse2click()` | Dispatches a right mouse click. |
| `mouse2press` | `<nil> mouse2press()` | Dispatches a right mouse button press. |
| `mouse2release` | `<nil> mouse2release()` | Dispatches a right mouse button release. |
| `mousemoveabs` | `<nil> mousemoveabs(<number> x, <number> y)` | Moves the mouse cursor to an absolute position. |
| `mousemoverel` | `<nil> mousemoverel(<number> x, <number> y)` | Moves the mouse cursor by a relative offset. |
| `mousescroll` | `<nil> mousescroll(<number> pixels)` | Dispatches a mouse-wheel scroll. |

## Instance Library

Instance functions expose helpers for Roblox instances, signals, properties, and client-side interaction state. Use only in experiences and test environments where you are authorized to inspect or manipulate client state.

| Function | Signature | Description |
| --- | --- | --- |
| `fireclickdetector` | `<nil> fireclickdetector(<ClickDetector> object, <number?> distance)` | Dispatches click or hover behavior for a `ClickDetector`. |
| `firetouchinterest` | `<nil> firetouchinterest(<Instance> instance, <Instance> touchingPart, <bool> isTouching)` | Simulates a touch state between two parts. |
| `isnetworkowner` | `<bool> isnetworkowner(<Instance> part)` | Returns whether the local player owns network simulation for a part. |
| `getcallbackvalue` | `<function?> getcallbackvalue(<Instance> object, <string> property)` | Returns a callback property function that cannot normally be indexed. |
| `fireproximityprompt` | `<nil> fireproximityprompt(<ProximityPrompt> prompt)` | Fires a proximity prompt trigger. |
| `getconnections` | `<table<ConnectionObject>> getconnections(<RBXScriptSignal> signal)` | Lists connection objects for a signal. |
| `getconnection` | `<table<ConnectionObject>> getconnection(<RBXScriptSignal> signal, <number> index)` | Returns a connection object by index. |
| `firesignal` | `<nil> firesignal(<RBXScriptSignal> signal, ...)` | Fires Lua connections for a signal. |
| `getcustomasset` | `<string> getcustomasset(<string> path)` | Returns an `rbxasset://` content ID for a local asset. |
| `gethiddenproperty` | `<any, boolean> gethiddenproperty(<Instance> object, <string> property)` | Returns a property value and whether it was hidden. |
| `setsimulationradius` | `<nil> setsimulationradius(<number> simulationRadius, <number?> maxSimulationRadius)` | Sets the local player's simulation radius values. |
| `gethui` | `<Folder> gethui()` | Returns a hidden GUI container. |
| `getinstances` | `<table<Instance>> getinstances()` | Returns every instance referenced on the client. |
| `getnilinstances` | `<table<Instance>> getnilinstances()` | Returns referenced instances not descended from a service provider. |
| `isscriptable` | `<boolean> isscriptable(<Instance> object, <string> property)` | Returns whether a property is scriptable. |
| `sethiddenproperty` | `<boolean> sethiddenproperty(<Instance> object, <string> property, <any> value)` | Sets a hidden property and returns whether it was hidden. |
| `setrbxclipboard` | `<boolean> setrbxclipboard(<string> data)` | Sets the Studio client clipboard to `rbxm` or `rbxmx` model data. |
| `setscriptable` | `<boolean> setscriptable(<Instance> object, <string> property, <boolean> value)` | Changes whether a property is scriptable and returns the prior scriptable state. |
| `getrendersteppedlist` | `<table> getrendersteppedlist()` | Returns callbacks bound with `RunService.BindToRenderStep`. |
| `replicatesignal` | `<nil> replicatesignal(<RBXScriptSignal> signal, ...)` | Replicates a signal to the server when supported by the signal. |

### Connection Object Shape

| Field or Method | Type | Description |
| --- | --- | --- |
| `Enabled` | `boolean` | Whether the connection can receive events. |
| `ForeignState` | `boolean` | Whether the function was connected by a foreign Luau state. |
| `LuaConnection` | `boolean` | Whether the connection was created by Luau code. |
| `Function` | `function?` | Bound callback, if available. |
| `Thread` | `thread?` | Thread that created the connection, if available. |
| `Fire(...: any)` | method | Invokes the connection immediately. |
| `Defer(...: any)` | method | Defers invocation with arguments. |
| `Disconnect()` | method | Disconnects the connection. |
| `Disable()` | method | Prevents the connection from firing. |
| `Enable()` | method | Re-enables a disabled connection. |

## Metatable Library

Metatable functions expose normally protected metatable operations.

| Function | Signature | Description |
| --- | --- | --- |
| `getrawmetatable` | `<table> getrawmetatable(<table> object)` | Returns the metatable even when `__metatable` would normally lock it. |
| `hookmetamethod` | `<function> hookmetamethod(<table> object, <string> method, <function> hook)` | Hooks a metamethod and returns a reference to the original function. |
| `getnamecallmethod` | `<string> getnamecallmethod()` | Returns the method name that invoked `__namecall`. |
| `setnamecallmethod` | `<nil> setnamecallmethod(<string> method)` | Changes the current `__namecall` method name. |
| `isreadonly` | `<boolean> isreadonly(<table> object)` | Returns whether a table is read-only. |
| `setrawmetatable` | `<nil> setrawmetatable(<table> object, <table> metatable)` | Sets a metatable even if it would normally be locked. |
| `setreadonly` | `<nil> setreadonly(<table> object, <boolean> readonly)` | Sets a table's read-only state. |
| `makewriteable` | `<nil> makewriteable(<table> object)` | Unfreezes a table. |
| `makereadonly` | `<nil> makereadonly(<table> object)` | Freezes a table while bypassing normal `__metatable` checks. |

## Miscellaneous Library

Miscellaneous functions include executor identity, compression, message boxes, HTTP requests, fast flags, clipboard, FPS cap, and internal-parent helpers.

| Function | Signature | Description |
| --- | --- | --- |
| `identifyexecutor` | `<string, string> identifyexecutor()` | Returns the executor name and version. |
| `lz4compress` | `<string> lz4compress(<string> data)` | Compresses data with LZ4. |
| `lz4decompress` | `<string> lz4decompress(<string> data, <number> size)` | Decompresses LZ4 data using the expected decompressed size. |
| `messagebox` | `<number> messagebox(<string> text, <string> caption, <number> flags)` | Displays a message box and returns the user input code. |
| `queue_on_teleport` | `<nil> queue_on_teleport(<string> code)` | Queues source code to execute after teleporting to another place. |
| `request` | `<HttpResponse> request(<HttpRequest> options)` | Sends an HTTP request and returns a response. |
| `getinternalparent` | `<Instance> getinternalparent(<Instance> instance)` | Returns an instance's internal parent field. |
| `getfflag` | `<any> getfflag(<string> fflag)` | Returns the value of a fast flag. |
| `setfflag` | `<nil> setfflag(<string> fflag, <any> value)` | Sets a fast flag value. |
| `setinternalparent` | `<nil> setinternalparent(<Instance> instance, <Instance> newparent)` | Sets an instance's internal parent field. |
| `setclipboard` | `<nil> setclipboard(<string> text)` | Copies text to the clipboard. |
| `setfpscap` | `<nil> setfpscap(<number> fps)` | Sets the FPS cap; `0` disables the cap. |

### HTTP Request Shape

| Field | Type | Description |
| --- | --- | --- |
| `Url` | `string` | Request URL. |
| `Method` | `string` | HTTP method. |
| `Body` | `string?` | Optional request body. |
| `Headers` | `table?` | Optional request headers. |
| `Cookies` | `table?` | Optional cookies. |

## Scripts Library

Script functions inspect script environments, bytecode, closures, script lists, and thread identity.

| Function | Signature | Description |
| --- | --- | --- |
| `getgc` | `<table<function \| userdata \| table>> getgc(<boolean?> includetables)` | Returns objects in the Luau garbage collector; tables may be excluded. |
| `getgenv` | `<table<[string]: any>> getgenv()` | Returns the executor's custom global environment. |
| `gettenv` | `<table<[string]: any>> gettenv(<thread> thread)` | Returns the environment of a thread. |
| `getreg` | `<table<function \| thread>> getreg()` | Returns the Lua registry. |
| `getloadedmodules` | `<table<ModuleScript>> getloadedmodules()` | Returns loaded module scripts. |
| `getrenv` | `<table<[string]: any>> getrenv()` | Returns the game client's global environment. |
| `getrunningscripts` | `<table<LocalScript \| ModuleScript>> getrunningscripts()` | Returns currently running scripts. |
| `getscriptbytecode` | `<string> getscriptbytecode(<LocalScript \| ModuleScript> script)` | Returns raw Luau bytecode for a script. |
| `getscriptclosure` | `<function> getscriptclosure(<LocalScript \| ModuleScript> script)` | Creates a closure from a script's bytecode. |
| `getscripthash` | `<string> getscripthash(<LocalScript \| ModuleScript> script)` | Returns a SHA384 hash of script bytecode. |
| `getscripts` | `<table<LocalScript \| ModuleScript>> getscripts()` | Returns every script in the game. |
| `getsenv` | `<table<[string]: any>> getsenv(<LocalScript \| ModuleScript> script)` | Returns a script's global environment. |
| `getthreadidentity` | `<number> getthreadidentity()` | Returns the current thread identity. |
| `setthreadidentity` | `<nil> setthreadidentity(<number> identity)` | Sets the current thread identity. |

## WebSocket Library

WebSocket functions create client WebSocket connections for local tools, dashboards, or controlled integrations.

| Function | Signature | Description |
| --- | --- | --- |
| `WebSocket.connect` | `<WebSocketConnection> WebSocket.connect(<string> url)` | Opens a WebSocket connection to a URL. |

### WebSocketConnection

| Member | Type | Description |
| --- | --- | --- |
| `Send` | method | Sends a message over the connection. |
| `Close` | method | Closes the connection. |
| `OnMessage` | event | Fires when a message is received. |
| `OnClose` | event | Fires when the connection closes. |

## Practical Notes

- Keep executor code isolated from production Roblox experiences unless you own the experience and are testing specific client behavior.
- Treat functions that change debug constants, stack values, hidden properties, scriptability, fast flags, simulation radius, or metatables as high-risk diagnostics.
- Avoid persisting sensitive data through `writefile`, `appendfile`, HTTP `request`, or WebSocket calls unless the storage and network endpoint are under your control.
- Prefer small, focused test scripts over broad environment mutation.
- Document which Potassium version you used when validating scripts, because executor APIs can change independently of Roblox updates.

## Sources

- Potassium official documentation: https://docs.potassium.pro/
- Potassium introduction page: https://docs.potassium.pro/api-reference/introduction
- Potassium indexed environment pages: `actor`, `cache`, `closures`, `console`, `drawing`, `instance`, and `scripts` under `https://docs.potassium.pro/environment/...`
- Potassium GitBook mirror pages used for plain-text API extraction: `crypt`, `debug`, `file-system`, `input`, `metatable`, `miscellaneous`, and `web-sockets` under `https://potassium.gitbook.io/api/environment/...`
