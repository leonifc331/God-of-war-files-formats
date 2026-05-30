# God of War II PS2 — Documentação do formato FXC

**Versão:** 1.1 PT-BR  
**Alvo analisado:** `SCUS_974.67` / God of War II PS2, ELF com símbolos  
**Endian:** little-endian  
**Escopo:** recursos soltos `FXC_*` e blocos equivalentes de FX dentro de WAD/IFF  

---

## 1. O que é um arquivo FXC

`FXC` é um recurso de **cliente de efeito visual** do motor de God of War II. Ele não representa obrigatoriamente partículas completas sozinho. Em geral, o FXC descreve **como um efeito deve ser instanciado**, onde ele será preso, qual emissor será usado, qual campo de força atuará, ou qual geometria será registrada para ser usada por outro emissor.

A parte visual final normalmente depende da relação entre vários recursos:

| Recurso | Função no efeito |
|---|---|
| `FXC_*` | Define o cliente de efeito: emissor, campo, geometria, trail etc. |
| `PTC_*` | Define o sistema de partículas renderizável: sprite, vida, tamanho, cor, renderer e coeficientes. |
| `MSH_*` / geometria | Pode servir como superfície de emissão para `PolySurfaceEmitter`. |
| `ANM` / script / GO | Pode disparar, anexar, ativar ou desligar o efeito em runtime. |

Exemplo prático:

```text
FXC_BDEsparkemit0
  tipo: PolySurfaceEmitter
  partícula usada: flame3Shape
  geometria usada: BDepoly3Shape

FXC_BDepoly3
  registra a geometria MSH_BDepoly3Shape com o identificador BDepoly3Shape
```

Nesse caso, o emissor `FXC_BDEsparkemit0` usa o identificador registrado por `FXC_BDepoly3`.

---

## 2. Estrutura geral do arquivo

Todos os samples analisados seguem a mesma base inicial de `0x54` bytes:

```text
0x00  svrLoadParm              0x04 bytes
0x04  goAttachmentLoadParm     até 0x50
0x50  fxLoadParm               0x04 bytes
0x54  payload específico       depende do tipo FXC
```

Diagrama:

```text
FXC
├─ 0x00 serverID / clientID
├─ 0x04 dados de anexo ao GameObject
├─ 0x10 matriz local 4x4
├─ 0x50 tamanho total do parâmetro
└─ 0x54 dados específicos do efeito
   ├─ emissor de partículas
   ├─ trail
   ├─ field/campo de força
   └─ geometry/geometria auxiliar
```

O campo `clientID`, no offset `0x02`, define qual layout aparece depois de `0x54`.

---

## 3. Identificação do arquivo

| Offset | Tipo | Nome | Descrição |
|---:|---|---|---|
| `0x00` | `u16` | `serverID` | Identifica o servidor do motor. Nos FXC analisados é sempre `0x0019`, decimal `25`, servidor de FX. |
| `0x02` | `u16` | `clientID` | Tipo concreto do efeito. Define o payload: emissor, campo, geometria, trail etc. |

Exemplos reais:

| Bytes iniciais | Interpretação |
|---|---|
| `19 00 02 00` | Servidor FX `25`, cliente `2`, `OmniEmitter`. |
| `19 00 03 00` | Servidor FX `25`, cliente `3`, `PolySurfaceEmitter`. |
| `19 00 0C 00` | Servidor FX `25`, cliente `12`, `Field`. |
| `19 00 0D 00` | Servidor FX `25`, cliente `13`, `Geometry`. |

---

## 4. Tipos FXC conhecidos

Os símbolos do ELF confirmam a tabela de factories/clientes abaixo:

