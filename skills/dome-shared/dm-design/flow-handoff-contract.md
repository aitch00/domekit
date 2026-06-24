# Flow Handoff Contract — il contratto flow → consumatori

Reference **condivisa** fra il produttore (`dm:flows`) e i consumatori del flow
(`dm:prototype` oggi; `dm:blueprint` — sezione A5/A6 — domani). Definisce **una sola
cosa**: quali sezioni e campi un `*-flow-*.md` deve esporre perché un consumatore lo
materializzi *senza inventare*. È il perno che disaccoppia produttore e consumatore:
se il flow cambia forma, si bumpa **qui**, e il drift diventa visibile via `schema_version`
invece che silenzioso.

> **Regola d'oro:** `dm:flows` **non inventa** la forma del flow — emette un flow conforme
> a questo schema. I consumatori **non indovinano** i campi mancanti — se una sezione
> richiesta manca, è una non-conformità del flow, non un buco da riempire a fantasia.

**Versione: 1.0.0**

L'istanza concreta (lo scheletro da compilare) è
[`dm-flows/references/flow-template.md`](../../dm-flows/references/flow-template.md);
questo file è lo **schema** a cui quel template aderisce.

---

## Frontmatter

Canonico `02-principio-handoff@1.1.0` (`title`, `type: flow`, `role: single|overview`,
`produced_by`, `upstream`, `id`, `weak_sections`, …). Vedi il template per i campi pieni.

## Sezioni del flow (`role: single`)

| Sezione | Stato | Campi / regole load-bearing |
|---------|-------|------------------------------|
| **Overview** | richiesta | descrizione · attori · trigger · precondizioni |
| **Happy Path** | richiesta | tabella `id · # · Step · Attore · Schermata · Azione · System Response · Outcome →`. `id` = anchor `s.<slug>`. `Schermata` = uno `sc.<slug>` dello Screen Inventory. `System Response` = cosa mostra il sistema dopo l'azione (no invenzione). `Outcome →` = transizione (Screen ID di destinazione o esito terminale). |
| **Screen Inventory** | **richiesta** | una riga per schermata distinta. `Screen ID` = anchor `sc.<slug>`. `Elementi UI / CTA (con stato)` = campi + ogni CTA con *condizione di enable* e *comportamento in-flight*. `Stati da materializzare` = **enumerazione** di TUTTI gli stati applicabili (default/popolato · vuoto · loading · errore · success), off-path inclusi — non una scelta singola. Ogni schermata con stato `errore` lega trigger+recovery (riferimento a un ramo `{#b.<slug>}`). È il **contratto materiale** verso `dm:prototype`. |
| **Diagramma** | richiesta | `graph TD` Mermaid dell'happy path (per l'umano; i consumatori usano Happy Path + Screen Inventory). |
| **Percorsi Alternativi** | opzionale | ramo: da quale step, condizione, step, ricongiunzione. Titolo con anchor `{#b.<slug>}`. |
| **Scenari di Errore** | opzionale* | a quale step, trigger, cosa vede l'utente, recovery. Titolo `{#b.<slug>}`. *Richiesto se una schermata dichiara lo stato `errore`.* |
| **Success Criteria** | opzionale | criteri/metriche misurabili; lo step terminale dichiara l'esito di chiusura. |
| **Cognitive Analysis** | richiesta | review automatica (Hick / Miller 7±2 / context switching); red-flag con anchor `{#rf.<slug>}`. Non consumata dal materializer (è analisi). |

## Anchor (congelati alla nascita, `02-principio-handoff`)

`s.<slug>` step · `sc.<slug>` schermata dello Screen Inventory · `b.<slug>` ramo
alternativo/errore · `rf.<slug>` red-flag cognitiva · `finding.<slug>` finding overview.

## Cosa garantisce ai consumatori

- **`dm:prototype`**: una schermata per riga di Screen Inventory (no duplicazioni), tutti
  gli stati dichiarati renderizzabili, CTA con stato e System Response cablabili senza
  inventare.
- **`dm:blueprint`**: lo Screen Inventory per-flow è il **feeder granulare** dell'A5
  (aggregazione cross-flow); l'Happy Path ordinato alimenta l'A6 (sequenza guidata).

---

## Governance & versioning

- **MAJOR** (1.x → 2.0): rimozione/rinomina di una sezione o colonna richiesta (rompe i
  consumatori) → bump del `schema_version` atteso da `dm:flows` e dai consumatori.
- **MINOR** (1.0 → 1.1): nuova sezione/colonna *opzionale* o nuovo anchor; retro-compatibile.
- **PATCH**: chiarimenti di wording senza effetto sui campi.

Quando questo schema cambia, aggiorna in pari passo: `flow-template.md` (istanza),
`dm:flows` (output) e i `consumes_contracts` dei consumatori. Il mismatch di
`schema_version` è il **segnale di drift** — è esattamente ciò che impedisce al problema
"il template è cambiato e il consumatore non se n'è accorto" di ripresentarsi.

**Versione corrente: 1.0.0** (2026-06-14).
