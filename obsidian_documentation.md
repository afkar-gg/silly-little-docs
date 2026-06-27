# Obsidian Documentation

Source: https://docs.mspaint.cc/obsidian

This document is a consolidated Markdown reference for Obsidian, a clean Luau UI library for Roblox script hubs. It is based on the public Obsidian documentation from https://docs.mspaint.cc/obsidian. Use this reference only in environments where you have permission to run client-side Luau code, such as your own Roblox experiences, private test places, or controlled research environments. It is not a guide for bypassing Roblox protections, cheating in public experiences, or interfering with other users.

## Table of Contents

### Introduction

- [Getting Started](#getting-started)
- [Structuring](#structuring)
- [Installation](#installation)
- [Executor](#executor)
- [Roblox Studio](#roblox-studio)
- [Next.js + Tailwind](#nextjs-tailwind)
- [HTML](#html)
- [Contributing](#contributing)

### Addons

- [SaveManager](#savemanager)
- [ThemeManager](#thememanager)

### Library

- [Loading](#loading)
- [Window](#window)
- [Notifications](#notifications)
- [Dialogs](#dialogs)
- [Overlays](#overlays)
- [Keybind Menu](#keybind-menu)
- [Utility](#utility)

### Structure

- [Tabs](#tabs)
- [Groupboxes](#groupboxes)
- [Tabboxes](#tabboxes)
- [Dependency Boxes](#dependency-boxes)
- [Dependency Groupboxes](#dependency-groupboxes)

### UI Elements

- [Label](#label)
- [Button](#button)
- [Toggles](#toggles)
- [Checkboxes](#checkboxes)
- [Inputs](#inputs)
- [Slider](#slider)
- [Dropdown](#dropdown)
- [Keybind](#keybind)
- [Color Picker](#color-picker)
- [Divider](#divider)
- [Viewport](#viewport)
- [Image](#image)
- [Video](#video)
- [UI Passthrough](#ui-passthrough)

---

## Getting Started

![Obsidian](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fobsidian.0bn3p5nrm_77_.png&w=3840&q=75)

* * *

### Introduction

Welcome to the documentation for Obsidian, a clean Roblox UI Library made by deivid for the [mspaint script hub](https://www.mspaint.cc).

_This is why the Library has an API modeled after the[LinoriaLib](https://github.com/mstudio45/linorialib) UI library (as that was the previous UI Library for mspaint)._

### Why Obsidian?

  * Clean and simple UI
  * Full Mobile and PC support
  * Customizable themes
  * Built-in Save Manager
  * Maintained UI Library

### Installation

Obsidian is available to use on **Roblox Studio** , **Executors** and even **Next.js + Tailwind**. Please refer to the [installation guide](https://docs.mspaint.cc/obsidian/installation).

> **Note** : 
> 
> The **Next.js + Tailwind** version of Obsidian may seem different because it is a React replica. If you notice any inconsistencies, feel free to report them.

### Quick Start

First understand the [structure](https://docs.mspaint.cc/obsidian/structure) of Obsidian **(This is important if you are not familiar with LinoriaLib)**

Then create a [window](https://docs.mspaint.cc/obsidian/core/library/Window) and start adding [tabs](https://docs.mspaint.cc/obsidian/structure/tabs) and [groupboxes](https://docs.mspaint.cc/obsidian/structure/groupboxes) to it. If you are less into reading and more into checking code, the Obsidian example script can be found [here](https://github.com/deividcomsono/Obsidian/blob/main/Example.lua).

### Suggestions and Bug Reports

If you have any suggestions or bug reports, please feel free to reach out to us on our [open source discord server variant](https://discord.gg/AZp8cR8DaU). We also read through [issues](https://github.com/deividcomsono/Obsidian/issues) and [pull requests](https://github.com/deividcomsono/Obsidian/pulls) regularly.

---

## Structuring

> **Note** : 
> 
> Obsidian uses a UI Library API similar to [LinoriaLib](https://github.com/mstudio45/linorialib). This means that our LinoriaLib fork and Obsidian are compatible with each other.

To structure your Obsidian UI, you first need to understand the underlying organization of Obsidian.

### Tabs

Tabs are the main way to seperate each category of elements for your UI. Each tab has a name and optionally a description and icon. The tabs show up on the left side of the Obsidian window.

![tab structure](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2FTabStructure.16jcw4oxaov5u.png&w=3840&q=75)

* * *

### Groupboxes & Tabboxes

Groupboxes and tabboxes are organized inside of the tab content area. The tab content area is divided into two columns, left and right. Each column can have multiple groupbox and/or tabbox inside of it.

![groupbox structure](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2FTabContentStructure.013_g3hx-uwbk.png&w=3840&q=75)

Inside each groupbox/tabbox, you can add any UI Element in the UI elements section.

![groupbox elements](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2FGroupboxElements.03s.5yhe44xse.png&w=3840&q=75)

---

## Installation

#### Selecting Installation Environment

Start by selecting in what environment you'd like Obsidian to be shown in. Then follow the instructions to install everything properly.

  * [Executor](https://docs.mspaint.cc/obsidian/installation/executor) - Install Obsidian in an executor environment
  * [Roblox Studio](https://docs.mspaint.cc/obsidian/installation/roblox-studio) - Install Obsidian in Roblox Studio
  * [Next.js + Tailwind](https://docs.mspaint.cc/obsidian/installation/next) - Install Obsidian in a Next.js + Tailwind environment
  * [HTML](https://docs.mspaint.cc/obsidian/installation/html) - Install Obsidian in a vanilla HTML environment

---

## Executor

### Using Obsidian in an Executor Environment

To use Obsidian in an executor environment, you can use the following code snippet to get the library:
```lua
 
    local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/deividcomsono/Obsidian/refs/heads/main/Library.lua"))()
```

Then, you should be good to go! You can start by checking out the [Window](https://docs.mspaint.cc/obsidian/core/library/Window) section to create your first window, tab, groupbox and UI elements.

If you are too lazy to read the documentation, you can also check out the [example script](https://github.com/deividcomsono/Obsidian/blob/main/Example.lua).

### Addons

The library comes with several built-in addons such as [ThemeManager](https://docs.mspaint.cc/obsidian/core/addons/thememanager) and [SaveManager](https://docs.mspaint.cc/obsidian/core/addons/savemanager).

### Types

The library comes with a type definition file for [luau lsp](https://marketplace.visualstudio.com/items?itemName=JohnnyMorganz.luau-lsp) to help with autocompletion.

You can find the type definition file [here](https://raw.githubusercontent.com/deividcomsono/Obsidian/refs/heads/main/Library.d.luau).

---

## Roblox Studio

### Installing Obsidian in Roblox Studio

To install Obsidian in Roblox Studio, you can copy the [Library.lua](https://raw.githubusercontent.com/deividcomsono/Obsidian/refs/heads/main/Library.lua) file into a modulescript and require it.
```lua
 
    local Library = require(game:GetService("ReplicatedStorage"):WaitForChild("Obsidian"))
```

#### Setting up Icons

If you want lucide icon support, save the [lucide icon registry](https://raw.githubusercontent.com/deividcomsono/lucide-roblox-direct/refs/heads/main/source.lua) as a modulescript and require it.
```lua
 
    local Library = require(game:GetService("ReplicatedStorage"):WaitForChild("Obsidian"))
    local Icons = require(game:GetService("ReplicatedStorage"):WaitForChild("Lucide"))
    
    -- Set Library Icon Module
    Library:SetIconModule(Icons)
```

> **Note** : 
> 
> Make sure to **use SetIconModule before you create anything UI related** (such as the window)

* * *

Then, you should be good to go! You can start by checking out the [Window](https://docs.mspaint.cc/obsidian/core/library/Window) section to create your first window, tab, groupbox and UI elements.

If you are too lazy to read the documentation, you can also check out the [example script](https://github.com/deividcomsono/Obsidian/blob/main/Example.lua).

### Addons

Due to Roblox Studio's limitations, only one addon is available for Roblox Studio at the moment. The [ThemeManager](https://docs.mspaint.cc/obsidian/core/addons/thememanager) addon is available to use (and it will not be able to save themes).

#### Installing ThemeManager

To install the ThemeManager addon, you can copy the [addons/ThemeManager.lua](https://raw.githubusercontent.com/deividcomsono/Obsidian/main/addons/ThemeManager.lua) file into a modulescript and require it.
```lua
 
    local Obsidian = game:GetService("ReplicatedStorage"):WaitForChild("Obsidian")
    
    local Library = require(Obsidian)
    local ThemeManager = require(Obsidian:WaitForChild("ThemeManager"))
```

### Types

The library comes with a type definition file for the lsp.

You can find the type definition file [here](https://raw.githubusercontent.com/deividcomsono/Obsidian/refs/heads/main/Library.d.luau).

---

## Next.js + Tailwind

We use the shadcn/ui CLI to automate the installation process.

[Open in ](https://v0.dev/chat/api/open?url=https://www.mspaint.cc/r/obsidian-demo.json)

![logo](https://docs.mspaint.cc/_next/image?url=%2Fmspaint.png&w=64&q=75)mspaint

Groupbox

This is a toggle

This is a checkbox

This is a label

This is a label which wraps its text!

This is a label exposed to Labels

This is a label made with table options and an index

This is a label that doesn't wrap it's own text

* * *

This is my slider!

3/5

This is my custom display slider!

0/5

This is a textbox

Color

Keybind

MB2

Press Keybind

X

Groupbox #2

This label spans multiple lines! We're gonna run out of UI space... Just kidding! Scroll down! Hello from below!

Tab1 Toggle

Dropdowns

A dropdown

A searchable dropdown

A display formatted dropdown

A multi dropdown

A disabled dropdown

A dropdown with disabled value

A very long dropdown

A player dropdown

A team dropdown

version: example
```lua
    import { useMemo } from "react";
    import { Obsidian } from "@/components/obsidian/obsidian";
    import UIData from "@/components/demo/ObsidianExtracted.json";
    
    export default function DemoObsidian() {
        const memoizedObsidian = useMemo(
            () => (
                <Obsidian
                    title="mspaint"
                    icon="/mspaint.png"
                    footer="version: example"
                    uiData={UIData}
                />
            ),
            []
        );
    
        return (
          <div className="w-screen h-screen flex justify-center items-center">
            <div className="max-w-[720px] max-h-[600px] scale-[0.8] max-sm:scale-[0.5] md:scale-90 lg:scale-100">
              {memoizedObsidian}
            </div>
          </div>
        );
    }
```

### Installation
```lua
 
    pnpm dlx shadcn@latest add "https://www.mspaint.cc/r/obsidian"
```
```lua
    npx shadcn@latest add "https://www.mspaint.cc/r/obsidian"
```
```lua
    yarn shadcn@latest add "https://www.mspaint.cc/r/obsidian"
```
```lua
    bunx --bun shadcn@latest add "https://www.mspaint.cc/r/obsidian"
```

* * *

Then add the following lines of code in your `global.css` file.
```lua
 
    .no-scrollbar {
      -ms-overflow-style: none; /* IE and Edge */
      scrollbar-width: none;    /* Firefox */
    }
    
    .no-scrollbar::-webkit-scrollbar {
      width: 0px;
      height: 0px;
      display: none;            /* Chrome, Safari, Opera */
    }
```

* * *

### Usage
```lua
 
    import {
      Obsidian,
      ObsidianTab,
      ObsidianLeft,
      ObsidianRight,
      ObsidianGroupbox,
      ObsidianTabWarning,
      ObsidianToggle,
      ObsidianSlider,
      ObsidianInput,
      ObsidianDropdown,
      ObsidianButton,
      ObsidianLabel,
      ObsidianDivider,
      ObsidianImage,
    } from "@/components/obsidian/obsidian";
```
```lua
 
    <Obsidian
      title="mspaint"
      icon="/mspaint.png"
      footer="version: example"
      //width={720}
      //height={600}
    >
      <ObsidianTab name="General" order={0} icon="Settings">
        <ObsidianLeft>
          <ObsidianGroupbox name="Basics" order={0}>
            <ObsidianToggle text="Enable feature" defaultChecked />
            <ObsidianSlider
              text="Opacity"
              min={0}
              max={100}
              value={80}
              suffix="%"
            />
            <ObsidianInput text="Display name" placeholder="Type your name" />
            <ObsidianDivider />
            <ObsidianDropdown text="Mode" options={["Simple", "Advanced"]} />
            <ObsidianButton text="Apply" />
          </ObsidianGroupbox>
        </ObsidianLeft>
    
        <ObsidianRight>
          <ObsidianGroupbox name="Info" order={0}>
            <ObsidianLabel text="This is a label." />
            <ObsidianImage text="Logo" image="/next.svg" height={200} />
          </ObsidianGroupbox>
        </ObsidianRight>
      </ObsidianTab>
    </Obsidian>
```

### Extra Resources

If you want to automatically dump your obsidian UI to a JSON file, you can use the following script which will save a file named `ObsidianExtracted.json` in your executor's `workspace` folder:
```lua
 
    loadstring(game:HttpGet("https://raw.githubusercontent.com/mspaint-cc/assets/refs/heads/main/uidata/extractor.lua"))()
```

This file can then be imported and put into the `uiData` prop of the Obsidian component:
```lua
 
    import UIData from "@/data/ObsidianExtracted.json";
    
    <Obsidian
      title="mspaint"
      icon="/mspaint.png"
      footer="version: example"
      uiData={UIData}
    />
```

---

## HTML

![logo](https://docs.mspaint.cc/_next/image?url=%2Fmspaint.png&w=64&q=75)mspaint

Groupbox

This is a toggle

This is a checkbox

This is a label

This is a label which wraps its text!

This is a label exposed to Labels

This is a label made with table options and an index

This is a label that doesn't wrap it's own text

* * *

This is my slider!

3/5

This is my custom display slider!

0/5

This is a textbox

Color

Keybind

MB2

Press Keybind

X

Groupbox #2

This label spans multiple lines! We're gonna run out of UI space... Just kidding! Scroll down! Hello from below!

Tab1 Toggle

Dropdowns

A dropdown

A searchable dropdown

A display formatted dropdown

A multi dropdown

A disabled dropdown

A dropdown with disabled value

A very long dropdown

A player dropdown

A team dropdown

version: example
```lua
    <!DOCTYPE HTML>
    <html>
      <head>
        <meta charset="utf-8" />
        
        <!-- Tailwind CSS -->
        <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
        
        <!-- Obsidian -->
        <script src="https://www.mspaint.cc/widgets/obsidian.js"></script>
    
        <!-- Obsidian Scrollbar CSS -->
        <style>
          .no-scrollbar {
            -ms-overflow-style: none; /* IE and Edge */
            scrollbar-width: none;    /* Firefox */
          }
    
          .no-scrollbar::-webkit-scrollbar {
            width: 0px;
            height: 0px;
            display: none;            /* Chrome, Safari, Opera */
          }
        </style>
      </head>
      <body>
        <Obsidian
          title="mspaint"
          footer="version: example"
          icon="https://www.mspaint.cc/icon.png"
          ui-data-src="https://raw.githubusercontent.com/mspaint-cc/webmspaint/refs/heads/main/registry/obsidian-demo/ObsidianExtracted.json">
        </Obsidian>
      </body>
    </html>
```

### Installation

Obsidian depends on TailwindCSS so you need to first setup TailwindCSS. You can do this by adding this line of code to the `<head>` section of your HTML file:
```lua
 
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
```

Then you can import the Obsidian widget script in the `<head>` section of your HTML file:
```lua
 
    <script src="https://www.mspaint.cc/widgets/obsidian.js"></script>
```

You will also need to add the following lines of code into a stylesheet.
```lua
 
    .no-scrollbar {
      -ms-overflow-style: none; /* IE and Edge */
      scrollbar-width: none;    /* Firefox */
    }
    
    .no-scrollbar::-webkit-scrollbar {
      width: 0px;
      height: 0px;
      display: none;            /* Chrome, Safari, Opera */
    }
```

* * *

### Usage
```lua
 
    <Obsidian
      title="mspaint"
      footer="version: example"
      icon="https://www.mspaint.cc/icon.png"
      ui-data-src="https://raw.githubusercontent.com/mspaint-cc/webmspaint/refs/heads/main/registry/obsidian-demo/ObsidianExtracted.json">
    </Obsidian>
```

or
```lua
 
    <Obsidian
      title="mspaint"
      footer="version: example"
      icon="https://www.mspaint.cc/icon.png"
    >
      <!-- Obsidian UI Data -->
      <script type="application/json">
        { "tabs": { "Home": { "order": 1, "groups": [] } } }
      </script>
    </Obsidian>
```

To check how the UI data is structured, you may refer to the [UI parser](https://github.com/mspaint-cc/webmspaint/blob/main/src/components/obsidian/DynamicTab.tsx) or the [UI Element Typedefs](https://github.com/mspaint-cc/webmspaint/blob/main/src/components/obsidian/element.types.ts). Additionally, the [test view page](https://docs.mspaint.cc/obsidian-test) allows you to test the extracted JSON UI data.

### Extra Resources

If you want to automatically dump your obsidian UI to a JSON file, you can use the following script which will save a file named `ObsidianExtracted.json` in your executor's `workspace` folder:
```lua
 
    loadstring(game:HttpGet("https://raw.githubusercontent.com/mspaint-cc/assets/refs/heads/main/uidata/extractor.lua"))()
```

This file can then be put in a static path and imported in the `ui-data-src` prop of the Obsidian component:
```lua
 
    <Obsidian title="mspaint" footer="version: example" icon="./icon.png" ui-data-src="./ObsidianExtracted.json"></Obsidian>
```

---

## Contributing

Our official repository can be found on [GitHub](https://github.com/deividcomsono/Obsidian). Any other forks of the repository may not be fully compatible with our documentation.

### Contributing to Obsidian

  1. Use the [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/) format when contributing to Obsidian.
  2. Please make sure to have used spaces instead of tabs for indentation.
  3. When creating a pull request, please describe the changes you made and why you made them.

---

## SaveManager

### What is the SaveManager?

The SaveManager is an addon that allows you to save, load, delete and set autoload configurations in Obsidian.

### Quick Start

To quickly get started with the SaveManager addon, you can use the following code:
```lua
 
    local repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
    local Library = loadstring(game:HttpGet(repo .. "Library.lua"))()
    local SaveManager = loadstring(game:HttpGet(repo .. "addons/SaveManager.lua"))()
    
    local Tabs = {
    	Main = Window:AddTab("Main", "user"),
    	["UI Settings"] = Window:AddTab("UI Settings", "settings"),
    }
    
    -- Set the library
    SaveManager:SetLibrary(Library)
    
    -- Don't save theme settings in configuration
    SaveManager:IgnoreThemeSettings()
    
    -- Manual Ignore List (will ignore all Toggles/Options with the indexes provided)
    -- Here we are ignoring the menu keybind
    SaveManager:SetIgnoreIndexes({ "MenuKeybind" })
    
    -- Map out folder structure
    SaveManager:SetFolder("MyScriptHub/specific-game")
    SaveManager:SetSubFolder("Lobby") -- if the game has multiple places inside of it (for example: DOORS)
    -- you can use this to save configs for those places separately
    -- The path in this script would be: MyScriptHub/specific-game/settings/specific-place
    -- [ This is optional ]
    
    -- Builds our config menu on the right side of our tab
    SaveManager:BuildConfigSection(Tabs["UI Settings"])
    
    -- Load the autoload config
    SaveManager:LoadAutoloadConfig()
```

### API Reference

#### Methods

##### SetLibrary

Sets the library to use for the SaveManager.
```lua
 
    SaveManager:SetLibrary(Library)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Library to use for the SaveManager| Library (table)| nil  
  
##### SetFolder

Sets the folder to use for the SaveManager.
```lua
 
    SaveManager:SetFolder(folder)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Folder path relative to workspace| string| nil  
  
##### SetSubFolder

Sets the subfolder to use for the SaveManager. This is not required, but can be used to save configs for specific places inside of the game.
```lua
 
    SaveManager:SetSubFolder(subfolder)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Subfolder to use for the SaveManager| string| nil  
  
##### SetIgnoreIndexes

Sets the indexes to ignore when saving configs.
```lua
 
    SaveManager:SetIgnoreIndexes(indexes)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Indexes to ignore when saving configs (e.g. `{ "MenuKeybind" }`)| table| nil  
  
##### IgnoreThemeSettings

Adds the default theme-related indexes to the ignore list so configs do not override user themes.
```lua
 
    SaveManager:IgnoreThemeSettings()
```

##### Save

Serializes the current library state to a JSON file inside the configured folder tree.
```lua
 
    local success, err = SaveManager:Save(name)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| File name without extension| string| nil  
  
Returns `boolean, string?` where the second value contains an error message when the save fails.

##### Load

Loads a JSON configuration produced by the save method and applies it to the library controls.
```lua
 
    local success, err = SaveManager:Load(name)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| File name without extension| string| nil  
  
Returns `boolean, string?` describing the outcome.

##### Delete

Removes a configuration file from disk.
```lua
 
    local success, err = SaveManager:Delete(name)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| File name without extension| string| nil  
  
Returns `boolean, string?` describing the outcome.

##### RefreshConfigList

Scans the folder tree and returns an array of available configuration names (without extensions).
```lua
 
    local configs = SaveManager:RefreshConfigList()
```

Returns `{ string }` of config names.

##### GetAutoloadConfig

Reads the persistent autoload marker and returns the config name that should be auto-loaded.
```lua
 
    local name = SaveManager:GetAutoloadConfig()
```

Returns a `string` representing the config name or the literal value `"none"`.

##### LoadAutoloadConfig

Loads and applies the configuration marked for autoload if a name is stored on disk.
```lua
 
    SaveManager:LoadAutoloadConfig()
```

##### SaveAutoloadConfig

Sets the configuration that should load automatically on script startup.
```lua
 
    local success, err = SaveManager:SaveAutoloadConfig(name)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Config name to mark for autoload| string| nil  
  
Returns `boolean, string?` describing the outcome.

##### DeleteAutoLoadConfig

Clears the autoload marker so no configuration is loaded automatically.
```lua
 
    local success, err = SaveManager:DeleteAutoLoadConfig()
```

Returns `boolean, string?` describing the outcome.

##### BuildConfigSection

Creates the SaveManager UI in your library tab so users can create, load, overwrite and manage configs directly.
```lua
 
    SaveManager:BuildConfigSection(tab, icon)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Tab to populate| Tab (table)| nil  
2| Groupbox icon| string| nil  
  
This call also ignores the config management controls when saving.

##### SetLoadingOrder

Allows you to change the order of element types when loading any config.
```lua
 
    SaveManager:SetLoadingOrder(enabled, order)
    
    -- Example: SaveManager:SetLoadingOrder(true, { "Dropdown", "ColorPicker", "KeyPicker", "Slider", "Input", "Toggle" })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Is Enabled?| boolean| false  
1| Element Types (Toggle, Slider, Dropdown, ColorPicker, KeyPicker, Input)| table| nil

---

## ThemeManager

### What is the ThemeManager?

The ThemeManager addon ships with a library of built-in color schemes and helper utilities to create, persist and reuse custom themes for your Obsidian UI.

### Quick Start

Add the ThemeManager alongside your library and attach it to a tab or groupbox so users can tweak colors on the fly:
```lua
 
    local repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
    local Library = loadstring(game:HttpGet(repo .. "Library.lua"))()
    local ThemeManager = loadstring(game:HttpGet(repo .. "addons/ThemeManager.lua"))()
    
    -- register the library so ThemeManager can manipulate controls
    ThemeManager:SetLibrary(Library)
    
    -- optional: keep themes inside a custom folder
    ThemeManager:SetFolder("MyScriptHub")
    
    local Tab = Window:AddTab("UI Settings", "settings")
    
    -- automatically creates the color pickers, theme lists and buttons
    ThemeManager:ApplyToTab(Tab)
    
    -- apply whatever theme is flagged as default (built-in or custom)
    ThemeManager:LoadDefault()
```

#### Built-In Themes

`ThemeManager.BuiltInThemes` exposes packaged schemes such as Default, BBot, Fatality, Nord, Dracula, Catppuccin and more. These are available instantly from the “Theme list” dropdown created by `ApplyToTab`.

### API Reference

#### Methods

##### SetLibrary

Registers the UI library instance so ThemeManager can read and update color pickers.
```lua
 
    ThemeManager:SetLibrary(Library)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Library to use for the ThemeManager| Library (table)| nil  
  
##### SetFolder

Overrides the base folder used to store theme JSON files and default markers.
```lua
 
    ThemeManager:SetFolder(folder)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Folder path relative to workspace| string| `"ObsidianLibSettings"`  
  
##### ApplyTheme

Applies either a built-in theme or a custom theme saved on disk.
```lua
 
    ThemeManager:ApplyTheme(name)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Theme name (built-in or custom)| string| nil  
  
##### ThemeUpdate

Pushes the current picker values back into the library scheme. Automatically called when color pickers change, but exposed if you need to trigger it manually.
```lua
 
    ThemeManager:ThemeUpdate()
```

##### GetCustomTheme

Loads a custom theme JSON file and returns the decoded table.
```lua
 
    local theme = ThemeManager:GetCustomTheme(name)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Theme file name without extension| string| nil  
  
Returns `table?`; `nil` when the file does not exist or cannot be decoded.

##### LoadDefault

Reads the saved default marker and applies the relevant theme (built-in or custom).
```lua
 
    ThemeManager:LoadDefault()
```

##### SaveDefault

Stores the provided theme name as the default choice for future sessions.
```lua
 
    ThemeManager:SaveDefault(name)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Theme name to persist| string| nil  
  
##### SetDefaultTheme

Replaces the built-in `Default` theme with custom color definitions before rendering the UI. Call this **before** `ApplyToTab`.
```lua
 
    ThemeManager:SetDefaultTheme(theme)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Table containing `FontColor`, `MainColor`, `AccentColor`, `BackgroundColor`, `OutlineColor`, `FontFace`| table| nil  
  
##### SaveCustomTheme

Serializes the current picker values to `<folder>/themes/<name>.json`.
```lua
 
    ThemeManager:SaveCustomTheme(name)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Theme name without file extension| string| nil  
  
##### Delete

Removes a saved custom theme file from disk.
```lua
 
    local success, err = ThemeManager:Delete(name)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Theme name without file extension| string| nil  
  
Returns `boolean, string?` describing the result.

##### ReloadCustomThemes

Re-scans the theme directory and returns a list of custom theme names.
```lua
 
    local names = ThemeManager:ReloadCustomThemes()
```

Returns `{ string }` of theme names.

##### CreateThemeManager

Builds the ThemeManager controls inside a provided groupbox (color pickers, dropdowns and action buttons).
```lua
 
    ThemeManager:CreateThemeManager(groupbox)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Groupbox returned by `AddLeftGroupbox`/`AddRightGroupbox`| Groupbox (table)| nil  
  
##### CreateGroupBox

Helper that creates the standard “Themes” groupbox on the left side of a tab.
```lua
 
    local groupbox = ThemeManager:CreateGroupBox(tab, icon)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Tab returned by `Window:AddTab`| Tab (table)| nil  
2| Icon to use for the groupbox| string| nil  
  
Returns the created groupbox so you can customize it further before calling `CreateThemeManager`.

##### ApplyToTab

Creates a groupbox within the tab and installs the ThemeManager UI into it.
```lua
 
    ThemeManager:ApplyToTab(tab, icon)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Tab returned by `Window:AddTab`| Tab (table)| nil  
2| Icon to use for the groupbox created inside the tab| string| nil  
  
##### ApplyToGroupbox

Installs the ThemeManager UI into an existing groupbox.
```lua
 
    ThemeManager:ApplyToGroupbox(groupbox)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Groupbox to populate| Groupbox (table)| nil

---

## Loading

* * *

### Loading

The `Loading` GUI is a specialized modal overlay designed to manage multi-stage script initialization. It provides a centered loading interface with a progress bar and an optional sidebar for showing information or interactive elements.

#### Usage

Create a loading instance with `Library:CreateLoading()`:
```lua
 
    local Loading = Library:CreateLoading({
        Title = "mspaint",
        Icon = 95816097006870,
        TotalSteps = 4
    })
    
    -- Loading...
    Loading:SetMessage("Initializing...")
    Loading:SetDescription("Waiting for game to load...")
    task.wait(1)
    
    Loading:SetCurrentStep(1)
    Loading:SetDescription("Loading configuration...")
    task.wait(1)
    
    -- Show sidebar with information
    Loading:SetCurrentStep(2)
    Loading:ShowSidebarPage(true)
    Loading.Sidebar:AddLabel("User: " .. game.Players.LocalPlayer.Name)
    Loading.Sidebar:AddLabel("Version: v1.0.0")
    task.wait(1)
    
    Loading:SetCurrentStep(3)
    Loading:SetDescription("Ready to start!")
    task.wait(1)
    
    Loading:SetCurrentStep(4)
    Loading:Continue() -- Destroys the loader and opens the main window
```

#### Configuration

Prop

Type

#### Methods

##### SetMessage

Updates the primary status message.
```lua
 
    Loading:SetMessage("Loading...")
```

##### SetDescription

Updates the secondary descriptive text.
```lua
 
    Loading:SetDescription("Connecting to server...")
```

##### SetCurrentStep

Updates the progress bar and step counter.
```lua
 
    Loading:SetCurrentStep(5)
```

##### SetTotalSteps

Updates the total steps needed.
```lua
 
    Loading:SetTotalSteps(20)
```

##### SetLoadingIcon

Switches the central rotating icon.
```lua
 
    Loading:SetLoadingIcon("info") -- Lucide icon or roblox asset
```

##### SetLoadingIconTweenTime

Updates the rotation tween time. Setting this to `0` stops rotation of the loading icom.
```lua
 
    Loading:SetLoadingIconTweenTime(2)
```

##### SetLoadingIconColor

Dynamically updates the color of the loading icon.
```lua
 
    Loading:SetLoadingIconColor(Color3.new(1, 0, 0))
```

##### ShowSidebarPage

Toggles the visibility of the sidebar.
```lua
 
    Loading:ShowSidebarPage(true)
```

##### ShowErrorPage

Switches to the error state view.
```lua
 
    Loading:ShowErrorPage(true)
```

##### SetErrorMessage

Updates the text on the error screen.
```lua
 
    Loading:SetErrorMessage("Failed to authenticate.")
```

##### SetErrorButtons

Replaces the buttons on the error screen. (same layout as the FooterButtons in Modals)
```lua
 
    Loading:SetErrorButtons({
        Retry = {
            Title = "Retry Connection",
            Variant = "Primary",
            Callback = function() -- logic end
        }
    })
```

##### Destroy

Closes the loader. If `CreateWindow` was already called, this will restore the main window's visibility.
```lua
 
    Loading:Destroy()
```

##### Continue

Alias for `Destroy()`.
```lua
 
    Loading:Continue()
```

* * *

#### Sidebar Interface

The `Loading.Sidebar` frame works like a groupbox. You are able to use every UI element inside the sidebar.
```lua
 
    Loading.Sidebar:AddLabel("Version: 1.2.0")
    Loading.Sidebar:AddButton("Print Hello World", function() print("Hello World") end)
```

---

## Window

* * *

### Window

![Obsidian](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fobsidian.0bn3p5nrm_77_.png&w=3840&q=75)

The `Window` object is your base UI container. It hosts tabs, groupboxes and mostly everything else driven by the library. The next step to using the library is to [create a tab inside the window](https://docs.mspaint.cc/obsidian/core/structure/tabs).

#### Usage

Create a window with `Library:CreateWindow()` and override any defaults you need:
```lua
 
    local Window = Library:CreateWindow({
        Title = "mspaint",
        Footer = "version: example",
        Icon = 95816097006870,
        NotifySide = "Right",
    })
```

Prop

Type

##### ChangeTitle

Changes the title of the window.
```lua
 
    Window:ChangeTitle("New Title")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The new title of the window| string| nil  
  
##### SetFooter

Changes the footer of the window.
```lua
 
    Window:SetFooter("New Footer")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The new footer of the window| string| nil  
  
##### SetBackgroundImage

Changes the background image of the window. (Only works if `BackgroundImage` was set in the constructor)
```lua
 
    Window:SetBackgroundImage("rbxasset://textures/ui/GuiImagePlaceholder.png")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The new background image of the window| string/ID| nil  
  
##### SetCornerRadius

Changes the corner radius for elements.
```lua
 
    Window:SetCornerRadius(10)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The new corner radius| number| nil  
  
##### Toggle

Toggles the visibility of the window.
```lua
 
    Window:Toggle()
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Force a specific visibility state| boolean?| nil  
  
##### ShowTabInfo

Displays the current tab's name and description in the header area (next to the searchbar).
```lua
 
    Window:ShowTabInfo("Settings", "Configure your preferences")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Tab name to display| string| nil  
2| Tab description to display| string| nil  
  
##### HideTabInfo

Hides the tab info display and restores the searchbar to full width.
```lua
 
    Window:HideTabInfo()
```

#### Sidebar Layout & Resizing

Obsidian can expose a draggable handle that lets players resize the sidebar at runtime. Enable it and tweak the behaviour directly from your window configuration:
```lua
 
    local Window = Library:CreateWindow({
        Title = "mspaint",
        EnableSidebarResize = true,
    
        -- Optional Sidebar Settings
        MinSidebarWidth = 200, -- stop shrinking when the sidebar hits 200px
        SidebarCompactWidth = 56,
    })
```

#### Sidebar Methods

All window instances expose helpers for responding to layout changes or driving your own resizing logic.

##### GetSidebarWidth

Returns the current sidebar width in pixels.
```lua
 
    local CurrentWidth = Window:GetSidebarWidth()
```

##### IsSidebarCompacted

Checks if the sidebar is currently in compact (icon-only) mode.
```lua
 
    if Window:IsSidebarCompacted() then
        print("Sidebar is collapsed")
    end
```

##### SetSidebarWidth

Programmatically resize the sidebar. Values outside the configured bounds are clamped automatically.
```lua
 
    Window:SetSidebarWidth(240)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Desired sidebar width in pixels| number| `nil (required)`  
  
##### SetCompact

Toggle compact mode explicitly. Pass `true` to force compact, or `false` to restore the last expanded width.
```lua
 
    Window:SetCompact(true)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether the sidebar should stay compacted| boolean| —

---

## Notifications

* * *

### Notifications

![notification](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fnotification.0qg2vqvo0irzm.png&w=3840&q=75)

Use `Library:Notify()` for quick, non-blocking feedback. Notifications support:

  * Simple timed popups.
  * Persistent messages you can update or destroy.
  * Progress indicators with step counters.
  * Optional sounds or other custom behavior based on your own logic.

#### Creating a notification

Choose between positional arguments for quick calls or a configuration table for full control.

##### Regular parameters
```lua
 
    Library:Notify("Hello world!", 4)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Description of the notification| string| "nil"  
2| Amount of time to show the notification for| number | instance| 4  
3| SoundId to play when the notification is shown| number| nil  
4| Volume of the notification sound| number| 3  
  
##### Table parameters

Pick an example that matches your use case:
```lua
 
    Library:Notify({
        Title = "mspaint",
        Description = "Hello world!",
        Time = 4,
    })
```
```lua
    Library:Notify({
        Title = "Icon Notification",
        Description = "This notification has a neat little icon beside the text.",
        Icon = "info", -- Lucide icon Name
        Time = 4,
    })
```
```lua
    Library:Notify({
        Title = "Big Icon Notification",
        Description = "This notification is using the legacy 24x24 separated image layout.",
        BigIcon = "rbxassetid://10204738596",
        IconColor = Color3.new(0, 1, 0), -- Green
        Time = 4,
    })
```
```lua
    local Notification = Library:Notify({
        Title = "mspaint",
        Description = "Waiting for next match...",
        Persist = true,
    })
    
    -- Wait for the player to start playing
    game.Players.LocalPlayer:GetAttributeChangedSignal("Playing"):Wait()
    
    -- Destroy the notification
    Notification:Destroy()
```
```lua
    -- Create a persistent notification (this also works for non persistent notifications)
    local Notification = Library:Notify({
        Title = "mspaint",
        Description = "Waiting for next match...",
        Persist = true,
    })
    
    -- Wait for the player to start playing
    game.Players.LocalPlayer:GetAttributeChangedSignal("Playing"):Wait()
    
    -- Update the notification
    Notification:ChangeTitle("mspaint - Match Starting")
    Notification:ChangeDescription("Waiting for match to finish loading...")
    
    -- Wait for the match to finish loading
    workspace.Map:GetAttributeChangedSignal("MatchLoaded"):Wait()
    
    -- Remove the notification
    Notification:Destroy()
```
```lua
    -- Create a progress notification (steps = amount of steps)
    local Notification = Library:Notify({
        Title = "mspaint",
        Description = "Loading...",
        Steps = 10,
    })
    
    for i = 1, 10 do
        -- Update the notification with the current step
        Notification:ChangeStep(i)
        task.wait(0.1)
    end
    
    Notification:Destroy()
```

Prop

Type

#### Methods

##### ChangeTitle

Update the notification title without recreating it.
```lua
 
    Notification:ChangeTitle("New Title")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| New title of the notification| string| nil  
  
##### ChangeDescription

Refresh the supporting text while keeping the notification open.
```lua
 
    Notification:ChangeDescription("New Description")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| New description of the notification| string| nil  
  
##### ChangeStep

Advance or rewind the progress bar when using step-based notifications.
```lua
 
    Notification:ChangeStep(5)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| New step of the progress notification| number| nil  
  
##### Destroy

Immediately dismiss the notification and free its resources.
```lua
 
    Notification:Destroy()
```

---

## Dialogs

* * *

### Dialogs

Dialogs overlay the user interface and blur out the background, demanding the user's attention.

Dialogs inherit methods from Groupboxes therefore you can call all standard Groupbox methods (`AddToggle`, `AddInput`, `AddSlider`, etc.) on the returned Dialog instance to build custom interactions directly into the Dialog.

![test-dialog](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Ftest-dialog.07dtzaftr9xp5.png&w=3840&q=75)
```lua
    local Dialog
    Dialog = Window:AddDialog("DialogueIdx", {
        Title = "Test Dialog",
        Description = "This is a test dialog. Please confirm or cancel.",
        AutoDismiss = true,
        OutsideClickDismiss = true,
        FooterButtons = {
            Cancel = {
                Title = "Cancel",
                Variant = "Ghost",
                Order = 1,
                Callback = function()
                    print("Cancelled the dialog.")
                end
            },
            Secondary = {
                Title = "Secondary",
                Variant = "Secondary",
                Order = 2,
                Callback = function()
                    print("Secondary action.")
                end
            },
            Delete = {
                Title = "Delete",
                Variant = "Destructive",
                Order = 3,
                Callback = function()
                    print("Deleted the asset.")
                end
            },
            Confirm = {
                Title = "Confirm",
                Variant = "Primary",
                WaitTime = 3, -- 3 seconds minimum wait
                Order = 4,
                Callback = function(self)
                    print("Confirmed the dialog.")
                end
            }
        }
    })
    
    Dialog:AddToggle("DisableSecondary", {
        Text = "Disable Secondary Button",
        Default = false,
        Callback = function(value) 
            Dialog:SetButtonDisabled("Secondary", value) 
        end
    })
    
    Dialog:AddInput("InputTest", {
        Text = "Type something here:",
        Callback = function(value) print("Typed:", value) end
    })
    
    Dialog:AddToggle("SwapDeleteOrder", {
        Text = "Send Delete to Right",
        Default = false,
        Callback = function(value) 
            Dialog:SetButtonOrder("Delete", value and 5 or 3)
        end
    })
```

![empty-dialog](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fempty-dialog.12of__cjy~14x.png&w=3840&q=75)
```lua
    Window:AddDialog("EmptyDialogueIdx", {
        Title = "Empty Dialog",
        Description = "This dialog has no elements. The padding should be completely normal and singular.",
        AutoDismiss = true,
        OutsideClickDismiss = true,
        FooterButtons = {
            Confirm = {
                Title = "Okay",
                Variant = "Primary",
                Callback = function() end
            }
        }
    })
```

Prop

Type

#### Custom Button Example

To configure custom footer buttons, specify a dictionary mapped to their properties in `FooterButtons`. You can natively assign a `WaitTime` to any button, and Obsidian will render an animated progress bar preventing interaction until the timer expires.
```lua
 
    Confirm = {
        Title = "Confirm",
        Variant = "Primary", -- Optional (Primary, Secondary, Destructive, Ghost)
        WaitTime = 3, -- 3 seconds minimum before the button can be clicked
        Order = 4,
        Callback = function(self)
            print("Confirmed the dialog.")
        end
    }
```

#### Methods

> **Note** : 
> 
> Dialogs inherit all methods from Groupboxes. You may use `AddToggle`, `AddInput`, `AddDropdown`, and so forth directly on the Dialog instance itself. The methods below are unique to Dialogs.

##### SetTitle

Update the dialog title dynamically.
```lua
 
    Dialog:SetTitle("New Title")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| New title of the dialog| string| nil  
  
##### SetDescription

Refresh the descriptive text dynamically.
```lua
 
    Dialog:SetDescription("New Description")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| New description of the dialog| string| nil  
  
##### AddFooterButton

Programmatically inject a new footer button into the dialog.
```lua
 
    Dialog:AddFooterButton("ExtraAction", {
        Title = "Do something else",
        Variant = "Secondary",
        Callback = function() end
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Dictionary Index Reference| string| nil  
2| Dialog Button Configuration Table| DialogButtonInfo| nil  
  
##### RemoveFooterButton

Programmatically remove an existing footer button from the dialog.
```lua
 
    Dialog:RemoveFooterButton("ExtraAction")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Dictionary Index Reference| string| nil  
  
##### SetButtonDisabled

Change the disabled state of a specific footer button dynamically.
```lua
 
    Dialog:SetButtonDisabled("Confirm", true)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Dictionary Index Reference| string| nil  
2| Whether the button should be disabled| boolean| nil  
  
##### SetButtonOrder

Change the display order index of a specific footer button dynamically.
```lua
 
    Dialog:SetButtonOrder("Delete", 5)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Dictionary Index Reference| string| nil  
2| New Layout Order| number| nil  
  
##### Dismiss

Explicitly close and destroy the dialog and all its visual child elements.
```lua
 
    Dialog:Dismiss()
```

---

## Overlays

* * *

### Draggable Labels

![Draggable Label](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fdraggable-label.0quqli8zdeq0j.png&w=3840&q=75)

Draggable Labels are compact, draggable overlays containing text. They are ideal for surfacing information such as FPS, ping, server info, etc. The feature is inspired by the [LinoriaLib UI Library Watermark feature](https://github.com/mstudio45/linorialib).

#### AddDraggableLabel

Adds a draggable label to the UI.
```lua
 
    local DraggableLabel = Library:AddDraggableLabel("Obsidian demo")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Text to display in the draggable label| string| nil  
2| Icon to display in the draggable label (Lucide icon Name or Roblox Asset ID)| string| number  
3| Position of the icon (either "Left" or "Right")| string| "Left"  
  
#### Methods

##### SetText

Sets the draggable label text.
```lua
 
    DraggableLabel:SetText("Obsidian demo v2")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Text to display in the draggable label| string| nil  
  
##### SetVisible

Sets the draggable label visibility.
```lua
 
    DraggableLabel:SetVisible(false)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to show the draggable label| boolean| true  
  
##### Destroy

Destroys the draggable label.
```lua
 
    DraggableLabel:Destroy()
```

#### Example
```lua
 
    -- Sets the draggable label visibility
    local DraggableLabel = Library:AddDraggableLabel("Obsidian demo")
    DraggableLabel:SetVisible(true)
    
    -- Example of dynamically-updating draggable label with common traits (fps and ping)
    local FrameTimer = tick()
    local FrameCounter = 0;
    local FPS = 60;
    
    local WatermarkConnection = game:GetService('RunService').RenderStepped:Connect(function()
        FrameCounter += 1;
    
        if (tick() - FrameTimer) >= 1 then
            FPS = FrameCounter;
            FrameTimer = tick();
            FrameCounter = 0;
        end;
    
        DraggableLabel:SetText(('Obsidian demo | %s fps | %s ms'):format(
            math.floor(FPS),
            math.floor(game:GetService('Stats').Network.ServerStatsItem['Data Ping']:GetValue()) -- Use LocalPlayer:GetNetworkPing() inside Studio
        ));
    end);
```

* * *

### Draggable Buttons

![Draggable Button](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fdraggable-button.0qu~308_oxda7.png&w=3840&q=75)

Draggable Buttons are compact floating buttons that can be dragged around the screen. When clicked, they fire a callback and pass themselves as the first argument.

#### AddDraggableButton

Adds a draggable button to the UI.
```lua
 
    local DraggableButton = Library:AddDraggableButton("Click Me", function(self)
        print("Button was clicked!")
    end)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Text to display on the button| string| nil  
2| Callback fired when clicked (receives the button table)| function| nil  
3| Whether to exclude DPI scaling from this button| boolean?| false  
4| Icon to display on the button (Lucide icon Name or Roblox Asset ID)| string | number| nil  
5| Position of the icon (either "Left" or "Right")| string| "Left"  
  
#### Methods

##### SetText

Sets the button text and auto-resizes the button to fit.
```lua
 
    DraggableButton:SetText("New Text")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Text to display on the button| string| nil  
  
##### Destroy

Destroys the draggable button.
```lua
 
    DraggableButton:Destroy()
```

* * *

### Draggable Menus

![Draggable Menu](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fdraggable-menu.0680hs_b6.9kl.png&w=3840&q=75)

Draggable Menus are floating panels with a title bar and a content container. They are useful for building custom floating windows with any content you want.

#### AddDraggableMenu

Creates a draggable menu and returns the holder frame and content container.
```lua
 
    local Holder, Container = Library:AddDraggableMenu("My Menu")
    
    -- Holder => The outer frame (the draggable window)
    -- Container => The inner frame with a UIListLayout (add your UI elements here)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Title text for the menu header| string| nil  
  
#### Methods

##### Destroy

Destroys the draggable menu.
```lua
 
    Holder:Destroy()
```

#### Example
```lua
 
    local Holder, Container = Library:AddDraggableMenu("Stats Panel")
    
    -- Add custom content to the container
    local Label = Instance.new("TextLabel")
    Label.Text = "Hello from the menu!"
    Label.Size = UDim2.new(0, 100, 0, 24)
    Label.BackgroundTransparency = 1
    Label.TextColor3 = Color3.new(1, 1, 1)
    Label.ZIndex = 11
    Label.Parent = Container
```

> **Note** : 
> 
> `Draggable Menus` renders at **ZIndex 10**. Any child elements you add to the container must have a **ZIndex of 10 or higher** , otherwise they will be hidden behind the menu frame.

* * *

####

---

## Keybind Menu

* * *

### Keybind Menu

![keybinds](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fkeybinds.0f7wz5ifbryaw.png&w=3840&q=75)

The Keybind Menu is a floating overlay that shows every registered [KeyPicker](https://docs.mspaint.cc/obsidian/elements/keybinds) alongside its current state. When a keybind is configured in `Toggle` mode the menu also renders tap-friendly buttons, giving mobile players parity with keyboard users.

#### Enabling the Keybind Menu

The Keybind Menu is **hidden by default**. To show it, set the frame's `Visible` property to `true`:
```lua
 
    Library.KeybindFrame.Visible = true
```

> **Note** : 
> 
> `Library.KeybindFrame` is only created after `Library:CreateWindow()` has been called. Make sure to enable it after the window is set up.

#### Configuration

##### ShowToggleFrameInKeybinds

Controls whether toggle-mode keybinds display their on/off checkbox in the Keybind Menu. When `true`, each toggle-mode keybind gets a clickable checkbox so users can tap it directly from the menu.
```lua
 
    Library.ShowToggleFrameInKeybinds = true
```

Value| Behaviour  
---|---  
`true`| Show toggle checkboxes in the keybind menu (default)  
`false`| Hide the toggle checkboxes; only show the keybind name and key

---

## Utility

* * *

### Custom Cursor

![custom cursor](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fcustomcursor.14v3b6nnv92wh.png&w=3840&q=75)

Enable the custom cursor to render the Obsidian-styled pointer at your mouse position -- handy for experiences that hide or replace Roblox's default cursor.
```lua
 
    Library.ShowCustomCursor = true
```

These functions allow you to set your own image instead of the default custom cursor.
```lua
 
    Library:ChangeCursorIcon(ImageId: string)
    
    -- If you need to change the cursor icon size:
    Library:ChangeCursorIconSize(Size: UDim2)
    
    -- To reset it back, use this function:
    Library:ResetCursorIcon()
```

* * *

### Icons

Icons originate from the [lucide icon pack](https://lucide.dev). You can change the icon library so long as you call it before creating any UI elements and it follows the expected return data.
```lua
 
    local Library = require(game:GetService("ReplicatedStorage"):WaitForChild("Obsidian"))
    local Icons = require(game:GetService("ReplicatedStorage"):WaitForChild("Lucide"))
    
    -- Set Library Icon Module
    Library:SetIconModule(Icons)
```

The direct lucide module can be found [here](https://raw.githubusercontent.com/deividcomsono/lucide-roblox-direct/refs/heads/main/source.lua). (Our automation updates the icon spritesheet each month)

#### Custom Icon Registry

If you'd like to make your own custom Icon Module, make sure it follows these types:
```lua
 
    type Icon = {
        Url: string,
        Id: number,
        IconName: string,
        ImageRectOffset: Vector2,
        ImageRectSize: Vector2,
    }
    
    type IconModule = {
        Icons: { string },
        GetAsset: (Name: string) -> Icon?,
    }
    
    local Icons: IconModule = {
        Icons: {}
    }
    
    function Icons.GetAsset(Name: string)
        return nil
    end
    
    return Icons
```

Obsidian is dependent on a few icons to be able to be displayed properly. Please make sure you have icons named:

  * `check` (Toggles)
  * `chevron-up` (Dropdowns)
  * `move-diagonal-2` (Window Resizing Icon bottom right of the window)
  * `key` (Key System Tab Icon)
  * `search` (Searchbar)
  * `move` (Window movement Icon top right of the window)

* * *

#### Custom Asset Icons

If you'd like to use custom hosted images (like those on Github) with a Roblox Asset ID as a fallback, you can use the built-in ImageManager:
```lua
 
    local ImageManager = Library.ImageManager
```

> **Note** : 
> 
> ImageManager requires the `FileSystem` and `getcustomasset` functions to properly download the images. If neither is available, it falls back to the provided Roblox Asset ID. If the saved image won't load properly, it will not fallback to the provided Roblox Asset ID.

##### AddAsset

Adds a custom asset to the ImageManager
```lua
 
    ImageManager.AddAsset("mspaint_logo", 95816097006870, "https://www.mspaint.cc/icon.png")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Asset Name| string| nil  
2| Roblox Asset ID| number| nil  
3| Asset URL| string| nil  
4| Force Redownload| boolean?| nil  
  
##### GetAsset

Retrieves the asset ID for the provided asset
```lua
 
    local AssetID = ImageManager.GetAsset("mspaint_logo")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Asset Name| string| nil  
  
##### DownloadAsset

Downloads the asset image to the workspace folder  

> **Note** : 
> 
> This runs automatically in AddAsset, so you don't need to call it separately (unless you want to redownload the asset image)
```lua
 
    ImageManager.DownloadAsset("mspaint_logo")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Asset Name| string| nil  
2| Force Redownload| boolean?| nil  
  
* * *

### Menu Keybind

In Obsidian, there is a helper field that you can set that allows you to easily bind a [Keybind](https://docs.mspaint.cc/obsidian/elements/keybinds) to toggle the main window.
```lua
 
    MenuGroup:AddLabel("Menu bind"):AddKeyPicker("MenuKeybind", {
        Default = "RightShift",
        NoUI = true,
        Text = "Menu keybind"
    })
    
    Library.ToggleKeybind = Options.MenuKeybind 
```

* * *

### Lifecycle

#### OnUnload

Registers a callback that fires when the library is unloaded via `Library:Unload()`.
```lua
 
    Library:OnUnload(function()
        print("Library was unloaded!")
    end)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Callback to fire on unload| function| nil  
  
#### Unload

Disconnects all signals registered with `GiveSignal`, fires all `OnUnload` callbacks, destroys tooltips and the ScreenGui, and sets `Library.Unloaded = true`.
```lua
 
    Library:Unload()
```

#### GiveSignal

Registers an `RBXScriptConnection` so it is automatically disconnected when `Library:Unload()` is called.
```lua
 
    Library:GiveSignal(RunService.RenderStepped:Connect(function()
        -- your code
    end))
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Connection to track| RBXScriptConnection| nil  
  
#### Toggle

Toggles the main window visibility. This is a convenience wrapper around `Window:Toggle()`.
```lua
 
    Library:Toggle()
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Force a specific state| boolean?| nil  
  
* * *

### Theme & Registry

#### SetFont

Changes the font used across the entire library.
```lua
 
    Library:SetFont(Enum.Font.GothamMedium)
    -- or
    Library:SetFont(Font.fromEnum(Enum.Font.GothamMedium))
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The font to apply| Font | Enum.Font| nil  
  
#### SetNotifySide

Changes which side of the screen notifications appear on.
```lua
 
    Library:SetNotifySide("Left")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Side to show notifications| "Left" | "Right"| "Right"  
  
#### SetDPIScale

Sets the DPI scale for the entire library. The value is treated as a percentage (100 = normal).
```lua
 
    Library:SetDPIScale(125) -- 125% scale
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| DPI scale percentage| number| 100  
  
#### AddToRegistry

Registers a GuiObject so its properties are automatically updated when the theme changes.
```lua
 
    Library:AddToRegistry(MyFrame, {
        BackgroundColor3 = "MainColor",
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The GUI instance to track| GuiObject| nil  
2| Property-to-scheme-key mapping| table| nil  
  
#### RemoveFromRegistry

Removes a GuiObject from the theme registry.
```lua
 
    Library:RemoveFromRegistry(MyFrame)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The GUI instance to untrack| GuiObject| nil  
  
#### UpdateColorsUsingRegistry

Re-applies the current scheme to every registered instance. Called automatically when the theme changes.
```lua
 
    Library:UpdateColorsUsingRegistry()
```

* * *

### Color Helpers

#### GetBetterColor

Adjusts a color's value (brightness) by the given amount.
```lua
 
    local adjusted = Library:GetBetterColor(Color3.new(1, 0, 0), 0.1)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Base color| Color3| nil  
2| Value adjustment amount| number| nil  
  
#### GetLighterColor

Returns a slightly lighter version of the given color.
```lua
 
    local lighter = Library:GetLighterColor(Library.Scheme.MainColor)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Base color| Color3| nil  
  
#### GetDarkerColor

Returns a slightly darker version of the given color.
```lua
 
    local darker = Library:GetDarkerColor(Library.Scheme.MainColor)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Base color| Color3| nil  
  
* * *

### Misc Helpers

#### SafeCallback

Wraps a function call in error handling. If the call errors and `Library.NotifyOnError` is `true`, a notification is shown.
```lua
 
    Library:SafeCallback(myFunction, arg1, arg2)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Function to call| function| nil  
...| Arguments to pass| any| nil  
  
#### GetTextBounds

Calculates the pixel dimensions of a text string with the given font and size.
```lua
 
    local width, height = Library:GetTextBounds("Hello", Library.Scheme.Font, 14, 200)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Text to measure| string| nil  
2| Font to use| Font| nil  
3| Text size| number| nil  
4| Max width constraint| number?| nil  
  
* * *

### Properties

These are notable properties on the `Library` object that you can read or set:

Prop

Type

---

## Tabs

Tabs are the main way to sepearate each section for your UI. Each tab can have a name, icon and a description. The order is determined by the order of when `Window:AddTab()` is called.

![Obsidian](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Ftabspreview.041sn0zpwp2~9.webp&w=3840&q=75)

### Quick Start

We recommend storing your tabs in a table to make it easier to manage.
```lua
 
    local Tabs = {
        Main = Window:AddTab("Main", "user"),
        ["UI Settings"] = Window:AddTab("UI Settings", "settings"),
    }
```

Once you have created your tabs, you can add UI elements to them via [groupboxes](https://docs.mspaint.cc/obsidian/structure/groupboxes) and/or [tabboxes](https://docs.mspaint.cc/obsidian/structure/tabboxes).

### Usage

You can create a Tab by calling the `AddTab` method on a Window.

#### Arguments
```lua
 
    local Tab = Window:AddTab("Main", "user")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Name of the tab| string| nil  
2| Custom Icon or Icon name from lucide.dev| string| nil  
3| Tab description| string| nil  
  
#### Configuration Table
```lua
 
    local Tab = Window:AddTab({
        Name = "Main",
        Description = "Main features",
        Icon = "home"
    })
```

Prop

Type

### Methods

You can use the following methods to interact with the tab:

##### UpdateWarningBox

Updates the warning box in a tab. This can be used to display warnings/errors, or any useful information.

![warningbox](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fwarningbox.1159fue79iz_4.png&w=3840&q=75)
```lua
 
    Tab:UpdateWarningBox({
        Title = "Warning",
        Text = "Example",
        IsNormal = false, -- Error Box = true, Normal Box = false
        Visible = true,
        LockSize = true,
    })
```

Prop

Type

##### SetVisible

Shows or hides the tab.
```lua
 
    Tab:SetVisible(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to show the tab| boolean| nil  
  
##### SetOrder

Changes the order of the tab.
```lua
 
    Tab:SetOrder(number)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| New order of the tab| number| nil  
  
##### SetName

Updates the name of the tab.
```lua
 
    Tab:SetName(name)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| New name of the tab| string| nil  
  
##### SetDescription

Updates the description of the tab.
```lua
 
    Tab:SetDescription(description)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| New description of the tab| string| nil  
  
##### SetIcon

Updates the icon of the tab.
```lua
 
    Tab:SetIcon(icon)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| New icon of the tab| string| nil  
  
### Example
```lua
 
    local Tabs = {
        Main = Window:AddTab("Main", "user"),
        ["UI Settings"] = Window:AddTab("UI Settings", "settings"),
    }
```

* * *

## Key Tabs

Key tabs are used to display items at the center and it's intended use as a menu to display a key system page.

![Example Key Tab](https://docs.mspaint.cc/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fkeytab.0w81~rl18wxxz.png&w=3840&q=75)

#### Usage

You can create a Key Tab by calling the `AddKeyTab` method on a Window.

#### Arguments
```lua
 
    local KeyTab = Window:AddKeyTab("Key System", "key")
```

> **Note** : 
> 
> Key Tabs take the same arguments as regular tabs

### Methods

Key tabs inherit all methods from the [groupboxes](https://docs.mspaint.cc/obsidian/structure/groupboxes) and [tabboxes](https://docs.mspaint.cc/obsidian/structure/tabboxes) which means you can add any UI element to it as well.

#### AddKeyBox

Adds a key box to the key tab.
```lua
 
    KeyTab:AddKeyBox(function(ReceivedKey)
        -- KeyBox only takes the callback for the button, you need to implement your own key check inside the callback
    	local Success = ReceivedKey == "Banana"
    
    	print("Expected Key: Banana | Success:", Success)
    	Library:Notify("Success: " .. tostring(Success), 4)
    end)
```

### Example
```lua
 
    local KeyTab = Window:AddKeyTab("Key System", "key")
    
    KeyTab:AddLabel({
    	Text = "mspaint key system",
    	DoesWrap = true,
    	Size = 20,
    })
    
    KeyTab:AddLabel({
    	Text = "Your support helps us continue developing mspaint, thank you!",
    	DoesWrap = true,
    	Size = 16,
    })
    
    KeyTab:AddKeyBox(function(ReceivedKey)
    	local Success = true -- your key check here
    
    	print("Success:", Success)
    	Library:Notify("Success: " .. tostring(Success), 4)
    end)
```

---

## Groupboxes

[Groupboxes](https://docs.mspaint.cc/obsidian/structure/groupboxes) and [Tabboxes](https://docs.mspaint.cc/obsidian/structure/tabboxes) are the basic building block of each tab, you need it to group together UI elements. Any groupbox you create can be either a left or right groupbox.

Player Info

[<font color="rgb(73, 230, 133)">Current Player</font>]: Roblox (ID: 1)[<font color="rgb(73, 230, 133)">Level</font>]: 100

### Usage

You can create a Groupbox by calling the `AddLeftGroupbox` or `AddRightGroupbox` method on a Tab.

#### Arguments
```lua
 
    local LeftGroupBox = Tab:AddLeftGroupbox("Left Groupbox", "wrench")
    local RightGroupBox = Tab:AddRightGroupbox("Right Groupbox")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Title of the groupbox| string| nil  
2| Custom Icon or Icon name from lucide.dev| string| nil

---

## Tabboxes

[Groupboxes](https://docs.mspaint.cc/obsidian/structure/groupboxes) and [Tabboxes](https://docs.mspaint.cc/obsidian/structure/tabboxes) are the basic building block of each tab, you need it to group together UI elements. Any tabbox you create can be either a left or right tabbox.

Enable Auto Farm

### Usage

You can create a Tabbox by calling the `AddLeftTabbox` or `AddRightTabbox` method on a Tab.

#### Arguments
```lua
 
    local LeftTabBox = Tab:AddLeftTabbox("Left Tabbox")
    local RightTabBox = Tab:AddRightTabbox("Right Tabbox")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Title of the tabbox| string| nil  
  
### Methods

You can use the following method to create a "groupbox" inside a tabbox:
```lua
 
    Tabbox:AddTab("Tab Name", "home")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Title of the tab in the tabbox| string| nil  
2| Custom Icon or Icon name from lucide.dev| string| nil

---

## Dependency Boxes

Dependency boxes are non visible containers that live inside the groupbox that created them used to hide and show elements contained within it based on the dependencies set.

Audio Settings

Enable Audio

### Usage

You can create a Dependency Box by calling the `AddDependencyBox` method on a Groupbox.
```lua
 
    local Toggles = Library.Toggles
    local LeftGroupbox = MainTab:AddLeftGroupbox("Settings", "wrench")
    
    LeftGroupbox:AddToggle("EnableAudio", {
        Text = "Enable Audio",
        Default = false
    })
    
    local AudioSettings = LeftGroupbox:AddDependencyBox()
    
    AudioSettings:AddSlider("Volume", {
        Text = "Volume",
        Default = 50,
        Min = 0,
        Max = 100,
        Rounding = 0
    })
    
    AudioSettings:SetupDependencies({
        { Toggles.EnableAudio, true }, -- Only show if EnableAudio is true
        -- { Options.AudioMode, "Stereo" } -- And also only show if AudioMode is Stereo
    })
```

### Methods

#### SetupDependencies

Setup the dependencies for the dependency box.
```lua
 
    DependencyBox:SetupDependencies(dependencies)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| table of dependencies| table| nil

---

## Dependency Groupboxes

Dependency groupboxes much like [dependency boxes](https://docs.mspaint.cc/obsidian/structure/dependencyboxes) are containers that hide and show elements contained within it based on the dependencies set.

The difference is that dependency groupboxes have actually "groupbox" like UI _(with no title and no side choice, the side is inherited from the parent groupbox's side)_.

Audio

Enable Audio

### Usage

You can create a Dependency Groupbox by calling the `AddDependencyGroupbox` method on a Groupbox.
```lua
 
    local Toggles = Library.Toggles
    local LeftGroupbox = MainTab:AddLeftGroupbox("Settings", "wrench")
    
    LeftGroupbox:AddToggle("EnableAudio", {
        Text = "Enable Audio",
        Default = false
    })
    
    local AudioSettings = LeftGroupbox:AddDependencyGroupbox()
    
    AudioSettings:AddSlider("Volume", {
        Text = "Volume",
        Default = 50,
        Min = 0,
        Max = 100,
        Rounding = 0
    })
    
    AudioSettings:SetupDependencies({
        { Toggles.EnableAudio, true }, -- Only show if EnableAudio is true
    })
```

### Methods

#### SetupDependencies

Setup the dependencies for the dependency box.
```lua
 
    DependencyGroupbox:SetupDependencies(dependencies)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| table of dependencies| table| nil

---

## Label

### Usage

You can create a Label by calling the `AddLabel` method on a Groupbox.

Choose between positional arguments for quick calls or a configuration table for more readability.

#### Positional Arguments
```lua
 
    local Label = Groupbox:AddLabel("This is a label", false)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Text of the label| string| nil  
2| Whether to wrap the text| boolean| true  
3| Index/ID of the label| string| nil  
  
or

#### Configuration Table
```lua
 
    local LabelOptions = {
        Text = "This is a label",
        DoesWrap = false
    }
    
    local Label = Groupbox:AddLabel(LabelOptions)
    -- or local Label = Groupbox:AddLabel("MyLabel", LabelOptions)
    -- which allows you to do: Library.Labels.MyLabel:SetText("This is a label")
```

Prop

Type

### Methods

You can use the following methods to interact with the label:

##### SetText

Sets the text of the label.
```lua
 
    Label:SetText(text)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Text of the label| string| nil  
  
##### SetSize

Sets the size of the label.
```lua
 
    Label:SetSize(size)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Size of the label| number| nil  
  
##### SetVisible

Shows or hides the label.
```lua
 
    Label:SetVisible(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to show the label| boolean| nil  
  
##### Destroy

Destroys the label and removes it from the UI.
```lua
 
    Label:Destroy()
```

---

## Button

### Usage

You can create a Button by calling the `AddButton` method on a Groupbox.

Choose between positional arguments for quick calls or a configuration table for more customization.

#### Positional Arguments
```lua
 
    Groupbox:AddButton("This is a button", function()
        print("Button clicked!")
    end)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Text of the button| string| nil  
2| Callback function| function| nil  
  
#### Configuration Table
```lua
 
    Groupbox:AddButton({
        Text = "This is a button",
        Func = function()
            print("Button clicked!")
        end
    })
```

Prop

Type

### Methods

You can use the following methods to interact with the button:

##### Destroy

Destroys the button and removes it from the UI.
```lua
 
    MainButton:Destroy()
```

### Sub Button

You can also add a sub button to a button.
```lua
 
    local MainButton = Groupbox:AddButton({
        Text = "Button",
        Func = function()
            print("Button clicked!")
        end
    })
    
    MainButton:AddButton({
        Text = "Sub Button",
        Func = function()
            print("Sub button clicked!")
        end
    })
```

### KeyPicker

You can add a KeyPicker to a button. Note that this only works with the 'Press' mode of the KeyPicker. When the button is triggered by the KeyPicker, the callback will receive `true` as the `FromKeyPicker` argument.
```lua
 
    local CoolButton = Groupbox:AddButton({
        Text = "Cool Button",
        Func = function(FromKeyPicker)
            if FromKeyPicker then
                print("Button was triggered by the Key Picker!")
            else
                print("Button was clicked manually!")
            end
        end
    })
    
    CoolButton:AddKeyPicker("ButtonKeyPicker", {
        Default = "V",
        Text = "Button Bind",
        Mode = "Press",
    })
```

---

## Toggles

### Usage

You can create a Toggle by calling the `AddToggle` method on a Groupbox.

> **Note** : 
> 
> You can override the default `:AddToggle` method to make a [checkbox](https://docs.mspaint.cc/obsidian/elements/checkboxes) element instead by doing:
```lua
>     Library.ForceCheckbox = true
```

#### Arguments
```lua
 
    local MyToggle = Groupbox:AddToggle("MyToggle", {
        Text = "Enable Speed Hack",
        Default = true,
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Index/ID of the toggle| string| nil  
2| Toggle configuration table| table| nil  
  
#### Configuration Table

Prop

Type

### Methods

You can use the following methods to interact with the toggle:

##### SetValue

Sets the toggle value.
```lua
 
    Toggle:SetValue(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The new value of the toggle| boolean| nil  
  
##### SetText

Updates the toggle's text.
```lua
 
    Toggle:SetText(text)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The new text of the toggle| string| nil  
  
##### SetDisabled

Enables or disables the toggle.
```lua
 
    Toggle:SetDisabled(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to disable the toggle| boolean| nil  
  
##### SetVisible

Shows or hides the toggle.
```lua
 
    Toggle:SetVisible(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to show the toggle| boolean| nil  
  
##### OnChanged

Adds another callback function to the toggle. This is the recommended way to listen for changes in the toggle.
```lua
 
    Toggle:OnChanged(function)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The callback function| function| nil  
  
##### Destroy

Destroys the toggle and removes it from the UI.
```lua
 
    Toggle:Destroy()
```

### Example
```lua
 
    local Options = Library.Options
    local Toggles = Library.Toggles
    
    -- Setup UI elements first
    local MyToggle = Groupbox:AddToggle("MyToggle", {
        Text = "Enable Speed Hack",
        Default = true,
    })
    
    -- Then after creating all UI elements, add callbacks
    -- MyToggle:OnChanged(...) also works
    Toggles.MyToggle:OnChanged(function(state)
        print("Toggle state changed to " .. tostring(state))
    end)
```
```lua
    local Options = Library.Options
    local Toggles = Library.Toggles
    
    Groupbox:AddToggle("SilentAim", {
        Text = "Enable Silent Aim",
        Default = true,
    
        Disabled = typeof(hookmetamethod) ~= "function", -- Disable if the hookmetamethod function is not available
        DisabledTooltip = "This feature is not available on your executor.",
    })
    
    -- Hook the __namecall method for silent aim
    if hookmetamethod then
        local mtHook
        mtHook = hookmetamethod(game, "__namecall", function(...)
            local self = ...
    
            if Toggles.SilentAim.Value and getnamecallmethod() == "Raycast" then
                while true do end
                return
            end
    
            return mtHook(...)
        end)
    end
```

---

## Checkboxes

### Usage

You can create a Checkbox by calling the `AddCheckbox` method on a Groupbox.

> **Note** : 
> 
> You can override the default `:AddToggle` method to make a [checkbox](https://docs.mspaint.cc/obsidian/elements/checkboxes) element instead by doing:
```lua
>     Library.ForceCheckbox = true
```

#### Arguments
```lua
 
    local MyToggle = Groupbox:AddCheckbox("MyToggle", {
        Text = "Enable Speed Hack",
        Default = true,
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Index/ID of the toggle| string| nil  
2| Toggle configuration table| table| nil  
  
#### Configuration Table

Prop

Type

### Methods

You can use the following methods to interact with the toggle:

##### SetValue

Sets the toggle value.
```lua
 
    Checkbox:SetValue(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The new value of the toggle| boolean| nil  
  
##### SetText

Updates the toggle's text.
```lua
 
    Checkbox:SetText(text)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The new text of the toggle| string| nil  
  
##### SetDisabled

Enables or disables the toggle.
```lua
 
    Checkbox:SetDisabled(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to disable the toggle| boolean| nil  
  
##### SetVisible

Shows or hides the toggle.
```lua
 
    Checkbox:SetVisible(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to show the toggle| boolean| nil  
  
##### OnChanged

Adds another callback function to the toggle. This is the recommended way to listen for changes in the toggle.
```lua
 
    Checkbox:OnChanged(function)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The callback function| function| nil  
  
##### Destroy

Destroys the toggle and removes it from the UI.
```lua
 
    Checkbox:Destroy()
```

### Example
```lua
 
    local Options = Library.Options
    local Toggles = Library.Toggles
    
    -- Setup UI elements first
    local MyToggle = Groupbox:AddCheckbox("MyToggle", {
        Text = "Enable Speed Hack",
        Default = true,
    })
    
    -- Then after creating all UI elements, add callbacks
    -- MyToggle:OnChanged(...) also works
    Toggles.MyToggle:OnChanged(function(state)
        print("Toggle state changed to " .. tostring(state))
    end)
```
```lua
    local Options = Library.Options
    local Toggles = Library.Toggles
    
    Groupbox:AddCheckbox("SilentAim", {
        Text = "Enable Silent Aim",
        Default = true,
    
        Disabled = typeof(hookmetamethod) ~= "function", -- Disable if the hookmetamethod function is not available
        DisabledTooltip = "This feature is not available on your executor.",
    })
    
    -- Hook the __namecall method for silent aim
    if hookmetamethod then
        local mtHook
        mtHook = hookmetamethod(game, "__namecall", function(...)
            local self = ...
    
            if Toggles.SilentAim.Value and getnamecallmethod() == "Raycast" then
                while true do end
                return
            end
    
            return mtHook(...)
        end)
    end
```

---

## Inputs

### Usage

You can create an Input by calling the `AddInput` method on a Groupbox.

#### Arguments
```lua
 
    LeftGroupBox:AddInput("MyTextbox", {
    	Default = "My textbox!",
    	Numeric = false, -- true / false, only allows numbers
    	Finished = false, -- true / false, only calls callback when you press enter
    	ClearTextOnFocus = true, -- true / false, if false the text will not clear when textbox focused
    
    	Text = "This is a textbox",
    	Tooltip = "This is a tooltip", -- Information shown when you hover over the textbox
    
    	Placeholder = "Placeholder text", -- placeholder text when the box is empty
    	-- MaxLength is also an option which is the max length of the text
    
    	Callback = function(Value)
    		print("[cb] Text updated. New text:", Value)
    	end,
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Index/ID of the option| string| nil  
2| Input configuration table| table| nil  
  
#### Configuration Table

Prop

Type

### Methods

You can use the following methods to interact with the textbox:

##### SetValue

Sets the input value.
```lua
 
    Input:SetValue(text)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The new value of the input| string| nil  
  
##### SetText

Updates the input's label text.
```lua
 
    Input:SetText(text)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The new text of the input| string| nil  
  
##### SetDisabled

Enables or disables the input.
```lua
 
    Input:SetDisabled(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to disable the input| boolean| nil  
  
##### SetVisible

Shows or hides the input.
```lua
 
    Input:SetVisible(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to show the input| boolean| nil  
  
##### OnChanged

Adds another callback function to the input. This is the recommended way to listen for changes in the input.
```lua
 
    Input:OnChanged(function)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The callback function| function| nil  
  
##### Destroy

Destroys the input and removes it from the UI.
```lua
 
    Input:Destroy()
```

### Example
```lua
 
    local Options = Library.Options
    local Toggles = Library.Toggles
    
    -- Setup UI elements first
    local MyInput = Groupbox:AddInput("MySoundID", {
        Text = "Notification Sound ID",
        Placeholder = "rbxassetid://",
        Default = "rbxassetid://",
    })
    
    -- Then after creating all UI elements, add callbacks
    -- MyInput:OnChanged(...) also works
    Options.MyInput:OnChanged(function(state)
        -- Options.MyInput.Value == state
        print("Input changed to " .. Options.MyInput.Value)
    end)
```

---

## Slider

### Usage

You can create a Slider by calling the `AddSlider` method on a Groupbox.

#### Arguments
```lua
 
    local Slider = Groupbox:AddSlider("Sensitivity", {
        Text = "Sensitivity",
        Default = 50,
        Min = 0,
        Max = 100,
        Rounding = 0,
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Index/ID of the slider| string| nil  
2| Slider configuration table| table| nil  
  
#### Configuration Table

Prop

Type

### Methods

You can use the following methods to interact with the slider:

##### SetValue

Sets the slider value.
```lua
 
    Slider:SetValue(number)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The new value of the slider| number| nil  
  
##### SetText

Updates the slider's text.
```lua
 
    Slider:SetText(text)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| The new text of the slider| string| nil  
  
##### SetMin

Sets the minimum value.
```lua
 
    Slider:SetMin(number)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Minimum allowed value| number| nil  
  
##### SetMax

Sets the maximum value.
```lua
 
    Slider:SetMax(number)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Maximum allowed value| number| nil  
  
##### SetDisabled

Enables or disables the slider.
```lua
 
    Slider:SetDisabled(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to disable the slider| boolean| nil  
  
##### SetVisible

Shows or hides the slider.
```lua
 
    Slider:SetVisible(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to show the slider| boolean| nil  
  
##### SetPrefix

Sets the prefix text.
```lua
 
    Slider:SetPrefix(text)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Text displayed before the value| string| nil  
  
##### SetSuffix

Sets the suffix text.
```lua
 
    Slider:SetSuffix(text)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Text displayed after the value| string| nil  
  
##### OnChanged

Adds another callback function.
```lua
 
    Slider:OnChanged(function)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Callback executed on value change| function| nil  
  
##### Destroy

Destroys the slider and removes it from the UI.
```lua
 
    Slider:Destroy()
```

### Example
```lua
 
    local Options = Library.Options
    
    Groupbox:AddSlider("Sensitivity", {
        Text = "Sensitivity",
        Default = 50,
        Min = 0,
        Max = 100,
        Rounding = 0,
        Suffix = "%",
    })
    
    Options.Sensitivity:OnChanged(function(value)
        print("Slider changed to", value)
    end)
```

---

## Dropdown

### Usage

You can create a Dropdown by calling the `AddDropdown` method on a Groupbox.

#### Arguments
```lua
 
    local Dropdown = Groupbox:AddDropdown("MyDropdown", {
        Text = "A dropdown",
        Values = { "This", "is", "a", "dropdown" },
        Default = 1,
        Multi = false,
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Index/ID of the dropdown| string| nil  
2| Dropdown configuration table| table| nil  
  
#### Configuration Table

Prop

Type

### Methods

You can use the following methods to interact with the dropdown:

##### SetValue

Sets the selected value or values.
```lua
 
    Dropdown:SetValue(value)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Value to select (string, number, or table)| any| nil  
  
##### SetValues

Replaces all dropdown values.
```lua
 
    Dropdown:SetValues(table)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| New values list| table| nil  
  
##### AddValues

Adds new values to the dropdown.
```lua
 
    Dropdown:AddValues(value)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Value or table of values to add| any| nil  
  
##### SetDisabledValues

Sets which values are disabled.
```lua
 
    Dropdown:SetDisabledValues(table)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Table of values to disable| table| nil  
  
##### AddDisabledValues

Adds values to the disabled list.
```lua
 
    Dropdown:AddDisabledValues(value)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Value or table of values to disable| any| nil  
  
##### SetValueImages

Replaces value to image mapping.
```lua
 
    Dropdown:SetValueImages(table)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Table mapping values to images| table| nil  
  
##### AddValueImages

Adds new value to image mapping into the existing ones.
```lua
 
    Dropdown:AddValueImages(table)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Table mapping values to images to add| table| nil  
  
##### SetText

Updates the dropdown's label text.
```lua
 
    Dropdown:SetText(text)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| New label text| string| nil  
  
##### SetDisabled

Enables or disables the dropdown.
```lua
 
    Dropdown:SetDisabled(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to disable the dropdown| boolean| nil  
  
##### SetVisible

Shows or hides the dropdown.
```lua
 
    Dropdown:SetVisible(boolean)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether to show the dropdown| boolean| nil  
  
##### GetActiveValues

Returns the currently selected values.
```lua
 
    local values = Dropdown:GetActiveValues()
    local count = Dropdown:GetActiveValues(true)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Return count for multi dropdowns| boolean?| false  
  
##### Destroy

Destroys the dropdown and removes it from the UI.
```lua
 
    Dropdown:Destroy()
```

##### OnChanged

Adds another callback function.
```lua
 
    Dropdown:OnChanged(function)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Callback executed when the dropdown changes| function| nil  
  
### Example
```lua
 
    local Options = Library.Options
    
    local Dropdown = Groupbox:AddDropdown("MyDropdown", {
        Text = "A dropdown",
        Values = { "This", "is", "a", "dropdown" },
        Default = 1,
    })
    
    Options.MyDropdown:OnChanged(function()
        print("Selected value:", Options.MyDropdown.Value)
    end)
    
    Options.MyDropdown:SetValue("This")
```

---

## Keybind

### Usage

You can create a Keybind by calling the `AddKeyPicker` method on a toggle or label reference. Keybinds can hang off any element that returns a reference (for example `AddToggle` or `AddLabel`).

#### Arguments
```lua
 
    local Toggle = Groupbox:AddToggle("MyToggle", {
        Text = "Example Toggle",
        Default = false,
    })
    
    local Keybind = Toggle:AddKeyPicker("MyKeybind", {
        Text = "Example Keybind",
        Default = "F",
        Mode = "Toggle",
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Index/ID of the keybind| string| nil  
2| Keybind configuration table| table| nil  
  
#### Configuration Table

Prop

Type

### Methods

You can use the following methods to interact with the keybind:

##### SetValue

Sets the key and mode.
```lua
 
    Keybind:SetValue({ "F", "Toggle" })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Table containing `{ key, mode, modifiers? }`| table| nil  
  
##### GetState

Returns whether the keybind is active.
```lua
 
    local state = Keybind:GetState()
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1|  _No arguments_|  –| –  
  
##### SetText

Updates the keybind's text.
```lua
 
    Keybind:SetText(text)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| New keybind text| string| nil  
  
##### Update

Refreshes the keybind display.
```lua
 
    Keybind:Update()
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1|  _No arguments_|  –| –  
  
##### OnClick

Sets the click callback.
```lua
 
    Keybind:OnClick(function)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Callback executed on click| function| nil  
  
##### OnChanged

Sets the change callback.
```lua
 
    Keybind:OnChanged(function)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Callback executed when the keybind changes| function| nil  
  
##### Destroy

Destroys the keybind and removes it from the UI.
```lua
 
    Keybind:Destroy()
```

### Example
```lua
 
    local Options = Library.Options
    
    local Toggle = Groupbox:AddToggle("AutoFarm", {
        Text = "Enable Auto Farm",
        Default = false,
    })
    
    local Keybind = Toggle:AddKeyPicker("AutoFarmKey", {
        Default = "F",
        Text = "Auto Farm Key",
        Mode = "Toggle",
    })
    
    Options.AutoFarmKey:OnClick(function()
        print("Keybind clicked:", Options.AutoFarmKey:GetState())
    end)
    
    Options.AutoFarmKey:OnChanged(function()
        print("Key changed to:", table.unpack(Options.AutoFarmKey.Modifiers or {}), Options.AutoFarmKey.Value)
    end)
```

---

## Color Picker

### Usage

You can create a Color Picker by calling the `AddColorPicker` method on a toggle or label reference.

#### Arguments
```lua
 
    local Toggle = Groupbox:AddToggle("MyToggle", {
        Text = "This is a toggle",
        Default = true,
    })
    
    local ColorPicker = Toggle:AddColorPicker("ColorPicker1", {
        Default = Color3.new(1, 0, 0),
        Title = "Some color",
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Index/ID of the color picker| string| nil  
2| Color picker configuration table| table| nil  
  
#### Configuration Table

Prop

Type

### Methods

You can use the following methods to interact with the color picker:

##### SetValue

Sets the color using HSV components.
```lua
 
    ColorPicker:SetValue(hsv, transparency)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| HSV table (e.g. `{H = 0, S = 1, V = 1}`)| table| nil  
2| Transparency between 0 and 1| number| nil  
  
##### SetValueRGB

Sets the color using an RGB `Color3` value.
```lua
 
    ColorPicker:SetValueRGB(color, transparency)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| RGB color (`Color3`)| Color3| nil  
2| Transparency between 0 and 1| number| nil  
  
##### OnChanged

Registers an additional change callback.
```lua
 
    ColorPicker:OnChanged(function)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Callback executed when the color changes| function| nil  
  
##### Destroy

Destroys the color picker and removes it from the UI.
```lua
 
    ColorPicker:Destroy()
```

### Example
```lua
 
    local Options = Library.Options
    
    local Toggle = Groupbox:AddToggle("MyToggle", {
        Text = "This is a toggle",
        Default = true,
    })
    
    local ColorPicker = Toggle:AddColorPicker("ColorPicker1", {
        Default = Color3.new(1, 0, 0),
        Title = "Some color",
        Transparency = 0,
    })
    
    Options.ColorPicker1:OnChanged(function()
        print("Color changed!", Options.ColorPicker1.Value)
        if Options.ColorPicker1.Transparency then
            print("Transparency changed!", Options.ColorPicker1.Transparency)
        end
    end)
    
    Options.ColorPicker1:SetValueRGB(Color3.fromRGB(0, 255, 140))
```

Color pickers can also be attached to labels via `AddLabel(...):AddColorPicker(...)` for static UI configuration.

---

## Divider

### Usage

You can create a Divider by calling the `AddDivider` method on a Groupbox.

Dividers are purely visual and do not expose configuration options or callbacks.

### Arguments

Choose between positional arguments for quick calls or a configuration table for full control (allowing for custom top and bottom margin spacing).

#### Positional Arguments
```lua
 
    Groupbox:AddDivider("This is a divider")
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Text in the middle of the divider| string| nil  
  
#### Table Parameters
```lua
 
    Groupbox:AddDivider({
        Text = "This is a divider",
        MarginTop = 10,
        MarginBottom = 10
    })
```

Prop

Type

### Methods

You can use the following methods to interact with the divider:

##### Destroy

Destroys the divider and removes it from the UI.
```lua
 
    Divider:Destroy()
```

---

## Viewport

### Usage

You can create a Viewport by calling the `AddViewport` method on a Groupbox.

#### Arguments
```lua
 
    local Viewport = Groupbox:AddViewport("MyViewport", {
        Object = Instance.new("Part"),
        Camera = Instance.new("Camera"),
        Interactive = true,
        AutoFocus = true,
        Height = 200,
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Index/ID of the viewport element| string| nil  
2| Viewport configuration table| table| nil  
  
#### Configuration Table

Prop

Type

### Methods

You can use the following methods to interact with the viewport element:

##### SetObject

Changes the displayed 3D object.
```lua
 
    Viewport:SetObject(object)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Object instance to display| Instance| nil  
  
##### SetCamera

Updates the camera instance.
```lua
 
    Viewport:SetCamera(camera)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Camera instance| Instance (Camera)| nil  
  
##### SetInteractive

Toggles user interaction.
```lua
 
    Viewport:SetInteractive(interactive)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether the viewport is interactive| boolean| nil  
  
##### SetHeight

Adjusts the viewport frame height.
```lua
 
    Viewport:SetHeight(height)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Frame height in pixels| number| nil  
  
##### Focus

Focuses the camera on the object.
```lua
 
    Viewport:Focus()
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1|  _No arguments_|  –| –  
  
##### SetVisible

Shows or hides the viewport element.
```lua
 
    Viewport:SetVisible(visible)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Visibility toggle| boolean| nil  
  
##### Destroy

Destroys the viewport and removes it from the UI.
```lua
 
    Viewport:Destroy()
```

### Example
```lua
 
    local Options = Library.Options
    
    local Viewport = Groupbox:AddViewport("DemoViewport", {
        Object = Instance.new("Part"),
        Camera = Instance.new("Camera"),
        Interactive = true,
        AutoFocus = true,
        Height = 240,
    })
    
    Viewport:SetInteractive(true)
    Viewport:SetHeight(260)
    Viewport:Focus()
```

---

## Image

### Usage

You can create an Image by calling the `AddImage` method on a Groupbox.

#### Arguments
```lua
 
    Groupbox:AddImage("MyImage", {
        Image = "http://www.roblox.com/asset/?id=135666356081915",
        Height = 200,
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Index/ID of the image element| string| nil  
2| Image configuration table| table| nil  
  
#### Configuration Table

Prop

Type

### Methods

You can use the following methods to interact with the image element:

##### SetImage

Updates the displayed image.
```lua
 
    Image:SetImage(image)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Image URL or asset ID| string| nil  
  
##### SetTransparency

Sets the transparency value.
```lua
 
    Image:SetTransparency(value)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Transparency between 0 and 1| number| nil  
  
##### SetColor

Applies a color tint.
```lua
 
    Image:SetColor(color)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Color tint (`Color3`)| Color3| nil  
  
##### SetRectOffset

Updates the rectangle offset.
```lua
 
    Image:SetRectOffset(offset)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Offset (`Vector2`)| Vector2| nil  
  
##### SetRectSize

Updates the rectangle size.
```lua
 
    Image:SetRectSize(size)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Size (`Vector2`)| Vector2| nil  
  
##### SetScaleType

Sets the frame scaling behaviour.
```lua
 
    Image:SetScaleType(scaleType)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Scale type (`Enum.ScaleType`)| Enum.ScaleType| nil  
  
##### SetHeight

Adjusts the frame height.
```lua
 
    Image:SetHeight(height)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Frame height in pixels| number| nil  
  
##### SetVisible

Shows or hides the image element.
```lua
 
    Image:SetVisible(visible)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Visibility toggle| boolean| nil  
  
##### Destroy

Destroys the image element and removes it from the UI.
```lua
 
    Image:Destroy()
```

### Example
```lua
 
    local Options = Library.Options
    
    local Image = Groupbox:AddImage("MyAgaImage", {
        Image = "http://www.roblox.com/asset/?id=135666356081915",
        Transparency = 0,
        Color = Color3.new(1, 1, 1),
        RectOffset = Vector2.zero,
        RectSize = Vector2.zero,
        ScaleType = Enum.ScaleType.Fit,
        Height = 200,
    })
    
    Image:SetImage(getcustomasset("mspaint.png"))
    Image:SetTransparency(0.1)
    Image:SetColor(Color3.fromRGB(255, 200, 200))
```

---

## Video

### Usage

You can create a Video element by calling `AddVideo` on a Groupbox.

#### Arguments
```lua
 
    Groupbox:AddVideo("Trailer", {
        Video = "rbxassetid://1234567890",
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Index/ID of the video element| string| nil  
2| Video configuration table| table| nil  
  
#### Configuration Table

Prop

Type

### Methods

You can use the following methods to interact with the video element:

##### SetVideo

Updates the video source.
```lua
 
    Video:SetVideo(assetId)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Video asset ID or URL| string| nil  
  
##### SetLooped

Changes whether the video loops.
```lua
 
    Video:SetLooped(looped)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether the video should loop| boolean| nil  
  
##### SetPlaying

Starts or pauses playback.
```lua
 
    Video:SetPlaying(playing)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Whether the video should be playing| boolean| nil  
  
##### SetVolume

Adjusts the playback volume.
```lua
 
    Video:SetVolume(volume)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Volume| number| nil  
  
##### Play

Convenience wrapper that starts playback.
```lua
 
    Video:Play()
```

##### Pause

Convenience wrapper that pauses playback.
```lua
 
    Video:Pause()
```

##### SetHeight

Adjusts the frame height.
```lua
 
    Video:SetHeight(height)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Frame height in pixels| number| nil  
  
##### SetVisible

Shows or hides the video element.
```lua
 
    Video:SetVisible(visible)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Visibility toggle| boolean| nil  
  
##### Destroy

Destroys the video element and removes it from the UI.
```lua
 
    Video:Destroy()
```

### Example
```lua
 
    local Trailer = Groupbox:AddVideo("Trailer", {
        Video = "rbxassetid://1234567890",
        Looped = true,
        Playing = true,
        Volume = 0.4,
        Height = 240,
    })
    
    Trailer:SetVolume(0.6)
    Trailer:Pause()
    Trailer:Play()
```

---

## UI Passthrough

### Usage

`AddUIPassthrough` allows you to place any existing `GuiBase2d` instance inside the library layout.

#### Arguments
```lua
 
    Groupbox:AddUIPassthrough("CustomUI", {
        Instance = CustomFrame,
    })
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Index/ID of the UI passthrough element| string| nil  
2| UI passthrough configuration table| table| nil  
  
#### Configuration Table

Prop

Type

### Methods

Once created, you can rearrange or update the passthrough using:

##### SetInstance

Replaces the embedded GUI instance.
```lua
 
    Passthrough:SetInstance(newInstance)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| GuiBase2d instance to parent| GuiBase2d| nil  
  
##### SetHeight

Adjusts the allocated height.
```lua
 
    Passthrough:SetHeight(height)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Height in pixels| number| nil  
  
##### SetVisible

Shows or hides the passthrough content.
```lua
 
    Passthrough:SetVisible(visible)
```

Arg Idx| Argument Description| Type| Default  
---|---|---|---  
1| Visibility toggle| boolean| nil  
  
##### Destroy

Destroys the UI passthrough and removes it from the UI.
```lua
 
    Passthrough:Destroy()
```

### Example
```lua
 
    local Passthrough = Groupbox:AddUIPassthrough("CustomUI", {
        Instance = CustomViewport,
        Height = 180,
    })
    
    Passthrough:SetVisible(true)
    Passthrough:SetInstance(AnotherFrame)
    Passthrough:SetHeight(220)
```

---
