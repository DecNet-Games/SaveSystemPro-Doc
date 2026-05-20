---
layout: default
title: Pro Key-Value DB Core
parent: Advanced Features
nav_order: 3
---

# Pro Key-Value DB Core

Save System Pro features an integrated, slot-based **Key-Value database architecture**. Rather than serializing a single mammoth save class structure that must be parsed completely every time you modify minor variables, you can store individual, target variables dynamically under unique string keys within specific slot files.

---

## 🏛️ Underlying Database Engine
When you save variables under keys inside Slot index `N`, Save System Pro manages a serializable dictionary representation internally for that slot. 

The framework uses the optimized `AdvancedSerializer` behind the scenes, allowing you to pass complex C# collections, custom classes, and standard Unity structures to the key-value database seamlessly.

---

## 📝 Writing Variables: `SaveValue()`
Use this method to create or overwrite individual variable keys within a target slot:

```csharp
using DecnetGames.SaveSystemPro;
using UnityEngine;

int activeSlot = 0;

// Save an integer
SaveSystemPro.Instance.SaveValue(activeSlot, "player_gems", 500);

// Save a Vector3
SaveSystemPro.Instance.SaveValue(activeSlot, "spawn_anchor", new Vector3(-20f, 1.5f, 40f));

// Save custom classes
SaveSystemPro.Instance.SaveValue(activeSlot, "quest_tracker", activeQuestData);
```

---

## 🔍 Reading Variables: `LoadValue()`
To load a key back, invoke `LoadValue<T>()`. You must provide a **default fallback value** which is safely returned if the key does not exist yet (e.g. at the first launch of the game):

```csharp
// Load the integer, defaulting to 0 if not found
int gems = SaveSystemPro.Instance.LoadValue<int>(activeSlot, "player_gems", 0);

// Load the Vector3, defaulting to Vector3.zero
Vector3 spawn = SaveSystemPro.Instance.LoadValue<Vector3>(activeSlot, "spawn_anchor", Vector3.zero);
```

---

## ❓ Checking Key Presence: `KeyExists()`
To verify if a key was previously registered in the slot's file, invoke `KeyExists()`:

```csharp
if (SaveSystemPro.Instance.KeyExists(activeSlot, "spawn_anchor"))
{
    transform.position = SaveSystemPro.Instance.LoadValue<Vector3>(activeSlot, "spawn_anchor");
}
else
{
    transform.position = defaultSpawnLocation.position;
}
```

---

## 🧼 Erasing Individual Keys: `DeleteKey()`
If you need to wipe out specific keys within a slot file without affecting other saved variables, use `DeleteKey()`:

```csharp
// Removes the quest tracking entry specifically from Slot 0
SaveSystemPro.Instance.DeleteKey(0, "quest_tracker");
```

---

## 📊 Getting All Registered Keys: `GetKeys()`
For custom save audits or modding/debugging workflows, you can retrieve a list of all variable keys registered inside a specific slot file:

```csharp
List<string> savedKeys = SaveSystemPro.Instance.GetKeys(activeSlot);

Debug.Log($"Slot {activeSlot} contains {savedKeys.Count} active variables:");
foreach (string key in savedKeys)
{
    Debug.Log($" - Key: {key}");
}
```
