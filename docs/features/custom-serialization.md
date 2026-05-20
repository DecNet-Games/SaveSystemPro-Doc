---
layout: default
title: Advanced C# Serializer
parent: Advanced Features
nav_order: 1
---

# Advanced C# Serializer

At the heart of Save System Pro is **`AdvancedSerializer.cs`** — a custom serialization engine written in pure C#. It bypasses the limitations of Unity's native `JsonUtility` without relying on bloated third-party DLLs (like Newtonsoft.Json) that can conflict with other assets or fail in IL2CPP web builds.

---

## 🛑 The JSON Utility Problem
Unity's built-in `JsonUtility` has several severe limitations for commercial games:
*   It **cannot** serialize generic dictionaries (`Dictionary<TKey, TValue>`).
*   It **cannot** serialize lists of primitives or nested arrays (`List<T>`, `T[]`).
*   It **fails** on basic Unity structures like `Vector3`, `Quaternion`, and `Color`, returning empty properties or throwing compilation warnings.

---

## 🛠️ The Save System Pro Solution: Wrapper Strategy
To resolve this, `AdvancedSerializer` maps complex, non-serializable objects and collections into highly optimized **custom wrapper classes** that Unity's native engine *can* serialize perfectly.

Here are the custom wrappers handled under the hood:

### 1. Unity Structures
```csharp
[Serializable]
public struct Vector3Wrapper
{
    public float x, y, z;
    public Vector3Wrapper(Vector3 v) { x = v.x; y = v.y; z = v.z; }
    public Vector3 ToVector3() => new Vector3(x, y, z);
}

[Serializable]
public struct ColorWrapper
{
    public float r, g, b, a;
    public ColorWrapper(Color c) { r = c.r; g = c.g; b = c.b; a = c.a; }
    public Color ToColor() => new Color(r, g, b, a);
}
```
*   Similar structures are defined for `Quaternion`, `Vector2`, `Vector4`, `Rect`, and `Bounds`.

### 2. C# Dictionaries & Dynamic Lists
Dynamic collections are automatically flattened into twin key-value array structures before serialization, and rebuilt back into hash-maps during loading:

```csharp
[Serializable]
public class DictionaryWrapper<TKey, TValue>
{
    public List<TKey> keys = new List<TKey>();
    public List<TValue> values = new List<TValue>();

    public DictionaryWrapper(Dictionary<TKey, TValue> dict)
    {
        foreach (var kvp in dict)
        {
            keys.Add(kvp.Key);
            values.Add(kvp.Value);
        }
    }

    public Dictionary<TKey, TValue> ToDictionary()
    {
        Dictionary<TKey, TValue> dict = new Dictionary<TKey, TValue>();
        for (int i = 0; i < keys.Count; i++)
        {
            dict[keys[i]] = values[i];
        }
        return dict;
    }
}
```

---

## 💻 API Usage Examples

Saving and loading dynamic types requires zero wrapper configuration on your end; the engine delegates mapping dynamically:

### Saving a C# Dictionary
```csharp
Dictionary<string, int> playerStats = new Dictionary<string, int>
{
    { "Kills", 150 },
    { "Deaths", 12 },
    { "GoldEarned", 54300 }
};

// Writes and serializes the dictionary to Slot 0
SaveSystemPro.Instance.SaveValue(0, "stats_dict", playerStats);
```

### Loading the Dictionary Back
```csharp
var loadedStats = SaveSystemPro.Instance.LoadValue<Dictionary<string, int>>(0, "stats_dict", new Dictionary<string, int>());
Debug.Log($"Stats Restored: Kills={loadedStats["Kills"]}");
```

---

> **Deep Technical Note (IL2CPP & AOT Compatibility)**:
> In IL2CPP builds (e.g. iOS, WebGL, Consoles), Unity strips unused code during ahead-of-time (AOT) compilation. `AdvancedSerializer` handles this by declaring explicit compiler hints, preventing Unity's link-editor from stripping generic wrappers and ensuring runtime stability across all target platforms.
