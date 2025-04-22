# Documentação do Formato de Arquivo `.ncv` (God of War - Rail Data)

## Visão Geral
Arquivos `.ncv` representam **dados de trilhos/movimentação (Rail)** utilizados em *God of War* para armazenar **matrizes de transformação** e **valores flutuantes**. Esses dados podem ser usados para caminhos de câmeras, animações de objetos ou movimentações controladas.

O arquivo é carregado através da estrutura `Rail`, definida no pacote `cam`.

---

## Estrutura de Dados
```go
type Rail struct {
    Matrices []mgl32.Mat4 // Lista de Matrizes 4x4
    Floats   []float32    // Lista de valores flutuantes (float32)
}
```

Cada arquivo `.ncv` contém:
- Um número `count` indicando quantos conjuntos de dados existem.
- `count` matrizes 4x4 (`mgl32.Mat4`).
- `count` valores de ponto flutuante (`float32`).

---

## Layout do Arquivo `.ncv`

| Offset | Tamanho | Descrição                                   |
|:------:|:-------:|:-------------------------------------------:|
| 0x00   | 4 bytes | `count` - Quantidade de matrizes/valores    |
| 0x04   | 4 bytes | `unk04` - Deve ser 0 (validação de segurança) |
| 0x08   | 4 bytes | `unk08` - Deve ser `0xFFFFFFFF` (validação) |
| 0x0C   | 4 bytes | `unk0c` - Deve ser `0xFFFFFFFF` (validação) |
| 0x10   | `count * 0x40` bytes | Matrizes 4x4 (cada matriz ocupa 64 bytes) |
| Após matrizes | `count * 4` bytes | Valores `float32` adicionais |

---

## Detalhamento dos Dados

- Cada **matriz** é armazenada de forma sequencial e usa o formato **Little Endian**.
  - Cada matriz 4x4 ocupa exatamente **64 bytes** (16 floats de 4 bytes).

- Depois das matrizes, vem **uma lista de floats** (um `float32` para cada matriz).

---

## Processo de Leitura (`FromData`)

1. **Ler `count`:**
   - Lê os primeiros 4 bytes (`uint32`) como número de matrizes/floats.

2. **Validar valores de segurança:**
   - `unk04` deve ser **0**.
   - `unk08` e `unk0c` devem ser **0xFFFFFFFF** (`4294967295`).

3. **Ler matrizes:**
   - A partir de `0x10`, ler `count` matrizes de 64 bytes cada.

4. **Ler floats:**
   - Após as matrizes, ler `count` valores `float32`.

---

## Processo de Salvamento (`Marshal`)

- A função `Marshal` retorna diretamente a estrutura `Rail` para o sistema de recursos (`WadNodeRsrc`).
- Não há transformação ativa, apenas disponibilização dos dados.

---

## Integração com o Sistema WAD

No `init()`, o tipo de arquivo `.ncv` é registrado como um **handler do Tag ID 112**:

```go
wad.SetTagHandler(112, func(rsrc *wad.WadNodeRsrc) (wad.File, error) {
    r := &Rail{}
    err := r.FromData(rsrc.Tag.Data)
    return r, err
})
```

Ou seja:
- Quando um nó com **Tag ID 112** é encontrado, o sistema carrega o conteúdo como um `Rail`.
- `Tag.Data` é passado para `FromData` para interpretar e armazenar o conteúdo.

---

## Resumo Visual

```plaintext
.ncv Arquivo
├── count (uint32)
├── unk04 (uint32) [deve ser 0]
├── unk08 (uint32) [deve ser 0xFFFFFFFF]
├── unk0c (uint32) [deve ser 0xFFFFFFFF]
├── Matrizes [count x 64 bytes]
└── Floats   [count x 4 bytes]
```

---

## Notas Técnicas

- Matrizes organizadas em **ordem linear** (sem padding).
- Usado para movimentação de câmeras e objetos.
- Validação rígida para evitar corrupção de dados.
- Dependência da biblioteca `mathgl` (`mgl32`) para lidar com matrizes 4x4.
