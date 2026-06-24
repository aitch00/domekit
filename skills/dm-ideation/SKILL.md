---
name: dm:ideation
description: "Genera soluzioni alternative divergenti a partire dai pain point/opportunità delle journey, lungo pattern di design diversi (self-service vs guidato, push vs pull, gamification, analogia…). Riformula i pain point in HMW e produce idea cards. Pura divergenza: non valuta né sceglie — quello è dm:concept. Usa dopo le journey, prima di convergere sul concept."
argument-hint: "[focus-area] [--n <ideas-per-area>] [--yolo] [--autonomous / mode:headless]"
---

# dm:ideation

Produce `docs/design/ideation/` con un `YYYY-MM-DD-ideation-cards-01.md`: per ogni area di
opportunità un **HMW statement** e N **idea cards** divergenti.

Atomo-dominio di design (categoria ideation). La modalità di default **non interroga area per
area da zero**: assorbe il contenuto condiviso, **deduce le aree di opportunità e le idee**, e
le fa **verificare/correggere**.

> **La regola che governa questa skill: divergenza pura.** Qui si *espande* lo spazio delle
> soluzioni, non si sceglie. La skill **non valuta, non scarta, non indica "la migliore"** — la
> convergenza è di `dm:concept`. Mischiare i due momenti è il bias di **convergenza prematura**,
> l'anti-pattern principale da difendere. Vedi `references/divergence-rules.md`.

---

## Domain context

Dominio: design. Categoria: ideation. Carica `references/ideation-patterns.md` (i 10 pattern di
divergenza) e `references/divergence-rules.md` (le regole load-bearing: separazione
divergenza/convergenza, HMW come gate, diversità > quantità). Lo scheletro di output è in
`references/ideation-template.md`. Cross-dominio: nessuno (puro design-research).

Fonte primaria delle opportunità: le **journey** (`dm:journeys`) — i loro pain point e i momenti
a bassa emozione sono il materiale grezzo dell'ideazione. Le personas ancorano le idee al contesto
dell'utente reale; il brief allinea ai vincoli del progetto.

---

## On Activation

### 1. Resume Check

Scansiona `docs/design/ideation/*-ideation-cards-*.md`.

Se trovati, lista le aree già coperte (HMW + n. idee + `status`) e chiedi:

```
AskUserQuestion (header: "Ideation"):
- Aggiungi nuove aree di opportunità (mantieni le esistenti)
- Ricomincia da capo (sovrascrive tutto)
```

### 2. Mode Detection

| Flag | Modalità | Comportamento |
|------|----------|---------------|
| (nessuno) | **Guided** | Deduci aree + idee → presenta → verifica/correggi → chiedi solo i gap |
| `--yolo` | **Yolo** | Deduci tutto, una conferma compatta |
| `--autonomous` / `mode:headless` | **Headless** | Deduci in silenzio, zero interazione |

`--n <k>` fissa le idee per area (default 3-5). La modalità si sceglie una volta e si propaga.

### 3. Discovery — Absorb & Deduce

**Grounding del corpus (`dm:research-corpus`).** Prima di scansionare a mano, dispatcha
`dm:research-corpus` (intent corpus, `--no-persist`): inventario, stato e contraddizioni del
corpus. Decisione a 3 stadi (esplicito → default grounding → gap-driven sui riferimenti che non
tornano); se l'atomo non è disponibile, **degrada** alla scansione diretta. I finding entrano solo
dove plasmano la bozza (load-bearing).

**Scansiona il contenuto condiviso** (via digest o diretta in fallback):
- `docs/design/journeys/*-journey-*.md` — **fonte primaria**: pain point, fasi a emozione bassa
  (−2/−1), opportunità dichiarate nella Peak-End Analysis
- `docs/design/personas/*-persona-*.md` — il profilo cognitivo dell'utente ancora le idee al
  contesto reale (cosa è plausibile che l'utente faccia/accetti)
- `docs/design/product-brief/*.md`, `PRD*.md`, `*brief*.md` — vincoli e obiettivi del progetto

**Triage:** `lightweight` (0-1 fonti) / `standard` (2-5) / `deep` (6+).

**Deduci le aree di opportunità.** Da journey + personas inferisci le **aree** su cui ideare: ogni
area è un pain point o un'opportunità rilevante (tipicamente i punti a emozione bassa o le
opportunità di amplificazione del peak/end). Se `focus-area` è passata come argomento, restringi a
quella. **Dichiara i gap** (aree dedotte a bassa confidenza).

**Se non c'è contenuto condiviso** (fallback): chiedi *"Su quale pain point o opportunità vuoi
ideare? Descrivilo in una frase."* Non aprire mai con una pagina vuota.

---

## Stima del perimetro (presenta prima del loop)

**Guided / Yolo.** Apri presentando le aree dedotte, così l'utente sa cosa aspettarsi:

```
Dalle journey prevedo ~[N] aree di opportunità su cui ideare:

| # | Area di opportunità | Origine (journey/fase) | Perché |
|---|---------------------|------------------------|--------|
| 1 | [area] | [journey · fase a emozione −X] | [razionale] |
| … | … | … | … |

Stima orientativa, non un limite. Ideiamo un'area alla volta. Partiamo dalla #1?
```

In **yolo** mostra la tabella e procedi con tutte le aree. In **headless** non presentare: deduci
il set, registralo nel report e idea tutte le aree in silenzio.

---

## Ideazione (loop — ripeti per area fino a "finito")

### A. HMW come gate (lead con ciò che sai)

Riformula il pain point dell'area in un **How Might We**:
*"Come potremmo [aiutare persona X] a [raggiungere obiettivo] nonostante [ostacolo]?"*

