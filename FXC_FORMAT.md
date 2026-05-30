# God of War II PS2 - FXC file format documentation

Version: 1.0  
Target: SCUS_974.67 / God of War II PS2 dev-symbol ELF  
Endian: little endian  
Scope: loose FXC resources such as `FXC_*` and the corresponding FX client load parameters inside WAD/IFF resources.

## 1. What an FXC resource is

`FXC` is not a single effect format with one fixed payload. It is a serialized **fx client load parameter**. The first word identifies the engine server, and the second word identifies the concrete FX client/factory type.

In the samples analyzed here, every file starts with server id `0x0019`, decimal `25`, which matches the engine FX server. The second word is the client/factory id:

| Type id | ELF factory/client class | Payload structure | Size |
|---:|---|---|---:|
| 0 | `fxParticleEmitterClient` | `fxParticleEmitterLoadParm` | 0xE4 |
| 1 | `fxDirectionalEmitter` | `fxParticleEmitterLoadParm` | 0xE4 |
| 2 | `fxOmniEmitter` | `fxParticleEmitterLoadParm` | 0xE4 |
| 3 | `fxPolySurfaceEmitter` | `fxParticleEmitterLoadParm` | 0xE4 |
| 4 | `fxCurveEmitter` | `fxParticleEmitterLoadParm` | 0xE4 |
| 5 | `fxCubeEmitter` | `fxParticleEmitterLoadParm` | 0xE4 |
| 6 | `fxSphereEmitter` | `fxParticleEmitterLoadParm` | 0xE4 |
| 7 | `fxCylinderEmitter` | `fxParticleEmitterLoadParm` | 0xE4 |
| 8 | `fxConeEmitter` | `fxParticleEmitterLoadParm` | 0xE4 |
| 9 | `fxTorusEmitter` | `fxParticleEmitterLoadParm` | 0xE4 |
| 10 | `fxSkeletonEmitter` | `fxParticleEmitterLoadParm` | 0xE4 |
| 11 | `fxTrail` | `fxTrailLoadParm` | 0x70 |
| 12 | `fxField` | `fxFieldLoadParm` | 0xB4 |
| 13 | `fxGeometry` | `fxGeometryLoadParm` | 0x88 |

The samples provided contain type 1, 2, 3, 12 and 13.

## 2. Base inheritance chain

The ELF debug symbols reveal the following inheritance chain:

```c
svrLoadParm                  // 0x04
  -> goAttachmentLoadParm    // 0x50
       -> fxLoadParm         // 0x54
            -> concrete FX payload
```

All FXC files begin with this common 0x54-byte base.

## 3. `svrLoadParm` - 0x04 bytes

| Offset | Type | Name | Meaning |
|---:|---|---|---|
| 0x00 | u16 | `serverID` | Always `0x0019` for FX server in the analyzed FXC files. |
| 0x02 | u16 | `clientID` | FX client/factory id. Controls which payload layout follows. |

Example:

```text
19 00 02 00 = FX server 25, OmniEmitter client 2
19 00 0C 00 = FX server 25, Field client 12
19 00 0D 00 = FX server 25, Geometry client 13
```

## 4. `goAttachmentLoadParm` - 0x50 bytes

`goAttachmentLoadParm` extends `svrLoadParm`. It provides the attachment information used to place the FX relative to the owning object, transform, animation set or local matrix.

| Offset | Type | Name | Meaning |
|---:|---|---|---|
| 0x00 | u16 | `serverID` | inherited from `svrLoadParm`. |
| 0x02 | u16 | `clientID` | inherited from `svrLoadParm`. |
| 0x04 | u32 | `fGroupID` | FX group id / grouping index. Usually zero in the samples. |
| 0x08 | s16 | `fTransformID` | Transform id used for attachment. |
| 0x0A | s16 | `fAnimationSetID` | Animation set id. `-1`/`0xFFFF` often means none. |
| 0x0C | u32 | `fAttachmentFlags` | Attachment flags. Unknown bit semantics; zero in all analyzed samples. |
| 0x10 | f32[16] | `fLocalMatrix` | Local transform matrix, 4x4 floats. Identity in most samples, but may contain scale/rotation/translation. |

### Matrix layout

The stored matrix is 16 `float32` values. Identity appears as:

```text
1 0 0 0
0 1 0 0
0 0 1 0
0 0 0 1
```

## 5. `fxLoadParm` - 0x54 bytes

`fxLoadParm` extends `goAttachmentLoadParm`.

