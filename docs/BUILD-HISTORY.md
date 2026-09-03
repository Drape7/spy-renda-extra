# BUILD HISTORY - Spy Dashboard (spy-renda-extra)

> Historico completo de construcao. Cada commit expandido com contexto.
> 62 commits, construido ao longo de multiplas sessoes (ago-set 2026).

---

## Fase 1: Dashboard Base (v13-v17)

### Commit: `ca49327`
**v13: historico por dia + descricoes KPI + fix link storage**

Primeiro commit rastreado no repo. Dashboard ja existia como artifact, migrado para GitHub.
- Tabela de ads com historico por dia
- KPIs no overview (Total Ads, Paginas, Videos, etc.)
- Links do Supabase storage para media

---

### Commit: `59110ea`
**v14: videos internos + filtro escala**

- Videos renderizados internamente (sem abrir nova aba)
- Filtro "Escala (100d+)" na tabela de ads — criativos com 100+ dias = retorno confirmado

---

### Commit: `ea529fc`
**v15: filtros duplicados + qtde ads**

- Filtro "Duplicados" — ads com mais de 1 variante rodando (anunciante testando versoes)
- Filtro "Qtde ADs" — ordena por pagina com mais ads ativos

---

### Commit: `40adc48`
**v16: fix navegacao sidebar apos KPI click**

Bug: clicar em KPI cards do overview quebrava a navegacao da sidebar.
Fix: click handler dos KPIs nao interfere mais com o sistema de navegacao.

---

### Commit: `060bfa6`
**v17: descricao no heatmap + fix navegacao KPI (v16)**

- Adicionada descricao explicativa no heatmap (mapa de lancamentos ultimos 30 dias)
- Fix complementar da navegacao pos-KPI click

---

## Fase 2: VSLs + Supabase (v18-v22)

### Commit: `41da171`
**v18: add VSLs tab + Supabase spy_vsls table integration**

- Nova tab "VSLs" na sidebar
- Integracao com tabela `spy_vsls` no Supabase
- Listagem de VSLs por anunciante

---

### Commit: `43f3b3d`
**v19: fix Supabase anon key typo in VSLs tab**

Bug: anon key do Supabase estava com typo.
Fix: corrigido o valor da key.

---

### Commit: `9b756a9`
**v20: VSLs tab with Vturb video download**

- Download de videos VTurb/ConverteAI direto do player
- Extracao de configuracao do player (pitchTime, fakeBar, etc.)

---

### Commit: `85fbf10`
**v20 fix: duracao correta via m3u8 + retry download + charset utf-8**

- Duracao dos videos agora extraida do m3u8 (HLS manifest)
- Retry automatico em downloads que falham
- Charset UTF-8 no documento

---

### Commit: `0080822`
**v21 fix: media index estatico - corrige Ver Video 404**

Bug: botoes "Ver Video" davam 404 porque o index de media era dinamico.
Fix: EXISTING_MEDIA como Set estatico no codigo (lista todos os arquivos que existem no bucket).

---

### Commit: `6da2a01`
**v22 fix: Ad Library URL params + version indicator**

- URLs da Ad Library com parametros corretos (`country=BR`, `view_all_page_id`)
- Indicador de versao no canto inferior direito

---

## Fase 3: Media + CopyCat + Ads Removidos

### Commit: `ad0b884`
**fix: ads removidos pelo Meta mostram 'Pagina' ao inves de link morto**

Ads removidos da Biblioteca do Meta agora mostram link para a pagina do anunciante em vez de link quebrado.

---

### Commit: `027bcd0`
**fix: protect all ad links from broken URLs on removed ads**

Protecao adicional: todos os links de ads verificam se o ad ainda existe antes de montar a URL.

---

### Commit: `286e084`
**feat: add Ver Criativo buttons linking to Supabase media in Evolucao tab**

Botoes "Ver Criativo" na tab Evolucao que abrem video/thumbnail direto do Supabase.

---

### Commits: `12eee3a`, `ce36cfb`, `d786b56`, `15daca9`
**VSLs tab rewrite series**

1. `12eee3a` — Reorganizar aba por anunciante com transcricao expansivel
2. `ce36cfb` — Swipe section com HLS.js video player + transcricoes
3. `d786b56` — Botoes de download nos cards do swipe
4. `15daca9` — Split em 2 tabs: "Extrair VSL" (extrator VTurb) + "Swipe de VSLs" (player HLS)

---

