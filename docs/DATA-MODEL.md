# DATA MODEL - Spy Dashboard (spy-renda-extra)

> Schema completo de todos os arrays de dados, fontes, e o que falta.

## PAGES (linha 819) - 12 anunciantes

Array de objetos. Cada objeto = 1 anunciante monitorado.

| Campo | Tipo | Descricao |
|---|---|---|
| name | string | Nome curto (ex: "Elida Empreendedora") |
| fullName | string | Nome completo |
| pageId | string | Meta Ad Library page ID (15-16 digitos) |
| domain | string | Dominio principal (ex: "inlead.digital/teste-2-quiz-reduzido/") |
| ads | number | Total de ads coletados |
| dias | number | Dias monitorando (0 = calculado dinamicamente) |
| oldest | string | Data do ad mais antigo (DD/mmm/AAAA) |
| firstSeen | string | Data da primeira deteccao |
| videos | number | Total de videos |
| prints | number | Total de prints/thumbnails |
| status | string | "escalado", "teste", ou "pausado" |
| label | string | "ESCALADO", "TESTE", "PAUSADO" |
| score | number | Scale Score = (ads x 2) + (variacoes x 1.5) |
| plat | string | Plataforma ("FB") |
| obs | string | Observacoes livres |
| color | string | Cor hex para graficos |
| adIds | string[] | Array de ad IDs coletados |
| dates | string[] | Array de datas de inicio (1:1 com adIds, formato DD/mmm/AAAA ou DD/mmm) |

**Os 12 anunciantes:**
| # | name | pageId | status | ads | score | color |
|---|------|--------|--------|-----|-------|-------|
| 1 | Elida Empreendedora | 100063920862282 | escalado | 18 | 217 | #f59e0b |
| 2 | For Traders Global | 100748915697920 | escalado | 85 | 184 | #3b82f6 |
| 3 | Rafaela Chagas | 100064093457556 | escalado | 26 | 91 | #ec4899 |
| 4 | Natalia Beauty | 100065036747754 | teste | 12 | 32 | #a855f7 |
| 5 | Elida Digital | 100064497898960 | escalado | 6 | 16 | #f97316 |
| 6 | Rafael Bottrel | (no PAGES) | escalado | 420 | 420 | #22c55e |
| 7 | Gabriel Navaro | (no PAGES) | escalado | 99 | 99 | #06b6d4 |
| 8 | GN Investimentos | (no PAGES) | teste | 15 | 15 | #64748b |
| 9 | Hytallo Soares | (no PAGES) | escalado | 38 | 92 | #f43f5e |
| 10 | Soares Hytallo | (no PAGES) | escalado | 54 | 54 | #8b5cf6 |
| 11 | Guilherme Cirilo | (no PAGES) | teste | 23 | 23 | #14b8a6 |
| 12 | Ricieri Rosset | 371228812738108 | escalado | 62 | 62 | #eab308 |

---

## KEYWORDS (linha 842) - 35 keywords

Array de objetos. Keywords buscadas na Meta Ad Library.

| Campo | Tipo | Descricao |
|---|---|---|
| text | string | Texto da keyword (ex: "renda extra") |
| freq | number | Frequencia de aparicao |
| status | string | "done" ou "pending" |
| results | number | Quantidade de resultados na Ad Library |

**Top keywords por resultados:**
| Keyword | Freq | Results |
|---|---|---|
| ganhar dinheiro | 33 | 50000 |
| renda extra | 64 | 24000 |
| trabalhar em casa | 18 | 15000 |
| ganhar dinheiro online | 22 | 12000 |
| dinheiro extra | 12 | 8000 |

Status: 19 de 35 buscadas ("done").

---

## DISCOVERIES (linha 877) - 13 concorrentes

Array de objetos. Concorrentes descobertos via pesquisa de keywords.

| Campo | Tipo | Descricao |
|---|---|---|
| name | string | Nome do concorrente |
| domain | string | Dominio |
| dias | number | Dias rodando ads |
| tier | number | 1-4 (1=match forte, 4=recente) |
| match | string | "ALTISSIMO", "MEDIO", "A VALIDAR", "BAIXO" |
| mecanismo | string | Mecanismo de venda (ex: "Quiz + VSL") |
| copy | string | Trecho da copy observada |
| keywords | number | Em quantas keywords apareceu |
| color | string | Cor hex |
| bib | string | URL da Ad Library |

