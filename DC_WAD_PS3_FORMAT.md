# DC_WAD PS3 — documentação funcional e serialização semântica
Esta versão amplia a documentação inicial com o cruzamento entre os chunks `[011]`..`[016]` e os símbolos/STABS do ELF `SCUS_974.67`. O ELF é MIPS/PS2 e foi usado como referência de nomes e comportamento; os chunks enviados estão em big-endian, compatíveis com a hipótese de payload PS3 usando a mesma família DC com troca de endian.
## Resultado principal
- O DC_WAD PS3 mantém os chunks `Version`, `Data`, `ExportTable`, `ImportTable`, `DbgStringTable` e `DbgSymbolTable`.
- `[012]` é o payload real. `[016]` não define apenas objetos independentes: ele também marca campos/subestruturas internas, como `CrankSetWind` dentro de `IO_SLCRANK`.
- `WAD_isle06` é o `dc::tWadInfo` raiz. Ele aponta/descreve arrays de `dc::tGOPool` e `dc::tMemoryPool`.
- `IO_SLCRANK` é um `dc::tIO` exportado, com parâmetros de crank/pushable, prompt `PB_StickBack`, ranges de handle e referências de som.
- `BRK_URN` e `BRK_BONES` são `dc::tBreakable` exportados e importam o mesmo emitter `ORBE_BREAK_SMALL`.
## Funções do loader DC confirmadas no ELF
| Endereço | Tamanho | Função | Papel |
|---:|---:|---|---|
| `0x001DA670` | 184 | `ActivateLevelWad__13GameWadLoaderi` |  |
| `0x001DDEC0` | 88 | `ActivateWadContext__13GameWadLoaderP10wadContext` |  |
| `0x001D9DC8` | 232 | `AddCommand__13GameWadLoaderiiUiPCc` |  |
| `0x001DABB0` | 68 | `AddHUDFlash__13GameWadLoader` |  |
| `0x001DA4A0` | 464 | `AddLevelWad__13GameWadLoaderPCcT1Ui` |  |
| `0x001DAB50` | 44 | `AddShellFlash__13GameWadLoader` |  |
| `0x001DB168` | 288 | `BeginPersistentWad__13GameWadLoader` |  |
| `0x001DD0D8` | 668 | `BeginWad__13GameWadLoaderi` | inicia ativação/carga de WAD |
| `0x001DAF10` | 64 | `CancelLoadCheck__13GameWadLoaderPCc` |  |
| `0x001D9620` | 160 | `CreateHeap__13GameWadLoaderPCciT1` |  |
| `0x00120680` | 164 | `Create__Q22dc7ContextPCc` | cria contexto DC por nome |
| `0x001DA728` | 92 | `DeactivateLevelWad__13GameWadLoaderi` |  |
| `0x001DDF18` | 84 | `DeactivateWadContext__13GameWadLoaderP10wadContext` |  |
| `0x00120580` | 132 | `DeleteAllFiles__Q22dc7Context` |  |
| `0x001DB288` | 268 | `EndPersistentWad__13GameWadLoader` |  |
| `0x001DD378` | 140 | `EndWad__13GameWadLoaderi` | finaliza ativação/carga de WAD |
| `0x001DD5F0` | 284 | `FinishLoadHero__13GameWadLoaderi` |  |
| `0x001207B0` | 56 | `Free__Q22dc4File` |  |
| `0x001D9EB0` | 124 | `GetLevelWadSlot__13GameWadLoaderPCc` |  |
| `0x001DB090` | 212 | `GetPersistentWad__13GameWadLoaderPc` |  |
| `0x001DA068` | 320 | `GetQueuedLoad__13GameWadLoaderiPcRUc` |  |
| `0x00120050` | 52 | `GetSymHash__2dcPCc` | gera/retorna hash de símbolo |
| `0x001D9F30` | 312 | `GetWadState__13GameWadLoaderPA19_cPUc` |  |
| `0x00120608` | 116 | `Get__Q22dc7ContextPCc` | obtém contexto DC por nome |
| `0x001201B0` | 176 | `IFFProcessData__Q22dc6LoaderPQ23IFF6HeaderPCc` | carrega o DataChunk bruto |
| `0x00120260` | 284 | `IFFProcessExportTable__Q22dc6LoaderPQ23IFF6HeaderPCc` | processa símbolos exportados por nome |
| `0x00120380` | 220 | `IFFProcessImportTable__Q22dc6LoaderPQ23IFF6HeaderPCc` | processa offsets que serão resolvidos contra imports |
| `0x00120460` | 28 | `IFFProcessStringTable__Q22dc6LoaderPQ23IFF6HeaderPCc` | processa hash → string de classe/tipo |
| `0x00120480` | 28 | `IFFProcessSymbolTable__Q22dc6LoaderPQ23IFF6HeaderPCc` | processa offset → nome → typeId para debug/reflexão |
| `0x00120150` | 96 | `IFFProcessVersion__Q22dc6LoaderPQ23IFF6HeaderPCc` | lê o VersionChunk / hash do header |
| `0x001D9CD8` | 236 | `IFFResourceNames__13GameWadLoaderPQ23IFF6HeaderPCc` |  |
| `0x001D96E8` | 928 | `InitGame__13GameWadLoader` |  |
| `0x001D96C0` | 36 | `Init__13GameWadLoader` |  |
| `0x001DAA58` | 12 | `IsSaveGameImgWadLoaded__13GameWadLoader` |  |
| `0x001DA9F0` | 12 | `IsShellWadLoaded__13GameWadLoader` |  |
| `0x00120118` | 52 | `IsSymAvailable__2dcPCcP10wadContext` | testa se símbolo existe no contexto |
| `0x001DAE08` | 264 | `LoadCheck__13GameWadLoaderPCc` |  |
| `0x001DAC28` | 52 | `LoadFirstLevel__13GameWadLoaderPCc` |  |
| `0x001DAC60` | 216 | `LoadFreshWads__13GameWadLoaderPCcT1Ui` |  |
| `0x001DD440` | 144 | `LoadHeroWad__13GameWadLoaderUi` |  |
| `0x001DAF50` | 240 | `LoadPersistentWad__13GameWadLoaderPCci` |  |
| `0x001DAA00` | 88 | `LoadSaveGameImgWad__13GameWadLoader` |  |
| `0x001DADC8` | 64 | `LoadSaveGame__13GameWadLoaderPCc` |  |
| `0x001DA908` | 228 | `LoadShellWad__13GameWadLoader` |  |
| `0x001DD710` | 172 | `LoadUpgradeLevel__13GameWadLoaderPCcii` |  |
| `0x00120088` | 48 | `LookupSym__2dcPCcP10wadContext` | procura símbolo por hash/nome no wadContext |
| `0x001200B8` | 96 | `LookupSym__2dcUiP10wadContext` | procura símbolo por hash/nome no wadContext |
| `0x001DCEB8` | 244 | `OverrideHeapSize__13GameWadLoaderPCc` |  |
| `0x001DE010` | 116 | `PermWadContext__13GameWadLoader` |  |
| `0x001D9B48` | 156 | `PreCreateGOPools__13GameWadLoaderPCQ22dc8tWadInfo` | pré-cria pools de game objects a partir de dc::tWadInfo |
| `0x001D9A88` | 192 | `PreCreateMemoryPools__13GameWadLoaderP10wadContextPCQ22dc8tWadInfo` | pré-cria pools de memória a partir de dc::tWadInfo |
| `0x001D9BE8` | 236 | `PreCreatePermGOPools__13GameWadLoaderPCc` |  |
| `0x001DA408` | 152 | `RemoveCanceledLevelWad__13GameWadLoader` |  |
| `0x001DABF8` | 44 | `RemoveHUDFlash__13GameWadLoader` |  |
| `0x001DA310` | 204 | `RemoveLevelWadCommands__13GameWadLoaderPCcT1` |  |
| `0x001DA1A8` | 356 | `RemoveQueuedCommands__13GameWadLoaderiii` |  |
| `0x001DA3E0` | 36 | `RemoveRsrcWadCommands__13GameWadLoader` |  |
| `0x001DAB80` | 44 | `RemoveShellFlash__13GameWadLoader` |  |
| `0x001DAD80` | 72 | `ResetLevelWad__13GameWadLoaderi` |  |
| `0x001DB040` | 76 | `ResetPersistentWad__13GameWadLoader` |  |
| `0x001DD408` | 52 | `ResetWad__13GameWadLoaderi` |  |
| `0x001DAA68` | 188 | `RestartShellFlash__13GameWadLoaderi` |  |
| `0x001DB470` | 6724 | `Run__13GameWadLoader` | máquina principal de carregamento de WADs |
| `0x001DD898` | 952 | `SetExternalLevelFileSets__13GameWadLoaderii` |  |
| `0x001DDC50` | 308 | `SetExternalPersistentFileSets__13GameWadLoaderi` |  |
| `0x001DDD88` | 312 | `SetExternalUpgradeFileSets__13GameWadLoaderii` |  |
| `0x001DD7C0` | 212 | `SetParentLinks__13GameWadLoaderi` |  |
| `0x001DB398` | 216 | `SetResourceWads__13GameWadLoaderii` |  |
| `0x001DD4D0` | 288 | `SwitchHero__13GameWadLoaderUi` |  |
| `0x001DAD38` | 72 | `UnloadLevelWad__13GameWadLoaderiUi` |  |
| `0x001DA840` | 108 | `UnloadRsrcWads__13GameWadLoader` |  |
| `0x001DAB28` | 36 | `UnloadShellWad__13GameWadLoader` |  |
| `0x001DA8B0` | 84 | `UnloadUpgradeWads__13GameWadLoader` |  |
| `0x001DDF70` | 156 | `UnloadWadContext__13GameWadLoaderP10wadContext` |  |
| `0x001DA788` | 180 | `WarpLevelWads__13GameWadLoaderi` |  |
| `0x00120730` | 56 | `__Q22dc4FilePCc` |  |
| `0x001204A0` | 64 | `__Q22dc7Context` |  |

## TypeIds e layouts confirmados/inferidos
| TypeId | Tipo | Layout/função |
|---:|---|---|
| `0x00E5` | `dc::tWadInfo` | size 8; m_GOPool pointer/array descriptor at +0x00; m_MemoryPools pointer/array descriptor at +0x04; getters GetGOPool/GetMemoryPools/Get*Length/Get*Entry. |
| `0x00E3` | `dc::tGOPool` | size 8; +0x00 uint32 m_Name/hash; +0x04 uint8 m_Cnt; serialized sample uses BE uint16 count at +0x04 plus pad16. |
| `0x00E4` | `dc::tMemoryPool` | size 8; +0x00 uint32 m_Name/hash; +0x04 uint16 m_ElementCount; +0x06 pad16. |
| `0x0069` | `dc::tIO` | STABS size 108; sample has 4-byte serialized prefix, then fields: type/weight/flags/defaultDir, CrankPrompt, physics floats, 3 tCrankMotionSet blocks, brake/window/list, sound refs, click counts, handle ranges. |
| `0x006A` | `dc::tIO::tCrankMotionSet` | size 8; +0x00 float m_Acceleration; +0x04 float m_MaxSpeed. |
| `0x0066` | `Handle field group` | debug symbol at offset 0x02B8 inside IO_SLCRANK; corresponds to handle/range area, not a standalone object boundary in this sample. |
| `0x0071` | `dc::tBreakable` | size 24; four 32-bit refs: OrbEmitter/AirOrbEmitter/Bonus/AirBonus; then hit/opaque/fade as fixed8.8 u16, type u8, flags u8. |

## `dc::tWadInfo` raiz — `WAD_isle06`
O objeto raiz em `[012]+0x0000` possui 8 bytes. Pelos STABS, `dc::tWadInfo` contém dois descritores de array: `m_GOPool` e `m_MemoryPools`. No sample:

- raw0 = `0x00008017`; array efetivo de `tGOPool`: `0x0008-0x00BF`, 23 entradas.
- raw1 = `0x000BC033`; array efetivo de `tMemoryPool`: `0x00C0-0x0257`, 51 entradas.

Esses arrays alimentam diretamente `GameWadLoader::PreCreateGOPools` e `GameWadLoader::PreCreateMemoryPools`.