| ID | Tipo interno | Payload usado | Tamanho observado/esperado |
|---:|---|---|---:|
| `0` | `fxParticleEmitterClient` | Emissor de partículas | `0xE4` |
| `1` | `fxDirectionalEmitter` | Emissor de partículas | `0xE4` |
| `2` | `fxOmniEmitter` | Emissor de partículas | `0xE4` |
| `3` | `fxPolySurfaceEmitter` | Emissor de partículas | `0xE4` |
| `4` | `fxCurveEmitter` | Emissor de partículas | `0xE4` |
| `5` | `fxCubeEmitter` | Emissor de partículas | `0xE4` |
| `6` | `fxSphereEmitter` | Emissor de partículas | `0xE4` |
| `7` | `fxCylinderEmitter` | Emissor de partículas | `0xE4` |
| `8` | `fxConeEmitter` | Emissor de partículas | `0xE4` |
| `9` | `fxTorusEmitter` | Emissor de partículas | `0xE4` |
| `10` | `fxSkeletonEmitter` | Emissor de partículas | `0xE4` |
| `11` | `fxTrail` | Trail | `0x70` |
| `12` | `fxField` | Campo de força | `0xB4` |
| `13` | `fxGeometry` | Geometria auxiliar | `0x88` |


---

## 5. Cabeçalho comum de anexo — `0x00` até `0x4F`

Este bloco conecta o efeito ao objeto dono, a uma transformação, a um set de animação ou a uma matriz local.

| Offset | Tipo | Nome | Descrição |
|---:|---|---|---|
| `0x00` | `u16` | `serverID` | Servidor. Para FXC, `0x0019`. |
| `0x02` | `u16` | `clientID` | Tipo de cliente FX. |
| `0x04` | `u32` | `groupID` | ID/grupo lógico do efeito. Nos samples está `0`. |
| `0x08` | `s16` | `transformID` | Transform/joint/slot usado para anexar o efeito. |
| `0x0A` | `s16` | `animationSetID` | ID de set de animação. `-1`/`0xFFFF` costuma indicar nenhum. |
| `0x0C` | `u32` | `attachmentFlags` | Flags de anexo. Nos samples enviados está `0`. Bits individuais ainda não nomeados. |
| `0x10` | `float[16]` | `localMatrix` | Matriz local 4x4 do efeito. Normalmente identidade. |

### Matriz local

A matriz identidade aparece como:

```text
1 0 0 0
0 1 0 0
0 0 1 0
0 0 0 1
```


---

## 6. Tamanho total — offset `0x50`

| Offset | Tipo | Nome | Descrição |
|---:|---|---|---|
| `0x50` | `u32` | `parmSize` | Tamanho total do recurso FXC em bytes. Nos arquivos soltos analisados, bate exatamente com o tamanho do arquivo. |

Valores observados:

| Tipo | Valor |
|---|---:|
| Emissores `0..10` | `0xE4` / `228` bytes |
| Trail `11` | `0x70` / `112` bytes, confirmado por símbolos; sem sample nesta leva |
| Field `12` | `0xB4` / `180` bytes |
| Geometry `13` | `0x88` / `136` bytes |

---

## 7. Emissores de partículas — tipos `0..10`

Os tipos `0` até `10` compartilham o mesmo layout de payload. O que muda é a classe runtime que interpreta esses mesmos parâmetros.

### Layout geral

| Offset | Tipo | Nome | Descrição |
|---:|---|---|---|
| `0x54` | `char[24]` | `particleSystemIdentifier` | Nome do sistema de partículas usado. Normalmente aponta para um `PTC_*`/shape, por exemplo `flame3Shape`. |
| `0x6C` | `char[24]` | `geometryIdentifier` | Identificador de geometria usado por emissores de superfície. Vazio em emissores omni comuns. |
| `0x84` | bloco `0x58` | `emitterAttributes` | Atributos numéricos do emissor. |
| `0xDC` | `float` | `lodNear` | Distância de LOD próximo. Nos samples aparece `-1.0`, indicando padrão/desativado. |
| `0xE0` | `float` | `lodFar` | Distância de LOD distante. Nos samples aparece `-1.0`, indicando padrão/desativado. |

### Atributos do emissor — bloco `0x84..0xDB`

