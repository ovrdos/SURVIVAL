# POSTAL Code Map

## Purpose

This document maps the existing POSTAL source architecture so SURVIVAL
can reuse existing functionality instead of creating replacement systems.

Primary rule:

    USE EXISTING POSTAL FUNCTIONALITY FIRST.

Only modify or create functionality when the existing implementation
cannot support the SURVIVAL requirement.

---

# 1. Repository Architecture

The source is organized into several major layers:

    POSTAL GAME
        |
        +-- Postal Common
        |
        +-- RSPiX
        |     |
        |     +-- Blue
        |     +-- Cyan
        |     +-- Green
        |     +-- Orange
        |
        +-- WishPiX
        |     |
        |     +-- Menu
        |     +-- Prefs
        |     +-- ResourceManager
        |     +-- Spry
        |
        +-- Game Assets
        |
        +-- Platform / Build Configuration

RWS describes RSPiX as the cross-platform library underneath the game.

---

# 2. Build System

## Primary Solution

    Postal.sln

Location:

    /Postal.sln

The repository also contains older Visual C++ project files (.vcproj)
and newer Visual Studio project files (.vcxproj).

Examples:

    BLiT.vcxproj
    BLiT3D.vcxproj
    Blue.vcxproj
    Cyan.vcxproj
    Green.vcxproj
    Orange.vcxproj
    Postal Common.vcxproj
    Postal Plus.vcxproj

### SURVIVAL rule

Do not replace the build system during the initial modernization.

First make the existing build work.

---

# 3. RSPiX

Location:

    /RSPiX

RSPiX is the foundation underneath the POSTAL game.

RWS documents four major functional levels:

    Blue
    Cyan
    Green
    Orange

---

# 4. Blue

Location:

    /RSPiX/Src/BLUE

Blue is the lowest-level RSPiX API.

It provides:

    Graphics
    Audio
    Input
    Mouse
    Keyboard
    Joystick
    Platform-specific functionality

There are platform-specific implementations under:

    /RSPiX/Src/BLUE/Win32
    /RSPiX/Src/BLUE/Mac

Important files include:

    blue.h
    system.h

### SURVIVAL use

Blue is potentially important for:

    modern display resolution
    rendering
    input
    video output

BUT:

Do not modify Blue initially.

The first goal is to determine whether existing rendering can
display upgraded SURVIVAL artwork.

---

# 5. Cyan

Location:

    /RSPiX/Src/CYAN

Cyan provides functionality above Blue.

Examples include:

    message boxes
    mouse cursor functionality
    printer functionality

POSTAL does not use all Cyan functionality.

### SURVIVAL use

Do not modify Cyan unless the SURVIVAL UI or platform requirements
actually require it.

---

# 6. Green

Location:

    /RSPiX/Src/GREEN

Green provides functionality built above Blue/Cyan.

### SURVIVAL use

Treat Green as an existing service layer.

Do not rewrite.

---

# 7. Orange

Location:

    /RSPiX/Src/ORANGE

Orange provides higher-level functionality built above Green or
independently of lower layers.

### SURVIVAL use

Reuse existing functionality.

Do not create a parallel SURVIVAL engine.

---

# 8. WishPiX

Location:

    /WishPiX

WishPiX contains higher-level systems that were intended to eventually
become part of RSPiX.

Major components:

    /WishPiX/Menu
    /WishPiX/Prefs
    /WishPiX/ResourceManager
    /WishPiX/Spry

---

# 9. Sprite System

Location:

    /WishPiX/Spry

RWS describes Spry as the sprite-array component.

This is one of the most important areas for SURVIVAL.

### SURVIVAL strategy

Investigate this system BEFORE creating a new sprite system.

The desired pipeline is:

    Existing POSTAL Object
            |
            v
    Existing Sprite System
            |
            v
    SURVIVAL Sprite
            |
            v
    Existing Renderer

We want modern artwork without replacing the underlying game object.

---

# 10. Animation

Important source files:

    AnimThing.cpp
    AnimThing.h

    Anim3D.cpp
    Anim3D.h

Also:

    AlphaAnimType.h

### SURVIVAL strategy

These files are primary investigation targets.

Determine:

    How animation objects are created
    How frames are stored
    How frames are selected
    How animation timing works
    How animation starts/stops
    How animation state is changed
    How sprites are associated with animation

DO NOT create a new animation framework.

First attempt:

    Existing animation code
            +
    New SURVIVAL artwork

---

# 11. Animated Game Objects

Important files:

    AnimThing.cpp
    AnimThing.h

These appear to represent animated game-level objects.

### SURVIVAL investigation

