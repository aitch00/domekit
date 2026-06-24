---
name: dm:ia
description: "Costruisce l'architettura informativa che connette i flow del prodotto: inventario degli oggetti navigabili, navigation model, content types, labeling, routing/URL, stati globali e findability gaps. Deduce la struttura dall'insieme dei flow e la fa verificare. Usa quando il prodotto supera i 2-3 flow, prima di dm:prototype."
argument-hint: "[--platform mobile|desktop|responsive] [--yolo] [--autonomous / mode:headless]"
---

# dm:ia

Produce `docs/design/ia/` con un `YYYY-MM-DD-ia-map-01.md`: la struttura navigazionale e informativa
che tiene insieme i flow del prodotto.

Atomo-dominio di design (categoria information-architecture). Il flow modella un task singolo; l'IA
modella la **struttura che collega i flow**: come l'utente si muove tra di loro, come si chiamano le
sezioni, cosa vede arrivando da un link diretto a uno stato intermedio. La modalità di default **non
interroga sezione per sezione da zero**: assorbe tutti i flow, **deduce la struttura**, e la fa
**verificare/correggere**.

> **Limite dichiarato.** La skill costruisce una struttura IA *razionale e coerente*, ma **non può
> predire come gli utenti reali categorizzano mentalmente** le cose. La sezione *Findability gaps*
> dichiara questa limitazione e propone un test veloce (tree testing / card sorting con ~5 utenti).
> Vedi `references/ia-heuristics.md`.

---

## Domain context

Dominio: design. Categoria: information-architecture. Carica `references/ia-heuristics.md` (le 7
sfaccettature di Morville come criteri di review + la tabella necessità/opzionalità + la disciplina
del limite predittivo) e usa `references/ia-template.md` come scheletro di output (le 7 sezioni della
ia-map). Cross-dominio: nessuno.

Upstream primario: **tutti i flow** del prodotto (`dm:flows`) — i loro Screen Inventory sono il
materiale grezzo dell'inventario navigabile. Il domain-model allinea le entità navigabili al modello
del dominio; le personas verificano che la struttura corrisponda ai mental model; il brief estrae i
vincoli di piattaforma (mobile-first, PWA, responsive).

---

## On Activation

### 1. Resume Check

Scansiona `docs/design/ia/*-ia-map-*.md`. Se trovata, chiedi:

```
AskUserQuestion (header: "IA"):
- Rivedi/estendi la IA esistente (rigenerazione anchor-preserving)
- Ricomincia da capo (sovrascrive)
```

### 2. Soglia di necessità (gate soft)

Scansiona `docs/design/flows/*-flow-*.md`. L'IA è **necessaria** sopra una certa soglia e **opzionale**
sotto. Carica la tabella in `references/ia-heuristics.md` e valuta:

| Condizione | Necessità |
|------------|-----------|
| 1-2 flow, utente singolo, nessuna navigazione top-level | Bassa — IA implicita, poco rischiosa |
| 3+ flow, navigazione primaria, utenti di tipo diverso | Alta — le incoerenze emergono obbligatoriamente |
| Contenuto user-generated (liste, documenti) | Alta — la findability è critica |
| Ruoli diversi (admin vs user) | Alta — la struttura diverge per ruolo |

Se la necessità è bassa, **dillo** e offri di procedere comunque:
```
Il prodotto ha [N] flow senza navigazione top-level: la IA qui è implicita e poco rischiosa.
Procedo lo stesso (utile come baseline), o rimando a quando aggiungi flow?
```
Non bloccare — è una calibrazione al contesto, non un veto.

### 3. Mode Detection

| Flag | Modalità | Comportamento |
|------|----------|---------------|
| (nessuno) | **Guided** | Deduci la struttura → presenta sezione per sezione → verifica/correggi |
| `--yolo` | **Yolo** | Deduci tutto, una conferma compatta |
| `--autonomous` / `mode:headless` | **Headless** | Deduci in silenzio, zero interazione |