| Offset | Type | Name | Meaning |
|---:|---|---|---|
| 0x50 | u32 | `fParmSize` | Total size of this FXC load parameter. Must match file size for loose FXC samples. |

Observed values:

| Payload | `fParmSize` |
|---|---:|
| Particle emitter | 0xE4 / 228 |
| Field | 0xB4 / 180 |
| Geometry | 0x88 / 136 |
| Trail | 0x70 / 112, confirmed by ELF symbols, no sample in this set |

## 6. Particle emitter payload - `fxParticleEmitterLoadParm`, 0xE4 bytes

Used by client types 0 to 10.

```c
struct fxParticleEmitterLoadParm : fxLoadParm
{
    char                fParticleSystemIdentifier[24];
    char                fGeometryIdentifier[24];
    fxEmitterAttributes fAttributes;
    float               fLODNear;
    float               fLODFar;
};
```

### Layout

| Offset | Type | Name | Meaning |
|---:|---|---|---|
| 0x54 | char[24] | `fParticleSystemIdentifier` | Name of the particle system resource, usually matching a PTC/particle shape. |
| 0x6C | char[24] | `fGeometryIdentifier` | Optional geometry identifier. Used especially by `PolySurfaceEmitter`. Empty for most omni/directional emitters. |
| 0x84 | struct | `fAttributes` | Emitter numeric attributes, 0x58 bytes. |
| 0xDC | f32 | `fLODNear` | Near LOD distance. `-1.0` in samples means disabled/default. |
| 0xE0 | f32 | `fLODFar` | Far LOD distance. `-1.0` in samples means disabled/default. |

### `fxEmitterAttributes` - 0x58 bytes

| Offset | Type | Field | Practical effect |
|---:|---|---|---|
| 0x84 | vec3 | `fDirection` | Main emission direction vector. |
| 0x90 | f32 | `fSpread` | Spread/cone/random emission angle. |
| 0x94 | f32 | `fSpeed` | Base particle velocity. |
| 0x98 | f32 | `fSpeedRandom` | Random velocity variation. |
| 0x9C | f32 | `fMinDistance` | Minimum distance for emission. |
| 0xA0 | f32 | `fMaxDistance` | Maximum distance for emission. |
| 0xA4 | f32 | `fRate` | Emission rate / particle spawn rate. |
| 0xA8 | f32 | `fTangentSpeed` | Tangential velocity contribution. |
| 0xAC | f32 | `fNormalSpeed` | Normal-axis velocity contribution. |
| 0xB0 | vec3 | `fVolumeOffset` | Offset of the emission volume. |
| 0xBC | f32 | `fVolumeSweep` | Sweep angle. Full sweep is about 6.283185. |
| 0xC0 | f32 | `fSectionRadius` | Radius of the emission section/volume. |
| 0xC4 | f32 | `fSpeedAwayCenter` | Velocity away from center. |
| 0xC8 | f32 | `fSpeedAwayAxis` | Velocity away from axis. |
| 0xCC | f32 | `fSpeedAlong` | Velocity along axis. |
| 0xD0 | f32 | `fSpeedAround` | Swirl/orbit velocity around axis. |
| 0xD4 | f32 | `fRandomDirection` | Random direction strength. |
| 0xD8 | f32 | `fDirectionalSpeed` | Extra velocity in `fDirection`. |

### Particle emitter client types

The payload layout is the same for ids 0..10. The type controls how the runtime interprets the same attributes:

| Type id | Emitter type | Notes |
|---:|---|---|
| 0 | Generic particle emitter | Basic particle emitter client. |
| 1 | Directional emitter | Direction vector and directional speed are important. |
| 2 | Omni emitter | Emits outward/omnidirectional particles. |
| 3 | Poly surface emitter | Uses `fGeometryIdentifier` to emit from a geometry/surface. |
| 4 | Curve emitter | Curve-based emission. |
| 5 | Cube emitter | Volume emission from cube. |
| 6 | Sphere emitter | Volume emission from sphere. |
| 7 | Cylinder emitter | Volume emission from cylinder. |
| 8 | Cone emitter | Cone-shaped emission. |
| 9 | Torus emitter | Donut/ring-shaped emission. |
| 10 | Skeleton emitter | Emits from skeleton/joints. |

## 7. Field payload - `fxFieldLoadParm`, 0xB4 bytes

Used by client type 12. Fields modify particle motion or apply forces such as gravity, wind, vortex, noise, radial pull/push and directional influence.

