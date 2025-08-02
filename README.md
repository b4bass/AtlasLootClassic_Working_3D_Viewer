# AtlasLootClassic - 3D Model Viewer Fix

This version of AtlasLootClassic includes fixes to restore the 3D model viewer functionality in the Classic Era client.

## Acknowledgements

The original addon is by **BlessedRabies**. The fixes were inspired by the work of **nanderson11 and Lag** on their fork of the addon, which provided the necessary logic for the modern client.

## The Problem

The 3D model viewer was broken due to changes in the World of Warcraft API. The function used to display models, `SetDisplayInfo`, was deprecated and no longer works reliably for this purpose, leading to Lua errors or models simply not appearing.

## The Fix

The following changes were made to fix the 3D model viewer:

1.  **`GUI/GUI.lua`:**
    *   A line was added to pass the `npcID` from the addon's loot data to the model frame (`GUI.ModelFrame.npcID = moduleData[dataID].items[bossID].npcID`). The new API call requires this specific ID, which was not being provided to the model frame in the original code.

2.  **`GUI/ModelFrame.lua`:**
    *   The `ButtonOnClick` function, which is triggered when you try to view a model, was significantly updated.
    *   It now uses the modern `SetCreature(npcID, displayID)` method to render the 3D model.
    *   A timer (`C_Timer.After`) is used to call `SetCreature` a second time after a brief delay. This is a clever workaround from the reference addon to deal with a known caching issue in the game client that sometimes prevents uncached models from rendering on the first try.
    *   Crucially, a check was added to ensure `ModelFrame.npcID` is a valid number before calling `SetCreature`. If the `npcID` is missing or invalid for a particular boss, the code now falls back to the old `SetDisplayInfo` method. This prevents the Lua errors you were seeing and ensures maximum compatibility.

These changes combine the modern API usage from the reference fork with the data structure of your preferred addon version, resulting in a fully functional and error-free 3D model viewer.