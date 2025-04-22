
# Documentação de Arquivos MAT

Este documento detalha a estrutura, funcionalidades e uso dos arquivos **.mat** do projeto `god_of_war_browser`, responsáveis por representar materiais ("Materials") no motor do jogo **God of War** (PS2/PS3).

## Sumário
- [Introdução](#introdução)
- [Estrutura do Arquivo MAT](#estrutura-do-arquivo-mat)
- [Componentes Principais](#componentes-principais)
  - [Flags](#flags)
  - [Layer](#layer)
  - [Material](#material)
- [Processo de Leitura (`NewFromData`)](#processo-de-leitura-newfromdata)
- [Processo de Serialização (`Marshal`)](#processo-de-serialização-marshal)
- [Integração com o Sistema WAD](#integração-com-o-sistema-wad)
- [Referências e Recursos Externos](#referências-e-recursos-externos)

---

## Introdução

Arquivos MAT são usados para descrever materiais aplicados a superfícies em modelos 3D do jogo. Cada material pode conter multiplas camadas de textura (Layers), combinações de blend, cores e propriedades de renderização específicas.

Esses arquivos são carregados e interpretados dinamicamente para compor a aparência visual de objetos durante o jogo.

---

## Estrutura do Arquivo MAT

O formato é estruturado como:

| Offset | Tamanho | Descrição                                |
|--------|---------|--------------------------------------------|
| 0x00   | 4 bytes | Magic Number (deve ser `0x00000008`)        |
| 0x08   | 12 bytes| Cor base do material (RGB)                 |
| 0x34   | 4 bytes | Quantidade de Layers                      |
| 0x38+  | 64 bytes por Layer | Definição das camadas individuais |

- Cada **Layer** possui flags de renderização, nome da textura, blend color, etc.

---

## Componentes Principais

### Flags

`Flags` define o comportamento visual e funcional do material.

| Flag | Significado |
|------|-------------|
| `FilterLinear` | Se verdadeiro, usa filtro linear. Se falso, usa filtro "nearest". |
| `DisableDepthWrite` | Desativa escrita no buffer de profundidade. |
| `RenderingAdditive`, `RenderingSubstract`, `RenderingUsual`, `RenderingStrangeBlended` | Modos de blend. Um apenas deve estar ativo. |
| `HaveTexture` | Indica se a Layer possui uma textura associada. |
| `AnimationUVEnabled` | Habilita animação UV (movimento de textura). |
| `AnimationColorEnabled` | Habilita animação de cor do material. |

### Layer

Cada `Layer` representa uma camada de textura e efeitos associados:

- `Texture`: Nome da textura associada
- `Flags`: Conjunto de 4 inteiros para diversas configurações
- `BlendColor`: Cor de mistura para combinação com a textura
- `FloatUnk`: Valor desconhecido (geralmente 1.0)
- `GameFlags`: Flags adicionais para animação
- `ParsedFlags`: Flags interpretadas (estruturadas)

### Material

Representa o conjunto geral:

- `Color`: Cor base do material
- `Layers`: Lista de camadas de textura (`[]Layer`)

---

## Processo de Leitura (`NewFromData`)

Função:
```go
func NewFromData(buf []byte) (*Material, error)
```

Processo:
1. Verifica o magic number (`0x00000008`).
2. Lê a cor base.
3. Identifica o número de Layers.
4. Para cada Layer:
   - Lê flags, textura, blend color, floatUnk e game flags.
   - Executa `ParseFlags` para interpretar.

Caso ocorra erro de parsing, a leitura é abortada.

---

## Processo de Serialização (`Marshal`)

Função:
```go
func (mat *Material) Marshal(wrsrc *wad.WadNodeRsrc) (interface{}, error)
```

Processo:
1. Cria estrutura `Ajax` contendo:
   - Material bruto
   - Texturas
   - Texturas com blending
   - Animações (se existirem)
2. Para cada Layer:
   - Tenta localizar a textura associada no WAD.
   - Serializa textura e textura com blend.
3. Para sub-nós:
   - Se encontrar animações (ANM), também serializa.

**Obs:** Em caso de erros de carregamento, o erro é propagado.

---

## Integração com o Sistema WAD

O `init()` registra o handler para arquivos `.mat`:

```go
func init() {
	wad.SetHandler(config.GOW1, MAT_MAGIC, handler)
	wad.SetHandler(config.GOW2, MAT_MAGIC, handler)
}
```

Assim, ao encontrar arquivos MAT no WAD dos jogos, o sistema sabe automaticamente como interpretá-los.

---

## Referências e Recursos Externos

- [Research sobre materiais no Maya](https://nccastaff.bournemouth.ac.uk/jmacey/RobTheBloke/www/research/maya/mfnmaterial.htm)
- [Research sobre mapeamento ambiental no Maya](https://nccastaff.bournemouth.ac.uk/jmacey/RobTheBloke/www/research/maya/mfnenvmap.htm)

Essas referências ajudam a entender conceitos como animação UV e blending de materiais usados.

---

## Conclusão

O sistema de arquivos MAT é responsável por configurar e definir a aparência visual dos objetos 3D do jogo, manipulando texturas, cores e modos de blend de forma eficiente e flexível.

A implementação permite integração direta com texturas (TXR) e animações (ANM) dentro do motor de extração `god_of_war_browser`.
