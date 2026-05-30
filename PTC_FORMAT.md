# GOW2 PTC File

## Origem da estrutura

Os arquivos `PTC_*` analisados são parametros de `renParticleSystemLoadParm`, herdando de `goAttachmentLoadParm`. O servidor usado e o ParticleServer, cujo ID interno e 17.

Amostras analisadas:

- `PTC_PWFfxpart5` ate `PTC_PWFfxpart13`
- `PTC_PWFfxpart17`, `18`, `20`, `21`, `23`, `24`, `26`, `27`, `28`

Todos estão em little-endian.

## Layout geral

| Offset | Tamanho | Campo | Tipo | Observação |
|---:|---:|---|---|---|
| 0x000 | 0x050 | `goAttachmentLoadParm` | struct | Base herdada |
| 0x050 | 0x004 | `fParmSize` | u32 | Tamanho total do arquivo/bloco |
| 0x054 | 0x018 | `fIdentifier` | char[24] | Nome ASCII interno |
| 0x06C | 0x004 | `fMaxParticles` | s32 | -1 nos samples |
| 0x070 | 0x004 | `fParticleLife` | f32 | Vida padrão/Intensidade |
| 0x074 | 0x004 | `fCullRadius` | f32 | Raio de culling |
| 0x078 | 0x004 | `fLODNear` | f32 | -1 = sem limite |
| 0x07C | 0x004 | `fLODFar` | f32 | -1 = sem limite |
| 0x080 | 0x004 | `fFlags` | u32 | Flags de particula |
| 0x084 | 0x004 | `fRendererDataSize` | s32 | Em quadwords de 16 bytes |
| 0x088 | 0x004 | `fNumImplicitParticles` | s32 | 0 nos samples |
| 0x08C | 0x00C | `renParticleFormat` | struct | Layout EE/VU por particula |
| 0x098 | 0x090 | `renParticleParmDataPacket` | struct | Renderer, offsets e descriptors |
| 0x128 | variavel | coefficient buffer | bytes/float32 | Bloco dinamico de coeficientes |
| variavel | variavel | renderer data | bytes | Tamanho = `fRendererDataSize * 0x10` |

## Base goAttachmentLoadParm

| Offset | Campo | Tipo | Observação |
|---:|---|---|---|
| 0x000 | `client_type` | u32 | Low16 = 17 para ParticleServer |
| 0x004 | `fGroupID` | u32 | Grupo/attachment |
| 0x008 | `fTransformID` | s16 | ID de transform/joint; varia nos samples |
| 0x00A | `fAnimationSetID` | s16 | Geralmente -1 |
| 0x00C | `fAttachmentFlags` | u32 | Flags de attachment |
| 0x010 | `fLocalMatrix` | 16 x f32 | Matriz local 4x4 |

## renParticleFormat

Tamanho: 12 bytes.

| Offset relativo | Campo | Tamanho | Descrição |
|---:|---|---:|---|
| 0x00 | `fOutputs[9]` | 9 bytes | Location compactado |
| 0x09 | `fPerParticleSize[2]` | 2 bytes | Tamanho por buffer EE/VU |
| 0x0B | `fFlags` | 1 byte | Bit 0 = VU double buffered |

Cada `Location` usa um byte:

```txt
bits 0..1 = buffer: 0 EE, 1 VU, 2 None
bits 2..7 = offset
```

A ordem dos outputs e:

```txt
0 Birthday
1 Position
2 Velocity
3 Mass
4 XAxis / OrientationX
5 YAxis / OrientationY
6 Normal / OrientationZ
7 Euler / OrientationE
8 Quaternion / OrientationQ
```

## renParticleParmDataPacket

Tamanho: 0x90 bytes.

| Offset relativo | Campo | Tipo | Descricao |
|---:|---|---|---|
| 0x00 | `fPad` | s32 | Padding |
| 0x04 | `fRenderer` | s32 | Renderer ID |
| 0x08 | `fRendererData` | s32 | Offset do renderer data relativo ao inicio do coefficient buffer |
| 0x0C | `fImplicitParticleSize` | s32 | Tamanho de particula implicita |
| 0x10 | `fInputCopyDescriptor_QWords` | 8 x s16 | Descriptor |
| 0x20 | `fInputCopyDescriptor_WComponents` | 8 x s16 | Descriptor |
| 0x30 | `fRandomDescriptor_QWords` | 8 x s16 | Descriptor |
| 0x40 | `fRandomDescriptor_WComponents` | 8 x s16 | Descriptor |
| 0x50 | `fOutputCopyDescriptor_QWords` | 8 x s16 | Descriptor |
| 0x60 | `fOutputCopyDescriptor_WComponents` | 8 x s16 | Descriptor |
| 0x70 | `fFunctionDescriptor_Subroutines` | 8 x s16 | IDs de funcoes |
| 0x80 | `fFunctionDescriptor_ArgumentOffsets` | 8 x s16 | Offsets de argumentos |

## Renderer IDs

| ID | Nome |
|---:|---|
| 0 | Point |
| 1 | Sprite2D |
| 2 | Sprite3D |
| 3 | Sprite3DR |
| 4 | GlowBall |
| 5 | BlurBall |
| 6 | MiniModel |
| 7 | Spark |
| 8 | Billboard |
| 9 | Decal |
| 10 | Discharge |
| 11 | Tristrip1 |
| 12 | Tristrip2 |
| 13 | Tristrip3 |

## Function IDs

| ID | Nome |
|---:|---|
| 0 | Constant |
| 1 | ConstantBlur |
| 2 | Linear |
| 3 | LinearColour |
| 4 | Quadratic |
| 5 | QuadraticColour |
| 6 | TableLookupDiscreteX |
| 7 | TableLookupInterpolatedX |
| 8 | Drag |
| 11 | LinearBlur |
| 12 | QuadraticBlur |
| 15 | FlipBookGetUVs |
| 16 | ConstantMod |
| 17 | LinearMod |
| 18 | QuadraticMod |
| 19 | SparkEnds |

## Particle flags

| Mascara | Nome |
|---:|---|
| 0x00000003 | BlendModeMask |
| 0x0000000C | TextureFunctionMask |
| 0x00000010 | TrackOrientation |
| 0x00000020 | TrackPosition |
| 0x00000040 | ScreenAsTexture |
| 0x00000080 | MaterialIsSprite |
| 0x00000100 | FixedPointViewport |
| 0x00000200 | AntiAliasLines |
| 0x00000400 | UseSpriteAlpha |
| 0x00001000 | LiveForever |
| 0x00002000 | ForceIntoPreBlurLayer |
| 0x00004000 | ForceIntoBlurLayer |
| 0x00008000 | ShareBuffers |
| 0x00010000 | EmitterInheritsVelocity |
| 0x00020000 | WriteZ |
| 0x00040000 | ForceIntoSub2Layer |
| 0x00080000 | ForceIntoPostWorldLayer |
| 0x00100000 | AutoFillOnConnect |
