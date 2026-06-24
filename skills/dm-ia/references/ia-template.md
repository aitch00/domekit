# IA Map — Template di output

Scheletro canonico del file prodotto da `dm:ia`. La skill **non** ridichiara la struttura nel corpo:
la legge da qui. Mantiene il contratto handoff (`02-principio-handoff@1.1.0`) — frontmatter con
`upstream`/`weak_sections`, `id` e anchor di elemento congelati alla nascita.

**Legenda placeholder:** `[…]` = valore dedotto/da compilare · `nav.<slug>` = oggetto navigabile ·
`ct.<slug>` = content type · `st.<slug>` = stato globale · `fg.<slug>` = findability gap. Anchor
content-derived (lowercase, trattini, congelato). In rigenerazione ri-aggancia gli anchor esistenti per
similarità di nome (mai rinumerare).

---

## IA Map (un file per progetto)

File: `docs/design/ia/YYYY-MM-DD-ia-map-01.md`

```markdown
---
title: "IA Map — [project]"
date: [YYYY-MM-DD]
type: ia-map
role: single
status: draft
process: dm-ia
produced_by: dm:ia@[version]
produced_at: [ISO-8601]
principle_versions: handoff@1.1.0
upstream: [flow consumati, domain-model, personas, brief o "input diretto"]
id: ia-map
project: [project-name]
platform: [mobile | desktop | responsive]
weak_sections: [oggetti/labeling a bassa confidenza, gap di prodotto, o "nessuno"]
---

# IA Map — [project]

## 1 · Inventario degli oggetti navigabili

Accesso: `top-level` · `drill-down` · `modal` · `contextual` · `deep-link`.

| id | Nome | Tipo | Accesso | Entità (domain-model) | Descrizione |
|----|------|------|---------|-----------------------|-------------|
| nav.[slug] | [nome] | View/Modal/… | [accesso] | [entità o "—"] | [descrizione] |

> **Completezza domain-model:** entità navigabili senza entry qui = gap di prodotto: [lista o "nessuno"].

## 2 · Navigation model

| Livello | Voce | Etichetta | Icona (mobile) | Comportamento | Stato attivo |
|---------|------|-----------|----------------|---------------|--------------|
| Primary | nav.[slug] | [label] | [icona] | [tap/click] | [come segnala "sei qui"] |
| Secondary | … | … | … | … | … |
| Contextual | … | … | … | … | … |
| Utility | … | … | … | … | … |

> **Divergenza per breakpoint** (se responsive): [es. tab bar mobile → sidebar desktop].

## 3 · Content types

| id | Content type | Campi | Template di visualizzazione | Azioni disponibili |
|----|--------------|-------|-----------------------------|--------------------|
| ct.[slug] | [tipo] | [campi] | [Card + Detail / Timeline / …] | [Edit, Export, Delete, …] |

## 4 · Labeling system

| Termine usato | Alternative scartate | Razionale |
|---------------|----------------------|-----------|
| [termine] | [scartati] | [perché — mappa sul mental model dell'utente] |

- Regole plurale/singolare, maiuscole, abbreviazioni (mobile/breadcrumb): [regole]
- Termini da evitare (jargon, false friend competitor): [lista]

## 5 · Routing e URL structure

- Pattern: `/[entity-type]/[id]/[view]`

| URL / deep link | View mostrata | Shareable |
|-----------------|---------------|-----------|
| [url] | nav.[slug] | [sì / no — stato privato] |

## 6 · Gestione degli stati globali

| id | Stato | Comportamento | View mostrata |
|----|-------|---------------|---------------|
| st.[slug] | Utente nuovo / no dati | Prima visita, nessun contenuto | Empty state + onboarding |
| st.[slug] | Utente con dati | Stato normale | View standard |
| st.[slug] | Sessione scaduta | Token non valido | Redirect login con messaggio |
| st.[slug] | Errore di connessione | Rete non disponibile | Offline state |

> Ancorati agli stati già materializzati negli Screen Inventory dei flow — non scollegati.

## 7 · Findability gaps (ipotesi da validare)

> **Limite predittivo:** la struttura è razionale ma non predice il mental model reale degli utenti.
> Validare con tree testing / card sorting (~5 utenti, es. Optimal Workshop).

- **[Item] {#fg.[slug]}** — ipotesi: [potrebbe essere cercato sotto X invece che Y]. Test: [tree testing su…].

## Review IA (euristiche di Morville)

Note di review non bloccanti contro findable/accessible/clear/communicative/usable/credible/controllable:
- [finding]

## Feedback upstream ai flow (se emersi)

- ⚠ [incongruenza IA ↔ flow] → aggiornare `docs/design/flows/[…]` prima di prototipare.
```

---

## Regole di compilazione

- **Una colonna `id`** con l'anchor congelato su oggetti navigabili (`nav.`), content type (`ct.`),
  stati globali (`st.`); findability gap con `{#fg.<slug>}`.
- **Completezza domain-model** sempre verificata nella sezione 1.
- **Findability gaps come ipotesi**, mai verdetti; sempre con il limite predittivo dichiarato.
- **Labeling sempre col razionale**; **stati globali ancorati** agli Screen Inventory dei flow.
- **Feedback ai flow** in coda quando emergono incongruenze — la ia-map non riscrive i flow.