```c
struct fxFieldLoadParm : fxLoadParm
{
    uint32            fFlags;
    fxFieldAttributes fAttributes;
};
```

### Layout

| Offset | Type | Name | Meaning |
|---:|---|---|---|
| 0x54 | u32 | `fFlags` | Field mode flags. Bit names not fully resolved yet. |
| 0x58 | struct | `fAttributes` | Field numeric attributes, 0x5C bytes. |

### `fxFieldAttributes` - 0x5C bytes

| Offset | Type | Field | Practical effect |
|---:|---|---|---|
| 0x58 | f32 | `fMagnitude` | Strength of the field. Negative values can pull/down depending mode. |
| 0x5C | f32 | `fAttenuation` | Distance attenuation/falloff. |
| 0x60 | vec3 | `fDirectionAxis` | Main field axis. |
| 0x6C | vec3 | `fVolumeOffset` | Field volume offset. |
| 0x78 | f32 | `fVolumeSweep` | Sweep angle. Full sweep is about 6.283185. |
| 0x7C | f32 | `fSectionRadius` | Radius for section/volume field. |
| 0x80 | f32 | `fMinDistance` | Minimum influence distance. |
| 0x84 | f32 | `fMaxDistance` | Maximum influence distance. `-1.0` usually means unlimited/default. |
| 0x88 | f32 | `fSpeed` | Field speed / animation speed. |
| 0x8C | f32 | `fInheritVelocity` | Parent velocity inheritance. |
| 0x90 | f32 | `fRadialType` | Radial mode selector stored as float. |
| 0x94 | f32 | `fSpread` | Spread/randomness. |
| 0x98 | f32 | `fFrequency` | Wave/noise frequency. |
| 0x9C | f32 | `fNoiseLevel` | Noise amplitude. |
| 0xA0 | f32 | `fNoiseRatio` | Noise mix/ratio. |
| 0xA4 | f32 | `fAwayFromCentreAxis` | Force away from center axis. |
| 0xA8 | f32 | `fAlongAxis` | Force along axis. |
| 0xAC | f32 | `fAroundAxis` | Swirl/rotation force. |
| 0xB0 | f32 | `fDirectionalSpeed` | Directional speed/force. |

## 8. Geometry payload - `fxGeometryLoadParm`, 0x88 bytes

Used by client type 13. It registers geometry that emitters can reference by identifier.

```c
struct fxGeometryLoadParm : fxLoadParm
{
    uint32 fFlags;
    char   fGeometryName[24];
    char   fIdentifier[24];
};
```

### Layout

| Offset | Type | Name | Meaning |
|---:|---|---|---|
| 0x54 | u32 | `fFlags` | Geometry flags. Observed value `1` in geometry samples. |
| 0x58 | char[24] | `fGeometryName` | Model/mesh resource name, for example `MSH_BDepoly3Shape`. |
| 0x70 | char[24] | `fIdentifier` | Runtime identifier referenced by emitters, for example `BDepoly3Shape`. |

Relationship example:

```text
FXC_BDepoly3
  fGeometryName = MSH_BDepoly3Shape
  fIdentifier   = BDepoly3Shape

FXC_BDEsparkemit0
  type id = 3 / PolySurfaceEmitter
  fGeometryIdentifier = BDepoly3Shape
```

The geometry FXC registers the identifier, and the poly-surface emitter references it.

## 9. Trail payload - `fxTrailLoadParm`, 0x70 bytes

No trail sample was included in this set, but the ELF debug symbols confirm this structure and size.

```c
struct fxTrailLoadParm : fxLoadParm
{
    char  fCurveName[24];
    float m_fTrailLength;
};
```

### Layout

| Offset | Type | Name | Meaning |
|---:|---|---|---|
| 0x54 | char[24] | `fCurveName` | Curve resource used by the trail. |
| 0x6C | f32 | `m_fTrailLength` | Trail length/time span. |

## 10. Strings

All FXC strings are fixed 24-byte ASCII buffers.

Rules for safe editing:

- Maximum visible string length: 23 characters if null-terminated.
- Preserve zero padding after the first null byte.
- Do not write UTF-8 multibyte characters unless the game resource system is confirmed to accept them.
- If a name is referenced by another file, update all references consistently.

## 11. Validation rules

A robust FXC parser/editor should validate:

1. File size must be at least 0x54.
2. `serverID` must be `0x0019` for FXC.
3. `clientID` must be a known type id.
4. `fParmSize` must equal file length for loose FXC files.
5. Payload size must match the type:
   - ids 0..10 -> 0xE4
   - id 11 -> 0x70
   - id 12 -> 0xB4
   - id 13 -> 0x88
