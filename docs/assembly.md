# assembly guide

## arm structure

Luminogotchi uses a **parallelogram linkage** for each arm segment — two parallel steel rods per segment rather than one. This mirrors the mechanism in the original Luxo L-1 desk lamp and gives the arm lateral stability under servo torque.

```
   [HEAD]
     │ ╲
     │  ╲  ← mid segment (2 parallel rods)
     │   ╲
   [MID JOINT]
     │ ╲
     │  ╲  ← lower segment (2 parallel rods)
     │   ╲
   [BASE JOINT]
     │
   [BASE DISC]
```

Total height: ~60cm fully extended.

## joint blocks (3D printed)

Each joint block (file: `cad/joint_block.stl`) has:
- A servo body slot on one face (MG996R dimensions)
- Two rod clamp holes (4mm dia) on the opposite face
- M3 mounting holes for the servo horn attachment
- M3 holes to bolt to the adjacent segment's rod clamps

Print in SLA resin via PCBWay for tight dimensional tolerances on the servo slot. FDM is fine for prototyping but the servo tends to wiggle.

## assembly order

1. **Base disc** — bolt S1 (base servo) into the base joint block. Mount to base disc with M3 bolts. Fill base disc cavity with sand or coins for stability.
2. **Lower segment** — feed two steel rods through lower joint block rod clamps. Tighten. Attach servo horn of S1 to lower joint block top face.
3. **Mid segment** — repeat for S2 and S3. S2 drives the lower→mid joint, S3 drives the mid→head joint.
4. **Head** — S4 controls head tilt. Lampshade bolts to head joint block. LED strip sits inside shade, wires route back down through the rod channels.
5. **Touch wiring** — solder a wire to the inside of the lampshade before closing it up. Route all touch wires down through the rods alongside servo signal wires.

## bonding

Use Araldite 2-part epoxy to fix servo brackets to rod ends where M3 bolts alone aren't enough. Let cure 24h before applying load.

## cad files

| File | Description |
|---|---|
| `cad/joint_block.stl` | Main servo joint block — print x4 |
| `cad/base_foot.stl` | Weighted base disc |
| `cad/shade_template.dxf` | Flat cone unroll for sheet metal cutting |
| `cad/assembly.step` | Full assembly reference |

*CAD files in progress — designed in Onshape, exports will be added once finalised.*
