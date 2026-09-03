# ARCHITECTURE - Spy Dashboard (spy-renda-extra)

> Estrutura tecnica do dashboard. Referencia para quem vai modificar o codigo.

## Visao geral

Arquivo unico `index.html` (~271 KB, ~3130 linhas). Tudo inline: CSS (linhas 7-492), HTML (linhas 494-817), JS (linhas 819-3126). Sem build step, sem bundler, sem framework.

Dependencias externas: Google Fonts (Inter + JetBrains Mono) via CSS import.

## CSS (linhas 7-492)

### Custom Properties (`:root`)
Dark theme por default. Light theme via `@media(prefers-color-scheme:light)` e `[data-theme="dark"]` override.

Variaveis principais:
- `--bg-0:#06090f` a `--bg-4:#223055`: backgrounds (do mais escuro ao mais claro)
- `--text-1:#eaf0fa` a `--text-4:#3d4f6e`: textos (do mais claro ao mais opaco)
- `--accent`, `--accent-2`, `--accent-bg`: azul primario
- `--green/yellow/red/orange/purple/cyan/pink` + `-bg`: cores semanticas
- `--sidebar-w: 240px`: largura da sidebar
- `--header-h: 60px`: altura do topbar

### Layout
- `.app`: flex container, min-height 100vh
- `.sidebar`: `position:fixed`, top/left/bottom 0, largura `--sidebar-w`, z-index 100
- `.main`: `margin-left: var(--sidebar-w)`, flex:1
- `.topbar`: `position:sticky`, top 0, z-index 50
- `.content`: padding 24px, contem as sections
- `.section`: display:none por default, `.section.active` = display:block

### Responsividade
4 breakpoints:
- **900px**: grids reduzem colunas, charts-grid 1col
- **768px**: sidebar esconde com `translateX(-100%)`, `.main` perde margin-left, burger aparece
- **600px**: kpi-grid 2col, detail-grid 1col, ic-kpi-grid 2col
- **480px**: heatmap cell menor (12px), tabelas font-size menor

### Classes de componentes (principais)
- `.kpi` / `.kpi-grid`: cards de metricas
- `.detail-card` / `.detail-grid`: cards de pagina
- `.table-card` / `.table-wrap`: tabelas com scroll
- `.ranking`, `.heatmap`, `.heatmap-cell`: overview
- `.tl` / `.tl-item` / `.tl-date` / `.tl-body` / `.tl-tags`: timeline
- `.kw-card` / `.kw-grid` / `.kw-chip`: keywords
- `.feed` / `.feed-item`: activity feed
- `.ic-kpi-grid` / `.ic-table-wrap`: Intel Criativa
- `.intel-card` / `.intel-grid`: Intel Competitiva
- `.media-gallery` / `.media-thumb` / `.mt-play` / `.mt-badge`: Projetos (galeria)
- `.evo-page` / `.evo-bar-row` / `.delta-grid`: Evolucao
- `.cc-panel` / `.cc-section`: CopyCat panels
- `.ad-act` / `.ad-dropdown` / `.ad-dropdown-menu`: acoes por ad
- `.status-chip` / `.status-escalado` / `.status-teste` / `.status-lancamento`: badges
- `.score-bar` / `.score-fill`: barras de score
- `.filter-btn` / `.time-btn` / `.time-filters`: filtros da tabela
- `.toast`: notificacoes
- `.video-modal-overlay` / `.video-modal`: modal de video
- `.niche-chip` / `.niche-lang-btn`: keywords de nicho
- `.ai-panel`: painel de IA

## HTML (linhas 494-817)

### Estrutura
```
div.app
  aside.sidebar#sidebar
    div.sidebar-brand (logo "SPY" + titulo "Spy Dashboard")
    nav.sidebar-nav
      [4 nav-sections: Principal, Analise, Discovery, Sistema]
      [15 nav-items com data-section="..."]
    div.sidebar-footer (versao + operador + data update)
  div.main
    header.topbar
      div.topbar-left (burger + topbar-title)
      div.topbar-right (pills: ATIVO, 4x/DIA, data)
    div.content
      [15 sections com id="sec-{nome}"]
```

