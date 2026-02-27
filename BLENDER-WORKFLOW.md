# Blender → Warehouse Workflow

## Naming Convention for Colliders

Name your objects in Blender with these prefixes:

| Prefix | Collider | Physics | Example |
|--------|----------|---------|---------|
| `floor_` | Trimesh | Static | `floor_main`, `floor_platform` |
| `wall_` | Trimesh | Static | `wall_north`, `wall_east` |
| `static_` | Trimesh | Static | `static_pillar`, `static_shelf` |
| `crate_` | Box | Dynamic | `crate_small`, `crate_wood_01` |
| `dynamic_` | Box | Dynamic | `dynamic_barrel` |
| `trigger_` | Box | Trigger (invisible) | `trigger_door`, `trigger_narrator` |
| `prop_` | None | - | `prop_lamp`, `prop_poster` |
| `decor_` | None | - | `decor_cobweb` |
| `light_` | None | - | `light_spot_01` |

## Export Settings

1. **File → Export → glTF 2.0 (.glb)**

2. **Format:** GLB (Binary)

3. **Include:**
   - ✓ Selected Objects (or all)
   - ✓ Custom Properties (if using)

4. **Transform:**
   - ✓ +Y Up

5. **Geometry:**
   - ✓ Apply Modifiers
   - ✓ UVs
   - ✓ Normals
   - ✓ Vertex Colors (if used)

6. **Materials:**
   - ✓ Export

7. **Images:**
   - Automatic (embeds in GLB)

## Material Setup

Use **Principled BSDF** for PBR materials:

| Blender Input | Three.js Output |
|---------------|-----------------|
| Base Color | color / map |
| Metallic | metalness |
| Roughness | roughness |
| Normal Map | normalMap |
| Emission | emissive / emissiveMap |
| Alpha | opacity / alphaMap |

## Loading in Code

```javascript
// Load the entire warehouse level
const level = await loadLevel('models/warehouse.glb');

// Create physics colliders from named objects
createCollidersFromLevel(level.objects);

// Access specific objects if needed
level.scene.traverse((child) => {
    if (child.name === 'artifact_pedestal') {
        // Do something special
    }
});
```

## Tips

1. **Keep collision geometry simple** - For complex visual meshes, create a separate simplified collision mesh named with `static_` prefix

2. **Check normals** - Trimesh colliders are one-sided. Make sure normals face outward (Mesh → Normals → Recalculate Outside)

3. **Apply transforms** - Before export: Ctrl+A → All Transforms

4. **Test scale** - Three.js units = meters. A 2-unit cube in Blender = 2 meters

5. **Dynamic objects** - Keep geometry simple for crates/throwables. Complex meshes use box approximation anyway.

## Custom Properties (Alternative)

Instead of naming convention, add Custom Properties in Blender:

1. Select object
2. Object Properties → Custom Properties
3. Add:
   - `collider`: "trimesh" | "box" | "convex" | "none"
   - `physics`: "static" | "dynamic"

These override the naming convention if present.
