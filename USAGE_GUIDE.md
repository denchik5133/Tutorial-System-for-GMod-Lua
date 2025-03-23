# Tutorial System Guide for GMod Lua

## Table of Contents
1. [System Overview](#system-overview)
2. [Installation](#installation)
3. [Basic Functions](#basic-functions)
4. [Creating Tutorials](#creating-tutorials)
5. [API](#api)
6. [Usage Examples](#use-examples)
7. [Extension-Functionality](#extension-functionality)
8. [Troubleshooting](#troubleshooting-problems)

## System Overview

Tutorial System is a flexible, developer-friendly system for creating interactive step-by-step tutorials (tutorials) for any addon in Garry's Mod. The system allows you to create tutorials with customizable steps, walkthrough conditions, and visual cues.

#### Key Features:
- Creating multi-step tutorials with progress of execution
- Conditions for transition between steps
- Interface elements highlighting
- Saving progress between sessions
- Support for categories to organize tutorials
- Extensive API for integration into your addons
- Customizable user interface

## Installation

1. Copy the `lua` folder to the directory of your server or addon
2. The system will automatically load when you start the server via the `lua/autorun/tutorials_init.lua` file
3. To access the tutorials, players can use the command `!tutorials` in chat or `tutorials` in the console

## Basic Functions

### Commands for players:
- `!tutorials` or `tutorials` - open the tutorials menu
- `tutorials_list` - show the list of available tutorials in the console
- `tutorials <id>` - open a specific tutorial by its ID
- `tutorials_reset <id>` - reset progress for a specific tutorial
- `tutorials_reset_all` - reset progress for all tutorials
- `tutorials_exit` - exit current tutorials

### Tutorials interface:
- The tutorials menu displays available tutorials grouped by category
- For each tutorial, the title, description, and completion percentage are shown
- The tutorial interface includes navigation buttons (Next, Back, Exit)
- Progress bar shows current step and total number of steps

### UI Modes:
The system supports different UI modes, customizable via the `UIMode` parameters and additional settings in the settings tutorial:

- **Standard** - Displays the full interface with window, navigation buttons and HUD. Suitable for normal tutorials.
- **Minimal (minimal)** - Displays only the HUD with step text and progress, no window. Ideal for interactive tutorials that require active player actions.
- **InteractiveMode** - Special mode for fully interactive tutorials, displays only the HUD with no user interface, all actions are performed in-game.

### Classic and modern interface

The system supports two different UI styles:

- **Classical UI** - Basic UI with simple elements
- **Modern UI** - Enhanced UI with animations, gradients, shadows and effects

The interface selection is configured in the configuration file via the `UseModernUI` parameter:

```lua
DDI.TutorialSystem.Config.UseModernUI = true -- Use modern interface (default)
```

### Customizing UI modes:
```lua
settings = {
    -- Main UI modes
    UIMode = 'standard',        -- standard mode with window and buttons (default)
    -- OR
    UIMode = 'minimal',         -- minimal mode with HUD only
    
    -- Additional UI settings
    InteractiveMode = true,     -- fully interactive mode (game interaction only)
    ShowHUD = true,             -- whether to show the HUD on the screen
    AutoAdvance = true,         -- automatically move to the next step when a condition is met
    
    -- Modern UI settings
    CompactMode = false,        -- compact display mode (smaller element sizes)
    AnimateUI = true,           -- use interface animations
    ShowKeyHints = true,        -- show key hints
    PlaySounds = true           -- play sounds when interacting
}
```

The choice of UI mode depends on the type of tutorial:

1. **Ordinary windowed tutorial**: `UIMode = 'standard'` (default).
2. **Minimal tutorial with HUD**: `UIMode = 'minimal'`.
3. **Fully interactive tutorial**: `InteractiveMode = true, ShowHUD = true`.
4. **Hidden tutorial with control only**: `InteractiveMode = true, ShowHUD = false`.
5. **Compact Tutorial**: `CompactMode = true, UIMode = 'standard'`.

### UI element highlighting

The modern UI supports advanced highlighting of UI elements with animations and effects:

```lua
-- In the steps of the tutorial, you can use highlight for the element ID
{
    text = 'Pay attention to this button',
    highlight = 'button_id'     -- Item ID for backlighting
}

-- Or programmatic highlighting of any element
local highlight = DDI.TutorialSystem:HighlightElement(panel, duration)
```

The `DDI.TutorialSystem:HighlightElement()` function creates an animated highlight around the specified panel element with ripple and gradient effects that catch the player's attention.

## Interface modes

Tutorial System offers several interface modes that allow you to create different types of tutorials.

### Standard mode (Standard)

Standard mode displays a full window with tutorial text and navigation buttons. The user can press the 'Next' and 'Back' buttons to move between steps.

```lua
settings = {
    UIMode = 'standard',    -- Standard interface mode
    ShowHUD = true,         -- Whether to show HUD with information
}
```

### Minimal mode (Minimal)

In minimal mode, the tutorial window is not displayed and information is only shown through the HUD on the screen. This mode is more suitable for interactive tutorials.

```lua
settings = {
    UIMode = 'minimal',     -- Minimum interface mode
    ShowHUD = true,         -- Be sure to enable HUD
}
```

### Interactive mode

In the fully interactive mode, the navigation buttons are not used, and the transition between steps takes place only when conditions are met.

```lua
settings = {
    InteractiveMode = true,  -- Fully interactive mode
    AutoAdvance = true,      -- Automatically jump when conditions are met
    ShowHUD = true,          -- Show HUD with information about the current step
    UIMode = 'minimal'       -- Usually used with minimal UI mode
}
```

This mode is great for natural learning, where the player simply performs actions in the game and the system automatically tracks progress.

## Creating Tutorials

To create your own tutorial, you need to:

1. Create a file for your tutorial (for example, `lua/tutorials/examples/my_addon_tutorial.lua`)
2. Define the tutorial as a table with the required fields
3. Register the tutorial via API

### An example of a basic tutorial:

```lua
local myTutorial = {
    id = 'my_addon_tutorial',
    title = 'My first tutorial',
    description = 'Learn how to use my addon',
    category = 'My Addon',
    author = 'Your name',
    icon = 'icon16/book.png',
    
    settings = {
        Skippable = true,
        Closable = true,
    },
    
    steps = {
        {
            text = 'Welcome to my addon! Click "Next" to continue.',
        },
        {
            text = 'This is the second step of the tutorial. Here you can tell about the function of the addon.',
        },
        {
            text = 'You have completed the tutorial! Now you know how to use my addon.',
        }
    }
}

DDI.TutorialSystem.API:Register(myTutorial)
```

### Tutorial structure:

- **Required fields:**
  - `id` - unique identifier of the tutorial
  - `title` - displayed title
  - `description` - detailed description
  - `steps` - array of tutorial steps

- ** Optional fields:**
  - `category` - tutorial category (by default 'General')
  - `author` - author's name (by default 'Unknown')
  - `icon` - path to the icon (by default 'icon16/book.png')
  - `settings` - tutorial settings (see below)
  - `onComplete` - function to be called when the tutorial is finished.

- **Tutorial settings:**
  - **General settings:**
    - `Skippable` - whether you can skip steps (bool)
    - `Closable` - whether you can close the tutorial without completing it (bool)
    - `SaveProgress` - whether to save progress between sessions (bool)
    - `RetakeTutorials` - allow retaking tutorials (bool)
    - `AutoAdvance` - automatic transition when conditions are met (bool)
    - `HighlightElements` - whether to highlight interface elements (bool)
    
  - **User interface settings:**
    - `UIMode` - user interface mode ('standard' or 'minimal')
    - `InteractiveMode` - enable fully interactive mode without UI (bool)
    - `ShowHUD` - show HUD with step text on the screen (bool)

- **Structure of step (steps[i]):**
  - `text` - instruction text (mandatory)
  - `action` - action when entering a step (function or string with console command)
  - `condition` - function that returns true when the step is executed
  - `highlight` - interface element for highlighting (string or panel)

## API

### Basic API Methods:

```lua
-- Tutorial registration
DDI.TutorialSystem.API:Register(tutorialData)

-- Getting the list of tutorials
DDI.TutorialSystem.API:GetList(categoryFilter)

-- Getting the list of categories
DDI.TutorialSystem.API:GetCategories()

-- Running the tutorial
DDI.TutorialSystem.API:Start(tutorialId)

-- Check if the tutorial is complete
DDI.TutorialSystem.API:IsCompleted(tutorialId, player)

-- Getting progress on the tutorial
DDI.TutorialSystem.API:GetProgress(tutorialId, player)

-- Reset tutorial progress
DDI.TutorialSystem.API:ResetProgress(tutorialId, player)

-- Reset all tutorials
DDI.TutorialSystem.API:ResetAllTutorials(player)

-- Exit the current tutorial
DDI.TutorialSystem.API:ExitCurrent()

-- Check if the player is in a tutorial
DDI.TutorialSystem.API:IsPlayer(player)

-- Adding a step to an existing tutorial
DDI.TutorialSystem.API:AddStep(tutorialId, stepData, position)

-- Deleting a step from a tutorial
DDI.TutorialSystem.API:RemoveStep(tutorialId, position)

-- Changing a step in a tutorial
DDI.TutorialSystem.API:ModifyStep(tutorialId, position, newStepData)

-- Opening the tutorials menu (client only)
DDI.TutorialSystem.API:OpenMenu()
```

#### Hooky:

The system provides several hooks that you can use to integrate with your addon:

```lua
-- Called when the tutorial system is fully initialized
hook.Add('DDI.TutorialSystem.Initialized', 'MyAddon_TutorialInit', function()
    -- Your code is here
end)

-- Called when the API of the tutorial system is ready to be used
hook.Add('DDI.TutorialSystem.APIReady', 'MyAddon_APIReady', function(api)
    -- api contains a reference to DDI.TutorialSystem.API
end)

-- Called when registering a tutorial
hook.Add('DDI.TutorialSystem.Registered', 'MyAddon_TutorialRegistered', function(tutorialId, tutorialData)
    -- Your code is here
end)

-- Called when the tutorial is launched
hook.Add('DDI.TutorialSystem.Started', 'MyAddon_TutorialStarted', function(tutorialId, state)
    -- state contains information about the status of the tutorial
end)

-- Called when changing a step
hook.Add('DDI.TutorialSystem.tepChanged', 'MyAddon_StepChanged', function(tutorialId, stepNumber)
    -- Your code is here
end)

-- Called when the tutorial is finished
hook.Add('DDI.TutorialSystem.Completed', 'MyAddon_TutorialCompleted', function(tutorialId)
    -- Your code is here
end)

-- Called when exiting the tutorial without completing it
hook.Add('DDI.TutorialSystem.Exited', 'MyAddon_TutorialExited', function(tutorialId, lastStep)
    -- Your code is here
end)
```

## Examples of Usage

### Tutorial with minimal UI mode:

```lua
local minimalUITutorial = {
    id = 'minimal_ui_tutorial',
    title = 'Active game tutorial',
    description = 'In-game tutorial with minimal interface',
    
    settings = {
        UIMode = 'minimal',     -- Set the minimum UI mode
        AutoAdvance = true,     -- Be sure to enable automatic promotion
        Closable = true,        -- Ability to exit the tutorial
        SaveProgress = true     -- Save progress
    },
    
    steps = {
        {
            text = 'Welcome to the active game tutorial! Jump in to continue.',
            action = function()
                -- Play a sound to attract attention
                surface.PlaySound('buttons/button17.wav')
                
                -- Show notification
                notification.AddLegacy('Press the SPACE key to jump', NOTIFY_HINT, 5)
            end,
            condition = function()
                -- Check to see if the player has jumped
                return LocalPlayer():KeyPressed(IN_JUMP)
            end
        },
        {
            text = 'All right! Now pick up the weapon by pressing the 1 key.',
            action = function()
                notification.AddLegacy('Press 1 to select a weapon', NOTIFY_HINT, 5)
            end,
            condition = function()
                -- Check if the first weapon is selected
                return LocalPlayer():GetActiveWeapon() and LocalPlayer():GetActiveWeapon():GetSlot() == 0
            end
        },
        {
            text = 'Move forward for 5 seconds.',
            action = function()
                -- Create a timer to track the movement
                if (not DDI.TutorialSystem.TempData) then 
                    DDI.TutorialSystem.TempData = {} 
                end
                
                DDI.TutorialSystem.TempData.MovementTime = 0
                DDI.TutorialSystem.TempData.IsMoving = false
                
                -- Create a timer to check the movement
                hook.Add('Think', 'TutorialMovementCheck', function()
                    if input.IsKeyDown(KEY_W) then
                        if (not DDI.TutorialSystem.TempData.IsMoving) then
                            DDI.TutorialSystem.TempData.IsMoving = true
                            DDI.TutorialSystem.TempData.StartTime = CurTime()
                        else
                            DDI.TutorialSystem.TempData.MovementTime =  CurTime() - DDI.TutorialSystem.TempData.StartTime
                        end
                    else
                        DDI.TutorialSystem.TempData.IsMoving = false
                    end
                end)
            end,
            condition = function()
                -- Checking the duration of the movement
                return DDI.TutorialSystem.TempData and DDI.TutorialSystem.TempData.MovementTime and DDI.TutorialSystem.TempData.MovementTime > 5
            end
        },
        {
            text = 'Congratulations! You have completed the game mode training.',
            action = function()
                -- Remove the motion check hook
                hook.Remove('Think', 'TutorialMovementCheck')
                
                -- Play the completion sound
                surface.PlaySound('buttons/button3.wav')
                
                -- Show completion notification
                notification.AddLegacy('Туториал завершен!', NOTIFY_GENERIC, 5)
            end
        }
    }
}

DDI.TutorialSystem.API:Register(minimalUITutorial)
```

### Interactive tutorial with conditions:

```lua
local interactiveTutorial = {
    id = 'my_addon_interactive',
    title = 'Interactive Tutorial',
    description = 'Tutorial with interactive elements',
    
    settings = {
        AutoAdvance = true, -- Enable automatic transition
    },
    
    steps = {
        {
            text = 'Welcome! Press the space bar to continue.',
            condition = function()
                return input.IsKeyDown(KEY_SPACE)
            end
        },
        {
            text = 'All right! Now press the E key.',
            condition = function()
                return input.IsKeyDown(KEY_E)
            end
        },
        {
            text = 'Now open the menu by pressing F4.',
            condition = function()
                return input.IsKeyDown(KEY_F4)
            end
        },
        {
            text = 'Congratulations! You have successfully completed the interactive tutorial.',
        }
    }
}

DDI.TutorialSystem.API:Register(interactiveTutorial)
```

### Tutorial with highlighting of interface elements:

```lua
-- Let's assume we have an addon menu
local menu = vgui.Create('DFrame')
menu:SetSize(400, 300)
menu:SetTitle("My addon's menu")
menu:Center()

-- Create a button to be highlighted
local button = vgui.Create('DButton', menu)
button:SetPos(10, 30)
button:SetSize(100, 30)
button:SetText('Important button')
button.TutorialID = 'important_button' -- ID for highlighting

-- Tutorial to illuminate this button
local highlightTutorial = {
    id = 'highlight_tutorial',
    title = 'Element illumination',
    description = 'Learn how to use important menu items',
    
    steps = {
        {
            text = 'Open the addon menu.',
            action = function()
                -- Open the menu when you go to this step
                menu:MakePopup()
            end
        },
        {
            text = 'Note the important backlit button.',
            highlight = 'important_button' -- Item ID for highlighting
        },
        {
            text = 'Click on the button to continue.',
            highlight = 'important_button',
            condition = function()
                -- Check if the button was pressed
                return button.WasClicked or false
            end
        },
        {
            text = "All right! You've learned how to use an important feature.",
        }
    }
}

-- Add button click tracking
button.DoClick = function()
    button.WasClicked = true
    -- Your code for the button
end

DDI.TutorialSystem.API:Register(highlightTutorial)
```

### Automatically start the tutorial for new players:

```lua
if SERVER then
    hook.Add('PlayerInitialSpawn', 'MyAddon_AutoTutorial', function(player)
        timer.Simple(5, function()
            if IsValid(player) then
                -- Check if the player has gone through the tutorial
                if (not DDI.TutorialSystem.API:IsCompleted('my_addon_basics', player)) then
                    -- Run the tutorial
                    net.Start('TutorialSystem_AutoTrigger')
                    net.WriteString('my_addon_basics')
                    net.Send(player)
                    
                    -- Send a message to the player
                    player:ChatPrint("We've launched a tutorial for you!")
                end
            end
        end)
    end)
end
```

## Expanded functionality

### Add your own actions:

```lua
-- Add support for our specific actions
function MyAddon.ExecuteSpecialAction(actionType, actionData)
    if actionType == 'give_item' then
        -- Code for issuing an item
        return true
    elseif actionType == 'teleport' then
        -- Code for teleportation
        return true
    end
    return false
end

-- Integrate with the tutorial system
hook.Add('DDI.TutorialSystem.StepChanged', 'MyAddon_CustomActions', function(tutorialId, stepNumber)
    local tutorial = DDI.TutorialSystem.API:GetList()[tutorialId]
    if (not tutorial) then return end
    
    local step = tutorial.steps[stepNumber]
    if (not step or not step.customAction) then return end
    
    -- Performing our own action
    MyAddon.ExecuteSpecialAction(step.customAction.type, step.customAction.data)
end)
```

### Creating your own UI for tutorials:

The tutorial system uses the standard Derma UI, but you can create your own interface using the API:

```lua
-- Hook to open tutorials
hook.Add('DDI.TutorialSystem.Started', 'MyAddon_CustomUI', function(tutorialId, state)
    -- Check if the tutorial belongs to our addon
    if string.StartWith(tutorialId, 'myaddon_') then
        -- Prevent opening the standard UI
        timer.Simple(0, function()
            if IsValid(DDI.TutorialSystem.UI.TutorialFrame) then
                DDI.TutorialSystem.UI.TutorialFrame:Close()
            end
        end)
        
        -- Open our own UI
        MyAddon.OpenCustomTutorialUI(tutorialId, state)
        
        -- Return false to prevent further processing
        return false
    end
end)

-- Creating a custom UI for tutorials
function MyAddon.OpenCustomTutorialUI(tutorialId, state)
    -- Your code to create a UI
end
```

## Troubleshooting

### Common Problems:

1. **Tutorial is not displayed or does not start:**
   - Check if the tutorial is registered correctly
   - Make sure that all required fields are filled in
   - Check the console for errors

2. **Conditions do not work:**
   - Check the logic of the condition function
   - Make sure AutoAdvance is enabled in the tutorial settings
   - Add debug messages to check if conditions work

3. **Element highlighting does not work:**
   - Verify that the element has a unique ID
   - Make sure HighlightElements is enabled in the settings
   - The element must exist at the time of highlighting

### Debugging:

Enable debug mode in the configuration for more information:

```lua
DDI.TutorialSystem:SetConfig('Debug', true)
```

Or change directly in the `lua/tutorials/sh_config.lua` file:

```lua
DDI.TutorialSystem.Config.Debug = true
```

## Additional documentation

For more information about the features and functions of the tutorial system, see the comments in the source code or refer to the following files:

- `lua/tutorials/sh_api.lua` - API documentation
- `lua/tutorials/examples/` - examples of tutorials
- `lua/tutorials/sh_config.lua` - system settings

---

If you have any questions or suggestions for improving the tutorial system, please contact the developer or create an issue on GitHub.

© Tutorial System for GMod Lua