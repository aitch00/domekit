# Cognitive Analysis — Reference

3 pattern per l'analisi automatica del carico cognitivo nei user flow. Applicare dopo la creazione di ogni flow, senza input utente.

---

## Pattern 1: Choice Overload (Hick's Law)

**Principio:** Il tempo di reazione aumenta logaritmicamente con il numero di scelte. Oltre 4 opzioni, gli utenti sperimentano paralisi decisionale misurabile.

### Detection

**Keyword nei step del flow:**
- "choose", "select", "pick", "options", "from", "decide between", "scegli", "seleziona"

**Soglia:** > 4 opzioni presentate simultaneamente in un singolo step.

### Severity

| N. opzioni | Severity | Impatto utente |
|-----------|----------|---------------|
| 4–5 | medium | Rallentamento percettibile, alcuni utenti esitano |
| 6–8 | high | Paralisi decisionale, rischio significativo di abbandono |
| 9+ | critical | La maggior parte degli utenti abbandona o sceglie casualmente |

### Warning Message

```
⚠ Hick's Law — Step [N]: [descrizione]
  Severity: [medium/high/critical]
  Impact: Paralisi decisionale — rischio abbandono
  Mitigation: Ridurre a 3–4 opzioni; progressive disclosure; smart default
```

### Strategie di Mitigation

1. **Ridurre le opzioni** — Mostrare solo le 3–4 più rilevanti
2. **Progressive disclosure** — Nascondere opzioni avanzate dietro "Altre opzioni"
3. **Smart defaults** — Pre-selezionare l'opzione raccomandata
4. **Grouping** — Organizzare opzioni in 2–3 categorie
5. **Search/filter** — Per set ampi, fornire ricerca invece di lista piatta

---

## Pattern 2: Memory Overload (Miller 7±2)

**Principio:** Gli esseri umani possono tenere circa 7 ± 2 elementi nella working memory simultaneamente. Superare questo limite causa errori, confusione e abbandono.

### Detection

**Keyword nei step del flow:**
- "fill", "enter", "form", "fields", "list", "items", "input", "complete", "compila", "inserisci", "campi"

**Soglia:** > 7 item, campi o informazioni in un singolo step.

### Severity

| N. item | Severity | Impatto utente |
|--------|----------|---------------|
| 7–9 | medium | Alcuni errori, attrito percettibile |
| 10–12 | high | Errori frequenti, frustrazione utente |
| 13+ | critical | Abbandono del form, overwhelm |

### Warning Message

```
⚠ Miller 7±2 — Step [N]: [descrizione]
  Severity: [medium/high/critical]
  Impact: Overload di working memory — errori e abbandono
  Mitigation: Chunking in gruppi di 5–7; form multi-step; ridurre campi totali
```

### Strategie di Mitigation

1. **Chunk in gruppi** — Dividere in gruppi logici di 3–5 item
2. **Form multi-step** — Distribuire su 2–3 schermate con progress indicator
3. **Ridurre i campi totali** — Rimuovere campi nice-to-have
4. **Label chiare** — Nomi di campo inequivocabili riducono il costo cognitivo per campo
5. **Smart defaults e auto-fill** — Pre-popolare da profilo, dispositivo o input precedente

---

## Pattern 3: Context Switching

**Principio:** Quando l'utente deve lasciare il contesto attuale per verificare, recuperare o interagire con qualcosa di esterno, il momentum si interrompe e il costo cognitivo aumenta al re-engagement.

### Detection

**Keyword nei step del flow:**
- "switch to", "open", "external", "check", "go to", "leave", "navigate away", "another tab"
- "apri", "vai a", "controlla", "esci", "altra scheda", "app esterna"

**Soglia:** qualsiasi occorrenza attiva il flag. Il conteggio cumulativo aumenta la severity.

### Severity

| Switch per flow | Severity | Impatto utente |
|----------------|----------|---------------|
| 1 | medium | Perdita di momentum, attrito minore |
| 2 | high | Attrito significativo, alcuni utenti non tornano |
| 3+ | critical | Flow effettivamente rotto — la maggior parte abbandona |

### Warning Message

```
⚠ Context Switching — Step [N]: [descrizione]
  Severity: [medium/high/critical]
  Impact: Interruzione del flow — perdita di momentum e rischio abbandono
  Mitigation: Embed informazioni inline; progressive disclosure; auto-save state
```

### Strategie di Mitigation

1. **Embed inline** — Mostrare le informazioni necessarie nel contesto corrente (preview inline, widget embedded)
2. **Progressive disclosure** — Rivelare info senza navigazione (sezioni espandibili, tooltip)
3. **State save/resume** — Auto-save del progresso per gli utenti che tornano
4. **Minimizzare i salti esterni** — Sostituire "vai a controllare la tua email" con notifica in-app
5. **Deep link** — Se la navigazione è inevitabile, fornire deep link diretti alla destinazione esatta

---

## Severity Combinata

Quando più pattern co-occorrono nello stesso flow:

| Scenario | Severity combinata |
|----------|--------------------|
| 1 pattern a medium | medium |
| 2 pattern a medium | high |
| 1 pattern a high | high |
| 1 pattern a critical O 3+ a medium | critical |

---

## Output Format per il Flow

Aggiungere una sezione **Cognitive Analysis** in ogni flow document:

```markdown
## Cognitive Analysis

[Se nessun flag]: Nessun red flag rilevato. Il flow appare cognitivamente sostenibile.

[Se flag presenti]: [N] red flag rilevati:

⚠ [Tipo] — Step [N]: [descrizione]
  Severity: [level]
  Impact: [impatto]
  Mitigation: [raccomandazione]
```

---

## Istruzioni per l'Agente

1. Scansiona ogni step description del happy path per le keyword dei 3 pattern
2. Conta le occorrenze vs. le soglie
3. Genera warning per ogni soglia superata
4. Applica severity combinata se più pattern presenti
5. Aggiungi la sezione "Cognitive Analysis" al documento del flow
6. **Non bloccare la creazione del flow** — i warning sono informativi, non gate
