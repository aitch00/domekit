# Journey — Template di output

Scheletro canonico dei file prodotti da `dm:journeys`. La skill **non** ridichiara
la struttura nel corpo: la legge da qui. Mantiene il contratto handoff
(`02-principio-handoff@1.1.0`) — frontmatter con `upstream`/`weak_sections`, `role`
(`single`|`overview`), `id` e anchor di elemento congelati alla nascita.

**Legenda placeholder:** `[…]` = valore dedotto/da compilare · `p.<slug>` = anchor di
fase · `r.<slug>` = anchor di design-recommendation. Anchor content-derived (lowercase,
trattini, congelato). In rigenerazione ri-aggancia gli anchor esistenti per similarità
di titolo invece di rislugare (mai rinumerare).

**Scala emozioni** (esatta, non semplificare): −2 Frustrato · −1 Incerto · 0 Neutro ·
+1 Soddisfatto · +2 Entusiasta · +3 Deliziato.

---

## 1 · Journey (role: single)

File: `docs/design/journeys/YYYY-MM-DD-journey-[scenario-slug]-[persona-slug]-01.md`

```markdown
---
title: "Journey: [Scenario] — [Persona]"
date: [YYYY-MM-DD]
type: journey
role: single
status: draft
process: dm-journeys
produced_by: dm:journeys@[version]
produced_at: [ISO-8601]
principle_versions: handoff@1.1.0
upstream: [lista path fonti consumate o "input diretto"]
id: journey-[scenario-slug]-[persona-slug]
project: [project-name]
scenario: [scenario]
persona: [nome persona o "inline"]
weak_sections: [fasi/emozioni dedotte a bassa confidenza o "nessuno"]
---

# Journey: [Scenario] — [Persona]

## Contesto

| Campo | Valore |
|-------|--------|
| Obiettivo | [obiettivo utente] |
| Aspettative iniziali | [con quali aspettative/bisogni l'attore entra nello scenario] |
| Trigger | [cosa avvia la journey] |
| Definizione successo | [come l'utente sa di avercela fatta] |
| Persona | [nome / riferimento file] |

## Journey Map

La colonna `id` porta l'anchor stabile della fase (`p.<slug>`), congelato alla nascita.

**Grounding emotivo** (anti-pattern: emozioni da opinione interna). Ogni valore Emozione
non ancorato a evidenza reale (quote, intervista, analytics, review) va marcato in
`weak_sections`: la curva deve essere onesta, non desiderata.

| id | Fase | Azione | Pensiero | Emozione | Touchpoint | Pain Point / Opportunità |
|----|------|--------|---------|---------|-----------|--------------------------|

## Sentiment Timeline

\`\`\`mermaid
xychart-beta
    title "Sentiment Timeline — [Scenario]"
    x-axis ["[Fase 1]", "[Fase 2]", …]
    y-axis "Emozione" -2 --> 3
    line [[valore1], [valore2], …]
\`\`\`

## Peak-End Analysis

**Peak:** [nome fase] — Emozione: [valore]
**End:** [nome ultima fase] — Emozione: [valore]

### Design Recommendations

Ogni raccomandazione porta un anchor `{#r.<slug>}` sul suo titolo (stabile) e un **owner**
interno (chi è responsabile di chiudere l'opportunità — Zona C di NN/g; "—" se ignoto).

1. **[Strategia peak]:** [raccomandazione specifica] *(owner: [ruolo/team o —])* {#r.[slug]}
2. **[Strategia end]:** [raccomandazione specifica] *(owner: [ruolo/team o —])* {#r.[slug]}

## Metriche di Successo

[metriche da tracciare per questa journey]
```

---

## 2 · Journeys Overview (role: overview)

File: `docs/design/journeys/YYYY-MM-DD-journeys-overview-01.md`

```markdown
---
title: "Journeys Overview — [project]"
date: [YYYY-MM-DD]
type: journey
role: overview
status: complete
process: dm-journeys
produced_by: dm:journeys@[version]
produced_at: [ISO-8601]
principle_versions: handoff@1.1.0
upstream: [le journey consumate]
id: journeys-overview
---

# Journeys Overview

## Summary Table

| Scenario | Persona | Fasi | Peak | End Emotion |
|----------|---------|------|------|-------------|

## Cross-Journey Insights

Ogni finding ricorrente porta un anchor `### Titolo {#finding.<slug>}` (stabile).

- Momenti critici ricorrenti
- Pain point e pattern ricorrenti
- Pattern di progressione emotiva
- Opportunità ad alto impatto
- Raccomandazioni design prioritarie

## Journey Index

| Journey | File |
|---------|------|
| [scenario] ([persona]) | `docs/design/journeys/YYYY-MM-DD-journey-[slug]-[slug]-01.md` |
```