**Por tier:**
- Tier 1 (match forte): Ricieri Rosset, Raphael Mancini
- Tier 2 (solido): varios
- Tier 3 (mega escala): varios
- Tier 4 (recente): varios

---

## Supabase Config (linhas 893-975)

### SUPABASE_BASE (linha 893)
```js
const SUPABASE_BASE = 'https://kykudpqqrkigbpkkyren.supabase.co/storage/v1/object/public/spy-media';
```

### PAGE_SLUGS (linha 894)
Mapeamento nome da pagina -> slug para URLs do Supabase.

| Name | Slug |
|---|---|
| Elida Empreendedora | elida-dias |
| For Traders Global | for-traders |
| Rafaela Chagas | rafaela-chagas |
| Natalia Beauty | natalia-beauty |
| Elida Digital | elida-digital |
| Rafael Bottrel | rafael-bottrel |
| Gabriel Navaro | gabriel-navaro |
| GN Investimentos | gn-investimentos |
| Hytallo Soares | hytallo-soares |
| Soares Hytallo | soares-hytallo |
| Guilherme Cirilo | guilherme-cirilo |
| Ricieri Rosset | ricieri-rosset |

### EXISTING_MEDIA (linha 896)
Set de strings. Cada string = path de um arquivo que existe no bucket Supabase.
Formato: `{slug}/{adId}.{ext}` onde ext = `jpg` ou `mp4`.

Centenas de entries. Usado por `mediaUrl()` para verificar existencia sem roundtrip ao Supabase.

Fonte: upload manual feito via scripts nas sessoes de coleta. Total ~950MB de media.

### BLOCKED_ADS (linha 912)
```js
const BLOCKED_ADS = new Set(['1005100855319047', '1027821252333070']);
```
2 ads bloqueados pelo Meta (verificacao de cookies). Nao podem ser acessados/baixados.

### SLUG_TO_PAGE
Mapeamento reverso, computado de PAGE_SLUGS. Slug -> nome da pagina.

### MEDIA_BY_PROJECT
Computado de EXISTING_MEDIA. Agrupa media por anunciante:
```js
{ 'Elida Empreendedora': { 'adId1': { jpg: true, mp4: true }, 'adId2': { mp4: true }, ... }, ... }
```

---

## PAGE_INTEL (linha 976) - 12 entries

Objeto. Mapeamento nome da pagina -> intel resumida.

| Campo | Tipo | Descricao |
|---|---|---|
| formato | string | Formato dominante (ex: "Video quiz") |
| angulo | string | Angulo principal (ex: "curiosidade/transformacao") |
| beneficios | string[] | Lista de beneficios (ex: ["renda extra", "autonomia"]) |

Populado para todas as 12 paginas, mas com dados basicos.

---

## AD_INTEL (linha 990) - 6 entries

Objeto. Mapeamento ad ID -> intel detalhada do ad.

| Campo | Tipo | Descricao |
|---|---|---|
| hookType | string | Tipo de hook (ex: "pattern-interrupt") |
| formato | string | Formato (ex: "Video 30-60s") |
| angulo | string | Angulo (ex: "curiosidade") |
| notas | string | Notas de analise |

Populado para apenas 6 ads (amostra).

---

## VIDEO_HOOKS (linha 998) - 232 hooks

Objeto. Mapeamento ad ID -> texto do hook falado.

```js
{ '1005100855319047': 'Irmao, pelo amor de Deus, nao pula esse video...',
  '1234567890123456': 'Voce sabia que da pra ganhar dinheiro...',
  ... }
```

232 entries. Transcritos via Whisper API (Groq, modelo whisper-large-v3).
Fonte: audio extraido dos videos do Supabase + transcricao automatica.
Usado na tab Intel Criativa (coluna "Hook") e na secao "Padroes de Hook".

---

## EVO_DATA (linha 1247) - 6 de 12 paginas

Objeto. Dados de evolucao por anunciante.

| Campo | Tipo | Descricao |
|---|---|---|
| unicos | number | Total de ads unicos |
| entradas | number | Ads novos no periodo |
| ads | array[] | Array de [adId, dias, variantes] |

**Paginas populadas:**
1. Elida Digital (6 ads)
2. Elida Empreendedora (18 ads)
3. Rafaela Chagas (26 ads)
4. Natalia Beauty (12 ads)
5. For Traders Global (parcial)
6. Hytallo Soares (parcial)

