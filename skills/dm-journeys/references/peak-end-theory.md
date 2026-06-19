# Peak-End Theory & Emotion Scale

Framework per l'analisi dell'esperienza utente nelle customer journey map. Basato sulla ricerca di Daniel Kahneman.

---

## Peak-End Theory

**Principio:** Il ricordo di un'esperienza è determinato principalmente da due momenti, non dalla media:

1. **Peak moment** — il punto di maggiore intensità emotiva (positiva o negativa)
2. **End moment** — l'impressione finale prima che l'esperienza si concluda

La durata e la qualità media dell'esperienza hanno un effetto sorprendentemente piccolo sul ricordo. Una journey di 30 minuti con un forte peak e un end positivo è ricordata meglio di una journey di 10 minuti uniformemente buona ma piatta.

**Implicazione chiave per il design:** investire sforzo di design sproporzionato nei momenti peak e end. Un'esperienza con un momento wow e una conclusione soddisfacente supera un'esperienza uniformemente "buona".

---

## Emotion Scale — Scala -2 a +3

**Nota:** La scala va da -2 a +3, non da 1 a 5. Questa asimmetria riflette che i momenti positivi straordinari (+3 "Delighted") sono più rari e preziosi dei momenti negativi forti (-2 "Frustrated").

| Valore | Label | Definizione |
|--------|-------|-------------|
| **-2** | Frustrated | Qualcosa ha bloccato l'utente chiaramente, o ha vissuto un fallimento. Risposta emotiva: rabbia, impotenza, forte fastidio. |
| **-1** | Uncertain | L'utente è confuso o dubita di essere sul percorso giusto. Non bloccato, ma non sicuro. |
| **0** | Neutral | Nessuna reazione emotiva — l'esperienza funziona come atteso. Stato baseline. |
| **+1** | Satisfied | Task completato come atteso con un lieve senso positivo. Le cose sono andate bene. |
| **+2** | Excited | Qualcosa ha superato le aspettative. Sorpresa positiva, piacere genuino. |
| **+3** | Delighted | Momento wow. Forte sorpresa positiva, picco emotivo. Raro e prezioso. |

### Guida per ciascun valore

**-2 Frustrated:**
- L'utente è stato bloccato da un errore poco chiaro, ha perso dati, o una feature su cui si basava è scomparsa
- Design implication: rimuovere il blocco. Se inevitabile, fornire guida cristallina su come procedere. Non lasciare mai l'utente a -2 senza un percorso di recovery.

**-1 Uncertain:**
- L'utente non è sicuro di aver selezionato il metodo di pagamento corretto, la navigazione è poco chiara, un campo è ambiguo
- Design implication: aggiungere chiarezza tramite label, inline help, confirmation prompt, contextual hint.

**0 Neutral:**
- Login avvenuto con successo (comportamento atteso), dashboard familiare, catalogo navigato come sempre
- Design implication: il neutral è lo stato baseline. L'UX funziona ma non è memorabile. Mirare a superarlo dove possibile.

**+1 Satisfied:**
- Checkout completato in 3 step senza attrito, impostazione trovata rapidamente, report generato correttamente al primo tentativo
- Design implication: buona UX, ma non memorabile. Small enhancements possono spingere +1 → +2.

**+2 Excited:**
- L'app ha risparmiato il 20% più del previsto, auto-fill dei dati inatteso, feature suggestion esattamente giusta
- Design implication: candidato peak. Memorabile e positivo. Celebrare, rendere condivisibile, rinforzare il valore.

**+3 Delighted:**
- L'app ha suggerito esattamente cosa serviva prima che l'utente cercasse, il support ha risolto un problema complesso in 2 minuti, il primo uso è stato "magico"
- Design implication: riservare per veri momenti peak. Non cercare di fare tutto +3 — diluerebbe l'impatto. Un momento +3 per journey è potente.

---

## Emotion Variance Validation

