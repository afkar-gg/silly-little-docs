# MoonVeil Macros Documentation

Source: https://moonveil.cc/docs/macros/

MoonVeil macros allow you to selectively apply additional features to your Lua scripts during obfuscation, adjusting the balance between performance and security.

## Overview

MoonVeil's macro system provides various functions that can be used to:

- Force or omit specific obfuscation passes (CFF, VM)
- Encrypt functions and strings
- Compress strings
- Virtualize functions
- Inline functions as a preprocessor
- Obfuscate named indexes
- Detect if the script is currently obfuscated
- Get the current line number
- Crash the VM intentionally

Macros are declared as globals and are replaced during obfuscation.

## MV_CFF

```lua
declare function MV_CFF<A..., R...>(vmFunction: (A...) -> R..., enableExpressionDecomposition?: boolean): (A...) -> R...
```

Will force the [CFF](https://moonveil.cc/docs/options/flatten-control-flow/) pass onto the passed function. Expression decomposition is enabled by default, but can be disabled with the second argument.

### Valid Usage

```lua
MV_CFF(function()
    for i = 0, 10 do
        print('this is flattened!', i)
    end
    MV_OMIT_CFF(function() print('this isnt') end)()
end)()

-- passing false as a second argument will disable expression decomposition, leading to much smaller output
MV_CFF(function()
    for i = 0, 10 do
        print('this is flattened!', i)
    end
    MV_OMIT_CFF(function() print('this isnt') end)()
end, false)()
```

## MV_COMPRESS

```lua
declare function MV_COMPRESS(data: string): string
```

Will compress the passed string and decompress at runtime.

> **Note**: `LPH_ENCSTR` is an available alias, although not a direct replacement.

### Valid Usage

```lua
return MV_COMPRESS([[Lorem ipsum odor amet, consectetuer adipiscing elit. Ipsum curae senectus ut tempor rhoncus sed aliquam fermentum. Luctus consectetur consectetur vitae parturient risus enim. Justo venenatis dictumst eget tincidunt eu fermentum. Erat elementum molestie ad aliquam donec mauris tempor. Justo dictumst dapibus ultricies potenti scelerisque class sociosqu vivamus. Dignissim lobortis tristique nullam placerat lectus maximus class dolor.

Class eros ligula facilisis viverra adipiscing. Convallis penatibus lacinia hac pretium tempor mauris risus. Aliquet sagittis quisque dignissim duis volutpat eleifend sagittis. Mollis duis congue non auctor duis sapien senectus. Magna aptent ac nostra nisl; penatibus sagittis dis. Tempus sapien porta dolor luctus tempus felis habitant phasellus.

Volutpat quam senectus tristique egestas dui risus praesent maecenas adipiscing. Ante est platea cursus consequat quisque curabitur euismod. Himenaeos blandit augue; sed condimentum curae curabitur aliquam. Hac hendrerit arcu maecenas nisi iaculis donec natoque gravida. Sapien pretium nascetur, nisl in feugiat ultricies. Pretium ipsum ultricies et non blandit accumsan arcu. Egestas enim litora tortor nisi aenean posuere. Tortor posuere purus facilisi augue ipsum.

Metus ultrices netus rhoncus sed mi et neque ex. Tincidunt commodo metus quisque hendrerit tristique nostra amet sociosqu. Curae egestas feugiat massa torquent a vel faucibus faucibus. Euismod posuere cubilia penatibus class molestie torquent tristique commodo nam. Nullam rhoncus ad sem, habitasse euismod porta gravida. Ut curabitur tristique orci vestibulum rhoncus. Habitasse pulvinar risus congue dis cras consequat inceptos. Nulla hac interdum commodo enim magna; habitant id? Lacus pretium per accumsan praesent rutrum vestibulum.]])
```

## MV_CRASH

```lua
declare function MV_CRASH(void): void
```

Crashes the VM and corrupts the state.

> **Note**: `LPH_CRASH` is an available alias.
> **Warning**: This macro can only be used in virtualized blocks.

### Valid Usage

```lua
MV_VM(function() MV_CRASH() end)()
MV_VM(function()
    local a = MV_CRASH()
end)()
```

## MV_ENC_FUNC

```lua
declare function MV_ENC_FUNC<A..., R...>(vmFunction: (A...) -> R..., encryptKey: string, decryptKey: string): (A...) -> R...
```

Will virtualize and encrypt the passed function constant with the provided `encryptKey`. Then `decryptKey` is evaluated at runtime to grab the key. There are no restrictions on the length or the allowed characters of the keys, only that they are the same.

> **Note**: `LPH_ENCFUNC` is an available alias.

### Valid Usage

```lua
local total = 0

local getKey = MV_VM(function()
  -- WARNING: this is just an example! don't do this
  return "pass" .. "word123"
end)

for i = 1, 10 do
  -- referenced upvalues will be captured
  MV_ENC_FUNC(function()
    print("in virtualized function # 1", i)
    total = total + i
  end, "password123", getKey())()
end

return total
```

## MV_ENC_STR

```lua
declare function MV_ENC_STR(str: string, encryptKey: string, decryptKey: string): string
```

Will encrypt the passed string constant with the provided `encryptKey`. Then `decryptKey` is evaluated at runtime to grab the key. There are no restrictions on the length or the allowed characters of the keys, only that they are the same.

### Valid Usage

```lua
local getKey = MV_VM(function()
  return "pass" .. "word123"
end)

return MV_ENC_STR("hello world", "password123", getKey())
```

## MV_INDEX_TO_NUM

```lua
declare function MV_INDEX_TO_NUM(tbl: {}): {}
```

Will replace all instances of named indexes with a unique number constant.

### Valid Usage

```lua
local foo = MV_INDEX_TO_NUM({total = 0, b = 2})
for i = 1, 10 do
  foo.total = foo.total + i
end
return foo.total + foo.b
```

turns into:

```lua
-- note that the order of the indexes has been changed as well
local a={[24453]=2,[19314]=0}
for b=1,10 do
    a[19314]=a[19314]+b
end
return a[19314] + a[24453]
```

## MV_INLINE

```lua
declare function MV_INLINE<A..., R...>(inlineFunction: (A...) -> R...): (A...) -> R...
```

Will mark the passed function to be inlined where used.

Some limitations apply:

- The passed function cannot be variadic (i.e. no `...` usage)
- The passed function cannot tailcall or otherwise recursively call itself.
- You must use this macro in its specific form, i.e. `local <var> = MV_INLINE(function() ... end)`

Note that this pass acts more like a preprocessor that replaces the function calls with the results of the function (if any), and can have strange effects in some cases.

For example, when an inlined function is called, its function body is inserted right before the statement the call is in. This can lead to some unexpected behavior when used in `or` or `if` expressions:

```lua
local called = false
local testFunc = MV_INLINE(function()
    called = true
    return 'hi'
end)

local foo = 'bar' or testFunc()
print(foo, called)
```

Will print `bar true`, where under normal circumstances testFunc should not be executed. This is because the `or` expression is evaluated after the inserted body.

### Valid Usage

```lua
local testFunc = MV_INLINE(function(start, endn)
    local total = 0
    for i = start, endn do
        total = total + i
    end
    return total
end)

local testFunc2 = MV_INLINE(function()
    return testFunc(1, 10), testFunc(11, 20), testFunc(1, 15)
end)

print(testFunc2())
```

## MV_LINE

```lua
declare MV_LINE: number
```

Will be replaced with a number constant representing the current line.

> **Note**: `LPH_LINE` is an available alias.

### Valid Usage

```lua
local a = MV_LINE
print(a, MV_LINE)
```

turns into:

```lua
print(1, 2)
```

> **Note**: the `a` local got folded yet retains the original line number.

## MV_OBFUSCATED

```lua
declare MV_OBFUSCATED: boolean
```

This macro is a constant value that is set as `true` during obfuscation, which can allow certain code paths to only run in an obfuscated or unobfuscated context. MoonVeil will optimize out the blocks which should not run.

> **Note**: `LPH_OBFUSCATED` is an available alias.

### Valid Usage

```lua
if MV_OBFUSCATED then
    validateWhitelist() -- This code will only run if the script has been obfuscated.
else
    skipWhitelist()
    -- This code will only run when the script is not obfuscated.
    -- Additionally, there will be no traces of this code in the obfuscated code.
end

return if MV_OBFUSCATED then runWithoutDebugging() else runWithDebugging()
```

turns into:

```lua
do
    validateWhitelist()
end

return runWithoutDebugging()
```

## MV_OMIT

```lua
declare function MV_OMIT(omit: any): any
```

Will omit all obfuscation steps from the passed expression, will also disregard any indexes set by `MV_INDEX_TO_NUM`. Passing a function to this can be useful to keep performance-critical sections unobfuscated.

When used in a virtualized block (using the `MV_VM` or `MV_ENC_FUNC` macros), this will lift the function from the virtualized block as a separate standalone minified function in plaintext.

> **Note**: `MV_OMIT_FUNCTION` and `LPH_NO_VIRTUALIZE` are also available aliases.
> Additionally, if you'd like to only omit the function from certain AST passes, you can use the `MV_OMIT_CFF` or `MV_OMIT_VM` macros.

### Valid Usage

```lua
local dontMangleMe = MV_OMIT(function()
  print("this whole function is in plaintext!")
end)

dontMangleMe()

local a = MV_OMIT("plaintext string")
print(a .. "im obfuscated")

local foo = 'hello'
MV_ENC_FUNC(function()
    MV_OMIT(function(aa)
        if aa == "lol" then
            foo = "OK"
        else 
            foo = "NO"
        end
    end)("lol")

    print(foo)
end, "abc", "abc")()
-- will print "OK"
```

## MV_OMIT_CFF

```lua
declare function MV_OMIT_CFF<A..., R...>(cffFunction: (A...) -> R...): (A...) -> R...
```

Similar to `MV_OMIT`, this will omit only the [CFF](https://moonveil.cc/docs/options/flatten-control-flow/) steps from the passed function. This can be useful to keep performance-critical sections unobfuscated but still have other AST passes applied to them.

### Valid Usage

```lua
MV_CFF(function()
    for i = 0, 10 do
        print('this is flattened!', i)
    end
    MV_OMIT_CFF(function() print('this isnt') end)()
end)()
```

> Make sure to call the macro result!

## MV_OMIT_VM

```lua
declare function MV_OMIT_VM<A..., R...>(vmFunction: (A...) -> R...): (A...) -> R...
```

Similar to `MV_OMIT`, this will only omit the passed function from the VM. This can be useful to keep performance-critical sections unobfuscated but still have other AST passes applied to them.

### Valid Usage

```lua
MV_VM(function()
    for i = 0, 10 do
        print('this is virtualized!', i)
    end
    MV_OMIT_VM(function() print('this isnt') end)()
end)()
```

> Make sure to call the macro result!

## MV_VM

```lua
declare function MV_VM<A..., R...>(vmFunction: (A...) -> R...): (A...) -> R...
```

Will mark the passed function to be lifted into the VM.

### Valid Usage

```lua
local virtualizedFunction = MV_VM(function(a, b, c)
  print(a .. b .. c .. " im in the vm!")
end)

virtualizedFunction("1", "2", "3")
```