## Fase 4: Expansao para 12 Anunciantes

### Commit: `a9e99c8`
**feat: add 5 new advertisers + fix pageIds + auto-discovery keywords**

Adicionados 5 novos anunciantes ao PAGES array:
- Rafael Bottrel, Gabriel Navaro, GN Investimentos, Hytallo Soares, Soares Hytallo
- PageIds corrigidos para anunciantes existentes
- Keywords de auto-discovery atualizadas

---

### Commit: `ddcabbb`
**feat: add Ricieri Rosset (page_id 371228812738108) + dynamic page count**

- 12o anunciante adicionado: Ricieri Rosset
- Contagem de paginas agora dinamica (nao hardcoded)

---

### Commit: `c2b3bcb`
**fix: filter buttons only show pages with collected ad IDs**

Botoes de filtro por pagina so mostram paginas que tem ads coletados (adIds nao vazio).

---

### Commit: `bd2d5a8`
**fix: atualizar GN Investimentos ads count (22->15) e verificar 7 novas**

Correcao de contagem de ads do GN Investimentos. Verificacao das 7 novas paginas.

---

### Commit: `9443e0f`
**feat: popular adIds das 7 novas paginas (146 ads unicos)**

Todos os 146 ad IDs das 7 novas paginas adicionados aos arrays `adIds[]` e `dates[]`.

---

### Commit: `d9b2aae`
**feat: add 7 new pages media (197 entries) + update video counts**

197 novos arquivos de media no EXISTING_MEDIA Set (videos + thumbnails das 7 novas paginas).

---

## Fase 5: Fixes Criticos

### Commit: `9ce985a`
**fix: SINAL dinamico + retry media completo (227/228 uploads)**

- SINAL (indicador de status do ad) agora calculado dinamicamente
- 227 de 228 arquivos de media no Supabase (99.6% coverage)

---

### Commit: `c947c6f`
**fix: replace misleading Removido label with Bloqueado/Nao coletado**

Labels mais precisas: "Removido" era enganoso (o ad pode existir mas nao foi coletado). Agora: "Bloqueado" (verificacao de cookies do Meta) ou "Nao coletado".

---

### Commit: `50ada02`
**feat: add Copycat features (AI analysis, Niche Keywords, Copy/Share/Translate)**

Feature grande. Integracao com CopyCat Ads:
- Dados de texto dos ads (titulo, desc, CTA, URL, info do anunciante)
- Painel "+ Info" expandivel por ad
- Menu de acoes: Copiar Texto, Copiar Titulo, Copiar Desc, Copiar CTA, Copiar Link, Copiar Tudo, Compartilhar WhatsApp
- Prompts de IA: Keywords, Segmentacao, Analise de Copy, Teste A/B, Oferta, Avatar, Angulos, Quiz
- Niche Keywords multilang (PT/EN/ES)

---

### Commit: `52e6eae`
**fix: corrigir 22 ads Rafaela Chagas mostrando Bloqueado**

Bug: 22 ads da Rafaela mostravam "Bloqueado" indevidamente.
Fix: corrigido o mapeamento de media.

---

### Commit: `37f06af`
**fix: composable filters - time + page + search + sort all combine**

Bug: filtros da tabela nao combinavam (aplicar tempo resetava pagina, etc.).
Fix: sistema de filtros compostos (`_applyFilters()`) com variaveis de estado separadas (`_pageF`, `_timeF`, `_search`, `_sort`).

---

### Commit: `533dfc6`
**docs: adicionar descricao do sistema de filtros compostos**

Documentacao inline no codigo descrevendo o sistema de filtros compostos.

---

### Commit: `6695140`
**fix: corrigir datas sem ano (DD/mmm) nas 7 paginas novas + parser resiliente**

Bug: datas no formato "DD/mmm" (sem ano) quebravam o calculo de dias.
Fix: parser aceita 2 ou 3 partes, assume ano atual para 2 partes, e se a data resultante for no futuro, subtrai 1 ano.

---

### Commit: `ec1266c`
**fix: replace scrape dates with real Ad Library start dates for 5 pages**

Bug: datas de ads eram a data do scrape, nao a data real de inicio do ad.
Fix: substituidas por datas reais extraidas da Ad Library.

---

### Commit: `2d31f2a`
**feat: merge Niche Keywords into Keywords tab (keep only Renda Extra)**

Niche Keywords (multilang PT/EN/ES) integradas diretamente na tab Keywords. Tab separada removida.