## Tabela de `dc::tGOPool`
| Offset | Símbolo | Hash | Classe | Count | Função |
|---:|---|---:|---|---:|---|
| `0x0008` | `tGOPool_1` | `0xA9443B39` | `goChest` | 7 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0010` | `tGOPool_5` | `0x0F2B776B` | `goSavePoint` | 1 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0018` | `tGOPool_7` | `0x2EE76396` | `goGrapplePtInactive` | 1 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0020` | `tGOPool_8` | `0x0DC35336` | `goDeadb00` | 1 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0028` | `tGOPool_21` | `0x28B4028C` | `goOrders10` | 5 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0030` | `tGOPool_22` | `0x4F07ABC1` | `goStoneOrders10` | 10 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0038` | `tGOPool_23` | `0x8B886903` | `goFreezeOrders10` | 5 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0040` | `tGOPool_24` | `0x5B254A33` | `goSpawnHole` | 5 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0048` | `tGOPool_25` | `0x71954EB4` | `goDeathParts` | 5 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0050` | `tGOPool_26` | `0xE2EA5761` | `goMiniGameCircle` | 5 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0058` | `tGOPool_27` | `0x1624F7AF` | `goOrdersKillBlood` | 5 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0060` | `tGOPool_28` | `0xFC56E002` | `goOrdersKillBlood1` | 5 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0068` | `tGOPool_29` | `0x59B8EC08` | `goCSGHoldMilk1` | 5 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0070` | `tGOPool_30` | `0x59B8EC09` | `goCSGHoldMilk2` | 5 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0078` | `tGOPool_31` | `0x7BBEAE06` | `goOrderSASwipe` | 5 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0080` | `tGOPool_32` | `0xBDC0DD1C` | `goOrderSBladeG` | 5 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0088` | `tGOPool_33` | `0x9EA56755` | `goGenericBlockS` | 20 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0090` | `tGOPool_47` | `0x6DA783F1` | `goRabdog00` | 10 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x0098` | `tGOPool_48` | `0x93FB2D26` | `goStoneRabdog00` | 20 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x00A0` | `tGOPool_49` | `0xD07BEA68` | `goFreezeRabdog00` | 10 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x00A8` | `tGOPool_50` | `0x5B254A33` | `goSpawnHole` | 10 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x00B0` | `tGOPool_51` | `0x71954EB4` | `goDeathParts` | 10 | pré-aloca capacidade de instâncias GO via `goPool` |
| `0x00B8` | `tGOPool_52` | `0x63BF8887` | `goRdogFoam` | 10 | pré-aloca capacidade de instâncias GO via `goPool` |

## Tabela de `dc::tMemoryPool`
| Offset | Símbolo | Hash | Classe/Sistema | Count | Função |
|---:|---|---:|---|---:|---|
| `0x00C0` | `tMemoryPool_0` | `0x28D4125E` | `odbEffect` | 10 | pré-aloca elementos de memória runtime |
| `0x00C8` | `tMemoryPool_2` | `0xF29974DC` | `hfsmIO_CSM` | 7 | pré-aloca elementos de memória runtime |
| `0x00D0` | `tMemoryPool_3` | `0x8FFD98EC` | `goIO_CSM` | 7 | pré-aloca elementos de memória runtime |
| `0x00D8` | `tMemoryPool_4` | `0x672501CB` | `tHandleSystem` | 7 | pré-aloca elementos de memória runtime |
| `0x00E0` | `tMemoryPool_6` | `0x2F65837B` | `hfsmSavePoint` | 1 | pré-aloca elementos de memória runtime |
| `0x00E8` | `tMemoryPool_9` | `0x21503BD1` | `hfsmReactive` | 1 | pré-aloca elementos de memória runtime |
| `0x00F0` | `tMemoryPool_10` | `0x8137C547` | `goPassiveAI` | 1 | pré-aloca elementos de memória runtime |
| `0x00F8` | `tMemoryPool_11` | `0x83EBA3BF` | `goPassive` | 1 | pré-aloca elementos de memória runtime |
| `0x0100` | `tMemoryPool_12` | `0x63B709F4` | `tAnimSystem` | 1 | pré-aloca elementos de memória runtime |
| `0x0108` | `tMemoryPool_13` | `0x16D04154` | `tMoveSystem` | 1 | pré-aloca elementos de memória runtime |
| `0x0110` | `tMemoryPool_14` | `0x13AB20F1` | `tStandardEffectSystem` | 1 | pré-aloca elementos de memória runtime |
| `0x0118` | `tMemoryPool_15` | `0x860166C1` | `hfsmBreakable` | 2 | pré-aloca elementos de memória runtime |
| `0x0120` | `tMemoryPool_16` | `0x672501CB` | `tHandleSystem` | 2 | pré-aloca elementos de memória runtime |
| `0x0128` | `tMemoryPool_17` | `0x08BEC00E` | `goIO` | 2 | pré-aloca elementos de memória runtime |
| `0x0130` | `tMemoryPool_18` | `0x5B57154D` | `hfsmIO_Misc` | 2 | pré-aloca elementos de memória runtime |
| `0x0138` | `tMemoryPool_19` | `0x1FF7A7C5` | `tMove` | 2 | pré-aloca elementos de memória runtime |
| `0x0140` | `tMemoryPool_20` | `0x7901DD00` | `fxBoneData` | 1 | pré-aloca elementos de memória runtime |
| `0x0148` | `tMemoryPool_34` | `0x28D4125E` | `odbEffect` | 15 | pré-aloca elementos de memória runtime |
| `0x0150` | `tMemoryPool_35` | `0x1AF7BEE1` | `hfsmEnemy1` | 5 | pré-aloca elementos de memória runtime |
| `0x0158` | `tMemoryPool_36` | `0x8F74ACDA` | `goSoldier` | 5 | pré-aloca elementos de memória runtime |
| `0x0160` | `tMemoryPool_37` | `0x63B709F4` | `tAnimSystem` | 5 | pré-aloca elementos de memória runtime |
| `0x0168` | `tMemoryPool_38` | `0x16D04154` | `tMoveSystem` | 5 | pré-aloca elementos de memória runtime |
| `0x0170` | `tMemoryPool_39` | `0x43DF450B` | `tFightSystem` | 5 | pré-aloca elementos de memória runtime |
| `0x0178` | `tMemoryPool_40` | `0x13AB20F1` | `tStandardEffectSystem` | 5 | pré-aloca elementos de memória runtime |
| `0x0180` | `tMemoryPool_41` | `0x860166C1` | `hfsmBreakable` | 10 | pré-aloca elementos de memória runtime |
| `0x0188` | `tMemoryPool_42` | `0x672501CB` | `tHandleSystem` | 10 | pré-aloca elementos de memória runtime |
| `0x0190` | `tMemoryPool_43` | `0x08BEC00E` | `goIO` | 10 | pré-aloca elementos de memória runtime |
| `0x0198` | `tMemoryPool_44` | `0x5B57154D` | `hfsmIO_Misc` | 10 | pré-aloca elementos de memória runtime |
| `0x01A0` | `tMemoryPool_45` | `0x1FF7A7C5` | `tMove` | 10 | pré-aloca elementos de memória runtime |
| `0x01A8` | `tMemoryPool_46` | `0x7901DD00` | `fxBoneData` | 1 | pré-aloca elementos de memória runtime |
| `0x01B0` | `tMemoryPool_53` | `0x28D4125E` | `odbEffect` | 30 | pré-aloca elementos de memória runtime |
| `0x01B8` | `tMemoryPool_54` | `0x1AF7BEE1` | `hfsmEnemy1` | 10 | pré-aloca elementos de memória runtime |
| `0x01C0` | `tMemoryPool_55` | `0x8F74ACDA` | `goSoldier` | 10 | pré-aloca elementos de memória runtime |
| `0x01C8` | `tMemoryPool_56` | `0x63B709F4` | `tAnimSystem` | 10 | pré-aloca elementos de memória runtime |
| `0x01D0` | `tMemoryPool_57` | `0x16D04154` | `tMoveSystem` | 10 | pré-aloca elementos de memória runtime |
| `0x01D8` | `tMemoryPool_58` | `0x43DF450B` | `tFightSystem` | 10 | pré-aloca elementos de memória runtime |
| `0x01E0` | `tMemoryPool_59` | `0x13AB20F1` | `tStandardEffectSystem` | 10 | pré-aloca elementos de memória runtime |
| `0x01E8` | `tMemoryPool_60` | `0x860166C1` | `hfsmBreakable` | 20 | pré-aloca elementos de memória runtime |
| `0x01F0` | `tMemoryPool_61` | `0x672501CB` | `tHandleSystem` | 20 | pré-aloca elementos de memória runtime |
| `0x01F8` | `tMemoryPool_62` | `0x08BEC00E` | `goIO` | 20 | pré-aloca elementos de memória runtime |
| `0x0200` | `tMemoryPool_63` | `0x5B57154D` | `hfsmIO_Misc` | 20 | pré-aloca elementos de memória runtime |
| `0x0208` | `tMemoryPool_64` | `0x1FF7A7C5` | `tMove` | 20 | pré-aloca elementos de memória runtime |
| `0x0210` | `tMemoryPool_65` | `0x7901DD00` | `fxBoneData` | 1 | pré-aloca elementos de memória runtime |
| `0x0218` | `tMemoryPool_66` | `0x21503BD1` | `hfsmReactive` | 1 | pré-aloca elementos de memória runtime |
| `0x0220` | `tMemoryPool_67` | `0x8137C547` | `goPassiveAI` | 1 | pré-aloca elementos de memória runtime |
| `0x0228` | `tMemoryPool_68` | `0x83EBA3BF` | `goPassive` | 1 | pré-aloca elementos de memória runtime |
| `0x0230` | `tMemoryPool_69` | `0x63B709F4` | `tAnimSystem` | 1 | pré-aloca elementos de memória runtime |
| `0x0238` | `tMemoryPool_70` | `0x16D04154` | `tMoveSystem` | 1 | pré-aloca elementos de memória runtime |
| `0x0240` | `tMemoryPool_71` | `0x13AB20F1` | `tStandardEffectSystem` | 1 | pré-aloca elementos de memória runtime |
| `0x0248` | `tMemoryPool_72` | `0x1FF7A7C5` | `tMove` | 2 | pré-aloca elementos de memória runtime |
| `0x0250` | `tMemoryPool_73` | `0x7901DD00` | `fxBoneData` | 1 | pré-aloca elementos de memória runtime |

## Objeto exportado `IO_SLCRANK` — `dc::tIO` / TypeId `0x0069`
O símbolo começa em `0x0258` e possui um prefixo serializado de 4 bytes antes do layout STABS de `dc::tIO`. O layout útil começa em `0x025C`.

| Campo | Valor decodificado | Função semântica |
|---|---:|---|
| `serializedPrefix` | `0x00010012` | metadado/descritor DC antes da struct tIO |
| `m_Type` | `0` | tipo de IO |
| `m_Weight` | `0` | peso/categoria de empurrável |
| `m_Flags` | `0` | flags de comportamento |
| `m_DefaultDir` | `0` | direção inicial/default |
| `m_CrankPrompt` | `0xD08A6D6A `PB_StickBack`` | prompt/input exibido/esperado para crank |
| `m_Radius` | `1.0` | raio de interação/colisão |
| `m_Height` | `2.0` | altura física |
| `m_Buoyancy` | `0.5` | flutuabilidade |
| `m_MaxSlopeSpeed` | `5.0` | velocidade máxima em inclinação |
| `m_CrankSetWind` | `acc=1.5, max=1.5` | motion set de girar/enrolar |
| `m_CrankSetUnwind` | `acc=1.0, max=1.5` | motion set de desenrolar/voltar |
| `m_CrankSetEvent` | `acc=2.0, max=2.0` | motion set usado ao disparar evento |
| `m_CrankBrake` | `50.0` | freio/desaceleração do crank |
| `m_CrankStopWindow` | `0.029999999329447746` | janela de snap/parada |
| `m_CrankClickFCount` | `10` | contagem de cliques forward |
| `m_CrankClickBCount` | `15` | contagem de cliques backward |
| `m_HandleYRange` | `0.0` | range vertical para agarrar |
| `m_HandleXZRange` | `1.0` | range horizontal XZ para agarrar |
| `m_HandleAngle` | `1.75` | ângulo de handle |
| `m_HandleBehindRange` | `72.5` | tolerância atrás do handle |

Strings próximas usadas por esse IO:

- `0x02D5`: `SND_CRANKFORWARD`
- `0x02E6`: `SND_CRANKBACKWARD`

Funções runtime relevantes de `goIO` confirmadas no ELF incluem `Init__4goIOPCQ22dc3tIOi`, `DoCrankMotion__4goIOUiUi`, `CrankProcessMotion__4goIO...`, `CrankGetChangesetAndTarget__4goIO...`, `SendProgressEvent__4goIOfff`, `HandlePushableSounds__4goIO...`, `HandlePushableParticles__4goIO...`, `GrabState__4goIO`, `Pause`, `Unpause`, `Restore`, `Reset` e `Update`.

## Objetos exportados `BRK_URN` e `BRK_BONES` — `dc::tBreakable` / TypeId `0x0071`
| Objeto | Offset | Import | HitPoints | OpaqueTime | FadeTime | Type | Flags | Função |
|---|---:|---|---:|---:|---:|---:|---:|---|
| `BRK_URN` | `0x02F8` | `ORBE_BREAK_SMALL` | 69.0 | 60.0 | 56.0 | 0 | 3 | configura quebrável, vida e timing de desaparecimento/orbs |
| `BRK_BONES` | `0x0310` | `ORBE_BREAK_SMALL` | 64.0 | 56.0 | 60.0 | 0 | 2 | configura quebrável, vida e timing de desaparecimento/orbs |

O import em `[014]` aplica `ORBE_BREAK_SMALL` no offset do primeiro campo (`m_OrbEmitter`) de cada quebrável.

## Mapa funcional das classes/hashes presentes em `[015]DbgStringTableChunk`
| Hash | Classe | GO pool | Memory pool | Função semântica | Funções ELF encontradas |
|---:|---|---:|---:|---|---:|
| `0x08BEC00E` | `goIO` | - | 2,10,20 | Classe runtime de objeto interativo empurrável/girável/alavanca/crank; usa dc::tIO como tweaker de física, prompt, sons e parâmetros de handle. | 46 |
| `0x0DC35336` | `goDeadb00` | 1 | - | Variante de corpo/morto; sem classe direta, relacionada a death/freeze/passive pipeline. | 0 |
| `0x0F2B776B` | `goSavePoint` | 1 | - | Objeto de save point; no ELF há HFSM_SavePoint e código de beam/material. | 1 |
| `0x13AB20F1` | `tStandardEffectSystem` | - | 1,5,10,1 | Sistema padrão de efeitos de morte/fade/dead particles para criaturas. | 12 |
| `0x1624F7AF` | `goOrdersKillBlood` | 5 | - | Variante de efeito/entidade de sangue para Orders. | 0 |
| `0x16D04154` | `tMoveSystem` | - | 1,5,10,1 | Sistema de movimentos/combat branches, colisão, dano, quick block e reação. | 54 |
| `0x1AF7BEE1` | `hfsmEnemy1` | - | 5,10 | HFSM genérica de inimigo; estados de target, wander, patrol, attack, frozen, prepdie etc. | 72 |
| `0x1FF7A7C5` | `tMove` | - | 2,10,20,2 | Instância de move/branch acionável; usado pelo tMoveSystem para ações, colisões e scripts. | 25 |
| `0x21503BD1` | `hfsmReactive` | - | 1,1 | HFSM reativo; sem símbolos diretos neste ELF, usado como pool de comportamento para entidades passivas/reativas. | 0 |
| `0x28B4028C` | `goOrders10` | 5 | - | Variante de inimigo Orders com quantidade 10; sem classe direta no ELF, tratado por goSoldier/hfsmEnemy1/tMove. | 0 |
| `0x28D4125E` | `odbEffect` | - | 10,15,30 | Objeto de efeito animado; cria efeitos por nome/parm, pode emitir por osso via fxBoneData. | 21 |
| `0x2EE76396` | `goGrapplePtInactive` | 1 | - | Ponto de grapple inativo/ativável; família goGrapplePoint tem Enable/Disable/Activate/Deactivate. | 39 |
| `0x2F65837B` | `hfsmSavePoint` | - | 1 | HFSM do save point; controla estado inicial e eventos da estação de save. | 17 |
| `0x43DF450B` | `tFightSystem` | - | 5,10 | Sistema de combate associado ao goCreature/goSoldier. | 14 |
| `0x4F07ABC1` | `goStoneOrders10` | 10 | - | Variante petrificada de Orders; sem classe direta, usa sistemas de soldado/freeze/stone. | 1 |
| `0x59B8EC08` | `goCSGHoldMilk1` | 5 | - | Objeto/prop de cutscene ou carregável específico do mapa; sem função direta no ELF. | 0 |
| `0x59B8EC09` | `goCSGHoldMilk2` | 5 | - | Objeto/prop de cutscene ou carregável específico do mapa; sem função direta no ELF. | 0 |
| `0x5B254A33` | `goSpawnHole` | 5,10 | - | Spawner/portal de inimigos; ligado a estatística EnemySpawns e lógica de spawn. | 2 |
| `0x5B57154D` | `hfsmIO_Misc` | - | 2,10,20 | HFSM genérico para IO misc; estados Initial/Done. | 21 |
| `0x63B709F4` | `tAnimSystem` | - | 1,5,10,1 | Sistema de animação por criatura/objeto; play, blend, slave anim clients e callbacks. | 25 |
| `0x63BF8887` | `goRdogFoam` | 10 | - | Efeito/variante de foam do Rabdog. | 0 |
| `0x672501CB` | `tHandleSystem` | - | 7,2,10,20 | Sistema runtime de handles/joints agarráveis; inicializa joints de handle a partir do esqueleto e ranges. | 10 |
| `0x6DA783F1` | `goRabdog00` | 10 | - | Variante Rabdog; sem classe direta, usa pools de soldado/AI/move. | 0 |
| `0x71954EB4` | `goDeathParts` | 5,10 | - | Partes/efeitos de morte; sem classe direta, relacionado a StandardEffectSystem/odbEffect. | 0 |
| `0x7901DD00` | `fxBoneData` | - | 1,1,1,1 | Dados auxiliares para emissão de efeito por osso/joint. | 2 |
| `0x7BBEAE06` | `goOrderSASwipe` | 5 | - | Variante/ataque swipe de Orders. | 0 |
| `0x8137C547` | `goPassiveAI` | - | 1,1 | AI/controller passivo; no sample só há pool pequeno, normalmente acoplado a goPassive/hfsmReactive. | 102 |
| `0x83EBA3BF` | `goPassive` | - | 1,1 | Criatura/passivo com física, freeze/death/incarnation, navegação e colisão. | 72 |
| `0x860166C1` | `hfsmBreakable` | - | 2,10,20 | HFSM de quebráveis; estados START, Initial, Idle, Smash e Done. | 32 |
| `0x8B886903` | `goFreezeOrders10` | 5 | - | Variante congelada de Orders; sem classe direta, usa sistemas de freeze/soldado. | 8 |
| `0x8F74ACDA` | `goSoldier` | - | 5,10 | Classe de criatura humanoide/inimigo; usada aqui como pool base para inimigos Rabdog/Orders/Soldier. | 136 |
| `0x8FFD98EC` | `goIO_CSM` | - | 7 | Variante de objeto interativo/chest-style; controla animação de baú/CSM, emissão de orbs e estado persistente. | 29 |
| `0x93FB2D26` | `goStoneRabdog00` | 20 | - | Rabdog petrificado; sem classe direta, usa freeze/stone pipeline. | 1 |
| `0x9EA56755` | `goGenericBlockS` | 20 | - | Objeto de bloqueio genérico pequeno; sem símbolos diretos, provavelmente data-driven. | 47 |
| `0xA9443B39` | `goChest` | 7 | - | Nome de pool para baús; neste ELF parece data-driven via goIO_CSM/hfsmIO_CSM, não classe C++ direta. | 43 |
| `0xBDC0DD1C` | `goOrderSBladeG` | 5 | - | Variante/ataque blade de Orders. | 3 |
| `0xD07BEA68` | `goFreezeRabdog00` | 10 | - | Rabdog congelado; sem classe direta, usa freeze pipeline. | 8 |
| `0xD08A6D6A` | `PB_StickBack` | - | - | Hash/string de prompt/input; usado como m_CrankPrompt em IO_SLCRANK. | 0 |
| `0xE2EA5761` | `goMiniGameCircle` | 5 | - | Objeto de círculo/minigame; sem classe direta neste ELF. | 0 |
| `0xF29974DC` | `hfsmIO_CSM` | - | 7 | HFSM da variante CSM/chest; estados Initial/Done e EntryCode com IO_CSM_Inter. | 21 |
| `0xFC56E002` | `goOrdersKillBlood1` | 5 | - | Outra variante de efeito/entidade de sangue para Orders. | 0 |

## Apêndice A — funções ELF por classe/hash do sample
Quando uma classe não possui símbolo direto, a documentação marca isso explicitamente. Para variantes data-driven (`goChest`, `goOrders10`, `goRabdog00`, etc.), os símbolos geralmente aparecem nos sistemas base (`goIO_CSM`, `goSoldier`, `hfsmEnemy1`, `tMoveSystem`).

### `goIO` — `0x08BEC00E`

Função semântica: Classe runtime de objeto interativo empurrável/girável/alavanca/crank; usa dc::tIO como tweaker de física, prompt, sons e parâmetros de handle.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00234C40` | 64 | `Allocate__4goIOP10wadContext` |
| `0x00235B20` | 732 | `CheckPositionForIO__4goIOR6VUVec4Ui` |
| `0x00237540` | 828 | `CrankGetChangesetAndTarget__4goIOfiiPPCQ32dc3tIO15tCrankMotionSetPfPi` |
| `0x00237880` | 284 | `CrankProcessMotion__4goIOPCQ32dc3tIO15tCrankMotionSetffiii` |
| `0x002379A0` | 336 | `CrankProcessSound__4goIOf` |
| `0x00236488` | 100 | `DoAttachedToMotion__4goIO` |
| `0x00237AF0` | 1192 | `DoCrankMotion__4goIOUiUi` |
| `0x00237358` | 484 | `DoLeverMotion__4goIO` |
| `0x002364F0` | 900 | `DoPushableMotion__4goIORiG6VUVec4` |
| `0x00264C08` | 8 | `GetAnimSystem__C4goIO` |
| `0x00264C18` | 8 | `GetAttachmentSystem__4goIO` |
| `0x00264C00` | 8 | `GetCreatureControls__4goIO` |
| `0x00264C30` | 8 | `GetEffectSystem__4goIO` |
| `0x00264C28` | 8 | `GetFightSystem__4goIO` |
| `0x00264C20` | 8 | `GetHandleSystem__4goIO` |
| `0x00264C48` | 16 | `GetHeight__C4goIO` |
| `0x00264C38` | 12 | `GetMass__C4goIO` |
| `0x00264C10` | 8 | `GetMoveSystem__C4goIO` |
| `0x002362B0` | 260 | `GetPushSpeed__4goIOi` |
| `0x00264C58` | 16 | `GetRadius__C4goIO` |
| `0x00235448` | 508 | `GrabState__4goIO` |
| `0x002358B8` | 428 | `HandleFloatingAndUnderwater__4goIOR6VUVec4` |
| `0x00236910` | 180 | `HandlePushableParticles__4goIOG6VUVec4` |
| `0x002369C8` | 492 | `HandlePushableSounds__4goIOUiG6VUVec4` |
| `0x00235E00` | 1200 | `HandleWalkingSlidingFalling__4goIOG6VUVec4` |
| `0x00235A68` | 184 | `InTheWaterGroundChecks__4goIOG6VUVec4` |
| `0x00234E60` | 1364 | `Init__4goIOPCQ22dc3tIOi` |
| `0x001D1170` | 288 | `LoadIOState__7WadInfoP12goGameObjectPvP4goIO` |
| `0x00236878` | 152 | `ModulateScrapeParticles__4goIOf` |
| `0x00236BB8` | 88 | `Pause__4goIO` |
| `0x00264C88` | 12 | `PushableWeight__C4goIO` |
| `0x00234DD8` | 132 | `Reset__4goIOUi` |
| `0x00236C20` | 752 | `Restore__4goIO` |
| `0x002363B8` | 208 | `SendProgressEvent__4goIOfff` |
| `0x00264C68` | 32 | `SetDamageInfo__4goIOfG6VUVec4Uii` |
| `0x00235808` | 76 | `SetMode_Falling__4goIO` |
| `0x00235888` | 24 | `SetMode_Floating__4goIO` |
| `0x00235858` | 24 | `SetMode_OnGround__4goIO` |
| `0x00235870` | 24 | `SetMode_Sliding__4goIO` |
| `0x002358A0` | 24 | `SetMode_Underwater__4goIO` |
| `0x002353B8` | 144 | `Unlock__4goIOf` |
| `0x00236C10` | 16 | `Unpause__4goIO` |
| `0x00234CC8` | 272 | `UpdateVarsFromTweakers__4goIO` |
| `0x00236F10` | 1096 | `Update__4goIOUi` |
| `0x00235648` | 136 | `__4goIOP8goClientPCQ22dc3tIOi` |
| `0x00234C80` | 72 | `__dl__4goIOPv` |

