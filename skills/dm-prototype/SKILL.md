---
name: dm:prototype
description: "Materializza un flow in un prototipo HTML navigabile con dati mock, per vedere il flow funzionare prima di costruirlo. Consuma un flow (e opzionalmente un domain-model) e produce un prototipo apribile in browser. Usa dopo dm:flows per validare un percorso o preparare un walkthrough cliente."
argument-hint: "[flow-path] [--deep] [--purpose validation|client] [--autonomous / mode:headless]"
---

# dm:prototype — Atomo Dominio Design (prototyping)

## Overview

Trasforma un **flow** (e l'eventuale **domain-model**) in un **prototipo HTML navigabile** con dati mock: un artefatto apribile in un browser, senza build step, che permette di percorrere il flow e vedere se regge prima di costruirlo.

L'output è **payload non-textual** (`index.html` + asset mock), quindi l'handoff vive in un **sidecar** `index.meta.yaml` con il frontmatter canonico (cfr. `02-principio-handoff`, sezione "Formati ammessi" — stesso schema di `colorize`). Il sidecar **è** l'handoff; l'HTML è il payload referenziato.

Implementa `03-principio-atomi` (è un atomo: interfaccia stabile, non orchestra, principio dichiarato, ≥2 consumatori — utente standalone + futuro composto `flow-to-prototype`), `02-principio-handoff` (consuma handoff flow, produce handoff sidecar) e i pattern `06-triage-complessita` (triage manuale a 2 livelli) e `08-modalita-esecuzione` (guided + headless).

## Domain context

Dominio: **design**. Categoria: **prototyping**.

**Confine critico con `dome-design`** (è ciò che giustifica l'atomo): `dome-design` lavora estetica e brand; `dm:prototype` lavora la **struttura navigabile** per validare che il flow regga. Anche in triage `deep` il prototipo resta strutturale, non un esercizio di marca. Se serve estetica di marca, si invoca `dome-design` **a valle** — non si gonfia questo atomo.

Carica `references/prototype-contract.md` (contratto dell'output HTML: wiring di navigazione, coerenza dei mock, regole no-build-step). Riusa la **disciplina** di HTML semantico / anti-slop già codificata in `dm-landing-render` come riferimento concettuale, **non** ne duplica il codice né i contratti brand.

## On Activation

### 1. Resume Check

Scansiona `docs/design/prototypes/*/index.meta.yaml`.

Se trovati, lista i prototipi esistenti (flow + `purpose` + `status`) e chiedi:

```
AskUserQuestion (header: "Prototipi"):
- Rigenera un prototipo esistente (seleziona quale)
- Crea un nuovo prototipo
```

### 2. Localizza e valida il flow upstream (obbligatorio)

Il flow è l'**upstream obbligatorio**: senza, l'atomo non procede.

- Se `flow-path` è passato come argomento, usa quello.
- Altrimenti scansiona `docs/design/flows/*-flow-*.md` (esclude `YYYY-MM-DD-flows-overview-01.md`). Se uno solo → usalo; se molti → chiedi quale.
- Se **nessun flow** esiste:
  ```
  Nessun flow trovato in docs/design/flows/.
  dm:prototype materializza un flow esistente — serve prima dm:flows.
  ```
  Offri di uscire e lanciare `dm:flows`. Non inventare un flow da zero.

Leggi il flow secondo il **flow-handoff-contract** (`../dome-shared/dm-design/flow-handoff-contract.md`):
- **Overview** — attori, trigger, **precondizioni** (definiscono lo stato d'ingresso)
- **Happy Path** — ogni step con `Schermata` (Screen ID), `Azione`, **System Response**, **Outcome →**
- **Screen Inventory** — una riga per schermata (`sc.<slug>`): elementi UI/CTA con il loro stato +
  gli **stati da materializzare** (enumerazione: default/vuoto/loading/errore/success)
- **Percorsi Alternativi / Scenari di Errore** — rami raggiungibili; ogni stato `errore` ha trigger+recovery

Lo Screen Inventory è **obbligatorio**: è il contratto materiale che evita di inventare. Se manca
(flow non conforme al contratto), **non improvvisare** — manda a `dm:flows` a rigenerare il flow.

**Domain-model (upstream opzionale).** Scansiona `docs/design/domain-model/YYYY-MM-DD-domain-model-01.md`. Se presente, caricalo: alza la qualità dei mock (campi ed entità coerenti). Se assente, procedi con mock plausibili e dichiaralo in `weak_sections`.

### 3. Mode Detection

| Flag | Modalità | Comportamento |
|------|----------|---------------|
| (nessuno) | **Guided** | Genera → l'utente apre e percorre → giudica se regge |
| `--autonomous` / `mode:headless` | **Headless** | Genera in silenzio, status `draft`, nessun gate utente |

`yolo` non è distinguibile da `guided` qui (la validazione è il percorrere il prototipo) → non è una modalità separata.

### 4. Dichiarazione del triage (manuale)

Il triage **non è deducibile** dal flow: lo stesso flow produce un loop sporco o un deliverable cliente a seconda di cosa serve. Quindi è **dichiarato dall'utente** (anti-pattern evitato: triage su segnali non misurabili).

```
AskUserQuestion (header: "Livello"):
- lightweight — sporco, mock essenziali, stile neutro di sistema · per il loop di validazione (default)
- deep — mock realistici, layout presentabile, navigazione completa · per la validazione cliente
```

Scorciatoie: `--deep` → deep. In headless: `lightweight` salvo `--deep`. Default: **lightweight** (il loop di studio è il caso più frequente).

`--purpose validation|client` registra l'uso del prototipo nel sidecar (non è il triage). Default: `validation`.

---

## Generazione Prototipo

### A. Mappa Screen Inventory → schermate

Una schermata navigabile per ogni **riga dello Screen Inventory** (`sc.<slug>`), **non** per
step: due step sulla stessa schermata la condividono (niente duplicati). Gli step dell'Happy
Path si agganciano alle schermate via la colonna `Schermata`. I percorsi alternativi/errori
diventano stati/diramazioni raggiungibili (non vicoli ciechi non dichiarati).

### B. Dati mock e stati

Per ogni schermata, renderizza **tutti gli stati da materializzare** dichiarati nello Screen
Inventory (default/popolato · vuoto · loading · errore · success) — **sempre tutti**, anche in
`lightweight` (sono contratto strutturale, non decorazione; gli stati off-path sono ciò che
altrimenti si inventa). Esponi uno **state-switcher** per percorrerli. Cambia la *cura* col
triage, non la copertura degli stati.

Popola con dati mock: lo **Stato dati** dello Screen Inventory vincola cosa mostrare (popolato
vs vuoto). Se il domain-model è presente, i mock **rispettano entità e campi**; se assente, lo
Screen Inventory resta il vincolo (meno invenzione). `lightweight`: valori essenziali; `deep`:
realistici e coerenti tra schermate.

### C. Wiring di navigazione e comportamento

Collega le schermate secondo il flow con link/azioni reali (`<a>`, `<button>`):
- **CTA con stato:** ogni CTA rispetta la sua *condizione di enable* (disabilitata finché non
  valida) e il *comportamento in-flight* (loading allo click); errori di campo **inline accanto
  al campo**, focus sul primo errore.
- **System Response:** dopo l'azione, mostra ciò che lo Screen Inventory/Happy Path dichiara —
  non inventare il feedback.
- **Outcome →:** ogni azione transita verso lo Screen ID dichiarato; nessun vicolo cieco muto.
- **Error-state:** ogni stato `errore` di una schermata è raggiungibile dal suo trigger e offre
  il recovery dichiarato.
- **Azioni distruttive:** se un Outcome → è terminale/irreversibile, richiedi conferma esplicita.

### D. Render HTML (no build step)

Genera `index.html` apribile direttamente in un browser. HTML semantico, nessuna dipendenza da build/bundler. **Dati mock inline** (un `fetch` di `assets/mock.json` è bloccato sotto `file://` da molti browser e lascerebbe le schermate vuote); il file mock esterno è ammesso solo se il prototipo verrà *servito*. Vedi `references/prototype-contract.md` §1.
- **lightweight:** stile neutro di sistema, minimo CSS.
- **deep:** layout presentabile, navigazione completa. Resta **strutturale** (no brand — vedi Domain context).

Dettaglio del contratto in `references/prototype-contract.md`.

### E. Self-Check (Review interna, silenziosa)

Prima di scrivere, verifica:
1. Ogni riga dello Screen Inventory ha una schermata navigabile, e ogni step la referenzia?
2. Ogni schermata renderizza **tutti** gli stati da materializzare dichiarati (vuoto/loading/errore inclusi)?
3. Ogni CTA rispetta enable + comportamento in-flight; gli errori sono inline?
4. Ogni stato `errore` è raggiungibile dal suo trigger e offre il recovery?
5. I link/azioni funzionano — nessun vicolo cieco non dichiarato; azioni distruttive con conferma?
6. I mock sono coerenti con lo Screen Inventory (e col domain-model se presente)?
7. Il prototipo si apre in un browser senza build step?

Se una risposta è "no", correggi. Annota i limiti residui in `weak_sections`.

### F. Scrittura payload + sidecar

Crea `docs/design/prototypes/YYYY-MM-DD-[flow-slug]-01/`:
- `index.html` (+ eventuale `assets/mock.json`) — il payload
- `index.meta.yaml` — il sidecar handoff:

```yaml
date: [YYYY-MM-DD]
project: [project-name]
phase: design
status: draft          # complete solo dopo la validazione utente (Guided)
generated_by: dm:prototype
upstream:              # catena verso il flow (e domain-model se usato)
  - docs/design/flows/YYYY-MM-DD-flow-[slug]-01.md
  - docs/design/domain-model/YYYY-MM-DD-domain-model-01.md   # ometti se assente
source_flow: docs/design/flows/YYYY-MM-DD-flow-[slug]-01.md
purpose: [validation|client]
triage: [lightweight|deep]
screens_count: [int]
payload: index.html
weak_sections: [es. "domain-model assente: mock plausibili non verificati" o "nessuno"]
```

---

## Validazione (Guided)

In Guided, il **gate è l'utente** — nessun confidence-gating automatico.

```
Prototipo pronto: docs/design/prototypes/YYYY-MM-DD-[flow-slug]-01/index.html
Aprilo nel browser e percorri il flow.
```

```
AskUserQuestion (header: "Il flow regge?"):
- Regge — conferma il prototipo (status: complete)
- Non regge — rientra in dm:flows con il finding di cosa si è rotto
```

- **Regge:** aggiorna il sidecar a `status: complete`.
- **Non regge:** lascia `status: draft`, raccogli il finding (quale step/azione si è rotto) e indica `/dm-flows` per correggere il flow upstream. Il loop `flow ⇄ prototype` è il cuore della validazione.

In Headless: salta il gate, lascia `status: draft`.

---

## Report

```
dm:prototype completato.

Output:
  docs/design/prototypes/YYYY-MM-DD-[flow-slug]-01/index.html   (payload)
  docs/design/prototypes/YYYY-MM-DD-[flow-slug]-01/index.meta.yaml   (handoff sidecar)

Flow: [feature]  ·  Schermate: [N]  ·  Triage: [lightweight|deep]  ·  Status: [draft|complete]

Next steps:
  Apri index.html e percorri il flow.
  Non regge?  →  /dm-flows   (correggi il flow, poi rigenera il prototipo)
  Serve estetica di marca?  →  dome-design (a valle, sul prototipo strutturale)
```

---

## Regole

- **Il flow è obbligatorio:** mai inventare un flow da zero — se manca, manda a `dm:flows`
- **Screen Inventory obbligatorio:** è il contratto materiale (`flow-handoff-contract`); se assente, flow non conforme → `dm:flows`. Una schermata per `sc.<slug>`, non per step
- **Tutti gli stati dichiarati, sempre renderizzati** (anche in lightweight): vuoto/loading/errore inclusi — sono struttura, non decorazione
- **CTA con stato, System Response reale:** enable + in-flight; feedback dallo Screen Inventory, mai inventato
- **Triage sempre dichiarato dall'utente:** mai dedurlo dal flow (non è un segnale misurabile)
- **Resta strutturale:** anche in `deep` niente brand/estetica di marca → quello è `dome-design` a valle
- Mock coerenti con il domain-model quando presente — niente campi inventati o relazioni rotte
- Nessun build step: `index.html` apribile direttamente in un browser
- Il sidecar `index.meta.yaml` è l'handoff: sempre presente, con `upstream` verso il flow
- `status: complete` solo dopo che l'utente ha percorso il prototipo e confermato (Guided)
- flow-slug: lowercase, solo trattini, max 40 caratteri
