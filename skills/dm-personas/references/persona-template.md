# Persona — Template di output

Scheletro canonico dei file prodotti da `dm:personas`. La skill **non** ridichiara
la struttura nel corpo: la legge da qui. Mantiene il contratto handoff
(`02-principio-handoff@1.1.0`) — frontmatter con `upstream`/`weak_sections`, `role`
(`single`|`overview`), `id` e anchor di elemento congelati alla nascita.

**Legenda placeholder:** `[…]` = valore dedotto/da compilare · `bias.<slug>` = anchor
content-derived (lowercase, trattini, congelato). In rigenerazione ri-aggancia gli
anchor esistenti per similarità di titolo invece di rislugare (mai rinumerare).

**Status:** la persona singola nasce `complete` — è scritta dopo il self-check di coerenza
(Step F della SKILL), che è il suo gate. Non resta `draft` dopo la chiusura del processo. Usa
`draft` solo per un file deliberatamente parcheggiato a metà (gap critici irrisolti dichiarati
in `weak_sections`); in quel caso la SKILL lo segnala esplicitamente nel report.

**Grounding dell'evidenza** (anti-pattern: persona basata su assunzioni). Ogni campo non
ancorato a una fonte reale del corpus va elencato in `weak_sections`. I valori più a
rischio d'invenzione — **Cognitive Profile** e **Key Biases** — se dedotti per
correlazione (occupation table) e non da evidenza, vanno marcati in `weak_sections`.

---

## 1 · Persona (role: single)

File: `docs/dome/personas/persona-[type]-[name-slug].md`

```markdown
---
title: "Persona: [Nome] ([type])"
date: [YYYY-MM-DD]
type: persona
role: single
status: complete
process: dm-personas
produced_by: dm:personas@[version]
produced_at: [ISO-8601]
principle_versions: handoff@1.1.0
upstream: [lista path fonti consumate o "input diretto"]
id: persona-[type]-[name-slug]
project: [project-name]
persona_name: [Nome]
persona_type: [primary|secondary|anti-persona]
weak_sections: [campi dedotti a bassa confidenza o "nessuno"]
---

# Persona: [Nome] ([type])

## Profile

| Campo | Valore |
|-------|--------|
| Età | [età] |
| Ruolo | [ruolo] |
| Contesto d'uso | [contesto] |
| Device / canale | [mobile / desktop / tablet — primario] |
| Frequenza d'uso | [daily / weekly / monthly / rarely] |
| Tech savviness | [0–5] |

**Goals:** [goals — cosa vuole ottenere]
**Motivations:** [motivations — *perché* lo vuole; il movente, distinto dal goal]
**Pain points:** [pain points]
**Behaviors:** [behaviors]
**Quote:** "[quote rappresentativa]"

## Cognitive Profile

| Dimensione | Score | Label |
|-----------|-------|-------|
| Decision Style | [0–10] | [label] |
| Cognitive Capacity | [0–10] | [label] |
| Attention | [0–10] | [label] |
| Processing Mode | [visual/verbal/kinesthetic] | — |

## Key Biases

La colonna `id` porta l'anchor stabile del bias (`bias.<slug>`), congelato alla nascita.

| id | Bias | Severity | Manifestazione |
|----|------|---------|----------------|
| bias.[slug] | [bias 1] | [low/medium/high] | [come si manifesta] |
| bias.[slug] | [bias 2] | [low/medium/high] | [come si manifesta] |
| bias.[slug] | [bias 3] | [low/medium/high] | [come si manifesta] |

## Radar Chart Data

I 6 assi sono **derivati** (non indipendenti), così la rigenerazione è deterministica:

| Dimension | Value | Derivazione |
|-----------|-------|-------------|
| working_memory | [0–10] | = cognitive_capacity |
| attention | [0–10] | = attention |
| processing_speed | [0–10] | = cognitive_capacity |
| analytical | [0–10] | = decision_style |
| bias_resistance | [0–10] | = 10 − media(severity bias) |
| flexibility | [0–10] | kinesthetic→8 · visual→7 · verbal→5 |

Mappatura severity→numero per `bias_resistance`: `low=2 · medium=5 · high=8`.
```

---

## 2 · Personas Overview (role: overview)

File: `docs/dome/personas/personas-overview.md`

```markdown
---
title: "Personas Overview — [project]"
date: [YYYY-MM-DD]
type: persona
role: overview
status: complete
process: dm-personas
produced_by: dm:personas@[version]
produced_at: [ISO-8601]
principle_versions: handoff@1.1.0
upstream: [le personas consumate]
id: personas-overview
---

# Personas Overview

## Summary Table

| Persona | Tipo | Età/Ruolo | Decision Style | Cognitive Capacity | Attention | Processing |
|---------|------|-----------|---------------|-------------------|-----------|------------|

## Quick Reference

### Primary
### Secondary
### Anti-Persona

## Cross-Persona Insights

Ogni finding ricorrente porta un anchor `### Titolo {#finding.<slug>}` (stabile).

- Goals comuni
- Pain point ricorrenti
- Diversità cognitive (range di decision style, attention)
- Implicazioni design prioritarie
```
