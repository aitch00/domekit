# Euristiche e regole IA — il contratto di review di dm:ia

Reference statica caricata da `dm:ia`. Tre cose: i criteri di review (le 7 sfaccettature di Morville),
la tabella necessità/opzionalità, e la disciplina del limite predittivo. Vive qui perché deve agire
**a runtime**, in qualunque repo la skill giri.

---

## 1 · Le 7 sfaccettature di Morville (criteri di review)

Peter Morville — i 7 facets dell'esperienza informativa. Usali come check di review della struttura
prodotta, non come sezioni di output.

| Facet | Domanda di review |
|-------|-------------------|
| **Findable** | L'utente trova ciò che cerca? Ogni oggetto chiave è raggiungibile in ≤ 2-3 passi da un punto top-level? |
| **Accessible** | La struttura funziona con tastiera/screen reader/contesti degradati? I deep link funzionano? |
| **Clear / Usable** | Le etichette sono chiare al primo colpo? La navigazione è prevedibile e coerente tra sezioni? |
| **Communicative** | La struttura comunica il modello del prodotto? L'utente capisce "dove si trova" e "cosa c'è"? |
| **Credible** | Il labeling evita jargon interno e false friend con i competitor? La struttura ispira fiducia? |
| **Controllable** | L'utente può tornare indietro, annullare, raggiungere ciò che vuole quando vuole (pull) senza vicoli ciechi? |
| **Valuable** | La struttura serve sia l'utente sia gli obiettivi del prodotto? |

Produci i finding di review come note **non bloccanti** annotate nella ia-map.

---

## 2 · Necessità dell'IA (gate soft di attivazione)

L'IA non è obbligatoria: scala con la complessità del prodotto.

| Condizione | Necessità | Perché |
|------------|-----------|--------|
| 1-2 flow, utente singolo, nessuna navigazione top-level | **Bassa** | La IA è implicita e poco rischiosa; il prototipo la assorbe senza incoerenze |
| 3+ flow, navigazione primaria, utenti di tipo diverso | **Alta** | Le decisioni di navigazione prese in ogni flow in modo indipendente creano incoerenze strutturali difficili da correggere dopo |
| Contenuto user-generated (liste, documenti, item) | **Alta** | La findability del contenuto creato dall'utente è critica e non emerge dai singoli flow |
| Ruoli diversi (admin vs user) | **Alta** | La struttura di navigazione diverge per ruolo: va modellata, non improvvisata per schermata |

Sotto soglia: dillo e offri di procedere come baseline, ma non bloccare. È calibrazione al contesto.

---

## 3 · Il limite predittivo (disciplina della sezione Findability gaps)

**La skill non può predire come gli utenti reali categorizzano mentalmente le cose.** Può costruire una
struttura razionale e internamente coerente; non può sostituire l'osservazione.

Conseguenze operative:
- La sezione **Findability gaps** elenca gli item che un test IA tipicamente mette in discussione
  **come ipotesi da validare, non come problemi accertati**. Mai scrivere "gli utenti non troveranno
  X" — scrivere "ipotesi: X potrebbe essere cercato sotto Y invece che Z; da validare".
- Dichiara sempre il limite e proponi un **test reale**: tree testing o card sorting con ~5 utenti
  (es. Optimal Workshop o simili). Il card sorting *aperto* scopre i raggruppamenti; il tree testing
  verifica la findability della struttura proposta.
- L'equivalente "simulabile" (card sorting con l'utente della skill) è utile per la *discovery* dei
  content type e del labeling, ma resta un proxy: non chiude il gap, lo riduce.

---

## Mappatura alle pratiche pre-AI

| Pratica | Equivalente nella skill |
|---------|-------------------------|
| Card sorting aperto | Discovery dei content types + labeling (simulabile con l'utente, resta proxy) |
| Tree testing | La sezione "Findability gaps" con ipotesi da validare + test proposto |
| Site map | Il Navigation model come artifact principale |
| Content audit | L'Inventario degli oggetti navigabili |
| IA heuristics (Morville) | La review delle 7 sfaccettature (sopra) |