### `goDeadb00` — `0x0DC35336`

Função semântica: Variante de corpo/morto; sem classe direta, relacionada a death/freeze/passive pipeline.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

### `goSavePoint` — `0x0F2B776B`

Função semântica: Objeto de save point; no ELF há HFSM_SavePoint e código de beam/material.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00265FC0` | 216 | `FindBeamMat__11goSavePoint` |

### `tStandardEffectSystem` — `0x13AB20F1`

Função semântica: Sistema padrão de efeitos de morte/fade/dead particles para criaturas.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00224348` | 64 | `Allocate__21tStandardEffectSystemP10wadContext` |
| `0x00225258` | 268 | `DeathParticleCallback__21tStandardEffectSystemP9odbEffect` |
| `0x00225120` | 148 | `InitDead__21tStandardEffectSystemP19goCreatureCollision` |
| `0x002251B8` | 88 | `InitFadeOut__21tStandardEffectSystemP19goCreatureCollision` |
| `0x00264818` | 8 | `Init__21tStandardEffectSystemP19goCreatureCollision` |
| `0x00225368` | 660 | `UpdateS_Dead__21tStandardEffectSystemP19goCreatureCollision` |
| `0x00225600` | 32 | `UpdateS_FadeOut__21tStandardEffectSystemP19goCreatureCollision` |
| `0x00225210` | 68 | `UpdateS__21tStandardEffectSystemP19goCreatureCollision` |
| `0x00225118` | 8 | `Update__21tStandardEffectSystemP19goCreatureCollision` |
| `0x002599F8` | 48 | `_$_21tStandardEffectSystem` |
| `0x002250E0` | 56 | `__21tStandardEffectSystem` |
| `0x00224388` | 72 | `__dl__21tStandardEffectSystemPv` |

### `goOrdersKillBlood` — `0x1624F7AF`

Função semântica: Variante de efeito/entidade de sangue para Orders.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

### `tMoveSystem` — `0x16D04154`

Função semântica: Sistema de movimentos/combat branches, colisão, dano, quick block e reação.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x0022DFB0` | 1184 | `AlignSynchJoints__11tMoveSystem` |
| `0x00228D90` | 64 | `Allocate__11tMoveSystemP10wadContext` |
| `0x0022BB98` | 2264 | `ApplyHit__11tMoveSystemiP12goGameObjectP11tMoveSystemUsT2P10goCreatureT3PC8AtrValueUsPC15collisionOutputPCQ22dc10tCollision` |
| `0x0022C688` | 168 | `ApplyImpulse__11tMoveSystemG6VUVec4PCQ22dc8tImpulse` |
| `0x0022EF30` | 1488 | `AutoAim__11tMoveSystem` |
| `0x0022EC58` | 728 | `CalcAutoAimDir__11tMoveSystem` |
| `0x0022C540` | 328 | `CalcImpulse__11tMoveSystemG6VUVec4PCQ22dc8tImpulse` |
| `0x0022F890` | 1580 | `CheckBranchAgainstPad__C11tMoveSystemP5tMovePCQ22dc7tBranchPC6sysPad` |
| `0x0022FEC0` | 1844 | `CheckBranchCommon__C11tMoveSystemP5tMovePCQ22dc7tBranchPiiUiP10goCreatureiiiPCQ22dc11tMaterialFX` |
| `0x0022D6F0` | 228 | `Damage__11tMoveSystemfP10goCreatureiiPCQ22dc10tCollisionG6VUVec4UiUi` |
| `0x0022D7D8` | 1304 | `Damage__11tMoveSystemfP10goCreatureiiPCQ22dc11tMaterialFXG6VUVec4UiUi` |
| `0x0022EB28` | 128 | `DecScriptRefCnt__11tMoveSystemP8goScript` |
| `0x00231810` | 112 | `DoCancelAllMoves__11tMoveSystem` |
| `0x00231880` | 44 | `DoCancelMove__11tMoveSystemP5tMove` |
| `0x0022C470` | 208 | `DoCollisionReaction__11tMoveSystemUiP10goCreatureiiiPCQ22dc10tCollisionG6VUVec4` |
| `0x002317D8` | 56 | `EndMove__11tMoveSystemP5tMove` |
| `0x00231B40` | 48 | `FindBranch__11tMoveSystemUi` |
| `0x0022F7C0` | 208 | `GetCreatureBranchMask__11tMoveSystem` |
| `0x00264070` | 8 | `GetCreature__C11tMoveSystem` |
| `0x00231A28` | 48 | `GetDamageMultiplier__C11tMoveSystem` |
| `0x00264D58` | 8 | `GetMoveSystem__C10goIO_TandF` |
| `0x00264C10` | 8 | `GetMoveSystem__C4goIO` |
| `0x00264CD0` | 8 | `GetMoveSystem__C8goIO_CSM` |
| `0x0022EBA8` | 172 | `GetSimilarScriptInstance__C11tMoveSystemP8goScriptUi` |
| `0x0022F500` | 260 | `GetSynchCreature__C11tMoveSystem` |
| `0x0022F6C8` | 116 | `GetTargetContext__C11tMoveSystem` |
| `0x0022F608` | 192 | `GetTargetCreature__C11tMoveSystem` |
| `0x0022F788` | 52 | `GetTargetHealth__C11tMoveSystem` |
| `0x0022F740` | 68 | `GetTargetID__C11tMoveSystem` |
| `0x0022C730` | 4028 | `HandleCollision__11tMoveSystemP12goGameObjectPC8AtrValueUsT1iUsPC15collisionOutput` |
| `0x002318B0` | 376 | `HasActiveCollision__C11tMoveSystemiPf` |
| `0x0022EAC0` | 100 | `IncScriptRefCnt__11tMoveSystemP8goScript` |
| `0x00231A58` | 228 | `IsDoingMoveWithScriptAction__C11tMoveSystemPCc` |
| `0x0022B650` | 268 | `MoveCollisionCallback__11tMoveSystem` |
| `0x00264068` | 8 | `MoveTwk__C11tMoveSystem` |
| `0x00231560` | 132 | `PushBranch__11tMoveSystemPCQ22dc7tBranchf` |
| `0x0022B8C0` | 284 | `QuickBlockProjectile__11tMoveSystem` |
| `0x0022B760` | 348 | `QuickBlock__11tMoveSystemP12goGameObjectPCQ22dc5tMoveUs` |
| `0x0022B548` | 260 | `Reset__11tMoveSystem` |
| `0x0022DE80` | 300 | `SetAttachmentStatus__11tMoveSystemiiii` |
| `0x0022B9E0` | 436 | `SetupSyncMove__11tMoveSystemP11tMoveSystem` |
| `0x002315E8` | 492 | `StartMove__11tMoveSystemP5tMovePCQ22dc7tBranchi` |
| `0x0022DCF0` | 256 | `StaticInit__11tMoveSystem` |
| `0x0022DDF0` | 144 | `StaticUpdate__11tMoveSystemP8goScript` |
| `0x002313B8` | 296 | `SynchBranch__11tMoveSystemPCQ22dc7tBranch` |
| `0x00231360` | 84 | `TriggerBranch__11tMoveSystemPCQ22dc7tBranchi` |
| `0x00231318` | 72 | `TriggerBranch__11tMoveSystemUii` |
| `0x00231018` | 764 | `TriggerReactionBranches__11tMoveSystemiP10goCreatureiiiPCQ22dc11tMaterialFX` |
| `0x00230AB8` | 792 | `TriggerRootBranches__11tMoveSystemPC6sysPad` |
| `0x0022E450` | 1644 | `Update__11tMoveSystem` |
| `0x0022B400` | 328 | `_$_11tMoveSystem` |
| `0x0022B340` | 192 | `__11tMoveSystemP10goCreature` |
| `0x00228E18` | 1016 | `__5tMoveP11tMoveSystemPCQ22dc7tBranchUi` |
| `0x00228DD0` | 72 | `__dl__11tMoveSystemPv` |

### `hfsmEnemy1` — `0x1AF7BEE1`

Função semântica: HFSM genérica de inimigo; estados de target, wander, patrol, attack, frozen, prepdie etc.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x0024B3C0` | 64 | `Allocate__Q211HFSM_Enemy110hfsmEnemy1P10wadContext` |
| `0x00270D68` | 8 | `CallExitCode__Q311HFSM_Enemy110hfsmEnemy17SEnemy1P9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027BE38` | 48 | `CallExitCode__Q411HFSM_Enemy110hfsmEnemy17SEnemy110SDESTROYAIP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027BD18` | 48 | `CallExitCode__Q411HFSM_Enemy110hfsmEnemy17SEnemy15SSINKP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027B618` | 136 | `CallExitCode__Q411HFSM_Enemy110hfsmEnemy17SEnemy16SPAUSEP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027AAC8` | 164 | `CallExitCode__Q411HFSM_Enemy110hfsmEnemy17SEnemy16SStartP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027BAD0` | 48 | `CallExitCode__Q411HFSM_Enemy110hfsmEnemy17SEnemy17SFROZENP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027B890` | 48 | `CallExitCode__Q411HFSM_Enemy110hfsmEnemy17SEnemy18SPREPDIEP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x00281518` | 128 | `CallExitCode__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart12SInitialMoveP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x00281968` | 184 | `CallExitCode__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttackP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x002816E8` | 48 | `CallExitCode__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTargetP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x00282CF0` | 148 | `CallExitCode__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack7SSearchP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x00282B88` | 48 | `CallExitCode__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack7STargetP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x00282F50` | 124 | `CallExitCode__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack9SReaquireP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x00282710` | 48 | `CallExitCode__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget6SStandP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x00282868` | 124 | `CallExitCode__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget7SPatrolP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x002827A0` | 48 | `CallExitCode__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget7SWanderP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0024B020` | 72 | `Create__CQ211HFSM_Enemy111HFSMCreator` |
| `0x00282AA0` | 232 | `Dispatch__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttackR14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x00265A28` | 208 | `Dispatch__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTargetR14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x00281BF8` | 60 | `EntryCode__Q411HFSM_Enemy110hfsmEnemy17SEnemy110SDESTROYAIR14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x00281B98` | 92 | `EntryCode__Q411HFSM_Enemy110hfsmEnemy17SEnemy15SSINKR14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x00281B28` | 56 | `EntryCode__Q411HFSM_Enemy110hfsmEnemy17SEnemy16SPAUSER14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x00281B60` | 52 | `EntryCode__Q411HFSM_Enemy110hfsmEnemy17SEnemy18SPREPDIER14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x002659A8` | 128 | `EntryCode__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart12SInitialMoveR14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x00282A50` | 76 | `EntryCode__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttackR14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x00283228` | 156 | `EntryCode__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack7STargetR14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x002832C8` | 28 | `EntryCode__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack9SReaquireR14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x00271020` | 12 | `EntryCode__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget6SStandR14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x00271050` | 100 | `EntryCode__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget7SPatrolR14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x00271030` | 28 | `EntryCode__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget7SWanderR14bhvrHFSMClientR4goAIRC7goEvent` |
| `0x002658E8` | 8 | `GetDataSize__CQ211HFSM_Enemy111HFSMCreator` |
| `0x0024B080` | 52 | `GetDependantClass__CQ211HFSM_Enemy111HFSMCreatori` |
| `0x0024B068` | 12 | `GetName__CQ211HFSM_Enemy111HFSMCreator` |
| `0x0024B078` | 8 | `GetNumDependantClasses__CQ211HFSM_Enemy111HFSMCreator` |
| `0x0024B010` | 12 | `GetParam__11HFSM_Enemy1R14bhvrHFSMClient` |
| `0x0024B0B8` | 772 | `GetStartState__Q211HFSM_Enemy110hfsmEnemy1R14bhvrHFSMClientRC7goEvent` |
| `0x00270D70` | 584 | `ProcessEvent__Q311HFSM_Enemy110hfsmEnemy17SEnemy1R14bhvrHFSMClientRC7goEvent` |
| `0x0027BE68` | 96 | `ProcessEvent__Q411HFSM_Enemy110hfsmEnemy17SEnemy110SDESTROYAIR14bhvrHFSMClientRC7goEvent` |
| `0x0027BD48` | 236 | `ProcessEvent__Q411HFSM_Enemy110hfsmEnemy17SEnemy15SSINKR14bhvrHFSMClientRC7goEvent` |
| `0x0027B6A0` | 492 | `ProcessEvent__Q411HFSM_Enemy110hfsmEnemy17SEnemy16SPAUSER14bhvrHFSMClientRC7goEvent` |
| `0x0027AB70` | 2444 | `ProcessEvent__Q411HFSM_Enemy110hfsmEnemy17SEnemy16SStartR14bhvrHFSMClientRC7goEvent` |
| `0x0027BB00` | 536 | `ProcessEvent__Q411HFSM_Enemy110hfsmEnemy17SEnemy17SFROZENR14bhvrHFSMClientRC7goEvent` |
| `0x0027B8C0` | 524 | `ProcessEvent__Q411HFSM_Enemy110hfsmEnemy17SEnemy18SPREPDIER14bhvrHFSMClientRC7goEvent` |
| `0x00281598` | 332 | `ProcessEvent__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart12SInitialMoveR14bhvrHFSMClientRC7goEvent` |
| `0x00281A20` | 44 | `ProcessEvent__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttackR14bhvrHFSMClientRC7goEvent` |
| `0x00281718` | 372 | `ProcessEvent__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTargetR14bhvrHFSMClientRC7goEvent` |
| `0x00282D88` | 456 | `ProcessEvent__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack7SSearchR14bhvrHFSMClientRC7goEvent` |
| `0x00282BB8` | 308 | `ProcessEvent__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack7STargetR14bhvrHFSMClientRC7goEvent` |
| `0x00282FD0` | 596 | `ProcessEvent__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack9SReaquireR14bhvrHFSMClientRC7goEvent` |
| `0x00282740` | 96 | `ProcessEvent__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget6SStandR14bhvrHFSMClientRC7goEvent` |
| `0x002828E8` | 356 | `ProcessEvent__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget7SPatrolR14bhvrHFSMClientRC7goEvent` |
| `0x002827D0` | 152 | `ProcessEvent__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget7SWanderR14bhvrHFSMClientRC7goEvent` |
| `0x00259D30` | 84 | `_$_Q211HFSM_Enemy110hfsmEnemy1` |
| `0x002658F0` | 164 | `__Q211HFSM_Enemy110hfsmEnemy1` |
| `0x00256AD8` | 52 | `__Q211HFSM_Enemy111HFSMCreator` |
| `0x002711D8` | 68 | `__Q411HFSM_Enemy110hfsmEnemy17SEnemy110SDESTROYAIRQ311HFSM_Enemy110hfsmEnemy17SEnemy1` |
| `0x00271190` | 68 | `__Q411HFSM_Enemy110hfsmEnemy17SEnemy15SSINKRQ311HFSM_Enemy110hfsmEnemy17SEnemy1` |
| `0x002710B8` | 68 | `__Q411HFSM_Enemy110hfsmEnemy17SEnemy16SPAUSERQ311HFSM_Enemy110hfsmEnemy17SEnemy1` |
| `0x00270FB8` | 104 | `__Q411HFSM_Enemy110hfsmEnemy17SEnemy16SStartRQ311HFSM_Enemy110hfsmEnemy17SEnemy1` |
| `0x00271148` | 68 | `__Q411HFSM_Enemy110hfsmEnemy17SEnemy17SFROZENRQ311HFSM_Enemy110hfsmEnemy17SEnemy1` |
| `0x00271100` | 68 | `__Q411HFSM_Enemy110hfsmEnemy17SEnemy18SPREPDIERQ311HFSM_Enemy110hfsmEnemy17SEnemy1` |
| `0x0027B500` | 68 | `__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart12SInitialMoveRQ411HFSM_Enemy110hfsmEnemy17SEnemy16SStart` |
| `0x0027B5B0` | 104 | `__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttackRQ411HFSM_Enemy110hfsmEnemy17SEnemy16SStart` |
| `0x0027B548` | 104 | `__Q511HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTargetRQ411HFSM_Enemy110hfsmEnemy17SEnemy16SStart` |
| `0x00281A98` | 68 | `__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack7SSearchRQ511HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack` |
| `0x00281A50` | 68 | `__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack7STargetRQ511HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack` |
| `0x00281AE0` | 68 | `__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack9SReaquireRQ511HFSM_Enemy110hfsmEnemy17SEnemy16SStart7SAttack` |
| `0x00281890` | 68 | `__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget6SStandRQ511HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget` |
| `0x00281920` | 68 | `__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget7SPatrolRQ511HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget` |
| `0x002818D8` | 68 | `__Q611HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget7SWanderRQ511HFSM_Enemy110hfsmEnemy17SEnemy16SStart9SNoTarget` |
| `0x0024B400` | 72 | `__dl__Q211HFSM_Enemy110hfsmEnemy1Pv` |