Determine whether existing animated objects can represent:

    Robot
    Player
    Server equipment
    Electrical equipment
    Doors
    Environmental animation

If yes:

    reuse them.

---

# 12. 3D Animation

Files:

    Anim3D.cpp
    Anim3D.h

### SURVIVAL strategy

Do not assume this is needed.

SURVIVAL's primary visual target is still the classic POSTAL-style
isometric gameplay presentation.

Investigate Anim3D only if an existing POSTAL mechanism already uses
it for something we need.

---

# 13. Player

The player implementation has NOT yet been completely mapped.

NEXT INVESTIGATION:

Search source for:

    player
    protagonist
    person
    actor
    movement
    controls
    health

Likely source relationships should be documented here after
inspection of the relevant game classes.

DO NOT invent a Player class.

---

# 14. NPC / Person System

Important file identified:

    Personatorium.cpp

This is a primary investigation target for character/NPC behavior.

NEXT INVESTIGATION:

Determine:

    NPC base class
    NPC creation
    NPC movement
    NPC animation
    NPC collision
    NPC damage
    NPC death
    NPC AI/state handling

SURVIVAL robots should preferably reuse the existing NPC/person
architecture.

Desired design:

    Existing NPC
          +
    SURVIVAL visual identity
          +
    Minimal behavior changes

NOT:

    New Robot Engine

---

# 15. Robot

SURVIVAL robot architecture should initially be:

    Existing NPC/Object
          |
          +-- new sprite
          +-- new animation
          +-- new appearance
          |
          +-- existing collision
          +-- existing movement
          +-- existing damage
          +-- existing death

Only add robot-specific functionality when an existing mechanism
cannot support it.

---

# 16. Weapons

NOT YET COMPLETELY MAPPED.

NEXT INVESTIGATION:

Search for:

    weapon
    projectile
    missile
    bullet
    fire
    impact
    damage

Goal:

Identify the existing projectile mechanism.

SURVIVAL syringe should reuse the existing projectile mechanism
wherever possible.

---

# 17. Projectile

NOT YET COMPLETELY MAPPED.

Required information:

    projectile class
    projectile creation
    projectile movement
    projectile collision
    projectile impact
    projectile destruction
    projectile lifetime

SURVIVAL syringe target:

    Existing Projectile
          |
          +-- syringe artwork
          +-- existing movement
          +-- existing collision
          +-- custom injection result

Avoid creating:

    NewProjectileEngine

---

# 18. Health

NOT YET COMPLETELY MAPPED.

NEXT INVESTIGATION:

Search for:

    health
    life
    hit points
    damage
    death
    heal

Required information:

    Where player health is stored
    How damage is applied
    How death is triggered
    How healing works

SURVIVAL injection should use the existing health/damage mechanism.

---

# 19. Damage

NOT YET COMPLETELY MAPPED.

Primary goal:

Find the common path for:

    object hit
       |
       v
    damage
       |
       v
    health
       |
       v
    death

SURVIVAL should use this path for:

    Robot attacks
    Electrical hazards
    Syringe impact

---

# 20. Items

NOT YET COMPLETELY MAPPED.

NEXT INVESTIGATION:

Search for:

    item
    pickup
    health
    medkit
    object
    inventory

SURVIVAL health objects should preferably be existing POSTAL item
objects with new artwork and behavior.

---

# 21. Maps / Levels

RWS documentation calls the game environments "realms."

Source contains realm-related configuration/header files including:

    CapFlag_Gskirts.h
    Goal_Gskirts.h
    Eskirts.h
    Hskirts.h
    Mskirts.h
    Mpskirts.h
    Gconsite.h
    Hconsite.h
    Mconsite.h
    Pconsite.h

These need further investigation.

### SURVIVAL strategy

Reuse the existing realm/map system.

The first SURVIVAL level should be created using the existing
level infrastructure.

---

# 22. World Objects

NOT YET COMPLETELY MAPPED.

NEXT INVESTIGATION:

Search for:

    CThing
    Thing
    object
    collision
    sprite
    realm
    world

Determine the base class used by interactive game objects.

This is potentially the most important class hierarchy to understand.

---

# 23. Collision

NOT YET COMPLETELY MAPPED.

Required:

    object collision
    character collision
    projectile collision
    environment collision
    damage collision

SURVIVAL rule:

    REUSE POSTAL COLLISION.

Do not create a new physics engine.

---

# 24. Electrical Hazards

SURVIVAL requirement:

    Electrical equipment damages player on contact.

Preferred implementation:

    Existing World Object
            +
    Existing Collision
            +
    Existing Damage
            +
    New electrical animation

Example:

    Server Rack
    Power Panel
    Generator
    Cable
    UPS

