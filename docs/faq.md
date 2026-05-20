---
layout: default
title: FAQ
nav_order: 5
---

# Frequently Asked Questions (FAQ)

Here are the most common questions developers ask when integrating and optimizing **Save System Pro**.

---

## ❓ 1. Do I need to buy a separate license for other platforms?
No. Save System Pro is fully cross-platform out-of-the-box. A single license covers all deployment targets supported by Unity, including Windows, macOS, Linux, iOS, Android, WebGL, Nintendo Switch, PlayStation 5, and Xbox Series X/S.

---

## ❓ 2. How are custom-made structs or classes serialized?
If you have a custom script or container class (e.g. `public class PlayerInventory`), simply mark the class signature with the standard Unity **`[System.Serializable]`** attribute:

```csharp
[System.Serializable]
public class SkillTree
{
    public string primarySkill = "DoubleJump";
    public int skillPointsSpent = 15;
}
```
You can now pass instances of `SkillTree` directly to `SaveValue()` and `LoadValue<SkillTree>()` and the custom serializer engine handles mapping and deep serialization automatically!

---

## ❓ 3. Is there a performance limit on encryption?
For 95% of standard games, AES-128 encryption executes in less than **2 milliseconds**, making it completely unnoticeable during gameplay. 

If your game writes massive datasets (e.g., millions of grid blocks in a voxel sandbox building game), we recommend performing save operations asynchronously using **`SaveAsync()`**, which runs the encryption tasks on background tasks to keep your main rendering thread running at maximum frame rates.

---

## ❓ 4. How does the system handle WebGL PlayerPrefs storage limits?
Most browsers restrict HTML5 LocalStorage to **5 Megabytes** per website. 
Save System Pro is highly optimized for WebGL targets:
1. **GZIP Compression** shrinks your JSON data strings down by 70% to 90% before writing, allowing you to fit far more gameplay hours and save entries within the 5MB browser boundary.
2. WebGL reads and writes utilize browser memory buffers, ensuring extremely fast operations.

---

## ❓ 5. How can I migrate my project from Save System Lite to Save System Pro?
Upgrading is fully seamless:
1. Purchase and import **Save System Pro** into your project.
2. Replace the namespace reference `using DecnetGames.SaveSystemLite;` with `using DecnetGames.SaveSystemPro;` in your scripts.
3. Replace your static class triggers `SaveSystemLite.SaveValue(...)` with their Pro equivalent `SaveSystemPro.Instance.SaveValue(slotIndex, ...)` which adds the capability to use up to 99 save slots!
