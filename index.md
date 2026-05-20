---
layout: default
title: Home (Getting Started)
nav_order: 1
---

# Save System Pro Documentation

Welcome to the definitive, developer-to-developer technical documentation for **Ultimate Save System Pro** — the complete, premium, military-grade encrypted JSON and cloud-ready storage backend for Unity. 

Ultimate Save System Pro represents a major leap in Unity local storage. It is built to offer the complete functionality of systems like *Easy Save 3*, but optimized specifically for modern, high-performance Unity games. It eliminates external third-party DLL dependencies (like Newtonsoft.Json) to guarantee zero package version conflicts, while delivering **AES-128 Encryption**, **GZIP Compression**, **Anti-Corruption Backups**, **99 Save Slots with metadata**, and a **fully automated component drag-and-drop auto-save suite**.

---

## ⚡ Key Features

*   **Zero DLL Dependency Strategy**: A custom, pure C# `AdvancedSerializer` handles structural Unity primitives and complex generic collections (like `Dictionary<TKey, TValue>`) natively. No imports of conflicting JSON DLLs.
*   **AES-128 Military Grade Encryption**: Keep your player save files fully secured from local hex editors and save-tampering.
*   **GZIP Data Compression**: Drastically reduce disk footprint and cloud transfer sizes for large RPG datasets.
*   **99 Save Slots + Metadata Tracker**: Built-in slot metadata manager tracking playtime (hours/minutes/seconds), last-saved dates, and specific game versions.
*   **Auto-Save Suite (No-Code Integration)**: Complete drag-and-drop workflow tracking Rigidbody physics (forces, angular speed), Game Object active states, and full Transform properties.
*   **Anti-Corruption Backups**: Active protection writing `.bak` files during saving. Automatically restores old data if power drops or write streams fail.
*   **Asynchronous I/O Threads**: Run massive save files on non-blocking threads to ensure zero frame stutter in action-packed scenes.

---

## 🚀 Pain vs Solution

| The Pain (Other Systems / DIY Systems) | The Solution (Save System Pro) |
| :--- | :--- |
| Handcrafting encryption, compression, and backups takes weeks of debugging. | **1-Click Security toggles.** AES-128, GZIP, and `.bak` corruption recovery are fully integrated. |
| Third-party Newtonsoft.Json imports cause package conflicts and compile failures. | **Zero-DLL architecture.** Custom pure C# wrappers handle generic dictionaries natively. |
| Player profiles get hacked locally, inflating stats and corrupting cloud sync. | **AES encryption obfuscates save files**, making local hex tampering impossible. |
| Complex RPG save files cause micro-stuttering, dropping target frame rates. | **Asynchronous operations** run massive write streams on separate threads smoothly. |

---

## 🛠️ Quick-Start Integration (1 Minute)

```csharp
using UnityEngine;
using System.Collections.Generic;
using DecnetGames.SaveSystemPro;

public class ProQuickStart : MonoBehaviour
{
    private void Start()
    {
        int activeSlot = 0;

        // 1. Save complex variables (like generic Dictionaries)
        Dictionary<string, int> inventory = new Dictionary<string, int>
        {
            { "Gold", 1200 },
            { "Elixirs", 5 }
        };
        SaveSystemPro.Instance.SaveValue(activeSlot, "inventory_dict", inventory);

        // 2. Save Unity structural types easily
        SaveSystemPro.Instance.SaveValue(activeSlot, "player_spawn", new Vector3(15f, 4f, -9f));

        // 3. Load variables back securely with automatic fallbacks
        var loadedInv = SaveSystemPro.Instance.LoadValue<Dictionary<string, int>>(activeSlot, "inventory_dict", new Dictionary<string, int>());
        Vector3 spawn = SaveSystemPro.Instance.LoadValue<Vector3>(activeSlot, "player_spawn", Vector3.zero);

        Debug.Log($"Inventory Restored: Gold={loadedInv["Gold"]}, Position={spawn}");
    }
}
```

---

## 📖 Explore the Documentation

*   [**Installation Guide**](docs/installation.html)
*   [**Quick-Start Guide**](docs/quick-start.html)
*   [**Advanced Features Deep-Dive**](docs/features/index.html)
    *   [Advanced C# Serializer](docs/features/custom-serialization.html)
    *   [99 Slot Metadata Manager](docs/features/slots-metadata.html)
    *   [Pro Key-Value DB Core](docs/features/key-value-db.html)
    *   [Transform Auto-Save Component](docs/features/auto-save-transform.html)
    *   [Rigidbody Physics Auto-Save](docs/features/auto-save-rigidbody.html)
    *   [Active State Hierarchy Auto-Save](docs/features/auto-save-active-state.html)
    *   [Global Auto-Save Manager](docs/features/auto-save-manager.html)
    *   [AES-128 Security Encryption](docs/features/security-encryption.html)
    *   [GZIP Compression & Backup Systems](docs/features/compression-backup.html)
    *   [Cloud Sync Ready Operations](docs/features/cloud-sync.html)
    *   [Save File Version Migrations](docs/features/version-migration.html)
*   [**Frequently Asked Questions (FAQ)**](docs/faq.html)