**Regola:** Se tutte le fasi di una journey hanno lo stesso valore emotivo, emettere un warning:

> "Attenzione: curva emotiva piatta rilevata — tutte le fasi hanno emozione [N]. Le journey realistiche hanno variazione emotiva. Rivedi i valori emotivi per verificare l'accuratezza della raccolta dati."

**Pattern attesi:**
- Journey realistiche hanno almeno 2 valori emotivi diversi
- La maggior parte delle journey include almeno un dip (incertezza o attrito) e un peak
- L'emozione finale (end) dovrebbe idealmente essere più alta del punto più basso della journey

### Pattern Tipici

| Tipo journey | Arco emotivo tipico | Note |
|-------------|---------------------|------|
| Onboarding | 0 → -1 → +1 → +2 | Parte neutro, cala alla complessità setup, sale al primo successo |
| Error recovery | -2 → -1 → 0 → +1 | Parte frustrato, migliora gradualmente attraverso la risoluzione |
| Feature adoption | 0 → +1 → +2 | Curva di apprendimento poi crescente fiducia e padronanza |
| Support interaction | -1 → -2 → 0 → +1 | Problema → frustrazione nell'attesa → sollievo dopo la risoluzione |
| Purchase flow | +1 → 0 → -1 → +2 | Eccitazione → neutralità di valutazione → attrito pagamento → soddisfazione post-acquisto |

---

## Peak Identification

**Come identificare il peak:**
- Peak = fase con il valore assoluto più alto (il massimo in valore assoluto: può essere +3 o -2)
- Se ci sono parità, il peak è la prima fase con quel valore nel percorso

**Come identificare l'end:**
- End = ultima fase della journey (indipendentemente dal valore emotivo)

---

## Design Recommendations — Template

### Peak positivo (≥ +2)

| Strategia | Come applicare | Esempio |
|-----------|---------------|---------|
| **Celebration** | Segnalare il successo visivamente e chiaramente | Animazione confetti al primo task completato |
| **Sharing** | Abilitare il reinforcement sociale al peak | "Condividi il tuo risultato" dopo un milestone |
| **Personalization** | Rendere il momento peak uniquamente loro | Messaggio custom che riferisce il loro specifico traguardo |
| **Reinforcement** | Ricordare il successo successivamente | Widget dashboard con streak o progress milestone |

### Peak negativo (≤ -1)

| Strategia | Come applicare | Esempio |
|-----------|---------------|---------|
| **Root cause** | Trovare cosa specificamente genera frustrazione | User testing rivela una label confusa |
| **Simplify** | Ridurre la complessità nel pain point | Sostituire input manuale con smart selection |
| **Add guidance** | Fornire aiuto dove avviene la confusione | Inline hint, tooltip contestuale, esempi |
| **Reduce decisions** | Rimuovere scelte non necessarie nel pain step | Auto-detect impostazioni invece di chiedere |

### End moment

| Strategia | Come applicare | Esempio |
|-----------|---------------|---------|
| **Clear closure** | Segnalare chiaramente che il task è completato | Success screen con riepilogo di cosa è stato fatto |
| **Next step clarity** | Mostrare cosa succede dopo | "Il tuo ordine spedisce in 2 giorni. Traccialo qui." |
| **Positive reinforcement** | Chiudere su una nota alta | "Hai risparmiato 2 ore questa settimana con la fatturazione automatica." |
| **Lasting impression** | Lasciare qualcosa di memorabile | Follow-up email con insight personalizzati |

---

## Anti-Patterns

- **Journey piatta:** nessun peak = nessuna esperienza memorabile. Se tutte le fasi sono neutrali (0), l'esperienza è dimenticabile.
- **End negativo:** anche se il resto della journey è buono, uno step finale frustrante rovina il ricordo.
- **Peak negativi multipli:** più di un momento -2 in una journey segnala un'esperienza fondamentalmente rotta — correggere la struttura, non solo i momenti.
