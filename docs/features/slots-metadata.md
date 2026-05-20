---
layout: default
title: Slot Metadata Manager
parent: Advanced Features
nav_order: 2
---

# Slot Metadata Manager

Creating a premium load-save screen inside a professional game requires more than just loading raw state data. You need slot metrics: playtime totals, date-timestamps, game version validations, and slot name identifiers. Save System Pro provides an integrated, automated **Slot Metadata tracking system** natively.

---

## 🏛️ The Slot Metadata Structure
Every slot file managed by Save System Pro (configurable up to 99 slots via the Control Panel) tracks and updates a structured metadata block on every save trigger:

```csharp
[Serializable]
public class SlotMetadata
{
    public int slotIndex;
    public string displayName = "Save Slot";
    public bool isEmpty = true;
    public float playtimeSeconds;
    public string lastSaved;
    public string gameVersion;
}
```

---

## ⏱️ Playtime Tracking API
Playtime is tracked using high-performance Unity engine counters. You can save updates by capturing the active session duration:

```csharp
// Record save data to Slot 0, automatically passing session playtime
float currentSessionTime = Time.time; 
SaveSystemPro.Instance.Save(0, myPlayerData, currentSessionTime);
```
Behind the scenes, the Slot Manager reads the existing saved metadata playtime, adds the new session delta, and writes the updated metadata back to disk, maintaining a highly accurate, cumulative playtime tracking history.

---

## 💻 Querying Metadata in Your UI
To populate a custom save-load UI, query the static `SlotManager` class:

```csharp
using DecnetGames.SaveSystemPro;

void PopulateSlotUI(int slotIndex)
{
    var metadata = SlotManager.GetSlotMetadata(slotIndex);

    if (metadata.isEmpty)
    {
        slotTitleText.text = "Empty Slot";
        playtimeText.text = "--:--:--";
        timestampText.text = "";
    }
    else
    {
        slotTitleText.text = $"Save Slot {slotIndex}";
        timestampText.text = $"Saved on: {metadata.lastSaved}";
        
        // Format playtime seconds into Hours:Minutes:Seconds
        System.TimeSpan timeSpan = System.TimeSpan.FromSeconds(metadata.playtimeSeconds);
        playtimeText.text = string.Format("{0:D2}h:{1:D2}m:{2:D2}s", 
            timeSpan.Hours + (timeSpan.Days * 24), 
            timeSpan.Minutes, 
            timeSpan.Seconds);
    }
}
```

---

## 🧼 Deleting Slot Files & Metadata
To erase save records completely, clear the slot data and reset metadata parameters in a single call:

```csharp
// Completely erases Slot 0 data files and resets metadata parameters
SaveSystemPro.Instance.Delete(0);
```

---

## 🛠️ The Editor Slot Manager Window
For developer convenience, Save System Pro features a premium dark-themed visual Slot Manager directly inside the Unity Editor:
1. Open it via `Tools > Save System Pro > Slot Manager`.
2. It lists all slots (up to your configured max).
3. If a slot is written, it displays active metadata (Playtime, Date, Game Version).
4. If the slot is empty, it labels it as "Empty".
5. Click **Delete Slot Data** on any slot to wipe files instantly from your machine, allowing rapid debug testing.
