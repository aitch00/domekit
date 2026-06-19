# brief-frame — contratto condiviso del frame del product brief

Il **frame** è l'insieme degli **slot informativi** che un product brief (e gli artefatti a
valle) si aspettano dal materiale. È la **fonte di verità unica** del "cosa serve sapere",
consumata da due lati senza duplicazione (principio DRY del dome-method):

- **`dm:intake`** lo usa come `--against` per calcolare i **gap** (quali slot non sono coperti
  dalle unità estratte) e per orientare l'estrazione altitude-aware.
- **`dm:brief`** lo usa nella discovery (quali slot sono attesi per lo scenario) e
  nell'elicitation (la checklist mentale e la lente di rigore per ogni gap).

Sostituisce e unifica due frame prima duplicati: `dm-intake/references/expected-frame.md`
e la checklist a 4 cluster di `dm-brief-elicitation`.

## Database L3: `brief-frame.csv`

Una riga per **slot**. Colonne:

| colonna | significato |
|---|---|
| `slot_id` | identificatore stabile dello slot (snake_case) |
| `cluster` | raggruppamento: `vision-problem` · `users-value` · `market-diff` · `success-scope` · `domain` · `journey` |
| `label` | descrizione leggibile dello slot |
| `evidence_kinds` | i **kind** di unità `dm:intake` che soddisfano lo slot (pipe-list); è il ponte fra extracted-context e copertura del frame |
| `consumers` | chi a valle consuma lo slot (`brief` · `personas` · `journeys` · `domain-model`); pipe-list. Allinea il `--against` di intake |
| `req_feature` / `req_module` / `req_new_product` / `req_unknown_market` | se lo slot è **richiesto** per quello **scenario** del brief (`yes`/`no`). È il gap-framework data-driven (pattern `documentation-requirements.csv`) |
| `elicit_lens` | la lente di rigore da applicare se lo slot è gap, in elicitation: `specificity` · `evidence` · `counterfactual` · `attachment` |

## Come si usa (lookup deterministico)

**Per lo scenario** (`feature|module|new-product|unknown-market`): filtra le righe con
`req_<scenario> = yes` → sono gli slot **attesi** per quel brief. Gli altri sono opzionali.

**Per la copertura (gap)**: per ogni slot atteso, cerca nell'`extracted-context` ≥1 unità con
`kind ∈ evidence_kinds`. Nessuna → `missing`; solo `reasoned` o singola debole → `thin`;
unità in conflitto → `contradicted`; altrimenti `covered`.

**Per l'elicitation**: uno slot `missing`/`thin` di uno scenario atteso → si interroga con la
`elicit_lens` indicata (non un questionario rigido: lente di rigore, pattern bmad
guided-elicitation "lead with what you know → gap question → confirm → soft gate").

## Asse altitudine (come si riempie lo slot, non quali slot)

Lo **scenario** decide *quali* slot sono richiesti; l'**altitudine** della sorgente decide
*come* si riempiono (vedi `dm-intake/references/source-altitude.md`):

| Altitudine sorgente | Trattamento per riempire gli slot |
|---|---|
| **raw** (note, transcript, dump) | `dm:intake` estrae a grana fine → unità → copertura per `evidence_kinds` |
| **structured** (tabelle, CSV, spec parziali) | estrazione a grana media, mapping diretto campo→slot |
| **synthesized** (PRD, brief esistente) | **non** si ri-estrae a unità: si mappano le **sezioni** del documento agli slot (spina dorsale) e si validano; i grezzi riempiono i buchi |

## Estensibilità

Aggiungere uno slot = aggiungere una riga (nuovo `slot_id`, cluster, evidence_kinds,
applicabilità per scenario). Nessun consumatore va modificato: sia intake sia brief leggono
il CSV come dato. Un dominio non-design può sostituire il CSV con il proprio frame mantenendo
lo schema delle colonne.

## Invarianti

- `slot_id` univoco e stabile (mai rinumerato).
- Ogni slot ha ≥1 `evidence_kinds` e ≥1 `consumers`.
- Almeno gli slot del cluster `success-scope` + `target_users` + `value_prop` sono `req_*=yes`
  per ogni scenario (il minimo per scrivere un brief, allineato a bmad product-brief).
