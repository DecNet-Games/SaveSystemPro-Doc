---
layout: default
title: AES-128 Security Encryption
parent: Advanced Features
nav_order: 8
---

# AES-128 Security Encryption

Local save files written in standard JSON format are stored as human-readable text. While this is great for developer debugging, it presents a major security vulnerability for released games: players can open save directories, edit their currency totals or unlocked items using a text editor, and easily bypass game loops, leaderboard metrics, or store systems.

Save System Pro eliminates this vulnerability with integrated **AES-128 Military Grade Encryption**.

---

## 🔒 What is AES-128?
Advanced Encryption Standard (AES) is a symmetric block cipher chosen by the U.S. government to protect classified information. 
*   It encrypts plain text data into randomized, non-human-readable bytes.
*   Decryption is only possible if the game client provides the exact match **128-bit key**.
*   Attempts to tamper with or modify the encrypted bytes corrupt the file signature, which our engine detects instantly, preventing corrupt data loads.

---

## ⚙️ Activating Encryption in the Control Panel
Activating security requires zero programming:
1. Open the Pro Control Panel via `Tools > Save System Pro > Control Panel`.
2. Scroll to the **Security & Optimization** header.
3. Check the box for **AES-128 Encryption**.
4. In the **Encryption Key** text input field, type your custom security password. 

> [!IMPORTANT]
> To guarantee strong security, your encryption key must be **exactly 16 characters** long (128 bits). If it is shorter, the Control Panel will draw a yellow safety warning box prompting you to increase key complexity!

---

## 💻 Manual Cryptography API
If you are implementing custom cloud syncs, custom data transfers, or manual files and need to encrypt raw strings, you can leverage our security wrappers directly:

```csharp
using DecnetGames.SaveSystemPro;

string rawJson = "{ \"player_gold\": 99999 }";
string secureKey = "MySecretKey_12345"; // Must be 16 chars

// 1. Encrypt raw string
string encryptedString = AESEncryptor.Encrypt(rawJson, secureKey);
Debug.Log($"Encrypted: {encryptedString}"); // Outputs base64 scrambled text

// 2. Decrypt raw string back
string decryptedJson = AESEncryptor.Decrypt(encryptedString, secureKey);
Debug.Log($"Decrypted: {decryptedJson}"); // Outputs: { "player_gold": 99999 }
```

---

## 🔬 Auditing Saves: The Pro Save Inspector
When encryption is active, opening slot files on disk displays binary scrambled bytes. For rapid testing and debugging:
1. Open `Tools > Save System Pro > Save Inspector`.
2. Select your slot and click **Load & Decrypt Slot**.
3. The editor queries the key configured in `SaveSystemProConfig.asset` dynamically, decrypts the slot file in memory, and displays it in a clean text field.
4. Edit variables directly inside the text field, click **Save Changes & Encrypt**, and the editor automatically re-encrypts the data and writes it back to disk!
