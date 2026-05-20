---
layout: default
title: Asynchronous Cloud-Ready Operations
parent: Advanced Features
nav_order: 10
---

# Asynchronous Cloud-Ready Operations

For large-scale, high-fidelity games, performing disk operations on Unity's main thread is dangerous. Reading or writing multi-megabyte save files synchronously blocks the main thread, resulting in visible frame drops (micro-stutters) that compromise the player's experience.

Additionally, modern games require cloud backups (e.g. Steam Cloud, Firebase, PlayFab). Save System Pro provides **Asynchronous Operations** and an **event-driven architecture** built to integrate with cloud platforms seamlessly.

---

## ⚡ 1. Asynchronous I/O Operations
Save System Pro supports non-blocking asynchronous threads. This offloads the file formatting, GZIP compression, and AES encryption tasks to background C# threads, keeping your game's frame rate perfectly smooth:

```csharp
using DecnetGames.SaveSystemPro;
using UnityEngine;

public class AsyncSaveController : MonoBehaviour
{
    private async void SaveGameAsync()
    {
        PlayerData currentData = GatherPlayerData();
        
        Debug.Log("Starting background save thread...");

        // Save asynchronously on a non-blocking background task
        bool success = await SaveSystemPro.Instance.SaveAsync(0, currentData, Time.time);

        if (success)
        {
            Debug.Log("Save file safely written on background thread!");
        }
    }
}
```

---

## ☁️ 2. Cloud Integration Architecture
Because Save System Pro handles files on disk as encapsulated buffers, integrating with custom cloud database backends like **PlayFab** or **Firebase** is straightforward. 

You can read the raw bytes of a slot file and push them to the cloud, or download cloud buffers and write them to the local directory:

```csharp
using System.IO;
using DecnetGames.SaveSystemPro;

// 1. Preparing local slot data for cloud upload
string localPath = SlotManager.GetSavePath(0);
if (File.Exists(localPath))
{
    // Read the encrypted, GZIP-compressed binary bytes directly
    byte[] localDataBytes = File.ReadAllBytes(localPath);
    
    // Push 'localDataBytes' to Firebase Storage or PlayFab User Data!
    UploadToPlayFabCloud(localDataBytes);
}

// 2. Applying data downloaded from the cloud
void OnCloudDownloadComplete(byte[] downloadedBytes)
{
    string localPath = SlotManager.GetSavePath(0);
    
    // Write bytes directly to local slot path
    File.WriteAllBytes(localPath, downloadedBytes);
    
    // Force Save System Pro to load the fresh cloud profile
    PlayerData cloudProfile = SaveSystemPro.Instance.Load<PlayerData>(0);
    ApplyProfileToGame(cloudProfile);
}
```

---

## 📣 3. System Event Pipeline
To coordinate loading indicators, auto-saving warnings, or database checks, you can subscribe to our high-performance **static events** from any class:

```csharp
using DecnetGames.SaveSystemPro;

private void OnEnable()
{
    SaveEvents.OnSaveComplete += HandleSaveComplete;
    SaveEvents.OnLoadComplete += HandleLoadComplete;
    SaveEvents.OnSaveFailed += HandleSaveFailed;
    SaveEvents.OnSaveCorrupted += HandleSaveCorrupted;
}

private void OnDisable()
{
    SaveEvents.OnSaveComplete -= HandleSaveComplete;
    SaveEvents.OnLoadComplete -= HandleLoadComplete;
    SaveEvents.OnSaveFailed -= HandleSaveFailed;
    SaveEvents.OnSaveCorrupted -= HandleSaveCorrupted;
}

private void HandleSaveComplete(int slot) { /* Play UI "Save Successful" animation */ }
private void HandleLoadComplete(int slot) { /* Fade out loading screen */ }
private void HandleSaveFailed(SaveResult result) { /* Show "Disk Full" error */ }
private void HandleSaveCorrupted(int slot) { /* Show "Restored from Backup" notice */ }
```
