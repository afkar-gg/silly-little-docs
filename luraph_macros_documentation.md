# Luraph Macros Documentation

Source: https://lura.ph/dashboard/documents/macros

This document is a consolidated Markdown reference for the Luraph obfuscator macro system. It is based on the official Luraph documentation and community resources. Luraph macros allow you to selectively apply additional features to your Lua scripts, adjusting the balance between performance and security.

## Overview

Luraph's macro system provides various functions that can be used to:

- Optimize performance for critical code sections
- Disable obfuscation for specific functions
- Encrypt strings, numbers, and functions
- Control code execution based on obfuscation state
- Securely crash the VM when needed

Macros are declared as globals and are replaced during obfuscation. They will error if used incorrectly or if the `LPH` prefix is used for non-macro identifiers.

## Macro FAQs

### What is Luraph's macro system used for?

Luraph's macro system allows various additional features to be selectively applied to your script, allowing you to take advantage of additional features that adjust the overall balance between performance and security.

### My script errors when I use macros without obfuscation. What do I do?

Luraph macros can be defined in your unobfuscated script as passthrough shims that contain no real logic. These definitions will be removed during obfuscation, and instead replaced with the real macro functionality.

These definitions **must be declared as globals**, and will not be properly removed if declared as local variables. Additionally, all macro overrides must be surrounded by an `if not LPH_OBFUCATED then` block.

Most macros can be shimmed as functions that return their arguments (`function(...) return ... end`); however, it is recommended to use Luraph's [Macro SDK](https://github.com/Luraph/macrosdk) that contains the latest macro definitions and performs validation of macro arguments.

### Luraph says my variable/global names are reserved. What do I do?

All identifiers starting with `LPH` are reserved for internal/macro usage only and will error if used incorrectly.

**Example 1**: `LPH_DO_SOMETHING()` will error during obfuscation because `LPH_DO_SOMETHING` is not a real macro.

**Example 2**: `local LPH_NAME = ...` will error during obfuscation because the `LPH` prefix is reserved.

### Which macros are available for my subscription?

Luraph macros are not currently restricted to any subscription tiers.

## Macro API

### LPH_ENCFUNC

> **WARNING!** This macro will produce undefined results if an incorrect decryption key is supplied at runtime. Use this macro only when untampered executions of your script will yield a correct decryption key, or wrap inside a `pcall` or another error handler to catch any failures.

```lua
function LPH_ENCFUNC(const function toEncrypt, const string encKey, string decKey)
```

**Description:** This macro will cryptographically encrypt the passed function with the provided encryption key and decrypt and load it at runtime with the passed decryption key. The encryption key must be a 64-length hex-encoded string. For security purposes, obfuscation will fail if the encryption key is encoded anywhere else in the script's strings/constants.

**Aliases:** `LPH_FUNCENC`

**Correct Usage:**

```lua
local function getDecryptionKey()
	--[[
	WARNING: **DO NOT ACTUALLY DO THIS! THIS IS JUST AN EXAMPLE!** By including the decryption key in your compiled script, it is possible for an attacker to still decode your script. Instead, your whitelist server or other server-side security should return the correct key to prevent unauthorized users from accessing your script.
	]]
	return "6c7572617068203e20616c6c21202331" .. "6c7572617068203e20616c6c21202331"
end

local decryptionKey = getDecryptionKey()

local myFunction = LPH_ENCFUNC(function()
	someImportantLogic()
end, "6c7572617068203e20616c6c212023316c7572617068203e20616c6c21202331", decryptionKey)

myFunction()
```

**Incorrect Usage:**

```lua
local decryptionKey = ...
local myFunc = function() end

LPH_ENCFUNC(function() end, "6c7572617068203e20616c6c212023316c7572617068203e20616c6c21202331", decryptionKey) -- LPH_ENCFUNC does not automatically call the passed function.

LPH_ENCFUNC(function() end, "6c7572617068203e20616c6c212023316c7572617068203e20616c6c21202331", "6c7572617068203e20616c6c212023316c7572617068203e20616c6c21202331") -- The decryption key cannot be a constant, and must be a runtime variable.

LPH_ENCFUNC(function() end, "", decryptionKey) -- The encryption key must be a 64-length hex-encoded string.

LPH_ENCFUNC(myFunc, decryptionKey, decryptionKey) -- The first argument must be a constant function, and the second argument must be a constant string
```

### LPH_ENCSTR

```lua
string LPH_ENCSTR(const string toEncrypt)
```

**Description:** Encrypts the specified string constant using a more intense encryption algorithm.

**Aliases:** `LPH_STRENC`

**Correct Usage:**

```lua
local myString = LPH_ENCSTR("Important String")
print(LPH_ENCSTR("Hello, World!"))
return LPH_ENCSTR("Goodbye!")
```

**Incorrect Usage:**

```lua
local myString = LPH_ENCSTR(variable) -- argument is not a constant.
print(LPH_ENCSTR("A", "B")) -- too many arguments passed.
print(LPH_ENCSTR()) -- not enough arguments passed.
```

