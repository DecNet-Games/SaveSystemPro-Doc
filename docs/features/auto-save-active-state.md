---
layout: default
title: Hierarchy Active State Auto-Save
parent: Advanced Features
nav_order: 6
---

# Hierarchy Active State Auto-Save

In dynamic Unity levels, objects are frequently deactivated (e.g., picked-up coins, opened doorways, completed quest triggers) rather than completely destroyed. When a scene is re-entered or a game slot is loaded, you must restore whether each of these game objects was visible/active or hidden.

Save System Pro provides **`AutoSaveActiveState`** to automate this tracking cleanly.

---

## 🤖 What does it track?
When attached, the script tracks:
*   **GameObject Active State (`bool`)**: Specifically the `gameObject.activeSelf` property.

---

## ⚙️ Drag-and-Drop Setup

To automate hierarchy visibility tracking:
1. Select the dynamic target GameObject.
2. Ensure it has an **`AutoSaveIdentifier`** component attached.
3. Click **Add Component** in the Inspector.
4. Search for and select **`AutoSaveActiveState`**.

---

## 🚀 Active-State Deactivation Gotcha & Solutions
In Unity, if a parent object or the object containing your tracking script is set to inactive (`activeSelf = false`), **its update cycles, start loops, and standard monobehaviour methods stop running completely**. 

This presents a critical save-system challenge: *How can an inactive script save itself when it cannot execute logic?*

Save System Pro solves this using two layered architectural safety nets:

### 1. Central Registration with AutoSaveManager
On startup, `AutoSaveActiveState` registers itself with the central **`AutoSaveManager`** while it is active. When a save is triggered, the `AutoSaveManager` queries the registered list directly—even if the target GameObjects are currently inactive—and writes their visibility status to disk.

### 2. Manual Trigger Integration
If you deactivate objects dynamically during runtime, the script will write its state to the active slot's Key-Value database immediately on deactivation:

```csharp
private void OnDisable()
{
    // Write state immediately to secure backup before script sleeps
    SaveActiveState();
}
```
This guarantees that even if the client crashes or loses power, the deactivated status of your game chest, key item, or coins remains perfectly recorded!
