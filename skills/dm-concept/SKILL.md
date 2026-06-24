---
name: dm:concept
description: "Converge le idea cards di dm:ideation in 2-3 concept di prodotto alternativi, strutturati, con trade-off espliciti, e seleziona quello da sviluppare con una decisione documentata. Un concept è l'approccio ad alto livello — non un flow, non un PRD. Usa dopo dm:ideation, prima di dm:flows."
argument-hint: "[--criteria \"<c1>; <c2>\"] [--n <concept-count>] [--yolo] [--autonomous / mode:headless]"
---

# dm:concept

Produce `docs/design/concepts/` con un `YYYY-MM-DD-concept-[nome]-01.md` per ogni concept e un
`YYYY-MM-DD-concept-selection-01.md` con la decisione documentata.

Atomo-dominio di design (categoria ideation). È la metà **convergente** della Fase 3: prende le idee
divergenti di `dm:ideation` e le sintetizza in **2-3 concept di prodotto** alternativi, con pro/contro
espliciti rispetto ai criteri del brief, e ne seleziona uno con una decisione tracciata.

> **Cosa è un concept (e cosa non è).** Un concept è la descrizione dell'**approccio ad alto
> livello**: cosa fa il prodotto per l'utente, secondo quale logica, con quale trade-off rispetto
> alle alternative. **Non è un flow** (niente step-by-step) e **non è un PRD** (niente requisiti
> dettagliati). Ogni volta che scende al dettaglio di un singolo step, è nella zona sbagliata —
> rimanda a `dm:flows`. Vedi `references/concept-rules.md`.

---

## Domain context

Dominio: design. Categoria: ideation. Carica `references/concept-rules.md` (regole load-bearing:
concept ≠ flow ≠ PRD; due criteri inviolabili di selezione; concept multipli come protezione) e usa
`references/concept-template.md` come scheletro di output (concept + selection).

Upstream primario: `dm:ideation` (`docs/design/ideation/*-ideation-cards-*.md`). Le idea cards — con
i loro pattern, assunzioni e rischi già esplicitati in fase divergente — sono il materiale grezzo da
cui i concept si compongono: un concept tipicamente **combina** più idee coerenti tra loro, non ne
adotta una sola. Il brief fornisce i criteri di selezione; le personas verificano il fit con il
profilo utente. Cross-dominio: nessuno.

---

## On Activation

### 1. Resume Check

Scansiona `docs/design/concepts/*-concept-*.md`.

Se trovati, lista i concept esistenti (nome + `status`) e l'eventuale selection, poi chiedi:

```
AskUserQuestion (header: "Concept"):
- Aggiungi/rivedi concept (mantieni gli esistenti)
- Ricomincia da capo (sovrascrive tutto)
```

### 2. Dipendenza da ideation (soft)

Scansiona `docs/design/ideation/*-ideation-cards-*.md`.

**Se trovati:** usali come upstream primario.

**Se non trovati:** avvisa e offri di procedere comunque:
```
Nessuna ideation trovata in docs/design/ideation/.
I concept funzionano meglio convergendo da idee divergenti già generate.
```
```
AskUserQuestion (header: "Come procedere"):
- Genera le idee prima (exit e avvia dm:ideation)
- Procedi dalle journey/brief direttamente (deduco le opzioni dalle fonti grezze)
```

### 3. Mode Detection

| Flag | Modalità | Comportamento |
|------|----------|---------------|
| (nessuno) | **Guided** | Deduci criteri + concept → presenta → verifica → seleziona insieme |
| `--yolo` | **Yolo** | Deduci tutto, una conferma compatta, selezione motivata autonoma |
| `--autonomous` / `mode:headless` | **Headless** | Deduci e seleziona in silenzio, zero interazione |

`--n <k>` fissa il numero di concept (default 2-3). `--criteria "..."` passa i criteri espliciti. La
modalità si sceglie una volta e si propaga.

### 4. Discovery — Absorb & Deduce