### `tMove` — `0x1FF7A7C5`

Função semântica: Instância de move/branch acionável; usado pelo tMoveSystem para ações, colisões e scripts.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x0022ADA0` | 576 | `AddActionScript__5tMoveUiUiPCQ22dc11tActionBase` |
| `0x002294D8` | 48 | `AddToCollisionList__5tMoveG8goHandlePCQ22dc10tCollisioni` |
| `0x00228D08` | 64 | `Allocate__5tMoveP10wadContext` |
| `0x0022F890` | 1580 | `CheckBranchAgainstPad__C11tMoveSystemP5tMovePCQ22dc7tBranchPC6sysPad` |
| `0x0022FEC0` | 1844 | `CheckBranchCommon__C11tMoveSystemP5tMovePCQ22dc7tBranchPiiUiP10goCreatureiiiPCQ22dc11tMaterialFX` |
| `0x00231880` | 44 | `DoCancelMove__11tMoveSystemP5tMove` |
| `0x002298B8` | 5352 | `DoUpdateActions__5tMove` |
| `0x002317D8` | 56 | `EndMove__11tMoveSystemP5tMove` |
| `0x00231D10` | 504 | `GenericAim__FP8goScriptP5tMoveiff` |
| `0x002305F8` | 100 | `GetPriority__C5tMovePCQ22dc7tBranchi` |
| `0x00229660` | 504 | `GetValidCollision__5tMoveG8goHandleiPi` |
| `0x00229858` | 28 | `IncNumBlocks__5tMove` |
| `0x00229878` | 28 | `IncNumHits__5tMove` |
| `0x00229898` | 28 | `IncNumKills__5tMove` |
| `0x0022B068` | 80 | `IsActionScriptAlreadyRunning__5tMovePCQ22dc11tActionBase` |
| `0x002314E0` | 124 | `PushBranch__5tMovePCQ22dc7tBranchf` |
| `0x0022AFE0` | 132 | `RemoveActionScripts__5tMove` |
| `0x00229508` | 40 | `ResetCollisionList__5tMove` |
| `0x002315E8` | 492 | `StartMove__11tMoveSystemP5tMovePCQ22dc7tBranchi` |
| `0x00230660` | 1112 | `TriggerBranches__5tMovePC6sysPadf` |
| `0x00230DD0` | 580 | `TriggerReactionBranches__5tMoveiP10goCreatureiiiPCQ22dc11tMaterialFX` |
| `0x0022B0B8` | 648 | `Update__5tMove` |
| `0x00228E18` | 1016 | `__5tMoveP11tMoveSystemPCQ22dc7tBranchUi` |
| `0x00228D48` | 72 | `__dl__5tMovePv` |
| `0x00264058` | 16 | `__t8stdCList2ZP5tMoveZt12stdAllocator1Z16stdCListNodeBase` |

### `hfsmReactive` — `0x21503BD1`

Função semântica: HFSM reativo; sem símbolos diretos neste ELF, usado como pool de comportamento para entidades passivas/reativas.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

### `goOrders10` — `0x28B4028C`

Função semântica: Variante de inimigo Orders com quantidade 10; sem classe direta no ELF, tratado por goSoldier/hfsmEnemy1/tMove.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

### `odbEffect` — `0x28D4125E`

Função semântica: Objeto de efeito animado; cria efeitos por nome/parm, pode emitir por osso via fxBoneData.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00243B70` | 64 | `Allocate__9odbEffectP10wadContext` |
| `0x00244768` | 144 | `Callback__9odbEffectP9anmPlayer` |
| `0x00225258` | 268 | `DeathParticleCallback__21tStandardEffectSystemP9odbEffect` |
| `0x00244B48` | 104 | `GetBoneData__9odbEffect` |
| `0x002643B0` | 8 | `GetType__C9odbEffect` |
| `0x00244850` | 88 | `SetEmitterRateModulation__9odbEffectf` |
| `0x002447F8` | 88 | `SetEmitterVelocity__9odbEffectG6VUVec4` |
| `0x00244BB0` | 88 | `SetupAsBoneEmitter__9odbEffectP10fxBoneData` |
| `0x002448A8` | 668 | `SetupBoneData__9odbEffectP10goSkeletonP10fxBoneData` |
| `0x002443F0` | 532 | `Setup__9odbEffectPC10goParmBaseUiG6VUMat4P12goGameObjecti` |
| `0x00244608` | 136 | `Setup__9odbEffectPCcUiG6VUMat4P12goGameObjecti` |
| `0x00244690` | 204 | `StartAnimation__9odbEffect` |
| `0x00244C08` | 204 | `Update__9odbEffect` |
| `0x00244358` | 152 | `_$_9odbEffect` |
| `0x00243EB8` | 212 | `__9odbEffectPC10goParmBaseUi` |
| `0x00243F90` | 240 | `__9odbEffectPC10goParmBaseUiG6VUMat4P12goGameObjecti` |
| `0x00244170` | 488 | `__9odbEffectPC10goParmBaseUiG6VUVec4T3P12goGameObjecti` |
| `0x00243DE0` | 212 | `__9odbEffectPCcUi` |
| `0x00244080` | 240 | `__9odbEffectPCcUiG6VUMat4P12goGameObjecti` |
| `0x00243BF8` | 488 | `__9odbEffectPCcUiG6VUVec4T3P12goGameObjecti` |
| `0x00243BB0` | 72 | `__dl__9odbEffectPv` |

### `goGrapplePtInactive` — `0x2EE76396`

Função semântica: Ponto de grapple inativo/ativável; família goGrapplePoint tem Enable/Disable/Activate/Deactivate.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x0019A338` | 152 | `Activate__14goGrapplePoint` |
| `0x0020C168` | 64 | `Allocate__14tGrappleSystemP10wadContext` |
| `0x002632E0` | 8 | `CodeByte__C20Grapple_SaveListItem` |
| `0x001E7468` | 108 | `ConvertGrappleToPOR__9goSoldierG6VUVec4T1` |
| `0x001E73F8` | 108 | `ConvertPORToGrapple__9goSoldierG6VUVec4T1` |
| `0x00266498` | 196 | `CreateScriptWithArgs__t14goScriptHelper2Z14goGrapplePointZ18goGrapplePointArgsP8goScript` |
| `0x0019A3D0` | 120 | `Deactivate__14goGrapplePoint` |
| `0x00271CD8` | 56 | `DeleteScript__t14goScriptHelper2Z14goGrapplePointZ19goScriptDefaultArgsP8goScript` |
| `0x0019A1B8` | 384 | `Disable__14goGrapplePoint` |
| `0x001E74D8` | 3120 | `DoGrappleMotion__9goSoldierG6VUVec4T1f` |
| `0x0019A070` | 328 | `Enable__14goGrapplePoint` |
| `0x001FE648` | 1020 | `FindGrappleTarget__8goPlayer` |
| `0x0019A448` | 132 | `FindPoint__14goGrapplePointPCc` |
| `0x0019A7D0` | 336 | `GetGrappleDirFromID__14goGrapplePointi` |
| `0x0019A698` | 312 | `GetGrappleDirID__14goGrapplePointG6VUVec4i` |
| `0x0019A920` | 148 | `GetMatrix__C14goGrapplePoint` |
| `0x001E8BB0` | 1148 | `HandleJumpingDuringGrapple__9goSoldierG6VUVec4T1` |
| `0x001E8108` | 1620 | `InitGrappleMotion__9goSoldier` |
| `0x001D0E20` | 152 | `LoadGrappleState__7WadInfoP12goGameObject` |
| `0x002632E8` | 8 | `PayloadOffset__C20Grapple_SaveListItem` |
| `0x002632F0` | 8 | `PayloadSize__C20Grapple_SaveListItem` |
| `0x0019A9B8` | 40 | `RegisterGrapplePointScripts__Fv` |
| `0x0019A600` | 152 | `RemoveWadContext__14goGrapplePointP10wadContext` |
| `0x0019A4D8` | 292 | `Remove__14goGrapplePoint` |
| `0x002652F8` | 8 | `Reset__14tGrappleSystem` |
| `0x001E8760` | 928 | `ResolvePhysicalOrientationDuringGrapple__9goSoldierG6VUVec4f` |
| `0x00271D10` | 32 | `RunScript__t14goScriptHelper2Z14goGrapplePointZ19goScriptDefaultArgsP8goScript` |
| `0x00199FB8` | 180 | `Run__14goGrapplePointP8goScript` |
| `0x001D0EB8` | 312 | `SaveGrappleState__7WadInfoP12goGameObjecti` |
| `0x001E8B00` | 176 | `SetMode_Grapple__9goSoldier` |
| `0x00199E48` | 136 | `StaticInit__14goGrapplePoint` |
| `0x00199F90` | 36 | `_$_14goGrapplePoint` |
| `0x002652C8` | 48 | `_$_14tGrappleSystem` |
| `0x002632B0` | 48 | `_$_20Grapple_SaveListItem` |
| `0x00199ED0` | 192 | `__14goGrapplePointP8goScriptP18goGrapplePointArgs` |
| `0x0020C1A8` | 72 | `__dl__14tGrappleSystemPv` |
| `0x001C0650` | 40 | `__dl__20Grapple_SaveListItemPv` |
| `0x001C0628` | 36 | `__nw__20Grapple_SaveListItemUi` |
| `0x00256698` | 16 | `__t8stdCList2ZP14goGrapplePointZt12stdAllocator1Z16stdCListNodeBase` |

### `hfsmSavePoint` — `0x2F65837B`

Função semântica: HFSM do save point; controla estado inicial e eventos da estação de save.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x0024D150` | 64 | `Allocate__Q214HFSM_SavePoint13hfsmSavePointP10wadContext` |
| `0x00271A78` | 8 | `CallExitCode__Q314HFSM_SavePoint13hfsmSavePoint10SSavePointP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027D910` | 48 | `CallExitCode__Q414HFSM_SavePoint13hfsmSavePoint10SSavePoint8SInitialP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0024D030` | 72 | `Create__CQ214HFSM_SavePoint11HFSMCreator` |
| `0x00266098` | 8 | `GetDataSize__CQ214HFSM_SavePoint11HFSMCreator` |
| `0x0024D090` | 52 | `GetDependantClass__CQ214HFSM_SavePoint11HFSMCreatori` |
| `0x0024D078` | 12 | `GetName__CQ214HFSM_SavePoint11HFSMCreator` |
| `0x0024D088` | 8 | `GetNumDependantClasses__CQ214HFSM_SavePoint11HFSMCreator` |
| `0x0024D020` | 12 | `GetParam__14HFSM_SavePointR14bhvrHFSMClient` |
| `0x0024D0C8` | 136 | `GetStartState__Q214HFSM_SavePoint13hfsmSavePointR14bhvrHFSMClientRC7goEvent` |
| `0x00271A80` | 8 | `ProcessEvent__Q314HFSM_SavePoint13hfsmSavePoint10SSavePointR14bhvrHFSMClientRC7goEvent` |
| `0x0027D940` | 184 | `ProcessEvent__Q414HFSM_SavePoint13hfsmSavePoint10SSavePoint8SInitialR14bhvrHFSMClientRC7goEvent` |
| `0x00259FE8` | 48 | `_$_Q214HFSM_SavePoint13hfsmSavePoint` |
| `0x00256C28` | 52 | `__Q214HFSM_SavePoint11HFSMCreator` |
| `0x002660A0` | 96 | `__Q214HFSM_SavePoint13hfsmSavePoint` |
| `0x00271A88` | 68 | `__Q414HFSM_SavePoint13hfsmSavePoint10SSavePoint8SInitialRQ314HFSM_SavePoint13hfsmSavePoint10SSavePoint` |
| `0x0024D190` | 72 | `__dl__Q214HFSM_SavePoint13hfsmSavePointPv` |

