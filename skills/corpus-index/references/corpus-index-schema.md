# Schema dell'artefatto `corpus-index`

Handoff markdown. Frontmatter canonico (`02-principio-handoff`) + campi specifici.

## Frontmatter

```yaml
---
title: "Corpus Index — [corpus-dir]"
date: [YYYY-MM-DD]
type: corpus-index
role: single
status: complete
process: corpus-index
produced_by: corpus-index@[version]
produced_at: [ISO-8601]
principle_versions: handoff@1.1.0
upstream: [corpus-dir]
id: corpus-index-[slug]
indexed: [N]                 # totale elementi indicizzati
by_type: { flow: n, journey: n, persona: n, domain-model: n, brief: n, distillate: n, unclassified: n }
collisions: 0                # deve essere 0
sources:                     # un hash per documento → base del delta/staleness
  - { path: [...], type: [...], role: [...], elements: [n], hash: [sha] }
reconciliation:              # esito della rete di sicurezza (Stadio 3)
  flagged_docs: []           # doc a resa anomala (es. resa 0 su doc denso)
delta:                       # solo sui re-run; assente al primo run
  new: [n], changed: [n], retired: [n]
---
```

## Corpo

### § Indice degli elementi
Per documento, la lista degli ID `<doc-id>#<kind>.<anchor>` con il titolo. È l'insieme di verità contro
cui il consumatore verifica la copertura (gate no-omission).

### § Menzioni di schermata (grezze)
Lista delle schermate menzionate nel corpus (non indicizzate con anchor). Input per l'elenco-pagine di
sintesi a valle.

### § Unclassified / segnalazioni
Documenti con `type` ignoto e doc segnalati dalla riconciliazione (resa anomala). Non bloccano, ma il
consumatore deve vederli.

## Invarianti
- `collisions == 0`.
- `indexed == somma(by_type)`.
- Ogni elemento ha un ID ben formato e doc-scoped-unico.
- Su re-run, `delta` riflette il confronto degli `hash` per documento.