**Grounding del corpus (`dm:research-corpus`).** Prima di scansionare a mano, dispatcha
`dm:research-corpus` (intent corpus, `--no-persist`): inventario, stato e contraddizioni del corpus.
Decisione a 3 stadi (esplicito → default grounding → gap-driven); se l'atomo non è disponibile,
**degrada** alla scansione diretta. I finding entrano solo dove plasmano la bozza.

**Scansiona il contenuto condiviso** (via digest o diretta in fallback):
- `docs/design/ideation/*-ideation-cards-*.md` — **upstream primario**: idee, pattern, assunzioni, rischi
- `docs/design/product-brief/*.md`, `PRD*.md`, `*brief*.md` — **i criteri di selezione** e i vincoli
- `docs/design/personas/*-persona-*.md` — per verificare il fit di ogni concept con il profilo utente
- `docs/design/journeys/*-journey-*.md` — per il criterio inviolabile #1 (mapping su una journey)

**Triage:** `lightweight` (0-1 fonti) / `standard` (2-5) / `deep` (6+).

**Deduci i criteri di selezione.** Se `--criteria` è passato, usalo. Altrimenti estrai i criteri dal
brief (es. "time to value < 2 min", "funziona offline", "zero onboarding") e dalle assunzioni più
rischiose emerse in ideation. **Dichiara i gap** se i criteri non sono deducibili.