These should initially be existing object types with new artwork.

---

# 25. Resource Manager

Location:

    /WishPiX/ResourceManager

Purpose:

    Resource loading
    Asset management

This is a major SURVIVAL investigation target.

Determine:

    How sprites are loaded
    How animations are loaded
    How maps are loaded
    How sounds are loaded
    How resource paths are resolved

---

# 26. SAK Files

POSTAL normally loads assets from .sak archives.

The original development system also supports loading individual
assets from a filesystem directory when configured appropriately.

### SURVIVAL development strategy

Use loose files during development if the existing mechanism works.

Preferred workflow:

    Edit asset
        |
        v
    Save
        |
        v
    Launch game
        |
        v
    See new asset

Avoid rebuilding asset archives after every artwork change.

---

# 27. Configuration

Important files:

    DefaultPostal.ini
    GameSettings.cpp
    GameSettings.h
    InputSettings.cpp
    InputSettings.h
    MenuSettings.cpp
    MenuSettings.h

Configuration includes runtime settings such as:

    input
    audio
    paths
    game settings

---

# 28. Compile Configuration

Important file:

    CompileOptions.h

This file controls build configuration.

Important concepts include:

    TARGET
    LOCALE
    SPAWN
    DEMO
    DEBUG-related configuration

SURVIVAL may eventually require a dedicated target/configuration.

DO NOT add it until the baseline build and asset pipeline are working.

---

# 29. Application Name

CompileOptions.h controls the application/menu naming through
configuration such as:

    APP_NAME
    MAIN_MENU_TITLE

SURVIVAL can eventually use:

    APP_NAME = "SURVIVAL"
    MAIN_MENU_TITLE = "SURVIVAL"

Do this only after the baseline build is working.

---

# 30. Input

Important files:

    InputSettings.cpp
    InputSettings.h
    CtrlBuf.h
    BJOY.CPP
    BJOY.H

Input includes:

    keyboard
    mouse
    joystick

### SURVIVAL strategy

Keep the existing input system.

Do not replace controls until the gameplay prototype requires it.

---

# 31. Game Settings

Files:

    GameSettings.cpp
    GameSettings.h

Use this system for existing game configuration.

SURVIVAL-specific settings should only be added here if existing
configuration cannot represent them.

---

# 32. Menu

Files:

    MenuSettings.cpp
    MenuSettings.h
    MenuTrans.cpp
    MenuTrans.h

WishPiX also contains:

    /WishPiX/Menu

### SURVIVAL strategy

Modernize the presentation first.

Do not create a completely independent UI framework.

---

# 33. Audio

RSPiX Blue provides audio functionality.

There are also explicit project components:

    Blue Audio

### SURVIVAL strategy

Reuse the existing audio pipeline.

Modernize:

    sound effects
    robot sounds
    electrical sounds
    environmental ambience
    UI sounds

before creating new audio infrastructure.

---

# 34. Rendering

Primary investigation targets:

    Blue
    BLiT
    BLiT3D
    WishPiX/Spry
    AnimThing

Projects include:

    BLiT.vcxproj
    BLiT3D.vcxproj
    Blue.vcxproj
    Blue Audio
    Blue Video

### SURVIVAL strategy

DO NOT immediately replace rendering.

First test:

    Higher-quality artwork
        +
    Existing rendering pipeline

If successful:

    leave renderer alone.

---

# 35. UI / HUD

NOT YET COMPLETELY MAPPED.

NEXT INVESTIGATION:

Find existing:

    HUD
    health display
    text rendering
    score
    status display

SURVIVAL should initially modify existing UI mechanisms.

---

# 36. Sound / Effects

NOT YET COMPLETELY MAPPED.

NEXT INVESTIGATION:

Find:

    explosion effects
    impact effects
    particle-like effects
    animation effects
    sound triggers

Reuse existing effect mechanisms.

---

# 37. Game Logic

Important likely investigation targets:

    Postal Common
    game source
    Personatorium
    LogicList.txt
    Goal_* files

The game contains existing goal and logic concepts.

SURVIVAL should use existing game logic where possible.

---

# 38. Goals / Objectives

Existing files include:

    Goal_Gskirts.h

This suggests level/objective configuration already exists.

SURVIVAL's initial objective:

    REACH EXTRACTION

should first be investigated as an existing goal/objective mechanism.

---

# 39. AI

NOT YET COMPLETELY MAPPED.

NEXT INVESTIGATION:

Search for:

    AI
    brain
    behavior
    target
    path
    waypoint
    person
    enemy
    chase

### SURVIVAL strategy

Early robots require almost no new AI.

Levels 1-3:

    Existing NPC behavior
    +
    new robot graphics

