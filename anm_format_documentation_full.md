
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

Funções chave:
- `ParseRotations(buf, stateIndex)`
- `ParsePositions(buf, stateIndex)`

---

# 📈 Renderização de Skinning Frame-a-Frame

## RenderSkinningInit
Inicia os valores default de cada osso.

## RenderedSkinningState
Contém os dados de todas as rotações e posições renderizadas.

## Funções
- `RenderSkinningData`
- `renderStream`

Fluxo:
- Renderiza todos os frames.
- Cada frame é interpolado ou aplicado de acordo com o tipo de substream.
- Usa `DataBitMap` para saber quais vetores são afetados.

---

# 📋 Detalhes Extras

## 🔥 Flags no Arquivo `.anm`

| Flag (bit) | Significado |
|:---|:---|
| `0x0001` | Provavelmente autoplay (inicia automaticamente) |
| `0x1000` | Contém animação de rotação de juntas |
| `0x2000` | Contém animação de posição de juntas |
| `0x4000` | Contém animação de escala de juntas |

## 🛠️ Detalhes sobre os DataTypes

| Tipo | Nome | Descrição |
|:---|:---|:---|
| `0` | Skinning | Animação de juntas |
| `3` | Material | Mudança de propriedades de materiais |
| `5` | Unknown5 | Ativação/desativação de objetos |
| `8` | UV Animation | Animação de UVs |
| `9` | Texture Sheet | Animação de sprites |
| `10` | Partículas | Movimento de partículas |
| `11` | Unknown11 | Associado a sons |
| `12` | Unknown12 | Física tipo bandeira/vento |

## 🔁 Interpolação Linear vs Aditiva

- **Raw**: valor direto lido.
- **Additive**: valor ajustado por shift e somado.

## 🧠 Compressão com DataBitMap

Bitmap de 16 bits para marcar vetores usados.

Exemplo:
```plaintext
0000 0000 0000 1111
```

## 🎮 Compatibilidade GoW1 vs GoW2

- Formato igual.
- Pequenas diferenças para suporte extra em GoW2 (ex: física de roupas).

## 🛠️ Exemplo de Parser

```go
func ParseAnm(data []byte) {
    magic := binary.LittleEndian.Uint32(data[0:])
    if magic != 3 {
        panic("Arquivo .anm inválido")
    }
    // Continua parsing...
}
```

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

O sistema `.anm` de God of War é altamente eficiente para armazenamento e blending dinâmico de animações.

---

# 💚 Autor
Baseado no repositório [god_of_war_browser](https://github.com/leonifc331/god_of_war_browser) + análise aprofundada.

---
