---
name: dm:personas
description: "Crea user personas con profilo cognitivo (4 dimensioni, bias, radar chart). Deduce le personas dai contenuti condivisi (brief, brainstorm, journeys) e le fa verificare. Usa per definire gli utenti target prima del design di flow e journey."
argument-hint: "[persona-hint] [--yolo] [--autonomous / mode:headless]"
---

# dm:personas

Produce `docs/dome/personas/` con un file per ogni persona e un `personas-overview.md`.

La modalità di default **non interroga**: assorbe il contenuto condiviso del progetto, **deduce una bozza di persona**, e la fa **verificare/correggere**. Le domande coprono solo i gap reali.

---

## On Activation

### 1. Resume Check

Scansiona `docs/dome/personas/persona-*.md`.

Se trovati, lista le personas esistenti (nome + tipo + `status`) e chiedi:

```
AskUserQuestion (header: "Personas"):
- Aggiungi una nuova persona (mantieni quelle esistenti)
- Modifica una persona esistente (seleziona quale)
- Ricomincia da capo (sovrascrive tutto)
```

### 2. Mode Detection

| Flag | Modalità | Comportamento |
|------|----------|---------------|
| (nessuno) | **Guided** | Deduci dal contenuto → presenta bozza → verifica/correggi → chiedi solo i gap |
| `--yolo` | **Yolo** | Deduci tutto, una sola conferma compatta, nessun dialogo sui gap non-critici |
| `--autonomous` / `mode:headless` | **Headless** | Deduci in silenzio, zero interazione |

La modalità è scelta una volta e vale per tutta l'esecuzione.

### 3. Discovery — Absorb & Deduce

**Grounding del corpus (`dm:research-corpus`).** Prima di scansionare a mano,
dispatcha `dm:research-corpus` (intent corpus, `--no-persist`) sul progetto:
restituisce inventario, stato e contraddizioni del corpus dome — sostituisce la
scansione improvvisata e segnala subito gap e incoerenze fra artefatti. Decisione
a 3 stadi: **sempre** se l'utente lo chiede esplicitamente; **di default** come
grounding del discovery; **gap-driven** se durante la deduzione un riferimento
non torna. Se l'atomo non è disponibile, **degrada** alla scansione diretta qui
sotto (mai bloccare). I finding del digest entrano nella bozza solo dove la
plasmano (load-bearing).

**Scansiona il contenuto condiviso** (via digest o diretta in fallback, in ordine di valore):
- `docs/dome/product-brief/*.md` — utenti target, valore, mercato
- `docs/dome/brainstorming/*.md` — ipotesi utente emerse
- `docs/dome/journeys/journey-*.md` — profili già in uso a valle
- `PRD*.md`, `discovery/*.md`, `*brief*.md` — fonti legacy

**Triage** (dimensiona lo sforzo): `lightweight` (0-1 fonti), `standard` (2-5), `deep` (6+).

