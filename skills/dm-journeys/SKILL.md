---
name: dm:journeys
description: "Mappa customer journey con analisi Peak-End (Kahneman) e Sentiment Timeline. Deduce le fasi dai contenuti condivisi (personas, brief, flows) e le fa verificare. Usa dopo aver definito le personas per mappare l'esperienza vissuta dagli utenti."
argument-hint: "[scenario-name] [--persona <slug>] [--yolo] [--autonomous / mode:headless]"
---

# dm:journeys

Produce `docs/design/journeys/` con un file per ogni journey e un `YYYY-MM-DD-journeys-overview-01.md`.

La modalità di default **non interroga fase per fase**: assorbe il contenuto condiviso, **deduce 5-7 fasi**, e le fa **verificare/correggere**.

---

## On Activation

### 1. Resume Check

Scansiona `docs/design/journeys/*-journey-*.md` (esclude `YYYY-MM-DD-journeys-overview-01.md`).

Se trovati, lista le journey esistenti (scenario + persona + `status`) e chiedi:

```
AskUserQuestion (header: "Journeys"):
- Aggiungi una nuova journey
- Ricomincia da capo
```

### 2. Persona Check (soft dependency)

Scansiona `docs/design/personas/*-persona-*.md`.

**Se trovati:** se `--persona <slug>` fornito, seleziona quella; altrimenti chiedi quale usare (in yolo/headless: usa la primary).

**Se non trovati:** avvisa e offri di procedere con profilo inline:
```
Nessuna persona trovata in docs/design/personas/.
Le journey funzionano meglio con personas definite.
```
```
AskUserQuestion (header: "Come procedere"):
- Crea le personas prima (exit e avvia dm:personas)
- Procedi con profilo inline (chiedo nome e breve descrizione)
```

Se inline: chiedi *"Nome e breve descrizione del profilo utente target?"* e usalo come riferimento.

### 3. Mode Detection

| Flag | Modalità | Comportamento |
|------|----------|---------------|
| (nessuno) | **Guided** | Deduci le fasi → presenta tabella → verifica/correggi → chiedi solo i gap |
| `--yolo` | **Yolo** | Deduci tutto, una conferma compatta |
| `--autonomous` / `mode:headless` | **Headless** | Deduci in silenzio, zero interazione |

### 4. Discovery — Absorb & Deduce

**Grounding del corpus (`dm:research-corpus`).** Prima di scansionare a mano,
dispatcha `dm:research-corpus` (intent corpus, `--no-persist`): inventario, stato
e contraddizioni del corpus. Decisione a 3 stadi (esplicito → default grounding →
gap-driven su riferimenti che non tornano); se l'atomo non è disponibile,
**degrada** alla scansione diretta. I finding entrano solo dove plasmano la
bozza (load-bearing).

**Scansiona il contenuto condiviso** (via digest o diretta in fallback):
- la persona selezionata — il profilo cognitivo (decision style, attention) informa le emozioni
- `docs/design/flows/*-flow-*.md` — gli step operativi diventano touchpoint della journey
- `docs/design/product-brief/*.md`, `PRD*.md`, `*brief*.md` — obiettivi e contesto
- `docs/design/brainstorming/*.md` — momenti chiave emersi

**Triage:** `lightweight` / `standard` / `deep` in base alle fonti.

**Scenario name:** dall'argomento, dedotto, oppure — fallback — chiedi *"Qual è lo scenario che vuoi mappare?"*.