**Gate.** Se il pain point **non** si riformula in un HMW sensato, è il segnale che il problema non
è abbastanza chiaro: **dichiaralo** invece di forzare idee su una base ambigua.

```
⚠ L'area "[area]" non si riformula in un HMW sensato: il pain point è troppo vago
  (manca [chi / obiettivo / ostacolo]). Vuoi chiarire l'area o saltarla?
```

Non inventare un HMW plausibile per coprire il buco — la chiarezza del problema viene prima.

### B. Genera idee divergenti per pattern

Carica `references/ideation-patterns.md`. Genera N idee (default 3-5) che divergono **per pattern**,
non variazioni della stessa soluzione. Copri pattern diversi tra i 10 disponibili (self-service vs
guidato, preventivo vs reattivo, centralizzato vs distribuito, esplicito vs implicito, sincrono vs
asincrono, push vs pull, gamification, social proof, defaults intelligenti, analogia da altro
dominio). Ogni area dovrebbe toccare almeno un'**analogia da altro dominio** (built-in).

Ogni idea card:
- **Nome sintetico**
- **Descrizione** (2-3 frasi, orientata a *cosa fa l'utente*, non a come funziona il sistema)
- **Pattern di design applicato** (quale dei 10)
- **Assunzione chiave** (cosa deve essere vero perché questa idea funzioni)
- **Rischio principale**
- **Analogia** (da quale dominio viene l'idea)

**Non valutare.** In questa fase tutte le idee convivono: niente "questa è la migliore", niente
scarti, niente ranking. È la regola "Yes, and…" — la critica è di `dm:concept`.

### C. Verifica (lead con ciò che sai)

**Guided.** Presenta HMW + idea cards dell'area come blocco unico:

```
Area "[area]" → HMW: "[statement]"

| # | Nome | Pattern | Descrizione (utente) | Assunzione chiave | Rischio | Analogia |
|---|------|---------|----------------------|-------------------|---------|----------|
| 1 | … | … | … | … | … | … |

Confermi, o cosa correggi/aggiungi? (puoi cambiare HMW, idee, pattern)
```

Itera al massimo 2 volte; oltre, raccogli idea per idea come fallback. Chiedi **solo i gap dichiarati**.

**Yolo:** mostra il blocco, una conferma compatta.
**Headless:** usa le idee dedotte senza verifica.

### D. Self-check di diversità

Verifica la **diversità dei pattern**, non il numero di idee: 3 idee su 3 pattern realmente diversi
valgono più di 8 variazioni dello stesso pattern. Se le idee dell'area collassano su 1-2 pattern,
segnalalo e proponi di sostituirne una con un pattern distante (es. un'analogia da altro dominio):

```
⚠ Le idee dell'area "[area]" convergono su [pattern]. La divergenza è debole.
  Sostituisco un'idea con un pattern distante? (es. analogia da [dominio])
```

Questo è un check di divergenza, non di qualità — non bloccare se l'utente conferma.

### E. Scrittura File

Crea `docs/design/ideation/` se non esiste. Scrivi (o aggiorna) `docs/design/ideation/YYYY-MM-DD-ideation-cards-01.md`
seguendo lo scheletro di `references/ideation-template.md` (frontmatter handoff + un blocco per area
con HMW `{#hmw.<slug>}` e idea cards con anchor `idea.<slug>`).

Conferma scrittura (in headless: silenzioso).

### F. Continua?

```
AskUserQuestion (header: "Prossima area"):
- Idea un'altra area di opportunità
- Ho finito
```

---

## Report

```
dm:ideation completato.

Perimetro: [M] aree ideate su ~[N] dedotte dal corpus · [K] idee totali.

Output:
  docs/design/ideation/YYYY-MM-DD-ideation-cards-01.md

Next steps:
  /dm-concept   — converge le idee in 2-3 concept alternativi e seleziona
```

---

## Regole

- **Divergenza pura — mai convergere:** non valutare, non scartare, non indicare "la migliore". La
  convergenza è di `dm:concept`. È l'anti-pattern centrale (convergenza prematura).
- **HMW come gate:** se un pain point non si riformula in un HMW sensato, dichiaralo — non forzare idee.
- **Diversità > quantità:** il self-check verifica la varietà dei pattern, non il numero delle idee.
- **Mai ideare da zero interrogando:** in guided deduci dalle fonti (journey come fonte primaria), poi fai verificare.
- **Lead con ciò che sai:** ogni domanda parte da ciò che hai già dedotto.
- **Descrizione orientata all'utente:** cosa fa l'utente, non come funziona il sistema.
- **Almeno un'analogia da altro dominio per area** quando le fonti lo permettono.
- Salva ogni area immediatamente dopo Step E.
- Modalità (guided/yolo/headless) scelta una volta, propagata.
- area-slug / idea-slug: lowercase, solo trattini, max 40 caratteri.
- **Anchor degli elementi consumabili** (convenzione `02-principio-handoff`): HMW → `{#hmw.<slug>}`
  sul titolo dell'area; idea card → colonna/anchor `idea.<slug>`. Anchor content-derived, **congelato
  alla nascita**.
- **Rigenerazione anchor-preserving:** rigenerando il file, ri-aggancia gli anchor esistenti per
  similarità di nome invece di rislugare (mai rinumerare).

---

## Sentinella harvest

A completamento avvenuto, emetti nell'output la riga sentinella. Il manifest dichiara
`harvest_policy.method: auto-on-finalize`: il trigger engine (`.claude/hooks/`) la rileva e propone
`dome:harvest --method` per raccogliere feedback di metodo su questa skill.

```
[[harvest:finalize skill=dm-ideation]]
```