`--platform mobile|desktop|responsive` fissa il target di navigazione (default: dedotto dal brief,
fallback responsive). La modalità si sceglie una volta e si propaga.

### 4. Discovery — Absorb & Deduce

**Grounding del corpus (`dm:research-corpus`).** Prima di scansionare a mano, dispatcha
`dm:research-corpus` (intent corpus, `--no-persist`): inventario, stato e contraddizioni del corpus.
Decisione a 3 stadi (esplicito → default grounding → gap-driven); se l'atomo non è disponibile,
**degrada** alla scansione diretta. I finding entrano solo dove plasmano la bozza.

**Scansiona il contenuto condiviso** (via digest o diretta in fallback):
- `docs/design/flows/*-flow-*.md` — **fonte primaria**: ogni Screen Inventory dà gli oggetti
  navigabili e i loro stati; gli Outcome→ danno le transizioni tra schermate
- `docs/design/domain-model/*-domain-model-*.md` — le entità: ognuna navigabile direttamente deve
  avere un entry nell'inventario (vedi regola di completezza sotto)
- `docs/design/personas/*-persona-*.md` — i mental model: il labeling e i raggruppamenti devono
  corrispondere al linguaggio degli utenti
- `docs/design/product-brief/*.md`, `PRD*.md`, `*brief*.md` — i vincoli di piattaforma

**Triage:** `lightweight` (1-2 flow) / `standard` (3-5) / `deep` (6+ flow o ruoli multipli).

**Deduci la struttura.** Da tutti i flow + domain-model inferisci le 7 sezioni della ia-map (sotto).
**Dichiara i gap** (oggetti dedotti a bassa confidenza, labeling incerto).

**Se non ci sono flow** (fallback): chiedi *"Quali sono le aree principali del prodotto? Elencale e
ne deduco la struttura."* Non aprire mai con una tabella vuota.

---

## Costruzione della IA Map (le 7 sezioni — deduci, presenta, verifica)

In guided presenta una sezione alla volta come bozza dedotta e chiedi conferma/correzione; in yolo una
conferma compatta sull'intera mappa; in headless deduci in silenzio.

### 1. Inventario degli oggetti navigabili
Tabella di tutto ciò che l'utente raggiunge direttamente. Tipi di accesso: `top-level` (sempre
raggiungibile), `drill-down` (da un parent), `modal` (sovrapposto), `contextual` (solo in certe
condizioni), `deep-link` (via URL diretto).
**Regola di completezza (domain-model):** ogni entità del domain-model navigabile direttamente deve
avere un entry. Un'entità senza view raggiungibile è un **gap di prodotto** → dichiaralo.

### 2. Navigation model
Primary (tab bar/sidebar/header nav) · Secondary · Contextual · Utility (settings, profilo, help,
logout). Per ogni livello: etichetta, icona (se mobile), comportamento al tap/click, stato attivo.
**Mobile vs desktop:** se `--platform responsive`, il navigation model può divergere per breakpoint —
modellalo esplicitamente (es. tab bar su mobile → sidebar su desktop).

### 3. Content types
Per ogni tipo di contenuto: campi, template di visualizzazione, azioni disponibili. Dedotti dalle
entità del domain-model e dagli Screen Inventory dei flow.

### 4. Labeling system
Glossario dei termini di navigazione **con il razionale** (es. "Persone" non "Profili" — perché mappa
sul linguaggio degli utenti). Regole di plurale/singolare, maiuscole, abbreviazioni in contesti
compatti (mobile, breadcrumb). Termini da evitare (jargon interno, false friend con competitor).

### 5. Routing e URL structure
Pattern (`/[entity-type]/[id]/[view]`), mappatura URL → view, URL shareable vs non-shareable (stato
privato). Per mobile: i deep link equivalenti.

