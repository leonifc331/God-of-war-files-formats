
# Documentação do Formato `ballhull`

## Visão Geral

O arquivo `ballhull` é uma estrutura usada no mecanismo do jogo *God of War* para representar colisões através de "bolas" e "malhas". Ele é utilizado principalmente em objetos 3D para delimitar áreas de interação física.

## Estrutura do Arquivo

O arquivo é composto por um cabeçalho fixo seguido de diversas seções de dados.

### Cabeçalho (Tamanho: 0x68 bytes)

| Offset | Tamanho | Tipo | Descrição |
|:------:|:-------:|:----:|:---------|
| 0x04 | 4 bytes | uint32 | Tipo da forma: 0=Objeto, 1=Câmera, 2=Sensor, 3=Emissor de Som (4=Ribsheet, só interno no engine) |
| 0x10 | 4 bytes | uint32 | Tamanho total do arquivo |
| 0x14 | 4 bytes | uint32 | Quantidade de "Bolas" |
| 0x18 | 4 bytes | uint32 | Quantidade de "Malhas" |
| 0x1C | 16 bytes | Vec4 | Bounding Sphere do objeto (centro x, y, z + raio) |
| 0x2C | 4 bytes | uint32 | Índice de Joint associado à Bounding Sphere |
| 0x34 | 4 bytes | uint32 | Quantidade de Materiais |
| 0x38 | 4 bytes | uint32 | Tamanho total dos dados de Materiais |
| 0x3C - 0x68 | 44 bytes | [11]uint32 | Offsets para as seções do arquivo |

### Seções
Cada seção contém dados relacionados às "Bolas" ou "Malhas":

| ID | Nome | Conteúdo |
|:--:|:----|:---------|
| 0 | `MATERIAL` | Lista de materiais |
| 1 | `BALLS_JOINTS` | Joint associada a cada bola |
| 2 | `BALLS_SCRIPTMARK` | Script Mark de cada bola |
| 3 | `BALLS_MAPMATERIAL` | Material associado a cada bola |
| 4 | `BALLS_COORDS` | Coordenadas (Vec4) de cada bola |
| 5 | `MESHES_PLANESCOUNT` | Número de planos de cada malha |
| 6 | `MESHES_JOINTS` | Joint associada a cada malha |
| 7 | `MESHES_SCRIPTMARK` | Script Mark de cada malha |
| 8 | `MESHES_BBOXES` | Bounding box (Vec4) de cada malha |
| 9 | `MESHES_MAPMATERIAL` | Material associado a cada plano da malha |
| 10 | `MESHES_PLANES` | Vetores dos planos das malhas (Hesse normal form)

## Estruturas de Dados

### BallHullBall

Representa uma "Bola" de colisão:

```go
struct BallHullBall {
    Coord      Vec4 // Coordenada e raio
    Joint      byte // Índice da articulação associada
    ScriptMark byte // Marca para scripts
    Material   byte // Índice do material
}
```

### BallHullMesh

Representa uma "Malha" de colisão:

```go
struct BallHullMesh {
    BBox       Vec4      // Caixa delimitadora
    Planes     []Vec4    // Planos definidos em forma normal de Hesse
    Materials  []int8    // Materiais para cada plano
    Joint      byte      // Índice da articulação associada
    ScriptMark byte      // Marca para scripts
}
```

### ShapeBallHull

Objeto principal que representa o arquivo como um todo:

```go
struct ShapeBallHull {
    Type         uint32
    FileSize     uint32
    BallsCount   uint32
    MeshesCount  uint32
    BSphere      Vec4
    BSphereJoint uint32
    MaterialsCount uint32
    MaterialSize   uint32
    Offsets      [11]uint32

    Balls  []*BallHullBall
    Meshes []*BallHullMesh

    DbgMesh *ShapeDbgHdr // apenas para exportação
}
```

## Funções Importantes

- **NewBallHull**: Função principal para carregar um arquivo `ballhull` de um `BufStack`.
- **readVec4**: Auxiliar para ler vetores de quatro floats (Vec4).
- **GetSectionSize**: Calcula o tamanho de uma seção baseado nos offsets.

## Observações

- Todas as leituras são feitas em **little endian**.
- As seções de dados podem ser expandidas na última posição (offset final).
- "Joint" e "ScriptMark" indicam ligações entre colisões e sistemas de animações/scripts.
- Planos são armazenados em formato normalizado para representar colisão de superfícies.

---

**Autor:** Projeto de documentação baseado em engenharia reversa de *God of War Browser*

**Status:** Completo para parsing básico de arquivos `ballhull`.
