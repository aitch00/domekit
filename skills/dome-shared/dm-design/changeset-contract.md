# Changeset Contract — il contratto edit-prototipo → flow

Reference **condivisa** fra il produttore (`dm:revise`) e il consumatore (`dm:flows`,
ramo **ingest**). Definisce **una sola cosa**: la forma di un `*-changeset-*.md` perché
`dm:flows` possa assorbirne i delta in un `*-flow-*.md` esistente **anchor-preserving e senza
inventare**. È il perno che disaccoppia chi cattura gli edit (la sessione guidata sul
prototipo) da chi li deposita nel contratto (il proprietario del flow): se la forma del
changeset cambia, si bumpa **qui**, e il drift diventa visibile via `schema_version` invece
che silenzioso.

> **Regola d'oro:** il changeset porta **solo delta di livello-contratto** già filtrati. La
> materializzazione (layout, spacing, posizione) **non entra**: vive ed muore nel prototipo.
> `dm:flows` ingest **non reinterpreta** l'intento — applica i delta dichiarati agli anchor
> dichiarati; se un anchor non esiste (e non è `NEW`), è una non-conformità del changeset,
> non un buco da risolvere a fantasia.

**Versione: 1.0.0**

Il produttore di riferimento è [`dm-revise`](../../dm-revise/SKILL.md); il consumatore è il
ramo **ingest** di [`dm-flows`](../../dm-flows/SKILL.md).

---

## Frontmatter

Canonico `02-principio-handoff@1.1.0`, con i campi load-bearing per l'ingest:

| Campo | Stato | Regola |
|-------|-------|--------|
| `title` | richiesto | `Changeset — <feature>` |
| `type` | richiesto | `changeset` |
| `produced_by` | richiesto | `dm:revise@<versione>` |
| `produced_at` | richiesto | ISO-8601 |
| `principle_versions` | richiesto | almeno `handoff@1.1.0` |
| `target_flow` | **richiesto** | path del `*-flow-*.md` da aggiornare. È il flow che ha generato il prototipo editato: l'ingest applica i delta **a questo file**, non ne sceglie un altro. |
| `upstream` | richiesto | `[<prototype meta>, <target_flow>]` — provenienza: il prototipo editato e il flow bersaglio. |
| `consumed_by` | richiesto | `dm:flows` |
| `status` | richiesto | `complete` quando esportato su "soddisfatto". |
| `source` | **richiesto** | il produttore-tipo: `edit` (da `dm:revise`, edit deliberati sul prototipo) · `validation` (da `dm:validate`, revisione proposta da osservazione utente). Calibra lo scetticismo del gate a valle. |
| `evidence` | opz. (richiesto se `source: validation`) | link al finding del `validation-report` che motiva il delta. |
| `confidence` | opzionale | per `source: validation`: quanto la sessione conferma il delta (`alta`/`media`/`bassa`). Assente per `source: edit` (un edit deliberato non è un'ipotesi). |

## Corpo — la tabella dei delta

Una sezione `## Delta` con una riga per cambiamento di contratto:

| Colonna | Regola load-bearing |
|---------|---------------------|
| `#` | progressivo, ordine di cattura. |
| `anchor` | bersaglio del delta: `sc.<slug>` (schermata dello Screen Inventory) · `s.<slug>` (step dell'Happy Path) · `NEW` (l'ingest conia un anchor nuovo, mai riusato). Deve risolvere a un anchor **esistente** nel `target_flow`, tranne `NEW`. |
| `tipo` | uno di: `add-element` · `add-state` · `change-behavior` · `change-outcome` · `add-screen` · `remove` (vedi tabella sotto). |
| `richiesta (intento)` | la frase dell'utente che ha originato l'edit (tracciabilità: *perché*). |
| `dettaglio` | il cambiamento concreto sul flow (*cosa*): campo/stato/outcome preciso, in termini di Screen Inventory / Happy Path. Niente layout. |

### Tipi di delta ammessi e dove atterrano nel flow

| `tipo` | Significato | Dove atterra nel `*-flow-*.md` |
|--------|-------------|------------------------------|
| `add-element` | nuovo campo/CTA su una schermata | riga `sc.<slug>` → colonna *Elementi UI / CTA (con stato)* |
| `add-state` | nuovo stato da materializzare | riga `sc.<slug>` → colonna *Stati da materializzare* (enumerazione) |
| `change-behavior` | cambia condizione di enable / comportamento in-flight di una CTA | riga `sc.<slug>` o step `s.<slug>` (System Response) |
| `change-outcome` | cambia la transizione di uno step | step `s.<slug>` → colonna *Outcome →* |
| `add-screen` | nuova schermata | nuova riga Screen Inventory con anchor `sc.<slug>` coniato (`NEW`) + eventuale step nell'Happy Path |
| `remove` | ritira un elemento/stato/step/schermata | l'anchor è **ritirato**, mai riusato (frozen-at-birth) |

> **Escluso per contratto:** ogni delta di materializzazione (sposta/ridimensiona/ricolora/
> impagina). Il filtro è a monte, nel produttore (`dm:revise`, test ADR decisione-vs-ritocco):
> il changeset che arriva all'ingest è **già** solo-contratto.

## Produttori (un ponte, due mittenti, un ricevitore)

Il changeset è la forma condivisa delle **revisioni-di-flow**: un ricevitore (`dm:flows` ingest,
producer-agnostic) e — per disegno — **due produttori**:

| Produttore | `source` | Origine del delta | Stato |
|------------|----------|-------------------|-------|
| **`dm:revise`** | `edit` | edit deliberati sul prototipo, classificati contratto-vs-ritocco | **attivo** |
| **`dm:validate`** | `validation` | "Revisioni proposte a monte" che colpiscono un **flow**, da sessioni utente, con `evidence` + `confidence` | **pianificato** — il suo ingest emetterà un changeset (oggi le propone come testo libero applicato a mano) |

> Convergenza **definita ora, implementata quando si tocca l'ingest di `dm:validate`** (la spec
> evita lo schema prematuro: si fattorizza quando il secondo produttore arriva, non prima). I
> *journey* restano fuori: non hanno mappa anchor né ricevitore ingest — sarebbe un binario a sé.

