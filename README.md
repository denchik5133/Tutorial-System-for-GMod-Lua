# Tutorial System for GMod Lua

A flexible, developer-friendly system for creating interactive step-by-step guides for any addon in Garry's Mod.

## Description

Tutorial System is a versatile tool for creating tutorials that can be easily integrated into any addon for Garry's Mod. The system provides a complete set of tools for creating step-by-step tutorials with support for interactive elements, walkthrough conditions and visual cues.

### Key features:

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

## Installation

1. Copy the `lua` folder to the directory of your server or addon
2. The system will automatically load when you start the server via the `lua/autorun/tutorials_init.lua` file
3. To access the tutorials, players can use the command `!tutorials` in chat or `tutorials` in the console

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

### Main sections of documentation:

- [System Overview](USAGE_GUIDE.md#system-overview)
- [Creating Tutorials](USAGE_GUIDE.md#create-tutorials)
- [System API](USAGE_GUIDE.md#api)
- [Usage Examples](USAGE_GUIDE.md#use-examples)
- [Extension-Functionality](USAGE_GUIDE.md#extension-functionality)
- [Troubleshooting](USAGE_GUIDE.md#troubleshooting-problems)

### API Help

#### Tutorial structure

```lua
{
    id = 'unique_tutorial_id',              -- Tutorial unique identifier (required)
    title = 'Tutorial title',               -- Tutorial title (mandatory)
    description = 'Tutorial description',   -- Short description
    category = 'Fundamentals',              -- Category for grouping in the menu
    author = 'Author name',                 -- Tutorial author
    icon = 'icon16/star.png',               -- Icon to be displayed in the menu
    order = 1,                              -- Order of display in the menu
    requireComplete = { 'tutorial_id' },    -- IDs of tutorials to pass before this one
    
    settings = {
        UIMode = 'standard',                -- UI mode: 'standard', 'minimal', 'custom'
        Closable = true,                    -- Can the player close the tutorial
        Skippable = true,                   -- Can the player skip steps
        ShowHUD = true,                     -- Whether to show HUD
        PlaySounds = true,                  -- Whether to play sounds
        AutoAdvance = false,                -- Auto-transition when a condition is met
        InteractiveMode = false,            -- Fully interactive mode
        HideUIOnComplete = true,            -- Whether to hide UI on completion
        HighlightElements = true,           -- Whether to highlight elements
        CompactMode = false,                -- Use compact UI mode
        Position = 'right',                 -- UI position: 'right', 'left', 'center'
        DirectStepNavigation = true,        -- Allow direct navigation through step indicators
        ShowStepImages = true,              -- Show images in steps
        ShowStepVideos = true,              -- Show videos in steps
        AutoPlayMedia = true,               -- Automatically play media
        AnimateUI = true,                   -- Use animations in UI
        RewardScreen = true,                -- Show reward screen on completion
        CompletionEffects = true,           -- Effects when a tutorial is completed
        ProgressTracking = true,            -- Track and display overall progress
        SandboxMode = false,                -- Sandbox mode for free experimentation
        ChallengeMode = false,              -- Challenge mode with achievements
        DynamicContent = false              -- Dynamically generated content
    },
    
    steps = { -- Array of tutorial steps (mandatory)
        {
            text = 'Step text',             -- Displayed text (mandatory)
            action = function() ... end,    -- Function to be executed when entering a step
            condition = function() ... end, -- Condition for moving to the next step
            highlight = 'element_name',     -- Item ID for highlighting
            image = 'path/to/image.jpg',    -- Path to the image to display
            video = 'https://youtube.com/embed/videoId', -- URL for video embedding
            position = { x = 100, y = 100 },-- Position for special elements
            onExit = function() ... end,    -- Executed when exiting this step
            forceNavigation = true,         -- Force to show navigation buttons for this step
        },
        -- Other steps...
    },
    
    onStart = function() ... end,           -- Executed when the tutorial is started
    onComplete = function() ... end,        -- Executed when the tutorial ends
    onExit = function() ... end             -- Executed when exiting the tutorial
}
```

#### Main functions of the API

```lua
-- Tutorial registration
DDI.TutorialSystem.API:Register(tutorialData)

-- Running a tutorial
DDI.TutorialSystem.API:Start(tutorialId, forceStart)

-- Checking if the player has completed the tutorial
DDI.TutorialSystem.API:IsCompleted(tutorialId)

-- Opening the tutorials menu
DDI.TutorialSystem.API:OpenMenu(category)

-- Getting the list of tutorials
DDI.TutorialSystem.API:Get(category, sortBy)

-- Getting Tutorials by ID
DDI.TutorialSystem.API:GetID(tutorialId)

-- Go to the next step of the tutorial
DDI.TutorialSystem.API:NextStep()

-- Go to the previous step
DDI.TutorialSystem.API:PreviousStep()

-- End Tutorial
DDI.TutorialSystem.API:Complete(tutorialId)

-- Exit tutorial
DDI.TutorialSystem.API:Exit(tutorialId)
```

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

#### UI Functions

```lua
-- Interface element illumination
DDI.TutorialSystem:HighlightElement(element)

-- Adding a key hint
DDI.TutorialSystem.UI:AddKeyHint('Press SPACE', KEY_SPACE)

-- Displaying a pop-up message
DDI.TutorialSystem.UI:ShowPopupMessage('Caption', 'Message', 5)

-- Создание кнопки
local button = DDI.TutorialSystem.UI:CreateModernButton(parent, text, x, y, w, h, icon)

-- Creating a shadow for the panel
DDI.TutorialSystem.UI:CreateShadow(panel, shadowSize, opacity)
```

## Examples of tutorials

The `lua/tutorials/examples/` folder contains several examples of how to use the system:

- `example_tutorial.lua` - a simple demonstration tutorial with basic functions
- `basic_controls_tutorial.lua` - tutorial on basic Garry's Mod controls
- `weapon_tutorial.lua` - interactive tutorial on using weapons
- `interactive_sandbox_tutorial.lua` - advanced Sandbox tutorial with interactive elements
- `fully_interactive_tutorial.lua` - fully interactive tutorial without interface buttons
- `interactive_challenge_tutorial.lua` - game challenge with timer and targets
- `visual_guides_tutorial.lua` - demonstration of visual cues and navigation elements
- `modern_ui_showcase.lua` - demonstration of all modern UI features
- `props_master_tutorial.lua` - tutorial on creating props with time trials
- `addon_developer_tutorial.lua` - guide for developers on integration with the tutorial system
- `demo_custom_addon.lua` - example of integration with own addon
- `integration_example.lua` - example of integration with an existing addon

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