### 6. Gestione degli stati globali
Tabella stato → comportamento → view: utente nuovo/no dati (empty + onboarding), utente con dati
(normale), sessione scaduta (redirect login), errore di connessione (offline state), ecc. Allinea
agli stati già materializzati negli Screen Inventory dei flow — non inventarne di scollegati.

### 7. Findability gaps
Lista degli item che un test IA (card sorting/tree testing) tipicamente mette in discussione,
**come ipotesi da validare, non come problemi accertati**. Dichiara il limite predittivo (la skill non
predice il mental model reale) e proponi un test veloce (~5 utenti, tree testing).

---

## Scrittura File

Crea `docs/design/ia/` se non esiste. Scrivi `docs/design/ia/YYYY-MM-DD-ia-map-01.md` seguendo lo
scheletro di `references/ia-template.md` (frontmatter handoff + le 7 sezioni con anchor di elemento).
Conferma scrittura (in headless: silenzioso).

## Review IA (euristiche di Morville)

Carica `references/ia-heuristics.md` e rivedi la struttura contro le 7 sfaccettature (findable,
accessible, clear, communicative, usable, credible, controllable). Produci i finding come note di
review interne — non bloccanti, ma annotati nella ia-map.

---

## Report

```
dm:ia completato.

Output:
  docs/design/ia/YYYY-MM-DD-ia-map-01.md
  ([N] oggetti navigabili · [M] content types · [K] findability gaps da validare)

Feedback upstream (se emersi):
  ⚠ Incongruenze IA ↔ flow: [lista] — aggiorna i flow (dm:flows) prima di prototipare

Next steps:
  /dm-prototype   — il navigation model e il routing diventano il layer strutturale del prototipo
  /dm-spec        — il content type model alimenta le definizioni del PRD
```

---

## Regole

- **Mai costruire la IA interrogando da zero:** in guided deduci da tutti i flow + domain-model, poi
  fai verificare.
- **Lead con ciò che sai:** ogni domanda parte da ciò che hai già dedotto.
- **Completezza domain-model:** ogni entità navigabile direttamente ha un entry nell'inventario;
  un'entità senza view raggiungibile è un gap di prodotto da dichiarare.
- **Findability gaps come ipotesi, non verdetti:** dichiara sempre il limite predittivo e suggerisci
  un test reale (card sorting / tree testing con ~5 utenti).
- **Labeling col razionale:** ogni etichetta porta il perché (mappa sul mental model dell'utente).
- **Mobile vs desktop:** se responsive, modella esplicitamente le divergenze di navigazione per breakpoint.
- **Feedback ai flow:** se emergono incongruenze IA ↔ flow, segnalale come feedback upstream a
  `dm:flows` (i flow vanno aggiornati prima di prototipare) — l'IA non riscrive i flow.
- **Stati globali ancorati ai flow:** allinea agli stati già materializzati negli Screen Inventory,
  non inventarne di scollegati.
- Modalità (guided/yolo/headless) scelta una volta, propagata.
- nav-slug / type-slug: lowercase, solo trattini, max 40 caratteri.
- **Anchor degli elementi consumabili** (convenzione `02-principio-handoff`): oggetto navigabile →
  colonna `id` `nav.<slug>`; content type → `ct.<slug>`; stato globale → `st.<slug>`; findability gap
  → `{#fg.<slug>}`. Anchor content-derived, **congelato alla nascita**.
- **Rigenerazione anchor-preserving:** ri-aggancia gli anchor esistenti per similarità di nome (mai rinumerare).

---

## Sentinella harvest

A completamento avvenuto, emetti nell'output la riga sentinella. Il manifest dichiara
`harvest_policy.method: auto-on-finalize`: il trigger engine (`.claude/hooks/`) la rileva e propone
`dome:harvest --method` per raccogliere feedback di metodo su questa skill.

```
[[harvest:finalize skill=dm-ia]]
```