## Cosa garantisce al consumatore (`dm:flows` ingest)

- Ogni delta è **localizzato** (anchor esplicito) → l'ingest applica **chirurgicamente** alla
  riga/step giusto, riusando la rigenerazione **anchor-preserving** già in `dm:flows` (mai
  rinumerare, ri-aggancio per similarità di titolo).
- `add-screen`/`NEW` → l'ingest conia un `sc.<slug>` nuovo content-derived, congelato.
- `remove` → ritiro dell'anchor, mai riassegnato.
- Nessun delta richiede di **inventare** forma: ogni `tipo` ha una destinazione deterministica
  nello schema del flow (`flow-handoff-contract@1.0.0`).

---

## Governance & versioning

- **MAJOR** (1.x → 2.0): rimozione/rinomina di una colonna richiesta o di un `tipo` di delta
  (rompe l'ingest) → bump del `schema_version` atteso da `dm:revise` e `dm:flows`.
- **MINOR** (1.0 → 1.1): nuovo `tipo` di delta o nuovo campo *opzionale*; retro-compatibile.
- **PATCH**: chiarimenti di wording senza effetto sui campi.

Quando questo schema cambia, aggiorna in pari passo: `dm:revise` (output) e i
`consumes_contracts` di `dm:flows`. Il mismatch di `schema_version` è il **segnale di drift**.

**Versione corrente: 1.1.0** (2026-06-16) — additivo: campi provenienza (`source`/`evidence`/
`confidence`) + sezione *Produttori* (`dm:revise` attivo, `dm:validate` pianificato).
Retro-compatibile con 1.0.0 (i campi sono opzionali eccetto `source`, che il solo produttore
attivo popola a `edit`).
