
# God of War - Formato `.anm` - Documentação Completa

## 🖊 Visão Geral

O formato `.anm` é um contêiner de animações usado em **God of War** (PS2), armazenando **grupos** de animações e seus **dados compactados** de movimentação de esqueletos, materiais, partículas e muito mais.

Cada arquivo `.anm` pode conter:
- Vários **grupos** (`AnimGroup`)
- Cada grupo com várias **ações** (`AnimAct`)
- Cada ação com vários **estados** (`AnimActStateDescr`)

O sistema também possui um **código de decodificação interna** para extrair e interpolar as animações.

---

# 📂 Estrutura do Arquivo `.anm`

| Offset | Tipo | Descrição |
|:---|:---|:---|
| 0x00 | `u32` | Magic (sempre 0x3) |
| 0x04 | `u32` | Desconhecido |
| 0x08 | `u32` | Flags de animação (posição/rotação/escala animada) |
| 0x0C | `u32` | Desconhecido |
| 0x10 | `u16` | Quantidade de `DataTypes` |
| 0x12 | `u16` | Quantidade de `AnimGroups` |
| 0x14 | [4 bytes] * N | Pointers para grupos |
| ... |   | `DataTypes` e `AnimGroups` |

---

# 📦 Componentes

## AnimDatatype
Define que tipo de dado é animado.

- `0`: Skinning (ossatura)
- `3`: Material (cor, luz)
- `8`: UV de textura
- `9`: Texture sheet animation
- `10`: Partículas

```go
struct AnimDatatype {
    uint16 TypeId
    uint8  Param1
    uint8  Param2
}
```

## AnimGroup
Agrupa várias ações de animação.

```go
struct AnimGroup {
    uint32 Offset
    string Name
    bool IsExternal
    []AnimAct Acts
}
```

## AnimAct
Representa uma ação de animação (ex: "Correr", "Atacar").

```go
struct AnimAct {
    uint32 Offset
    float32 UnkFloat0x4
    float32 UnkFloat0xc
    float32 Duration
    string Name
    []AnimActStateDescr StateDescrs
}
```

## AnimActStateDescr
Descritor de estados de um `Act`.

```go
struct AnimActStateDescr {
    uint16 Unk0
    uint16 CountOfSomething
    uint32 OffsetToData
    float32 FrameTime
    interface{} Data
}
```

---

# 🛰️ Sistema de Chunk e Interval

## Interval
Blocos de amostras de animação.

```go
struct Interval {
    uint16 Count
    uint16 Offset
    uint16 Unk4
    uint16 DataOffset
}
```

## Chunk
Controla o update de intervalo em função do tempo.

```go
struct Chunk {
    uint16 DataIndex
    uint8 Flags
    uint8 Offset_In64kb
    Interval Interval
}
```

## DataBitMap
Controla quais componentes de vetores (X, Y, Z, W) são animados.

```go
struct DataBitMap {
    uint8 WordsCount
    uint8 ElementsPerSample
    uint16 Offset
    []uint16 Bitmap
}
```

---

# 🛋️ Decodificação de Skinning: `AnimState0Skinning`

Representa o movimento de ossos.

| Campo | Descrição |
|:---|:---|
| `PositionDescr` | Header da posição |
| `RotationDescr` | Header da rotação |
| `PositionStream` | Stream principal de posição |
| `RotationStream` | Stream principal de rotação |
| `SubStreamsAdd` | Streams refinados (aditivos) |
| `SubStreamsRough` | Streams principais (crus) |

---

# 📈 Renderização de Skinning Frame-a-Frame

## RenderSkinningInit
Inicia os valores default de cada osso.

## RenderedSkinningState
Contém os dados de todas as rotações e posições renderizadas.

## Funções
- `RenderSkinningData`
- `renderStream`

---

# 📆 Diagrama de Fluxo

```plaintext
[.anm Container]
   └──> [AnimGroup]
         └──> [AnimAct]
               └──> [AnimState0Skinning]
                       └──> [SubStreams Add/Rough]
                               └──> [Frame Values] (Interpolados)
```

---

# 🔍 Conclusão

O sistema `.anm` é extremamente otimizado, permitindo blending de múltiplas fontes de animação com compactação eficiente.

---
