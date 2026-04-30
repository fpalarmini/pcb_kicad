# KiCad 10 — Guia Completo de PCB Design

> Guia prático com foco em produtividade, atalhos de teclado e boas práticas.

---

## Sumário

1. [Visão Geral do Workflow](#1-visão-geral-do-workflow)
2. [Schematic Editor (Eeschema)](#2-schematic-editor-eeschema)
3. [PCB Editor (Pcbnew)](#3-pcb-editor-pcbnew)
4. [Atalhos de Teclado](#4-atalhos-de-teclado)
5. [Design Rules & DRC](#5-design-rules--drc)
6. [Tips & Tricks](#6-tips--tricks)
7. [Footprint & Symbol Libraries](#7-footprint--symbol-libraries)
8. [Gerber & Fabricação](#8-gerber--fabricação)

---

## 1. Visão Geral do Workflow

```
Ideia → Schematic (Eeschema) → Assign Footprints → PCB Layout (Pcbnew) → DRC → Gerbers → Fabricação
```

### Ferramentas principais

| Ferramenta         | Função                                      |
|--------------------|---------------------------------------------|
| Schematic Editor   | Desenhar o diagrama elétrico (netlist)      |
| Symbol Editor      | Criar/editar símbolos esquemáticos          |
| PCB Editor         | Layout físico da placa                      |
| Footprint Editor   | Criar/editar footprints (padstacks)         |
| 3D Viewer          | Visualizar a PCB em 3D                      |
| SPICE Simulator    | Simulação de circuitos analógicos           |
| Gerber Viewer      | Verificar arquivos para fabricação          |

---

## 2. Schematic Editor (Eeschema)

### Fluxo básico

1. **Novo projeto**: `File → New Project` — cria `.kicad_pro`, `.kicad_sch`, `.kicad_pcb`
2. **Adicionar símbolos**: tecla `A` abre o browser de símbolos
3. **Conectar com fios**: tecla `W` para traçar wire
4. **Adicionar valores e referências**: `E` sobre o componente abre as propriedades
5. **Power symbols**: `P` — use `VCC`, `GND`, `+3V3` etc.
6. **Labels de rede (net labels)**: `L` — preferível a fios longos cruzando o schematic
7. **Hierarquia**: para projetos grandes, use `Place → Hierarchical Sheet`

### Boas práticas de Schematic

- Agrupe componentes por função (power supply, MCU, interfaces)
- Use **net labels** em vez de fios longos — o schematic fica mais limpo
- Adicione **Power Flags** (`PWR_FLAG`) em redes de power para evitar falsos warnings do ERC
- Use **blocos de título** (`Place → Add Title Block`) com versão e data
- Sempre anote **valores** nos capacitores e resistores (não só a referência)
- Separe o schematic em **sheets hierárquicas** quando tiver mais de ~50 componentes

### ERC — Electrical Rules Check

`Inspect → Electrical Rules Checker`

Erros comuns e como resolver:

| Erro                        | Causa                          | Solução                          |
|-----------------------------|--------------------------------|----------------------------------|
| Pin unconnected             | Pino solto                     | Conectar ou adicionar `No Connect (X)` |
| Power pin not driven        | Rede de power sem fonte        | Adicionar `PWR_FLAG`             |
| Wire not connected          | Wire solto                     | Verificar terminações            |
| Duplicate reference         | Dois componentes com mesmo ref | Renumerate: `Tools → Annotate`  |

---

## 3. PCB Editor (Pcbnew)

### Importando o netlist do schematic

No Schematic Editor: `Tools → Update PCB from Schematic` (ou `F8`)  
Isso sincroniza o netlist diretamente — não precisa exportar arquivo separado no KiCad moderno.

### Layers mais importantes

| Layer              | Função                                           |
|--------------------|--------------------------------------------------|
| `F.Cu` / `B.Cu`    | Cobre frontal / traseiro                         |
| `In1.Cu`...`In30.Cu` | Camadas internas (para multilayer)             |
| `F.SilkScreen`     | Silkscreen frontal (marcações visuais)           |
| `F.Mask`           | Máscara de solda frontal                         |
| `F.Paste`          | Pasta de solda (para SMD stencil)                |
| `Edge.Cuts`        | Contorno da placa (obrigatório)                  |
| `F.Fab` / `B.Fab`  | Informação de fabricação (não vai para fábrica)  |
| `F.Courtyard`      | Área de exclusão do componente                   |
| `User.1`...`User.9`| Layers personalizados de documentação            |

### Fluxo de Layout

1. **Board outline**: desenhar o contorno em `Edge.Cuts` com `Add Rectangle` ou linhas
2. **Place components**: arrastar da lista de footprints; agrupe por função e sinal
3. **Route tracks**: `X` para roteamento manual; `Tab` muda a largura do track
4. **Flood fill (copper pour)**: `B` preenche todas as zonas; `Zone Fill` para GND/VCC
5. **3D View**: `Alt+3` para verificar visualmente
6. **DRC**: `Inspect → Design Rules Checker`

### Roteamento — estratégias

- **Comece pelos componentes críticos**: sinais de alta frequência, linhas de potência
- **Componentes de desacoplamento** (capacitores 100nF): coloque fisicamente próximos ao pino VCC do IC
- **Evite ângulos de 90°** em sinais de RF/alta velocidade — use 45° ou arcos
- **Plano de GND**: sempre que possível, use um plano de GND em toda uma camada (melhor EMI e retorno de corrente)
- **Via stitching**: conecte o plano de GND entre camadas com vias distribuídas
- **Differential pairs**: use `Route → Route Differential Pair` para USB, LVDS, Ethernet

---

## 4. Atalhos de Teclado

### Global (qualquer editor)

| Atalho             | Ação                                    |
|--------------------|-----------------------------------------|
| `Ctrl+Z`           | Desfazer                                |
| `Ctrl+Shift+Z`     | Refazer                                 |
| `Ctrl+S`           | Salvar                                  |
| `Ctrl+C` / `V`     | Copiar / Colar                          |
| `Del`              | Deletar selecionado                     |
| `Ctrl+A`           | Selecionar tudo                         |
| `Esc`              | Cancelar operação atual                 |
| `?`                | Mostrar todos os atalhos                |
| `Ctrl+H`           | Busca e substituição                    |

### Schematic Editor

| Atalho             | Ação                                    |
|--------------------|-----------------------------------------|
| `A`                | Adicionar símbolo (componente)          |
| `P`                | Adicionar power symbol                  |
| `W`                | Traçar wire                             |
| `L`                | Adicionar net label                     |
| `Q`                | Adicionar global label                  |
| `X`                | Adicionar no-connect flag               |
| `E`                | Editar propriedades do item selecionado |
| `R`                | Rotacionar 90° (anti-horário)           |
| `M`                | Mover item                              |
| `G`                | Arrastar (mantendo conexões)            |
| `T`                | Adicionar texto                         |
| `B`                | Adicionar bus                           |
| `I`                | Adicionar bus entry                     |
| `U`                | Selecionar toda a net conectada         |
| `Ctrl+D`           | Duplicar                                |
| `H`                | Mirror horizontal                       |
| `V`                | Mirror vertical                         |
| `F8`               | Atualizar PCB a partir do schematic     |
| `Ctrl+L`           | Highlight net                           |

### PCB Editor

| Atalho             | Ação                                    |
|--------------------|-----------------------------------------|
| `X`                | Iniciar roteamento de track             |
| `Tab`              | Mudar largura do track durante roteamento |
| `/`                | Mudar layer durante roteamento (adiciona via) |
| `U`                | Selecionar toda a net/track conectada   |
| `I`                | Selecionar tracks conectadas            |
| `E`                | Propriedades do item                    |
| `R`                | Rotacionar                              |
| `M`                | Mover                                   |
| `G`                | Arrastar (com rubberbanding)            |
| `F`                | Flip (mover para outra face)            |
| `B`                | Preencher todas as zonas (flood fill)   |
| `Ctrl+B`           | Limpar preenchimento de zonas           |
| `D`                | Roteamento de pista interativo          |
| `T`                | Adicionar texto                         |
| `O`                | Adicionar footprint                     |
| `Z`                | Adicionar zona de cobre                 |
| `N`                | Modo de seleção por net                 |
| `Alt+3`            | Abrir 3D Viewer                         |
| `Ctrl+F`           | Buscar componente por referência        |
| `Ctrl+Shift+F`     | Buscar e mover para footprint           |
| `Space`            | Resetar coordenadas relativas           |
| `` ` ``            | Mostrar/ocultar ratsnest                |
| `H`                | Highlight net sob o cursor              |
| `~`                | Highlight net (toggle)                  |
| `W`                | Mudar largura de pista                  |

### Navegação / Zoom

| Atalho             | Ação                                    |
|--------------------|-----------------------------------------|
| `Scroll`           | Zoom in/out                             |
| `Middle click drag`| Pan (arrastar a view)                   |
| `Ctrl+Scroll`      | Zoom horizontal                         |
| `F`                | Fit everything in view (viewer)         |
| `0` (teclado num.) | Zoom para exibir toda a placa           |
| `Ctrl+Home`        | Zoom para exibir tudo                   |
| `+` / `-`          | Zoom in/out incremental                 |

---

## 5. Design Rules & DRC

### Configurando Design Rules

`File → Board Setup → Design Rules`

Parâmetros essenciais para fabricação comum (JLCPCB, PCBWay etc.):

| Parâmetro              | Valor mínimo típico (2 camadas) |
|------------------------|----------------------------------|
| Track width            | 0.127 mm (5 mil)                |
| Clearance              | 0.127 mm (5 mil)                |
| Via hole diameter      | 0.3 mm                          |
| Via annular ring       | 0.15 mm (cada lado)             |
| Minimum drill size     | 0.2 mm                          |
| Copper to edge         | 0.2 mm                          |

### Net Classes

Use net classes para aplicar regras diferentes por tipo de sinal:

1. `File → Board Setup → Net Classes`
2. Crie classes: `Power` (largura maior), `Signal` (padrão), `RF` (largura específica)
3. Atribua nets às classes

### Rodando o DRC

`Inspect → Design Rules Checker → Run DRC`

Tipos de violações comuns:

| Violação               | Significado                                 |
|------------------------|---------------------------------------------|
| Clearance violation    | Duas geometrias muito próximas              |
| Track too narrow       | Track abaixo do mínimo configurado          |
| Unconnected items      | Nets do schematic não roteadas na PCB       |
| Footprint courtyard    | Footprints se sobrepondo                    |
| Silkscreen overlap     | Texto na silkscreen sobre pad               |
| Missing courtyard      | Footprint sem layer de courtyard            |

---

## 6. Tips & Tricks

### Produtividade geral

**Instâncias múltiplas**  
Abra vários projetos em janelas separadas — KiCad 10 suporta multi-projeto nativamente.

**Scripting com Python**  
`Tools → Scripting Console` — automatize tarefas repetitivas com Python. Útil para renomear em lote, gerar relatórios, modificar propriedades.

**Board Statistics**  
`Inspect → Board Statistics` — veja contagem de componentes, vias, tracks.

**Net Inspector**  
`Inspect → Net Inspector` — analise comprimento de tracks, vias por net. Essencial para length matching em DDR/USB.

**Differential Pair Length Matching**  
`Route → Length Tuning → Tune Differential Pair Skew` — adiciona meandering automaticamente.

**Design para manufatura (DFM)**  
- Adicione `mounting holes` (furos de fixação) desde o início
- Use `fiducial marks` para placas com SMD para pick-and-place
- Verifique `paste aperture` dos pads SMD (deve ser ≤100% do pad)
- Adicione `test points` em nets críticas para facilitar o debug

### Schematic Tips

**Global Labels vs Hierarchical Labels**  
- `Global Label`: conecta em QUALQUER sheet do projeto — cuidado com nomes duplicados
- `Hierarchical Label`: conecta apenas entre sheets pai-filho

**Custom Title Block**  
Edite `File → Page Settings` para adicionar info de projeto, revisão, e empresa.

**Bus de sinais**  
Use buses para agrupar múltiplos pinos (ex: `DATA[0..7]`). Conecte com bus entries (`I`).

**Campos personalizados**  
Em propriedades do componente, adicione campos como `Manufacturer`, `Part Number`, `Datasheet` — exportáveis para BOM.

### Layout Tips

**Interactive Router Settings**  
`Route → Interactive Router Settings`:
- **Shove mode**: empurra tracks existentes para abrir espaço (mais lento mas poderoso)
- **Walk around**: contorna obstáculos (mais rápido)
- **Push and shove**: recomendado para PCBs densas

**Align & Distribute**  
Selecione múltiplos componentes → clique direito → `Align/Distribute`. Essencial para ICs em fileiras, conectores, LEDs.

**Grupos**  
Selecione vários itens → `Ctrl+G` — agrupa para mover/copiar junto. Ideal para subcircuitos repetidos.

**Array de componentes**  
`Edit → Create Array` — cria matrizes de footprints (ex: 8x2 LEDs, banco de capacitores).

**Lock de componentes**  
`Right click → Lock` — trava footprints para não mover acidentalmente após posicionado.

**Seleção por filtro**  
`Right click → Select → All Tracks in Net` — seleciona toda uma net de uma vez.

**Copper Pour com ilhas**  
No KiCad 10, zonas com `Remove Unconnected Islands = Always` eliminam cobre flutuante automaticamente.

**Teardrops**  
`Edit → Edit Teardrops` — adiciona teardrops em vias e pads. Melhora confiabilidade na soldagem e reduz quebra de pista.

**Push Track Length**  
Para DDR4, RGMII, PCIe: use `Inspect → Net Inspector → Length` para verificar matching.

### Thermal Relief

Para pads em planos de cobre, configure o thermal relief:
- `Board Setup → Pads → Thermal Relief`
- Componentes through-hole de potência: use `solid fill` para melhor dissipação
- Componentes SMD de sinal: `thermal relief` padrão facilita soldagem manual

### Vias

| Tipo            | Uso                                          |
|-----------------|----------------------------------------------|
| Through-hole    | Padrão — atravessa todas as camadas          |
| Blind via       | Conecta camada externa a interna (mais caro) |
| Buried via      | Somente camadas internas (mais caro ainda)   |
| Microvia        | Para HDI (usado em BGA fino)                 |
| Via-in-pad      | Permite colocar via dentro do pad SMD        |

Para 2 camadas: use apenas through-hole vias para manter custo baixo.

---

## 7. Footprint & Symbol Libraries

### Criando Symbol customizado

1. `Tools → Symbol Editor`
2. `File → New Symbol`
3. Desenhe os pinos com `Place → Add Pin`
4. Defina `Reference` (ex: `U`) e `Value`
5. Salve em uma biblioteca `.kicad_sym` local (não modifique as padrão)

### Criando Footprint customizado

1. `Tools → Footprint Editor`
2. `File → New Footprint`
3. Adicione pads com `Add Pad` (`O`)
4. Configure:
   - Tipo: `SMD`, `Through-hole`, `NPTH` (non-plated)
   - Shape: `Round`, `Rect`, `Oval`, `Custom`
   - Layer assignments
5. Adicione courtyard, silk e fab layers
6. Salve em `.pretty` local

### KiCad Official Libraries (Github)

As libraries oficiais ficam em:
- `kicad-symbols` → símbolos esquemáticos
- `kicad-footprints` → footprints
- `kicad-packages3D` → modelos 3D STEP/WRL

### Fontes externas de footprints

- **SnapEDA** — snapeda.com — gera símbolos e footprints para download direto
- **Ultra Librarian** — ultralibrarian.com
- **Component Search Engine** — componentsearchengine.com
- **LCSC/EasyEDA** — importar e converter para KiCad

### Associar Footprint no Schematic

1. Selecione o componente → `E` → aba `Footprint`
2. Clique no ícone de livro → abre Footprint Browser
3. Atribua o footprint correto

Ou use: `Tools → Assign Footprints` para atribuição em massa.

---

## 8. Gerber & Fabricação

### Gerando Gerbers

`File → Fabrication Outputs → Gerbers`

Layers típicos para exportar:

| Arquivo            | Camada                    |
|--------------------|---------------------------|
| `*.GTL`            | F.Cu (top copper)         |
| `*.GBL`            | B.Cu (bottom copper)      |
| `*.GTS`            | F.Mask (top solder mask)  |
| `*.GBS`            | B.Mask (bottom solder mask)|
| `*.GTO`            | F.SilkScreen (top silk)   |
| `*.GBO`            | B.SilkScreen              |
| `*.GKO` ou `*.GM1` | Edge.Cuts (board outline) |
| `*.DRL`            | Drill file (Excellon)     |

### Configurações recomendadas para JLCPCB/PCBWay

- Format: `Gerber X2` ou `Gerber (legacy)` — ambos aceitos
- Marque: `Use Protel filename extensions`
- Desmarque: `Plot pad numbers`, `Plot footprint values` (silkscreen mais limpo)

### Drill File

`File → Fabrication Outputs → Drill Files`
- Format: `Excellon`
- Marque: `Merge PTH and NPTH holes into single file` (padrão das fabricantes)

### BOM — Bill of Materials

`File → Fabrication Outputs → BOM`

Ou use o plugin `KiCad BOM` ou scripts Python para BOM mais detalhada com `Part Number`, `Supplier`, etc.

### Pick and Place (para montagem SMD)

`File → Fabrication Outputs → Component Placement`

Gera arquivo `.csv` com posição X/Y e rotação de cada componente SMD.

### Verificação final antes de enviar

- [ ] DRC sem erros
- [ ] Board outline em `Edge.Cuts`
- [ ] Mínimo de clearance atende às regras da fabricante
- [ ] Silkscreen não sobrepõe pads expostos
- [ ] Furos de montagem incluídos
- [ ] Layers corretos no Gerber
- [ ] Drill file incluído
- [ ] Visualizar no Gerber Viewer da fabricante (JLCPCB, PCBWay têm viewers online)

---

## Recursos de Estudo

- **Documentação oficial**: [docs.kicad.org](https://docs.kicad.org)
- **KiCad Forum**: [forum.kicad.info](https://forum.kicad.info)
- **Phil's Lab** (YouTube): tutoriais excelentes de KiCad e design de PCB
- **Robert Feranec** (YouTube): design para manufatura, EMC, alta velocidade
- **Contextual Electronics** (YouTube/curso): projetos práticos com KiCad

---

*Guia criado em 2026-04-30 — KiCad 10*