6. Strings should be ASCII and stay within 24-byte buffers.
7. For emitter attributes:
   - `fVolumeSweep` often defaults to `2*pi`.
   - `fLODNear`/`fLODFar` can be `-1.0`.
   - Very high `fRate` values can create heavy particle load.
8. For matrix editing, preserve exact float order unless the editor exposes a tested transform conversion.

## 12. Editing recommendations

### Safer edits

| Goal | Fields |
|---|---|
| Increase/decrease particle amount | `fRate`, particle system PTC max particles, particle life |
| Make particles faster/slower | `fSpeed`, `fDirectionalSpeed`, `fNormalSpeed`, `fSpeedAwayCenter`, `fSpeedAwayAxis` |
| Make emission wider/narrower | `fSpread`, `fVolumeSweep`, `fSectionRadius` |
| Attach effect to different animation set | `fTransformID`, `fAnimationSetID` |
| Change poly-surface source | `fGeometryIdentifier` on emitter plus matching geometry FXC `fIdentifier` |
| Change field strength | `fMagnitude`, `fDirectionalSpeed`, `fAwayFromCentreAxis`, `fAlongAxis`, `fAroundAxis` |


## 13. Sample inventory

The following table summarizes the analyzed files.

| File | Size | Type | Key data |
|---|---:|---|---|
| `FXC_gravityField5` | 180 | 12 - Field | Flags=0x00000003; Magnitude=-20.0; Axis=[0.0, 1.0, 0.0] |
| `FXC_BDepoly3` | 136 | 13 - Geometry | Geom=MSH_BDepoly3Shape; ID=BDepoly3Shape; Flags=0x00000001 |
| `FXC_BDepoly6` | 136 | 13 - Geometry | Geom=MSH_BDepoly6Shape; ID=BDepoly6Shape; Flags=0x00000001 |
| `FXC_BDEsparkemit` | 228 | 3 - PolySurfaceEmitter | PS=flame6Shape; Geo=BDepoly6Shape; LOD=-1.0/-1.0 |
| `FXC_BDEsparkemit0` | 228 | 3 - PolySurfaceEmitter | PS=flame3Shape; Geo=BDepoly3Shape; LOD=-1.0/-1.0 |
| `FXC_BDEsparkemit2` | 228 | 2 - OmniEmitter | PS=flame5Shape; Geo=-; LOD=-1.0/-1.0 |
| `FXC_BGTemit2` | 228 | 2 - OmniEmitter | PS=BGTpar2Shape; Geo=-; LOD=-1.0/-1.0 |
| `FXC_EGemit` | 228 | 2 - OmniEmitter | PS=EGpartShape; Geo=-; LOD=-1.0/-1.0 |
| `FXC_EGemit1` | 228 | 2 - OmniEmitter | PS=EGpart1Shape; Geo=-; LOD=-1.0/-1.0 |
| `FXC_Fcloudemit4` | 228 | 1 - DirectionalEmitter | PS=FcloudpartShape4; Geo=-; LOD=-1.0/-1.0 |
| `FXC_flashemit4` | 228 | 2 - OmniEmitter | PS=FlashpartShape4; Geo=-; LOD=-1.0/-1.0 |
| `FXC_GodMhitemitC` | 228 | 2 - OmniEmitter | PS=GodMhitpartCShape; Geo=-; LOD=-1.0/-1.0 |
| `FXC_GodMhitFemit1` | 228 | 2 - OmniEmitter | PS=GodMhitFpart1Shape; Geo=-; LOD=-1.0/-1.0 |


## 14. Confirmed vs inferred

Confirmed by ELF symbols:

- Structure names and sizes.
- Field names and offsets.
- FX factory ids and client class names.
- Inheritance chain from `svrLoadParm` to concrete FX payloads.

Confirmed by provided samples:

- Little-endian encoding.
- `serverID = 0x19`.
- File size equals `fParmSize` for loose FXC samples.
- Type 1/2/3 emitters use the same 0xE4 emitter payload.
- Type 12 field uses the 0xB4 field payload.
- Type 13 geometry uses the 0x88 geometry payload.

Still not fully named:

- Individual bits of `fAttachmentFlags`.
- Individual bits of `fxFieldLoadParm.fFlags`.
- Individual bits of `fxGeometryLoadParm.fFlags`.
- Exact DCC matrix transpose convention without in-game orientation tests.