| Offset | Tipo | Nome | Função prática |
|---:|---|---|---|
| `0x84` | `vec3` | `direction` | Direção principal da emissão. |
| `0x90` | `float` | `spread` | Abertura/dispersão da emissão. |
| `0x94` | `float` | `speed` | Velocidade base das partículas. |
| `0x98` | `float` | `speedRandom` | Variação aleatória de velocidade. |
| `0x9C` | `float` | `minDistance` | Distância mínima de emissão/influência. |
| `0xA0` | `float` | `maxDistance` | Distância máxima de emissão/influência. |
| `0xA4` | `float` | `rate` | Taxa de emissão. Valores altos geram mais partículas. |
| `0xA8` | `float` | `tangentSpeed` | Velocidade tangencial. |
| `0xAC` | `float` | `normalSpeed` | Velocidade no eixo normal da superfície/volume. |
| `0xB0` | `vec3` | `volumeOffset` | Offset do volume de emissão. |
| `0xBC` | `float` | `volumeSweep` | Ângulo/varredura do volume. `6.283185` equivale aproximadamente a 360°. |
| `0xC0` | `float` | `sectionRadius` | Raio da seção/volume. |
| `0xC4` | `float` | `speedAwayCenter` | Velocidade afastando do centro. |
| `0xC8` | `float` | `speedAwayAxis` | Velocidade afastando do eixo. |
| `0xCC` | `float` | `speedAlong` | Velocidade ao longo do eixo. |
| `0xD0` | `float` | `speedAround` | Movimento orbital/espiral ao redor do eixo. |
| `0xD4` | `float` | `randomDirection` | Intensidade de direção aleatória. |
| `0xD8` | `float` | `directionalSpeed` | Força/velocidade adicional na direção principal. |

### Interpretação por tipo de emissor

| ID | Tipo | Interpretação provável |
|---:|---|---|
| `0` | Generic ParticleEmitter | Emissor base de partículas. |
| `1` | DirectionalEmitter | Usa fortemente `direction` e `directionalSpeed`. |
| `2` | OmniEmitter | Emissão omnidirecional. Muito usado para fogo, brilho, impacto e aura. |
| `3` | PolySurfaceEmitter | Emite a partir de uma geometria registrada. Usa `geometryIdentifier`. |
| `4` | CurveEmitter | Emissão a partir de curva. |
| `5` | CubeEmitter | Emissão por volume cúbico. |
| `6` | SphereEmitter | Emissão por volume esférico. |
| `7` | CylinderEmitter | Emissão por volume cilíndrico. |
| `8` | ConeEmitter | Emissão em cone. |
| `9` | TorusEmitter | Emissão em toro/anel. |
| `10` | SkeletonEmitter | Emissão a partir de esqueleto/joints. |

---

## 8. Field — tipo `12`

`Field` representa um campo que altera o movimento das partículas ou aplica força: gravidade, vento, atração, turbulência, swirl, empurrão radial etc.

### Layout geral

| Offset | Tipo | Nome | Descrição |
|---:|---|---|---|
| `0x54` | `u32` | `fieldFlags` | Flags do campo. Nos samples de gravidade aparece `0x00000003`. Bits individuais ainda não totalmente nomeados. |
| `0x58` | bloco `0x5C` | `fieldAttributes` | Parâmetros numéricos do campo. |

### Atributos do campo — bloco `0x58..0xB3`

| Offset | Tipo | Nome | Função prática |
|---:|---|---|---|
| `0x58` | `float` | `magnitude` | Força do campo. Pode ser negativa, como em gravidade. |
| `0x5C` | `float` | `attenuation` | Atenuação/falloff. |
| `0x60` | `vec3` | `directionAxis` | Eixo/direção principal do campo. |
| `0x6C` | `vec3` | `volumeOffset` | Offset do volume de influência. |
| `0x78` | `float` | `volumeSweep` | Ângulo de varredura. `6.283185` ≈ 360°. |
| `0x7C` | `float` | `sectionRadius` | Raio da seção/volume. |
| `0x80` | `float` | `minDistance` | Distância mínima de atuação. |
| `0x84` | `float` | `maxDistance` | Distância máxima de atuação. `-1.0` costuma indicar ilimitado/padrão. |
| `0x88` | `float` | `speed` | Velocidade do campo ou fator de movimento. |
| `0x8C` | `float` | `inheritVelocity` | Quanto o campo herda velocidade do objeto pai. |
| `0x90` | `float` | `radialType` | Seleção de modo radial armazenada como float. |
| `0x94` | `float` | `spread` | Espalhamento/randomização. |
| `0x98` | `float` | `frequency` | Frequência de onda/ruído. |
| `0x9C` | `float` | `noiseLevel` | Intensidade do ruído. |
| `0xA0` | `float` | `noiseRatio` | Mistura/proporção de ruído. |
| `0xA4` | `float` | `awayFromCentreAxis` | Força afastando do eixo central. |
| `0xA8` | `float` | `alongAxis` | Força ao longo do eixo. |
| `0xAC` | `float` | `aroundAxis` | Força circular/orbital ao redor do eixo. |
| `0xB0` | `float` | `directionalSpeed` | Força direcional adicional. |

