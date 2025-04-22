
# Documentação do Arquivo `GeomShape`

## Visão Geral
O arquivo `GeomShape` define uma estrutura de malha geométrica utilizada para representar formas tridimensionais simples no projeto God of War Browser. Este arquivo é interpretado a partir de dados binários e é composto principalmente por vértices e índices, cada um contendo informações de posição e normais.

## Constantes
- `MESH_TAG = 112`
  - Identifica o tipo de tag relacionado a malhas dentro do sistema de arquivos WAD.

## Estruturas de Dados

### `GeomShapeVertex`
Representa um vértice de uma forma geométrica.

- **Pos**: `[3]float32`
  - Coordenadas de posição (X, Y, Z).
- **Norm**: `[3]float32`
  - Vetor normal (X, Y, Z), usado para iluminação e shading.

### `GeomShapeIndex`
Representa um conjunto de índices formando um triângulo.

- **Indexes**: `[3]uint16`
  - Índices dos três vértices que compõem o triângulo.
- **Flags**: `uint16`
  - Informações adicionais ou propriedades relacionadas ao triângulo.

### `GeomShape`
Contêiner principal que agrupa vértices e índices.

- **Vertexes**: `[]GeomShapeVertex`
  - Lista de vértices.
- **Indexes**: `[]GeomShapeIndex`
  - Lista de triângulos (cada um definido por três vértices).

## Funções

### `NewGeomShapeFromData(r io.Reader) (*GeomShape, error)`
Cria uma instância de `GeomShape` a partir de um fluxo de dados binário.

#### Processo:
1. Lê 16 bytes iniciais:
   - 4 bytes: quantidade de vértices.
   - 4 bytes: quantidade de índices.
2. Para cada vértice:
   - Lê 24 bytes: 12 para posição (3 floats) + 12 para normal (3 floats).
3. Para cada conjunto de índices:
   - Lê 8 bytes: 6 para os índices (3x uint16) + 2 para flags.

### `Marshal(wrsrc *wad.WadNodeRsrc) (interface{}, error)`
Função de "serialização" que simplesmente retorna a estrutura `GeomShape` como está.

### `init()`
Inicializador para registro do manipulador de tags no sistema WAD.
- Atualmente comentado.
- Se habilitado, permitiria associar `MESH_TAG` à função `NewGeomShapeFromData`.

## Observações
- A estrutura espera leitura sequencial correta dos dados binários.
- Pequenas corrupções no arquivo podem causar erros na interpretação dos dados.
- O sistema é altamente dependente da ordem e formato Little Endian.

## Formato Binário Resumido

| Offset  | Tamanho | Tipo          | Descrição                      |
|---------|---------|---------------|----------------------------------|
| 0x00    | 4 bytes | uint32         | Número de vértices              |
| 0x04    | 4 bytes | uint32         | Número de triângulos            |
| 0x08    | N        | Vértices       | Cada um com 24 bytes (Pos+Norm)  |
| (após) | N        | Triângulos    | Cada um com 8 bytes (3 índices + flags) |


## Exemplo de Pseudo-Código de Leitura
```pseudo
ler num_vertexes
ler num_indexes
para i de 0 até num_vertexes:
    ler pos_x, pos_y, pos_z
    ler norm_x, norm_y, norm_z
para i de 0 até num_indexes:
    ler index1, index2, index3, flags
```

## Dependências
- `encoding/binary`: Para conversão de dados binários.
- `io`: Para leitura de streams de dados.
- `math`: Para conversão de bits em float32.
- `github.com/mogaika/god_of_war_browser/pack/wad`: Para integração com o sistema de arquivos WAD.
