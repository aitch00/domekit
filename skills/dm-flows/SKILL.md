---
name: dm:flows
description: "Documenta user flow con diagramma Mermaid e analisi cognitiva automatica (Hick's Law, Miller 7±2, context switching). Deduce l'happy path dai contenuti condivisi (journeys, personas, domain-model, brief) e lo fa verificare. Usa per mappare come gli utenti usano il prodotto."
argument-hint: "[feature-name] [--yolo] [--autonomous / mode:headless] [--ingest <changeset.md>]"
---

# dm:flows

Produce `docs/dome/flows/` con un file per ogni flow e un `flows-overview.md`.

La modalità di default **non interroga step per step**: assorbe il contenuto condiviso, **deduce un happy path**, e lo fa **verificare/correggere**.

---

## On Activation

### 0. Routing — creazione o ingest?

| Invocazione | Ramo |
|-------------|------|
| `--ingest <changeset.md>` (o `mode:ingest changeset:<path>`) | **Ingest Changeset** (vedi sotto): assorbe i delta in un flow esistente. Salta Discovery/Creazione. |
| (altrimenti) | **Creazione/Resume** (il flusso standard qui sotto). |

L'ingest è il **ramo ricevente** del round-trip edit→flow: `dm:revise` cattura gli edit sul
prototipo e ne esporta un `changeset-*.md`; `dm:flows` è il proprietario del flow e l'unico
che lo riscrive. Non duplicare altrove la scrittura del flow.

### 1. Resume Check

Scansiona `docs/dome/flows/flow-*.md` (esclude `flows-overview.md`).

Se trovati, lista i flow esistenti (feature + `status`) e chiedi:

```
AskUserQuestion (header: "Flows"):
- Aggiungi nuovi flow (mantieni quelli esistenti)
- Ricomincia da capo (sovrascrive tutto)
```

### 2. Mode Detection

| Flag | Modalità | Comportamento |
|------|----------|---------------|
| (nessuno) | **Guided** | Deduci l'happy path → presenta tabella → verifica/correggi → chiedi solo i gap |
| `--yolo` | **Yolo** | Deduci tutto, una conferma compatta, salta alt/error non-critici |
| `--autonomous` / `mode:headless` | **Headless** | Deduci in silenzio, zero interazione |

### 3. Discovery — Absorb & Deduce

**Grounding del corpus (`dm:research-corpus`).** Prima di scansionare a mano,
dispatcha `dm:research-corpus` (intent corpus, `--no-persist`): inventario, stato
e contraddizioni del corpus. Decisione a 3 stadi (esplicito → default grounding →
gap-driven su riferimenti che non tornano); se l'atomo non è disponibile,
**degrada** alla scansione diretta. I finding entrano solo dove plasmano la
bozza (load-bearing).

**Scansiona il contenuto condiviso** (via digest o diretta in fallback):
- `docs/dome/journeys/journey-*.md` — semina i touchpoint/momenti critici (la journey precede il flow)
- `docs/dome/personas/persona-*.md` — gli attori e il loro profilo cognitivo
- `docs/dome/domain-model/domain-model.md` — le entità che il flow manipola
- `docs/dome/product-brief/*.md`, `PRD*.md`, `*brief*.md` — feature e obiettivi

**Triage:** `lightweight` (0-1 fonti) / `standard` (2-5) / `deep` (6+).

**Deduci i flow candidati.** Da journey + brief + personas inferisci: feature, attori, trigger, precondizioni, un **happy path** di step sequenziali, e — per ogni schermata distinta toccata — lo **Screen Inventory** (elementi UI/CTA con il loro stato + **tutti** gli stati da materializzare, off-path inclusi: vuoto/loading/errore), che è il contratto materiale verso `dm:prototype`. **Dichiara i gap**.

**Feature name:** dall'argomento (`/dm-flows nome-feature`), oppure dedotto dalle fonti, oppure — fallback — chiedi *"Per quale feature vuoi creare il flow?"*.

**Se non c'è contenuto condiviso** (fallback): chiedi una descrizione minima della feature e deduci da lì. Non aprire mai con una tabella vuota da riempire.

---

## Creazione Flow (loop — ripeti fino a "finito")

### A. Verifica dell'Happy Path Dedotto (lead con ciò che sai)

**Guided.** Presenta la bozza dedotta come tabella e guida la verifica:

```
Dalla journey/brief, l'happy path di [feature] sembra:

| # | Step | Attore | Schermata | Azione | System Response | Outcome → |
|---|------|--------|-----------|--------|-----------------|-----------|
| 1 | [dedotto] | [dedotto] | [dedotta] | [dedotta] | [dedotta] | [dedotto] |

Confermi, o cosa correggi/aggiungi?
```