### Tabs (sections)
| Secao sidebar | data-section | id HTML | Render |
|---|---|---|---|
| **Principal** | | | |
| Overview | overview | sec-overview | KPIs + Insights + Ranking + Heatmap (inline HTML) |
| Paginas | pages | sec-pages | renderPageDetails() |
| Ads Coletados | ads | sec-ads | renderAdsTable(allAds) |
| Projetos | projetos | sec-projetos | renderProjetosTab() |
| Evolucao | evolucao | sec-evolucao | renderEvolucaoTab() |
| **Analise** | | | |
| Graficos | charts | sec-charts | renderCharts() (4 canvas) |
| Timeline | timeline | sec-timeline | Inline HTML (hardcoded) |
| Intel Criativa | reports | sec-reports | renderIntelCriativa() |
| Atividade | activity | sec-activity | Inline HTML (feedItems array) |
| Intel Competitiva | intel | sec-intel | controleData + pipelineSteps + heuristics (inline) |
| **Discovery** | | | |
| Concorrentes | discovery | sec-discovery | DISCOVERIES array (inline) + Auto-Discovery |
| Keywords | keywords | sec-keywords | KEYWORDS array (inline) + Niche Keywords |
| **Extras** | | | |
| Auto-Discovery | auto-discovery | sec-auto-discovery | initAutoDiscovery() |
| Extrair VSL | vsls | sec-vsls | (VTurb extractor) |
| Swipe de VSLs | vsl-swipe | sec-vsl-swipe | (HLS.js player) |
| **Sistema** | | | |
| Configuracoes | config | sec-config | (Supabase config fields) |

## JS - Arrays de dados (linhas 819-1277)

### PAGES (linha 819) - 12 anunciantes
```js
{ name:'Elida Empreendedora', fullName:'Elida Empreendedora', pageId:'100063920862282',
  domain:'inlead.digital/teste-2-quiz-reduzido/', ads:18, dias:0, oldest:'06/ago/2026',
  firstSeen:'21/ago/2026', videos:18, prints:18, status:'escalado', label:'ESCALADO',
  score:217, plat:'FB', obs:'Quiz + VSL', color:'#f59e0b',
  adIds:['id1','id2',...], dates:['06/ago/2026','06/ago/2026',...] }
```
Campos-chave: `score` = Scale Score pre-calculado, `status` = 'escalado'|'teste'|'pausado', `adIds[]` = IDs dos ads coletados, `dates[]` = datas de inicio (1:1 com adIds).

### KEYWORDS (linha 842) - 35 keywords
```js
{ text:'renda extra', freq:64, status:'done'|'pending', results:24000 }
```

### DISCOVERIES (linha 877) - 13 concorrentes
```js
{ name:'Ricieri Rosset', domain:'ricieri.com.br', dias:390, tier:1,
  match:'ALTISSIMO', mecanismo:'Quiz + VSL', copy:'texto do copy...',
  keywords:10, color:'#22c55e', bib:'https://facebook.com/ads/library/...' }
```
Tiers: 1=match forte, 2=solido, 3=mega escala, 4=recente.

### Supabase Integration (linhas 893-975)
```js
const SUPABASE_BASE = 'https://kykudpqqrkigbpkkyren.supabase.co/storage/v1/object/public/spy-media';
const PAGE_SLUGS = { 'Elida Empreendedora':'elida-dias', 'For Traders Global':'for-traders', ... };
const EXISTING_MEDIA = new Set(['elida-dias/1234567890.jpg', 'elida-dias/1234567890.mp4', ...]);
const BLOCKED_ADS = new Set(['1005100855319047','1027821252333070']);
const SLUG_TO_PAGE = {}; // reverse mapping (computed)
const MEDIA_BY_PROJECT = {}; // computed from EXISTING_MEDIA, groups by advertiser
```

Funcoes helper:
- `mediaUrl(page, adId, ext)` - constroi URL se o arquivo existe em EXISTING_MEDIA
- `safeAdUrl(page, adId)` - URL do Ad Library se media nao existe
- `bestMediaUrl(page, adId)` - retorna jpg, mp4, ou null

### PAGE_INTEL (linha 976) - 12 entries
```js
{ formato:'Video quiz', angulo:'curiosidade/transformacao', beneficios:['renda extra','autonomia'] }
```

### AD_INTEL (linha 990) - 6 entries
```js
{ hookType:'pattern-interrupt', formato:'Video 30-60s', angulo:'curiosidade',
  notas:'Abre com pergunta direta, pico de curiosidade nos primeiros 3s' }
```

### VIDEO_HOOKS (linha 998) - 232 hooks
```js
{ '1005100855319047': 'Irmao, pelo amor de Deus, nao pula esse video...', ... }
```
Mapeamento ad ID -> texto do hook falado. Transcritos via Whisper (Groq API).

