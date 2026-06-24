# Regole di concept — il contratto load-bearing di dm:concept

Reference statica caricata da `dm:concept`. Tre regole che proteggono il concept dal degenerare in
qualcos'altro (un flow, un PRD, una scelta per inerzia). Vivono qui perché devono agire **a runtime**,
in qualunque repo la skill giri.

---

## 1 · Concept ≠ flow ≠ PRD (altitudine)

Un concept è l'**approccio ad alto livello**: cosa fa il prodotto per l'utente, secondo quale logica,
con quale trade-off. Resta sopra il dettaglio.

| Artefatto | Domanda a cui risponde | Altitudine |
|-----------|------------------------|------------|
| **Concept** (dm:concept) | *Quale approccio?* Cosa fa per l'utente, con che logica e trade-off | Alta — narrazione, modello, trade-off |
| Flow (dm:flows) | *Come si svolge?* Step, schermate, system response, outcome | Media — step-by-step |
| PRD (dm:spec) | *Cosa si comporta e si misura?* Requisiti, acceptance, metriche | Bassa — requisiti verificabili |

**Segnale di deriva.** Ogni volta che il concept scende al dettaglio di un singolo step ("l'utente
clicca il bottone in alto a destra, poi…"), è nella zona del flow → rimanda a `dm:flows`. Ogni volta
che enumera requisiti e metriche ("il sistema deve rispondere in < 200ms"), è nella zona del PRD →
rimanda a `dm:spec`. Il **Core interaction model** descrive il *pattern* di movimento, non la sequenza.

**Test pratico:** il concept deve essere comprensibile e utile a uno stakeholder **senza background
tecnico**. È il documento da mostrare *prima* di investire nel dettaglio. Se serve un developer per
capirlo, è sceso troppo.

---

## 2 · Due criteri inviolabili nella selezione

Un concept non passa al flow se non supera entrambi:

1. **Mapping su journey.** Il concept deve mappare su **almeno una journey esistente** con un
   miglioramento **misurabile** del peak o dell'end (la Peak-End Analysis di `dm:journeys`). Un
   concept che non aggancia nessuna journey è scollegato dall'esperienza reale: dichiaralo. *Il
   concept esiste per migliorare un'esperienza vissuta, non in astratto.*

2. **Assunzione più rischiosa testabile.** L'assunzione critica più rischiosa del concept deve essere
   **identificata** ed essere **falsificabile** (testabile con un esperimento, un'intervista,
   analytics). È il principio del pretotyping / assumption mapping: il concept più elegante crolla se
   poggia su un'assunzione falsa mai verificata. Se l'assunzione non è testabile, marcala in
   `weak_sections`.

---

## 3 · Concept multipli come protezione (minimo 2)

Anche se alla fine se ne sceglie uno, **scrivere 2-3 concept alternativi è il valore**, non solo la
selezione. Il rischio che difende: la "prima idea vince per inerzia" — quando l'unica opzione scritta
diventa il prodotto perché non c'è un termine di paragone.

- I concept devono essere **genuinamente alternativi** (divergono per logica/pattern), non varianti
  cosmetiche dello stesso approccio. Due concept che si distinguono solo per il colore del bottone non
  sono due concept.
- **Niente idea muore senza traccia.** Gli elementi buoni dei concept scartati vanno in "Elementi da
  salvare" della selection: candidate a feature future, non perdite.

---

## Dove avviene la valutazione

| Momento | Valutazione? |
|---------|--------------|
| `dm:ideation` (idee) | ❌ Vietata — divergenza pura |
| `dm:concept`, singolo concept | ⚠️ Solo *descrizione* dei trade-off (cosa fa bene / cosa sacrifica) — niente score |
| `dm:concept`, `concept-selection.md` | ✅ Qui — matrice concept × criteri con score e razionale |

La valutazione è concentrata in un solo punto (la selection) e tracciata: così la decisione è
rivedibile, non implicita.