Later:

    minimal modifications for syringe behavior.

Do not build a new AI framework.

---

# 40. Animation Modernization Strategy

This is the PRIMARY SURVIVAL engineering target.

Desired pipeline:

    Existing Object
          |
          v
    Existing Animation System
          |
          v
    New SURVIVAL Frames
          |
          v
    Existing Renderer

Modernization should focus on:

    higher quality frames
    better silhouettes
    more frames
    better impact frames
    better directional frames
    smoother transitions
    richer environmental animation

---

# 41. SURVIVAL Robot Animation

Initial animation set:

    IDLE
    WALK
    RUN
    HIT
    FALL
    BROKEN
    DEATH

Later:

    SYRINGE_READY
    SYRINGE_FIRE
    SYRINGE_RETURN

These should use existing animation infrastructure.

---

# 42. Character Modernization

Initial player animation set:

    IDLE
    WALK
    RUN
    ATTACK
    HIT
    DEATH

Do not change movement mechanics during the first visual prototype.

---

# 43. Data Center Environment

Initial environment assets:

    Server rack
    Server cabinet
    Cooling unit
    Power panel
    UPS
    Generator
    Cable
    Workstation
    Door
    Catwalk
    Glass wall
    Extraction point

These should initially be mapped onto existing POSTAL world-object
types.

---

# 44. SURVIVAL Graphics Layer

Concept:

    EXISTING POSTAL OBJECT
              |
              v
       EXISTING LOGIC
              |
              v
       SURVIVAL ASSET
              |
              v
       EXISTING RENDERER

The graphics layer should replace the presentation without
unnecessarily replacing the underlying game object.

---

# 45. SURVIVAL New Functionality

Keep new functionality extremely small.

Expected eventual additions:

    Injection status
    Syringe behavior
    Electrical hazard behavior
    SURVIVAL-specific objectives

Everything else should attempt to reuse existing functionality.

---

# 46. Priority Order

Development order:

    1. Build POSTAL
    2. Understand rendering
    3. Understand sprites
    4. Understand animation
    5. Replace one character sprite
    6. Replace character animation
    7. Replace one NPC sprite
    8. Replace NPC animation
    9. Create data center environment
    10. Build first playable room
    11. Create robot visual identity
    12. Validate existing combat
    13. Add syringe
    14. Add injection behavior
    15. Add medical recovery
    16. Add electrical hazards
    17. Build levels
    18. Polish graphics
    19. Polish animation
    20. Optimize

---

# 47. Forbidden Early Work

DO NOT:

    Rewrite RSPiX
    Rewrite Blue
    Replace the renderer
    Create a new sprite engine
    Create a new animation engine
    Create a new physics engine
    Create a new collision engine
    Create a new AI engine
    Create a new map engine
    Create a new entity system
    Create a new input system
    Create multiplayer
    Create procedural generation

unless the existing architecture has been proven incapable of
supporting the requirement.

---

# 48. First Technical Proof

The first technical proof is:

    Existing POSTAL character
            |
            v
    Existing animation system
            |
            v
    New SURVIVAL artwork
            |
            v
    Existing renderer
            |
            v
    Character moves normally

SUCCESS means:

    gameplay unchanged
    collision unchanged
    movement unchanged
    AI unchanged
    only presentation changes

---

# 49. Current Investigation Status

COMPLETED:

    Repository identified
    RSPiX identified
    Blue identified
    Cyan identified
    Green identified
    Orange identified
    WishPiX identified
    Spry identified
    ResourceManager identified
    AnimThing identified
    Anim3D identified
    Build projects identified
    CompileOptions identified
    Configuration identified
    Personatorium identified
    Realm/goal configuration identified

NOT YET COMPLETELY MAPPED:

    Player class
    NPC class hierarchy
    Base world object
    Collision implementation
    Weapon implementation
    Projectile implementation
    Health implementation
    Damage implementation
    Item implementation
    Realm implementation
    AI implementation
    HUD implementation

These are the next source-code investigations.

---

# 50. SURVIVAL Architecture Principle

The desired architecture is:

                 EXISTING POSTAL
                       |
        +--------------+--------------+
        |              |              |
     GAMEPLAY       OBJECTS       ENGINE
        |              |              |
        +--------------+--------------+
                       |
                       v
                SURVIVAL CONTENT
                       |
        +--------------+--------------+
        |              |              |
      ART          ANIMATION        FX
        |              |              |
        +--------------+--------------+
                       |
                       v
                   SURVIVAL

The less new engine functionality we create,
the better.

The majority of SURVIVAL development should occur
in:

    assets
    sprites
    animation
    environments
    sound
    level content

rather than in:

    core engine code.