### LPH_ENCNUM

```lua
number LPH_ENCNUM(const number toEncrypt)
```

**Description:** Encrypts the specified number constant using a more intense encryption algorithm. `LPH_ENCNUM` works on doubles and integers (*integers only exist in Lua 5.3+*) and always preserves types when encrypting.

**Aliases:** `LPH_NUMENC`

**Correct Usage:**

```lua
local myNumber = LPH_ENCNUM(1000)
print(LPH_ENCNUM(1), LPH_ENCNUM(1.0))
return LPH_ENCNUM(1E100)
```

**Incorrect Usage:**

```lua
local myString = LPH_ENCNUM(variable) -- argument is not a constant.
print(LPH_ENCNUM(0, 0)) -- too many arguments passed.
print(LPH_ENCNUM()) -- not enough arguments passed.
```

### LPH_CRASH

```lua
void LPH_CRASH()
```

**Description:** Securely crashes the VM and corrupts the VM context.

**Aliases:** *None!*

**Correct Usage:**

```lua
LPH_CRASH()
return LPH_CRASH()
```

**Incorrect Usage:**

*There is technically no incorrect usage for this macro if it is called because the VM will crash irrespective of any passed arguments or other context.*

### LPH_JIT

```lua
function LPH_JIT(const function toEnhance)
```

**Description:** These macros heavily optimize the passed function to run at exponentially higher speeds (in most cases). These macros should be used on large pieces of code that should run fast and don't need intensive security (like rendering loops or math calculations). You should use this macro on any parts of your script that are performance-critical. `LPH_JIT_MAX` is a more intense version of `LPH_JIT` that applies even more optimization.

**Aliases:** `LPH_JIT_MAX` (*Applies more aggressive optimizations!*)

**Correct Usage:**

```lua
local myFunction = LPH_JIT(function() end)
LPH_JIT(function() end)()
someFunction(LPH_JIT(function() end))
```

**Incorrect Usage:**

```lua
LPH_JIT(function() end) -- LPH_JIT does not call the passed function automatically.
```

### LPH_NO_VIRTUALIZE

> **WARNING!** This macro will expose your code, minified and stripped of comments and variable names. Use this macro carefully on code that does not require obfuscation.

```lua
function LPH_NO_VIRTUALIZE(const function toDevirtualize)
```

**Description:** This macro is used to disable obfuscation for certain sections of your code and can be utilized similarly to `LPH_JIT`. Your code will be stripped of local and upvalue names, comments, and line information. Raw strings and other components of your code will still be exposed. You should only use this macro on functions that do not need *any* security.

**Aliases:** *None!*

**Limitations:**

Code wrapped by this macro has more restrictions than virtualized code because non-virtualized code is subject to Lua's syntax/VM limitations instead of Luraph's syntax/VM limitations.

Some characteristics that are different inside `LPH_NO_VIRTUALIZE` are but not limited to:

- Syntax extensions accepted by Luraph's compiler that are unsupported in vanilla Lua may cause syntax errors.
- Register allocation: Luraph may allocate internal registers or temporary locals that count towards the limit.
- Syntax recursion depth: Luraph may add additional levels/depth to the file's syntax tree that can cause overflow.

To prevent these issues, please ensure your code works unobfuscated on your platform and is not structured in a way that could exceed Lua's limits (e.g., having very long/deep syntax chains, or defining mass quantities of locals).

**Correct Usage:**

```lua
local myFunction = LPH_NO_VIRTUALIZE(function() end)
LPH_NO_VIRTUALIZE(function() end)()
someFunction(LPH_NO_VIRTUALIZE(function() end))
```

**Incorrect Usage:**

```lua
LPH_NO_VIRTUALIZE(function() end) -- LPH_NO_VIRTUALIZE does not call the passed function automatically.
LPH_NO_VIRTUALIZE(myFunction) -- The first argument must be a constant function!
```

**Usage on Platforms:**

```lua
task.spawn(LPH_NO_VIRTUALIZE(function() end))
```

```lua
game:GetService("RunService").RenderStepped:Connect(LPH_NO_VIRTUALIZE(function() end))
```

```lua
hookmetamethod(game, "__index", LPH_NO_VIRTUALIZE(function() end))
```

```lua
Citizen.CreateThread(LPH_NO_VIRTUALIZE(function() end))
```

### LPH_NO_UPVALUES

> **WARNING!** This macro does not work when `Static Environment` is enabled or on platforms with `_ENV`.

```lua
function LPH_NO_UPVALUES(const function toFix)
```

**Description:** Due to the structure of Luraph, virtualized functions will have a much larger amount of upvalues than normal. To fix this, `LPH_NO_UPVALUES` will wrap the passed function in a proxy function with 0 upvalues to fix bugs in certain programs that do not work with high upvalue counts.

**Aliases:** *None!*

**Correct Usage:**

```lua
local myFunction = LPH_NO_UPVALUES(function() end)
LPH_NO_UPVALUES(function() end)()
someFunction(LPH_NO_UPVALUES(function() end))
```