**Deduci il set di journey candidate (stima upfront).** Prima di entrare nel dettaglio della prima
journey, inferisci dalle fonti **quante journey servono e quali**: una per ogni coppia rilevante
*persona × scenario/obiettivo* emersa da personas, brief e flows (es. onboarding, uso ricorrente,
recovery/errore, churn per l'anti-persona). Ogni candidata: scenario + persona + una riga di razionale.
Questa è una **stima orientativa, non un cap**: dà all'utente il senso del perimetro e si può sempre
aggiungere/togliere nel loop. In assenza di fonti, la stima è "1+ (da scoprire)".

**Deduci la prima journey.** Inferisci: obiettivo utente, aspettative iniziali dell'attore, trigger, definizione di successo, e **5-7 fasi** (nome, azione, pensiero, emozione −2/+3, touchpoint, pain point/opportunità). Usa il profilo cognitivo della persona per stimare le emozioni. **Dichiara i gap**.

---

## Stima del perimetro (presenta prima del loop)

**Guided / Yolo.** Apri presentando la stima dedotta, così l'utente sa cosa aspettarsi:

```
Dal corpus prevedo ~[N] journey per coprire il prodotto:

| # | Scenario | Persona | Perché |
|---|----------|---------|--------|
| 1 | [scenario] | [persona] | [razionale] |
| … | … | … | … |

Stima orientativa, non un limite: ne mappiamo una alla volta, puoi aggiungerne o toglierne.
Partiamo dalla #1?
```

In **yolo** mostra la tabella e procedi con tutte le candidate. In **headless** non presentare:
deduci il set, registra `[N]` nel report e mappa tutte le candidate in silenzio. Le journey già
esistenti (Resume Check) si scalano dalla stima.

---

## Creazione Journey (loop — ripeti fino a "finito")

### A. Verifica delle Fasi Dedotte (lead con ciò che sai)

**Guided.** Presenta la bozza dedotta come tabella unica e guida la verifica:

```
Per [persona] nello scenario "[scenario]", la journey sembra:

| Fase | Azione | Pensiero | Emozione | Touchpoint | Pain Point / Opportunità |
|------|--------|---------|---------|-----------|--------------------------|
| [dedotta] | … | … | [−2..+3] | … | … |

Confermi, o cosa correggi? (puoi cambiare fasi, emozioni, touchpoint)
```

Itera al massimo 2 volte sulle correzioni; se servono più giri, raccogli fase per fase come fallback. Chiedi **solo i gap dichiarati**.

**Yolo:** mostra la tabella, una conferma globale.
**Headless:** usa le fasi dedotte senza verifica.

**Scala emozioni** (riferimento, non un corso): −2 Frustrato · −1 Incerto · 0 Neutro · +1 Soddisfatto · +2 Entusiasta · +3 Deliziato.

### B. Validazione Emotion Variance

Carica `references/peak-end-theory.md`. Se tutti i valori emotivi sono identici:
```
⚠ Curva emotiva piatta — tutte le fasi hanno emozione [N].
Le journey realistiche hanno variazione. Vuoi rivedere i valori?
```
Offri di rivedere; non bloccare se confermato intenzionalmente.

### C. Sentiment Timeline

Genera `xychart-beta` Mermaid dalle emozioni delle fasi. **Sempre generata.**

```mermaid
xychart-beta
    title "Sentiment Timeline — [Scenario]"
    x-axis ["[Fase 1]", "[Fase 2]", …]
    y-axis "Emozione" -2 --> 3
    line [[valore1], [valore2], …]
```

### D. Review Peak-End

Carica `references/peak-end-theory.md`. **Identifica peak** (valore assoluto più alto) e **end** (ultima fase). Genera design recommendations:
- peak ≥ +2 → amplification (celebration, sharing, reinforcement)
- peak ≤ −1 → mitigation (root cause, simplify, guidance, ridurre decisioni)
- end → ottimizzazione (closure, next step clarity, reinforcement positivo)

Minimo 2 raccomandazioni (una per peak, una per end). In guided puoi chiedere conferma prima di formularle; in yolo/headless generale direttamente.

### E. Scrittura File

Crea `docs/design/journeys/` se non esiste.

Scrivi `docs/design/journeys/YYYY-MM-DD-journey-[scenario-slug]-[persona-slug]-01.md` seguendo lo
scheletro `role: single` di
[`references/journey-template.md`](./references/journey-template.md) (frontmatter handoff
+ Contesto + Journey Map con anchor `p.<slug>` + Sentiment Timeline + Peak-End Analysis
con Design Recommendations `{#r.<slug>}` + Metriche di Successo).

Conferma scrittura (in headless: silenzioso).

### F. Continua?

```
AskUserQuestion (header: "Prossima journey"):
- Mappa un'altra journey
- Ho finito — genera l'overview
```

---

## Overview

Dopo "ho finito", aggiorna `docs/design/journeys/YYYY-MM-DD-journeys-overview-01.md` seguendo lo
scheletro `role: overview` di
[`references/journey-template.md`](./references/journey-template.md).

---

## Report

```
dm:journeys completato.

Perimetro: [M] journey mappate su ~[N] stimate dal corpus.

Output:
  docs/design/journeys/YYYY-MM-DD-journeys-overview-01.md
  docs/design/journeys/*-journey-*.md ([N] file)

Next steps:
  /dm-flows   — approfondisci i touchpoint critici identificati nelle journey
```

---

## Regole

- **Mai raccogliere le fasi una per una da zero:** in guided deduci dalle fonti, poi fai verificare
- **Lead con ciò che sai:** ogni domanda parte da ciò che hai già dedotto
- **Stima il perimetro prima del loop:** in guided/yolo presenta ~N journey candidate (persona ×
  scenario) prima di mappare la prima — è una stima orientativa, mai un cap; il loop resta aperto
- Sentiment Timeline `xychart-beta` sempre generata — non saltare mai
- Review Peak-End sempre eseguita — mai opzionale
- Emotion scale esatta: −2 a +3 — non semplificare mai
- Emotion variance: segnalare curva piatta sempre (ma non bloccare)
- Salva ogni journey immediatamente dopo Step E
- Modalità (guided/yolo/headless) scelta una volta, propagata
- scenario-slug e persona-slug: lowercase, solo trattini, max 40 caratteri ciascuno
- **Anchor degli elementi consumabili** (convenzione `02-principio-handoff`): fase → colonna `id`
  (`p.<slug>`); design-recommendation → `{#r.<slug>}`; finding cross-journey dell'overview →
  heading `### Titolo {#finding.<slug>}`. Anchor content-derived, **congelato alla nascita**.
- **Rigenerazione anchor-preserving:** rigenerando una journey, ri-aggancia gli anchor esistenti per
  similarità di titolo invece di rislugare (mai rinumerare).

---

## Sentinella harvest

A completamento avvenuto, emetti nell'output la riga sentinella. Il manifest dichiara
`harvest_policy.method: auto-on-finalize`: il trigger engine (`.claude/hooks/`) la rileva e propone
`dome:harvest --method` per raccogliere feedback di metodo su questa skill.

```
[[harvest:finalize skill=dm-journeys]]
```
