# WindUI Documentation

Source: https://footagesus.github.io/WindUI-Docs/docs

This document is a consolidated Markdown reference for WindUI, a Luau UI library for Roblox script hubs. It is based on the public WindUI documentation and source documentation files from the WindUI-Docs repository. Use this reference only in environments where you have permission to run client-side Luau code, such as your own Roblox experiences, private test places, or controlled research environments. It is not a guide for bypassing Roblox protections, cheating in public experiences, or interfering with other users.

## Overview

WindUI provides window creation, theming, tabs, tab sections, dialogs, popups, tags, notifications, dividers, spacing, form elements, code blocks, paragraphs, sections, config saving, localization, icons, and an optional key system. The documented version visible in the upstream site navigation is `1.6.53` Beta.

The API examples assume `WindUI` has already been loaded, usually with `loadstring(game:HttpGet(...))()`, and that UI elements are created from a `Window`, `Tab`, or related parent object.

## Table of Contents

### Introduction

- [About WindUI](#about-windui)

### Setup

- [Load WindUI](#load-windui)
- [Themes](#themes)

### UI

- [Window](#window)
- [Key System](#key-system)
- [Tab](#tab)
- [Tab Section](#tab-section)
- [Dialog](#dialog)
- [Popup](#popup)
- [Tag](#tag)
- [Notification](#notification)
- [Divider](#divider)
- [Space](#space)

### Elements

- [Button](#button)
- [Code](#code)
- [Colorpicker](#colorpicker)
- [Dropdown](#dropdown)
- [Input](#input)
- [Keybind](#keybind)
- [Paragraph](#paragraph)
- [Section](#section)
- [Slider](#slider)
- [Toggle](#toggle)

### Other

- [Installation](#installation)
- [Localization](#localization)
- [Configs](#configs)
- [Icons](#icons)
- [FAQ](#faq)

## About WindUI

WindUI is a stylish, open-source Luau UI library designed for Roblox script hubs. It provides a modern and customizable interface toolkit with windows, tabs, dialogs, popups, notifications, form elements, theming, localization, config saving, and an optional key system.

Key characteristics documented by the project:

- Modern design
- Customizable themes and controls
- Open source project
- Many UI elements
- Built-in key system support

> WindUI is currently in Beta. The upstream documentation notes that bugs, issues, and unstable features may occur while active development continues.

## Load WindUI

Load WindUI with loadstring.

### Load methods

- Load latest version Recommended
```luau
local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()
```

- Load selected version
```luau
local Version = "1.6.41"
local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/download/" .. Version .. "/main.lua"))()
```
#### Setting Library
#### Set Notification Lower
```luau
WindUI:SetNotificationLower(true)
```

#### Set Font
```luau
WindUI:SetFont("rbxassetid://font-id-here")
```
#### Other
#### Get Themes
```luau
WindUI:GetThemes()
```

#### Get Current Theme
```luau
WindUI:GetCurrentTheme()
```

#### Get Window Transparency (true or false)
```luau
WindUI:GetTransparency()
```

#### Get Window Size (UDim2)
```luau
WindUI:GetWindowSize()
```

## Themes

Creating Theme and Using it.

### Creating Theme
**Default:**

```luau
WindUI:AddTheme({
    Name = "My Theme", -- theme name

    Accent = Color3.fromHex("#18181b"),
    Background = Color3.fromHex("#101010"), -- Accent
    Outline = Color3.fromHex("#FFFFFF"),
    Text = Color3.fromHex("#FFFFFF"),
    Placeholder = Color3.fromHex("#7a7a7a"),
    Button = Color3.fromHex("#52525b"),
    Icon = Color3.fromHex("#a1a1aa"),
})
```
**Advanced:**

```luau
WindUI:AddTheme({
    Name = "My Theme", -- theme name

    -- More Soon!

    Accent = Color3.fromHex("#18181b"),
    Background = Color3.fromHex("#101010"), -- Accent
    BackgroundTransparency = 0,
    Outline = Color3.fromHex("#FFFFFF"),
    Text = Color3.fromHex("#FFFFFF"),
    Placeholder = Color3.fromHex("#7a7a7a"),
    Button = Color3.fromHex("#52525b"),
    Icon = Color3.fromHex("#a1a1aa"),

    Hover = Color3.fromHex("#FFFFFF"), -- Text
    BackgroundTransparency = 0,

    WindowBackground = Color3.fromHex("101010"), -- Background
    WindowShadow = Color3.fromHex("000000"),

    DialogBackground = Color3.fromHex("#101010"), -- Background
    DialogBackgroundTransparency = 0, -- BackgroundTransparency
    DialogTitle = Color3.fromHex("#FFFFFF"), -- Text
    DialogContent = Color3.fromHex("#FFFFFF"), -- Text
    DialogIcon = Color3.fromHex("#a1a1aa"), -- Icon

    WindowTopbarButtonIcon = Color3.fromHex("a1a1aa"), -- Icon
    WindowTopbarTitle = Color3.fromHex("FFFFFF"), -- Text
    WindowTopbarAuthor = Color3.fromHex("FFFFFF"), -- Text
    WindowTopbarIcon = Color3.fromHex("FFFFFF"), -- Text

    TabBackground = Color3.fromHex("#FFFFFF"), -- Text
    TabTitle = Color3.fromHex("#FFFFFF"), -- Text
    TabIcon = Color3.fromHex("a1a1aa"), -- Icon

    ElementBackground = Color3.fromHex("#FFFFFF"), -- Text
    ElementTitle = Color3.fromHex("#FFFFFF"), -- Text
    ElementDesc = Color3.fromHex("#FFFFFF"), -- Text
    ElementIcon = Color3.fromHex("#a1a1aa"), -- Icon

    PopupBackground = Color3.fromHex("#101010"), -- Background
    PopupBackgroundTransparency = 0, -- BackgroundTransparency
    PopupTitle = Color3.fromHex("#FFFFFF"), -- Text
    PopupContent = Color3.fromHex("#FFFFFF"), -- Text
    PopupIcon = Color3.fromHex("#a1a1aa"), -- Icon

    DialogBackground = Color3.fromHex("#101010"), -- Background
    DialogBackgroundTransparency = 0, -- Transparency
    DialogTitle = Color3.fromHex("#FFFFFF"), -- Text
    DialogContent = Color3.fromHex("#FFFFFF"), -- Text
    DialogIcon = Color3.fromHex("#a1a1aa"), -- Icon

    Toggle = Color3.fromHex("#52525b"), -- Button
    ToggleBar = Color3.fromHex("#FFFFFF"), -- White

    Checkbox = Color3.fromHex("#52525b"), -- Button
    CheckboxIcon = Color3.fromHex("#FFFFFF"), -- White

    Slider = Color3.fromHex("#52525b"), -- Button
    SliderThumb = Color3.fromHex("#FFFFFF"), -- White

})
```
**With Gradients:**

```luau
WindUI:AddTheme({
    Name = "My Theme", -- theme name

    -- example of gradient (available for all values. e.g. Button, Icon, Text...)   --
    Accent = WindUI:Gradient({                                                      --
        ["0"] = { Color = Color3.fromHex("#1f1f23"), Transparency = 0 },            --
        ["100"]   = { Color = Color3.fromHex("#18181b"), Transparency = 0 },        --
    }, {                                                                            --
        Rotation = 0,                                                               --
    }),                                                                             --

})
```
#### Gradient Example
```lua
WindUI:Gradient({
    ["0"] = { Color = Color3.fromHex("#1f1f23"), Transparency = 0 },
    ["100"]   = { Color = Color3.fromHex("#18181b"), Transparency = 0 },
}, {
    Rotation = 0,
}),
```

#### Set Theme
```luau
WindUI:SetTheme("My Theme")
```

## Window

Creating Window and Editing it.

### Creating Window

#### Full & Short example
**Full example:**

```luau
local Window = WindUI:CreateWindow({
    Title = "My Super Hub",
    Icon = "door-open", -- lucide icon
    Author = "by .ftgs and .ftgs",
    Folder = "MySuperHub",

    -- ↓ This all is Optional. You can remove it.
    Size = UDim2.fromOffset(580, 460),
    MinSize = Vector2.new(560, 350),
    MaxSize = Vector2.new(850, 560),
    ToggleKey = Enum.KeyCode.LeftShift,
    Transparent = true,
    Theme = "Dark",
    Resizable = true,
    SideBarWidth = 200,
    BackgroundImageTransparency = 0.42,
    HideSearchBar = true,
    ScrollBarEnabled = false,

    -- ↓ Optional. You can remove it.
    --[[ You can set 'rbxassetid://' or video to Background.
        'rbxassetid://':
            Background = "rbxassetid://", -- rbxassetid
        Video:
            Background = "video:YOUR-RAW-LINK-TO-VIDEO.webm", -- video
    --]]

    -- ↓ Optional. You can remove it.
    User = {
        Enabled = true,
        Anonymous = true,
        Callback = function()
            print("clicked")
        end,
    },

    --       remove this all,
    -- !  ↓  if you DON'T need the key system
    KeySystem = {
        -- ↓ Optional. You can remove it.
        Key = { "1234", "5678" },

        Note = "Example Key System.",

        -- ↓ Optional. You can remove it.
        Thumbnail = {
            Image = "rbxassetid://",
            Title = "Thumbnail",
        },

        -- ↓ Optional. You can remove it.
        URL = "YOUR LINK TO GET KEY (Discord, Linkvertise, Pastebin, etc.)",

        -- ↓ Optional. You can remove it.
        SaveKey = false, -- automatically save and load the key.

        -- ↓ Optional. You can remove it.
        -- API = {} ← Services. Read about it below ↓
    },
})
```

**Short example:**

```luau
local Window = WindUI:CreateWindow({
    Title = "My Super Hub",
    Icon = "door-open", -- lucide icon. optional
    Author = "by .ftgs and .ftgs", -- optional
})
```
#### Setting Window
#### Set Background Image
```luau
Window:SetBackgroundImage("rbxassetid://id-here")
```

#### Set Background Image Transparency
```luau
Window:SetBackgroundImageTransparency(value: number) -- from 0  to 1
```

#### Set Toggle Key
```luau
Window:SetToggleKey(Enum.KeyCode.H)
```

#### Set Transparency
```luau
Window:ToggleTransparency(true)
```

#### Set Resizable
```luau
Window:IsResizable(false)
```
#### Edit 'OpenButton'
```luau
Window:EditOpenButton({
    Title = "Open Example UI",
    Icon = "monitor",
    CornerRadius = UDim.new(0,16),
    StrokeThickness = 2,
    Color = ColorSequence.new( -- gradient
        Color3.fromHex("FF0F7B"),
        Color3.fromHex("F89B29")
    ),
    OnlyMobile = false,
    Enabled = true,
    Draggable = true,
})
```
#### Edit Topbar Buttons
#### Disable Topbar Buttons
```luau
Window:DisableTopbarButtons({
    "Close",
    "Minimize",
    "Fullscreen",
})
```

#### Create Custom Topbar Buttons
```luau
--                        ↓ Special name     ↓ Icon     ↓ Callback                         ↓ Order
Window:CreateTopbarButton("MyCustomButton1", "bird",    function() print("clicked!") end,  990)
```
#### Configure User Icon
#### Enable
```luau
Window.Icon:Enable()
```

#### Disable
```luau
Window.Icon:Disable()
```

#### Set Anonymous
```luau
Window.Icon:SetAnonymous(true) -- true or false
```
#### Other
### Actions

#### Open Window
```luau
Window:Open()
```

#### Close Window
```luau
Window:Close()
```

#### Toggle Window (open or close)
```luau
Window:Toggle()
```

---

### Triggers

#### `:OnClose()`
```luau
Window:OnClose(function()
    print("Window Closed")
end)
```

#### `:OnOpen()`
```luau
Window:OnOpen(function()
    print("Window Opened")
end)
```

#### `:OnDestroy()`
```luau
Window:OnDestroy(function()
    print("Window Destroyed")
end)
```
---

### Elements

#### Unlock All Elements
```luau
Window:UnlockAll()
```

#### Lock All Elements
```luau
Window:LockAll()
```

#### Get Locked Elements
```luau
Window:GetLocked()
```

#### Get Unlocked Elements
```luau
Window:Unlocked()
```

#### Get All Elements
```luau
Window.AllElements
```

---

### Other

#### Set Window Icon Size
```luau
Window:SetIconSize(48) -- default is 20
```

---
#### API
### Window Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `UI Library` | Title of the window. |
| `Icon` | `string` | `false` | `nil` | Lucide, rbxassetid, or URL icon for the window. |
| `IconSize` | `number` | `false` | `20` | Icon Size. |
| `IconThemed` | `boolean` | `false` | `false` | Makes custom icon themed |
| `Author` | `string` | `false` | `nil` | Name of the UI author. |
| `Folder` | `string` | `true` | `nil` | Folder name for saving data (like keys, URL images). |
| `Debug` | `boolean` | `false` | `false` | Enable debug mode |
| `Size` | `UDim2` | `false` | `UDim2.new(0,580,0,460)` | Custom size for the window. |
| `MinSize` | `Vector2` | `false` | `Vector2.new(560, 350)` | Minimum window size |
| `MaxSize` | `Vector2` | `false` | `Vector2.new(850, 560)` | Maximal window size |
| `Transparent` | `boolean` | `false` | `false` | Whether the window background is transparent. |
| `Theme` | `string` | `false` | `Dark` | Theme style (e.g., Dark, Light). |
| `Resizable` | `boolean` | `false` | `true` | Can or cannot resize ui |
| `SideBarWidth` | `number` | `false` | `200` | Width of the sidebar. |
| `Background` | `string` | `false` | `nil` | Background image ID. |
| `BackgroundImageTransparency` | `number` | `false` | `0` | Changes Background Image Transparency |
| `HideSearchBar` | `boolean` | `false` | `false` | Hides Searchbar |
| `HidePanelBackground` | `boolean` | `false` | `false` | Hides Panel Background |
| `ScrollBarEnabled` | `boolean` | `false` | `false` | Enable ScrollBar |
| `User` | `table` | `false` | `{}` | Settings related to the user system. |
| `KeySystem` | `table` | `false` | `{}` | Settings for key authentication system. |
| `OpenButton` | `table` | `false` | `{}` | Editing the openbutton |

### User Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Enabled` | `boolean` | `true` | `false` | Enable the user system. |
| `Anonymous` | `boolean` | `false` | `false` | Hide your nickname and avatar. |
| `Callback` | `function` | `false` | `nil` | Function executed on user interaction. |

### KeySystem Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Key` | `table` | `true` | `{}` | List of valid keys. |
| `Note` | `string` | `false` | `nil` | Additional note or instructions. |
| `Thumbnail` | `table` | `false` | `{}` | Thumbnail image and title. |
| `URL` | `string` | `false` | `nil` | URL to link for key system. |
| `SaveKey` | `boolean` | `false` | `false` | Save the entered key locally. |
| `API` | `table` | `false` | `nil` | Connect with your key system services |

### Thumbnail Options (inside KeySystem)

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Image` | `string` | `true` | `nil` | Thumbnail image ID. |
| `Title` | `string` | `false` | `nil` | Title shown with thumbnail. |
| `Width` | `number` | `false` | `240` | Thumbnail image width |

## Key System

Adding the key system.

### Key System
The Key System is created in :CreateWindow()

#### Available Services
At the moment, there are only:
- [PlatoBoost](https://platoboost.com)
- [Panda Development](https://pandadevelopment.net/)
- [Luarmor](https://luarmor.net/)

More services will be soon.

**Example:**

```luau
local Window = WindUI:CreateWindow({
    -- ...

    KeySystem = {                                                               --
        Note = "Example Key System. With platoboost, etc.",                     --
        API = {                                                                 --
            { -- PlatoBoost
                --[[ Here you can write your title, description, and icon --]]  --
                Title = "Platoboost",-- optional . you can remove it
                Desc = "Click to copy.", -- optional . you can remove it
                Icon = "rbxassetid://", -- optional . you can remove it

                Type = "platoboost", -- type
                ServiceId = 1234, -- service id
                Secret = "platoboost-secret", -- platoboost secret
            },                                                                  --
            { -- Panda development
                Type = "pandadevelopment", -- type
                ServiceId = "myServiceId", -- service id
            },                                                                  --
        },                                                                      --
    },                                                                          --
})
```

**Platoboost:**

```luau
local Window = WindUI:CreateWindow({
    -- ...

    KeySystem = {                                                   --
        Note = "Example Key System. With platoboost.",              --
        API = {                                                     --
            { -- PlatoBoost
                Type = "platoboost",                                --
                ServiceId = 1234, -- service id
                Secret = "platoboost-secret", -- platoboost secret
            },                                                      --
        },                                                          --
    },                                                              --
})
```

**Panda development:**

```luau
local Window = WindUI:CreateWindow({
    -- ...

    KeySystem = {                                                   --
        Note = "Example Key System. With pandadevelopment.",        --
        API = {                                                     --
            { -- pandadevelopment
                Type = "pandadevelopment", -- type
                ServiceId = "myServiceId", -- service id
            },                                                      --
        },                                                          --
    },                                                              --
})
```

**Luarmor:**

```luau
local Window = WindUI:CreateWindow({
    -- ...

    KeySystem = {                                                   --
        Note = "Example Key System. With luarmor.",                 --
        API = {                                                     --
            { -- luarmor
                Type = "luarmor", -- type
                ScriptId = 1234, -- script id
                Discord = "Discord URL", -- DISCORD server link
            },                                                      --
        },                                                          --
    },                                                              --
})
```

---

#### Add your service.
You can also add your own services if you can't wait for the new update.
**With tips:**

```luau
--              ↓ Change this to your service (like `luarmor`, `platoboost`, `keyguardian`)
WindUI.Services.mysuperservicetogetkey = {
    Name = "My Super Service",
    Icon = "droplet", -- <-- lucide or rbxassetid or raw link to img

    Args = { "ServiceId", "SuperId" }, --      <- \
                                       --         |
    New = function(ServiceId, SuperId) -- <------ | Args!!!!!!!!!!!!
        function validateKey(key) -- <--- this too important!!!
            -- your function to validate key
            -- see examples at src/utils/ in WindUI Repo

            if not key then
                return false, "Key is invalid!"

            end

            return true, "Key is valid!"
        end

        function copyLink()
            return setclipboard("link to key system service.")
        end

        return {
            --↓↓ do not change this!!
            Verify = validateKey, -- <-- important!!!
            Copy = copyLink -- <-- important!!!
        }
    end
}
```

**Without tips:**

```luau
WindUI.Services.mysuperservicetogetkey = {
    Name = "My Super Service",
    Icon = "droplet",

    Args = { "ServiceId", "SuperId" },

    New = function(ServiceId, SuperId)
        function validateKey(key)
            -- your function to validate key
            -- see examples at src/utils/ in WindUI Repo

            if not key then
                return false, "Key is invalid!"
            end

            return true, "Key is valid!"
        end

        function copyLink()
            return setclipboard("link to key system service.")
        end

        return {
            Verify = validateKey,
            Copy = copyLink
        }
    end
}
```

#### Use your service.
Now your can use your service.
```luau
local Window = WindUI:CreateWindow({
    -- ...

    KeySystem = {
        Note = "...",
        API = {
            {
                Type = "mysuperservicetogetkey",
                ServiceId = 1234",
                SuperId = 1234",
            },
        },
    },
})
```

Yes, I know it's difficult to understand.

## Tab

Creating Tab.

### Creating Tab

```luau
local Tab = Window:Tab({
    Title = "Tab Title",
    Icon = "bird", -- optional
    Locked = false,
})
```

#### Select Tab
```luau
Tab:Select() -- Select Tab
```

---
#### API
### Tab Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Tab` | Title of the Tab. |
| `Icon` | `string` | `false` | `nil` | Lucide or rbxassetid icon or URL for the Tab. |
| `IconThemed` | `boolean` | `false` | `false` | Makes custom icon themed |
| `Locked` | `boolean` | `false` | `false` | Locking the Tab. |

## Tab Section

Creating Section for the Tabs and Editing it.

### Creating Section for the Tabs

```luau
local Section = Window:Section({
    Title = "Section for the tabs",
    Icon = "bird",
    Opened = true,
})
```

Tab sections have two states:
1. Just a text (when there are no tabs),
2. a state where it can be opened/closed (when there are tabs inside)

---
#### API
### Section Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Section` | Title of the Section. |
| `Icon` | `string` | `false` | `nil` | Lucide or rbxassetid icon or URL for the Tab. |
| `IconThemed` | `boolean` | `false` | `false` | Makes custom icon themed |
| `Opened` | `boolean` | `false` | `false` | Opened section |

## Dialog

Dialog component for WindUI.

### Creating Dialog

```luau
local Dialog = Window:Dialog({
    Icon = "bird",
    Title = "Dialog Title",
    Content = "Content Text",
    Buttons = {
        {
            Title = "Confirm",
            Callback = function()
                print("Confirmed!")
            end,
        },
        {
            Title = "Cancel",
            Callback = function()
                print("Cancelled!")
            end,
        },
    },
})
```

#### Show Dialog
```luau
Dialog:Show()
```

#### Close Dialog
```luau
Dialog:Close()
```

---

### API

### Dialog Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Dialog` | Title text of the dialog. |
| `Icon` | `string` | `false` | `nil` | Dialog Icon |
| `IconThemed` | `boolean` | `false` | `false` | Makes custom icon themed |
| `Content` | `string` | `true` | `nil` | Main description inside the dialog. |
| `Buttons` | `table` | `true` | `{}` | List of buttons with their titles and callbacks. |

### Button Options (inside dialog)

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Button` | Button title text. |
| `Icon` | `string` | `false` | `nil` | Lucide icon for the Button. |
| `Variant` | `string` | `false` | `Primary` | Variants: (Primary, Secondary, Tertiary) |
| `Callback` | `function` | `false` | `nil` | Function executed when button is clicked. |

## Popup

Creating Popup.

### Creating Popup

```luau
WindUI:Popup({
    Title = "Popup Title",
    Icon = "info",
    Content = "Popup content",
    Buttons = {
        {
            Title = "Cancel",
            Callback = function() end,
            Variant = "Tertiary",
        },
        {
            Title = "Continue",
            Icon = "arrow-right",
            Callback = function() end,
            Variant = "Primary",
        }
    }
})
```

---
#### API
### Popup Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Popup` | Title of the Popup. |
| `Icon` | `string` | `false` | `nil` | Lucide or rbxassetid or URL icon for the Popup. |
| `IconThemed` | `boolean` | `false` | `false` | Makes custom icon themed |
| `Content` | `string` | `true` | `Content` | Content for information or warnings. |
| `Buttons` | `table` | `true` | `{}` | List of button objects for the Popup. |

### Button Options (inside Popup)

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Button` | Title of the Button. |
| `Icon` | `string` | `false` | `nil` | Lucide icon for the Button. |
| `Variant` | `string` | `true` | `Primary` | Style variant: Primary, Secondary, or Tertiary. |
| `Callback` | `function` | `false` | `nil` | Function to execute when the button is clicked. |

## Tag

Creating Tag.

### Creating Tag
```luau
Window:Tag({
    Title = "v1.6.6",
    Icon = "github",
    Color = Color3.fromHex("#30ff6a"),
    Radius = 0, -- from 0 to 13
})
```
#### API
### Tag Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Tag` | Title of the Tag. |
| `Icon` | `string` | `false` | `nil` | Icon of the Tag. |
| `Color` | `Color3` | `false` | `Color3.fromHex("#315dff")` | Tag Color. |
| `Radius` | `number` | `false` | `13` | Tag Radius (0–13) |

## Notification

Creating Notification.

### Creating Notification
```luau
WindUI:Notify({
    Title = "Notification Title",
    Content = "Notification Content example!",
    Duration = 3, -- 3 seconds
    Icon = "bird",
})
```
#### API
### Notification Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Button` | Title of the Notification. |
| `Content` | `string` | `false` | `nil` | Content of the Notification. |
| `Duration` | `number` | `false` | `5` | Duration of the Notification. |
| `Icon` | `string` | `false` | `nil` | Icon of the Notification. |
| `IconThemed` | `boolean` | `false` | `false` | Will color the icon to match the Theme, if you don\'t use Lucide icons but use other vector icons (rbxassetid or URL) |

## Divider

Creating Divider.

### Creating Divider

Divider can be created in the [Window](/docs/window) (next to the Tabs) and in the [Tab](/docs/tab) (inside)

```luau
Window:Divider()
```

```luau
Tab:Divider()
```

## Space

Creating Space.

### Creating Space

Space can be created in the [Window](/docs/window) (next to the Tabs) and in the [Tab](/docs/tab) (inside)

```luau
Window:Space()
```

```luau
Tab:Space()
```

## Button

Creating Button and Editing it.

### Creating Button

**Default:**

```luau
local Button = Tab:Button({
    Title = "Button",
    Desc = "Test Button",
    Locked = false,
    Callback = function()
        -- ...
    end
})
```

**Advanced:**

```luau
local Button = Tab:Button({
    Title = "Advanced Button",
    Color = Color3.fromHex("#a2ff30"), -- paint the button
    Justify = "Center", -- align items in the center (Center or Between or Left or Right)
    IconAlign = "Left", -- Left or Right of the text
    Icon = "", -- removing icon
    Callback = function()
        -- ...
    end
})
```

#### Set Title
```luau
Button:SetTitle("Title Example")
```

#### Set Description
```luau
Button:SetDesc("Description Example")
```

#### Lock Element
```luau
Button:Lock()
```

#### Unlock Element
```luau
Button:Unlock()
```

#### Destroy Element
```luau
Button:Destroy()
```

---
#### API
### Button Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Button` | Title of the Button. |
| `Desc` | `string` | `false` | `nil` | Description of the Button. |
| `Color` | `Color3` | `false` | `nil` | Button Color |
| `Justify` | `string` | `false` | `Between` | Items horizontal align |
| `IconAlign` | `string` | `false` | `Right` | Icon align |
| `Locked` | `boolean` | `false` | `false` | Locks the Element |
| `Callback` | `function` | `false` | `nil` | Callback of the Button |

## Code

Creating Code and Editing it.

### Creating Code

```lua
local Code = Tab:Code({
    Title = "Code",
    Code = [[print("Hello World!")]]
})
```

#### Set Code
```lua
Code:SetCode([[print("New code!")]])
```

#### Destroy Code
```lua
Code:Destroy()
```

#### `:OnCopy()`
```lua
Code:OnCopy(function()
    print("Code '" .. Code.Title .. '" Copied!")
end)
```

---
#### API
### Code Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Code` | Title of the Code. |
| `Code` | `string` | `true` | `nil` | Code. |

## Colorpicker

Creating Colorpicker and Editing it.

### Creating Colorpicker

```luau
local Colorpicker = Tab:Colorpicker({
    Title = "Colorpicker",
    Desc = "Colorpicker Description",
    Default = Color3.fromRGB(0, 255, 0),
    Transparency = 0,
    Locked = false,
    Callback = function(color)
        print("Background color: " .. tostring(color))
    end
})
```

#### Set Title
```luau
Colorpicker:SetTitle("Title Example")
```

#### Set Description
```luau
Colorpicker:SetDesc("Description Example")
```

#### Lock Element
```luau
Colorpicker:Lock()
```

#### Unlock Element
```luau
Colorpicker:Unlock()
```

#### Destroy Element
```luau
Colorpicker:Destroy()
```

---
#### API
### Colorpicker Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Colorpicker` | Title of the Colorpicker. |
| `Desc` | `string` | `false` | `nil` | Description of the Colorpicker. |
| `Default` | `Color3` | `true` | `nil` | Default color of the Colorpicker |
| `Transparency` | `number` | `true` | `nil` | Transparency of the Colorpicker `(0-1)` Remove this if you don't want to adjust the transparency. |
| `Locked` | `boolean` | `false` | `false` | Locks the Element |
| `Callback` | `function` | `false` | `nil` | Callback of the Colorpicker |

## Dropdown

Creating Dropdown and Editing it.

### Creating Dropdown

#### Dropdown

**Multi Dropdown:**

```luau
local Dropdown = Tab:Dropdown({
    Title = "Dropdown (Multi)",
    Desc = "Dropdown Description",
    Values = { "Category A", "Category B", "Category C" },
    Value = { "Category A" },
    Multi = true,
    AllowNone = true,
    Callback = function(option)
        -- option is a table: { "Category A", "Category B" }
        print("Categories selected: " .. game:GetService("HttpService"):JSONEncode(option))
    end
})
```

**No Multi Dropdown:**

```luau
local Dropdown = Tab:Dropdown({
    Title = "Dropdown",
    Desc = "Dropdown Description",
    Values = { "Category A", "Category B", "Category C" },
    Value = "Category A",
    Callback = function(option)
        print("Category selected: " .. option)
    end
})
```

**Advanced Dropdown:**

```luau
local Dropdown = Tab:Dropdown({
    Title = "Advanced Dropdown",
    Desc = "Dropdown Description",
    Values = {
        {
            Title = "Category A",
            Icon = "bird"
        },
        {
            Title = "Category B",
            Icon = "house"
        },
        {
            Title = "Category C",
            Icon = "droplet"
        },
    },
    Value = "Category A",
    Callback = function(option)
        print("Category selected: " .. option.Title .. " with icon " .. option.Icon)
    end
})
```

**Advanced Dropdown 2:**

```luau
local Dropdown = Tab:Dropdown({
    Title = "Advanced Dropdown 2 (example)",
    Values = {
        {
            Title = "New file",
            Desc = "Create a new file",
            Icon = "file-plus",
            Callback = function()
                print("Clicked 'New File'")
            end
        },
        {
            Title = "Copy link",
            Desc = "Copy the file link",
            Icon = "copy",
            Callback = function()
                print("Clicked 'Copy link'")
            end
        },
        {
            Title = "Edit file",
            Desc = "Allows you to edit the file",
            Icon = "file-pen",
            Callback = function()
                print("Clicked 'Edit file'")
            end
        },
        { Type = "Divider", },
        {
            Title = "Delete file",
            Desc = "Permanently delete the file",
            Icon = "trash",
            Callback = function()
                print("Clicked 'Delete file'")
            end
        },
    }
})
```

#### Set Title
```luau
Dropdown:SetTitle("Title Example")
```

#### Set Description
```luau
Dropdown:SetDesc("Description Example")
```

#### Select value
- For default Dropdown
```luau
Dropdown:Select("Category B")
```
- For Multi Dropdown
```luau
Dropdown:Select({"Category B", "Category C"})
```

#### Refresh Values (Update)
```luau
Dropdown:Refresh({ "New Category A", "New Category B" })
```

#### Lock Element
```luau
Dropdown:Lock()
```

#### Unlock Element
```luau
Dropdown:Unlock()
```

#### Destroy Element
```luau
Dropdown:Destroy()
```

---
#### API
### Dropdown Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Dropdown` | Title of the Dropdown. |
| `Desc` | `string` | `false` | `nil` | Description of the Dropdown. |
| `Value` | `string or table (for multi)` | `true` | `nil` | Value of the Dropdown |
| `Values` | `table` | `true` | `{}` | Values to select in the Dropdown |
| `AllowNone` | `boolean` | `false` | `false` | Allow None selected |
| `SearchBarEnabled` | `boolean` | `false` | `false` | Enable Search |
| `Multi` | `boolean` | `false` | `false` | Select multiple values |
| `Locked` | `boolean` | `false` | `false` | Locks the Element |
| `Callback` | `function` | `false` | `nil` | Callback of the Dropdown |

## Input

Creating Input and Editing it.

### Creating Input

```luau
local Input = Tab:Input({
    Title = "Input",
    Desc = "Input Description",
    Value = "Default value",
    InputIcon = "bird",
    Type = "Input", -- or "Textarea"
    Placeholder = "Enter text...",
    Callback = function(input)
        print("text entered: " .. input)
    end
})
```

#### Set Title
```luau
Input:SetTitle("Title Example")
```

#### Set Description
```luau
Input:SetDesc("Description Example")
```

#### Set Input value
```luau
Input:Set("New value")
```

#### Set Input Placeholder
```luau
Input:SetPlaceholder("New Placeholder")
```

#### Lock Element
```luau
Input:Lock()
```

#### Unlock Element
```luau
Input:Unlock()
```

#### Destroy Element
```luau
Input:Destroy()
```

---
#### API
### Input Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Input` | Title of the Input. |
| `Desc` | `string` | `false` | `nil` | Description of the Input. |
| `Value` | `string` | `false` | `nil` | Default value of the Input |
| `Placeholder` | `string` | `true` | `nil` | Placeholder Text of the Input |
| `Type` | `string` | `false` | `Input` | Type of input (`Textarea` or `Input`) |
| `InputIcon` | `string` | `false` | `nil` | Lucide, rbxassetid, or URL icon for the Input |
| `Locked` | `boolean` | `false` | `false` | Locks the Element |
| `Callback` | `function` | `false` | `nil` | Callback of the Input |

## Keybind

Creating Keybind and Editing it.

### Creating Keybind

```luau
local Keybind = Tab:Keybind({
    Title = "Keybind",
    Desc = "Keybind to open ui",
    Value = "G",
    Callback = function(v)
        Window:SetToggleKey(Enum.KeyCode[v])
    end
})
```

#### Set Title
```luau
Keybind:SetTitle("Title Example")
```

#### Set Description
```luau
Keybind:SetDesc("Description Example")
```

#### Lock Element
```luau
Keybind:Lock()
```

#### Unlock Element
```luau
Keybind:Unlock()
```

#### Destroy Element
```luau
Keybind:Destroy()
```

---
#### API
### Keybind Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Keybind` | Title of the Keybind. |
| `Desc` | `string` | `false` | `nil` | Description of the Keybind. |
| `Value` | `string` | `true` | `F` | Default value of the Keybind |
| `Locked` | `boolean` | `false` | `false` | Locks the Element |
| `Callback` | `function` | `false` | `nil` | Callback of the Keybind |

## Paragraph

Creating Paragraph and Editing it.

### Creating Paragraph

```luau
local Paragraph = Tab:Paragraph({
    Title = "Paragraph with Image, Thumbnail, Buttons",
    Desc = "Test Paragraph",
    Color = "Red",
    Image = "",
    ImageSize = 30,
    Thumbnail = "",
    ThumbnailSize = 80,
    Locked = false,
    Buttons = {
        {
            Icon = "bird",
            Title = "Button",
            Callback = function() print("1 Button") end,
        }
    }
})
```

#### Set Title
```luau
Paragraph:SetTitle("Title Example")
```

#### Set Description
```luau
Paragraph:SetDesc("DescriptionExample")
```

#### `:SetImage(image: string, size: number)`
```luau
Paragraph:SetImage("rbxassetid://...")
```

#### `:SetThumbnail(image: string, size: number)`
```luau
Paragraph:SetThumbnail("rbxassetid://...")
```

#### Destroy Element
```luau
Paragraph:Destroy()
```

---
#### API
### Paragraph Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Paragraph` | Title of the paragraph. |
| `Desc` | `string` | `false` | `nil` | Description of the paragraph. |
| `Image` | `string` | `false` | `nil` | Lucide, rbxassetid, or URL icon for the paragraph. |
| `ImageSize` | `number` | `false` | `22` | Image Size. |
| `IconThemed` | `boolean` | `false` | `false` | Makes custom icon (image) themed |
| `Thumbnail` | `string` | `false` | `nil` | Thumbnail of the paragraph. |
| `ThumbnailSize` | `number` | `false` | `80` | Thumbnail height size. |
| `Locked` | `boolean` | `false` | `false` | Locks the Element |
| `Buttons` | `table` | `false` | `{}` | List of buttons with their titles and callbacks. |

### Button Options (inside paragraph)

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Button` | Button title text. |
| `Icon` | `string` | `false` | `nil` | Lucide icon for the Button. |
| `Callback` | `function` | `false` | `nil` | Function executed when button is clicked. |

### Paragraph colors

| Preview                                                | Name   | Hex       |
| ------------------------------------------------------ | ------ | --------- |
|     | Red    | `#e53935` |
|     | Orange | `#f57c00` |
|     | Green  | `#43a047` |
|     | Blue   | `#039be5` |
|  | White  | `#ffffff` |
|     | Grey   | `#484848` |

## Section

Creating Section and Editing it.

### Creating Section

**Full example:**

```luau
local Section = Tab:Section({
    Title = "Section",
    Box = false,
    FontWeight = "SemiBold",
    TextTransparency = 0.05,
    TextXAlignment = "Left",
    TextSize = 17, -- Default Size
    Opened = true,
})
```

**Short example:**

```luau
local Section = Tab:Section({
    Title = "Section",
})
```

#### Set Title
```luau
Section:SetTitle("New Title Example")
```

#### Destroy Element
```luau
Section:Destroy()
```

Now you can create elements inside this section. e.g.
**Button:**

```luau
Section:Button({ ... })
```

---
#### API
### Section Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Section` | Title of the Section. |
| `Box` | `boolean` | `false` | `false` | Section in the box |
| `FontWeight` | `string` | `false` | `SemiBold` | Title Font Weight |
| `TextTransparency` | `number` | `false` | `0.05` | Text Transparency |
| `TextXAlignment` | `string` | `false` | `nil` | Text Horizontal Alignment |
| `TextSize` | `number` | `false` | `nil` | Text Size of the Section |
| `Opened` | `boolean` | `false` | `false` | Will the section be open |

## Slider

Creating Slider and Editing it.

### Creating Slider

```luau
local Slider = Tab:Slider({
    Title = "Slider",
    Desc = "Slider Description",

    -- To make float number supported,
    -- make the Step a float number.
    -- example: Step = 0.1
    Step = 1,
    Value = {
        Min = 20,
        Max = 120,
        Default = 70,
    },
    Callback = function(value)
        print(value)
    end
})
```

#### Set Title
```luau
Slider:SetTitle("Title Example")
```

#### Set Description
```luau
Slider:SetDesc("Description Example")
```

#### Set Slider value
```luau
Slider:Set(42)
```

#### Set Slider maximum value
```luau
Slider:SetMax(200)
```

#### Set Slider minimum value
```luau
Slider:SetMin(10)
```

#### Lock Element
```luau
Slider:Lock()
```

#### Unlock Element
```luau
Slider:Unlock()
```

#### Destroy Element
```luau
Slider:Destroy()
```

---
#### API
### Slider Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Slider` | Title of the Slider. |
| `Desc` | `string` | `false` | `nil` | Description of the Slider. |
| `Value` | `table` | `true` | `{}` | Value of the Slider |
| `Step` | `number` | `false` | `1` | Slider Moving step  |
| `Locked` | `boolean` | `false` | `false` | Locks the Element |
| `Callback` | `function` | `false` | `nil` | Callback of the Slider |

### Value Options (inside Slider)

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Min` | `number` | `true` | `0` | The minimum Value of the Slider |
| `Max` | `number` | `true` | `nil` | The maximum Value of the Slider |
| `Default` | `number` | `true` | `0` | The default Value of the Slider |

## Toggle

Creating Toggle and Editing it.

### Creating Toggle

```luau
local Toggle = Tab:Toggle({
    Title = "Toggle",
    Desc = "Toggle Description",
    Icon = "bird",
    Type = "Checkbox",
    Value = false, -- default value
    Callback = function(state)
        print("Toggle Activated" .. tostring(state))
    end
})
```

#### Set Title
```luau
Toggle:SetTitle("Title Example")
```

#### Set Description
```luau
Toggle:SetDesc("Description Example")
```

#### Set Toggle value
```luau
Toggle:Set(true)
```

#### Lock Element
```luau
Toggle:Lock()
```

#### Unlock Element
```luau
Toggle:Unlock()
```

#### Destroy Element
```luau
Toggle:Destroy()
```

---
#### API
### Toggle Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Title` | `string` | `true` | `Toggle` | Title of the Toggle. |
| `Desc` | `string` | `false` | `nil` | Description of the Toggle. |
| `Value` | `boolean` | `false` | `false` | Default value of the Toggle |
| `Type` | `string` | `false` | `Toggle` | Type of the Toggle (`Checkbox` or `Toggle`) |
| `Icon` | `string` | `false` | `nil` | Lucide, rbxassetid, or URL icon for the Toggle |
| `Locked` | `boolean` | `false` | `false` | Locks the Element |
| `Callback` | `function` | `false` | `nil` | Callback of the Toggle |

## Installation

Install WindUI source and Edit it.

A Tutorial on how to install WindUI Source on your device and edit it.

#### Clone Repository
Clone the [WindUI](https://github.com/Footagesus/WindUI/) repository from GitHub to your local machine.
```bash
git clone https://github.com/Footagesus/WindUI.git
```
This command will create a copy of the WindUI project in your working directory, allowing you to modify and customize it as needed.

#### Install Dependencies
Navigate to the cloned directory and install npm packages:
You need [aftman](https://github.com/LPGhatguy/aftman) to install darklua
```bash
cd WindUI

npm install
aftman install
```

#### Build WindUI
Once everything is set up, you can build WindUI:

- For development, run:
```bash
npm run dev
```

- For production build, run:
```bash
npm run build
```

- For development live-build, run:
```bash
npm run live-build
```

This will start a local build server at http://localhost:8642.
It automatically build your changes and serves a ready-to-use development bundle, so you can instantly test it in Roblox without manual rebuilding.

Usage in Roblox (exploit environment):
```luau
loadstring(game:HttpGet("http://localhost:8642/dist/main.lua"))()
```

## Localization

Translate UI into different languages.

### Creating Localization example
```luau
-- Paste this before 'CreateWindow()'
WindUI:Localization({ -- it automatically detects the user's language
    Enabled = true,
    Prefix = "loc:",
    DefaultLanguage = "en",
    Translations = {
        ["ru"] = {
            ["WINDUI_EXAMPLE"] = "WindUI Пример",
            ["WELCOME"] = "Добро пожаловать в WindUI!",
            ["SETTINGS"] = "Настройки",
            ["FEATURES"] = "Функционал",
            ["LOGO"] = "rbxassetid://...", -- russian logo
        },
        ["en"] = {
            ["WINDUI_EXAMPLE"] = "WindUI Example",
            ["WELCOME"] = "Welcome to WindUI!",
            ["SETTINGS"] = "Settings",
            ["FEATURES"] = "Features",
            ["LOGO"] = "rbxassetid://...", -- english logo
        },
        -- and more languages...
    }
})
```

#### Set Language
```luau
-- removes automatic language detection
WindUI:SetLanguage("en") -- english language
```

#### Usage example
```luau
local Window = WindUI:CreateWindow({
    Title = "loc:WINDUI_EXAMPLE", -- using with prefix 'loc:'
    Icon = "loc:LOGO",
    Author = "loc:WELCOME", -- too
    Folder = "MyTestHub",
    Theme = "Dark",
})
```
#### API
### Localization Options

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `Enabled` | `boolean` | `true` | `false` | Enabled Localizations |
| `Prefix` | `string` | `false` | `loc:` | Prefix for the Localization |
| `DefaultLanguage` | `string` | `true` | `en` | Default Language if users language not found |
| `Translations` | `table` | `true` | `nil` | Languages |

## Configs

Creating Configs. Saving and Loading it.

#### Load ConfigManager
```luau
local ConfigManager = Window.ConfigManager
```

#### Create Config File
```luau
local myConfig = ConfigManager:CreateConfig("myConfigExample")
```

#### Register elements
```luau
myConfig:Register("SpecialNameExample", Element)
```

##### Example Register
**Flags (easy):**

```luau
local ToggleElement = Tab:Toggle({
    Title = "Toggle",
    Desc = "Config Test Toggle",
    Flag = "ToggleElement"
    Callback = function(v) print("Toggle Changed: " .. tostring(v)) end
})
```
**Function:**

```luau
local ToggleElement = Tabs.ConfigTab:Toggle({
    Title = "Toggle",
    Desc = "Config Test Toggle",
    Callback = function(v) print("Toggle Changed: " .. tostring(v)) end
})

-- register
--                 | Element name (no spaces)    | Element          |
myConfig:Register( "toggleNameExample",          ToggleElement      )
```

#### Save & Load configs

The config will be saved in
```text
{yourExecutor}/Workspace/WindUI/{yourFolderName: Window.Folder}/config/myConfigExample.json
```

#### Save
```luau
myConfig:Save()
```

#### Load
```luau
myConfig:Load()
```

#### Delete
```luau
myConfig:Delete()
```

#### Get All Config Files
```luau
ConfigManager:AllConfigs() -- returns a table
```

#### Example script
```luau
local ConfigManager = Window.ConfigManager
local myConfig = ConfigManager:CreateConfig("config1") -- will be saved as config1.json

Tab:Toggle({
    Title = "Toggle Example",
    Flag = "ToggleElement",
    Callback = function(v)
        print("Toggle: " .. v)
    end
})

myConfig:Save()
myConfig:Load()
```

## Icons

What icons WindUI uses and how to use them.

### Icons

#### Icons that are used in WindUI:
- [Lucide Icons](https://lucide.dev)
- [Geist Icons](https://vercel.com/geist/icons)
- [Craft Icons](https://www.figma.com/community/file/1415718327120418204) (figma community)

#### How to use them
You can use it like this:

```luau
-- lucide
local Icon = "lucide:bird"

-- geist
local Icon = "geist:window"

-- craft
local Icon = "craft:macbook-stroke"
```

Source of icons: https://github.com/Footagesus/Icons

More icons will be soon

## FAQ

Frequently Asked Questions.

### Answers to your questions.

---

###### Want to ask a question?

[Visit our Discord server!](https://discord.gg/ftgs-development-hub-1300692552005189632)

---
#### What is WindUI?

WindUI is a stylish, open-source UI (User Interface) library specifically designed for Roblox Script Hubs.
Developed by `Footagesus`   (`.ftgs`, `Footages`).
It aims to provide developers with a modern, customizable, and easy-to-use toolkit for creating visually appealing interfaces within Roblox.
The project is primarily written in Lua (Luau), the scripting language used in Roblox.

#### How to lock the elements?
You can lock Element with `Locked`

Example with [Button](/docs/button):
```luau
local MySuperButton = Tab:Button({
    Title = "Button",
    Locked = true, -- lock the element
    Callback = function() -- callback will not work. element is locked.
        print("clicked")
    end
})
```

__All elements with callback have a `Locked` thing__

Also you can lock the element at any time:
```luau
MySuperButton:Lock()
```

or unlock:
```luau
MySuperButton:Unlock()
```
#### How to colorize custom icons?
if you use Nebula icons, or your custom vector* icons.

Usually WindUI doesn't colorize custom icons to match the theme.

- But you can do it with `IconThemed`

`IconThemed` is available in:
- [Window](/docs/window)
- [Tab](/docs/tab)
- [Tab Section](/docs/tabsection)
- [Dialog](/docs/dialog)
- [Popup](/docs/popup)
- [Notification](/docs/notification)
- [Paragraph](/docs/paragraph)

For example:
**Window:**

```luau
local Window = WindUI:CreateWindow({
    -- ...

    Icon = "rbxassetid://..." -- my custom vector* icon
    IconThemed = true                                       --
})
```

**Tab:**

```luau
local Tab = Window:Tab({
    -- ...

    Icon = "rbxassetid://..." -- my custom vector* icon
    IconThemed = true                                       --
})
```

**Popup:**

```luau
local Popup = WindUI:Popup({
    -- ...

    Icon = "rbxassetid://..." -- my custom vector* icon
    IconThemed = true                                       --
})
```

and others.
#### How to disable OpenButton?
You can disable the `OpenButton` if you don't need it.

```luau
Window:EditOpenButton({
    Enabled = false
})
```

_More coming soon_