### Exemplo real: `FXC_gravityField5`

| Campo | Valor |
|---|---:|
| Tipo | `12` / `Field` |
| Tamanho | `0xB4` |
| Flags | `0x00000003` |
| `magnitude` | `-20.0` |
| `directionAxis` | `(0.0, 1.0, 0.0)` |
| `volumeSweep` | `6.283185` |
| `sectionRadius` | `0.5` |
| `maxDistance` | `-1.0` |

Interpretação prática: campo de gravidade/força vertical aplicado às partículas.

---

## 9. Geometry — tipo `13`

`Geometry` registra uma geometria com um identificador curto para que outro FXC, principalmente `PolySurfaceEmitter`, possa usá-la como superfície de emissão.

### Layout geral

| Offset | Tipo | Nome | Descrição |
|---:|---|---|---|
| `0x54` | `u32` | `geometryFlags` | Flags da geometria. Valor observado: `0x00000001`. |
| `0x58` | `char[24]` | `geometryName` | Nome do recurso de mesh/modelo, normalmente começando com `MSH_`. |
| `0x70` | `char[24]` | `identifier` | Nome curto usado por emissores para referenciar esta geometria. |

### Exemplos

| Arquivo | `geometryName` | `identifier` |
|---|---|---|
| `FXC_BDepoly3` | `MSH_BDepoly3Shape` | `BDepoly3Shape` |
| `FXC_BDepoly6` | `MSH_BDepoly6Shape` | `BDepoly6Shape` |

Relação:

```text
FXC_BDepoly3
  identifier = BDepoly3Shape

FXC_BDEsparkemit0
  tipo = PolySurfaceEmitter
  geometryIdentifier = BDepoly3Shape
```

---

## 10. Trail — tipo `11`

| Offset | Tipo | Nome | Descrição |
|---:|---|---|---|
| `0x54` | `char[24]` | `curveName` | Nome da curva usada como base do trail. |
| `0x6C` | `float` | `trailLength` | Comprimento/duração visual do rastro. |

Tamanho esperado: `0x70` bytes.

---

## 11. Strings fixas de 24 bytes

Os nomes internos do FXC usam buffers ASCII fixos de `24` bytes.

Regras:

| Regra | Detalhe |
|---|---|
| Tamanho do buffer | `24` bytes. |
| Texto visível seguro | até `23` caracteres com terminador `00`. |
| Preenchimento | bytes após o terminador devem permanecer `00`. |
| Encoding | ASCII simples nos samples. |
| Referências cruzadas | se um identificador é usado por outro FXC, os dois precisam continuar batendo. |

Exemplos:

```text
flame3Shape
BDepoly3Shape
MSH_BDepoly3Shape
GodMhitFpart1Shape
```

---

## 12. Relação FXC ↔ PTC

Nos emissores de partículas, `particleSystemIdentifier` aponta para o sistema de partículas usado pelo efeito. Na prática, esse nome costuma corresponder a um recurso `PTC`/shape.

Exemplos:

| FXC | `particleSystemIdentifier` | Função provável |
|---|---|---|
| `FXC_BDEsparkemit0` | `flame3Shape` | Chama uma partícula de fogo/faísca. |
| `FXC_BDEsparkemit` | `flame6Shape` | Chama outra variação de fogo/faísca. |
| `FXC_flashemit4` | `FlashpartShape4` | Chama partícula de flash/brilho. |
| `FXC_GodMhitemitC` | `GodMhitpartCShape` | Partícula de impacto mágico/ataque. |
| `FXC_GodMhitFemit1` | `GodMhitFpart1Shape` | Variação de impacto mágico/ataque. |

