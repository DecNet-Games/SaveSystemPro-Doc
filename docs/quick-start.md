---
layout: default
title: Quick Start
nav_order: 3
---

# Quick Start Guide

Want to verify Save System Pro is working in your environment immediately? Follow this quick guide to implement an automated test script that tests slots, events, encryption, and dynamic collections!

---

## 💻 Copy-Paste Test script
Create a new C# script in your project named `SaveSystemProTest.cs` and paste the following code:

```csharp
using UnityEngine;
using System.Collections.Generic;
using DecnetGames.SaveSystemPro;

public class SaveSystemProTest : MonoBehaviour
{
    [Header("Select Slot (0 to 98)")]
    public int slotIndex = 0;

    [Header("Test Data")]
    public string heroName = "Pro Knight";
    public float baseAttackPower = 45.8f;
    public Vector3 lastCoordinates = new Vector3(100f, 15f, -250f);
    public Color heroAuraColor = Color.green;

    // A dynamic collection that standard serializers cannot save natively
    private Dictionary<string, int> playerCurrencies = new Dictionary<string, int>()
    {
        { "Gold", 1000 },
        { "Gems", 45 },
        { "ArenaTokens", 12 }
    };

    private void Start()
    {
        // 1. Subscribe to save events for robust architecture hooks
        SaveEvents.OnSaveComplete += (slot) => Debug.Log($"[SaveSystemPro] Save complete on Slot {slot}!");
        SaveEvents.OnLoadComplete += (slot) => Debug.Log($"[SaveSystemPro] Load complete on Slot {slot}!");
        SaveEvents.OnSaveFailed += (result) => Debug.LogError($"[SaveSystemPro] Save failed! Reason: {result}");
        SaveEvents.OnSaveCorrupted += (slot) => Debug.LogError($"[SaveSystemPro] Save file corrupt on Slot {slot}!");
    }

    private void Update()
    {
        // Press S key to Save everything
        if (Input.GetKeyDown(KeyCode.S))
        {
            SaveGameData();
        }

        // Press L key to Load and restore everything
        if (Input.GetKeyDown(KeyCode.L))
        {
            LoadGameData();
        }
    }

    private void SaveGameData()
    {
        // Save variables directly to our selected slot
        SaveSystemPro.Instance.SaveValue(slotIndex, "hero_name", heroName);
        SaveSystemPro.Instance.SaveValue(slotIndex, "attack_power", baseAttackPower);
        SaveSystemPro.Instance.SaveValue(slotIndex, "coordinates", lastCoordinates);
        SaveSystemPro.Instance.SaveValue(slotIndex, "aura_color", heroAuraColor);
        
        // Save dynamic C# Generic Dictionaries smoothly without external DLLs!
        SaveSystemPro.Instance.SaveValue(slotIndex, "currencies", playerCurrencies);

        Debug.Log("[SaveSystemPro] Save operation triggered. Checking event logs...");
    }

    private void LoadGameData()
    {
        // Load variables back with secure fallback default values if key missing
        heroName = SaveSystemPro.Instance.LoadValue<string>(slotIndex, "hero_name", "Unknown Warrior");
        baseAttackPower = SaveSystemPro.Instance.LoadValue<float>(slotIndex, "attack_power", 10.0f);
        lastCoordinates = SaveSystemPro.Instance.LoadValue<Vector3>(slotIndex, "coordinates", Vector3.zero);
        heroAuraColor = SaveSystemPro.Instance.LoadValue<Color>(slotIndex, "aura_color", Color.white);
        
        playerCurrencies = SaveSystemPro.Instance.LoadValue<Dictionary<string, int>>(slotIndex, "currencies", new Dictionary<string, int>());

        Debug.Log($"[SaveSystemPro] Restore complete! Warrior: {heroName}, Level attack: {baseAttackPower}");
        Debug.Log($"Coordinates: {lastCoordinates}, Aura color: {heroAuraColor}");
        Debug.Log($"Currencies: Gold={playerCurrencies.GetValueOrDefault("Gold", 0)}, Gems={playerCurrencies.GetValueOrDefault("Gems", 0)}");
    }
}
```

---

## 🏃 Running the Test

1. Create a new empty GameObject in your scene named `SaveProTest`.
2. Attach the `SaveSystemProTest` script to it.
3. Enter **Play Mode** in Unity.
4. Tweak the variables in the Inspector (e.g. change name to `Viking Legend` and aura to red).
5. Press the **`S`** key. You'll see `[SaveSystemPro] Save complete on Slot 0!` print in the console.
6. Stop Play Mode, restart Play Mode (variables will return to their script default values).
7. Press the **`L`** key. Watch the Inspector variables snap back to your customized values instantly!

> **Pro Tip**: Toggle **AES-128 Encryption** "On" in the `Tools > Save System Pro > Control Panel` window. Run the test again, then use the **Save Inspector** (`Tools > Save System Pro > Save Inspector`) to inspect and edit your custom dictionary keys securely!
