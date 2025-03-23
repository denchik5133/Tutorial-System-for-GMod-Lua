# Tutorial System-for GMod Lua

A flexible, developer-friendly system for creating interactive step-by-step guides for any addon in Garry's Mod.

## Description

Tutorial System is a versatile tool for creating tutorials that can be easily integrated into any addon for Garry's Mod. The system provides a complete set of tools for creating step-by-step tutorials with support for interactive elements, walkthrough conditions and visual cues.

## Installation

1. Copy the `lua` folder to the directory of your server or addon
2. The system will automatically load when you start the server via the `lua/autorun/tutorials_init.lua` file
3. To access the tutorials, players can use the command `!tutorials` in chat or `tutorials` in the console

### Key Features:

- **Easy integration** - easy to add to any existing addon
- **Interactive steps** - ability to set conditions for transition between steps
- **Highlighting of UI elements** - highlights UI elements to attract attention
- **Progress Tracking** - save progress between game sessions on the client side
- **Categorization** - group tutorials into categories
- **Customizable UI** - Ability to change appearance and behavior, including minimal and interactive modes
- **Modern interface** - stylish design with animations, gradients and visual cues
- **Automatic execution** - Tutorial support with automatic progression through the steps
- **Interactive HUD** - display tutorial progress on screen without interface windows
- **Visual tooltips** - intuitive tooltips for required actions with animations
- **Separation of UI and process** - ability to display only HUD for fully interactive tutorials
- **Expanded API** - detailed API for integration with other addons
- **Media Content** - support for images and videos in tutorial steps
- **Compact Mode** - compact interface with step indicators on the sides
- **Direct Navigation** - quick navigation between steps via indicators
- **Visual highlighting of completed tutorials** - clear display of completed tutorials in the menus
- **Sound Effects** - a variety of sound effects to enhance the experience
- **Animations and effects** - visual effects when passing and completing tutorials
- **Reward completion screen** - special congratulations screen when completing tutorials
- **Fully Interactive Mode** - Tutorials controlled solely by player actions
- **Dynamic Tutorial Creation** - Ability to create tutorials based on current game data
- **Integration with addons** - hooks system for easy integration with existing addons
- **Creative sandboxes** - support for creating interactive game sandboxes for learning
- **Gamification of learning** - game elements and challenges to increase engagement
- **Support for keyboard navigation** - convenient hotkeys for moving between steps (arrows) and closing (CTRL+ALT)
- **Customizable keyboard shortcuts** - ability to change and customize keyboard shortcuts for different actions

## Quick Start

### Create a simple tutorial:

```lua
local myTutorial = {
    id = 'my_first_tutorial',
    title = 'My first tutorial',
    description = 'A simple tutorial to demonstrate the system',
    
    steps = {
        {
            text = 'Step 1: Welcome to my tutorial!'
        },
        {
            text = 'Step 2: This is the second step of the tutorial.'
        },
        {
            text = 'Step 3: Congratulations! You have completed the tutorial.'
        }
    }
}

DDI.TutorialSystem.API:Register(myTutorial)
```

### Interactive Tutorial:

```lua
local interactiveTutorial = {
    id = 'interactive_tutorial',
    title = 'Interactive tutorial',
    description = 'Tutorial with conditions for passing',
    
    settings = {
        AutoAdvance = true,      -- Automatically move to the next step when a condition is met
        InteractiveMode = true,  -- Enable fully interactive mode (HUD only, no window)
        ShowHUD = true,          -- Show HUD on the screen
        UIMode = 'minimal'       -- Minimum UI mode
    },
    
    steps = {
        {
            text = 'Press the space bar to continue',
            action = function() -- Executed when you enter this step
                notification.AddLegacy('Press SPACE', NOTIFY_HINT, 3)
            end,
            condition = function() -- Checked to proceed to the next step
                return input.IsKeyDown(KEY_SPACE)
            end
        },
        {
            text = 'Now click the left mouse button',
            action = function()
                notification.AddLegacy('Press LKM', NOTIFY_HINT, 3)
            end,
            condition = function()
                return input.IsMouseDown(MOUSE_LEFT)
            end
        },
        {
            text = 'All right! You have successfully completed the interactive tutorial.',
            action = function()
                -- Completion effect
                local effectData = EffectData()
                effectData:SetOrigin(LocalPlayer():GetPos() + Vector(0, 0, 50))
                util.Effect('confetti', effectData)
                
                -- Ending sound
                surface.PlaySound('garrysmod/save_load1.wav')
            end
        }
    }
}

DDI.TutorialSystem.API:Register(interactiveTutorial)
```

## Documentation

Full documentation and usage examples are available in the `USAGE_GUIDE.md` file. It is also recommended to study the examples in the `lua/tutorials/examples/` folder.

#### Hooky (Events)

```lua
-- API is ready to use
hook.Add('DDI.TutorialSystem.APIReady', 'MyAddon_Integration', function(api) ... end)

-- Tutorial is running
hook.Add('DDI.TutorialSystem.Started', 'MyHandler', function(tutorialId, state) ... end)

-- Tutorial closed without completion
hook.Add('DDI.TutorialSystem.Exited', 'MyHandler', function(tutorialId, step) ... end)

-- Changing the step of the tutorial
hook.Add('DDI.TutorialSystem.StepChanged', 'MyHandler', function(tutorialId, stepNumber) ... end)

-- Player has completed the tutorial (client event)
hook.Add('DDI.TutorialSystem.Completed', 'MyHandler', function(tutorialId) ... end)
```

## Integration with other addons

Tutorial System provides a simple API for integration with other addons:

```lua
-- When the system API is ready to be used
hook.Add('DDI.TutorialSystem.APIReady', 'MyAddon_Integration', function(api)
    -- Register a tutorial for our addon
    api:Register({
        id = 'myaddon_tutorial',
        title = 'MyAddon training',
        description = 'Learn how to use MyAddon',
        -- ...other parameters
    })
    
    -- Check if the current player has been through a tutorial
    hook.Add('PlayerSpawn', 'MyAddon_CheckTutorial', function(player)
        if player == LocalPlayer() and not api:IsCompleted('myaddon_tutorial') then
            -- Offer to take the tutorial
        end
    end)
end)
```

## License

This project is distributed under the MIT license. See the LICENSE file for details.