O FXC controla **onde e como emitir**. O PTC controla **como a partícula se parece e se comporta visualmente**: vida, tamanho, cor, renderer, sprite, coeficientes etc.

---

## 13. Relação Geometry ↔ PolySurfaceEmitter

O tipo `Geometry` funciona como um registro de geometria. O tipo `PolySurfaceEmitter` usa esse registro para emitir partículas a partir de uma superfície.

Fluxo:

```text
1. FXC_BDepoly3 registra:
   geometryName = MSH_BDepoly3Shape
   identifier   = BDepoly3Shape

2. FXC_BDEsparkemit0 usa:
   geometryIdentifier = BDepoly3Shape

3. O engine resolve BDepoly3Shape e emite partículas sobre/ao redor dessa geometria.
```

Isso explica por que renomear apenas um lado pode quebrar o efeito. O identificador registrado e o identificador usado pelo emissor precisam permanecer consistentes.

---

## 14. Regras de validação do arquivo

Para identificar um FXC válido, os pontos mais importantes são:

| Checagem | Valor esperado |
|---|---|
| Tamanho mínimo | `0x54` bytes. |
| `serverID` | `0x0019`. |
| `clientID` | valor conhecido entre `0` e `13`. |
| `parmSize` | igual ao tamanho do arquivo solto analisado. |
| Emissor `0..10` | tamanho `0xE4`. |
| Trail `11` | tamanho `0x70`. |
| Field `12` | tamanho `0xB4`. |
| Geometry `13` | tamanho `0x88`. |
| Strings | ASCII, máximo 24 bytes. |

---

## 15. Campos mais úteis para modificação manual

| Objetivo | Campos relevantes |
|---|---|
| Aumentar quantidade visual de partículas | `rate` no FXC e limite/vida no PTC associado. |
| Aumentar velocidade | `speed`, `directionalSpeed`, `normalSpeed`, `speedAwayCenter`, `speedAwayAxis`. |
| Alterar direção | `direction`, `directionalSpeed`, `directionAxis` no caso de `Field`. |
| Espalhar ou concentrar emissão | `spread`, `sectionRadius`, `volumeSweep`. |
| Emitir de outra superfície | `geometryIdentifier` no emissor e `identifier` do FXC Geometry. |
| Mudar força de campo | `magnitude`, `alongAxis`, `aroundAxis`, `awayFromCentreAxis`. |
| Mover efeito localmente | `localMatrix`. |

Campos mais arriscados:

| Campo | Risco |
|---|---|
| `serverID` | Pode fazer o motor tentar carregar o recurso no servidor errado. |
| `clientID` | Muda a interpretação do payload inteiro. |
| `parmSize` | Se não bater com o layout real, pode causar leitura inválida. |
| strings de 24 bytes | Overflow corrompe campos seguintes. |
| `geometryName` | Precisa apontar para uma mesh/recurso existente. |
| matriz local | Pode deslocar/rotacionar o efeito de forma incorreta se a convenção for interpretada errado. |

---

## 16. Inventário dos samples analisados

