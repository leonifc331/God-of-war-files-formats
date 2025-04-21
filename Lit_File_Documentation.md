
# Documentação do Formato de Arquivo `.lit` - **God of War 1 e 2**

Esta documentação descreve o formato de arquivos `.lit` utilizados nos jogos **God of War 1** e **God of War 2** para armazenar dados de luzes (iluminação).

---

## Estrutura Geral
Cada arquivo `.lit` possui um tamanho fixo de:
- **88 bytes (0x58)** no **God of War 1**
- **88 bytes (0x58)** no **God of War 2**

> Obs: A diferença está apenas na interpretação de alguns campos finais, mas o tamanho é o mesmo.

---

## Layout dos Dados
| Offset | Tipo de dado         | Nome      | Descrição |
|:------:|:---------------------|:---------:|:-----------|
| 0x00   | Ignorado             | -         | (Contém outros dados internos, geralmente ignorado) |
| 0x04   | `uint32`             | Unk04     | Sempre 0 |
| 0x08   | `uint32`             | Flags     | Define o tipo de luz:<br>- `0`: Luz ambiente<br>- `1`: Luz pontual<br>- `2` ou `6`: Luz direcional |
| 0x0C   | `mgl32.Vec4`         | Position  | Vetor de posição XYZW |
| 0x1C   | `mgl32.Vec4`         | Rotation  | Vetor de rotação XYZW |
| 0x2C   | `mgl32.Vec4`         | Color     | Cor da luz (RGB) + Intensidade (W)<br>**Nota**: Valores podem ser negativos. |
| 0x3C   | `float32`            | Unk3c     | Finalidade desconhecida |
| 0x40   | `float32`            | Unk40     | Finalidade desconhecida |
| 0x44   | `float32`            | Unk44     | Finalidade desconhecida |
| 0x48   | `uint32`             | Unk48     | Sempre 0 ou 1 (tipo "ativo/desativado") |
| 0x4C   | `float32`            | Unk4c     | Finalidade desconhecida (sempre 0?) |
| 0x50   | `float32`            | Unk50     | Finalidade desconhecida (sempre 0?) |
| 0x54   | `float32` (GoW1 only) | Unk54    | Somente presente no **God of War 1** |

---

## Observações Importantes

- **Posição e Rotação:**
  - Para luzes ambientes (Flags == 0), posição e rotação geralmente **não são relevantes**.
  - Para luzes pontuais e direcionais (Flags == 1, 2 ou 6), ambas podem influenciar.

- **Cor:**
  - O vetor de cor (`mgl32.Vec4`) inclui os componentes R, G, B e um quarto componente que parece representar **intensidade** ou outro ajuste especial.
  - Componentes negativos são possíveis e aceitos no motor do jogo.

- **Unk48:**
  - É lido como `uint32` mas funciona como `bool`: valores possíveis são **0** (falso) ou **1** (verdadeiro).

- **Campos finais Unk4c, Unk50, Unk54:**
  - Normalmente **zerados**.
  - `Unk54` **só existe em God of War 1**. Em God of War 2, este campo é inexistente e não lido.

---

## Magic Number

Todos os arquivos `.lit` possuem o identificador ("magic number"):
- **`0x6`**

Esse magic é usado pelo sistema de arquivos `.wad` para identificar e carregar o handler correto.

---

## Uso prático no código

Ao ler o arquivo `.lit`:
- O sistema checa se está lidando com **God of War 1** ou **2** para saber se precisa interpretar o `Unk54`.
- A estrutura é lida sequencialmente a partir dos offsets.
- A biblioteca `mathgl` (`mgl32`) é usada para ler `Vec4` diretamente.

---

## Exemplo de Mapeamento Rápido

```go
light := &Light{}
err := light.FromWad(data, gow2)
if err != nil {
    log.Fatalf("Erro ao carregar luz: %v", err)
}
```

---

## Referência

- [God of War Browser (mogaika)](https://github.com/mogaika/god_of_war_browser)
- Análise direta de arquivos `.lit` de "LeftBladeLight-gow1" e "LeftBladeLight-gow2".

---

> **Nota final**: Para criar ou editar luzes manualmente, recomenda-se respeitar as estruturas de `float32` e `uint32` para garantir compatibilidade com o motor original do jogo.
