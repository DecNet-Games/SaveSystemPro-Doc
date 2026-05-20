---
layout: default
title: Installation
nav_order: 2
---

# Installation Guide

To protect development cycles and ensure full developer licensing, **Save System Pro** is distributed exclusively via the Unity Asset Store. Do not clone raw files from third-party public git registries.

---

## 🛠️ Step 1: Add to Your Assets
1. Open the [Unity Asset Store](https://assetstore.unity.com/) in your browser.
2. Complete your purchase of **Ultimate Save System Pro – Encrypted JSON, Save Slots & Cloud Ready**.
3. Click **Add to My Assets** to link the license to your Unity Developer Account.

---

## 📥 Step 2: Download & Import in Unity
1. In the Unity Editor, open the **Package Manager** (`Window > Package Manager`).
2. Switch the top-left dropdown filter to **My Assets**.
3. Locate **Save System Pro** in your list of assets.
4. Click **Download** in the bottom-right corner.
5. Once downloaded, click **Import**.
6. Keep all files, directories, and assets checked in the import dialog, and click **Import** to merge the folder into your project's `Assets/SaveSystemPro` directory.

---

## 🏗️ Step 3: Welcome and Auto-Configuration
Once imported, a custom Unity compiler script will automatically run to verify your environment and instantiate a secure configuration database:
1. The **Ultimate Save System Pro Welcome & Setup** window will automatically launch on screen.
2. In the background, the setup scripts will automatically instantiate a `SaveSystemProConfig.asset` configuration database in `Assets/SaveSystemPro/Resources/` to store your encryption keys and slot settings.
3. Click **Open Control Panel** in the welcome window to customize slots and security, or click **Generate Pro Demo Scene** to programmatically build an interactive playground in your editor.

---

## 📦 Assembly Definitions (ASMDEF)
Save System Pro incorporates clean physical assembly separations using Unity's modern compilation system:
*   **`SaveSystemPro.Runtime.asmdef`**: Contains the core serializer, Key-Value database backend, and runtime Auto-Save scripts. Compiles into a separate runtime library.
*   **`SaveSystemPro.Editor.asmdef`**: Houses custom Inspector windows, the Slot Manager, the Pro Control Panel, and welcome panels. Configured strictly for the Editor build compilation phase.

### Reference Configuration:
If your game codebase compiles under a custom assembly definition (e.g. `Game.asmdef`), you must add a reference to `SaveSystemPro.Runtime` within your asmdef inspector panel. 

> **Important**: Never add a reference to `SaveSystemPro.Editor` in your runtime assembly. Doing so will cause severe errors during standalone executable builds (Windows/Mac/Linux/WebGL/Console) as Editor namespaces are not compiled in final builds.
