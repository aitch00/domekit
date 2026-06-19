---
name: corpus-index
description: "Indicizza un'intera cartella di handoff-artifact (brief, personas, journeys, flows, domain-model, …) in un corpus-index: la lista di tutti gli elementi consumabili con anchor-ID stabili, più menzioni grezze di schermata, delta e segnale di staleness sui re-run. Atomo universale (non solo design). Scan ibrido per type/role, indicizzazione a grana fine, stamp dei doc legacy, riconciliazione LLM come rete di sicurezza. Usa prima di sintetizzare un corpus (dm:blueprint) o quando aggiungi un documento e vuoi sapere cosa è cambiato."
argument-hint: "[corpus-dir] [--stamp] [--autonomous / mode:headless]"
---

# corpus-index — Atomo universale di indicizzazione del corpus

## Overview

Prende **una cartella** di handoff-artifact e produce un **corpus-index**: l'inventario completo degli
**elementi consumabili** del corpus, ciascuno con un **anchor-ID stabile** (`<doc-id>#<kind>.<anchor>`,
convenzione di `02-principio-handoff`). È il front-end meccanico che separa l'**indicizzazione** (questo
atomo) dalla **sintesi** (il consumatore a valle, es. `dm:blueprint`) e dalla **verifica** (il gate del
consumatore).

Risolve due problemi: (1) **scarica il contesto** — il consumatore legge l'indice scritto invece di
ri-leggere N documenti grezzi; (2) **rende l'ingestione incrementale** — su re-run emette un delta e un
segnale di staleness, così chi ha già consumato l'indice sa di essere disallineato quando il corpus cresce.

Confine: corpus-index **indicizza, non sintetizza** e **non decide la consumabilità**. Indicizza a
**grana fine** (vedi Regole) e lascia la consolidazione al consumatore. È **universale**: qualunque
corpus di handoff-artifact con frontmatter conforme, non solo design.

Implementa `03-principio-atomi` (interfaccia stabile cartella → corpus-index, ≥2 consumatori:
`dm:blueprint` + `dm:product`), `02-principio-handoff` (produce un handoff). Dispatcha agent interni
(`index-worker`, `coverage-reconciler`) → resta **L1** (pattern `01-orchestratore-multi-agente`,
sezione "Posizione architetturale"). Calibra la delega sul triage (`06-triage-complessita`).

## Domain context

Carica on-demand (pattern `07-reference-loading`):
- `references/id-scheme.md` — lo schema dell'anchor-ID e il suo ciclo di vita (rimanda a `02`).
- `references/extraction-registry.md` — il registro `type → kind elementi → carrier dell'anchor`.
- `references/corpus-index-schema.md` — lo schema dell'artefatto di output.

## On Activation

### 1. Localizza il corpus
`corpus-dir` esplicito, oppure la cartella dome di default. Scansiona **tutto** ciò che trova
(nessun elenco di path hardcoded). Se la cartella è vuota o senza handoff-artifact → segnala e fermati.

### 2. Mode & stamp
| Flag | Comportamento |
|------|---------------|
| (nessuno) / `--autonomous` | Indicizza in sola lettura; non modifica i doc. |
| `--stamp` | Sui doc legacy privi di anchor, **riscrive** gli anchor inline (migrazione una tantum, idempotente). |

### 3. Triage (upfront, propagato)
Pesa `volume_documenti` ed `elementi_stimati` (vedi manifest `triage`). Determina `delegation_policy`
(`in-thread | selective | mandatory`) e propagala agli stadi seguenti.

## Esecuzione (stadi)

### Stadio 1 — Scan ibrido + classificazione
Per ogni file: leggi il frontmatter e classifica per **`type`** + **`role`** (`single|overview`).
Fallback per il legacy non conforme: deduci la classe da `generated_by`/`process` e il `role` dal nome
file (`*-overview`). Un `type` ignoto → bucket **`unclassified`**, indicizzato comunque e **segnalato**
(mai scartato in silenzio).

### Stadio 2 — Indicizzazione con anchor-ID (+ stamp)
Per ogni documento, estrai gli **elementi consumabili** secondo `references/extraction-registry.md` e
assegna a ciascuno un ID `<doc-id>#<kind>.<anchor>` (vedi `references/id-scheme.md`). Estrai anche le
**menzioni grezze di schermata** (input per l'elenco-pagine di sintesi a valle).
- A regime (doc con anchor inline) la lettura è uniforme.
- Sui doc legacy si calcola l'anchor dal contenuto; con `--stamp` lo si **riscrive nel doc**.
- **Delega:** se `delegation_policy != in-thread`, dispatcha un `index-worker` **per documento**
  (paralleli se indipendenti) per non saturare il contesto; l'orchestratore raccoglie gli indici parziali.

### Stadio 3 — Riconciliazione LLM (rete di sicurezza)
Dispatcha `coverage-reconciler`: per ogni documento confronta il **count meccanico** con una stima LLM
della densità e **segnala i doc a resa anomala** (es. un documento strutturalmente denso che rende `0`).
È la mitigazione del single-point-of-failure dell'extractor coverage: ciò che l'estrattore non parsa
diventerebbe invisibile, e nessun gate a valle può segnalare ciò che non è mai stato indicizzato.

### Stadio 4 — Output + delta + staleness
Scrivi il `corpus-index` (vedi `references/corpus-index-schema.md`) **in `docs/dome/corpus-index/`**
(crea la cartella se non esiste), nome file `corpus-index-<slug>.md` dove `<slug>` deriva dalla
`corpus-dir` indicizzata. **Non** scriverlo dentro la cartella analizzata: l'output durevole vive nel
corpus dome (`B3` di AGENTS.md), e l'indice non deve inquinare ciò che indicizza né essere riassorbito
come sorgente al re-run. Override esplicito solo se l'utente passa un path di destinazione.
Il file contiene: l'indice di tutti gli ID,
le menzioni-schermata, un **hash per documento**. Su re-run, calcola il **delta**
(`{nuovi, cambiati, ritirati}`) confrontando gli hash, e marca l'indice aggiornato. Un consumatore che
ha salvato l'hash dell'indice consumato può così rilevare di essere **stale**.

## Output

Un `corpus-index` markdown (handoff) conforme a `references/corpus-index-schema.md`, scritto in
`docs/dome/corpus-index/corpus-index-<slug>.md`. Frontmatter `type: corpus-index`, `role: single`,
`indexed: [N]`, campi handoff canonici, hash per sorgente.

## Regole
- **Indicizza, non sintetizzare:** nessun giudizio di consumabilità — quello è del consumatore.
- **Sbaglia fine, non grosso:** in dubbio indicizza **di più**. Over-indicizzare è sicuro (il gate a
  valle forza a consumare/dichiarare il di più); under-indicizzare è una perdita silenziosa.
- **Mai scartare in silenzio:** un type ignoto va in `unclassified` e si segnala.
- **Anchor stabili:** `<doc-id>#<kind>.<anchor>`, content-derived, frozen-at-birth, never-renumber
  (vedi `references/id-scheme.md`).
- **Delega sopra soglia:** rispetta `delegation_policy` del triage; non indicizzare un corpus deep
  tutto in-thread.
- **Universale:** nessun assunto specifico di dominio oltre al registro di estrazione (estensibile).
- **Output nel corpus dome, non nella cartella analizzata:** l'indice va sempre in
  `docs/dome/corpus-index/`, mai dentro la `corpus-dir` (evita di inquinare le sorgenti e di
  ri-indicizzare sé stesso al re-run). Override solo su path esplicito dell'utente.
