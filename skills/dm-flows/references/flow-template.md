# Flow — Template di output

Scheletro canonico dei file prodotti da `dm:flows`. La skill **non** ridichiara la
struttura nel corpo: la legge da qui. Questo template è l'**istanza** dello schema
condiviso [`flow-handoff-contract@1.0.0`](../../_shared/dm-design/flow-handoff-contract.md)
— il contratto a cui `dm:flows` produce-conforme e i consumatori (`dm:prototype`,
`dm:blueprint`) leggono-conforme. Se cambi la struttura, bumpa **anche** il contratto. Mantiene il contratto handoff
(`02-principio-handoff@1.1.0`) — frontmatter con `upstream`/`weak_sections`, `role`
(`single`|`overview`), `id` e anchor di elemento congelati alla nascita.

**Legenda placeholder:** `[…]` = valore dedotto/da compilare · `s.<slug>` = anchor di
step (colonna `id`) · `b.<slug>` = anchor di ramo alternativo/errore · `rf.<slug>` =
anchor di red-flag della Cognitive Analysis. Anchor content-derived (lowercase,
trattini, congelato). In rigenerazione ri-aggancia gli anchor esistenti per similarità
di titolo invece di rislugare (mai rinumerare).

---

## 1 · Flow (role: single)

File: `docs/dome/flows/flow-[feature-slug].md`

```markdown
---
title: "Flow: [Nome Feature]"
date: [YYYY-MM-DD]
type: flow
role: single
status: draft
process: dm-flows
produced_by: dm:flows@[version]
produced_at: [ISO-8601]
principle_versions: handoff@1.1.0
upstream: [lista path fonti consumate o "input diretto"]
id: flow-[feature-slug]
project: [project-name]
feature: [nome feature]
actors: [attori]
weak_sections: [step dedotti a bassa confidenza o "nessuno"]
---

# Flow: [Nome Feature]

## Overview

| Campo | Valore |
|-------|--------|
| Descrizione | [1–2 frasi] |
| Attori | [attori] |
| Trigger | [cosa avvia il flow] |
| Precondizioni | [precondizioni o "Nessuna"] |

## Happy Path

La colonna `id` porta l'anchor stabile dello step (`s.<slug-del-titolo>`), congelato
alla nascita. In rigenerazione ri-aggancia gli anchor esistenti per similarità di
titolo invece di rislugare. `Schermata` referenzia uno **Screen ID** dello Screen
Inventory (sotto), così due step sulla stessa schermata sono espliciti e non duplicati.
`Outcome →` è la **transizione**: lo Screen ID (o esito) verso cui porta l'azione.

| id | # | Step | Attore | Schermata | Azione | System Response | Outcome → |
|----|---|------|--------|-----------|--------|-----------------|-----------|

- **System Response:** cosa mostra/fa il sistema dopo l'azione (feedback, validazione,
  cambio stato) — così il prototipo non lo inventa.
- **Outcome →:** Screen ID di destinazione (`sc.<slug>`) o esito terminale.

## Screen Inventory

Il **contratto materiale** verso `dm:prototype`: una riga per schermata distinta toccata
dal flow. Chiude il "campo libero" — il prototipo materializza queste schermate senza
inventare. Screen ID stabile `sc.<slug>`, congelato alla nascita.

> **Aggancio a `dm:blueprint` A5.** Questa è la vista *per-flow*; è il **feeder granulare**
> dello **Screen inventory A5** del blueprint, che la **aggrega cross-flow** (stessa schermata
> toccata da più flow → una sola riga A5, con tutti gli stati). Il blueprint consuma questi
> `sc.<slug>` come sorgente delle righe A5. Nomenclatura unica da consolidare a valle (oggi i
> due livelli convivono dichiarati).

| Screen ID | Nome schermata | Elementi UI / CTA (con stato) | Stati da materializzare |
|-----------|----------------|-------------------------------|-------------------------|
| sc.[slug] | [nome] | [campi + ogni CTA con *condizione di enable* · *comportamento in-flight*] | [**tutti** gli stati applicabili: default/popolato · vuoto · loading · errore · success] |

**Regole del contratto (chiudono il "campo libero", non lasciano inventare al prototipo):**

- **Stati da materializzare = enumerazione, non scelta singola.** Elenca **tutti** gli stati
  che la schermata deve rendere, inclusi gli off-path (vuoto / loading / errore): sono
  proprio quelli che il prototipo inventa di più (~30% dell'uso reale è off-path).
- **Ogni CTA porta il suo stato:** *condizione di enable* (es. "abilitata se form valido") e
  *comportamento in-flight* (es. "spinner, non disabilitare in submit"). Errori di campo
  **inline accanto al campo**, focus sul primo errore — mai un solo errore in cima.
- **Ogni schermata con stato `errore` lega trigger + recovery:** referenzia il ramo in
  `## Scenari di Errore` (anchor `{#b.<slug>}`) — l'error-path non è opzionale se lo stato è
  dichiarato qui.
- **Azioni distruttive** (Outcome→ terminale/irreversibile): richiedono conferma esplicita.

## Diagramma

\`\`\`mermaid
graph TD
    …
\`\`\`

## Percorsi Alternativi
[omettere se saltato — ramo: da quale step, condizione, step, se si ricongiunge; titolo con anchor {#b.<slug>}]

## Scenari di Errore
[omettere se saltato — a quale step, trigger, cosa vede l'utente, come recupera; titolo con anchor {#b.<slug>}]

## Success Criteria
[omettere se saltato — criteri/metriche misurabili]

## Cognitive Analysis

Ogni red-flag porta un anchor `{#rf.<slug>}`. [risultati review automatica: Hick's Law,
Miller 7±2, context switching — vedi references/cognitive-analysis.md]
```

---

## 2 · Flows Overview (role: overview)

File: `docs/dome/flows/flows-overview.md`

```markdown
---
title: "Flows Overview — [project]"
date: [YYYY-MM-DD]
type: flow
role: overview
status: complete
process: dm-flows
produced_by: dm:flows@[version]
produced_at: [ISO-8601]
principle_versions: handoff@1.1.0
upstream: [i flow consumati]
id: flows-overview
---

# Flows Overview

## Summary Table

| Feature | Attori | Trigger | Step | Red Flag |
|---------|--------|---------|------|----------|

## Cross-Flow Cognitive Findings

Pattern ricorrenti tra i flow, problemi sistemici, raccomandazioni prioritarie. Ogni
finding è un heading `### Titolo {#finding.<slug>}` (anchor stabile).

## Flow Index

| Flow | File |
|------|------|
| [nome] | `docs/dome/flows/flow-[slug].md` |
```