**Deduci i concept candidati.** Raggruppa le idea cards in **2-3 concept coerenti**: ogni concept è
una combinazione di idee che condividono una logica di prodotto (es. "tutto preventivo + defaults
intelligenti" vs "tutto guidato + social proof"). I concept devono essere **genuinamente
alternativi**, non varianti dello stesso approccio.

**Se non c'è ideation** (fallback): deduci 2-3 approcci alternativi direttamente da journey + brief.

---

## Creazione Concept (loop — uno per concept)

### A. Verifica del concept dedotto (lead con ciò che sai)

**Guided.** Presenta la bozza di ogni concept come blocco e guida la verifica:

```
Concept "[nome]" — "[tagline]"
  Core interaction model: [come l'utente si muove ad alto livello]
  Combina le idee: [idea.slug, idea.slug…]
  Trade-off: fa bene [X] · sacrifica [Y] · per chi [Z]

Confermi, o cosa correggi? (nome, modello, trade-off, idee combinate)
```

Itera al massimo 2 volte; chiedi **solo i gap dichiarati**.

**Yolo:** mostra il blocco, una conferma compatta.
**Headless:** usa i concept dedotti senza verifica.

Struttura di ogni concept (vedi `references/concept-template.md`):
- **Nome e tagline** — in una frase da utente: "Con [prodotto] posso [outcome] senza [attrito]"
- **Core interaction model** — il pattern generale di movimento attraverso il prodotto (NON step-by-step)
- **Assunzioni critiche** — cosa deve essere vero (utente, mercato, tecnica) perché il concept funzioni
- **Trade-off dichiarati** — cosa fa molto bene · cosa sacrifica deliberatamente · per chi funziona / per chi no
- **Sketch testuale** — narrazione di un uso tipico ("Maria apre il prodotto, vede X, fa Y, ottiene Z")
- **Rischi di implementazione** — complessità tecnica, dipendenze, vincoli legali/etici

### B. Gate dei due criteri inviolabili

Per ogni concept verifica (carica `references/concept-rules.md`):
1. **Mapping su journey** — il concept mappa su almeno una journey esistente con un miglioramento
   *misurabile* del peak/end. Se non mappa su nessuna journey, dichiaralo (il concept è scollegato
   dall'esperienza reale).
2. **Assunzione più rischiosa testabile** — l'assunzione critica più rischiosa è identificata ed è
   *falsificabile*. Se non lo è, marcala in `weak_sections`.

### C. Scrittura concept

Crea `docs/design/concepts/` se non esiste. Scrivi `docs/design/concepts/YYYY-MM-DD-concept-[nome-slug]-01.md`
seguendo lo scheletro `role: concept` di `references/concept-template.md` (frontmatter handoff +
le 6 sezioni + il gate dei due criteri). Conferma scrittura (in headless: silenzioso).

### D. Continua?

```
AskUserQuestion (header: "Prossimo concept"):
- Scrivi un altro concept
- Ho finito — passa alla selezione
```

Minimo 2 concept prima della selezione: scrivere gli alternativi **è** il valore (protegge dalla
"prima idea vince per inerzia"), non solo la scelta finale.

---

## Selezione (dopo "ho finito")

### S1. Matrice di valutazione

Costruisci la matrice **concept × criteri** con score e razionale per cella. È **qui** — e solo qui —
che la valutazione avviene (in ideation era vietata; nei singoli concept i trade-off si descrivono,
non si pesano). I criteri sono quelli dedotti in Discovery o passati con `--criteria`.

### S2. Concept selezionato

Dichiara il concept scelto con **motivazione esplicita** ancorata alla matrice. In guided presenta la
matrice e chiedi conferma prima di dichiarare il vincitore; in yolo/headless seleziona e motiva.

### S3. Elementi da salvare e decisioni aperte

- **Elementi degli altri concept da salvare** — idee buone che non muoiono con il concept scartato
  (candidate a feature future / backlog).
- **Decisioni aperte** — aspetti da definire **nei flow**, non qui (il concept resta ad alto livello).

### S4. Scrittura selection

Scrivi `docs/design/concepts/YYYY-MM-DD-concept-selection-01.md` seguendo lo scheletro
`role: selection` di `references/concept-template.md`.

---

## Report

```
dm:concept completato.

[N] concept scritti · selezionato: "[nome]".

Output:
  docs/design/concepts/YYYY-MM-DD-concept-[nome]-01.md ([N] file)
  docs/design/concepts/YYYY-MM-DD-concept-selection-01.md

Next steps:
  /dm-flows    — costruisci i flow entro il frame del concept selezionato
  /dm-spec     — il concept alimenta il PRD come contesto strategico (a valle dei flow)
```

---

## Regole

- **Concept ≠ flow ≠ PRD:** resta ad alto livello. Dettaglio di step → rimanda a `dm:flows`; requisiti
  e metriche → rimanda a `dm:spec`.
- **Valutazione solo nella selezione:** nei singoli concept i trade-off si *descrivono*; la matrice
  con score vive solo in `concept-selection.md`.
- **Due criteri inviolabili** per concept: (1) mappa su ≥1 journey con miglioramento misurabile del
  peak/end; (2) l'assunzione più rischiosa è identificata e testabile.
- **Minimo 2 concept** prima della selezione: scrivere gli alternativi è il valore, non solo la scelta.
- **Concept genuinamente alternativi:** non varianti dello stesso approccio (divergono per logica/pattern).
- **Artefatto condivisibile:** il concept deve essere comprensibile senza background tecnico (è il doc
  da mostrare agli stakeholder).
- **Lead con ciò che sai:** deduci dalle idee/brief, poi fai verificare; chiedi solo i gap.
- **Niente idea muore senza traccia:** gli elementi buoni dei concept scartati vanno in "da salvare".
- Salva ogni concept subito dopo Step C; la selection dopo S4.
- Modalità (guided/yolo/headless) scelta una volta, propagata.
- concept-slug: lowercase, solo trattini, max 40 caratteri.
- **Anchor degli elementi consumabili** (convenzione `02-principio-handoff`): concept → `id`
  `concept-<slug>` nel frontmatter; assunzione critica → `{#a.<slug>}`; riga della matrice di
  selezione → `crit.<slug>`. Anchor content-derived, **congelato alla nascita**.
- **Rigenerazione anchor-preserving:** ri-aggancia gli anchor esistenti per similarità di nome (mai rinumerare).

---

## Sentinella harvest

A completamento avvenuto, emetti nell'output la riga sentinella. Il manifest dichiara
`harvest_policy.method: auto-on-finalize`: il trigger engine (`.claude/hooks/`) la rileva e propone
`dome:harvest --method` per raccogliere feedback di metodo su questa skill.

```
[[harvest:finalize skill=dm-concept]]
```
