---
name: dm:domain-modeler
description: "Modella il dominio applicativo producendo un entity-relationship model con Mermaid erDiagram. Deduce le entità dai contenuti condivisi (brief, brainstorm, flows, glossario) e le fa verificare a gate. Usa pre-sviluppo per allineare design e sviluppo sul vocabolario condiviso."
argument-hint: "[--yolo] [--autonomous / mode:headless]"
---

# dm:domain-modeler

Produce `docs/dome/domain-model/domain-model.md` con `erDiagram` Mermaid validato.

La modalità di default **non chiede le entità da zero**: assorbe il contenuto condiviso, **deduce le entità**, e le fa **verificare** attraverso 4 gate.

---

## On Activation

### 1. Resume Check

Scansiona `docs/dome/domain-model/domain-model.md`.

Se esiste:

```
AskUserQuestion (header: "Domain Model"):
- Aggiorna il domain model esistente (carica le entità attuali come punto di partenza)
- Ricomincia da capo (ignora il file esistente)
```

Se non esiste: procedi.

### 2. Mode Detection

| Flag | Modalità | Comportamento |
|------|----------|---------------|
| (nessuno) | **Guided** | Deduci le entità → conferma ai 4 gate → preview |
| `--yolo` | **Yolo** | Deduci tutto, gate auto-confermati, mostra solo la preview finale |
| `--autonomous` / `mode:headless` | **Headless** | Deduci in silenzio, self-check interno, scrivi senza gate |

### 3. Discovery — Absorb & Deduce

**Grounding del corpus (`dm:research-corpus`).** Prima di scansionare a mano,
dispatcha `dm:research-corpus` (intent corpus, `--no-persist`): inventario, stato
e contraddizioni del corpus. Decisione a 3 stadi (esplicito → default grounding →
gap-driven su entità citate ma non definite); se l'atomo non è disponibile,
**degrada** alla scansione diretta. I finding entrano solo dove plasmano la
deduzione (load-bearing). Nota: il `domain-glossary.md` ora è alimentato anche
dal vocabulary capture di brief/brainstorm (C1) — il digest lo segnala se presente.

**Scansiona il contenuto condiviso** per dedurre le entità del dominio (via digest o diretta in fallback):
- `discovery/domain-glossary.md` — glossario esplicito, se presente
- `docs/dome/product-brief/*.md` — entità di business e vocabolario
- `docs/dome/brainstorming/*.md` — concetti emersi
- `docs/dome/flows/flow-*.md` — oggetti manipolati negli step
- `docs/dome/personas/persona-*.md` — entità legate agli attori
- `PRD*.md`, `*brief*.md` — fonti legacy

**Estrai le entità candidate** da tutte le fonti (nomi, attributi impliciti, relazioni suggerite). **Dichiara i gap**: entità o relazioni non deducibili.

**Se non c'è alcun contenuto** (fallback, non default): chiedi *"Quali sono le entità principali del dominio?"* (lista o descrizione libera). Non aprire mai con una richiesta a freddo se le fonti esistono.

---

## Gate sequenziali (guided)

In yolo i gate sono auto-confermati; in headless saltati (self-check interno).

### Gate 1 — Entità

Presenta le entità dedotte con una breve descrizione e la fonte da cui provengono:

```
Entità dedotte (fonte):
1. [EntityName] — [descrizione]  (da: brief / flow / glossario)
2. [EntityName] — [descrizione]
…

Confermi questa lista? Aggiungi, rimuovi o modifica prima di procedere.
```

Attendi conferma. Non proseguire finché l'utente non approva.

### Gate 2 — Attributi

Per ogni entità confermata, proponi un blocco attributi completo (dedotto, non chiesto a freddo):

```
Attributi per [EntityName]:
  int id PK
  string [nome]
  …

Confermi questi attributi? Aggiungi, rimuovi o modifica prima di procedere.
```

Una entità alla volta. Usa solo tipi da `references/mermaid-validation.md`: `string`, `int`, `boolean`, `float`, `date`, `datetime`.

### Gate 3 — Relazioni