**Paginas SEM dados (pendente P0.4):**
- Rafael Bottrel, Gabriel Navaro, GN Investimentos, Soares Hytallo, Guilherme Cirilo, Ricieri Rosset

---

## COPYCAT_DATA (distribuido)

Dados de ads coletados do CopyCat Ads. Referenciado como `COPYCAT_DATA[adId]`.

| Campo | Tipo | Descricao |
|---|---|---|
| texto | string | Texto principal do ad |
| titulo | string | Titulo |
| desc | string | Descricao |
| url | string | URL de destino |
| cta | string | Call-to-action (ex: "Saiba mais") |
| adv | object | Info do anunciante |
| adv.nome | string | Nome do anunciante |
| adv.cat | string | Categoria |
| adv.ig | string | Handle do Instagram |

---

## RENDA_EXTRA_KW (linha 2998)

Keywords de nicho em 3 idiomas. 15 por idioma.

| Idioma | Exemplos |
|---|---|
| pt | renda extra, ganhar dinheiro online, trabalhar em casa, renda pelo celular |
| en | make money online, side hustle, work from home, passive income |
| es | ganar dinero online, trabajo desde casa, ingresos extra, negocio online |

---

## AI_PROMPTS (linha 3083)

9 funcoes que geram prompts de IA:

| Key | Proposito | Input |
|---|---|---|
| keywords_form | Gerar keywords de ads | nicho + produto |
| keywords_sel | Keywords do texto selecionado | texto |
| segmentacao | Sugestoes de segmentacao | texto |
| analise_copy | Analise de copywriting | texto |
| teste_ab | Criar 3 variacoes A/B | texto |
| oferta | Descrever estrutura da oferta | texto |
| avatar | Identificar avatar do cliente | texto |
| angulos | Gerar 4 angulos criativos | texto |
| quiz | Criar funil de quiz | texto |

---

## allAds (variavel computada, linha 1660)

Array flat construido a partir de PAGES[]. Cada elemento:

| Campo | Tipo | Descricao |
|---|---|---|
| page | string | Nome da pagina |
| id | string | Ad ID |
| date | string | Data original (string) |
| dateObj | Date | Data parseada |
| dias | number | Dias desde a data de inicio |
| type | string | "Video" ou "Imagem" |
| domain | string | Dominio do anunciante |
| pageId | string | Meta page ID |
| color | string | Cor hex |
| vars | number | Variantes do ad (de EVO_DATA) |

Usado em: tabela de ads, filtros, graficos, Intel Criativa.

---

## adDiscoveryData (localStorage)

Persistido em `localStorage['spy-discovery-data']`. Array de objetos (Auto-Discovery).

| Campo | Tipo | Descricao |
|---|---|---|
| id | string | UUID gerado |
| name | string | Nome do anunciante |
| domain | string | Dominio |
| pageId | string | Meta page ID (opcional) |
| adCount | number | Scale Score / contagem de ads |
| mech | string | Mecanismo de venda |
| keywords | string[] | Keywords associadas |
| monitorando | boolean | Se esta sendo monitorado |
| seed | boolean | Se e seed (referencia, nao removivel) |
| updatedAt | string | ISO date da ultima atualizacao |

Seeds sao os anunciantes originais do DISCOVERIES. Nao podem ser removidos.

---

## feedItems (constante inline, linha 2134)

Array de 14 eventos do activity feed.

| Campo | Tipo | Descricao |
|---|---|---|
| dot | string | Cor CSS do dot (ex: "var(--green)") |
| text | string | HTML do evento |
| time | string | Timestamp (DD/mmm HH:MM) |

---

## Constantes de classificacao

### Status dos ads (Evolucao)
| Status | Dias | Cor |
|---|---|---|
| Veterano | 100+ | var(--green) |
| Campeao | 30-99 | var(--accent) |
| Validando | 7-29 | var(--yellow) |
| Teste | <7 | var(--orange) |

### Tiers (Discovery)
| Tier | Label |
|---|---|
| 1 | TIER 1 - MATCH FORTE |
| 2 | TIER 2 - SOLIDO |
| 3 | TIER 3 - MEGA ESCALA |
| 4 | TIER 4 - RECENTE |

### Scale Score
Formula: `(ads x 2) + (variacoes x 1.5)`
Usado para rankear anunciantes no overview e na tabela de discovery.