---

### Commit: `e22d487`
**fix: dynamic dias calculation - remove all hardcoded dates**

Calculo de dias agora 100% dinamico a partir das datas dos ads. Zero datas hardcoded.

---

### Commit: `1a04e6d`
**fix: add missing ricieri-rosset/1389415556426654.mp4 to EXISTING_MEDIA**

1 arquivo de media faltando adicionado ao Set.

---

### Commit: `b4c7ae5`
**fix: AD ID in +Info panel links to specific ad and shows bloqueado/nao coletado**

AD ID no painel +Info agora linka direto para o ad especifico na Ad Library. Status correto.

---

### Commit: `d4f2157`
**fix: correct Hytallo/Soares dates from scrape date to oldest field value**

Correcao de datas de Hytallo Soares e Soares Hytallo.

---

### Commits: `4991dce`, `d2253b8`
**Updates de ronda**

- `4991dce` — Update PAGES array com dados da ronda 13 (24/ago)
- `d2253b8` — Fix dominio Empreendedora para `inlead.digital/teste-2-quiz-reduzido/`

---

### Commit: `d7abe3f`
**fix: rename Elida Dias -> Elida Empreendedora + show full names on filters**

Rename da pagina principal. Filtros agora mostram nome completo.

---

## Fase 6: Projetos + Evolucao Rewrite

### Commits: `a6f236e`, `8e545bb`, `712dfd4`
**Projetos tab series**

1. `a6f236e` — Nova tab Projetos: galeria de media por anunciante (thumbnails + videos do Supabase)
2. `8e545bb` — Filtrar para somente videos de escalados
3. `712dfd4` — Incluir Elida Empreendedora e Elida Digital como escalados

---

### Commits: `d35a7cc`, `19fb022`, `808423f`
**Evolucao tab rewrite series**

1. `d35a7cc` — Rewrite completo: dados dinamicos, todas as 12 paginas, foco nos ultimos 7 dias
2. `19fb022` — Grid de ad IDs para os ultimos 7 dias
3. `808423f` — Mostrar todos os ad IDs por anunciante

---

## Fase 7: Intel Criativa + VIDEO_HOOKS

### Commit: `8cb8cf3`
**feat: redesign Relatorios tab into Intel Criativa**

Tab "Relatorios" redesenhada como "Intel Criativa":
- Ranking de ads por longevidade (dias ativos)
- KPIs: Total criativos, Veteranos (100+d), Campeoes (30-99d), Validando (7-29d), Teste (<7d)
- Filtros por status, formato, pagina
- Cards por anunciante com resumo

---

### Commit: `dd48192`
**feat: add VIDEO_HOOKS (232 spoken video hooks via Whisper) to Intel Criativa**

Feature significativa. 232 hooks de video transcritos via Whisper (Groq API):
- Cada hook mapeado por ad ID no objeto `VIDEO_HOOKS`
- Coluna "Hook" na tabela da Intel Criativa mostra o texto transcrito
- Secao "Padroes de Hook" agrupa hooks por tipo

---

### Commit: `2b1706d`
**feat: add TODOS filter to Intel Criativa status dropdown**

Filtro "TODOS" adicionado ao dropdown de status na Intel Criativa.

---

### Commit: `2d11bec` (HEAD)
**fix: corrigir transcricoes Whisper nos VIDEO_HOOKS**

Correcoes pontuais em transcricoes que tinham erros de ASR (automatic speech recognition).

---

## Decisoes de design

1. **Arquivo unico**: facilita deploy GitHub Pages, sem build step
2. **Dados inline**: PAGES/KEYWORDS/DISCOVERIES como constantes JS no arquivo
3. **EXISTING_MEDIA como Set estatico**: evita roundtrip ao Supabase para verificar se arquivo existe
4. **Canvas pra charts**: sem dependencia de chart library
5. **localStorage pra Auto-Discovery**: persiste no browser sem backend
6. **Supabase pra media**: bucket publico, URLs diretas, sem auth para leitura
7. **Dark theme default**: padrao pra ferramentas de trabalho
8. **Filtros compostos**: variaveis de estado separadas, funcao unica `_applyFilters()` aplica todos
9. **Scale Score**: formula `(ads x 2) + (variacoes x 1.5)` — pondera volume e teste
10. **VIDEO_HOOKS via Whisper**: Groq API (mais barato/rapido que OpenAI) para transcrever hooks falados
