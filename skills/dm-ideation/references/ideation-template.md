# Ideation Cards — Template di output

Scheletro canonico del file prodotto da `dm:ideation`. La skill **non** ridichiara la struttura nel
corpo: la legge da qui. Mantiene il contratto handoff (`02-principio-handoff@1.1.0`) — frontmatter
con `upstream`/`weak_sections`, `id` e anchor di elemento congelati alla nascita.

**Legenda placeholder:** `[…]` = valore dedotto/da compilare · `hmw.<slug>` = anchor dell'HMW di
un'area · `idea.<slug>` = anchor di idea card. Anchor content-derived (lowercase, trattini,
congelato). In rigenerazione ri-aggancia gli anchor esistenti per similarità di nome invece di
rislugare (mai rinumerare).

---

## Ideation Cards (un file per progetto)

File: `docs/design/ideation/YYYY-MM-DD-ideation-cards-01.md`

```markdown
---
title: "Ideation Cards — [project]"
date: [YYYY-MM-DD]
type: ideation-cards
role: single
status: draft
process: dm-ideation
produced_by: dm:ideation@[version]
produced_at: [ISO-8601]
principle_versions: handoff@1.1.0
upstream: [lista path fonti consumate — journeys, personas, brief — o "input diretto"]
id: ideation-cards
project: [project-name]
weak_sections: [aree dedotte a bassa confidenza, HMW falliti, o "nessuno"]
---

# Ideation Cards — [project]

> Divergenza pura: queste idee NON sono valutate né ordinate. La convergenza
> (pro/contro, criteri, selezione) è di `dm:concept`.

## Aree di opportunità (sintesi)

| # | Area | HMW | # idee | Pattern coperti | Origine |
|---|------|-----|--------|-----------------|---------|
| 1 | [area] | [statement breve] | [N] | [lista pattern] | [journey · fase] |

---

## Area 1 — [Nome area] {#hmw.[slug]}

**HMW:** Come potremmo [aiutare persona X] a [obiettivo] nonostante [ostacolo]?
**Origine:** [journey/fase a emozione −X · pain point]

| id | Nome | Pattern | Descrizione (cosa fa l'utente) | Assunzione chiave | Rischio principale | Analogia |
|----|------|---------|--------------------------------|-------------------|--------------------|----------|
| idea.[slug] | [nome] | [pattern] | [2-3 frasi orientate all'utente] | [cosa deve essere vero] | [rischio] | [dominio d'origine] |

<!-- ripeti il blocco "Area N" per ogni area di opportunità -->

## Note di divergenza

- Aree con HMW fallito (problema non abbastanza chiaro): [lista o "nessuna"]
- Self-check diversità: [aree dove la divergenza è risultata debole, o "tutte ≥3 pattern"]
```

---

## Regole di compilazione

- **Descrizione orientata all'utente:** *cosa fa l'utente*, mai *come funziona il sistema*.
- **Una riga per idea**, una colonna `id` con l'anchor `idea.<slug>` congelato.
- **Nessun campo di valutazione:** niente score, ranking, "consigliata". Assunzione e rischio si
  *descrivono*, non si giudicano (li userà `dm:concept`).
- **HMW falliti** vanno comunque registrati nelle "Note di divergenza" e in `weak_sections`: sono un
  segnale per il brief/le journey, non un buco da nascondere.
