# WindUI Documentation

Source: https://footagesus.github.io/treehub-web/docs/windui
Upstream Docs: https://footagesus-windui.mintlify.app
Repository: https://github.com/Footagesus/WindUI

This document is a consolidated Markdown reference for WindUI, a modern, open-source Luau UI library for Roblox script hubs. It is based on the official WindUI documentation from the treehub-web and Mintlify documentation sites, as well as the upstream repository source code. Use this reference only in environments where you have permission to run client-side Luau code, such as your own Roblox experiences, private test places, or controlled research environments.

## Overview

WindUI provides window creation, theming, tabs, tab sections, dialogs, popups, tags, notifications, dividers, spacing, form elements, code blocks, paragraphs, sections, image display, progress bars, stack layouts (HStack & VStack), tooltips, config saving, localization, icons, and an optional key system. The current release is `1.6.66` Beta.

The API examples assume `WindUI` has already been loaded via `loadstring(game:HttpGet(...))()`, and that UI elements are created from a `Window`, `Tab`, `Section`, `HStack`, or related parent container.

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
- [Toggle](#toggle)
- [Slider](#slider)
- [Input](#input)
- [Dropdown](#dropdown)
- [Keybind](#keybind)
- [Colorpicker](#colorpicker)
- [Code](#code)
- [Paragraph](#paragraph)
- [Section](#section)
- [Image](#image)
- [HStack & VStack](#hstack--vstack)
- [ProgressBar](#progressbar)
- [Group (deprecated)](#group-deprecated)

### Features & Utilities
- [Tooltip](#tooltip)
- [Acrylic & Transparency](#acrylic--transparency)
- [Localization](#localization)
- [Configs](#configs)
- [Icons](#icons)
- [Fork & Development](#fork--development)
- [FAQ](#faq)

---

## About WindUI

WindUI is a modern, themeable, and open-source Luau UI library designed for Roblox script hubs. It provides a customizable interface toolkit with windows, tabs, dialogs, popups, notifications, form elements, acrylic frosted glass effects, localization, config saving, and an optional key system.

Key characteristics:
- Modern design with smooth tween transitions
- 15+ built-in themes and customizable palettes with gradient support
- Comprehensive UI controls including buttons, toggles, sliders, dropdowns, inputs, colorpickers, and keybinds
- Advanced layout containers (HStack, VStack, collapsible sections)
- Built-in key system with static, multi-key, file-persisted, validator, and external API verification
- Dynamic localization and automatic config persistence

WindUI is in active Beta. Method signatures and configuration keys may change between releases.

---

## Load WindUI

Load WindUI using `loadstring` and `game:HttpGet`.

### Load methods

#### Selected version (Recommended for stability)
```luau
local Version = "1.6.66"
local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/download/" .. Version .. "/main.lua"))()
```

#### Latest release
```luau
local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()
```

#### Development build (Unstable / dist)
```luau
local WindUI = loadstring(game:HttpGet("https://raw.githubusercontent.com/Footagesus/WindUI/main/dist/main.lua"))()
```

### Library Configuration & Methods

#### Set Notification Lower
Positions notifications lower to avoid overlapping mobile jump buttons:
```luau
WindUI:SetNotificationLower(true)
```

#### Set Custom Font
Applies a custom font across the entire interface:
```luau
WindUI:SetFont("rbxassetid://font-id-here")
```

#### Set Parent GUI
Sets the GUI parent container:
```luau
WindUI:SetParent(gethui and gethui() or game:GetService("CoreGui"))
```

#### Toggle Acrylic Blur
Enables or disables the acrylic blur background effect:
```luau
WindUI:ToggleAcrylic(true)
```

#### Get Themes
Returns an array of all registered theme names:
```luau
local themes = WindUI:GetThemes()
```

#### Get Current Theme
Returns the name of the currently active theme:
```luau
local currentTheme = WindUI:GetCurrentTheme()
```

#### Get Window Transparency
Returns the base transparency value applied to the window:
```luau
local transparency = WindUI:GetTransparency()
```

#### Get Window Size
Returns the base `UDim2` size of the window:
```luau
local windowSize = WindUI:GetWindowSize()
```

#### Send Notification
```luau
WindUI:Notify({
    Title = "Notification Title",
    Content = "Notification message content",
    Duration = 3,
    Icon = "bell",
})
```

#### Open Popup
```luau
WindUI:Popup({
    Title = "Notice",
    Icon = "info",
    Content = "Important message for the user.",
    Buttons = {
        {
            Title = "Cancel",
            Variant = "Tertiary",
            Callback = function() end,
        },
        {
            Title = "Continue",
            Icon = "arrow-right",
            Variant = "Primary",
            Callback = function() end,
        },
    },
})
```

---

## Themes

WindUI includes 16 built-in themes and full support for custom theme registration and runtime switching.

### Built-in Themes
- `Dark` (default)
- `Light`
- `Rose`
- `Plant`
- `Red`
- `Indigo`
- `Sky`
- `Violet`
- `Amber`
- `Emerald`
- `Midnight`
- `Crimson`
- `MonokaiPro`
- `CottonCandy`
- `Mellowsi`
- `Rainbow`

### Switching Themes

#### In `CreateWindow`
```luau
local Window = WindUI:CreateWindow({
    Title = "My Hub",
    Theme = "Dark",
})
```

#### At runtime
```luau
WindUI:SetTheme("Indigo")
```

### Creating Custom Themes

#### Minimalist Theme
```luau
WindUI:AddTheme({
    Name = "CustomDark",
    Accent = Color3.fromHex("#18181b"),
    Background = Color3.fromHex("#101010"),
    Outline = Color3.fromHex("#FFFFFF"),
    Text = Color3.fromHex("#FFFFFF"),
    Placeholder = Color3.fromHex("#7a7a7a"),
    Button = Color3.fromHex("#52525b"),
    Icon = Color3.fromHex("#a1a1aa"),
})
```

#### Advanced Theme
```luau
WindUI:AddTheme({
    Name = "AdvancedCustom",
    Primary = Color3.fromHex("#0091FF"),
    White = Color3.new(1, 1, 1),
    Black = Color3.new(0, 0, 0),

    Accent = Color3.fromHex("#18181b"),
    Background = Color3.fromHex("#101010"),
    BackgroundTransparency = 0,
    Hover = Color3.fromHex("#FFFFFF"),

    Outline = Color3.fromHex("#FFFFFF"),
    Text = Color3.fromHex("#FFFFFF"),
    Placeholder = Color3.fromHex("#7a7a7a"),
    Button = Color3.fromHex("#52525b"),
    Icon = Color3.fromHex("#a1a1aa"),

    PanelBackground = Color3.new(1, 1, 1),
    PanelBackgroundTransparency = 0.95,

    WindowBackground = Color3.fromHex("#101010"),
    WindowShadow = Color3.new(0, 0, 0),

    WindowTopbarTitle = Color3.fromHex("#FFFFFF"),
    WindowTopbarAuthor = Color3.fromHex("#FFFFFF"),
    WindowTopbarIcon = Color3.fromHex("#a1a1aa"),
    WindowTopbarButtonIcon = Color3.fromHex("#a1a1aa"),
    WindowSearchBarBackground = Color3.fromHex("#101010"),

    TabBackground = Color3.fromHex("#FFFFFF"),
    TabTitle = Color3.fromHex("#FFFFFF"),
    TabIcon = Color3.fromHex("#a1a1aa"),

    ElementBackground = Color3.fromHex("#2A2A2C"),
    ElementBackgroundTransparency = 0,
    ElementTitle = Color3.fromHex("#FFFFFF"),
    ElementDesc = Color3.fromHex("#FFFFFF"),
    ElementIcon = Color3.fromHex("#a1a1aa"),

    Toggle = Color3.fromHex("#33C759"),
    ToggleBar = Color3.fromHex("#FFFFFF"),
    Checkbox = Color3.fromHex("#0091FF"),
    CheckboxIcon = Color3.fromHex("#FFFFFF"),
    Slider = Color3.fromHex("#0091FF"),
    SliderThumb = Color3.fromHex("#FFFFFF"),

    Dialog = Color3.fromHex("#18181b"),
    DialogBackground = Color3.fromHex("#101010"),
    DialogBackgroundTransparency = 0,
    DialogTitle = Color3.fromHex("#FFFFFF"),
    DialogContent = Color3.fromHex("#FFFFFF"),
    DialogIcon = Color3.fromHex("#a1a1aa"),

    PopupBackground = Color3.fromHex("#101010"),
    PopupBackgroundTransparency = 0,
    PopupTitle = Color3.fromHex("#FFFFFF"),
    PopupContent = Color3.fromHex("#FFFFFF"),
    PopupIcon = Color3.fromHex("#a1a1aa"),

    Tooltip = Color3.fromHex("#18181b"),
})
```

#### Gradients
Theme tokens support gradients via `WindUI:Gradient()`:
```luau
WindUI:AddTheme({
    Name = "GradientTheme",
    Accent = WindUI:Gradient({
        ["0"] = { Color = Color3.fromHex("#1f1f23"), Transparency = 0 },
        ["100"] = { Color = Color3.fromHex("#18181b"), Transparency = 0 },
    }, {
        Rotation = 0,
        Offset = Vector2.new(0, 0),
    }),
})
```

---

## Window

The Window serves as the root container for all tabs and UI components.

### Creating a Window

#### Comprehensive Example
```luau
local Window = WindUI:CreateWindow({
    Title = "My Hub",
    Icon = "door-open",
    Author = "by Developer",
    Folder = "MyHubData",

    Size = UDim2.fromOffset(580, 460),
    MinSize = Vector2.new(560, 350),
    MaxSize = Vector2.new(850, 560),
    Position = UDim2.new(0.5, 0, 0.5, 0),

    Transparent = true,
    Theme = "Dark",
    Resizable = true,
    SideBarWidth = 200,
    HideSearchBar = true,
    ScrollBarEnabled = false,
    Acrylic = false,
    NewElements = true,
    AutoScale = true,

    BackgroundImageTransparency = 0.42,
    -- Background can be "rbxassetid://", a raw image URL, "video:path", or a WindUI:Gradient table

    Topbar = {
        Height = 44,
        ButtonsType = "Default", -- "Default" or "Mac"
    },

    OpenButton = {
        Title = "Open UI",
        Icon = "monitor",
        CornerRadius = UDim.new(0, 16),
        StrokeThickness = 2,
        Enabled = true,
        Draggable = true,
        OnlyMobile = false,
        Scale = 1,
        Color = ColorSequence.new(
            Color3.fromHex("#30FF6A"),
            Color3.fromHex("#e7ff2f")
        ),
    },

    User = {
        Enabled = true,
        Anonymous = false,
        Callback = function()
            print("User profile clicked")
        end,
    },
})
```

### Window Parameters

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Title` | `string` | **Required** | Title displayed in the window topbar |
| `Icon` | `string?` | `nil` | Icon name (Lucide, Craft, Geist, Solar), `rbxassetid://`, or URL |
| `Author` | `string?` | `nil` | Subtitle/author displayed below the title |
| `Folder` | `string?` | `nil` | Root folder name for config saving and key persistence |
| `Size` | `UDim2?` | `UDim2.fromOffset(580, 460)` | Default window size |
| `MinSize` | `Vector2?` | `Vector2.new(560, 350)` | Minimum resize dimensions |
| `MaxSize` | `Vector2?` | `Vector2.new(850, 560)` | Maximum resize dimensions |
| `Position` | `UDim2?` | `UDim2.new(0.5, 0, 0.5, 0)` | Starting window screen position |
| `Transparent` | `boolean?` | `false` | Enables window background transparency |
| `Theme` | `string?` | `"Dark"` | Initial theme name |
| `Resizable` | `boolean?` | `true` | Allows users to drag-resize the window |
| `SideBarWidth` | `number?` | `200` | Width of sidebar containing tabs |
| `HideSearchBar` | `boolean?` | `true` | Hides the search bar in the tab sidebar |
| `ScrollBarEnabled` | `boolean?` | `false` | Enables visible scrollbars on scrollable content |
| `BackgroundImageTransparency` | `number?` | `0` | Transparency of background image (0 to 1) |
| `Background` | `string \| table?` | `nil` | `rbxassetid://`, raw image URL, `video:`, or `WindUI:Gradient()` table |
| `ShadowTransparency` | `number?` | `0.7` | Transparency of window drop shadow |
| `Radius` | `number?` | `16` | Corner radius of the window frame |
| `ElementsRadius` | `number?` | `nil` | Border radius for UI elements inside window |
| `TopBarButtonIconSize` | `number?` | `16` | Pixel size of topbar button icons |
| `ToggleKey` | `Enum.KeyCode?` | `nil` | Keyboard key to toggle window visibility |
| `Acrylic` | `boolean?` | `false` | Enables frosted glass background blur effect |
| `NewElements` | `boolean?` | `false` | Uses newer UI element styling |
| `IgnoreAlerts` | `boolean?` | `false` | Skips confirmation dialog when closing |
| `HidePanelBackground` | `boolean?` | `false` | Hides background panel in main content area |
| `AutoScale` | `boolean?` | `true` | Automatically scales window to fit viewport |
| `Topbar` | `table?` | `nil` | Topbar options (`Height`, `ButtonsType` = `"Default"` or `"Mac"`) |
| `OpenButton` | `table?` | `nil` | Mobile/desktop minimize reopen button options |
| `User` | `table?` | `nil` | User profile section in bottom-left (`Enabled`, `Anonymous`, `Callback`) |
| `KeySystem` | `table?` | `nil` | Key system gating configuration |

### Window Methods

#### Containers & Elements
- `Window:Tab(TabConfig)`: Creates a new tab.
- `Window:Section(SectionConfig)`: Creates a tab section in the sidebar.
- `Window:Divider()`: Inserts a visual divider in the sidebar.
- `Window:Tag(TagConfig)`: Adds a badge/tag in the topbar.
- `Window:Dialog(DialogConfig)`: Opens a modal dialog.

#### Topbar Controls
- `Window:CreateTopbarButton(Name, Icon, Callback, LayoutOrder?, IconThemed?, Color?, IconSize?)`: Adds a custom button to the window topbar.
- `Window:DisableTopbarButtons(ButtonsTable)`: Disables specific topbar buttons.
- `Window:EditOpenButton(Config)`: Updates the OpenButton properties.

#### Window State & Appearance
- `Window:Open()`: Opens the window with animation.
- `Window:Close()`: Closes the window with animation.
- `Window:Toggle()`: Toggles window visibility.
- `Window:Destroy()`: Destroys the window GUI.
- `Window:SetTitle(Title)`: Dynamically updates the window title.
- `Window:SetAuthor(Author)`: Updates the author/subtitle text.
- `Window:SetIcon(Icon)`: Updates the window icon.
- `Window:SetIconSize(Size)`: Changes the window icon size.
- `Window:SetSize(UDim2)`: Updates window size.
- `Window:SetToggleKey(KeyCode)`: Updates the visibility toggle key.
- `Window:SetTheme(ThemeName)`: Changes the window theme.
- `Window:SetBackgroundImage(ImageId)`: Sets background image.
- `Window:SetBackgroundImageTransparency(Number)`: Sets background image transparency.
- `Window:SetBackgroundTransparency(Number)`: Sets window background transparency.
- `Window:ToggleTransparency(Boolean)`: Toggles transparency mode.
- `Window:SetPanelBackground(Visible)`: Shows or hides the main panel background.
- `Window:SetUIScale(Scale)`: Sets UI scale factor.
- `Window:GetUIScale()`: Returns current UI scale factor.
- `Window:IsResizable(Boolean)`: Sets whether the window can be resized.
- `Window:SetToTheCenter()`: Centers window on screen.

#### Bulk Element Operations
- `Window:UnlockAll()`: Unlocks all locked elements.
- `Window:LockAll()`: Locks all elements in the window.
- `Window:GetAll()`: Returns a table of all elements.
- `Window:GetLocked()`: Returns a table of all locked elements.
- `Window:GetUnlocked()`: Returns a table of all unlocked elements.

#### Lifecycle Callbacks
- `Window:OnOpen(Callback)`: Fires when the window opens.
- `Window:OnClose(Callback)`: Fires when the window closes.
- `Window:OnDestroy(Callback)`: Fires when the window is destroyed.

---

## Key System

The Key System gates access to the window behind key validation. When enabled, WindUI renders a modal key dialog and pauses window creation until a valid key is provided.

### Configuration Example
```luau
local Window = WindUI:CreateWindow({
    Title = "Admin Hub",
    Folder = "MyHubData", -- required when SaveKey is enabled

    KeySystem = {
        Title = "Access Required",
        Note = "Join our Discord to obtain a key.",
        URL = "https://discord.gg/example",
        SaveKey = true,

        KeyValidator = function(enteredKey)
            return enteredKey == "supersecretkey"
        end,

        Thumbnail = {
            Image = "rbxassetid://1234567890",
            Title = "Premium Access",
            Width = 200,
        },
    },
})
```

### Key System Parameters

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `KeyValidator` | `function?` | Custom validator function: `function(key) return key == "valid" end`. Recommended. |
| `Key` | `string \| table?` | Accepted key string or array of strings. Deprecated in favor of `KeyValidator`. |
| `SaveKey` | `boolean?` | Persists valid key to `<Folder>/<hwid>.key` for subsequent launches. |
| `Title` | `string?` | Title displayed in the key dialog. Defaults to window title. |
| `Note` | `string?` | Informational note displayed to users (supports RichText). |
| `URL` | `string?` | Link copied to clipboard when the user clicks "Get Key". |
| `Thumbnail` | `table?` | Decorative image panel (`Image`, `Title`, `Width`). |
| `API` | `table?` | Third-party key verification services configuration. |

### Validation Flow & SaveKey
1. If `SaveKey = true` and `<Folder>/<hwid>.key` exists on disk, WindUI silently validates the saved key. If valid, the window opens immediately without showing the dialog.
2. Otherwise, the key modal dialog is presented.
3. Upon entering a valid key, it is written to `<Folder>/<hwid>.key` and the window opens.
4. If invalid, an error notification is shown and the dialog remains open.

### Third-Party Key Services (API)

WindUI supports external key systems.

#### Panda Development
The validation endpoint is updated to the current Pandauth API:
```luau
KeySystem = {
    API = {
        Type = "PandaDevelopment",
        Service = "your_service_id",
    },
}
```
Validation URL: `https://api.pandauth.com/api/v1/keys/validate`

#### Platoboost
```luau
KeySystem = {
    API = {
        Type = "Platoboost",
        Service = "your_service_id",
    },
}
```

---

## Tab

Tabs organize content into separate navigable pages in the sidebar.

### Creating a Tab
```luau
local Tab = Window:Tab({
    Title = "Combat",
    Desc = "Combat and aim settings",
    Icon = "swords",
    IconColor = Color3.fromRGB(255, 100, 100),
    IconShape = "Square", -- "Square" or "Circle"
    IconThemed = false,
    Locked = false,
    ShowTabTitle = true,
    TabTitleAlign = "Left", -- "Left", "Center", or "Right"
    Border = true,

    CustomEmptyPage = {
        Icon = "lucide:smile",
        IconSize = 48,
        Title = "No Features Here",
        Desc = "This tab currently has no elements.",
    },
})
```

### Tab Parameters

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Title` | `string` | `"Tab"` | Tab title displayed in the sidebar |
| `Desc` | `string?` | `nil` | Description shown as a hover tooltip in the sidebar |
| `Icon` | `string?` | `nil` | Lucide icon name, `rbxassetid://`, or image URL |
| `IconColor` | `Color3?` | `nil` | Custom color for the tab icon |
| `IconShape` | `string?` | `nil` | Icon shape: `"Square"` or `"Circle"` |
| `IconThemed` | `boolean?` | `false` | Whether to apply theme colors to the icon |
| `Locked` | `boolean?` | `false` | Prevents user interaction with the tab when true |
| `ShowTabTitle` | `boolean?` | `false` | Displays the tab title at the top of the content page |
| `TabTitleAlign` | `string?` | `"Left"` | Title alignment when `ShowTabTitle` is enabled (`"Left"`, `"Center"`, `"Right"`) |
| `Border` | `boolean?` | `false` | Adds a visual outline border around the tab container |
| `CustomEmptyPage` | `table?` | `nil` | Custom empty page display (`Icon`, `IconSize`, `Title`, `Desc`) |

### Tab Methods
- `Tab:Select()`: Selects and opens the tab.
- `Tab:SetTitle(Title)`: Updates tab title.
- `Tab:SetDesc(Description)`: Updates tab description tooltip.
- `Tab:Lock()`: Disables tab interaction.
- `Tab:Unlock()`: Enables tab interaction.
- `Tab:ScrollToTheElement(Index)`: Scrolls the tab content directly to the target element index.

---

## Tab Section

Tab sections group multiple tabs together under a labeled category in the sidebar.

```luau
local TabSection = Window:Section({
    Title = "Settings & Config",
    Icon = "settings",
    Opened = true,
})

local GeneralTab = TabSection:Tab({ Title = "General", Icon = "sliders" })
local AdvancedTab = TabSection:Tab({ Title = "Advanced", Icon = "terminal" })
```

### Tab Section Parameters

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Title` | `string` | `"Section"` | Category title in sidebar |
| `Icon` | `string?` | `nil` | Icon for section header |
| `IconThemed` | `boolean?` | `false` | Applies theme color to the icon |
| `Opened` | `boolean?` | `true` | Whether section is expanded by default |

---

## Dialog

Dialogs are modal prompt overlays that block interaction with the rest of the window until dismissed.

```luau
Window:Dialog({
    Title = "Reset Settings",
    Content = "Are you sure you want to reset all configurations?",
    Icon = "alert-triangle",
    IconThemed = true,
    Buttons = {
        {
            Title = "Confirm",
            Icon = "check",
            Variant = "Destructive", -- "Primary", "Secondary", "Tertiary", "Destructive"
            Callback = function()
                print("Reset confirmed")
            end,
        },
        {
            Title = "Cancel",
            Variant = "Secondary",
            Callback = function()
                print("Reset cancelled")
            end,
        },
    },
})
```

---

## Popup

Popups are standalone floating modal windows for notices, announcements, or multi-button user prompts.

```luau
WindUI:Popup({
    Title = "Welcome to v1.6.66",
    Icon = "sparkles",
    Content = "Thank you for using our hub. Check out the changelog for new features!",
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

---

## Tag

Tags are badges placed in the window topbar for version labels, statuses, or build channels.

```luau
local VersionTag = Window:Tag({
    Title = "v1.6.66",
    Color = Color3.fromHex("#0091FF"),
    Radius = 6,
})

-- Update or destroy
VersionTag:SetTitle("v1.6.66-PRO")
-- VersionTag:Destroy()
```

---

## Notification

Toast notifications slide onto the screen and automatically dismiss after a duration.

```luau
WindUI:Notify({
    Title = "Configuration Saved",
    Content = "Your settings have been saved to storage.",
    Icon = "check-circle",
    Duration = 4,
})
```

---

## Divider

Creates a visual separator line between UI elements in a tab or in the sidebar.

```luau
Tab:Divider()
-- or
Window:Divider()
```

---

## Space

Adds vertical spacing between elements.

```luau
Tab:Space({
    Size = 12,
})
```

---

## Button

Clickable action button with icon, color tint, alignment, description, and locked state support.

```luau
local Button = Tab:Button({
    Title = "Execute Script",
    Desc = "Runs the primary automation loop",
    Icon = "play",
    Color = Color3.fromHex("#305dff"),
    Justify = "Between", -- "Left", "Center", "Right", "Between"
    IconAlign = "Right", -- "Left" or "Right"
    Locked = false,
    LockedTitle = "Locked",
    Callback = function()
        print("Action executed")
    end,
})

-- Visual sweep animation
Button:Highlight()

-- Methods
Button:SetTitle("New Title")
Button:SetDesc("Updated description")
Button:Lock()
Button:Unlock()
Button:Destroy()
```

---

## Toggle

Binary on/off control with smooth animations, switch or checkbox rendering, and config saving.

```luau
local Toggle = Tab:Toggle({
    Title = "Auto Farm",
    Desc = "Automatically collects resources",
    Icon = "power",
    Value = false,
    Type = "Toggle", -- "Toggle" or "Checkbox"
    Locked = false,
    Flag = "auto_farm",
    Callback = function(state)
        print("Auto Farm:", state)
    end,
})

-- Methods
Toggle:SetValue(true)
Toggle:SetTitle("Auto Farm (Active)")
Toggle:SetDesc("Running in background")
Toggle:Lock()
Toggle:Unlock()
Toggle:Destroy()
```

---

## Slider

Numeric input slider with support for decimals, customizable width, tooltips, textbox entry, and range icons.

```luau
local Slider = Tab:Slider({
    Title = "WalkSpeed",
    Desc = "Adjust player movement speed",
    Value = {
        Min = 16,
        Max = 150,
        Default = 16,
    },
    Step = 1,
    Width = 160,
    IsTooltip = true, -- Displays value in a floating tooltip above thumb while dragging
    IsTextbox = true, -- Allows manual numeric entry
    Locked = false,
    Flag = "player_walkspeed",
    Icons = {
        From = "footprints",
        To = "zap",
    },
    Callback = function(value)
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = value
    end,
})

-- Methods
Slider:SetValue(32)
Slider:SetMin(16)
Slider:SetMax(200)
Slider:SetTitle("Movement Speed")
Slider:SetDesc("Current speed modified")
Slider:Lock()
Slider:Unlock()
Slider:Destroy()
```

---

## Input

Single-line text field or multi-line textarea with placeholder text, icon, and config integration.

```luau
local Input = Tab:Input({
    Title = "Target Player",
    Desc = "Enter username or display name",
    Icon = "user",
    Type = "Default", -- "Default" (single-line) or "Textarea" (multi-line)
    Placeholder = "Username...",
    Value = "",
    Locked = false,
    Flag = "target_player",
    Callback = function(text)
        print("Target set to:", text)
    end,
})

-- Methods
Input:SetValue("RobloxUser")
Input:SetPlaceholder("Type username here...")
Input:SetTitle("Selected Player")
Input:Lock()
Input:Unlock()
Input:Destroy()
```

---

## Dropdown

Select menu supporting single-choice, multi-choice, search filtering, dividers, and custom option items.

```luau
local Dropdown = Tab:Dropdown({
    Title = "Teleport Location",
    Desc = "Select destination point",
    Values = {
        "Spawn",
        "Shop",
        "Arena",
        "Bank",
    },
    Value = "Spawn",
    Multi = false,
    AllowNone = false,
    SearchBarEnabled = true,
    Locked = false,
    Flag = "teleport_destination",
    Callback = function(selected)
        print("Selected location:", selected)
    end,
})

-- Advanced rich items with icons and dividers
local ActionDropdown = Tab:Dropdown({
    Title = "Item Actions",
    Values = {
        { Title = "Use Item", Desc = "Consumes the item", Icon = "sparkles", Callback = function() end },
        { Title = "Drop Item", Desc = "Drops onto ground", Icon = "arrow-down", Callback = function() end },
        { Type = "Divider" },
        { Title = "Delete Item", Desc = "Permanently removes", Icon = "trash", Callback = function() end },
    },
})

-- Methods
Dropdown:Select("Shop") -- or {"Spawn", "Arena"} when Multi = true
Dropdown:Refresh({ "New Location 1", "New Location 2" })
Dropdown:SetTitle("Updated Locations")
Dropdown:Lock()
Dropdown:Unlock()
Dropdown:Highlight()
Dropdown:Destroy()
```

---

## Keybind

Interactive keybind listener supporting all Roblox `KeyCode` values.

```luau
local Keybind = Tab:Keybind({
    Title = "Fly Toggle",
    Desc = "Press key to toggle flying",
    Value = "E", -- Enum.KeyCode name or key string
    Locked = false,
    Flag = "fly_keybind",
    Callback = function(key)
        print("Fly keybind pressed:", key)
    end,
})

-- Methods
Keybind:SetValue("F")
Keybind:SetTitle("Fly Key")
Keybind:Lock()
Keybind:Unlock()
Keybind:Destroy()
```

---

## Colorpicker

Color selection component opening an HSV picker dialog with optional transparency.

```luau
local Colorpicker = Tab:Colorpicker({
    Title = "ESP Color",
    Desc = "Color of the player highlight box",
    Default = Color3.fromRGB(255, 0, 100),
    Locked = false,
    Flag = "esp_box_color",
    Callback = function(color)
        print("ESP color set to:", color)
    end,
})

-- Methods
Colorpicker:SetValue(Color3.fromRGB(0, 255, 120))
Colorpicker:SetTitle("Box Color")
Colorpicker:Lock()
Colorpicker:Unlock()
Colorpicker:Destroy()
```

---

## Code

Formatted code block display with syntax highlighting, custom theme token coloring, copy callback, and adjustable dimensions.

```luau
local CodeBlock = Tab:Code({
    Title = "Generated Script",
    Code = [[local Player = game.Players.LocalPlayer
print("Hello, " .. Player.Name)]],
    CodeSize = 18,
    CanCopied = true,
    Height = UDim.new(0, 200), -- or UDim.new(1, 0) for full page height

    CodeTheme = {
        numbers = Color3.fromHex("#FAB387"),
        boolean = Color3.fromHex("#FAB387"),
        operator = Color3.fromHex("#94E2D5"),
        lua = Color3.fromHex("#CBA6F7"),
        rbx = Color3.fromHex("#F38BA8"),
        str = Color3.fromHex("#A6E3A1"),
        comment = Color3.fromHex("#9399B2"),
        null = Color3.fromHex("#F38BA8"),
        call = Color3.fromHex("#89B4FA"),
        self_call = Color3.fromHex("#89B4FA"),
        local_property = Color3.fromHex("#CBA6F7"),
    },

    OnCopy = function()
        print("Code copied to clipboard!")
    end,
})

-- Methods
CodeBlock:SetCode("print('Updated code')")
CodeBlock:Destroy()
```

---

## Paragraph

Formatted text card with optional small icon, large thumbnail image, accent colors, and action buttons.

```luau
local Paragraph = Tab:Paragraph({
    Title = "Changelog v1.6.66",
    Desc = "Added ProgressBar element, updated Code parameters, and fixed Dropdown.Locked behavior.",
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

-- Methods
Paragraph:SetTitle("New Announcement")
Paragraph:SetDesc("Updated description text.")
Paragraph:SetImage("bell", 20)
Paragraph:SetThumbnail("rbxassetid://987654321", 48)
Paragraph:Destroy()
```

---

## Section

Collapsible or boxed grouping container within a tab page.

```luau
local Section = Tab:Section({
    Title = "Combat Features",
    Desc = "Aimbot and triggerbot configuration",
    Icon = "crosshair",
    IconColor = Color3.fromRGB(255, 100, 100),
    TextSize = 19,
    TextXAlignment = "Left", -- "Left", "Center", or "Right"
    Box = true, -- Adds an enclosed frame around the section
    BoxBorder = true,
    Opened = true, -- Expanded by default
    FontWeight = Enum.FontWeight.SemiBold,
    DescFontWeight = Enum.FontWeight.Medium,
    TextTransparency = 0.05,
    DescTextTransparency = 0.4,
})

-- Add elements directly into section
Section:Toggle({ Title = "Aimbot Enabled" })
Section:Slider({ Title = "FOV Radius", Value = { Min = 10, Max = 300, Default = 90 } })

-- Methods
Section:SetTitle("Weapon Configuration")
Section:Destroy()
```

---

## Image

Displays visual content from asset IDs (`rbxassetid://`) or raw HTTPS image URLs.

```luau
Tab:Image({
    Image = "https://example.com/preview.png",
    AspectRatio = "16:9", -- "16:9", "1:1", "4:3", etc.
    Radius = 12,
})
```

---

## HStack & VStack

Modern stack layout containers that arrange child elements horizontally (`HStack`) or vertically (`VStack`). Replaces the deprecated `Group` element for building multi-column layouts.

```luau
-- Create a horizontal row
local Row = Tab:HStack({
    AutoSpace = true, -- Automatically spaces child elements
})

-- Split into two vertical columns side by side
local LeftCol = Row:VStack()
local RightCol = Row:VStack()

-- Add elements to left column
LeftCol:Button({ Title = "Option 1" })
LeftCol:Toggle({ Title = "Feature A" })

-- Add elements to right column
RightCol:Button({ Title = "Option 2" })
RightCol:Toggle({ Title = "Feature B" })
```

---

## ProgressBar

Creates a determinate or indeterminate progress bar to visualize loading states, level progress, or numeric thresholds. Added in WindUI `1.6.66`.

### Determinate Progress Bar
```luau
local Bar = Tab:ProgressBar({
    Title = "Downloading Assets",
    Desc = "Caching remote assets to storage",
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

-- Update value
Bar:Set(75)

-- Read values
local current = Bar:Get()
local pct = Bar:GetPercentage()
```

### Indeterminate Progress Bar
```luau
local LoadingBar = Tab:ProgressBar({
    Title = "Syncing Database",
    Desc = "Please wait...",
    Indeterminate = true,
    IndeterminateText = "Connecting...",
    Speed = 1.2,
})
```

### Custom Value Formatter
```luau
local StorageBar = Tab:ProgressBar({
    Title = "Storage Used",
    Value = { Min = 0, Max = 1000, Default = 450 },
    DisplayMode = "Value",
    Format = function(value, percentage, min, max)
        return string.format("%d MB / %d MB", value, max)
    end,
})
```

### ProgressBar Parameters

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `Title` | `string` | `"Progress"` | Label displayed above the progress bar |
| `Desc` | `string?` | `nil` | Description text below title |
| `Value.Min` | `number` | `0` | Minimum range value |
| `Value.Max` | `number` | `100` | Maximum range value |
| `Value.Default` | `number` | `Min` | Initial progress value |
| `ShowValue` | `boolean?` | `not Indeterminate` | Whether value text is visible |
| `DisplayMode` | `string?` | `"Percent"` | Display format: `"Percent"`, `"Value"`, or `"Fraction"` |
| `Format` | `function?` | `nil` | Custom text formatter `function(val, pct, min, max)` |
| `Animate` | `boolean?` | `true` | Enables tweened fill transitions |
| `AnimationDuration`| `number?` | `0.15` | Fill animation duration in seconds |
| `Indeterminate` | `boolean?` | `false` | Enables looping loading animation |
| `IndeterminateText`| `string?` | `""` | Text shown during indeterminate state |
| `Speed` | `number?` | `1` | Animation speed for indeterminate mode |
| `ControlGap` | `number?` | `16` | Spacing between bar and value text |
| `Width` | `number?` | `160` | Total width of the progress bar container |
| `ValueWidth` | `number?` | `44` | Width allocated for the value label |

### ProgressBar Methods
- `Bar:Set(Number)`: Sets current progress value.
- `Bar:Get()`: Returns current value.
- `Bar:GetPercentage()`: Returns progress percentage (0 to 100).
- `Bar:SetRange(Min, Max)`: Updates both minimum and maximum bounds.
- `Bar:SetMin(Min)`: Updates minimum bound.
- `Bar:SetMax(Max)`: Updates maximum bound.

---

## Group (deprecated)

> [!WARNING]
> `Group` is officially deprecated in WindUI. Use [HStack & VStack](#hstack--vstack) instead for multi-element layouts.

```luau
local ActionGroup = Tab:Group({})
ActionGroup:Button({ Title = "Save", Icon = "save", Justify = "Center" })
ActionGroup:Button({ Title = "Delete", Icon = "trash", Justify = "Center" })
```

---

## Tooltip

Tooltips render in a dedicated top-level `ScreenGui` layer (`WindUI/Tooltips`), ensuring they float above all other windows.

### Usage in Sliders & Tabs
- In **Sliders**: Set `IsTooltip = true` to show a live numeric tooltip above the thumb while dragging.
- In **Tabs**: Set `Desc = "Description text"` to display a tooltip when hovering over the tab in the sidebar.

```luau
Tab:Slider({
    Title = "Smooth Aim",
    IsTooltip = true,
    Value = { Min = 1, Max = 10, Default = 5 },
})
```

---

## Acrylic & Transparency

WindUI supports frosted-glass acrylic blur and background transparency.

### Acrylic Blur
Acrylic blur places a 3D `Part` behind the GUI combined with a `DepthOfFieldEffect` in `Lighting` (`FarIntensity = 0`, `NearIntensity = 1`, `InFocusRadius = 0.1`).

```luau
local Window = WindUI:CreateWindow({
    Title = "Frosted Hub",
    Acrylic = true,
})

-- Toggle dynamically at runtime
WindUI:ToggleAcrylic(false)
```

> [!NOTE]
> Acrylic requires an executor that supports workspace 3D Part manipulation behind CoreGui/gethui. In environments lacking this support, use `Transparent = true` instead.

---

## Localization

WindUI includes a built-in localization system. When registered, any element title prefixed with `"loc:"` automatically translates to the active language.

```luau
-- Register localization before creating the Window
WindUI:Localization({
    Enabled = true,
    Prefix = "loc:",
    DefaultLanguage = "en",
    Translations = {
        ["en"] = {
            ["TITLE"] = "Automation Hub",
            ["FARM"] = "Auto Farm",
            ["SPEED"] = "Player Speed",
        },
        ["ru"] = {
            ["TITLE"] = "Хаб Автоматизации",
            ["FARM"] = "Авто Фарм",
            ["SPEED"] = "Скорость Игрока",
        },
        ["es"] = {
            ["TITLE"] = "Centro de Automatización",
            ["FARM"] = "Granja Automática",
            ["SPEED"] = "Velocidad del Jugador",
        },
    },
})

local Window = WindUI:CreateWindow({
    Title = "loc:TITLE",
})

local Tab = Window:Tab({ Title = "General" })
Tab:Toggle({ Title = "loc:FARM" })
Tab:Slider({ Title = "loc:SPEED", Value = { Min = 16, Max = 100, Default = 16 } })

-- Switch language dynamically at runtime
WindUI:SetLanguage("es")
```

---

## Configs

WindUI provides automatic preference persistence for all elements equipped with a `Flag` string.

### Configuration Saving Workflow
1. Specify `Folder` in `CreateWindow`:
```luau
local Window = WindUI:CreateWindow({
    Title = "My Script",
    Folder = "MyHubFolder",
})
```
2. Assign unique `Flag` keys to elements:
```luau
Tab:Toggle({ Title = "ESP", Flag = "esp_toggle", Value = false })
Tab:Slider({ Title = "ESP Range", Flag = "esp_range", Value = { Min = 100, Max = 1000, Default = 500 } })
```
3. Save or load configs using `Window.ConfigManager`:
```luau
local Config = Window.ConfigManager:Config("DefaultProfile")

-- Save configuration to disk
Config:Save()

-- Load configuration
Config:Load()
```

Configs are saved as JSON files in `<Folder>/configs/<Profile>.json`.

---

## Icons

WindUI supports Lucide icons by default, as well as multiple icon sets via prefixes, Roblox asset IDs, and direct web URLs.

### Icon Sets and Prefixes

| Icon Set | Prefix / Format | Example |
| :--- | :--- | :--- |
| **Lucide Icons** (default) | `lucide:` or bare name | `"swords"`, `"lucide:home"` |
| **Craft Icons** | `craft:` | `"craft:folder"` |
| **Geist Icons** | `geist:` | `"geist:triangle"` |
| **Solar Icons** | `solar:` | `"solar:bell-bold"` |
| **SF Symbols** | `sfsymbols:` | `"sfsymbols:sunMinFill"` |
| **Roblox Asset ID** | `rbxassetid://` | `"rbxassetid://123456789"` |
| **Direct Image URL** | `https://` | `"https://example.com/icon.png"` |

Browse Lucide icon names at [lucide.dev](https://lucide.dev).

---

## Fork & Development

To modify, build, or contribute to WindUI:

### Setup Local Repository
```bash
git clone https://github.com/Footagesus/WindUI.git
cd WindUI
```

### Install Dependencies
WindUI uses [Darklua](https://darklua.com) for bundling and minification:
```bash
cargo install darklua
npm install
```

### Build WindUI
```bash
npm run build
```
This processes the source code in `src/` and produces the bundled output file at `dist/main.lua`.

---

## FAQ

#### How do I lock UI elements dynamically?
Each interactive element exposes `:Lock()` and `:Unlock()` methods. When locked, user clicks and keystrokes are ignored and a visual overlay is displayed.
```luau
local MyButton = Tab:Button({ Title = "Admin Action" })
MyButton:Lock()
task.wait(5)
MyButton:Unlock()
```

#### How do I hide the mobile OpenButton?
In `CreateWindow`, configure `OpenButton.Enabled = false`:
```luau
OpenButton = {
    Enabled = false,
}
```
Or update it at runtime:
```luau
Window:EditOpenButton({ Enabled = false })
```

#### Why does background image not work?
Ensure you are using a raw image URL or a direct image asset ID (`rbxassetid://<id>`). Roblox Decal IDs will not display directly on GUI ImageLabels without resolving to asset IDs.
