---
layout: default
title: Save File Version Migrations
parent: Advanced Features
nav_order: 11
---

# Save File Version Migrations

As your game receives updates, DLC patches, and gameplay balances during its commercial lifespan, your save file schemas will inevitably evolve. You will add new player stats, delete obsolete items, or change variable types.

If an existing player loads their old save file after updating their game client, a standard JSON engine will crash or lose vital properties, corrupting their profile.

Save System Pro eliminates this risk with **Version Migrations** — a framework designed to upgrade legacy files smoothly.

---

## 🛠️ Step 1: Declaring the Schema Version
You can declare the target version of your current game build in the Pro Control Panel or directly in the configuration file (`SaveSystemProConfig.asset`):

*   **`Game Version`**: A standard string tag (e.g. `1.2.0`).

Every time a player saves their game, the engine embeds this build string directly into the slot's metadata.

---

## 🏗️ Step 2: The Migration Architecture
When the game client executes a load operation:
1. The engine checks the build version declared in the slot file's metadata.
2. If it matches the current game build version, the save is loaded directly.
3. If it detects a mismatch (the slot contains older version data), it delegates the migration process to the **`VersionMigrator.cs`** class.

---

## 💻 Step 3: Implementing a Migration Sequence
You can declare custom upgrade pipelines to handle transition changes smoothly:

```csharp
using DecnetGames.SaveSystemPro;
using System.Collections.Generic;
using UnityEngine;

public class GameMigrationHandler : MonoBehaviour
{
    private void Start()
    {
        // Register our custom migration callbacks
        VersionMigrator.RegisterMigration("1.0.0", "1.1.0", MigrateTo_v110);
        VersionMigrator.RegisterMigration("1.1.0", "1.2.0", MigrateTo_v120);
    }

    // Upgrade path from 1.0.0 to 1.1.0 (e.g., adding an inventory dictionary)
    private string MigrateTo_v110(string oldJson)
    {
        Debug.Log("[Migrator] Upgrading save format from 1.0.0 to 1.1.0...");
        
        // Custom upgrades: you can manipulate raw JSON nodes, or load into 
        // temporary classes and inject new default values:
        
        // For example, inject default currency variables missing in older builds:
        string updatedJson = oldJson.Replace("}", ", \"player_gems\": 10 }");
        
        return updatedJson;
    }

    // Upgrade path from 1.1.0 to 1.2.0 (e.g., balance stats adjustments)
    private string MigrateTo_v120(string oldJson)
    {
        Debug.Log("[Migrator] Upgrading save format from 1.1.0 to 1.2.0...");
        
        // Apply adjustments cleanly before deserialization
        return oldJson;
    }
}
```

---

> **Pro Tip**: Keep your migration callbacks modular and chronologically structured. If a user returns to your game after years of inactivity (e.g. they own a `1.0.0` save, but the game is now running version `1.2.0`), Save System Pro will automatically execute **daisy-chain migrations** (`1.0.0` $\rightarrow$ `1.1.0` $\rightarrow$ `1.2.0`) in order, restoring their save file flawlessly!
