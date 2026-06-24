# Concept — Template di output

Scheletro canonico dei file prodotti da `dm:concept`. La skill **non** ridichiara la struttura nel
corpo: la legge da qui. Mantiene il contratto handoff (`02-principio-handoff@1.1.0`) — frontmatter con
`upstream`/`weak_sections`, `role` (`concept`|`selection`), `id` e anchor di elemento congelati alla
nascita.

**Legenda placeholder:** `[…]` = valore dedotto/da compilare · `a.<slug>` = anchor di assunzione
critica · `crit.<slug>` = anchor di riga della matrice. Anchor content-derived (lowercase, trattini,
congelato). In rigenerazione ri-aggancia gli anchor esistenti per similarità di nome (mai rinumerare).

---

## 1 · Concept (role: concept)

File: `docs/design/concepts/YYYY-MM-DD-concept-[nome-slug]-01.md`

```markdown
---
title: "Concept: [Nome]"
date: [YYYY-MM-DD]
type: concept
role: concept
status: draft
process: dm-concept
produced_by: dm:concept@[version]
produced_at: [ISO-8601]
principle_versions: handoff@1.1.0
upstream: [ideation-cards, brief, personas, journeys consumati o "input diretto"]
id: concept-[nome-slug]
project: [project-name]
combines_ideas: [idea.<slug>, idea.<slug>, …]   # le idea cards di dm:ideation da cui nasce
weak_sections: [assunzioni non testabili, journey non agganciate, o "nessuno"]
---

# Concept: [Nome]

**Tagline:** Con [prodotto] posso [outcome] senza [attrito].

## Core interaction model

[Come l'utente si muove attraverso il prodotto ad alto livello — il pattern generale, NON
step-by-step. Es. "tutto parte da un'unica timeline dove le azioni si fanno in contesto, senza
sezioni separate".]

## Assunzioni critiche

Cosa deve essere vero perché il concept funzioni. L'assunzione più rischiosa porta `{#a.<slug>}` ed è
marcata come **[più rischiosa]**; deve essere falsificabile.

- **[Utente]** [assunzione] {#a.[slug]}
- **[Mercato]** [assunzione]
- **[Tecnica]** [assunzione]

## Trade-off dichiarati

| Dimensione | Dichiarazione |
|------------|---------------|
| Fa molto bene | [cosa] |
| Sacrifica deliberatamente | [cosa] |
| Funziona per | [chi] |
| Non funziona per | [chi] |

> Qui i trade-off si *descrivono*, non si pesano. Lo scoring è in `concept-selection.md`.

## Sketch testuale

[Narrazione di un uso tipico: "Maria apre il prodotto, vede X, fa Y, ottiene Z." Una storia breve,
concreta, comprensibile senza background tecnico.]

## Rischi di implementazione

[Complessità tecnica, dipendenze, vincoli legali/etici.]

## Gate dei due criteri inviolabili

- **Mapping su journey:** [journey agganciata] — miglioramento misurabile: [peak/end · metrica]
- **Assunzione più rischiosa testabile:** {#a.[slug]} — come si testa: [esperimento/intervista/analytics]
```

---

## 2 · Concept Selection (role: selection)

File: `docs/design/concepts/YYYY-MM-DD-concept-selection-01.md`

```markdown
---
title: "Concept Selection — [project]"
date: [YYYY-MM-DD]
type: concept
role: selection
status: complete
process: dm-concept
produced_by: dm:concept@[version]
produced_at: [ISO-8601]
principle_versions: handoff@1.1.0
upstream: [i concept valutati]
id: concept-selection
selected: concept-[nome-slug]
---

# Concept Selection — [project]

## Matrice di valutazione

Score per cella (es. 1-5) con razionale. Questo è l'unico punto dove i concept si valutano.

| crit | Criterio | Peso | [Concept A] | [Concept B] | [Concept C] |
|------|----------|------|-------------|-------------|-------------|
| crit.[slug] | [criterio] | [peso] | [score] — [razionale] | … | … |

## Concept selezionato

**[Nome]** — [motivazione esplicita ancorata alla matrice: perché vince sui criteri pesati].

## Elementi degli altri concept da salvare

Idee buone che non muoiono con il concept scartato (candidate a feature future / backlog).

- Da **[Concept scartato]**: [elemento] — perché vale la pena conservarlo

## Decisioni aperte

Aspetti da definire **nei flow** (`dm:flows`), non qui — il concept resta ad alto livello.

- [decisione rimandata a dm:flows]
```

---

## Regole di compilazione

- **Altitudine:** concept ad alto livello (approccio + trade-off), niente step (→ flow), niente
  requisiti/metriche (→ PRD).
- **Trade-off descritti nel concept, pesati nella selection:** non anticipare lo scoring nei singoli concept.
- **`combines_ideas`** traccia le idea cards di `dm:ideation` da cui il concept nasce (provenienza).
- **Gate dei due criteri** sempre compilato per ogni concept; i fail vanno in `weak_sections`.
- **Comprensibile senza background tecnico:** è l'artefatto da mostrare agli stakeholder.