Poi chiedi **solo i gap dichiarati** (es. un outcome o una system response non deducibile), mirati. Parti sempre da ciò che hai dedotto.

**Yolo:** mostra la tabella, una conferma globale.
**Headless:** usa l'happy path dedotto senza verifica.

Campi per step: Nome step (imperativo), Attore, Schermata (Screen ID), Azione, System
Response (cosa mostra il sistema dopo l'azione), Outcome → (transizione/Screen ID di
destinazione). Inoltre, lo **Screen Inventory**: per ogni schermata distinta, elementi
UI/CTA (ognuna con condizione di enable e comportamento in-flight) e **tutti** gli stati
da materializzare (default/vuoto/loading/errore/success) — il contratto materiale che
evita al prototipo di inventare; ogni stato `errore` lega trigger+recovery.

### B. Percorsi Alternativi ed Errori (skippabile)

```
AskUserQuestion (header: "Percorsi"):
- Aggiungi percorsi alternativi e scenari di errore
- Salta — procedi con solo il happy path
```

Se sì:
- **Alternative paths:** da quale step si ramifica, condizione trigger, steps, se si ricongiunge
- **Error scenarios:** a quale step, trigger, cosa vede l'utente, come recupera
- **Success criteria:** metriche di successo misurabili

In yolo/headless: deduci alt/error dalle fonti se evidenti, altrimenti salta.

### C. Diagramma Mermaid

Genera `graph TD` dall'happy path. Includi i branch principali se emersi. **Sempre generato.**

### D. Review Cognitiva Automatica

**Nessun input utente.** Carica `references/cognitive-analysis.md` e applica i 3 pattern alle descrizioni degli step:

1. **Hick's Law:** keyword ("choose", "select", "pick", "options", "from") → flag se >4 opzioni in un singolo step
2. **Miller 7±2:** keyword ("fill", "enter", "form", "fields", "list") → flag se >7 item in un singolo step
3. **Context Switching:** keyword ("switch to", "open", "external", "check", "go to") → flag qualsiasi occorrenza