### EVO_DATA (linha 1247) - 6 de 12 paginas populadas
```js
{ 'Elida Digital': { unicos:6, entradas:0, ads:[['adId', dias, variantes], ...] }, ... }
```
Paginas populadas: Elida Digital, Elida Empreendedora, Rafaela Chagas, Natalia Beauty, For Traders Global. Restantes pendentes (task P0.4).

### COPYCAT_DATA (distribuido nas linhas da tabela)
Dados de ads coletados do CopyCat Ads. Acessado como `COPYCAT_DATA[adId]`:
```js
{ texto:'...', titulo:'...', desc:'...', url:'...', cta:'Saiba mais',
  adv:{ nome:'...', cat:'...', ig:'...' } }
```

### RENDA_EXTRA_KW (linha 2998) - Keywords multilang
```js
{ pt:['renda extra','ganhar dinheiro online',...], en:[...], es:[...] }
```
15 keywords por idioma. Usadas na tab Keywords (chips clicaveis).

### AI_PROMPTS (linha 3083)
9 funcoes que geram prompts para colar no Claude/ChatGPT:
`keywords_form`, `keywords_sel`, `segmentacao`, `analise_copy`, `teste_ab`, `oferta`, `avatar`, `angulos`, `quiz`.

## JS - Funcoes principais (linhas 1278-3126)

### Helpers
- `escHtml(s)` - escape HTML entities
- `showToast(msg, type)` - toast notification (3s auto-dismiss)
- `computeDias(p)` - calcula dias do ad mais antigo (formato DD/mmm/AAAA)
- `computeDiasCC(inicio)` - similar para copycats (formato DD/MM/AAAA)
- `mediaUrl(page, adId, ext)` - URL do Supabase se arquivo existe
- `safeAdUrl(page, adId)` - URL do Ad Library como fallback
- `bestMediaUrl(page, adId)` - melhor media disponivel

### Construcao do allAds (linha 1660-1681)
Transforma `PAGES[]` em `allAds[]` (flat array, um objeto por ad):
```js
{ page, id, date, dateObj, dias, type, domain, pageId, color, vars }
```
Parsing de data: aceita 'DD/mmm/AAAA' (3 partes) ou 'DD/mmm' (2 partes, assume ano atual).

### Render do Overview (inline no HTML)
- KPIs: Total Ads, Paginas, Videos, Scale Score medio
- Insights banner: analise automatica
- Ranking table: paginas ordenadas por score
- Heatmap: ultimos 30 dias de lancamentos

### renderPageDetails() / renderPages()
Cards por anunciante com: score badge, status chip, metricas, info rows, links (Ad Library, Dominio, Videos).

### renderAdsTable(ads) (linha 1685)
Tabela completa de ads com:
- Filtros compostos (`_applyFilters()`): pagina + tempo + busca + ordenacao
- Colunas: Pagina, Ad ID, Data Inicio, Dias Ativo, Vars, Tipo, Dominio, Media, Acoes, Detalhes
- Time filters: 3d, 5d, 1sem, 2sem, 3sem, 4sem, 60d, 100d+
- Sort clicavel em headers

### renderRows(ads) (linha 1781)
Renderiza as linhas da tabela. Cada linha tem: thumbnail, video link, Ad Library link, menu de acoes (copiar texto/titulo/desc/CTA/link, compartilhar WhatsApp, gerar IA), botao "+ Info".

### renderProjetosTab() (linha ~1548)
Galeria de videos por anunciante escalado. Filtros ativo/historico. Thumbnails do Supabase. Click abre video modal.

### renderEvolucaoTab()
KPIs de evolucao (novos, removidos, veteranos, taxa de sobrevivencia) + barras por pagina com classificacao automatica (cores: verde=veterano, azul=campeao, amarelo=validando, laranja=teste).

### renderCharts()
4 graficos canvas: Ads por Pagina, Videos por Pagina, Scale Score, Dias Monitorando.

### renderIntelCriativa() (IIFE)
Ranking de ads por longevidade com filtros (status, formato, pagina). KPIs. Tabela com: rank, ad ID, pagina, dias, formato, status, hook text (do VIDEO_HOOKS). Padroes de hook por tipo. Cards por anunciante.

### Discovery (linha 2161)
Tabela de DISCOVERIES (13 concorrentes) + cards dos top tiers. Renderizado inline.

