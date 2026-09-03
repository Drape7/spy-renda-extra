# HANDOFF - Spy Dashboard (spy-renda-extra)

> Cole este documento inteiro na primeira mensagem do novo Claude.
> Ultima atualizacao: 2026-09-03

## QUEM

Tiago Felipe de Souza. Copywriter Senior DR / Trafego Meta / Creative Strategist.
Santa Fe do Sul, SP. PT-BR, conciso, direto.

## O QUE E

**Spy Dashboard** e um dashboard de espionagem criativa para o nicho **Renda Extra BR**. Monitora 12 anunciantes brasileiros no Meta Ads, cataloga seus ads, preserva videos/thumbnails no Supabase, transcreve hooks falados via Whisper, e oferece ferramentas de analise competitiva (Intel Criativa, Evolucao, Discovery, Keywords).

O proposito e mapear, dissecar e comparar as estrategias criativas dos maiores anunciantes do nicho renda extra no Brasil, identificando controles (ads com 100+ dias = gasto de midia confirmado), padroes de hook, e oportunidades.

Construido como arquivo HTML unico (~271 KB, ~3130 linhas), tudo inline (CSS + JS + dados). Sem dependencias externas alem do Google Fonts (Inter + JetBrains Mono).

## ONDE

- **Repo:** https://github.com/Drape7/spy-renda-extra
- **Live (GitHub Pages):** https://drape7.github.io/spy-renda-extra/
- **Local:** `C:\base_conhecimento\raw\swipefile\renda extra\renda-extra-white\Spy atual\spy-renda-extra\index.html`
- **Arquivo:** `index.html` (unico, ~271 KB, ~3130 linhas, tudo inline)
- **Dev server:** porta 8765, config em `.claude/launch.json` (Python http.server)
- **Supabase:** `https://kykudpqqrkigbpkkyren.supabase.co`, bucket `spy-media` (publico)

## ESTADO ATUAL (2026-09-03)

### Features implementadas (62 commits)
- Dashboard completo com 15 tabs na sidebar (4 secoes: Principal, Analise, Discovery, Sistema)
- 12 anunciantes monitorados (PAGES array) com score, status, ads, datas, videos
- Scale Score formula: `(ads x 2) + (variacoes x 1.5)`
- Tabela de ads com filtros compostos (pagina + tempo + busca + ordenacao)
- Supabase media storage: videos (.mp4) + thumbnails (.jpg) no bucket `spy-media`
- 232 hooks falados transcritos via Whisper (VIDEO_HOOKS)
- Intel Criativa: ranking de ads por longevidade com filtros por status/formato/pagina
- Evolucao de Criativos: lifecycle de ads com classificacao automatica (Veterano 100+d / Campeao 30-99d / Validando 7-29d / Teste <7d)
- Projetos: galeria de videos por anunciante escalado, com filtro ativo/historico
- Graficos canvas (Ads por Pagina, Videos por Pagina, Scale Score, Dias Monitorando)
- Timeline: historico de rondas com tags
- Activity Feed: 14 eventos cronologicos
- Intel Competitiva: sinais de controle + 10 dados fundamentais + pipeline W1 (12 passos)
- Discovery: 13 concorrentes por tier/match + Auto-Discovery com localStorage
- Keywords: 35 keywords buscadas + Niche Keywords multilang (PT/EN/ES) com chips clicaveis
- CopyCat features: Copy/Share/Translate por ad, AI prompts (keywords, segmentacao, analise, A/B, oferta, avatar, angulos, quiz)
- VSLs: 2 tabs (Extrair VSL + Swipe de VSLs com HLS.js player)
- Video modal para preview de ads
- Responsividade (breakpoints: 900px, 768px, 600px, 480px)
- Dark theme default + light theme support
- Versao: v27 (indicator no canto inferior direito)

### Os 12 anunciantes
| # | Nome | Score | Status | Ads | Dias |
|---|------|-------|--------|-----|------|
| 1 | Rafael Bottrel | 420 | ESCALADO | 420 | - |
| 2 | Elida Empreendedora | 217 | ESCALADO | 18 | - |
| 3 | For Traders Global | 184 | ESCALADO | 85 | 424 |
| 4 | Gabriel Navaro | 99 | ESCALADO | 99 | - |
| 5 | Hytallo Soares | 92 | ESCALADO | 38 | - |
| 6 | Rafaela Chagas | 91 | ESCALADO | 26 | 419 |
| 7 | Ricieri Rosset | 62 | ESCALADO | 62 | 116 |
| 8 | Soares Hytallo | 54 | ESCALADO | 54 | - |
| 9 | Natalia Beauty | 32 | TESTE | 12 | - |
| 10 | Guilherme Cirilo | 23 | TESTE | 23 | - |
| 11 | Elida Digital | 16 | ESCALADO | 6 | - |
| 12 | GN Investimentos | 15 | TESTE | 15 | - |

### O que falta fazer
- **P0.3** (em progresso): Atualizar EXISTING_MEDIA com bucket Supabase (reconciliar o Set local com o que realmente existe no bucket)
- **P0.4** (pendente): Completar EVO_DATA para as 12 paginas (atualmente so 6 de 12 estao populadas: Elida Digital, Elida Empreendedora, Rafaela Chagas, Natalia Beauty, For Traders Global, e parcialmente Hytallo)
- PAGE_INTEL e AD_INTEL tem dados para apenas 6 e 6 paginas/ads respectivamente
- 2 ads bloqueados (BLOCKED_ADS) por verificacao de cookies do Meta