### `tFightSystem` — `0x43DF450B`

Função semântica: Sistema de combate associado ao goCreature/goSoldier.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00226478` | 64 | `Allocate__12tFightSystemP10wadContext` |
| `0x00226668` | 48 | `CanAddProximityPressure__12tFightSystemUs` |
| `0x00264BD0` | 12 | `Frozen__C12tFightSystem` |
| `0x00264D70` | 8 | `GetFightSystem__10goIO_TandF` |
| `0x00264C28` | 8 | `GetFightSystem__4goIO` |
| `0x00264CE8` | 8 | `GetFightSystem__8goIO_CSM` |
| `0x00226698` | 284 | `Invulnerable__C12tFightSystem` |
| `0x00226628` | 32 | `ResetFightWeightLimit__12tFightSystem` |
| `0x00226648` | 32 | `ResetProjectileWeightLimit__12tFightSystem` |
| `0x002265A8` | 128 | `Reset__12tFightSystem` |
| `0x00226548` | 92 | `SetFight__12tFightSystemP5Fight` |
| `0x00264B78` | 84 | `_$_12tFightSystem` |
| `0x00226500` | 68 | `__12tFightSystemP10goCreature` |
| `0x002264B8` | 72 | `__dl__12tFightSystemPv` |

### `goStoneOrders10` — `0x4F07ABC1`

Função semântica: Variante petrificada de Orders; sem classe direta, usa sistemas de soldado/freeze/stone.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x001B12C0` | 60 | `RegisterTurnToStoneScripts__Fv` |

### `goCSGHoldMilk1` — `0x59B8EC08`

Função semântica: Objeto/prop de cutscene ou carregável específico do mapa; sem função direta no ELF.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

### `goCSGHoldMilk2` — `0x59B8EC09`

Função semântica: Objeto/prop de cutscene ou carregável específico do mapa; sem função direta no ELF.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

### `goSpawnHole` — `0x5B254A33`

Função semântica: Spawner/portal de inimigos; ligado a estatística EnemySpawns e lógica de spawn.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x001BC5C8` | 116 | `CanSpawn__FP8goAIBaseP10goCreaturePCQ22dc10tDTreeNode` |
| `0x001AFDF8` | 720 | `SCR_Spawn__FP8goScript` |

### `hfsmIO_Misc` — `0x5B57154D`

Função semântica: HFSM genérico para IO misc; estados Initial/Done.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x0024B950` | 64 | `Allocate__Q212HFSM_IO_Misc11hfsmIO_MiscP10wadContext` |
| `0x002715F8` | 8 | `CallExitCode__Q312HFSM_IO_Misc11hfsmIO_Misc8SIO_MiscP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027C988` | 48 | `CallExitCode__Q412HFSM_IO_Misc11hfsmIO_Misc8SIO_Misc5SDoneP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027C6F0` | 48 | `CallExitCode__Q412HFSM_IO_Misc11hfsmIO_Misc8SIO_Misc8SInitialP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0024B7A8` | 72 | `Create__CQ212HFSM_IO_Misc11HFSMCreator` |
| `0x00282628` | 28 | `EntryCode__Q412HFSM_IO_Misc11hfsmIO_Misc8SIO_Misc5SDoneR14bhvrHFSMClientR7IOInterRC7goEvent` |
| `0x00265D30` | 8 | `GetDataSize__CQ212HFSM_IO_Misc11HFSMCreator` |
| `0x0024B808` | 72 | `GetDependantClass__CQ212HFSM_IO_Misc11HFSMCreatori` |
| `0x0024B7F0` | 12 | `GetName__CQ212HFSM_IO_Misc11HFSMCreator` |
| `0x0024B800` | 8 | `GetNumDependantClasses__CQ212HFSM_IO_Misc11HFSMCreator` |
| `0x0024B798` | 12 | `GetParam__12HFSM_IO_MiscR14bhvrHFSMClient` |
| `0x0024B850` | 252 | `GetStartState__Q212HFSM_IO_Misc11hfsmIO_MiscR14bhvrHFSMClientRC7goEvent` |
| `0x00271600` | 8 | `ProcessEvent__Q312HFSM_IO_Misc11hfsmIO_Misc8SIO_MiscR14bhvrHFSMClientRC7goEvent` |
| `0x0027C9B8` | 344 | `ProcessEvent__Q412HFSM_IO_Misc11hfsmIO_Misc8SIO_Misc5SDoneR14bhvrHFSMClientRC7goEvent` |
| `0x0027C720` | 616 | `ProcessEvent__Q412HFSM_IO_Misc11hfsmIO_Misc8SIO_Misc8SInitialR14bhvrHFSMClientRC7goEvent` |
| `0x00259DE0` | 140 | `_$_Q212HFSM_IO_Misc11hfsmIO_Misc` |
| `0x00256B48` | 52 | `__Q212HFSM_IO_Misc11HFSMCreator` |
| `0x00265D38` | 136 | `__Q212HFSM_IO_Misc11hfsmIO_Misc` |
| `0x00271650` | 68 | `__Q412HFSM_IO_Misc11hfsmIO_Misc8SIO_Misc5SDoneRQ312HFSM_IO_Misc11hfsmIO_Misc8SIO_Misc` |
| `0x00271608` | 68 | `__Q412HFSM_IO_Misc11hfsmIO_Misc8SIO_Misc8SInitialRQ312HFSM_IO_Misc11hfsmIO_Misc8SIO_Misc` |
| `0x0024B990` | 72 | `__dl__Q212HFSM_IO_Misc11hfsmIO_MiscPv` |

### `tAnimSystem` — `0x63B709F4`

Função semântica: Sistema de animação por criatura/objeto; play, blend, slave anim clients e callbacks.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x0021EDB0` | 116 | `AddSlave__11tAnimSystemP9anmClient` |
| `0x0021DCA0` | 64 | `Allocate__11tAnimSystemP10wadContext` |
| `0x0021E9B8` | 8 | `Callback__11tAnimSystemP9anmPlayer` |
| `0x00264D50` | 8 | `GetAnimSystem__C10goIO_TandF` |
| `0x00264878` | 8 | `GetAnimSystem__C11goPegasusAI` |
| `0x00264C08` | 8 | `GetAnimSystem__C4goIO` |
| `0x00264CC8` | 8 | `GetAnimSystem__C8goIO_CSM` |
| `0x00264660` | 8 | `GetAnimSystem__C9goPassive` |
| `0x00263A18` | 8 | `GetAnimSystem__C9goSoldier` |
| `0x0021ED08` | 168 | `GetBankIdxFromName__11tAnimSystemUi` |
| `0x0021E270` | 80 | `GetFileIdx__11tAnimSystemP9anmClientUi` |
| `0x0021E328` | 776 | `PlayAnimFromFileIdx__11tAnimSystemUifff` |
| `0x0021E630` | 88 | `PlayAnim__11tAnimSystemUi` |
| `0x0021E2C0` | 100 | `PlayAnim__11tAnimSystemUifff` |
| `0x0021E998` | 28 | `ProcessCallback__11tAnimSystemP9anmClient` |
| `0x0021EE28` | 120 | `RemoveSlave__11tAnimSystemP9anmClient` |
| `0x0021EC30` | 212 | `Reset__11tAnimSystem` |
| `0x0021DD28` | 172 | `SetAnimPos__11tAnimSystemf` |
| `0x0021DDD8` | 132 | `SetAnimTimeScale__11tAnimSystemf` |
| `0x0021DEF0` | 892 | `UpdateAnim__11tAnimSystemffffffffi` |
| `0x0021DE98` | 88 | `UpdateBlendSets__11tAnimSystem` |
| `0x0021E688` | 784 | `UpdateZeroJoint__11tAnimSystem` |
| `0x0021EB70` | 188 | `_$_11tAnimSystem` |
| `0x0021E9C0` | 432 | `__11tAnimSystemP10goCreatureUiiPC20tStaticAnimDescEntryPCc` |
| `0x0021DCE0` | 72 | `__dl__11tAnimSystemPv` |

### `goRdogFoam` — `0x63BF8887`

Função semântica: Efeito/variante de foam do Rabdog.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

### `tHandleSystem` — `0x672501CB`

Função semântica: Sistema runtime de handles/joints agarráveis; inicializa joints de handle a partir do esqueleto e ranges.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00226DB8` | 64 | `Allocate__13tHandleSystemP10wadContext` |
| `0x00264D68` | 8 | `GetHandleSystem__10goIO_TandF` |
| `0x00264880` | 8 | `GetHandleSystem__11goPegasusAI` |
| `0x00264C20` | 8 | `GetHandleSystem__4goIO` |
| `0x00264CE0` | 8 | `GetHandleSystem__8goIO_CSM` |
| `0x00264670` | 8 | `GetHandleSystem__9goPassive` |
| `0x00263A28` | 8 | `GetHandleSystem__9goSoldier` |
| `0x00226E40` | 244 | `Init__13tHandleSystemP10goSkeleton` |
| `0x00265298` | 48 | `_$_13tHandleSystem` |
| `0x00226DF8` | 72 | `__dl__13tHandleSystemPv` |

### `goRabdog00` — `0x6DA783F1`

Função semântica: Variante Rabdog; sem classe direta, usa pools de soldado/AI/move.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

### `goDeathParts` — `0x71954EB4`

Função semântica: Partes/efeitos de morte; sem classe direta, relacionado a StandardEffectSystem/odbEffect.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

### `fxBoneData` — `0x7901DD00`

Função semântica: Dados auxiliares para emissão de efeito por osso/joint.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00244BB0` | 88 | `SetupAsBoneEmitter__9odbEffectP10fxBoneData` |
| `0x002448A8` | 668 | `SetupBoneData__9odbEffectP10goSkeletonP10fxBoneData` |

### `goOrderSASwipe` — `0x7BBEAE06`

Função semântica: Variante/ataque swipe de Orders.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

### `goPassiveAI` — `0x8137C547`

Função semântica: AI/controller passivo; no sample só há pool pequeno, normalmente acoplado a goPassive/hfsmReactive.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00246FC0` | 64 | `Allocate__9goPassiveP10wadContext` |
| `0x0024ADB0` | 64 | `Allocate__Q212HFSM_Passive11hfsmPassiveP10wadContext` |
| `0x00247E30` | 324 | `BreakFrozenIncarnation__9goPassivei` |
| `0x00270978` | 8 | `CallExitCode__Q312HFSM_Passive11hfsmPassive8SPassiveP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x00279ED8` | 48 | `CallExitCode__Q412HFSM_Passive11hfsmPassive8SPassive11SInitializeP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x00279FB8` | 48 | `CallExitCode__Q412HFSM_Passive11hfsmPassive8SPassive5SIdleP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027A2C8` | 48 | `CallExitCode__Q412HFSM_Passive11hfsmPassive8SPassive6SDyingP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027A140` | 48 | `CallExitCode__Q412HFSM_Passive11hfsmPassive8SPassive7SFrozenP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027A3D8` | 48 | `CallExitCode__Q412HFSM_Passive11hfsmPassive8SPassive8SDestroyP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x002646B8` | 44 | `CanDoCombatMove__C9goPassive` |
| `0x00247C80` | 428 | `CreateFrozenIncarnation__9goPassive` |
| `0x0024AC30` | 72 | `Create__CQ212HFSM_Passive11HFSMCreator` |
| `0x00264708` | 12 | `DefyLightning__C9goPassive` |
| `0x00264628` | 8 | `DerivedTweaks__C9goPassive` |
| `0x0024A810` | 88 | `Destroy__19goPassiveController` |
| `0x00247730` | 44 | `Disable__9goPassive` |
| `0x00247760` | 48 | `Enable__9goPassive` |
| `0x0024A9F0` | 408 | `EnterState__19goPassiveControllerQ219goPassiveController6eStatePCv` |
| `0x00281270` | 116 | `EntryCode__Q412HFSM_Passive11hfsmPassive8SPassive6SDyingR14bhvrHFSMClientR19goPassiveControllerRC7goEvent` |
| `0x002812E8` | 60 | `EntryCode__Q412HFSM_Passive11hfsmPassive8SPassive8SDestroyR14bhvrHFSMClientR19goPassiveControllerRC7goEvent` |
| `0x0024AB88` | 56 | `ExitCurrentState__19goPassiveControllerQ219goPassiveController6eState` |
| `0x0024ABD0` | 80 | `ForceMove__19goPassiveControllerUii` |
| `0x00264660` | 8 | `GetAnimSystem__C9goPassive` |
| `0x00264668` | 8 | `GetAttachmentSystem__9goPassive` |
| `0x00264BE0` | 8 | `GetControlSystem__19goPassiveController` |
| `0x00264658` | 8 | `GetCreatureControls__9goPassive` |
| `0x002657A0` | 8 | `GetDataSize__CQ212HFSM_Passive11HFSMCreator` |
| `0x0024AC90` | 52 | `GetDependantClass__CQ212HFSM_Passive11HFSMCreatori` |
| `0x00264678` | 8 | `GetEffectSystem__9goPassive` |
| `0x00264760` | 16 | `GetFreezeTimeLeft__C9goPassivef` |
| `0x00264748` | 20 | `GetFrozenRatio__C9goPassive` |
| `0x00264630` | 20 | `GetGOName__C9goPassive` |
| `0x00264670` | 8 | `GetHandleSystem__9goPassive` |
| `0x00264690` | 12 | `GetHeight__C9goPassive` |
| `0x00264680` | 12 | `GetMass__C9goPassive` |
| `0x0024AC78` | 12 | `GetName__CQ212HFSM_Passive11HFSMCreator` |
| `0x00247AF8` | 172 | `GetNavBranch__C9goPassivePCQ22dc8tNavBanki` |
| `0x00247BA8` | 216 | `GetNavBranch__C9goPassivePCt8stdStack2ZSci3i` |
| `0x0024AC88` | 8 | `GetNumDependantClasses__CQ212HFSM_Passive11HFSMCreator` |
| `0x0024AC20` | 12 | `GetParam__12HFSM_PassiveR14bhvrHFSMClient` |
| `0x002646A0` | 12 | `GetRadius__C9goPassive` |
| `0x0024ACC8` | 232 | `GetStartState__Q212HFSM_Passive11hfsmPassiveR14bhvrHFSMClientRC7goEvent` |
| `0x00248708` | 312 | `GroundCheck_Water__9goPassiveG6VUVec4` |
| `0x00248DF8` | 1368 | `HandleCollision_Complete__9goPassiveG6VUVec4f` |
| `0x00248840` | 212 | `HandleCollision_Water__9goPassiveG6VUVec4` |
| `0x00248918` | 1248 | `HandleCollision_World__9goPassiveG6VUVec4iiif` |
| `0x0024AE38` | 32 | `InitHFSMPassiveCreator__Fv` |
| `0x0024A670` | 320 | `Init__19goPassiveControllerP8goClientUis` |
| `0x00247510` | 540 | `Init__9goPassiveP8goClientPCQ22dc9tCreature` |
| `0x00264770` | 52 | `IsFreezable__C9goPassive` |
| `0x00264718` | 44 | `IsFrozen__C9goPassive` |
| `0x0024A990` | 96 | `IsStateTerminating__19goPassiveControllerQ219goPassiveController6eState` |
| `0x00270980` | 272 | `ProcessEvent__Q312HFSM_Passive11hfsmPassive8SPassiveR14bhvrHFSMClientRC7goEvent` |
| `0x00279F08` | 172 | `ProcessEvent__Q412HFSM_Passive11hfsmPassive8SPassive11SInitializeR14bhvrHFSMClientRC7goEvent` |
| `0x00279FE8` | 344 | `ProcessEvent__Q412HFSM_Passive11hfsmPassive8SPassive5SIdleR14bhvrHFSMClientRC7goEvent` |
| `0x0027A2F8` | 220 | `ProcessEvent__Q412HFSM_Passive11hfsmPassive8SPassive6SDyingR14bhvrHFSMClientRC7goEvent` |
| `0x0027A170` | 344 | `ProcessEvent__Q412HFSM_Passive11hfsmPassive8SPassive7SFrozenR14bhvrHFSMClientRC7goEvent` |
| `0x0027A408` | 96 | `ProcessEvent__Q412HFSM_Passive11hfsmPassive8SPassive8SDestroyR14bhvrHFSMClientRC7goEvent` |
| `0x0024ABC8` | 8 | `QuickBlockProjTest__19goPassiveController` |
| `0x0024ABC0` | 8 | `QuickBlockTest__19goPassiveControllerP12goGameObjectPCQ22dc5tMoveUs` |
| `0x002471A8` | 188 | `RemoveWadContext__9goPassiveP10wadContext` |
| `0x002474D8` | 56 | `ResetStatue__9goPassive` |
| `0x0024A500` | 160 | `ResetTweaks__19goPassiveController` |
| `0x00247370` | 360 | `Reset__9goPassiveUi` |
| `0x00249C08` | 892 | `ResolveMovement_CombatSystem__9goPassivef` |
| `0x00249F88` | 464 | `ResolveMovement_Conveyor__9goPassivef` |
| `0x00249948` | 700 | `ResolveMovement_DoMotion__9goPassiveG6VUVec4N21Rif` |
| `0x0024A288` | 556 | `ResolveMovement_Other__9goPassiveG6VUVec4T1f` |
| `0x0024A158` | 300 | `ResolveMovement_PhysicsDriven__9goPassiveG6VUVec4T1f` |
| `0x00265438` | 872 | `ResolveMovement__9goPassivef` |
| `0x00247AC8` | 48 | `SetDamageInfo__9goPassivefG6VUVec4Uii` |
| `0x00247180` | 40 | `SetInstanceVariations__9goPassiveffUi` |
| `0x00249350` | 364 | `SetMovement_Falling__9goPassiveUif` |
| `0x00249600` | 444 | `SetMovement_OnGround_Landing__9goPassiveG6VUVec4` |
| `0x002494C0` | 320 | `SetMovement_OnGround__9goPassive` |
| `0x002497C0` | 388 | `SetMovement_Sliding__9goPassive` |
| `0x00247268` | 8 | `SetSaveData__9goPassiveUc` |
| `0x0024A4B8` | 68 | `SetTweaks__19goPassiveControllerPCQ22dc9tCreature` |
| `0x00247048` | 120 | `SetTweaks__9goPassivePCQ22dc9tCreature` |
| `0x0024A868` | 292 | `SwitchToState__19goPassiveControllerQ219goPassiveController6eStatePCv` |
| `0x00248068` | 240 | `UpdateSH_Dead__9goPassive` |
| `0x00247FF8` | 108 | `UpdateSH_Frozen__9goPassivef` |
| `0x00247F78` | 124 | `UpdateSH_PostProcess__9goPassive` |
| `0x00248518` | 496 | `UpdateSH_ProcessDamage__9goPassivef` |
| `0x00248158` | 956 | `UpdateSH_ProcessFreezeBeam__9goPassive` |
| `0x002470C0` | 192 | `UpdateVarsFromTweakers__9goPassive` |
| `0x0024A7B0` | 92 | `Update__19goPassiveControllerUi` |
| `0x00247790` | 820 | `Update__9goPassiveUi` |
| `0x0024A608` | 100 | `_$_19goPassiveController` |
| `0x002472B8` | 180 | `_$_9goPassive` |
| `0x00259C80` | 84 | `_$_Q212HFSM_Passive11hfsmPassive` |
| `0x0024A5A0` | 104 | `__19goPassiveController` |
| `0x00247270` | 68 | `__9goPassiveP8goClient` |
| `0x00256A68` | 52 | `__Q212HFSM_Passive11HFSMCreator` |
| `0x002657A8` | 152 | `__Q212HFSM_Passive11hfsmPassive` |
| `0x00270A90` | 68 | `__Q412HFSM_Passive11hfsmPassive8SPassive11SInitializeRQ312HFSM_Passive11hfsmPassive8SPassive` |
| `0x00270AD8` | 68 | `__Q412HFSM_Passive11hfsmPassive8SPassive5SIdleRQ312HFSM_Passive11hfsmPassive8SPassive` |
| `0x00270B68` | 68 | `__Q412HFSM_Passive11hfsmPassive8SPassive6SDyingRQ312HFSM_Passive11hfsmPassive8SPassive` |
| `0x00270B20` | 68 | `__Q412HFSM_Passive11hfsmPassive8SPassive7SFrozenRQ312HFSM_Passive11hfsmPassive8SPassive` |
| `0x00270BB0` | 68 | `__Q412HFSM_Passive11hfsmPassive8SPassive8SDestroyRQ312HFSM_Passive11hfsmPassive8SPassive` |
| `0x00247000` | 72 | `__dl__9goPassivePv` |
| `0x0024ADF0` | 72 | `__dl__Q212HFSM_Passive11hfsmPassivePv` |

