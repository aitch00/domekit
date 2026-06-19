# Cognitive Bias Catalog

Catalogo di bias cognitivi per la profilazione delle personas. Selezionare 3 bias più rilevanti per contesto, occupazione e pattern d'uso della persona. Assegnare una severity basata su quanto il bias impatta il comportamento nel contesto specifico del prodotto.

## Severity Scale

| Range | Label | Significato |
|-------|-------|-------------|
| 0–3 | low | Il bias esiste ma raramente impatta il comportamento in questo contesto |
| 4–6 | medium | Il bias influenza regolarmente le decisioni; il design dovrebbe tenerlo in conto |
| 7–10 | high | Il bias guida fortemente il comportamento; il design deve mitigarlo o sfruttarlo attivamente |

---

## Confirmation Bias

**Definizione:** Tendenza a cercare, interpretare e ricordare informazioni che confermano le credenze esistenti, ignorando quelle contraddittorie.

**Manifestazione UX:** L'utente ignora messaggi di errore che contraddicono il suo modello mentale. Scarta workflow non familiari. Cerca conferme del proprio approccio preferito.

**Design response:**
- Presentare informazioni contraddittorie accanto a quelle confermanti
- Usare progressive disclosure per mostrare prospettive alternative
- Stati di errore impossibili da ignorare (inline, non dismissibili)

**Severity alta quando:** L'utente ha forte expertise nel dominio (si fida della propria esperienza).

---

## Anchoring Effect

**Definizione:** Eccessiva dipendenza dal primo dato incontrato nelle decisioni.

**Manifestazione UX:** Il primo prezzo visto diventa il riferimento. I valori di default influenzano fortemente le scelte finali. I risultati iniziali di una ricerca definiscono la percezione dell'offerta.

**Design response:**
- Essere intenzionali su quale informazione appare per prima
- Usare l'anchoring strategicamente (mostrare il tier premium prima per percezione del valore)
- Fornire strumenti di confronto per contrastare la fissazione su un singolo anchor

**Severity alta quando:** Contesti di pricing/e-commerce. Utenti che prendono decisioni non familiari.

---

## Availability Heuristic

**Definizione:** Sovrastima della probabilità di eventi facilmente ricordati — tipicamente recenti, vividi o emotivamente carichi.

**Manifestazione UX:** Dopo un errore, l'utente si aspetta errori ovunque. Un'esperienza positiva recente gonfia la fiducia. Notizie su data breach aumentano l'ansia di sicurezza.

**Design response:**
- Fornire contesto fattuale (statistiche, tassi di successo) accanto all'esperienza aneddotica
- Usare indicatori di successo consistenti per costruire modelli mentali affidabili
- Rispondere a preoccupazioni fear-based con dati trasparenti

**Severity alta quando:** Utenti che usano il prodotto raramente (campione di esperienza limitato). Dopo esperienze negative.

---

## Choice Overload

**Definizione:** Paralisi decisionale quando si presentano troppe opzioni. Correlato alla Hick's Law (il tempo di reazione aumenta con il numero di scelte).

**Manifestazione UX:** L'utente abbandona la selezione con troppe opzioni. Passa tempo eccessivo a comparare item simili. Default a "nessuna scelta" (abbandona la pagina).

**Design response:**
- Limitare le opzioni visibili a 3–4 (soglia Hick's Law)
- Usare progressive disclosure per opzioni avanzate
- Smart defaults e raccomandazioni
- Raggruppare opzioni in categorie

**Severity alta quando:** Decision style basso (intuitivo). Cognitive capacity bassa.

---

## Status Quo Bias

**Definizione:** Preferenza per lo stato attuale delle cose. Il cambiamento è percepito come perdita anche quando è oggettivamente vantaggioso.

**Manifestazione UX:** Resistenza agli aggiornamenti UI. Continua a usare workflow inefficienti per abitudine. Ignora annunci di nuove feature. Torna ai vecchi metodi dopo aver provato quelli nuovi.

**Design response:**
- Introdurre cambiamenti gradualmente (feature flags, opt-in)
- Mostrare chiaro confronto prima/dopo dei benefici
- Preservare pattern di interazione familiari dove possibile
- Opzioni "undo" o "revert" per ridurre il rischio percepito

**Severity alta quando:** Utenti long-term. Utenti in industrie regolamentate (risk aversion).

---

## Recency Effect

**Definizione:** Peso sproporzionato dato alle informazioni o esperienze più recenti.

**Manifestazione UX:** L'ultimo item di una lista riceve più attenzione. Un'interazione negativa recente colora la percezione complessiva del prodotto. L'ultima feature usata è assunta come la più importante.

**Design response:**
- Posizionare informazioni critiche sia all'inizio (primacy) che alla fine (recency) delle sequenze
- Usare schermate di riepilogo per rinforzare decisioni chiave precedenti
- Esperienze consistenti per evitare che una singola interazione negativa domini la percezione

**Severity alta quando:** Utenti occasionali (poche esperienze da mediare).

---

## Sunk Cost Fallacy

**Definizione:** Continuazione di un'azione a causa delle risorse già investite (tempo, denaro, effort), indipendentemente dal valore futuro.

**Manifestazione UX:** L'utente continua con un setup problematico perché ha già speso del tempo. Riluttante a cambiare piano nonostante alternative migliori. Mantiene abbonamenti inutilizzati.

**Design response:**
- Rendere facile ricominciare senza penalità
- Mostrare chiaramente costo/beneficio del continuare vs. cambiare
- Fornire percorsi di migrazione che preservano l'investimento precedente (dati, impostazioni)

**Severity alta quando:** Utenti che hanno investito setup time significativo. Prodotti a pagamento.

---

## Endowment Effect

**Definizione:** Sopravvalutazione di ciò che si possiede già rispetto a ciò che non si possiede.

**Manifestazione UX:** Gli utenti sopravvalutano i propri dati, workflow, personalizzazioni. Riluttanti a eliminare contenuti vecchi. Resistenza alla migrazione di piattaforma anche oggettivamente migliore.

**Design response:**
- Enfatizzare cosa si guadagna piuttosto che cosa si perde
- Opzioni di export/backup per preservare il senso di ownership
- Periodi di prova che creino un senso di possesso prima dell'acquisto

**Severity alta quando:** Prodotti di content creation. Utenti con estesa personalizzazione.

---

## Default Effect

**Definizione:** Forte tendenza ad accettare opzioni pre-selezionate o default senza valutazione attiva.

**Manifestazione UX:** Raramente cambia le impostazioni default. Accetta piani/opzioni suggeriti. Non personalizza notifiche o impostazioni privacy.

**Design response:**
- Scegliere i default con cura — diventano la scelta de facto per la maggior parte degli utenti
- Default etici (privacy-protective, cost-effective)
- Visivamente chiaro quando si usa un default vs. una scelta custom

**Severity alta quando:** Utenti non tecnici. Decision style basso.

---

## Loss Aversion

**Definizione:** Le perdite fanno circa il doppio del male rispetto al piacere di guadagni equivalenti.

**Manifestazione UX:** La paura di perdere dati frena l'adozione di nuovi strumenti. "Cancella abbonamento" genera ansia di perdere l'accesso. Gli utenti accumulano feature/item raramente utilizzati.

**Design response:**
- Inquadrare i cambiamenti come guadagni ("Sblocca X" vs. "Non perdere X")
- Safety net (undo, recovery, grace period)
- Usare il framing della perdita raramente e eticamente

**Severity alta quando:** Prodotti con dati utente persistenti. Modelli in abbonamento.
