---
layout: default
title: Global Auto-Save Manager
parent: Advanced Features
nav_order: 7
---

# Global Auto-Save Manager

The backbone of Save System Pro's automated tracking is the **Global Auto-Save System**. It coordinates state-gathering, registers active scenes, generates unique IDs, and triggers save/load operations automatically. 

This page covers the core coordinator: **`AutoSaveManager`** and its component companion **`AutoSaveIdentifier`**.

---

## 🔑 1. Unique ID Mapping: `AutoSaveIdentifier`
For an automated save system to know which database entries belong to which game objects, every tracked item in your scene must have a completely unique identity key (GUID). 

Configuring these keys manually is tedious and highly prone to developer errors. To prevent this, Save System Pro features the **`AutoSaveIdentifier`** component:

1. Attach `AutoSaveIdentifier` to any GameObject you wish to automate.
2. In the background, **`AutoSaveIdentifierEditor.cs`** hooks into the Unity Inspector drawing cycle.
3. If the script detects the **`ID Key`** field is empty or duplicate, it automatically generates a unique, cryptographically secure 128-bit GUID string (e.g. `c74b2f1a-9860-4ea5-a22d-bb91ea513c12`) instantly.
4. If you duplicate the object in the editor, the inspector editor script detects the collision and automatically regenerates a new, isolated ID for the duplicate object!

---

## 🤖 2. Global Scene Coordinator: `AutoSaveManager`
The **`AutoSaveManager`** is a central manager script that orchestrates saving and loading across the active scene hierarchy:

### Setup:
1. Create a new empty GameObject in your scene named `AutoSaveManager`.
2. Add the **`AutoSaveManager`** component to it.
3. Configure the trigger parameters in the Inspector:

| Parameter | Type | Description |
| :--- | :--- | :--- |
| **`Save Trigger`** | Enum | Chooses when to trigger an auto-save automatically: `OnSceneUnload`, `OnApplicationQuit`, `OnInterval` (timer-based), or `ManualOnly`. |
| **`Save Interval Seconds`** | float | If set to interval-based, the frequency (in seconds) to write background saves. |
| **`Active Slot`** | int | The slot index targeting this automated save operation. |

---

## 💻 Manual Code Triggers
If you configure your `AutoSaveManager` trigger mode to `ManualOnly`, you can trigger a global scene-save and scene-load manually from a button event, checkpoint zone, or custom gameplay trigger:

```csharp
using DecnetGames.SaveSystemPro;

// Triggers a complete gathering of all AutoSaveIdentifiers in the scene
// and writes their active states, physics, and transforms to Slot 0
AutoSaveManager.Instance.SaveAllObjects();

// Reloads and restores the state of all scene-managed game objects
AutoSaveManager.Instance.LoadAllObjects();
```

---

> **Pro Tip**: To keep compilation and execution fast, `AutoSaveManager` uses a **Runtime Register pattern**. Instead of executing expensive `FindObjectsOfType<AutoSaveIdentifier>()` searches (which search the entire scene hierarchy and drop frames), each identifier registers itself in a fast memory cache on `Awake()`, keeping memory searches at $O(1)$ efficiency!