### Auto-Discovery (linhas 2800-2994)
Sistema independente com localStorage (`spy-discovery-data`). CRUD de anunciantes descobertos. Funcoes: `loadDiscoveryData()`, `saveDiscoveryData()`, `renderDiscoveryTable()`, `salvarNovoAnunciante()`, `toggleMonitorar()`, `editarAdCount()`, `removerDiscovery()`, `buscarAdLibrary()`.

### Keywords (linha 2201)
KEYWORDS array renderizado como cards. Niche Keywords multilang (RENDA_EXTRA_KW) com chips e busca Meta Ad Library.

### Intel Competitiva (linha 2214)
Sinais de controle por pagina (>30d = CONTROLE, 7-30d = validando, <7d = lancamento). 10 dados fundamentais (checklist). Pipeline W1 (12 passos). Heuristicas.

### CopyCat Features (linha 3004)
- `copiarTexto(adId, campo)` - copia texto/titulo/desc/CTA/link/tudo
- `compartilharAd(adId, pageName, dias)` - compartilha via WhatsApp
- `gerarIA(tipo, adId)` - gera prompt AI e copia pro clipboard
- `toggleAdMenu(e, menuId)` - menu dropdown de acoes por ad

### Video Modal
- `openVideoModal(vidUrl, thumbUrl, adId)` - abre modal de video
- `closeVideoModal()` - fecha modal

### Navegacao
Event listeners nos `.nav-item` que:
1. Escondem todas as `.section`
2. Mostram a section correspondente ao `data-section`
3. Atualizam `topbar-title`
4. Chamam render function se necessario (charts, projetos, evolucao, auto-discovery)

### Init (final do script)
- `renderRendaExtraKw('pt')` - carrega keywords PT
- `loadDiscoveryData()` - carrega discovery do localStorage
- DOMContentLoaded: adiciona versao indicator (`v27`)
- Badges: atualiza contadores na sidebar

## Fluxo de navegacao

```
1. Pagina carrega → execucao sequencial do script
   ├── PAGES, KEYWORDS, DISCOVERIES, SUPABASE config (constantes)
   ├── PAGE_INTEL, AD_INTEL, VIDEO_HOOKS, EVO_DATA (constantes)
   ├── renderCCPanel/renderFullPanel (funcoes definidas)
   ├── Ranking + Heatmap + Page Details (renderizados inline)
   ├── renderProjetosTab() (IIFE)
   ├── allAds[] construido a partir de PAGES[]
   ├── renderAdsTable(allAds) + filtros compostos
   ├── renderEvolucaoTab()
   ├── renderIntelCriativa() (IIFE)
   ├── Timeline + Activity Feed (inline HTML)
   ├── Discovery + Keywords + Intel Competitiva (inline)
   ├── Auto-Discovery: loadDiscoveryData()
   └── renderRendaExtraKw('pt')

2. Click em nav-item
   ├── Esconde todas as .section
   ├── Mostra a section do data-section
   ├── Atualiza topbar-title
   └── Chama render especial se necessario (charts, projetos, etc.)

3. Filtros na tabela de ads
   ├── Click em filter-btn → seta _pageF → _applyFilters()
   ├── Click em time-btn → seta _timeF → _applyFilters()
   ├── Input em search → seta _search → _applyFilters()
   └── Click em th[data-s] → seta _sort → _applyFilters()

4. _applyFilters()
   ├── Copia allAds[]
   ├── Aplica filtro de pagina (_pageF)
   ├── Aplica filtro de tempo (_timeF)
   ├── Aplica busca (_search)
   ├── Aplica ordenacao (_sort) ou sort default por contexto
   └── renderRows(resultado)
```

## Supabase Integration

- **Project URL:** `https://kykudpqqrkigbpkkyren.supabase.co`
- **Bucket:** `spy-media` (publico, sem auth necessaria para leitura)
- **Estrutura do bucket:** `spy-media/{slug}/{adId}.{ext}` onde ext = jpg ou mp4
- **Slugs:** mapeados em `PAGE_SLUGS` (ex: `'Elida Empreendedora':'elida-dias'`)
- **EXISTING_MEDIA:** Set com centenas de paths que existem no bucket (verificacao local, sem roundtrip)
- **Acesso:** URLs publicas, nao precisa de auth key para leitura
- **Upload:** feito manualmente via Supabase dashboard ou script (nao pelo dashboard)