**Incorrect Usage:**

```lua
LPH_NO_UPVALUES(function() end) -- LPH_NO_UPVALUES does not automatically call the passed function!
LPH_NO_UPVALUES(myFunction) -- The first argument must be a constant function!
```

**Usage on Platforms:**

```lua
hookfunction(functionToHook, LPH_NO_UPVALUES(function() end))
```

```lua
local myFunction = LPH_NO_UPVALUES(function() end)
hookfunction(functionToHook, myFunction)
```

```lua
hookmetamethod(game, "__index", LPH_NO_UPVALUES(function() end))
```

### LPH_OBFUSCATED

```lua
const boolean LPH_OBFUSCATED
```

**Description:** This macro is a constant value that is set as `true` during obfuscation, which can allow certain code paths to only run in an obfuscated *or* unobfuscated context. Any conditions containing this macro will be simplified at compile time, and unreachable code will be removed entirely.

Optimization is applied to this macro, so code that is not run will be removed entirely from the script at compile time.

**Aliases:** *None!*

**Limitations:**

This macro does not remove the extra scope created for the condition body when used in conditions (e.g., if statements, or while statements). *This means that locals inside of `LPH_OBFUSCATED` branches will **not** be visible even if the condition passes.*

```lua
if LPH_OBFUSCATED then
	local myValue = "Hello, World!" -- This variable is only visible to the scope of the branch.
end

print(myValue) -- Even if obfuscated, this will print nil since myValue will be accessed globally.
```

However, optimized branches can still access the parent scope. This can be used to fix situations like this:

```lua
local myValue = someDefaultValue

if LPH_OBFUSCATED then
	myValue = "Hello, World!"
end

print(myValue) -- If obfuscated, this will print "Hello, World!" properly.
```

**Correct Usage:**

```lua
if LPH_OBFUSCATED then
	validateWhitelist() -- This code will only run during obfuscation.
else
	skipWhitelist()
	-- This code will only run when the script is not obfuscated.
	-- Additionally, there will be no traces of this code in the obfuscated code.
end

return LPH_OBFUSCATED and runWithoutDebugging() or runWithDebugging()
```

Obfuscates to:

```lua
do
	validateWhitelist()
end

return runWithoutDebugging()

-- skipWhitelist() and runWithDebugging() are not present in the obfuscated code.
-- LPH_OBFUSCATED is replaced with true, and the conditions are optimized away.
```

**Incorrect Usage:**

```lua
LPH_OBFUSCATED() -- LPH_OBFUSCATED is a boolean value, not a function.
```

### LPH_LINE

```lua
const int LPH_LINE
```

**Description:** This macro is a constant value that is replaced with the current line during compilation. This macro is replaced with a constant value, so line information is not required to be enabled for it to work.

**Aliases:** *None!*

**Correct Usage:**

```lua
local firstLine = LPH_LINE
print("Current Line ", LPH_LINE)
error("Error on line: " .. LPH_LINE)
```

Obfuscates to:

```lua
local firstLine = 1
print("Current Line ", 2)
error("Error on line: 3")
```

**Incorrect Usage:**

```lua
LPH_LINE() -- LPH_LINE is a number value, not a function.
```

## Quick Reference Table

| Macro | Type | Description |
| --- | --- | --- |
| `LPH_ENCFUNC` | Function | Cryptographically encrypts a function |
| `LPH_ENCSTR` | Function | Encrypts a string constant |
| `LPH_ENCNUM` | Function | Encrypts a number constant |
| `LPH_CRASH` | Function | Securely crashes the VM |
| `LPH_JIT` | Function | Optimizes function for performance |
| `LPH_JIT_MAX` | Function | More aggressive performance optimization |
| `LPH_NO_VIRTUALIZE` | Function | Disables obfuscation for a function |
| `LPH_NO_UPVALUES` | Function | Fixes high upvalue count issues |
| `LPH_OBFUSCATED` | Constant | Boolean, true when obfuscated |
| `LPH_LINE` | Constant | Current line number during compilation |

## Usage Examples

### Performance Optimization

```lua
-- Optimize a rendering loop
local renderLoop = LPH_JIT(function()
	for i = 1, #objects do
		objects[i].CFrame = calculateTransform(objects[i])
	end
end)

RunService.RenderStepped:Connect(renderLoop)
```

### Conditional Code Based on Obfuscation

```lua
if LPH_OBFUSCATED then
	-- Code only runs when obfuscated
	validateLicenseKey()
else
	-- Code only runs when not obfuscated (for testing)
	skipValidation()
end
```

### Encrypting Sensitive Strings

```lua
local apiKey = LPH_ENCSTR("sk-1234567890abcdef")
local secretEndpoint = LPH_ENCSTR("https://api.example.com/v1/secret")
```

### Disabling Obfuscation for Performance-Critical Code

```lua
-- For code that needs maximum performance and doesn't need security
local mathCalculation = LPH_NO_VIRTUALIZE(function(a, b)
	return a * b + math.sqrt(a^2 + b^2)
end)
```
