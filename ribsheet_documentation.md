
# Documentação do Formato RIB_sheet

## Visão Geral

O arquivo `.RIB_sheet` é um formato binário utilizado para descrever:
- Estrutura de colisão baseada em KD-Tree.
- Materiais.
- Zonas de contexto.
- Polígonos (triângulos e quadrados).
- Pontos 3D.

O arquivo possui um cabeçalho de tamanho fixo seguido de múltiplas seções variáveis.

---

## Layout do Header (0x90 bytes)

| Offset | Tamanho | Campo                          | Descrição |
|:------:|:-------:|:-------------------------------|:----------|
| 0x0C   | 4 bytes | FileSize                       | Tamanho do arquivo. |
| 0x10   | 4 bytes | Unk0x10 (sempre 0x1f)           | Valor fixo. |
| 0x14   | 4 bytes | Unk0x14 (sempre 0x02140201)     | Valor fixo. |
| 0x18   | 32 bytes| LevelBBox (2x Vec4)             | Bounding box 3D. |
| 0x38   | 2 bytes | Unk0x38                        | Desconhecido. |
| 0x3A   | 2 bytes | Unk0x3a                        | Desconhecido. |
| 0x3C   | 2 bytes | Count Some1 (KDTree nodes)      | Quantidade de nós KDTree. |
| 0x40   | 2 bytes | Count Some6 (Polígonos)         | Quantidade de polígonos. |
| 0x42   | 2 bytes | Unk0x42                        | (Geralmente 0 ou 1). |
| 0x46   | 2 bytes | Count Some7 (Triângulos)        | Quantidade de triângulos. |
| 0x48   | 2 bytes | Count Some8 (Quadrados)         | Quantidade de quads. |
| 0x4A   | 2 bytes | Count Some9 (Pontos 3D)         | Quantidade de vértices. |
| 0x4C   | 2 bytes | Unk0x4c (sempre 0x40)           | Valor fixo. |
| 0x4E   | 2 bytes | Unk0x4e (sempre 0x40)           | Valor fixo. |
| 0x50   | 2 bytes | Count Some4 (Materiais)         | Quantidade de materiais. |
| 0x52   | 2 bytes | Count Some5 (Material Fields)   | Campos extras de materiais. |
| 0x54   | 2 bytes | Count SomeX                    | Desconhecido. |
| 0x56   | 2 bytes | Count Some2 (Contextos)         | Referências de contexto. |
| 0x5A   | 2 bytes | Count Some3 (Nomes de contexto) | Quantidade de nomes de contexto. |
| 0x5C   | 2 bytes | Count Some10 (Zonas)            | Quantidade de zonas. |
| 0x64~0x8C | Vários | Offsets para seções             | Ponteiros para cada seção. |


---

## Seções do Arquivo

### 1. RibKDTreeNode (Seção Some1)
- Estrutura de árvore espacial.
- Cada nó tem:
  - `IsPolygon` (bool)
  - `PlaneCoordinate` (float32) ou `PolygonIndex`
  - `PlaneAxis` (0=x, 1=y, 2=z)
  - `PlaneSubNodeHigher` (uint16)
  - `PolygonsCount` (uint16) *(se IsPolygon)*

### 2. RibContextZone (Seção Some2)
- Mapeamento de zonas.
- Lista de uint16 com estruturas internas de agrupamento.

### 3. Context Names (Seção Some3)
- Strings fixas de 0x18 bytes.

### 4. RibMaterial (Seção Some4)
- Materiais:
  - Nome.
  - Valores dinâmicos lidos segundo `Material Fields`.

### 5. RibMaterialField (Seção Some5)
- Define como ler valores dos materiais:
  - Tipo: `0=int`, `1=float`, `2=bit`.
  - OffsetBytes, OffsetBits, valores default e máximo.
  
