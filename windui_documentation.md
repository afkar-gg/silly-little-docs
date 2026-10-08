# WindUI Documentation

Source: https://footagesus.github.io/treehub-web/docs/windui  
Upstream Docs: https://footagesus-windui.mintlify.app  
Repository: https://github.com/Footagesus/WindUI  

This document is a comprehensive technical reference for WindUI, a modern, themeable, open-source Luau UI library for Roblox script hubs. It is synchronized with the latest upstream specifications (v1.6.66) from the Mintlify documentation and the repository source code.

---

## Table of Contents

- [Overview](#overview)
- [Installation & Loading](#installation--loading)
- [Quickstart](#quickstart)
- [Library API & Global Methods](#library-api--global-methods)
- [Theming System](#theming-system)
  - [Built-in Themes](#built-in-themes)
  - [Runtime Theme Switching](#runtime-theme-switching)
  - [Custom Themes](#custom-themes)
  - [Theme Fallback Resolution](#theme-fallback-resolution)
  - [Gradients](#gradients)
- [Window](#window)
  - [Window Configuration](#window-configuration)
  - [Window Methods](#window-methods)
- [Key System](#key-system)
  - [Configuration](#key-system-configuration)
  - [Validation Flow & Persistence](#validation-flow--persistence)
  - [Third-Party Services (API)](#third-party-services-api)
- [Navigation Components](#navigation-components)
  - [Tab](#tab)
  - [Sidebar Section](#sidebar-section)
- [Modal & Overlay Components](#modal--overlay-components)
  - [Dialog](#dialog)
  - [Popup](#popup)
  - [Notification](#notification)
  - [Tag](#tag)
  - [Tooltip](#tooltip)
- [Form & Interactive Elements](#form--interactive-elements)
  - [Button](#button)
  - [Toggle](#toggle)
  - [Slider](#slider)
  - [Input](#input)
  - [Dropdown](#dropdown)
  - [Keybind](#keybind)
  - [Colorpicker](#colorpicker)
  - [Code](#code)
  - [ProgressBar](#progressbar)
  - [Paragraph](#paragraph)
  - [Content Section](#content-section)
- [Layout Containers](#layout-containers)
  - [HStack & VStack](#hstack--vstack)
  - [Divider](#divider)
  - [Space](#space)
  - [Group (Deprecated)](#group-deprecated)
- [Media & Viewport Elements](#media--viewport-elements)
  - [Image](#image)
  - [Video](#video)
  - [Viewport](#viewport)
- [Features & System Guides](#features--system-guides)
  - [Acrylic Blur & Transparency](#acrylic-blur--transparency)
  - [Localization](#localization)
  - [Config Saving (ConfigManager)](#config-saving-configmanager)
  - [Icon System](#icon-system)
- [v1.6.66 Changelog](#v1666-changelog)
- [Contributing & Building](#contributing--building)
- [FAQ](#faq)

---

## Overview

WindUI is a modular, themeable UI library built in Luau. It provides a complete design system for Roblox hubs, including animated windows, responsive layouts, acrylic blur effects, tabbed navigation, form controls, toast notifications, modal dialogs, and configuration persistence.

Key characteristics:
- Responsive desktop and mobile interfaces with auto-scaling
- 17 built-in color themes with full support for runtime switching and gradients
- Form controls: Button, Toggle, Slider, Input, Dropdown, Keybind, Colorpicker, Code, and ProgressBar
- Advanced layout containers: HStack, VStack, and collapsible sections
- Security and access gating: Key system with file persistence and third-party API verification (Platoboost, Panda Development, Luarmor, Junkie Development)
- Automatic config persistence via `Window.ConfigManager`
- Multi-language localization with runtime language switching
- Multiple icon sets: Lucide, Solar, SF Symbols, Craft, Geist, and custom asset IDs

---

## Installation & Loading

WindUI is loaded using `loadstring` and `game:HttpGet`.

### Selected Version (v1.6.66 - Recommended for Stability)
```luau
local Version = "1.6.66"
local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/download/" .. Version .. "/main.lua"))()
```

### Latest Release
```luau
local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()
```

### Development Build (dist/main.lua)
```luau
local WindUI = loadstring(game:HttpGet("https://raw.githubusercontent.com/Footagesus/WindUI/main/dist/main.lua"))()
```

---

## Quickstart

```luau
local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/download/1.6.66/main.lua"))()

local Window = WindUI:CreateWindow({
    Title = "My Script Hub",
    Author = "by Developer",
    Folder = "MyHubData",
    Icon = "solar:folder-2-bold-duotone",
    Theme = "Dark",
    Size = UDim2.fromOffset(580, 460),
    Transparent = true,
})

local MainTab = Window:Tab({
    Title = "Main",
    Icon = "solar:home-2-bold",
})

MainTab:Button({
    Title = "Click Me",
    Desc = "Prints a greeting to the console",
    Icon = "mouse",
    Callback = function()
        print("Hello from WindUI!")
    end,
})

MainTab:Toggle({
    Title = "Speed Boost",
    Value = false,
    Callback = function(state)
        print("Speed Boost:", state)
    end,
})
```

---

## Library API & Global Methods

These methods are accessible on the root `WindUI` table and control global settings across the interface.

| Method | Parameters | Return | Description |
| :--- | :--- | :--- | :--- |
| `WindUI:CreateWindow(Config)` | `Config: table` | `Window` | Initializes and displays a new window |
| `WindUI:SetTheme(name)` | `name: string` | `table?` | Switches the active theme globally |
| `WindUI:GetCurrentTheme()` | None | `string` | Returns the active theme name |
| `WindUI:GetThemes()` | None | `table` | Returns all registered theme tables keyed by name |
| `WindUI:OnThemeChange(callback)` | `callback: function(themeName: string)` | None | Registers a listener for theme changes |
| `WindUI:AddTheme(themeTable)` | `themeTable: table` | `table` | Registers a custom theme table |
| `WindUI:Gradient(stops, props)` | `stops: table, props: table?` | `table` | Builds a gradient data table for theme colors |
| `WindUI:SetFont(fontAssetId)` | `fontAssetId: string` | None | Overrides the global font across all elements |
| `WindUI:SetParent(parentInstance)`| `parentInstance: Instance` | None | Changes the parent container for all GUI elements |
| `WindUI:ToggleAcrylic(boolean)` | `boolean: boolean` | None | Enables or disables acrylic background blur |
| `WindUI:GetWindowSize()` | None | `UDim2` | Returns the base size of the window |
| `WindUI:GetTransparency()` | None | `boolean` | Returns whether window transparency mode is active |
| `WindUI:SetNotificationLower(bool)`| `bool: boolean` | None | Moves notifications lower to avoid mobile UI overlap |
| `WindUI:Notify(Config)` | `Config: table` | `Notification` | Displays a toast notification |
| `WindUI:Popup(Config)` | `Config: table` | `Popup` | Displays a floating modal popup dialog |
| `WindUI:Localization(Config)` | `Config: table` | None | Configures translation dictionary and prefix |
| `WindUI:SetLanguage(code)` | `code: string` | None | Switches active UI translation language |

```luau
-- Reparent GUI to gethui or CoreGui
WindUI:SetParent(gethui and gethui() or game:GetService("CoreGui"))

-- Apply a custom font
WindUI:SetFont("rbxassetid://12187365364")

-- Adjust notification position for mobile devices
WindUI:SetNotificationLower(true)

-- Listen for theme changes
WindUI:OnThemeChange(function(newTheme)
    print("Active theme changed to:", newTheme)
end)
```

---

## Theming System

WindUI includes 17 built-in themes and a theme engine that supports solid colors, gradients, and fallback inheritance.

### Built-in Themes

| Name | Background | Accent | Primary | Description |
| :--- | :--- | :--- | :--- | :--- |
| `Dark` | `#101010` | `#18181b` | `#0091ff` | Default theme: charcoal background with blue highlights |
| `Light` | `#e9e9e9` | `#ffffff` | `#0091ff` | Crisp light mode with black text |
| `Rose` | `#1f0308` | `#be185d` | `#be185d` | Deep crimson with pink tones |
| `Plant` | `#0a1b0f` | `#166534` | `#16a34a` | Forest green palette |
| `Red` | `#1c0606` | `#991b1b` | `#dc2626` | Deep red dark theme |
| `Indigo` | `#0f0a2e` | `#3730a3` | `#4f46e5` | Navy background with indigo accents |
| `Sky` | `#051a26` | `#00d4ff` | `#00d4ff` | Ocean blue background with cyan highlights |
| `Violet` | `#1e0a3e` | `#6d28d9` | `#7c3aed` | Dark purple with vivid violet accents |
| `Amber` | Gradient | Gradient | `#d97706` | Gold and amber tones driven by gradients |
| `Emerald` | `#011411` | `#047857` | `#059669` | Dark teal with emerald highlights |
| `Midnight`| `#0a0f1e` | `#1e3a8a` | `#2563eb` | Deep navy with royal blue accents |
| `Crimson` | `#0c0404` | `#b91c1c` | `#991b1b` | Near-black with deep crimson accents |
| `Monokai Pro` | `#191622` | `#fc9867` | `#ab9df2` | Code editor aesthetic with vibrant highlights |
| `Cotton Candy` | `#1a0b2e` | `#ec4899` | `#d946ef` | Pastel purple, pink, and cyan accents |
| `Mellowsi`| `#1c1002` | `#342a1e` | `#c9a24d` | Warm sepia and brown tones |
| `Rainbow` | Gradient | Gradient | Gradient | Full-spectrum gradient theme |

### Runtime Theme Switching

```luau
-- Switch theme immediately
WindUI:SetTheme("Indigo")

-- Read active theme
local currentTheme = WindUI:GetCurrentTheme()
```

### Custom Themes

Register custom themes using `WindUI:AddTheme(themeTable)`.

#### Color Token Reference

| Key | Type | Description |
| :--- | :--- | :--- |
| `Name` | `string` | **Required.** Theme identifier used in `SetTheme` |
| `Background` | `Color3 \| gradient` | Main window background fill |
| `Accent` | `Color3 \| gradient` | Sidebar, topbar, and frame borders |
| `Dialog` | `Color3 \| gradient` | Modal and popup dialog background |
| `Outline` | `Color3` | Window border and stroke lines |
| `Text` | `Color3 \| gradient` | Primary text and label color |
| `Placeholder` | `Color3` | Text input placeholder color |
| `Button` | `Color3 \| gradient` | Interactive button background fill |
| `Icon` | `Color3` | Primary icon tint color |
| `Toggle` | `Color3 \| gradient` | Active toggle knob/fill color |
| `Slider` | `Color3` | Slider active track fill |
| `Checkbox` | `Color3 \| gradient` | Active checkbox fill |
| `Primary` | `Color3` | Primary accent color for controls |
| `SliderIcon` | `Color3` | Icon color for slider endpoints |
| `PanelBackground` | `Color3` | Tab content area background |
| `PanelBackgroundTransparency` | `number` | Transparency of content panel (0 to 1) |
| `LabelBackground` | `Color3` | Section label background fill |
| `LabelBackgroundTransparency` | `number` | Transparency of section labels |
| `WindowTopbarTitle` | `Color3` | Window title text color |
| `WindowTopbarAuthor`| `Color3` | Subtitle/author text color |
| `WindowTopbarIcon` | `Color3` | Topbar icon tint |
| `WindowTopbarButtonIcon` | `Color3` | Topbar button icon tint |
| `WindowShadow` | `Color3` | Window drop-shadow color |

### Theme Fallback Resolution

When keys are omitted from a custom theme table, WindUI applies the following inheritance fallbacks:
- `Dialog` -> falls back to `Accent`
- `Toggle` -> falls back to `Button`
- `Checkbox` -> falls back to `Primary` -> falls back to `Icon`
- `Slider` -> falls back to `Primary` -> falls back to `Icon`
- `Primary` -> falls back to `Icon`
- `SliderIcon` -> falls back to `Icon`
- `PanelBackground` -> falls back to `#ffffff` at `0.95` transparency
- `LabelBackground` -> falls back to `#000000` at `0.83` transparency

```luau
WindUI:AddTheme({
    Name = "Ocean",
    Background = Color3.fromHex("#0d1b2a"),
    Accent = Color3.fromHex("#1b3a4b"),
    Dialog = Color3.fromHex("#0a2236"),
    Text = Color3.fromHex("#e0f0ff"),
    Placeholder = Color3.fromHex("#5b8fa8"),
    Button = Color3.fromHex("#2196a8"),
    Icon = Color3.fromHex("#4dd0e1"),
    Toggle = Color3.fromHex("#00bcd4"),
    Slider = Color3.fromHex("#0097a7"),
    Checkbox = Color3.fromHex("#00bcd4"),
    Primary = Color3.fromHex("#0097a7"),
})

WindUI:SetTheme("Ocean")
```

### Gradients

Gradients can be assigned to `Background`, `Accent`, `Dialog`, `Text`, `Placeholder`, `Button`, `Toggle`, and `Checkbox` via `WindUI:Gradient(stops, props)`.

```luau
WindUI:AddTheme({
    Name = "Sunset",
    Accent = WindUI:Gradient({
        ["0"] = { Color = Color3.fromHex("#f97316"), Transparency = 0 },
        ["100"] = { Color = Color3.fromHex("#ec4899"), Transparency = 0 },
    }, { Rotation = 45 }),

    Background = WindUI:Gradient({
        ["0"] = { Color = Color3.fromHex("#1a0000"), Transparency = 0 },
        ["100"] = { Color = Color3.fromHex("#2d0a1e"), Transparency = 0 },
    }, { Rotation = 90 }),

    Text = Color3.fromHex("#fff1f2"),
    Button = Color3.fromHex("#e11d48"),
    Icon = Color3.fromHex("#fb7185"),
})
```

---

## Window

The Window is the top-level container that manages tabs, sidebar navigation, topbars, and dialog overlays. Only one window can exist simultaneously.

### Window Configuration

```luau
local Window = WindUI:CreateWindow({
    Title = "Automation Hub",
    Author = "v1.6.66",
    Icon = "solar:folder-2-bold-duotone",
    Folder = "AutomationHubData",

    Size = UDim2.fromOffset(580, 460),
    MinSize = Vector2.new(560, 350),
    MaxSize = Vector2.new(850, 560),
    Position = UDim2.new(0.5, 0, 0.5, 0),

    Theme = "Dark",
    Transparent = true,
    Acrylic = false,
    Resizable = true,
    AutoScale = true,
    NewElements = true,

    SideBarWidth = 200,
    HideSearchBar = false,
    ScrollBarEnabled = false,
    HidePanelBackground = false,

    Topbar = {
        Height = 44,
        ButtonsType = "Default", -- "Default" or "Mac"
    },

    OpenButton = {
        Title = "Open Hub",
        Icon = "monitor",
        CornerRadius = UDim.new(0, 16),
        StrokeThickness = 2,
        Enabled = true,
        Draggable = true,
        OnlyMobile = false,
        Scale = 1,
    },

    User = {
        Enabled = true,
        Anonymous = false,
        Callback = function()
            print("User profile clicked")
        end,
    },

    ToggleKey = Enum.KeyCode.RightShift,
    IgnoreAlerts = false,
})
```

#### Window Parameters Table

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Title` | `string` | **Required** | Title displayed in the window topbar |
| `Author` | `string?` | `nil` | Secondary author/subtitle text below the title |
| `Icon` | `string?` | `nil` | Topbar icon name, `rbxassetid://`, or image URL |
| `IconSize` | `number?` | `nil` | Size of the topbar icon in pixels |
| `IconThemed` | `boolean?` | `false` | Whether the topbar icon follows the theme icon tint |
| `Folder` | `string?` | `nil` | Folder name for config saving and saved keys |
| `Size` | `UDim2?` | `UDim2.fromOffset(580, 460)` | Initial window size |
| `MinSize` | `Vector2?` | `Vector2.new(560, 350)` | Minimum resize bounds in pixels |
| `MaxSize` | `Vector2?` | `Vector2.new(850, 560)` | Maximum resize bounds in pixels |
| `Position` | `UDim2?` | `UDim2.new(0.5, 0, 0.5, 0)` | Screen starting anchor position |
| `Theme` | `string?` | `"Dark"` | Initial theme identifier |
| `Transparent` | `boolean?` | `false` | Semi-transparent window background |
| `Acrylic` | `boolean?` | `false` | Frosted glass acrylic blur effect |
| `Resizable` | `boolean?` | `true` | Enables draggable resize handle |
| `AutoScale` | `boolean?` | `true` | Scales window to fit small viewports |
| `NewElements` | `boolean?` | `false` | Modernized elements styling with pill corners |
| `SideBarWidth` | `number?` | `200` | Sidebar navigation width in pixels |
| `HideSearchBar` | `boolean?` | `false` | Hides the tab search bar at bottom of sidebar |
| `ScrollBarEnabled`| `boolean?` | `false` | Enables visible scrollbar indicators |
| `HidePanelBackground`| `boolean?`| `false` | Hides background frame of tab content area |
| `Background` | `string \| table?`| `nil` | Custom asset ID, image URL, video, or gradient |
| `BackgroundImageTransparency`| `number?`| `0` | Transparency of background image layer |
| `ShadowTransparency`| `number?` | `0.7` | Window drop-shadow transparency |
| `Radius` | `number?` | `16` | Corner radius of the window frame |
| `ElementsRadius` | `number?` | `nil` | Overrides corner radius for child elements |
| `Topbar` | `table?` | `nil` | `{ Height = 52, ButtonsType = "Default" \| "Mac" }` |
| `TopBarButtonIconSize`| `number?`| `16` (Default) / `11` (Mac) | Pixel size of topbar button icons |
| `OpenButton` | `table?` | `nil` | Floating mobile/desktop reopen button config |
| `User` | `table?` | `nil` | Profile card in sidebar (`Enabled`, `Anonymous`, `Callback`) |
| `ToggleKey` | `Enum.KeyCode?` | `nil` | Keyboard key to toggle window visibility |
| `IgnoreAlerts` | `boolean?` | `false` | Destroys window on close without confirmation dialog |
| `KeySystem` | `table?` | `nil` | Access verification configuration table |

### Window Methods

#### Navigation & Layout
- `Window:Tab(Config)`: Creates a new tab page in the sidebar.
- `Window:Section(Config)`: Creates a collapsible group in the sidebar that contains tabs.
- `Window:Divider()`: Inserts a horizontal divider line into the sidebar.
- `Window:SelectTab(TabObject)`: Switches the active tab programmatically.

#### Topbar Controls
- `Window:Tag(Config)`: Adds a badge chip to the topbar. Returns tag object with `:SetTitle(title)` and `:Destroy()`.
- `Window:CreateTopbarButton(Name, Icon, Callback, LayoutOrder?, IconThemed?, Color?, IconSize?)`: Adds a custom icon button to the topbar.
- `Window:DisableTopbarButtons(ButtonsTable)`: Disables specific topbar buttons.
- `Window:EditOpenButton(Config)`: Dynamically updates properties of the floating reopen button.

#### Visibility & State
- `Window:Open()`: Opens the window with expand animation.
- `Window:Close()`: Closes the window. Returns a close object with a `:Destroy()` method.
- `Window:Toggle()`: Toggles window visibility between open and closed.
- `Window:Destroy()`: Closes and destroys all GUI instances and connections.
- `Window:ToggleFullscreen()`: Toggles fullscreen mode for the window.

#### Appearance & Styling
- `Window:SetTitle(Title)`: Dynamically updates topbar title text.
- `Window:SetAuthor(Author)`: Updates or creates the author subtitle text.
- `Window:SetIcon(Icon)`: Updates topbar icon.
- `Window:SetIconSize(Size)`: Sets topbar icon size.
- `Window:SetSize(UDim2)`: Animates window to a new size.
- `Window:SetUIScale(Scale)`: Sets UI scale factor with smooth animation.
- `Window:GetUIScale()`: Returns current window UI scale.
- `Window:SetBackgroundImage(AssetId)`: Sets background image.
- `Window:SetBackgroundImageTransparency(Transparency)`: Adjusts background image transparency.
- `Window:SetBackgroundTransparency(Transparency)`: Adjusts window frame transparency.
- `Window:ToggleTransparency(Boolean)`: Toggles transparency mode.
- `Window:SetPanelBackground(Visible)`: Shows or hides the content panel background.
- `Window:SetToTheCenter()`: Centers window on screen.
- `Window:IsResizable(Boolean)`: Enables or disables window resize dragging.

#### Bulk Element Control
- `Window:LockAll()`: Invokes `:Lock()` on all registered elements in the window.
- `Window:UnlockAll()`: Invokes `:Unlock()` on all registered elements in the window.
- `Window:GetAll()`: Returns a table of all elements.
- `Window:GetLocked()`: Returns a table of all currently locked elements.
- `Window:GetUnlocked()`: Returns a table of all unlocked elements.

#### Lifecycle Callbacks
- `Window:OnOpen(Callback)`: Connects a function called when the window opens.
- `Window:OnClose(Callback)`: Connects a function called when the window closes.
- `Window:OnDestroy(Callback)`: Connects a function called when the window is destroyed.

---

## Key System

The Key System gates access to the window behind key validation. When configured, WindUI halts window construction, renders a modal key dialog, and only initializes the window upon successful key verification.

### Key System Configuration

```luau
local Window = WindUI:CreateWindow({
    Title = "Admin Hub",
    Folder = "AdminHubData",

    KeySystem = {
        Title = "Access Key Required",
        Note = "Join our Discord server to obtain an access key.",
        URL = "https://discord.gg/example",
        SaveKey = true,

        KeyValidator = function(enteredKey)
            return enteredKey == "my-secret-key-123"
        end,

        Thumbnail = {
            Image = "rbxassetid://1234567890",
            Title = "Premium Access",
            Width = 200,
        },
    },
})
```

#### Key System Parameters

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `Title` | `string?` | Dialog title. Defaults to the window title |
| `KeyValidator` | `function?` | Custom validation function: `function(key) return bool end` |
| `Key` | `string \| table?` | Accepted static key string or array of accepted key strings |
| `SaveKey` | `boolean?` | Persists valid key to disk at `<Folder>/<hwid>.key` |
| `Note` | `string?` | Informational text displayed in dialog body (supports RichText) |
| `URL` | `string?` | URL copied to clipboard when user clicks "Get Key" |
| `Thumbnail` | `table?` | Image panel displayed on left side (`Image`, `Title`, `Width`) |
| `API` | `table?` | Third-party verification provider configuration |

### Validation Flow & Persistence

1. **Saved Key Check**: If `SaveKey = true` and `<Folder>/<hwid>.key` exists on disk, WindUI silently validates the saved key. If valid, the dialog is skipped entirely.
2. **Key Presentation**: If no saved key exists or validation fails, the key dialog is displayed.
3. **Submission**: When the user enters a key, verification priority is evaluated in order: `KeyValidator` -> static `Key` / `Key[]` -> `API`.
4. **Acceptance**: Upon validation, the key is saved to disk (if `SaveKey = true`) and the main window opens.
5. **Rejection**: If invalid, an error notification is displayed and the key dialog remains open.

### Third-Party Services (API)

WindUI includes built-in integrations for popular Roblox executor key systems.

#### Panda Development
In v1.6.66, Panda Development validates against the Pandauth API endpoint:
```luau
KeySystem = {
    API = {
        Type = "PandaDevelopment",
        Service = "your_service_id",
    },
}
```
Validation Endpoint: `https://api.pandauth.com/api/v1/keys/validate`

#### Platoboost
```luau
KeySystem = {
    API = {
        Type = "Platoboost",
        Service = "your_service_id",
        Secret = "your_secret_key",
    },
}
```

#### Luarmor
```luau
KeySystem = {
    API = {
        Type = "Luarmor",
        ScriptId = "your_script_id",
        Discord = "https://discord.gg/example",
    },
}
```

#### Junkie Development
```luau
KeySystem = {
    API = {
        Type = "JunkieDevelopment",
        ServiceId = "your_service_id",
        ApiKey = "your_api_key",
        Provider = "your_provider",
    },
}
```

---

## Navigation Components

### Tab

Tabs represent individual pages in the sidebar and host the interactive content elements.

```luau
local Tab = Window:Tab({
    Title = "Combat",
    Desc = "Aimbot and combat features",
    Icon = "solar:shield-warning-bold",
    IconColor = Color3.fromRGB(255, 80, 80),
    IconShape = "Square", -- "Square" or nil (circle)
    IconThemed = false,
    Locked = false,
    ShowTabTitle = true,
    TabTitleAlign = "Left", -- "Left", "Center", or "Right"
    Border = true,

    CustomEmptyPage = {
        Icon = "solar:folder-error-bold",
        IconSize = 48,
        Title = "No Features",
        Desc = "This tab does not have elements yet.",
    },
})
```

#### Tab Parameters

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Title` | `string` | `"Tab"` | Label displayed on sidebar entry |
| `Desc` | `string?` | `nil` | Hover description tooltip in sidebar |
| `Icon` | `string?` | `nil` | Icon name or asset ID on sidebar entry |
| `IconColor` | `Color3?` | `nil` | Accent color for the icon background chip |
| `IconShape` | `string?` | `nil` | Shape of icon chip (`"Square"` or circle) |
| `IconThemed` | `boolean?` | `false` | Whether icon color follows theme |
| `Locked` | `boolean?` | `false` | Disables tab selection when true |
| `ShowTabTitle` | `boolean?` | `false` | Displays large title header in content panel |
| `TabTitleAlign`| `string?` | `"Left"` | Header alignment (`"Left"`, `"Center"`, `"Right"`) |
| `Border` | `boolean?` | `false` | Adds glass-sheen border to selected tab entry |
| `CustomEmptyPage`| `table?` | `nil` | Placeholder shown when tab has no elements |

#### Tab Methods
- `Tab:Select()`: Programmatically selects and opens the tab.
- `Tab:Lock()`: Disables all child elements and locks the tab.
- `Tab:Unlock()`: Unlocks the tab and its elements.
- `Tab:GetLocked()`: Returns array of locked child elements.
- `Tab:GetUnlocked()`: Returns array of unlocked child elements.
- `Tab:ScrollToTheElement(Index)`: Smoothly scrolls the content panel to an element by index.
- `Tab:SetTitle(Title)`: Updates tab sidebar title.
- `Tab:SetDesc(Description)`: Updates hover tooltip text.
- `Tab:Destroy()`: Removes the tab and its content panel.

### Sidebar Section

Sidebar sections (`Window:Section`) group related tabs under a collapsible header in the navigation sidebar.

```luau
local CombatSection = Window:Section({
    Title = "Combat Features",
    Icon = "swords",
    IconThemed = true,
    Opened = true,
})

local AimbotTab = CombatSection:Tab({ Title = "Aimbot", Icon = "crosshair" })
local EspTab = CombatSection:Tab({ Title = "Visuals", Icon = "eye" })

-- Collapse or expand programmatically
CombatSection:Collapse()
CombatSection:Expand()
```

---

## Modal & Overlay Components

### Dialog

A modal overlay attached to the window that blocks interaction with other UI components until dismissed. Created via `Window:Dialog(Config)`.

```luau
local myDialog = Window:Dialog({
    Title = "Reset Configuration",
    Content = "Are you sure you want to restore default settings? <b>This action cannot be undone.</b>",
    Icon = "alert-triangle",
    IconThemed = true,
    Width = 320, -- Clamped between 180 and 400
    Buttons = {
        {
            Title = "Cancel",
            Variant = "Secondary",
            Callback = function() end,
        },
        {
            Title = "Reset",
            Icon = "trash",
            Variant = "Primary",
            Callback = function()
                print("Configuration reset")
            end,
        },
    },
})

-- Programmatic dismissal
-- myDialog:Close()
```

#### Dialog Parameters

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Title` | `string` | `"Dialog"` | Dialog heading text |
| `Content` | `string` | `nil` | Body message (supports RichText markup) |
| `Icon` | `string?` | `nil` | Icon name displayed beside title |
| `IconThemed` | `boolean?` | `false` | Whether icon follows theme text color |
| `Width` | `number?` | `320` | Panel width in pixels (clamped to 180–400) |
| `Buttons` | `table` | `{}` | Ordered array of button configuration tables |

#### Button Variants
- `"Primary"`: Filled accent background for affirmative actions (Confirm, Save).
- `"Secondary"`: Subdued background for neutral actions (Cancel, Dismiss).
- `"Tertiary"`: Minimal borderless style for optional actions.
- `"White"`: High-contrast white background with dark text.

### Popup

Popups (`WindUI:Popup(Config)`) are self-contained floating panels that appear centered on screen without blocking interaction with the main window.

```luau
WindUI:Popup({
    Title = "Welcome to v1.6.66",
    Icon = "sparkles",
    Content = "Thank you for using our hub. Check out the changelog for new features!",
    Thumbnail = {
        Image = "rbxassetid://123456789",
        Title = "Announcement",
    },
    Buttons = {
        {
            Title = "Dismiss",
            Variant = "Secondary",
            Callback = function() end,
        },
        {
            Title = "Join Discord",
            Icon = "external-link",
            Variant = "Primary",
            Callback = function()
                setclipboard("https://discord.gg/example")
            end,
        },
    },
})
```

### Notification

Toast notifications (`WindUI:Notify(Config)`) slide in from the screen edge and automatically dismiss after a countdown duration.

```luau
local notification = WindUI:Notify({
    Title = "Export Complete",
    Content = "Profile configurations saved to disk.",
    Icon = "solar:check-circle-bold",
    Duration = 5,
    CanClose = true,
})

-- Dismiss programmatically
-- notification:Close()
```

#### Notification Parameters
- `Title`: string heading text.
- `Content`: string message body (supports RichText).
- `Icon`: string icon name or `rbxassetid://`.
- `IconThemed`: boolean tint toggle.
- `Duration`: countdown seconds before auto-dismissal (default 5).
- `CanClose`: boolean toggle for early close `×` button.
- `Background`: `rbxassetid://` image background.
- `BackgroundTransparency`: number (0 to 1).
- `Buttons`: array of action button configs.

### Tag

Tags (`Window:Tag(Config)`) render status badge chips in the window topbar.

```luau
local VersionTag = Window:Tag({
    Title = "v1.6.66",
    Icon = "github",
    Color = Color3.fromHex("#1c1c1c"),
    Border = true,
})

VersionTag:SetTitle("v1.6.66-STABLE")
-- VersionTag:Destroy()
```

### Tooltip

Tooltips display floating labels on hover. They render inside a dedicated top-level `ScreenGui` layer (`WindUI/Tooltips`) to float above all interfaces.
- **Sliders**: Enable `IsTooltip = true` to display the active value above the thumb while dragging.
- **Tabs**: Assign `Desc = "Description text"` to display a tooltip when hovering over a sidebar tab.
- Maximum tooltip width is 400px with automatic word wrap.

---

## Form & Interactive Elements

All interactive elements are created by calling their constructor on a `Tab`, `Section`, `HStack`, or `VStack`.

### Button

Clickable button supporting icons, background tints, layout alignment, and locked states.

```luau
local Button = Tab:Button({
    Title = "Execute Action",
    Desc = "Performs the primary calculation",
    Icon = "play",
    Color = Color3.fromHex("#305dff"),
    Justify = "Between", -- "Between", "Left", "Center", or "Right"
    IconAlign = "Right", -- "Left" or "Right"
    Locked = false,
    LockedTitle = "Action Unavailable",
    Callback = function()
        print("Button clicked")
    end,
})

Button:Highlight() -- Plays visual feedback sweep animation
Button:SetTitle("New Title")
Button:SetDesc("Updated description")
Button:Lock()
Button:Unlock()
Button:Destroy()
```

### Toggle

Boolean switch supporting sliding pill (`"Toggle"`) and square checkbox (`"Checkbox"`) styles.

```luau
local Toggle = Tab:Toggle({
    Title = "Auto Farm",
    Desc = "Collects resources in background",
    Icon = "power",
    Type = "Toggle", -- "Toggle" or "Checkbox"
    Value = false,
    Locked = false,
    Flag = "auto_farm",
    Callback = function(state)
        print("Auto farm toggled:", state)
    end,
})

Toggle:Set(true) -- or Toggle:SetValue(true)
Toggle:Lock()
Toggle:Unlock()
Toggle:Destroy()
```

### Slider

Numeric input slider with precision step configuration, thumb tooltip, and direct textbox input.

```luau
local Slider = Tab:Slider({
    Title = "WalkSpeed",
    Desc = "Adjust character movement speed",
    Value = {
        Min = 16,
        Max = 150,
        Default = 16,
    },
    Step = 1,
    Width = 160,
    IsTooltip = true, -- Displays value tooltip above thumb
    IsTextbox = true, -- Displays direct numeric entry box
    Icons = {
        From = "footprints",
        To = "zap",
    },
    Flag = "player_walkspeed",
    Callback = function(value)
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = value
    end,
})

Slider:Set(32) -- or Slider:SetValue(32)
Slider:SetMin(16)
Slider:SetMax(200)
Slider:Destroy()
```

> [!NOTE]
> Omitting `Title` causes the slider track to expand to the full width of the element container. In this mode, `IsTextbox` defaults to `false`.

### Input

Single-line or multi-line text input field with placeholder text and focus-loss triggers.

```luau
local Input = Tab:Input({
    Title = "Target Player",
    Desc = "Enter username or display name",
    Icon = "user",
    InputIcon = "search", -- Icon placed inside input box
    Type = "Input", -- "Input" (single-line) or "Textarea" (multi-line)
    Placeholder = "Search user...",
    Value = "",
    ClearOnFocus = false,
    Flag = "target_player",
    Callback = function(text)
        print("Input submitted:", text)
    end,
})

Input:Set("RobloxPlayer") -- or Input:SetValue("RobloxPlayer")
Input:SetPlaceholder("Type username here...")
Input:Destroy()
```

### Dropdown

Select menu supporting single-selection, multi-selection, search filtering, custom object items, and dividers.

```luau
local Dropdown = Tab:Dropdown({
    Title = "Teleport Destination",
    Desc = "Select destination waypoint",
    Values = { "Spawn", "Shop", "Arena", "Bank" },
    Value = "Spawn",
    Multi = false,
    AllowNone = false,
    SearchBarEnabled = true,
    MenuWidth = 180,
    Flag = "teleport_point",
    Callback = function(selected)
        print("Selected point:", selected)
    end,
})

-- Advanced object entries with icons and dividers
local ActionDropdown = Tab:Dropdown({
    Title = "Actions",
    Values = {
        { Title = "Use", Desc = "Consume item", Icon = "sparkles", Callback = function() end },
        { Title = "Drop", Desc = "Drop item", Icon = "arrow-down", Callback = function() end },
        { Type = "Divider" },
        { Title = "Delete", Desc = "Permanently remove", Icon = "trash", Locked = true },
    },
    Multi = false,
})

Dropdown:Select({ "Shop" })
Dropdown:Refresh({ "New Destination 1", "New Destination 2" })
Dropdown:Highlight()
```

> [!TIP]
> When calling `:Refresh()` and `:Select()` inside another dropdown's callback, wrap the calls in `task.spawn` to avoid menu re-entrancy conflicts.

### Keybind

Interactive keybind listener supporting keyboard keys and mouse buttons.

```luau
local Keybind = Tab:Keybind({
    Title = "Toggle Flying",
    Desc = "Press bound key to toggle flight",
    Value = "E", -- Accepts string ("E", "MouseLeft", "MouseRight") or Enum.KeyCode
    CanChange = true,
    Blacklist = { "W", "A", "S", "D" },
    Flag = "fly_keybind",
    Callback = function(keyName)
        print("Key pressed:", keyName)
    end,
})

-- Programmatic updates accept string or EnumItem
Keybind:Set("F")
-- or Keybind:Set(Enum.KeyCode.F)

-- Converting string callback to EnumItem
local keyCode = Enum.KeyCode[keyName]
```

### Colorpicker

Color selection component with an HSV picker dialog, hex/RGB inputs, and optional transparency slider.

```luau
local Colorpicker = Tab:Colorpicker({
    Title = "ESP Highlight",
    Desc = "Color of bounding box",
    Default = Color3.fromRGB(255, 0, 100),
    Transparency = 0, -- 0 (opaque) to 1 (transparent)
    Flag = "esp_color",
    Callback = function(color)
        print("Color selected:", color)
    end,
})

-- Update color and transparency programmatically
Colorpicker:Update(Color3.fromRGB(0, 255, 120), 0.25)
print("Current transparency:", Colorpicker.Transparency)
```

### Code

Code block container with syntax highlighting, custom theme colors, copy button, and dimension controls.

```luau
local CodeBlock = Tab:Code({
    Title = "Generated Script",
    Code = [[local Players = game:GetService("Players")
print("Local player:", Players.LocalPlayer.Name)]],
    CodeSize = 18,
    Height = UDim.new(0, 200),
    CanCopied = true,
    CodeTheme = {
        numbers = Color3.fromHex("#FAB387"),
        boolean = Color3.fromHex("#FAB387"),
        operator = Color3.fromHex("#94E2D5"),
        lua = Color3.fromHex("#CBA6F7"),
        rbx = Color3.fromHex("#F38BA8"),
        str = Color3.fromHex("#A6E3A1"),
        comment = Color3.fromHex("#9399B2"),
        call = Color3.fromHex("#89B4FA"),
    },
    OnCopy = function()
        print("Code copied to clipboard")
    end,
})

CodeBlock:SetCode("print('Updated code')")
```

### ProgressBar

Visualizes progress for tasks, timers, loading sequences, or thresholds. Added in WindUI `1.6.66`.

```luau
-- Determinate progress bar
local Bar = Tab:ProgressBar({
    Title = "Asset Preload",
    Desc = "Caching remote models",
    Value = {
        Min = 0,
        Max = 100,
        Default = 25,
    },
    DisplayMode = "Percent", -- "Percent", "Value", or "Fraction"
    ShowValue = true,
    Animate = true,
    AnimationDuration = 0.15,
    Width = 180,
    ValueWidth = 44,
})

Bar:Set(75)
local currentVal = Bar:Get()
local percentage = Bar:GetPercentage()

-- Indeterminate progress bar (loading animation)
local LoadingBar = Tab:ProgressBar({
    Title = "Syncing Data",
    Desc = "Please wait...",
    Indeterminate = true,
    IndeterminateText = "Connecting...",
    Speed = 1.2,
})

-- Custom value formatting
local MemoryBar = Tab:ProgressBar({
    Title = "Memory Usage",
    Value = { Min = 0, Max = 1024, Default = 512 },
    DisplayMode = "Value",
    Format = function(value, percentage, min, max)
        return string.format("%d MB / %d MB (%d%%)", value, max, percentage)
    end,
})
```

#### ProgressBar Methods
- `Bar:Set(Number)`: Sets current progress value.
- `Bar:Get()`: Returns current value.
- `Bar:GetPercentage()`: Returns progress percentage (0 to 100).
- `Bar:SetRange(Min, Max)`: Updates both bounds.
- `Bar:SetMin(Min)`: Updates minimum bound.
- `Bar:SetMax(Max)`: Updates maximum bound.
- `Bar:SetTitle(Title)`: Updates title label.
- `Bar:SetDesc(Desc)`: Updates description label.
- `Bar:Destroy()`: Destroys the element.

### Paragraph

Text card supporting descriptive text, leading icons, large thumbnail preview images, and action buttons.

```luau
local Card = Tab:Paragraph({
    Title = "Update Notice",
    Desc = "Version 1.6.66 is now active with progress bar support.",
    Image = "info",
    ImageSize = 24,
    Thumbnail = "rbxassetid://1234567890",
    ThumbnailSize = 64,
    Color = Color3.fromRGB(100, 200, 255),
    Buttons = {
        {
            Title = "Copy Link",
            Icon = "copy",
            Variant = "Tertiary",
            Callback = function()
                setclipboard("https://github.com/Footagesus/WindUI")
            end,
        },
    },
})

Card:SetTitle("New Announcement")
Card:SetDesc("Updated description text.")
Card:SetImage("bell", 20)
Card:SetThumbnail("rbxassetid://987654321", 48)
```

### Content Section

In-page grouping element (`Tab:Section`) that arranges elements into a distinct boxed panel with an optional collapsible toggle.

```luau
local BoxedSection = Tab:Section({
    Title = "Movement Settings",
    Desc = "Adjust speed and jump power",
    Icon = "activity",
    Box = true,
    BoxBorder = true,
    Opened = true,
})

BoxedSection:Slider({ Title = "Speed", Value = { Min = 16, Max = 100, Default = 16 } })
BoxedSection:Toggle({ Title = "Infinite Jump" })

-- Collapse or expand
BoxedSection:Collapse()
BoxedSection:Expand()
```

---

## Layout Containers

### HStack & VStack

Modern stack containers that arrange child components horizontally (`HStack`) or vertically (`VStack`). Replaces the legacy `Group` element.

```luau
-- Create a two-column row
local Row = Tab:HStack({
    AutoSpace = true,
})

local LeftColumn = Row:VStack()
local RightColumn = Row:VStack()

LeftColumn:Button({ Title = "Action 1" })
LeftColumn:Toggle({ Title = "Option A" })

RightColumn:Button({ Title = "Action 2" })
RightColumn:Toggle({ Title = "Option B" })
```

### Divider

Inserts a separator line in a tab page or sidebar:
```luau
Tab:Divider()
-- or
Window:Divider()
```

### Space

Inserts vertical blank spacing between elements:
```luau
Tab:Space({
    Size = 12, -- Pixel height
})
```

### Group (Deprecated)

> [!WARNING]
> `Group` is officially deprecated in WindUI. Use [HStack & VStack](#hstack--vstack) instead.

---

## Media & Viewport Elements

### Image

Displays a static image from an asset ID or remote URL.

```luau
Tab:Image({
    Image = "https://example.com/preview.png",
    AspectRatio = "16:9", -- "16:9", "1:1", "4:3", or number
    Radius = 12,
})
```

### Video

Displays a video frame inside the UI container.

```luau
Tab:Video({
    Video = "rbxassetid://1234567890",
    AspectRatio = "16:9",
    Radius = 10,
})
```

### Viewport

Embeds a 3D model inside an interactive `ViewportFrame`.

```luau
local CharacterModel = game.Players.LocalPlayer.Character:Clone()

local ViewportElement = Tab:Viewport({
    Object = CharacterModel,
    Interactive = true, -- Enables mouse/touch camera rotation and pinch-zoom
    Height = 220,
    Focused = true,
})
```

---

## Features & System Guides

### Acrylic Blur & Transparency

WindUI provides frosted glass blur via `DepthOfFieldEffect` combined with a workspace 3D part.

```luau
-- Enable at window creation
local Window = WindUI:CreateWindow({
    Title = "Frosted Window",
    Acrylic = true,
    Transparent = true,
})

-- Configure transparency level (0 = opaque, 1 = transparent)
WindUI.TransparencyValue = 0.20

-- Toggle at runtime
WindUI:ToggleAcrylic(true)
Window:ToggleTransparency(true)
```

> [!NOTE]
> Acrylic requires an executor environment that permits 3D part creation behind the GUI layer. If unsupported, use `Transparent = true` instead.

### Localization

WindUI includes dynamic translation resolution. Register dictionaries before constructing the UI; element titles matching the prefix translate automatically.

```luau
WindUI:Localization({
    Enabled = true,
    Prefix = "loc:",
    DefaultLanguage = "en",
    Translations = {
        en = {
            ["hub_title"] = "Automation Hub",
            ["autofarm"] = "Auto Farm",
        },
        ru = {
            ["hub_title"] = "Хаб Автоматизации",
            ["autofarm"] = "Авто Фарм",
        },
    },
})

local Window = WindUI:CreateWindow({
    Title = "loc:hub_title",
})

local Tab = Window:Tab({ Title = "Main" })
Tab:Toggle({ Title = "loc:autofarm" })

-- Switch language at runtime
WindUI:SetLanguage("ru")
```

### Config Saving (ConfigManager)

All interactive elements with a `Flag` property are automatically tracked by `Window.ConfigManager`.

#### Storage Location
Configurations are stored as JSON files on disk:
```
WindUI/<Folder>/config/<ProfileName>.json
```

#### Saving & Loading Setup

```luau
local Window = WindUI:CreateWindow({
    Title = "My Hub",
    Folder = "MyHubData", -- Required for config saving
})

local Tab = Window:Tab({ Title = "Settings" })

Tab:Toggle({
    Title = "Auto Farm",
    Flag = "farm_state",
    Value = false,
})

Tab:Slider({
    Title = "Speed",
    Flag = "speed_val",
    Value = { Min = 16, Max = 100, Default = 16 },
})

-- Build configuration management controls
local ConfigManager = Window.ConfigManager
local ConfigName = "default"

Tab:Input({
    Title = "Profile Name",
    Callback = function(text)
        ConfigName = text
    end,
})

Tab:Button({
    Title = "Save Config",
    Callback = function()
        local cfg = ConfigManager:Config(ConfigName)
        cfg:Save()
        WindUI:Notify({ Title = "Saved", Content = "Profile '" .. ConfigName .. "' saved." })
    end,
})

Tab:Button({
    Title = "Load Config",
    Callback = function()
        local cfg = ConfigManager:Config(ConfigName)
        cfg:Load()
        WindUI:Notify({ Title = "Loaded", Content = "Profile '" .. ConfigName .. "' loaded." })
    end,
})
```

#### ConfigManager Methods
- `ConfigManager:Config(name)`: Returns config object (alias for `CreateConfig`).
- `ConfigManager:CreateConfig(name, autoload?)`: Creates a config instance. If `autoload = true`, it loads 0.5s after creation if the file exists.
- `config:Save()`: Serializes flagged element values to disk.
- `config:Load()`: Restores values and fires callbacks.
- `config:Delete()`: Deletes profile file from disk.
- `ConfigManager:AllConfigs()`: Returns an array of existing profile names.
- `ConfigManager:GetAutoLoadConfigs()`: Returns an array of configs set to auto-load.

### Icon System

WindUI supports Lucide icons by default, multiple named libraries via prefixes, and custom asset IDs.

| Icon Library | Prefix Format | Example |
| :--- | :--- | :--- |
| **Lucide** (Default) | Bare name or `lucide:` | `"house"`, `"lucide:shield"` |
| **Solar** | `solar:` | `"solar:home-2-bold"`, `"solar:folder-2-bold-duotone"` |
| **SF Symbols** | `sfsymbols:` | `"sfsymbols:sunMinFill"` |
| **Craft Icons** | `craft:` | `"craft:folder"` |
| **Geist Icons** | `geist:` | `"geist:triangle"` |
| **Roblox Asset ID** | `rbxassetid://` | `"rbxassetid://123456789"` |

#### Registering Custom Icons
```luau
WindUI.Creator.AddIcons("custom", {
    ["MyIcon"] = "rbxassetid://9876543210",
})

-- Usage:
Tab:Button({
    Title = "Custom Icon",
    Icon = "custom:MyIcon",
})
```

---

## v1.6.66 Changelog

- **Code Element Updates**: Added `CodeSize: number`, `CodeTheme: table`, `CanCopied: boolean`, and `Height: UDim` parameters to Code component (#91).
- **Panda Development API**: Updated key verification endpoint to `https://api.pandauth.com/api/v1/keys/validate` (#92).
- **ProgressBar Element**: Added dedicated determinate and indeterminate `ProgressBar` element (#95).
- **Dropdown Fix**: Fixed `Dropdown.Locked` behavior to prevent menu expansion when locked (#94).
- **Window Dragging**: Fixed touch dragging issues when using multiple fingers on mobile devices (#79).
- **Custom Theme Sections**: Fixed background color rendering bugs on Section elements within custom themes.
- **Viewport Frame Fix**: Corrected touch pinch-zoom calculation when gestures originate outside viewport boundaries.
- **Colorpicker Fixes**: Resolved slider thumb calculation errors in color selection dialogs.

---

## Contributing & Building

WindUI uses [Darklua](https://darklua.com) for bundling and transformation.

```bash
git clone https://github.com/Footagesus/WindUI.git
cd WindUI
cargo install darklua
npm install
npm run build
```
Compiled bundle is output to `dist/main.lua`.

---

## FAQ

#### How do I lock UI elements dynamically?
Call `:Lock()` and `:Unlock()` directly on the element object:
```luau
local Button = Tab:Button({ Title = "Restricted Action" })
Button:Lock()
task.wait(5)
Button:Unlock()
```

#### How do I hide the mobile reopen button?
Set `OpenButton.Enabled = false` in `CreateWindow`:
```luau
OpenButton = {
    Enabled = false,
}
```
Or update it at runtime:
```luau
Window:EditOpenButton({ Enabled = false })
```

#### Why are images failing to load?
Ensure you are using valid image asset IDs (`rbxassetid://<id>`) or direct image file URLs (`https://`). Roblox Decal IDs cannot be rendered directly in `ImageLabel` elements without conversion.