**Deduci le personas candidate.** Da ciò che hai trovato, inferisci per ogni tipo utente plausibile: profilo (nome, età, ruolo, contesto, device/canale, frequenza d'uso, tech savviness, goals, motivations, pain points, behaviors, quote), le 4 dimensioni cognitive (usa la occupation correlation table di `references/cognitive-dimensions.md`), 3 bias pertinenti, e il radar. **Dichiara i gap**: cosa non è deducibile dalle fonti.

**Se non c'è alcun contenuto condiviso** (fallback, non default): chiedi una breve descrizione libera del tipo utente target — *"Descrivimi in 1-2 frasi chi è l'utente principale"* — poi deduci da lì. Non presentare mai un blocco di campi vuoti da compilare.

---

## Creazione Persona (loop — ripeti fino a "finito")

### A. Tipo Persona

Proponi il tipo dedotto, confermabile:

```
AskUserQuestion (header: "Tipo"):
- primary — utente principale, uso più frequente
- secondary — tipo utente alternativo, pattern diversi
- anti-persona — chi NON è il target (utile per definire i confini)
```

In yolo/headless: usa il tipo dedotto senza chiedere.

### B. Verifica del Profilo Dedotto (lead con ciò che sai)

**Guided.** Presenta la bozza dedotta come tabella unica (profilo + layer cognitivo + bias) e guida la verifica:

```
Dalla ricerca, [tipo] sembra essere:

| Campo | Valore dedotto |
|-------|----------------|
| Nome | [dedotto / proposto] |
| Età | [dedotta] |
| Ruolo | [dedotto] |
| Contesto d'uso | [dedotto] |
| Device / canale | [dedotto] |
| Frequenza d'uso | [dedotta] |
| Tech savviness | [0–5 dedotto] |
| Goals | [dedotti] |
| Motivations | [dedotte — il perché] |
| Pain points | [dedotti] |
| Behaviors | [dedotti] |
| Quote | "[dedotta]" |

Confermi, o cosa correggi?
```

Poi **chiedi solo i gap dichiarati** in Discovery — mirati, uno per volta, non un questionario. Per ogni campo a bassa confidenza, parti da ciò che sai: *"Sul contesto d'uso ho ipotizzato X — è corretto?"*.

**Yolo:** mostra la tabella, una sola richiesta di conferma globale ("Confermi tutto o correggi?").
**Headless:** salta la verifica, usa i valori dedotti.

### C. Layer Cognitivo

Carica `references/cognitive-dimensions.md`. Le 4 dimensioni sono **già state dedotte** in Discovery dalla occupation table. In guided, mostrale e chiedi conferma solo se la confidenza è bassa:

| Dimensione | Score | Label |
|-----------|-------|-------|
| Decision Style | [0–10] | [label] |
| Cognitive Capacity | [0–10] | [label] |
| Attention | [0–10] | [label] |
| Processing Mode | [visual/verbal/kinesthetic] | — |

Non spiegare i concetti con analogie a meno che l'utente non lo chieda: la verifica è sul *valore*, non un corso di cognitive science.

### D. Bias Cognitivi

Carica `references/cognitive-biases.md`. I 3 bias pertinenti sono già dedotti dal profilo (occupazione, contesto, decision style). In guided, presentali e chiedi conferma/sostituzione. In yolo/headless: usa i dedotti.

### E. Radar Chart

Calcola i 6 valori radar secondo `references/cognitive-dimensions.md`:
- `radar_working_memory` = cognitive_capacity
- `radar_attention` = attention
- `radar_processing_speed` = cognitive_capacity
- `radar_analytical` = decision_style
- `radar_bias_resistance` = 10 − media(bias_1_severity, bias_2_severity, bias_3_severity)
- `radar_flexibility` = kinesthetic→8 / visual→7 / verbal→5

### F. Self-Check (coerenza)

**Nessun input utente.** Prima di scrivere, verifica internamente la coerenza:
1. Le dimensioni cognitive sono plausibili rispetto al ruolo/occupazione?
2. I bias scelti sono coerenti con contesto e decision style?
3. Il radar è coerente con le dimensioni?

Se rilevi un'incoerenza, annotala in `weak_sections` (non bloccare).

### G. Scrivi File

Crea `docs/dome/personas/` se non esiste.

Scrivi `docs/dome/personas/persona-[type]-[name-slug].md` seguendo lo scheletro
`role: single` di [`references/persona-template.md`](./references/persona-template.md)
(frontmatter handoff + Profile + Cognitive Profile + Key Biases con anchor `bias.<slug>`
+ Radar Chart Data).

Il file nasce con **`status: complete`** — ha superato il self-check (Step F), che è il suo gate.
Marca `status: draft` **solo** se la persona resta deliberatamente incompleta (gap critici
irrisolti dichiarati in `weak_sections`); in quel caso esplicitalo nel report finale. Una persona
non deve mai restare `draft` a processo concluso senza una ragione dichiarata.

Conferma scrittura (in headless: silenzioso).

### H. Continua?

```
AskUserQuestion (header: "Prossima persona"):
- Aggiungi un'altra persona
- Ho finito — genera l'overview
```

---

## Overview

Dopo l'ultimo "ho finito", aggiorna `docs/dome/personas/personas-overview.md`
seguendo lo scheletro `role: overview` di
[`references/persona-template.md`](./references/persona-template.md).

---

## Report

```
dm:personas completato.

Output:
  docs/dome/personas/personas-overview.md
  docs/dome/personas/persona-*.md ([N] file)

Next steps:
  /dm-journeys   — mappa l'esperienza vissuta (usa le personas come input)
  /dm-flows      — mappa come queste personas usano il prodotto
  (ordine logico della pipeline design: personas → journeys → flows)
```

---

## Regole

- **Mai un blocco di campi vuoti da compilare:** in guided deduci sempre prima dal contenuto, poi fai verificare
- **Lead con ciò che sai:** ogni domanda parte da ciò che hai già dedotto
- Chiedi solo i gap dichiarati in Discovery — non un questionario
- Salva ogni persona immediatamente dopo Step G — non accumulare
- **Status onesto:** la persona singola nasce `complete` (superato il self-check); `draft` solo se
  resta deliberatamente incompleta, e in tal caso dichiaralo nel report. Mai `draft` silenzioso a fine processo
- Modalità (guided/yolo/headless) scelta una volta, propagata a tutto il flusso
- Il slug del nome: lowercase, solo trattini, max 40 caratteri
- **Anchor degli elementi consumabili** (convenzione `02-principio-handoff`): bias → colonna `id`
  (`bias.<slug>`); finding dell'overview → heading `### Titolo {#finding.<slug>}`. Anchor
  content-derived, **congelato alla nascita**.
- **Rigenerazione anchor-preserving:** rigenerando una persona, ri-aggancia gli anchor esistenti per
  similarità di titolo invece di rislugare (mai rinumerare).

---

## Sentinella harvest

A completamento avvenuto, emetti nell'output la riga sentinella. Il manifest dichiara
`harvest_policy.method: auto-on-finalize`: il trigger engine (`.claude/hooks/`) la rileva e propone
`dome:harvest --method` per raccogliere feedback di metodo su questa skill.

```
[[harvest:finalize skill=dm-personas]]
```