| Nome do Campo 	  | Tipo     | OffsetBits | Descrição           							 |
|---------------------|----------|------------|--------------------------------------------------|
| Ground        	  | bitfield | 0          | Superfície normal   							 |
| Water         	  | bitfield | 1          | Água                							 |
| Narrow        	  | bitfield | 2          | Superfície estreita							     |
| Climbable     	  | bitfield | 3          | Superfície escalável							 |
| Ladder        	  | bitfield | 4 	      | Escada											 |
| Slide         	  | bitfield | 5          | Superfície escorregadia							 |
| Death 			  | bitfield | 6          | Mata ao tocar									 |
| AIBlock 			  | bitfield | 16         | Bloqueia IA										 |
| ClimbGuide 		  | bitfield | 17 		  | Guia para escalada								 |
| PushPullSlide 	  | bitfield | 18 		  | Superfície de empurrar/puxar					 |
| BackPress	    	  | bitfield | 19 		  | Pressionar para trás							 |
| NoPlayerCollision   | bitfield | 20 		  | Jogador não colide								 |
| NoAICollision 	  | bitfield | 21 		  | IA não colide									 |
| NoPlayerUse 		  | bitfield | 22 		  | Jogador não usa									 |
| NoAIUse 			  | bitfield | 23 		  | IA não usa								   		 |
| GeneralGuide 		  | bitfield | 24 		  | Guia geral de movimentação						 |
| NoDiving 			  | bitfield | 25 		  | Não pode mergulhar								 |
| NoPushPullCollision | bitfield | 26 		  | Sem colisão de empurrar/puxar					 |
| CombatGuide 		  | bitfield | 27 		  | Guia de combate									 |
| NarrowNoBB 		  | bitfield | 28 		  | Estreito sem bounding box						 |
| NoCSMCollision   	  | bitfield | 29    	  | Sem colisão CSM (possível referência de sistema) |
| TakeDamage 		  | bitfield | 32 		  | Recebe dano 									 |
| NoIK 				  | bitfield | 33 		  | Sem IK (cinemática inversa) 					 |
| NoDecals 			  | bitfield | 34 		  | Sem decalques 									 |
| DarkSurface         | bitfield | 35 		  | Superfície escura								 |
| NoWallHang          | bitfield | 36 		  | Não permite pendurar							 |
| CamRelControl       | bitfield | 37 		  | Controle relativo de câmera						 |
| NoLadderSliding     | bitfield | 38 		  | Não desliza em escada							 |
| NoBackJumps         | bitfield | 39 		  | Sem saltos para trás							 |
| NoShadows           | bitfield | 40 		  | Sem sombras										 |
### 6. RibPolygon (Seção Some6)
- Definição de polígonos.
- Cada entrada é 2 bytes:
  - 1 bit: Quad ou Triângulo.
  - 15 bits: Índice para triângulo ou quad.

### 7. RibTriangle (Seção Some7)
- Lista de triângulos:
  - 10 bytes por triângulo (Flags, Material Index, 3 vértices).

### 8. RibQuad (Seção Some8)
- Lista de quadrados:
  - 12 bytes por quad (Flags, Material Index, 4 vértices).

### 9. Pontos 3D (Seção Some9)
- Cada ponto é um Vec3 (x, y, z), 12 bytes.

### 10. RibZone (Seção Some10)
- Bounding box e referência de zona.


---

## Lista de Flags Conhecidas

### RibKDTreeNode
- **IsPolygon**: 1 bit indicando se é polígono (1) ou plano de divisão (0).
- **PlaneAxis**: 2 bits (0=x, 1=y, 2=z).
- **PolygonIndex**: 24 bits (índice de polígono).
- **PolygonFlag**: 8 bits adicionais.

### RibMaterialField
- **Type**:
  - 0 = Inteiro
  - 1 = Float
  - 2 = Bitfield (booleano)
- **Flags Diversos**: (IsBitField1, IsBitField2)

### RibPolygon
- 1 bit definindo se é quad (1) ou triângulo (0).

### RibTriangle / RibQuad Flags
- **Flags**: Não completamente documentado; geralmente são propriedades de renderização ou colisão.


---

# Fim da Documentação
