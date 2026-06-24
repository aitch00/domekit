---
name: dm:revise
description: "Rivede un flow editando il suo prototipo: sessione guidata in cui chiedi modifiche, le vedi applicate all'HTML, e mentre le applica le registra mappate a schermata/step. Su 'soddisfatto' esporta un changeset e lo fa assorbire dal flow (via dm:flows ingest). Usa per raffinare un flow vedendo le modifiche, non riscrivendo l'md a mano."
argument-hint: "[prototipo] [--flow <flow.md>] [--yolo] [--autonomous / mode:headless] [--no-apply]"
---

# dm:revise — Atomo Dominio Design (round-trip edit→flow)

## Overview

Atomo-dominio L1 del design. Apre un prototipo HTML (prodotto da `dm:prototype` da un
`*-flow-*.md`), conduce una **sessione di edit guidata** in cui l'utente chiede modifiche che
vengono **applicate all'HTML live per farle vedere**, e **mentre le applica le registra**
mappate alla schermata/step del flow (`sc.<slug>` / `s.<slug>`). Su "soddisfatto" esporta un
`*-changeset-*.md` conforme a `changeset-contract@1.0.0` e **chiude chiamando `dm:flows --ingest`**
(handoff): il flow — la verità durevole — assorbe i delta anchor-preserving; l'HTML è usa-e-getta.

Implementa `03-principio-atomi@1.1.0`. È il **lato produttore** del round-trip edit→flow; il lato
ricevente è il ramo `ingest` di `dm:flows` (`changeset-contract`). Diverge da `dm:variants` (che
esplora alternative strutturali a estetica invariata): `dm:revise` raffina **una** scelta e la
deposita nel contratto.

## Domain context

Dominio: design. Categoria: ux-research (round-trip). **Non** consuma database L3.

Produce-conforme a [`../dome-shared/dm-design/changeset-contract.md`](../dome-shared/dm-design/changeset-contract.md)@1.0.0
(handoff verso `dm:flows`). Assume in input un **prototipo materializzato da un flow**: il
prototipo porta il riferimento al `*-flow-*.md` di provenienza (sidecar/meta o, in fallback,
`--flow`). Questo è il vincolo che tiene il processo semplice → vedi *Vincolo* sotto.

> **Vincolo (perché resta semplice):** edit e cattura avvengono **nella stessa sessione**, che ha
> in contesto la mappa flow↔HTML (il prototipo è stato reso dal flow: ogni sezione corrisponde a un
> `sc.<slug>`). Così ogni edit è **ancorato live, gratis** — niente diff semantico, niente
> fingerprint. Funziona **solo editando attraverso `dm:revise`**: se l'HTML viene smanettato a mano
> in un editor esterno, la mappatura è persa e questo atomo non la ricostruisce.

## On Activation

### 1. Risolvi prototipo e flow bersaglio

- Prototipo: dall'argomento, o chiedi quale prototipo rivedere.
- `target_flow`: risolvi in cascata —
  1. `--flow <flow.md>` se passato (vince sempre);
  2. dal meta/sidecar del prototipo (`source_flow` / `upstream`): se il path **risolve su disco**, usalo;
  3. **se il path del meta non esiste** (tipico in repo rilocati: il meta porta il path *canonico di
     generazione* `docs/design/flows/...`, non la posizione reale), **cerca per basename** il
     `flow-<slug>.md` sotto l'albero del progetto (es. `**/flows/flow-<slug>.md`); se c'è un solo
     match, usalo; se più d'uno, chiedi quale;
  4. ultimo fallback: chiedi.
  **Senza un flow bersaglio risolto, ferma**: il changeset ha bisogno di `target_flow`.
- Carica il `*-flow-*.md` per avere la **mappa anchor** (Screen Inventory `sc.<slug>` + Happy Path
  `s.<slug>`): è la legenda con cui ancori ogni edit.
- **Gate di conformità del flow.** Se il flow non ha mappa anchor (Screen Inventory assente, zero
  `sc.`/`s.` — flow **legacy pre-`flow-handoff-contract@1.0.0`**), **ferma con rimedio**: *"il flow
  `<path>` precede il contratto: rigeneralo con `dm:flows` (aggiunge lo Screen Inventory) prima di
  revisionarlo."* Senza mappa non c'è nulla a cui ancorare gli edit — non degradare sugli id
  dell'HTML (rimanderebbe il problema all'ingest, che giustamente rifiuterebbe).

### 2. Mode Detection

| Flag | Modalità | Comportamento |
|------|----------|---------------|
| (nessuno) | **Guided** | Loop di edit interattivo, log live, gate prima di export e apply |
| `--yolo` | **Yolo** | Edit rapidi, conferme compatte |
| `--autonomous` / `mode:headless` | **Headless** | Applica una lista di edit passata, zero interazione |

`--no-apply`: esporta il changeset ma **non** chiama `dm:flows` (handoff manuale a valle).

## Sessione di edit (loop — ripeti fino a "soddisfatto")

### A. Richiesta di modifica

L'utente chiede una modifica in linguaggio naturale ("aggiungi un campo telefono al checkout",
"dopo il pagamento vai al riepilogo, non alla home", "serve una schermata di riepilogo").

### B. Applica all'HTML (transitorio — per *vedere*)

Applica la modifica all'HTML del prototipo, così l'utente la vede. Questa modifica è **effimera**:
serve a valutare se ha senso. L'HTML non è la verità — il flow lo è.