### `goPassive` — `0x83EBA3BF`

Função semântica: Criatura/passivo com física, freeze/death/incarnation, navegação e colisão.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00246FC0` | 64 | `Allocate__9goPassiveP10wadContext` |
| `0x00247E30` | 324 | `BreakFrozenIncarnation__9goPassivei` |
| `0x002646B8` | 44 | `CanDoCombatMove__C9goPassive` |
| `0x00247C80` | 428 | `CreateFrozenIncarnation__9goPassive` |
| `0x00264708` | 12 | `DefyLightning__C9goPassive` |
| `0x00264628` | 8 | `DerivedTweaks__C9goPassive` |
| `0x0024A810` | 88 | `Destroy__19goPassiveController` |
| `0x00247730` | 44 | `Disable__9goPassive` |
| `0x00247760` | 48 | `Enable__9goPassive` |
| `0x0024A9F0` | 408 | `EnterState__19goPassiveControllerQ219goPassiveController6eStatePCv` |
| `0x00281270` | 116 | `EntryCode__Q412HFSM_Passive11hfsmPassive8SPassive6SDyingR14bhvrHFSMClientR19goPassiveControllerRC7goEvent` |
| `0x002812E8` | 60 | `EntryCode__Q412HFSM_Passive11hfsmPassive8SPassive8SDestroyR14bhvrHFSMClientR19goPassiveControllerRC7goEvent` |
| `0x0024AB88` | 56 | `ExitCurrentState__19goPassiveControllerQ219goPassiveController6eState` |
| `0x0024ABD0` | 80 | `ForceMove__19goPassiveControllerUii` |
| `0x00264660` | 8 | `GetAnimSystem__C9goPassive` |
| `0x00264668` | 8 | `GetAttachmentSystem__9goPassive` |
| `0x00264BE0` | 8 | `GetControlSystem__19goPassiveController` |
| `0x00264658` | 8 | `GetCreatureControls__9goPassive` |
| `0x00264678` | 8 | `GetEffectSystem__9goPassive` |
| `0x00264760` | 16 | `GetFreezeTimeLeft__C9goPassivef` |
| `0x00264748` | 20 | `GetFrozenRatio__C9goPassive` |
| `0x00264630` | 20 | `GetGOName__C9goPassive` |
| `0x00264670` | 8 | `GetHandleSystem__9goPassive` |
| `0x00264690` | 12 | `GetHeight__C9goPassive` |
| `0x00264680` | 12 | `GetMass__C9goPassive` |
| `0x00247AF8` | 172 | `GetNavBranch__C9goPassivePCQ22dc8tNavBanki` |
| `0x00247BA8` | 216 | `GetNavBranch__C9goPassivePCt8stdStack2ZSci3i` |
| `0x002646A0` | 12 | `GetRadius__C9goPassive` |
| `0x00248708` | 312 | `GroundCheck_Water__9goPassiveG6VUVec4` |
| `0x00248DF8` | 1368 | `HandleCollision_Complete__9goPassiveG6VUVec4f` |
| `0x00248840` | 212 | `HandleCollision_Water__9goPassiveG6VUVec4` |
| `0x00248918` | 1248 | `HandleCollision_World__9goPassiveG6VUVec4iiif` |
| `0x0024A670` | 320 | `Init__19goPassiveControllerP8goClientUis` |
| `0x00247510` | 540 | `Init__9goPassiveP8goClientPCQ22dc9tCreature` |
| `0x00264770` | 52 | `IsFreezable__C9goPassive` |
| `0x00264718` | 44 | `IsFrozen__C9goPassive` |
| `0x0024A990` | 96 | `IsStateTerminating__19goPassiveControllerQ219goPassiveController6eState` |
| `0x0024ABC8` | 8 | `QuickBlockProjTest__19goPassiveController` |
| `0x0024ABC0` | 8 | `QuickBlockTest__19goPassiveControllerP12goGameObjectPCQ22dc5tMoveUs` |
| `0x002471A8` | 188 | `RemoveWadContext__9goPassiveP10wadContext` |
| `0x002474D8` | 56 | `ResetStatue__9goPassive` |
| `0x0024A500` | 160 | `ResetTweaks__19goPassiveController` |
| `0x00247370` | 360 | `Reset__9goPassiveUi` |
| `0x00249C08` | 892 | `ResolveMovement_CombatSystem__9goPassivef` |
| `0x00249F88` | 464 | `ResolveMovement_Conveyor__9goPassivef` |
| `0x00249948` | 700 | `ResolveMovement_DoMotion__9goPassiveG6VUVec4N21Rif` |
| `0x0024A288` | 556 | `ResolveMovement_Other__9goPassiveG6VUVec4T1f` |
| `0x0024A158` | 300 | `ResolveMovement_PhysicsDriven__9goPassiveG6VUVec4T1f` |
| `0x00265438` | 872 | `ResolveMovement__9goPassivef` |
| `0x00247AC8` | 48 | `SetDamageInfo__9goPassivefG6VUVec4Uii` |
| `0x00247180` | 40 | `SetInstanceVariations__9goPassiveffUi` |
| `0x00249350` | 364 | `SetMovement_Falling__9goPassiveUif` |
| `0x00249600` | 444 | `SetMovement_OnGround_Landing__9goPassiveG6VUVec4` |
| `0x002494C0` | 320 | `SetMovement_OnGround__9goPassive` |
| `0x002497C0` | 388 | `SetMovement_Sliding__9goPassive` |
| `0x00247268` | 8 | `SetSaveData__9goPassiveUc` |
| `0x0024A4B8` | 68 | `SetTweaks__19goPassiveControllerPCQ22dc9tCreature` |
| `0x00247048` | 120 | `SetTweaks__9goPassivePCQ22dc9tCreature` |
| `0x0024A868` | 292 | `SwitchToState__19goPassiveControllerQ219goPassiveController6eStatePCv` |
| `0x00248068` | 240 | `UpdateSH_Dead__9goPassive` |
| `0x00247FF8` | 108 | `UpdateSH_Frozen__9goPassivef` |
| `0x00247F78` | 124 | `UpdateSH_PostProcess__9goPassive` |
| `0x00248518` | 496 | `UpdateSH_ProcessDamage__9goPassivef` |
| `0x00248158` | 956 | `UpdateSH_ProcessFreezeBeam__9goPassive` |
| `0x002470C0` | 192 | `UpdateVarsFromTweakers__9goPassive` |
| `0x0024A7B0` | 92 | `Update__19goPassiveControllerUi` |
| `0x00247790` | 820 | `Update__9goPassiveUi` |
| `0x0024A608` | 100 | `_$_19goPassiveController` |
| `0x002472B8` | 180 | `_$_9goPassive` |
| `0x0024A5A0` | 104 | `__19goPassiveController` |
| `0x00247270` | 68 | `__9goPassiveP8goClient` |
| `0x00247000` | 72 | `__dl__9goPassivePv` |

### `hfsmBreakable` — `0x860166C1`

Função semântica: HFSM de quebráveis; estados START, Initial, Idle, Smash e Done.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x0024BFA8` | 64 | `Allocate__Q214HFSM_Breakable13hfsmBreakableP10wadContext` |
| `0x002718D8` | 8 | `CallExitCode__Q314HFSM_Breakable13hfsmBreakable10SBreakableP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027D828` | 48 | `CallExitCode__Q414HFSM_Breakable13hfsmBreakable10SBreakable5SDoneP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027D4F0` | 48 | `CallExitCode__Q414HFSM_Breakable13hfsmBreakable10SBreakable5SIdleP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027D2B0` | 48 | `CallExitCode__Q414HFSM_Breakable13hfsmBreakable10SBreakable6SSTARTP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027D720` | 48 | `CallExitCode__Q414HFSM_Breakable13hfsmBreakable10SBreakable6SSmashP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027D3B8` | 48 | `CallExitCode__Q414HFSM_Breakable13hfsmBreakable10SBreakable8SInitialP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0024BF08` | 72 | `Create__CQ214HFSM_Breakable11HFSMCreator` |
| `0x002826F0` | 28 | `EntryCode__Q414HFSM_Breakable13hfsmBreakable10SBreakable5SDoneR14bhvrHFSMClientR11goBreakableRC7goEvent` |
| `0x002826D0` | 28 | `EntryCode__Q414HFSM_Breakable13hfsmBreakable10SBreakable6SSmashR14bhvrHFSMClientR11goBreakableRC7goEvent` |
| `0x00282688` | 68 | `EntryCode__Q414HFSM_Breakable13hfsmBreakable10SBreakable8SInitialR14bhvrHFSMClientR11goBreakableRC7goEvent` |
| `0x00265F10` | 8 | `GetDataSize__CQ214HFSM_Breakable11HFSMCreator` |
| `0x0024BF68` | 52 | `GetDependantClass__CQ214HFSM_Breakable11HFSMCreatori` |
| `0x0024BF50` | 12 | `GetName__CQ214HFSM_Breakable11HFSMCreator` |
| `0x0024BF60` | 8 | `GetNumDependantClasses__CQ214HFSM_Breakable11HFSMCreator` |
| `0x0024BEF8` | 12 | `GetParam__14HFSM_BreakableR14bhvrHFSMClient` |
| `0x0024BFA0` | 8 | `GetStartState__Q214HFSM_Breakable13hfsmBreakableR14bhvrHFSMClientRC7goEvent` |
| `0x002718E0` | 44 | `ProcessEvent__Q314HFSM_Breakable13hfsmBreakable10SBreakableR14bhvrHFSMClientRC7goEvent` |
| `0x0027D858` | 180 | `ProcessEvent__Q414HFSM_Breakable13hfsmBreakable10SBreakable5SDoneR14bhvrHFSMClientRC7goEvent` |
| `0x0027D520` | 508 | `ProcessEvent__Q414HFSM_Breakable13hfsmBreakable10SBreakable5SIdleR14bhvrHFSMClientRC7goEvent` |
| `0x0027D2E0` | 216 | `ProcessEvent__Q414HFSM_Breakable13hfsmBreakable10SBreakable6SSTARTR14bhvrHFSMClientRC7goEvent` |
| `0x0027D750` | 212 | `ProcessEvent__Q414HFSM_Breakable13hfsmBreakable10SBreakable6SSmashR14bhvrHFSMClientRC7goEvent` |
| `0x0027D3E8` | 264 | `ProcessEvent__Q414HFSM_Breakable13hfsmBreakable10SBreakable8SInitialR14bhvrHFSMClientRC7goEvent` |
| `0x00259F90` | 84 | `_$_Q214HFSM_Breakable13hfsmBreakable` |
| `0x00256BF0` | 52 | `__Q214HFSM_Breakable11HFSMCreator` |
| `0x00265F18` | 152 | `__Q214HFSM_Breakable13hfsmBreakable` |
| `0x00271A30` | 68 | `__Q414HFSM_Breakable13hfsmBreakable10SBreakable5SDoneRQ314HFSM_Breakable13hfsmBreakable10SBreakable` |
| `0x002719A0` | 68 | `__Q414HFSM_Breakable13hfsmBreakable10SBreakable5SIdleRQ314HFSM_Breakable13hfsmBreakable10SBreakable` |
| `0x00271910` | 68 | `__Q414HFSM_Breakable13hfsmBreakable10SBreakable6SSTARTRQ314HFSM_Breakable13hfsmBreakable10SBreakable` |
| `0x002719E8` | 68 | `__Q414HFSM_Breakable13hfsmBreakable10SBreakable6SSmashRQ314HFSM_Breakable13hfsmBreakable10SBreakable` |
| `0x00271958` | 68 | `__Q414HFSM_Breakable13hfsmBreakable10SBreakable8SInitialRQ314HFSM_Breakable13hfsmBreakable10SBreakable` |
| `0x0024BFE8` | 72 | `__dl__Q214HFSM_Breakable13hfsmBreakablePv` |

