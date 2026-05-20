---
layout: default
title: Rigidbody Physics Auto-Save
parent: Advanced Features
nav_order: 5
---

# Rigidbody Physics Auto-Save

Saving moving physics elements (e.g., rolling boulders, thrown objects, ragdoll segments, interactive items) requires more than just saving standard coordinates. If you restore an active rigid object's position without restoring its physics parameters, gravity forces will stop, and kinetic energy is lost instantly.

To address this challenge, Save System Pro features **`AutoSaveRigidbody`** — a drag-and-drop component that tracks active physics properties across both 3D and 2D physics worlds.

---

## 🤖 What does it track?
When attached to a GameObject, `AutoSaveRigidbody` automatically queries the object's `Rigidbody` (3D) or `Rigidbody2D` component and tracks:
*   **Linear Velocity (`Vector3` / `Vector2`)**: Preserves moving speed direction and momentum.
*   **Angular Velocity (`Vector3` / `float`)**: Preserves spinning velocity and rotational inertia.
*   **Is Kinematic (`bool`)**: Restores kinematic/gravity toggles.
*   **Is Sleeping (`bool`)**: Maintains physics optimizer sleep cycles to prevent unnecessary scene calculations.

---

## ⚙️ Drag-and-Drop Setup

To automate physics tracking:
1. Select the physics-based GameObject containing a `Rigidbody` or `Rigidbody2D` component.
2. Ensure it has an **`AutoSaveIdentifier`** component attached.
3. Click **Add Component** in the Inspector.
4. Search for and select **`AutoSaveRigidbody`**.
5. The component automatically detects if it needs to query the 3D or 2D physics system on start and configures itself!

---

## 🛠️ Custom Inspector Parameters

| Parameter | Type | Description |
| :--- | :--- | :--- |
| **`Save Velocity`** | bool | Toggles tracking of linear speed vectors. |
| **`Save Angular Velocity`** | bool | Toggles tracking of rotational spin rates. |
| **`Save Physics State`** | bool | Toggles tracking of kinematic status and sleep triggers. |

---

## 💻 Technical Code Overview

Here is how `AutoSaveRigidbody` captures and restores states under the hood without causing frame spikes:

### Snapshot Capture (Saving)
```csharp
public void SaveRigidbodyState(string guid, int slotIndex)
{
    if (is3D && rb3d != null)
    {
        SaveSystemPro.Instance.SaveValue(slotIndex, guid + "_rb_vel", rb3d.velocity);
        SaveSystemPro.Instance.SaveValue(slotIndex, guid + "_rb_ang", rb3d.angularVelocity);
        SaveSystemPro.Instance.SaveValue(slotIndex, guid + "_rb_kin", rb3d.isKinematic);
    }
    else if (!is3D && rb2d != null)
    {
        SaveSystemPro.Instance.SaveValue(slotIndex, guid + "_rb_vel2d", rb2d.velocity);
        SaveSystemPro.Instance.SaveValue(slotIndex, guid + "_rb_ang2d", rb2d.angularVelocity);
        SaveSystemPro.Instance.SaveValue(slotIndex, guid + "_rb_kin2d", rb2d.isKinematic);
    }
}
```

### State Reconstruction (Loading)
```csharp
public void LoadRigidbodyState(string guid, int slotIndex)
{
    if (is3D && rb3d != null)
    {
        rb3d.velocity = SaveSystemPro.Instance.LoadValue<Vector3>(slotIndex, guid + "_rb_vel", Vector3.zero);
        rb3d.angularVelocity = SaveSystemPro.Instance.LoadValue<Vector3>(slotIndex, guid + "_rb_ang", Vector3.zero);
        rb3d.isKinematic = SaveSystemPro.Instance.LoadValue<bool>(slotIndex, guid + "_rb_kin", false);
    }
    // ... handles 2D variants automatically
}
```

---

> **Pro Tip**: Physics variables can fluctuate rapidly. To prevent collision glitches at the exact moment of loading, `AutoSaveRigidbody` automatically forces the Rigidbody to briefly sleep, snaps the positions, applies the velocities, and awakens the Rigidbody in the next frame update smoothly.