### Bugs conhecidos
- Nenhum bug aberto no momento.

## COMO CONTINUAR

### Para rodar localmente
```bash
cd "C:\base_conhecimento\raw\swipefile\renda extra\renda-extra-white\Spy atual\spy-renda-extra"
python -m http.server 8765
```
Abrir `http://localhost:8765` no browser.

### Padrao do codigo
- Tudo num unico `index.html` (CSS no `<style>`, JS no `<script>`)
- CSS usa custom properties (`:root` vars) com suporte dark/light theme
- Dados em arrays/objects JS constantes no topo do script
- Cada tab e uma `<div class="section" id="sec-{nome}">` no HTML
- Navegacao: click em `.nav-item[data-section]` mostra a section correspondente e esconde as demais
- Filtros compostos na tabela de ads: `_pageF`, `_timeF`, `_search`, `_sort` passam por `_applyFilters()`
- Media via Supabase: `mediaUrl(page, adId, ext)` constroi URL do bucket publico
- `EXISTING_MEDIA` (Set) mapeia quais arquivos existem no bucket (formato: `slug/adId.ext`)
- `PAGE_SLUGS` mapeia nome da pagina para slug de URL (ex: `'Elida Empreendedora':'elida-dias'`)
- Discovery tab usa localStorage (`spy-discovery-data`) para persistir dados

### Para adicionar um novo anunciante
1. Adicionar objeto ao array `PAGES` (linha ~819) seguindo o schema:
```js
{ name:'Nome', fullName:'Nome Completo', pageId:'META_PAGE_ID',
  domain:'dominio.com', ads:50, dias:0, oldest:'DD/mmm/AAAA',
  firstSeen:'DD/mmm/AAAA', videos:10, prints:10, status:'escalado'|'teste',
  label:'ESCALADO'|'TESTE', score:100, plat:'FB', obs:'Observacoes',
  color:'#hex', adIds:['id1','id2'], dates:['DD/mmm/AAAA','DD/mmm'] }
```
2. Adicionar slug em `PAGE_SLUGS` (linha ~894)
3. Upload de media no Supabase bucket `spy-media` na pasta `slug/`
4. Adicionar paths em `EXISTING_MEDIA` (linha ~896)
5. (Opcional) Adicionar em `EVO_DATA` (linha ~1247), `PAGE_INTEL` (~976), `VIDEO_HOOKS` (~998)

### Para commit e deploy
```bash
cd "C:\base_conhecimento\raw\swipefile\renda extra\renda-extra-white\Spy atual\spy-renda-extra"
git add index.html
git commit -m "feat: descricao"
git push origin main
```
GitHub Pages atualiza automaticamente em ~1 min.

## REGRAS

- **REGRA ZERO:** O projeto original (o `index.html` em `Spy atual/spy-renda-extra/`) NAO e tocado fora do fluxo normal de trabalho. Este arquivo e o dashboard ativo.
- **R1:** NUNCA modificar `/raw` (material bruto imutavel na base_conhecimento). O `index.html` do spy-renda-extra esta DENTRO de `/raw/` mas e uma excecao funcional (e o dashboard ativo, nao material bruto).
- **R7:** Nunca apagar arquivos. Arquivar em `/ARCHIVE/` com timestamp.
- **gh CLI NAO esta instalado** nesta maquina. Usar `git push` direto.

## ARQUIVOS RELACIONADOS

- `C:\base_conhecimento\raw\swipefile\renda extra\renda-extra-white\Spy atual\spy-renda-extra\index.html` - o dashboard completo
- `C:\base_conhecimento\raw\swipefile\renda extra\renda-extra-white\Spy atual\spy-renda-extra\.claude\launch.json` - config do dev server (port 8765)
- `C:\base_conhecimento\raw\swipefile\renda extra\renda-extra-white\Spy atual\spy-renda-extra\docs\` - esta documentacao
- `C:\base_conhecimento\raw\swipefile\renda extra\renda-extra-white\Spy atual\HANDOFF-SESSAO.md` - handoff historico (versao anterior, fora do repo)
- `C:\base_conhecimento\raw\swipefile\renda extra\renda-extra-white\Spy atual\_MANUAL-SISTEMA-SPY.md` - manual do sistema (versao anterior, fora do repo)
- Supabase bucket: `https://kykudpqqrkigbpkkyren.supabase.co/storage/v1/object/public/spy-media/` (videos + thumbs dos 12 anunciantes)

## HISTORICO DE SESSOES

O dashboard foi construido ao longo de multiplas sessoes entre agosto e setembro de 2026. 62 commits no total. Comecou como v13 (primeiro commit rastreado) e evoluiu ate v27. Ver `docs/BUILD-HISTORY.md` para o historico completo commit a commit.

### Marcos principais
- v13-v17: Dashboard base (tabela de ads, KPIs, heatmap, navegacao)
- v18-v20: VSLs tab com Vturb download + Supabase integration
- v21-v22: Fix de media, Ad Library URLs
- Ronda inaugural: 5 paginas, 94 videos, 940MB no Supabase
- CopyCat Ads: AI analysis, Niche Keywords, Copy/Share/Translate
- 7 novas paginas adicionadas (total 12 anunciantes)
- Composable filters, dynamic dias calculation
- Projetos tab (galeria de videos por anunciante)
- Evolucao rewrite (lifecycle dinamico)
- Intel Criativa (ranking por longevidade + VIDEO_HOOKS 232 hooks via Whisper)