| Arquivo | Tamanho | ID | Tipo | Dados principais |
|---|---:|---:|---|---|
| `FXC_gravityField5` | 180 / 0xB4 | 12 | Field | Flags=0x00000003; Magnitude=-20.0; Axis=[0.0, 1.0, 0.0] |
| `FXC_BDepoly3` | 136 / 0x88 | 13 | Geometry | Modelo=`MSH_BDepoly3Shape`; ID=`BDepoly3Shape`; Flags=0x00000001 |
| `FXC_BDepoly6` | 136 / 0x88 | 13 | Geometry | Modelo=`MSH_BDepoly6Shape`; ID=`BDepoly6Shape`; Flags=0x00000001 |
| `FXC_BDEsparkemit` | 228 / 0xE4 | 3 | PolySurfaceEmitter | PTC=`flame6Shape`; Geo=`BDepoly6Shape`; Rate=2500.0; Speed=0.75 |
| `FXC_BDEsparkemit0` | 228 / 0xE4 | 3 | PolySurfaceEmitter | PTC=`flame3Shape`; Geo=`BDepoly3Shape`; Rate=1300.0; Speed=0.75 |
| `FXC_BDEsparkemit2` | 228 / 0xE4 | 2 | OmniEmitter | PTC=`flame5Shape`; Geo=`-`; Rate=23.77973; Speed=3.767223 |
| `FXC_BGTemit2` | 228 / 0xE4 | 2 | OmniEmitter | PTC=`BGTpar2Shape`; Geo=`-`; Rate=0.0; Speed=0.0 |
| `FXC_EGemit` | 228 / 0xE4 | 2 | OmniEmitter | PTC=`EGpartShape`; Geo=`-`; Rate=200.0; Speed=0.0 |
| `FXC_EGemit1` | 228 / 0xE4 | 2 | OmniEmitter | PTC=`EGpart1Shape`; Geo=`-`; Rate=200.0; Speed=0.0 |
| `FXC_Fcloudemit4` | 228 / 0xE4 | 1 | DirectionalEmitter | PTC=`FcloudpartShape4`; Geo=`-`; Rate=0.0; Speed=0.0 |
| `FXC_flashemit4` | 228 / 0xE4 | 2 | OmniEmitter | PTC=`FlashpartShape4`; Geo=`-`; Rate=0.0; Speed=3.0 |
| `FXC_GodMhitemitC` | 228 / 0xE4 | 2 | OmniEmitter | PTC=`GodMhitpartCShape`; Geo=`-`; Rate=1000.0; Speed=8.5 |
| `FXC_GodMhitFemit1` | 228 / 0xE4 | 2 | OmniEmitter | PTC=`GodMhitFpart1Shape`; Geo=`-`; Rate=827.624451; Speed=12.0 |

---

## 17. O que está confirmado e o que ainda precisa de teste

### Confirmado

| Item | Status |
|---|---|
| Endian little-endian | Confirmado nos samples. |
| `serverID = 0x0019` para FXC | Confirmado nos samples. |
| `clientID` define o tipo de FX | Confirmado pelos símbolos do ELF. |
| Tipos `0..10` usam payload de emissor | Confirmado pelos símbolos do ELF. |
| Tipo `12` usa payload de Field | Confirmado pelo ELF e pelo sample `FXC_gravityField5`. |
| Tipo `13` usa payload de Geometry | Confirmado pelo ELF e pelos samples `FXC_BDepoly3/6`. |
| Tamanho de emissor `0xE4` | Confirmado nos samples. |
| Tamanho de Field `0xB4` | Confirmado no sample. |
| Tamanho de Geometry `0x88` | Confirmado nos samples. |
| Strings fixas de 24 bytes | Confirmado nos samples. |

### Ainda não totalmente nomeado

| Item | Situação |
|---|---|
| Bits individuais de `attachmentFlags` | Nos samples estão zerados; ainda não há variação suficiente. |
| Bits individuais de `fieldFlags` | O sample de gravidade usa `0x00000003`, mas faltam mais campos para nomear cada bit. |
| Bits individuais de `geometryFlags` | Samples usam `0x00000001`; significado exato ainda depende de comparação com mais recursos. |
| Convenção exata da matriz em runtime | Offsets e valores estão claros; row/column-major precisa de teste visual no jogo. |
| Tipos `4..11` em samples reais | Confirmados por símbolos, mas ainda faltam samples soltos para validar valores práticos. |

---

## 18. Resumo final

O arquivo `FXC` de God of War II é um recurso pequeno, direto e fortemente tipado. Ele começa com o servidor `0x0019`, usa `clientID` para escolher o tipo de efeito, possui um bloco comum de anexo/matriz e termina com um payload específico.

A divisão funcional principal é:

| Tipo | Função |
|---|---|
| `0..10` | Emissores que instanciam sistemas de partículas, normalmente apontando para `PTC`. |
| `11` | Trail/rastro baseado em curva. |
| `12` | Campo de força que altera movimento das partículas. |
| `13` | Registro de geometria usado por emissores de superfície. |

Para modding, os pontos mais importantes são `particleSystemIdentifier`, `geometryIdentifier`, atributos de velocidade/rate/spread e a relação entre `Geometry` e `PolySurfaceEmitter`. Alterações de tipo, tamanho, strings longas e matriz devem ser feitas com cuidado porque podem quebrar o carregamento do efeito no jogo.