### `goFreezeOrders10` — `0x8B886903`

Função semântica: Variante congelada de Orders; sem classe direta, usa sistemas de freeze/soldado.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00263780` | 12 | `GetFreezeTimeLeft__C10goCreaturef` |
| `0x00264760` | 16 | `GetFreezeTimeLeft__C9goPassivef` |
| `0x00263BA0` | 16 | `GetFreezeTimeLeft__C9goSoldierf` |
| `0x001B0F60` | 96 | `SCR_UnFreeze__FP8goScript` |
| `0x00265370` | 16 | `SetFreezeResistance__9goSoldierf` |
| `0x002057B0` | 208 | `SetInFreezeBeam__10goCreaturefi` |
| `0x00248158` | 956 | `UpdateSH_ProcessFreezeBeam__9goPassive` |
| `0x002645D8` | 8 | `__22FreezeBeamCallbackData` |

### `goSoldier` — `0x8F74ACDA`

Função semântica: Classe de criatura humanoide/inimigo; usada aqui como pool base para inimigos Rabdog/Orders/Soldier.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x0020C1F0` | 64 | `Allocate__9goSoldierP10wadContext` |
| `0x001E07C0` | 252 | `AttachToRope__9goSoldierG8goHandlef` |
| `0x001DE6B0` | 1648 | `BalanceGroundChecks__9goSoldierG6VUVec4f` |
| `0x0020E3C0` | 260 | `BreakFrozenIncarnation__9goSoldieri` |
| `0x00263980` | 140 | `CanDoCombatMove__C9goSoldier` |
| `0x001E9F20` | 64 | `CancelDriftWings__9goSoldier` |
| `0x001E54A0` | 408 | `CheckForDiveIntoWater__9goSoldierG6VUVec4ff` |
| `0x001E3FC8` | 1012 | `ClimbingGroundChecks__9goSoldierG6VUVec4i` |
| `0x001E7468` | 108 | `ConvertGrappleToPOR__9goSoldierG6VUVec4T1` |
| `0x001E73F8` | 108 | `ConvertPORToGrapple__9goSoldierG6VUVec4T1` |
| `0x0020E228` | 408 | `CreateFrozenIncarnation__9goSoldier` |
| `0x001E6C80` | 1056 | `DebugFlying__9goSoldier` |
| `0x00263968` | 12 | `DefyLightning__C9goSoldier` |
| `0x00263978` | 8 | `DerivedTweaks__C9goSoldier` |
| `0x0020D790` | 68 | `DestroyFightSystem__9goSoldier` |
| `0x001E08C0` | 176 | `DetachFromRope__9goSoldieri` |
| `0x001E4F58` | 48 | `DetachFromWall__9goSoldier` |
| `0x0020D7D8` | 44 | `Disable__9goSoldier` |
| `0x001E5E60` | 348 | `DivingChecks__9goSoldierG6VUVec4f` |
| `0x0020F898` | 224 | `Do180Flip__9goSoldier` |
| `0x001E06D0` | 240 | `DoAttachedToMotion__9goSoldierG6VUVec4f` |
| `0x0023EFD0` | 160 | `DoCliffFX__10MaterialFXP9goSoldieri` |
| `0x001EA280` | 1408 | `DoDriftingMotion__9goSoldierG6VUVec4N21f` |
| `0x00214490` | 888 | `DoFeetIK__9goSoldierG6VUVec4` |
| `0x0023EF88` | 72 | `DoFootFX__10MaterialFXP9goSoldierUii` |
| `0x0023EC38` | 844 | `DoFootFX__10MaterialFXP9goSoldierii` |
| `0x001E74D8` | 3120 | `DoGrappleMotion__9goSoldierG6VUVec4T1f` |
| `0x001DFB08` | 3012 | `DoHaveAttachedMotion__9goSoldierG6VUVec4T1f` |
| `0x001E5FC0` | 2528 | `DoInTheWaterMotion__9goSoldierG6VUVec4T1f` |
| `0x0023F070` | 316 | `DoLandingFX__10MaterialFXPCQ22dc11tMaterialFXP9goSoldierP10wadContextG6VUVec4T4` |
| `0x00211260` | 2676 | `DoNormalMotion__9goSoldierG6VUVec4N21iRif` |
| `0x001E17F8` | 2968 | `DoOnARopeMotion__9goSoldierG6VUVec4N21f` |
| `0x001E9740` | 1508 | `DoQuicksandMotion__9goSoldierG6VUVec4N21f` |
| `0x0023F1B0` | 132 | `DoRopeGrabFX__10MaterialFXP9goSoldier` |
| `0x00210DD0` | 1164 | `DoStrafeMotion__9goSoldierG6VUVec4N21Rif` |
| `0x001E4B40` | 1048 | `DoWallAnimation__9goSoldierfPfN32` |
| `0x001E43C0` | 1916 | `DoWallMotion__9goSoldierG6VUVec4N21f` |
| `0x0020D808` | 48 | `Enable__9goSoldier` |
| `0x001DF7B0` | 236 | `FindIOCreature__9goSoldier` |
| `0x001E70A0` | 420 | `FlyingGroundChecks__9goSoldierG6VUVec4` |
| `0x00263A18` | 8 | `GetAnimSystem__C9goSoldier` |
| `0x00263A20` | 8 | `GetAttachmentSystem__9goSoldier` |
| `0x00263A10` | 8 | `GetCreatureControls__9goSoldier` |
| `0x00263A30` | 8 | `GetEffectSystem__9goSoldier` |
| `0x00215010` | 152 | `GetFirstNavBankMotionP__C9goSoldier` |
| `0x00263BA0` | 16 | `GetFreezeTimeLeft__C9goSoldierf` |
| `0x00263A68` | 20 | `GetFrozenRatio__C9goSoldier` |
| `0x00263AA8` | 20 | `GetGOName__C9goSoldier` |
| `0x00263A28` | 8 | `GetHandleSystem__9goSoldier` |
| `0x00263A48` | 12 | `GetHeight__C9goSoldier` |
| `0x00263A38` | 12 | `GetMass__C9goSoldier` |
| `0x002148B0` | 1672 | `GetNavBranch__C9goSoldierPCQ22dc8tNavBanki` |
| `0x00214F38` | 216 | `GetNavBranch__C9goSoldierPCt8stdStack2ZSci3i` |
| `0x00263A58` | 12 | `GetRadius__C9goSoldier` |
| `0x00263AC0` | 76 | `GetRope__C9goSoldier` |
| `0x0020D988` | 140 | `GetTargetingPos__9goSoldieri` |
| `0x001DE490` | 544 | `HandleBalanceWalk__9goSoldier` |
| `0x001E2DB8` | 4624 | `HandleClimbingAndWallHang__9goSoldierG6VUVec4T1f` |
| `0x0020FCB0` | 1352 | `HandleCollisionOnly__9goSoldierG6VUVec4iiif` |
| `0x001E9F60` | 796 | `HandleDrifting__9goSoldierf` |
| `0x001E1488` | 876 | `HandleHangTargets__9goSoldierUi` |
| `0x001DF8A0` | 616 | `HandleInteractiveObjects__9goSoldierG6VUVec4` |
| `0x001E8BB0` | 1148 | `HandleJumpingDuringGrapple__9goSoldierG6VUVec4T1` |
| `0x001E28B0` | 1288 | `HandleJumpingFacingAWall__9goSoldierf` |
| `0x001E0CE0` | 952 | `HandleJumpingOnARope__9goSoldierG6VUVec4T1f` |
| `0x0020F978` | 824 | `HandleJumping__9goSoldierf` |
| `0x001E9D28` | 336 | `HandleQuicksand__9goSoldier` |
| `0x001E57A8` | 1340 | `HandleSwimmingAndDiving__9goSoldierG6VUVec4f` |
| `0x00210B48` | 644 | `HandleTrapDamage__9goSoldierf` |
| `0x001E91B0` | 1420 | `HandleWalkingInQuicksand__9goSoldierG6VUVec4f` |
| `0x001E1098` | 700 | `HandleWalkingSlidingFalling_OnARope__9goSoldierG6VUVec4f` |
| `0x002101F8` | 2384 | `HandleWalkingSlidingFalling__9goSoldierG6VUVec4f` |
| `0x001E69A0` | 324 | `HandleWaterSurfaceCollisionOnly__9goSoldierG6VUVec4` |
| `0x0020DB28` | 1684 | `HeadTrack__9goSoldier` |
| `0x001E5CE8` | 376 | `InTheWaterGroundChecks__9goSoldierG6VUVec4` |
| `0x00214408` | 132 | `InitFeetIK__9goSoldierPCci` |
| `0x001E8108` | 1620 | `InitGrappleMotion__9goSoldier` |
| `0x0020D3C0` | 832 | `Init__9goSoldierPCQ22dc9tCreatureP13tEffectSystem` |
| `0x00263BB0` | 44 | `IsFreezable__C9goSoldier` |
| `0x00263A80` | 36 | `IsFrozen__C9goSoldier` |
| `0x002141C0` | 340 | `PostSoldierUpdate__9goSoldier` |
| `0x0020D0E8` | 272 | `RemoveWadContext__9goSoldierP10wadContext` |
| `0x0020CD50` | 112 | `ResetLightSoldier__9goSoldier` |
| `0x0020CCA0` | 56 | `ResetStatue__9goSoldier` |
| `0x0020CDC0` | 808 | `Reset__9goSoldierUi` |
| `0x00226868` | 268 | `ResolveDirectionMatrix__13tFlyingSystemP9goSoldierG6VUVec4f` |
| `0x00226978` | 780 | `ResolveMovementMatrix__13tFlyingSystemP9goSoldierG6VUVec4f` |
| `0x001E8760` | 928 | `ResolvePhysicalOrientationDuringGrapple__9goSoldierG6VUVec4f` |
| `0x001E5638` | 364 | `ResolvePhysicalOrientationInWater__9goSoldierG6VUVec4f` |
| `0x001E0B68` | 372 | `ResolvePhysicalOrientationOnARope__9goSoldierG6VUVec4f` |
| `0x0020CCD8` | 120 | `RunLightSoldier__9goSoldier` |
| `0x0020D238` | 292 | `SetAttachmentSaveData__9goSoldieri` |
| `0x0020D360` | 32 | `SetCircleSaveData__9goSoldieri` |
| `0x00214808` | 48 | `SetDamageInfo__9goSoldierfG6VUVec4Uii` |
| `0x0020C310` | 1608 | `SetDynamicNavCollision__9goSoldier` |
| `0x00265370` | 16 | `SetFreezeResistance__9goSoldierf` |
| `0x00263B38` | 44 | `SetImpulse__9goSoldierG6VUVec4` |
| `0x0020CC28` | 120 | `SetInstanceVariations__9goSoldierffUi` |
| `0x001E2390` | 736 | `SetMode_Climbing__9goSoldieri` |
| `0x001E5360` | 228 | `SetMode_DiveIntoWater__9goSoldierf` |
| `0x001E51C0` | 416 | `SetMode_Diving__9goSoldier` |
| `0x0020F7D0` | 196 | `SetMode_DoubleJumping__9goSoldier` |
| `0x001E9E78` | 168 | `SetMode_Drifting__9goSoldier` |
| `0x0020E9B8` | 712 | `SetMode_Falling__9goSoldierUif` |
| `0x001E4F88` | 288 | `SetMode_Floating__9goSoldier` |
| `0x001E6AE8` | 404 | `SetMode_Flying__9goSoldier` |
| `0x001E8B00` | 176 | `SetMode_Grapple__9goSoldier` |
| `0x001DED20` | 516 | `SetMode_HaveAttached__9goSoldierP10goCreatureG6VUVec4T2ii` |
| `0x0020F6A8` | 292 | `SetMode_Jumping__9goSoldier` |
| `0x001E09E0` | 388 | `SetMode_OnARope__9goSoldier` |
| `0x0020F370` | 324 | `SetMode_OnCeiling__9goSoldier` |
| `0x001DEF28` | 628 | `SetMode_OnGround_HaveAttached__9goSoldier` |
| `0x001E9030` | 384 | `SetMode_OnGround_InQuicksand__9goSoldier` |
| `0x0020F2A8` | 200 | `SetMode_OnGround_Landing__9goSoldier` |
| `0x0020EFD8` | 720 | `SetMode_OnGround_Landing__9goSoldierG6VUVec4` |
| `0x0020EC80` | 856 | `SetMode_OnGround__9goSoldier` |
| `0x0020F4B8` | 492 | `SetMode_Sliding__9goSoldier` |
| `0x001E50A8` | 276 | `SetMode_Underwater__9goSoldier` |
| `0x001E2670` | 576 | `SetMode_WallHang__9goSoldier` |
| `0x0020C278` | 148 | `SetNavCollision__9goSoldier` |
| `0x0020D380` | 64 | `SetSaveData__9goSoldierUc` |
| `0x00214838` | 120 | `SetTweaks__9goSoldierPCQ22dc9tCreature` |
| `0x00214318` | 240 | `UpdateDead__9goSoldier` |
| `0x0020E4C8` | 1224 | `UpdateFreezing__9goSoldier` |
| `0x00226C88` | 300 | `UpdateHarpy__13tFlyingSystemP9goSoldierG6VUVec4N22` |
| `0x0020E990` | 40 | `UpdateImpulse__9goSoldierf` |
| `0x001E0970` | 112 | `UpdateRope__9goSoldier` |
| `0x001AEDF8` | 1020 | `UpdateShadow__8goShadowP12goGameObjectG6VUVec4PQ210attachment4tSysP9goSoldier` |
| `0x0020E1C0` | 104 | `UpdateTimeScale__9goSoldier` |
| `0x0020C958` | 720 | `UpdateVarsFromTweakers__9goSoldier` |
| `0x00211CD8` | 9448 | `Update__9goSoldierUi` |
| `0x00263B90` | 12 | `UsingAnimDrivenMotion__C9goSoldier` |
| `0x0020D1F8` | 64 | `Warp__9goSoldierRC6VUMat4` |
| `0x0020D838` | 336 | `_$_9goSoldier` |
| `0x0020D700` | 144 | `__9goSoldierP8goClientPCQ22dc9tCreatureP13tEffectSystemi` |
| `0x0020C230` | 72 | `__dl__9goSoldierPv` |

### `goIO_CSM` — `0x8FFD98EC`

Função semântica: Variante de objeto interativo/chest-style; controla animação de baú/CSM, emissão de orbs e estado persistente.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00239780` | 64 | `Allocate__8goIO_CSMP10wadContext` |
| `0x0023A2B8` | 48 | `EmitOrbs__8goIO_CSMPCQ22dc11tOrbEmitter` |
| `0x00264CC8` | 8 | `GetAnimSystem__C8goIO_CSM` |
| `0x00264CD8` | 8 | `GetAttachmentSystem__8goIO_CSM` |
| `0x00264CC0` | 8 | `GetCreatureControls__8goIO_CSM` |
| `0x00264CF0` | 8 | `GetEffectSystem__8goIO_CSM` |
| `0x00264CE8` | 8 | `GetFightSystem__8goIO_CSM` |
| `0x00264CE0` | 8 | `GetHandleSystem__8goIO_CSM` |
| `0x00264D08` | 16 | `GetHeight__C8goIO_CSM` |
| `0x00264CF8` | 12 | `GetMass__C8goIO_CSM` |
| `0x00264CD0` | 8 | `GetMoveSystem__C8goIO_CSM` |
| `0x00264D18` | 16 | `GetRadius__C8goIO_CSM` |
| `0x00239CB0` | 52 | `GrabState__8goIO_CSM` |
| `0x00239EB8` | 352 | `HandleCollision__8goIO_CSMP10goCreaturei` |
| `0x002399D0` | 500 | `Init__8goIO_CSMPCQ22dc4tCSMi` |
| `0x001D1290` | 276 | `LoadIO_CSM_State__7WadInfoP12goGameObjectPvP8goIO_CSM` |
| `0x00239EB0` | 8 | `Pause__8goIO_CSM` |
| `0x00239900` | 204 | `PlayAnim__8goIO_CSMUi` |
| `0x002398D0` | 44 | `Reset__8goIO_CSMUi` |
| `0x00239DF0` | 192 | `SendProgressEvent__8goIO_CSMfff` |
| `0x00264D28` | 8 | `SetDamageInfo__8goIO_CSMfG6VUVec4Uii` |
| `0x00239BC8` | 232 | `Unlock__8goIO_CSM` |
| `0x0023A018` | 192 | `UpdateChestGFX__8goIO_CSMi` |
| `0x0023A0D8` | 476 | `UpdateChest__8goIO_CSM` |
| `0x00239808` | 200 | `UpdateVarsFromTweakers__8goIO_CSM` |
| `0x0023A2E8` | 1588 | `Update__8goIO_CSMUi` |
| `0x00239D78` | 120 | `_$_8goIO_CSM` |
| `0x00239CE8` | 140 | `__8goIO_CSMP8goClientPCQ22dc4tCSMi` |
| `0x002397C0` | 72 | `__dl__8goIO_CSMPv` |

### `goStoneRabdog00` — `0x93FB2D26`

Função semântica: Rabdog petrificado; sem classe direta, usa freeze/stone pipeline.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x001B12C0` | 60 | `RegisterTurnToStoneScripts__Fv` |