### C. Classifica e logga (il filtro decisione-vs-ritocco)

Decidi se la modifica è **di contratto** o **di materializzazione** (test stile ADR: è
hard-to-reverse / cambia il comportamento / aggiunge struttura? → contratto; sposta/ridimensiona/
ricolora? → ritocco):

| Esito | Azione |
|-------|--------|
| **Contratto** | logga una riga di delta: `anchor` (`sc.<slug>` / `s.<slug>` / `NEW`) · `tipo` (`add-element`/`add-state`/`change-behavior`/`change-outcome`/`add-screen`/`remove`) · `richiesta` (la frase) · `dettaglio` (il cambiamento sul flow). Vedi il contratto per gli atterraggi. |
| **Materializzazione** | applicata all'HTML per *vedere*, **non loggata**: è effimera, si perde alla rigenerazione. Dillo brevemente ("ritocco visivo, non tocca il contratto"). |

L'anchor si legge dalla mappa del flow (passo 1): la sezione editata corrisponde a un `sc.<slug>`;
una nuova schermata richiesta → `NEW`.

**Caso ambiguo (non forzare la classificazione).** Alcuni edit hanno *entrambe* le facce — es.
"sposta il campo telefono *e rendilo obbligatorio*" (ritocco + cambio di contratto), o "raggruppa
questi 3 campi in una sezione 'Recapiti'" (è solo presentazione o introduce un raggruppamento
semantico?). Quando la faccia-contratto non è netta:
- **guided:** *chiedi*, guidando con la tua ipotesi — *"questo lo leggo come cambio di contratto
  (campo ora obbligatorio): lo registro nel changeset, o è solo presentazione?"*. Non decidere in
  silenzio su un edit load-bearing.
- **yolo / headless:** **scomponi** l'edit nelle sue parti (la parte che cambia *cosa fa il sistema*
  → contratto; la parte di sola resa → ritocco) e logga **solo** la prima. Se resta indecidibile,
  **non** inquinare il flow: trattalo come ritocco ma **segnalalo nel report** come "edit ambiguo non
  registrato", così è rivedibile invece che perso in silenzio.

Principio: meglio una domanda (guided) o una nota nel report (autonomo) che un delta di contratto
inventato o un cambio di comportamento perso.

### D. Continua?

```
AskUserQuestion (header: "Edit"):
- Altra modifica
- Sono soddisfatto — esporta il changeset
```

## Export del changeset (su "soddisfatto")

Scrivi `docs/design/changesets/YYYY-MM-DD-changeset-<feature-slug>-01.md` conforme a
[`changeset-contract@1.1.0`](../dome-shared/dm-design/changeset-contract.md): frontmatter
(`type: changeset`, `target_flow`, `upstream: [<prototipo>, <target_flow>]`, `consumed_by: dm:flows`,
`status: complete`, **`source: edit`**) + sezione `## Delta` con tutte le righe di contratto loggate.
(`source: edit` segna la provenienza: edit deliberato, non ipotesi da validazione — vedi *Produttori*
nel contratto.) **Solo delta di
contratto** (i ritocchi non entrano). Se nessun delta di contratto è stato loggato, dillo e
fermati: non c'è nulla da depositare nel flow.

## Apply (handoff → dm:flows ingest)

Salvo `--no-apply`, chiudi invocando il ramo ricevente:

```
Skill dm-flows  (argomenti: --ingest docs/design/changesets/YYYY-MM-DD-changeset-<feature-slug>-01.md, + modalità)
```

`dm:flows` ingest applica i delta al `target_flow` anchor-preserving **con il proprio gate** (è il
proprietario del flow: l'unico che lo riscrive). `dm:revise` **non scrive mai il flow da sé**.

## Output

- `docs/design/changesets/YYYY-MM-DD-changeset-<feature-slug>-01.md` — handoff `changeset-contract@1.0.0`.
- (effetto, via dm:flows) `docs/design/flows/flow-<feature-slug>.md` aggiornato anchor-preserving.

```
dm:revise completato.

  Changeset: docs/design/changesets/YYYY-MM-DD-changeset-<feature>-01.md  ([N] delta di contratto, [R] ritocchi scartati)
  Flow:      docs/design/flows/flow-<feature>.md  (aggiornato via dm:flows ingest)

Next step:
  /dm-prototype <feature>   — rigenera il prototipo pulito dal flow aggiornato
```

## Regole

- **Il flow è la verità, l'HTML è usa-e-getta:** ogni delta di contratto deve finire nel changeset;
  i ritocchi di materializzazione vivono e muoiono nel prototipo.
- **Ancoraggio live, non diff:** l'anchor di ogni edit si legge dalla mappa del flow caricata a
  inizio sessione; non ricostruire la mappatura a posteriori.
- **Solo contratto nel changeset:** filtra a monte (decisione-vs-ritocco); l'ingest assume un
  changeset già pulito.
- **Mai scrivere il flow direttamente:** l'apply è sempre delegato a `dm:flows --ingest` (handoff).
  Il proprietario del flow è uno solo.
- **Editing solo attraverso l'atomo:** la semplicità dipende dal fatto che edit e cattura siano nella
  stessa sessione; HTML editato fuori non è supportato.
- feature-slug: lowercase, solo trattini, max 40 caratteri.
- Anchor coniati per `add-screen`/`NEW`: content-derived, congelati alla nascita; `remove` ritira
  l'anchor (mai riusato) — la disciplina è del contratto e dell'ingest.