Proponi le relazioni dedotte tra le entità confermate:

```
Relazioni dedotte:
- [EntityA] ||--o{ [EntityB] : "[label]"  →  [EntityA] può avere zero o più [EntityB]
…

Confermi queste relazioni? Modifica cardinalità o label prima di procedere.
```

Semantica esatta di `references/mermaid-validation.md`: `||` = esattamente uno · `}o` = zero o più · `}|` = uno o più · `|o` = zero o uno.

### Self-Check Mermaid (Review interna)

Prima di generare il diagramma, esegui internamente le 5 domande di `references/mermaid-validation.md`:
1. Tutti i nomi entità sono PascalCase o quotati?
2. Tutte le relazioni hanno label in virgolette?
3. Tutti i tipi attributo sono dalla lista valida?
4. La cardinalità usa solo `||`, `}o`, `}|`, `|o`?
5. Ci sono `{{variables}}` che dovrebbero essere `[placeholders]`?

Se una risposta è "no", correggi. Il self-check è sempre silenzioso.

### Genera erDiagram

Genera il blocco `erDiagram` completo con entità, attributi e relazioni confermate.

### Gate 4 — Preview

Mostra il diagramma Mermaid completo:

```
Preview erDiagram:

\`\`\`mermaid
erDiagram
    …
\`\`\`

Approvi? Per modificare: Entità → Gate 1 · Attributi → Gate 2 · Relazioni → Gate 3
```

Se approva: scrivi. Se richiede modifica: torna al gate indicato **conservando lo stato confermato degli altri gate**.

---

## Scrittura Output

Crea `docs/dome/domain-model/` se non esiste.

Scrivi `docs/dome/domain-model/domain-model.md` seguendo `references/domain-model-template.md`:
- Frontmatter handoff (sotto)
- Entity Catalog (tabella)
- Relationships (tabella in linguaggio naturale)
- Business Glossary (solo se ci sono termini ambigui)
- ER Diagram (erDiagram embedded)

Frontmatter:

```yaml
---
title: "Domain Model — [project]"
date: [YYYY-MM-DD]
type: domain-model
role: single
status: draft
process: dm-domain-modeler
produced_by: dm:domain-modeler@[version]
produced_at: [ISO-8601]
principle_versions: handoff@1.1.0
upstream: [lista path fonti consumate o "input diretto"]
id: domain-model
project: [project-name]
weak_sections: [entità/relazioni dedotte a bassa confidenza o "nessuno"]
---
```

---

## Report

```
dm:domain-modeler completato.

Output:
  docs/dome/domain-model/domain-model.md

Entità: [N] · Relazioni: [N]

Next steps:
  Il domain model chiude il ramo strutturale della ricerca di design.
  Se non l'hai ancora fatto, completa il ramo comportamentale:
  /dm-personas   — definisci i profili utente
  /dm-journeys   — mappa l'esperienza vissuta
  /dm-flows      — mappa come gli utenti usano il prodotto
  Consolidamento di tutti gli artefatti: /dm-synthesis (in arrivo)
```

---

## Regole

- **Mai chiedere le entità a freddo se le fonti esistono:** in guided deduci dal contenuto condiviso, poi fai verificare ai gate
- Non generare il diagramma prima del Gate 4 — presentalo solo lì
- Se l'utente torna a un gate precedente, conserva le conferme dei gate non modificati
- Il self-check Mermaid è sempre silenzioso (non mostrare le 5 domande)
- Usa solo `[placeholder]` — mai `{{VARIABLE}}` nei file di output
- Modalità (guided/yolo/headless) scelta una volta, propagata
- **Anchor degli elementi consumabili** (convenzione `02-principio-handoff`): entità → `e.<nome>`
  (slug del nome, già stabile nell'erDiagram); relazione → `rel.<A>-<B>`; termine del glossario →
  colonna `id` (`g.<slug>`). Anchor content-derived, **congelato alla nascita**.
- **Rigenerazione anchor-preserving:** rigenerando il domain-model, ri-aggancia gli anchor esistenti
  per similarità di nome/titolo invece di rislugare (mai rinumerare).