### `goGenericBlockS` — `0x9EA56755`

Função semântica: Objeto de bloqueio genérico pequeno; sem símbolos diretos, provavelmente data-driven.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x0014B120` | 160 | `AddFINISHCommand__11renDMABlock` |
| `0x001943A8` | 56 | `ApplyBlock__FP10goCreaturePCQ22dc11tConcussionG6VUVec4` |
| `0x00158368` | 1256 | `CalculateImageBlockMask__16renGfxClutServerssUsPiii` |
| `0x001599C8` | 340 | `ClearVRAMBlocks__16renGfxClutServer` |
| `0x0014A3A0` | 372 | `CreateAndRegisterDMABlock__11renDMABlockUiUiPFv_vUi` |
| `0x0014A518` | 380 | `CreateAndRegisterVectorDMABlock__11renDMABlockUiUiPFv_vUi` |
| `0x0014AE10` | 244 | `CreateSystemDMABlocks__11renDMABlock` |
| `0x00159EA0` | 128 | `DeAllocImageVRAM__16renGfxClutServerP7vrBlock` |
| `0x0015B1A0` | 128 | `FreeBlock__14renGfxClutPoolP7vrBlock` |
| `0x0014B360` | 372 | `GenerateAndKickBlackScreens__11renDMABlock` |
| `0x0014B1E8` | 372 | `GenerateAndKickFinalScreenCopy__11renDMABlock` |
| `0x0014B4D8` | 372 | `GenerateAndKickMPEGHack__11renDMABlock` |
| `0x0014B1C0` | 36 | `GenerateEpilogueDMA__11renDMABlock` |
| `0x0014AF08` | 536 | `GeneratePrologueDMA__11renDMABlock` |
| `0x001346A0` | 576 | `GetFieldParameterBlocks__7fxFieldP21fxFieldParameterBlock` |
| `0x001487B0` | 184 | `GetLargestFreeBlockSize__3MemP8HeapZoneUi` |
| `0x001BC9B8` | 248 | `GetRandomBlock__C13tDecisionTreePCQ22dc14tDTreeSlotNode` |
| `0x0015B228` | 96 | `HardLock__14renGfxClutPoolP7vrBlock` |
| `0x001488F8` | 128 | `HeapNumUsedBlocks__3MemP8HeapZone` |
| `0x0018C108` | 148 | `IFFProcessDataBlockAlign16__9wadLoaderPQ23IFF6HeaderPCc` |
| `0x0018C088` | 124 | `IFFProcessDataBlockWLength__9wadLoaderPQ23IFF6HeaderPCc` |
| `0x0018C010` | 116 | `IFFProcessDataBlock__9wadLoaderPQ23IFF6HeaderPCc` |
| `0x00229858` | 28 | `IncNumBlocks__5tMove` |
| `0x0014AC60` | 428 | `Kick__11renDMABlock` |
| `0x0014A698` | 928 | `LinkDMABuffers__11renDMABlock` |
| `0x00264870` | 8 | `QuickBlockProjTest__11goPegasusAI` |
| `0x0024ABC8` | 8 | `QuickBlockProjTest__19goPassiveController` |
| `0x001EBA30` | 204 | `QuickBlockProjTest__4goAI` |
| `0x0022B8C0` | 284 | `QuickBlockProjectile__11tMoveSystem` |
| `0x00264868` | 8 | `QuickBlockTest__11goPegasusAIP12goGameObjectPCQ22dc5tMoveUs` |
| `0x0024ABC0` | 8 | `QuickBlockTest__19goPassiveControllerP12goGameObjectPCQ22dc5tMoveUs` |
| `0x001EB498` | 1428 | `QuickBlockTest__4goAIP12goGameObjectPCQ22dc5tMoveUs` |
| `0x0022B760` | 348 | `QuickBlock__11tMoveSystemP12goGameObjectPCQ22dc5tMoveUs` |
| `0x0014A300` | 156 | `RegisterDMABlock__11renDMABlockP11renDMABlock` |
| `0x0015B288` | 48 | `RemoveHardLock__14renGfxClutPoolP7vrBlock` |
| `0x0015B2B8` | 96 | `SoftLock__14renGfxClutPoolP7vrBlock` |
| `0x0025FC10` | 76 | `StandardRETCallback__11renDMABlock` |
| `0x0014AA38` | 552 | `SwapDMABuffers__11renDMABlock` |
| `0x001BC120` | 244 | `TargetBlocking__FP8goAIBaseP10goCreaturePCQ22dc10tDTreeNode` |
| `0x0015B318` | 88 | `Unlock__14renGfxClutPoolP7vrBlock` |
| `0x00264010` | 20 | `__18QuickBlockTestBase` |
| `0x0025FD10` | 8 | `__21fxFieldParameterBlock` |
| `0x00256490` | 16 | `__t8stdCList2ZP11renDMABlockZt12stdAllocator1Z16stdCListNodeBase` |
| `0x0029A960` | 304 | `_sceMcCoreBlockErase` |
| `0x00299AA8` | 72 | `_sceMcpIsSpeckOutBlock` |
| `0x00299B38` | 372 | `_sceMcpMakeSpecoutBlock` |
| `0x002844B0` | 44 | `snd_SRAMMaxFreeBlockSize_CB` |

### `goChest` — `0xA9443B39`

Função semântica: Nome de pool para baús; neste ELF parece data-driven via goIO_CSM/hfsmIO_CSM, não classe C++ direta.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00239780` | 64 | `Allocate__8goIO_CSMP10wadContext` |
| `0x0024BE50` | 64 | `Allocate__Q211HFSM_IO_CSM10hfsmIO_CSMP10wadContext` |
| `0x00271838` | 8 | `CallExitCode__Q311HFSM_IO_CSM10hfsmIO_CSM7SIO_CSMP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027D128` | 48 | `CallExitCode__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM5SDoneP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027CEE0` | 48 | `CallExitCode__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM8SInitialP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0023A2B8` | 48 | `EmitOrbs__8goIO_CSMPCQ22dc11tOrbEmitter` |
| `0x00282668` | 28 | `EntryCode__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM5SDoneR14bhvrHFSMClientR12IO_CSM_InterRC7goEvent` |
| `0x00264CC8` | 8 | `GetAnimSystem__C8goIO_CSM` |
| `0x00264CD8` | 8 | `GetAttachmentSystem__8goIO_CSM` |
| `0x00264CC0` | 8 | `GetCreatureControls__8goIO_CSM` |
| `0x00264CF0` | 8 | `GetEffectSystem__8goIO_CSM` |
| `0x00264CE8` | 8 | `GetFightSystem__8goIO_CSM` |
| `0x00264CE0` | 8 | `GetHandleSystem__8goIO_CSM` |
| `0x00264D08` | 16 | `GetHeight__C8goIO_CSM` |
| `0x00264CF8` | 12 | `GetMass__C8goIO_CSM` |
| `0x00264CD0` | 8 | `GetMoveSystem__C8goIO_CSM` |
| `0x00264D18` | 16 | `GetRadius__C8goIO_CSM` |
| `0x0024BD50` | 252 | `GetStartState__Q211HFSM_IO_CSM10hfsmIO_CSMR14bhvrHFSMClientRC7goEvent` |
| `0x00239CB0` | 52 | `GrabState__8goIO_CSM` |
| `0x00239EB8` | 352 | `HandleCollision__8goIO_CSMP10goCreaturei` |
| `0x002399D0` | 500 | `Init__8goIO_CSMPCQ22dc4tCSMi` |
| `0x001D1290` | 276 | `LoadIO_CSM_State__7WadInfoP12goGameObjectPvP8goIO_CSM` |
| `0x00239EB0` | 8 | `Pause__8goIO_CSM` |
| `0x00239900` | 204 | `PlayAnim__8goIO_CSMUi` |
| `0x00271840` | 8 | `ProcessEvent__Q311HFSM_IO_CSM10hfsmIO_CSM7SIO_CSMR14bhvrHFSMClientRC7goEvent` |
| `0x0027D158` | 344 | `ProcessEvent__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM5SDoneR14bhvrHFSMClientRC7goEvent` |
| `0x0027CF10` | 536 | `ProcessEvent__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM8SInitialR14bhvrHFSMClientRC7goEvent` |
| `0x002398D0` | 44 | `Reset__8goIO_CSMUi` |
| `0x00239DF0` | 192 | `SendProgressEvent__8goIO_CSMfff` |
| `0x00264D28` | 8 | `SetDamageInfo__8goIO_CSMfG6VUVec4Uii` |
| `0x00239BC8` | 232 | `Unlock__8goIO_CSM` |
| `0x0023A018` | 192 | `UpdateChestGFX__8goIO_CSMi` |
| `0x0023A0D8` | 476 | `UpdateChest__8goIO_CSM` |
| `0x00239808` | 200 | `UpdateVarsFromTweakers__8goIO_CSM` |
| `0x0023A2E8` | 1588 | `Update__8goIO_CSMUi` |
| `0x00239D78` | 120 | `_$_8goIO_CSM` |
| `0x00259F00` | 140 | `_$_Q211HFSM_IO_CSM10hfsmIO_CSM` |
| `0x00239CE8` | 140 | `__8goIO_CSMP8goClientPCQ22dc4tCSMi` |
| `0x00265E78` | 136 | `__Q211HFSM_IO_CSM10hfsmIO_CSM` |
| `0x00271890` | 68 | `__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM5SDoneRQ311HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM` |
| `0x00271848` | 68 | `__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM8SInitialRQ311HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM` |
| `0x002397C0` | 72 | `__dl__8goIO_CSMPv` |
| `0x0024BE90` | 72 | `__dl__Q211HFSM_IO_CSM10hfsmIO_CSMPv` |

### `goOrderSBladeG` — `0xBDC0DD1C`

Função semântica: Variante/ataque blade de Orders.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00224270` | 80 | `BladeCollision__CQ210attachment8tChained` |
| `0x001E7350` | 164 | `CalcBladeRot__FG6VUVec4T0` |
| `0x00227E90` | 240 | `UpdateBladeStatus__Q210attachment5tHandP6VUMat4` |

### `goFreezeRabdog00` — `0xD07BEA68`

Função semântica: Rabdog congelado; sem classe direta, usa freeze pipeline.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x00263780` | 12 | `GetFreezeTimeLeft__C10goCreaturef` |
| `0x00264760` | 16 | `GetFreezeTimeLeft__C9goPassivef` |
| `0x00263BA0` | 16 | `GetFreezeTimeLeft__C9goSoldierf` |
| `0x001B0F60` | 96 | `SCR_UnFreeze__FP8goScript` |
| `0x00265370` | 16 | `SetFreezeResistance__9goSoldierf` |
| `0x002057B0` | 208 | `SetInFreezeBeam__10goCreaturefi` |
| `0x00248158` | 956 | `UpdateSH_ProcessFreezeBeam__9goPassive` |
| `0x002645D8` | 8 | `__22FreezeBeamCallbackData` |

### `PB_StickBack` — `0xD08A6D6A`

Função semântica: Hash/string de prompt/input; usado como m_CrankPrompt em IO_SLCRANK.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

### `goMiniGameCircle` — `0xE2EA5761`

Função semântica: Objeto de círculo/minigame; sem classe direta neste ELF.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

### `hfsmIO_CSM` — `0xF29974DC`

Função semântica: HFSM da variante CSM/chest; estados Initial/Done e EntryCode com IO_CSM_Inter.

| Endereço | Tamanho | Função |
|---:|---:|---|
| `0x0024BE50` | 64 | `Allocate__Q211HFSM_IO_CSM10hfsmIO_CSMP10wadContext` |
| `0x00271838` | 8 | `CallExitCode__Q311HFSM_IO_CSM10hfsmIO_CSM7SIO_CSMP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027D128` | 48 | `CallExitCode__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM5SDoneP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0027CEE0` | 48 | `CallExitCode__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM8SInitialP9hfsmStateR14bhvrHFSMClientRC7goEvent` |
| `0x0024BCA8` | 72 | `Create__CQ211HFSM_IO_CSM11HFSMCreator` |
| `0x00282668` | 28 | `EntryCode__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM5SDoneR14bhvrHFSMClientR12IO_CSM_InterRC7goEvent` |
| `0x00265E70` | 8 | `GetDataSize__CQ211HFSM_IO_CSM11HFSMCreator` |
| `0x0024BD08` | 72 | `GetDependantClass__CQ211HFSM_IO_CSM11HFSMCreatori` |
| `0x0024BCF0` | 12 | `GetName__CQ211HFSM_IO_CSM11HFSMCreator` |
| `0x0024BD00` | 8 | `GetNumDependantClasses__CQ211HFSM_IO_CSM11HFSMCreator` |
| `0x0024BC98` | 12 | `GetParam__11HFSM_IO_CSMR14bhvrHFSMClient` |
| `0x0024BD50` | 252 | `GetStartState__Q211HFSM_IO_CSM10hfsmIO_CSMR14bhvrHFSMClientRC7goEvent` |
| `0x00271840` | 8 | `ProcessEvent__Q311HFSM_IO_CSM10hfsmIO_CSM7SIO_CSMR14bhvrHFSMClientRC7goEvent` |
| `0x0027D158` | 344 | `ProcessEvent__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM5SDoneR14bhvrHFSMClientRC7goEvent` |
| `0x0027CF10` | 536 | `ProcessEvent__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM8SInitialR14bhvrHFSMClientRC7goEvent` |
| `0x00259F00` | 140 | `_$_Q211HFSM_IO_CSM10hfsmIO_CSM` |
| `0x00265E78` | 136 | `__Q211HFSM_IO_CSM10hfsmIO_CSM` |
| `0x00256BB8` | 52 | `__Q211HFSM_IO_CSM11HFSMCreator` |
| `0x00271890` | 68 | `__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM5SDoneRQ311HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM` |
| `0x00271848` | 68 | `__Q411HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM8SInitialRQ311HFSM_IO_CSM10hfsmIO_CSM7SIO_CSM` |
| `0x0024BE90` | 72 | `__dl__Q211HFSM_IO_CSM10hfsmIO_CSMPv` |

### `goOrdersKillBlood1` — `0xFC56E002`

Função semântica: Outra variante de efeito/entidade de sangue para Orders.

Nenhuma função direta com esse nome foi encontrada em `.symtab`. A entrada está presente como hash/string de pool ou referência DC; a execução deve cair em sistemas genéricos/pai.

## Apêndice B — correção importante sobre limites de objeto
A forma inicial de calcular tamanho como `próximo DbgSymbol - offset atual` é útil para listar símbolos, mas não é segura para serialização. `DbgSymbolTableChunk` contém símbolos de campos internos. Exemplo: `CrankSetWind`, `CrankSetUnwind` e `CrankSetEvent` ficam dentro de `IO_SLCRANK`. Para editar/repackar, use o layout do TypeId/struct, não apenas a distância até a próxima entrada de debug.

## Regras práticas para edição/repack
- Preservar big-endian em todos os campos numéricos.
- Para `tGOPool`/`tMemoryPool`, alterar `count` é seguro apenas se o runtime aceitar a nova capacidade; o tamanho da entrada continua 8 bytes.
- Para `tBreakable`, `m_HitPoints`, `m_OpaqueTime` e `m_FadeTime` são `u16` fixed 8.8 no sample. Exemplo: `0x4500 = 69.0`.
- Para `tIO`, editar floats e refs existentes é mais seguro do que inserir/remover strings. Os campos de som parecem refs/índices para strings próximas; preserve offsets/tamanho até mapear totalmente o sistema de string refs.
- Imports de `[014]` devem ser preservados quando o campo for referência externa, como `ORBE_BREAK_SMALL` em `BRK_URN` e `BRK_BONES`.