Calcola la severity combinata e produci i findings (questa è la fase di Review dell'atomo).

### E. Scrittura File

Crea `docs/dome/flows/` se non esiste.

Scrivi `docs/dome/flows/flow-[feature-slug].md` seguendo lo scheletro `role: single` di
[`references/flow-template.md`](./references/flow-template.md) (frontmatter handoff +
Overview + Happy Path con anchor `s.<slug>`, System Response e Outcome→ + Screen Inventory
con anchor `sc.<slug>` (UI/CTA + stato dati) + Diagramma Mermaid + Percorsi Alternativi /
Scenari di Errore / Success Criteria opzionali + Cognitive Analysis con red-flag
`{#rf.<slug>}`).

Conferma scrittura (in headless: silenzioso).

### F. Continua?

```
AskUserQuestion (header: "Prossimo flow"):
- Crea un altro flow
- Ho finito — genera l'overview
```

---

## Overview

Dopo "ho finito", aggiorna `docs/dome/flows/flows-overview.md` seguendo lo scheletro
`role: overview` di [`references/flow-template.md`](./references/flow-template.md).

---

## Ingest Changeset (ramo ricevente del round-trip)

Attivato da `--ingest <changeset.md>`. Assorbe i delta di un changeset in un `flow-*.md`
esistente, **anchor-preserving e con gate**. Riusa la disciplina anchor della Creazione
(Step E / Regole): non rinumera, conia anchor solo per `add-screen`/`NEW`, ritira (mai riusa)
gli anchor su `remove`.

### I1. Carica e valida il changeset

Leggi il changeset; deve essere conforme a
[`changeset-contract@1.0.0`](../dome-shared/dm-design/changeset-contract.md):
- frontmatter con `type: changeset`, `target_flow` (il `flow-*.md` da aggiornare), `status: complete`;
- una sezione `## Delta` con la tabella (`# · anchor · tipo · richiesta · dettaglio`).

Apri il `target_flow`. **Non-conformità** (frontmatter mancante, `target_flow` inesistente,
un `anchor` non-`NEW` che non risolve nel flow, un `tipo` non ammesso) → **ferma e riporta**:
è un changeset difettoso, non un buco da indovinare.

**Gate di conformità del flow bersaglio.** Prima di mappare, verifica che il `target_flow`
sia conforme a [`flow-handoff-contract@1.0.0`](../dome-shared/dm-design/flow-handoff-contract.md):
deve avere una sezione **Screen Inventory** con anchor `sc.<slug>` e Happy Path con `id` `s.<slug>`.
Se il flow **non ha alcuna mappa anchor** (Screen Inventory assente, zero `sc.`/`s.` — tipico dei
flow **legacy pre-contratto**), **ferma con rimedio esplicito**: *"il flow `<path>` precede
`flow-handoff-contract@1.0.0` (nessuno Screen Inventory / anchor): rigeneralo con `dm:flows` per
aggiungere lo Screen Inventory prima di assorbire un changeset."* Un changeset non può atterrare su
un flow senza anchor — non inventare la mappa.

### I2. Mappa i delta agli atterraggi

Per ogni riga di `## Delta`, risolvi l'atterraggio nel flow (vedi il contratto, *Tipi di delta*):

| `tipo` | Atterraggio nel flow |
|--------|----------------------|
| `add-element` | riga `sc.<slug>` → *Elementi UI / CTA (con stato)* |
| `add-state` | riga `sc.<slug>` → *Stati da materializzare* (aggiungi all'enumerazione) |
| `change-behavior` | `sc.<slug>` (stato CTA) o `s.<slug>` (System Response) |
| `change-outcome` | step `s.<slug>` → *Outcome →* |
| `add-screen` | nuova riga Screen Inventory, anchor `sc.<slug>` **coniato** + eventuale step Happy Path |
| `remove` | ritira l'elemento/stato/step/schermata; l'anchor è **ritirato**, mai riusato |

### I3. Gate — anteprima prima di scrivere (mai sovrascrittura silenziosa)

Presenta la diff proposta per anchor:

```
Ingest changeset → flow-[feature].md ([N] delta)

  sc.checkout   add-element    + campo telefono (required)          ["voglio il telefono"]
  s.submit      change-outcome   Outcome → da sc.home a sc.summary  ["dopo il pagamento → riepilogo"]
  NEW           add-screen     + sc.summary (stati: default, vuoto) ["serve un riepilogo"]

Applico al flow? (guided: conferma · yolo: conferma compatta · headless: applica)
```

In `--autonomous`/`mode:headless` applica senza gate (resta tracciato in `upstream`).

### I4. Scrivi anchor-preserving

Rigenera il `target_flow` applicando i delta **agli anchor risolti**, preservando tutti gli
anchor esistenti (ri-aggancio per similarità di titolo, mai rislugare da zero). Se un
`add-screen`/`change-outcome` tocca anche il Diagramma Mermaid o richiede un percorso/errore,
aggiorna in coerenza. Aggiorna il frontmatter del flow: `upstream` += il changeset,
`produced_at`, e marca le `weak_sections` toccate come riviste.

### I5. Report ingest

```
dm:flows ingest completato.

  Flow:      docs/dome/flows/flow-[feature].md
  Changeset: [path]  ([N] delta applicati, [M] anchor coniati, [K] ritirati)

Next step:
  /dm-prototype [feature]   — rigenera il prototipo pulito dal flow aggiornato
```

---

## Report

```
dm:flows completato.

Output:
  docs/dome/flows/flows-overview.md
  docs/dome/flows/flow-*.md ([N] file)

Next steps:
  /dm-domain-modeler   — modella le entità che questi flow manipolano
  (ordine logico della pipeline design: journeys → flows → domain-model)
```

---

## Regole

- **Mai costruire l'happy path interrogando step per step da zero:** in guided deduci dalle fonti, poi fai verificare
- **Lead con ciò che sai:** ogni domanda parte da ciò che hai già dedotto
- Il diagramma `graph TD` è sempre generato — non saltare mai
- La review cognitiva è sempre automatica — non chiedere all'utente
- Salva ogni flow immediatamente dopo Step E
- Modalità (guided/yolo/headless) scelta una volta, propagata
- feature-slug: lowercase, solo trattini, max 40 caratteri
- **Anchor degli elementi consumabili** (convenzione `02-principio-handoff`): step → colonna `id`
  (`s.<slug>`); schermata dello Screen Inventory → `sc.<slug>`; ramo alternativo/errore →
  `{#b.<slug>}` sul suo titolo; red-flag della Cognitive Analysis → `{#rf.<slug>}`. Anchor
  content-derived, **congelato alla nascita**.
- **Rigenerazione anchor-preserving:** quando rigeneri un flow esistente, ri-aggancia gli anchor già
  presenti agli elementi per similarità di titolo invece di rislugare da zero (mai rinumerare).
- **Ingest = stessa disciplina anchor:** l'assorbimento di un changeset applica i delta agli anchor
  dichiarati, conia solo su `add-screen`/`NEW`, ritira (mai riusa) su `remove`. È l'unico canale
  scrittura-flow per il round-trip: il prototipo non riscrive mai il flow da sé.

---

## Sentinella harvest

A completamento avvenuto (flow scritti, report finale prodotto), emetti nell'output la riga
sentinella. Il manifest dichiara `harvest_policy.method: auto-on-finalize`: il trigger engine
(`.claude/hooks/`) la rileva e propone `dome:harvest --method` per raccogliere feedback di metodo su
questa skill.

```
[[harvest:finalize skill=dm-flows]]
```
