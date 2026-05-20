---
layout: default
title: Advanced Features
nav_order: 4
has_children: true
---

# Advanced Features Deep-Dive

Welcome to the technical deep-dive of the core systems that drive **Ultimate Save System Pro**. Use the links below to explore the architecture, reflection interfaces, serialization wrappers, and security layers.

---

## 🏛️ Serialization & Key-Value DB Core

### [1. Advanced C# Serializer](custom-serialization.html)
Learn how we bypassed Unity's standard `JsonUtility` limitations to build a pure C# serialization engine. It natively wraps, serializes, and deserializes standard C# generic lists, generic dictionaries, multi-dimensional arrays, and complex Unity structures with **zero third-party DLL dependencies**.

### [2. 99 Slot Metadata Manager](slots-metadata.html)
Understand slot management. Learn how to configure up to 99 save slots, track playtime (h/m/s), monitor game build versions, record write timestamps, and leverage the built-in Editor Slot Manager GUI.

### [3. Pro Key-Value DB Backend](key-value-db.html)
Explore the database architecture under the hood. Discover how individual key updates are packed inside active slot instances, how the database handles key presence, and how bulk index retrievals are executed.

---

## 🤖 Component-Based Auto-Save Suite

### [4. Transform Auto-Save](auto-save-transform.html)
Track object transforms automatically. This page covers dragging and dropping the `AutoSaveTransform` script, configuring position/rotation/scale parameters, and integrating seamless lerped frame snapshots.

### [5. Rigidbody Physics Auto-Save](auto-save-rigidbody.html)
Rigidbodies present unique save challenges. Learn how `AutoSaveRigidbody` automatically tracks forces, linear and angular velocities, mass parameters, and kinematic state variables across both 2D and 3D physics frameworks.

### [6. Hierarchy Active State Auto-Save](auto-save-active-state.html)
Easily track the active state of dynamic GameObjects in scene hierarchies (activeSelf vs activeInHierarchy) during runtime scene transitions.

### [7. Global Auto-Save Manager](auto-save-manager.html)
The orchestration engine of the Auto-Save suite. Explore the custom **`AutoSaveIdentifier` Editor GUID generator**, and learn how `AutoSaveManager` tracks registered items globally to handle saves on disable or play mode exits automatically.

---

## 🔒 Security, Compression & Performance

### [8. AES-128 Security Encryption](security-encryption.html)
Discover the mechanics behind our AES-128 encryption algorithm. Secure player save states from local hex editor adjustments, configure strong security keys, and manage configuration databases.

### [9. GZIP Compression & Auto-Backups](compression-backup.html)
Configure file sizes and data stability. Learn about shrinking large JSON files on disk using GZIP compression, and deploying our `.bak` file auto-recovery system to protect against write stream crashes and corrupted sectors.

### [10. Asynchronous Cloud-Ready Operations](cloud-sync.html)
Achieve optimal performance with non-blocking async threads. Explore Firebase, PlayFab, and Steam Cloud integrations, and review our event framework triggers.

### [11. Save File Version Migrations](version-migration.html)
Manage save state structure upgrades smoothly. Learn how to declare game version build numbers in your configurations and trigger data format upgrades automatically when users patch their clients.
