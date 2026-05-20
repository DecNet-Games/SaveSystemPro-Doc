---
layout: default
title: GZIP Compression & Auto-Backups
parent: Advanced Features
nav_order: 9
---

# GZIP Compression & Auto-Backups

Large-scale RPGs, strategy games, or sandbox builders write massive amounts of data to local save files. Left unoptimized, this results in large files that consume local disk space and lead to slower cloud sync transfers. 

Furthermore, if a player's machine loses power, the game crashes, or a write stream is interrupted while saving, the slot file will corrupt, destroying the player's entire playthrough progress.

Save System Pro addresses both challenges with **GZIP Compression** and **Anti-Corruption Backups**.

---

## 🗜️ 1. GZIP Data Compression
GZIP is an industry-standard lossless data compression algorithm. It parses repeating patterns in JSON text data and shrinks them down dynamically.
*   **Drastic Size Reductions**: Average compression ratios of 70% to 90% for large JSON profiles.
*   **Fast I/O Performance**: Smaller file footprints translate to shorter disk write and read times.
*   **Reduced Cloud Budgets**: Ideal for cloud sync platforms (e.g. Firebase, PlayFab, Steam Cloud) as it lowers network bandwidth costs.

### Activating Compression:
1. Open the Pro Control Panel (`Tools > Save System Pro > Control Panel`).
2. Toggle the check box for **GZIP Compression** to **On**.
3. Save System Pro will now compress all slot files automatically.

---

## 🛡️ 2. Anti-Corruption Backups (`.bak` restoration)
Save corruption almost always occurs *during the write stream*, when an active file is opened, truncated, and partially written before a crash occurs.

To prevent this catastrophy, Save System Pro uses a **Double-Buffering write technique** under the hood:

```mermaid
graph TD
    A[Start Save Operation] --> B{Verify Existing Save?}
    B -- Yes -- > C[Rename current file to .bak]
    B -- No --> D[Proceed with Write]
    C --> D
    D --> E[Write fresh data to slot.json]
    E --> F{Write Successful?}
    F -- Yes --> G[Delete temporary .bak file]
    F -- No --> H[Restore old .bak file back to slot.json]
    H --> I[Trigger OnSaveCorrupted Event]
```

### Automatic Recovery Flow:
When the game client executes `Load<T>()`:
1. The engine checks if `slot.json` is missing or corrupt.
2. If it detects a signature failure or empty file, it checks if a corresponding `slot.json.bak` backup file exists.
3. If the backup exists, it automatically recovers and restores the `.bak` file back to the primary slot path and loads the player's previous save seamlessly!
4. The system triggers the static event `SaveEvents.OnSaveCorrupted(slotIndex)` so your game UI can display a reassuring warning notification (e.g., "Save corrupted. Successfully restored backup file!").
