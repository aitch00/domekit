# Scheda input — `dm-prototype`

> Atomo-dominio design che materializza un flow in un prototipo HTML navigabile con dati mock.
> Compilata secondo `02-processo-creazione/01-scheda-input.md` (workflow v1.0.0).
> Da archiviare come `references/scheda-input.md` nella cartella della skill.
>
> **Ordine di costruzione**: questo atomo si costruisce ORA, in standalone, insieme
> alla resa handoff-conforme di `dm-flows`. È il banco di prova: `dm-flows` → `dm-prototype`
> invocati a mano su progetti reali servono a validare se il loop regge e se mancano step,
> PRIMA di cristallizzare i composti (`flow-to-prototype`, `design-to-prototype`).
> I composti si scrivono dopo la validazione empirica, non prima (cfr. decision tree Q4,
> anti-pattern "composti che nascondono pipeline": prima gli atomi, il composto dopo).

---

## Area 1 — Identità del processo

```yaml
area_1_identita:
  nome: dm-prototype
  famiglia: design            # dominio -> dome/dm-prototype/
  intent: >
    Trasforma un flow (e l'eventuale domain-model) in un prototipo HTML
    navigabile con dati mock, per vedere il flow funzionare prima di costruirlo.
  invocata_da:
    # DUE consumatori reali -> soddisfa il test del riuso per atomi-dominio
    # (decision tree Q2: serve invocazione da >=2 skill/contesti diversi).
    - utente diretto, in STANDALONE — testare un flow al volo senza passare dal
      composto. Caso d'uso primario nella fase di validazione: dm-flows -> dm-prototype
      a mano, per capire se il loop regge e se servono altri step.
    - composto flow-to-prototype (downstream) — come fase terminale del loop,
      una volta che i composti verranno costruiti dopo la validazione.
  produce: >
    payload HTML navigabile (index.html + dati mock inline o JSON)
    + sidecar metadata handoff (index.meta.yaml) con frontmatter canonico
```

Nota: l'output è payload non-textual, quindi l'handoff vive nel sidecar
`index.meta.yaml` (vedi `02-principio-handoff.md`, sezione "Formati ammessi" —
stesso schema usato da `colorize`). Il sidecar È l'handoff; l'HTML è il payload referenziato.

---

## Area 2 — Dominio e perimetro

```yaml
area_2_dominio:
  dominio_applicativo: design
  scope_in:
    - rendering di schermate navigabili a partire dagli step di un flow
    - popolamento con dati mock coerenti con il domain-model (se presente)
    - navigazione funzionante tra schermate (link, stati, transizioni del flow)
  scope_out:
    - brand direction, estetica curata di marca   # -> competenza di dome-design
    - design system, token, componenti riusabili  # -> competenza di dome-design
    - codice di produzione, logica backend reale
    - validazione di accessibilità formale          # -> accessibility-audit
  vocabolario:
    [prototipo, mock data, schermata, navigazione, flow-step,
     stato, hi-fi, lo-fi, walkthrough, click-through]
```

**Confine critico con `dome-design`** (è la distinzione che giustifica l'esistenza
dell'atomo): `dome-design` lavora l'estetica e il brand; `dm-prototype` lavora la
*struttura navigabile* per validare che il flow regga. Anche in modalità Deep-curata
il prototipo resta strutturale, non un esercizio di brand. Se serve estetica di marca,
si invoca `dome-design` a valle, non si gonfia questo atomo.

---

## Area 3 — Fasi e flusso

```yaml
area_3_fasi:
  fasi_coperte: [Drafting]      # atomo monofase: genera l'artefatto
  classe_atomo: dominio
  posizione_fase: "step interno alla fase di materializzazione del design"
  ordine: n/a (atomo singolo, non orchestra)
  estensioni_frontmatter:
    - purpose: validation | client   # dichiara l'uso del prototipo, NON il triage
    - screens_count: <int>           # numero di schermate generate
    - source_flow: <path handoff flow consumato>
  output:
    Drafting: >
      index.html navigabile + index.meta.yaml.
      Lo stato dell'handoff dipende dal giudizio dell'utente (vedi Area 5):
      complete se il flow regge, altrimenti resta draft con upstream al flow.
```

Segnale di livello: nessuna sequenza di fasi multiple, vocabolario di dominio ricco
→ **atomo-dominio**, non pipeline. Coerente con `02-scelta-livello.md`.

---

## Area 4 — Prospettive da incorporare

```yaml
area_4_prospettive:
  punti_vista:
    - utente del flow (riesce a completare il percorso? dove si blocca?)
    - designer (il flow che ho disegnato regge una volta reso navigabile?)
  tensioni:
    - "navigabilità minima per validare" vs "fedeltà sufficiente a non falsare il test"
      -> si compone scegliendo il livello di triage (lo sporco basta a validare,
         il curato serve a presentare)
  agent_dedicati: nessuno
```

Nota: due sole prospettive, **non** è candidato a Review Multi-Persona (servono 3+ con
tensione forte per giustificarla, vedi `01-scheda-input.md` Area 4). La review qui è
il giudizio diretto dell'utente in Guided, non una passata multi-agente.

---

## Area 5 — Qualità attesa

```yaml
area_5_qualita:
  criteri_buono:
    - ogni step del flow upstream ha una schermata corrispondente navigabile
    - i link/azioni del flow funzionano (nessun vicolo cieco non dichiarato)
    - i dati mock sono coerenti con le entità del domain-model (se presente)
    - il prototipo è apribile in un browser senza build step
  esempi_accettabili:
    - "[TODO: 3 prototipi reali da progetti passati - lo-fi loop + hi-fi cliente]"
  esempi_rifiutati:
    - "prototipo bello esteticamente ma che salta 2 step del flow"        # falsa il test
    - "schermate statiche non navigabili (è un mockup, non un prototipo)"
    - "dati mock incoerenti col domain-model (campi inventati, relazioni rotte)"
  validazione: >
    giudizio diretto dell'utente in Guided. Il prototipo si apre, l'utente
    percorre il flow, decide: regge -> conferma complete; non regge -> rientra
    in dm-flows con il finding di cosa si è rotto. NESSUN confidence-gating
    automatico: il gate è l'utente (coerente con modalità Guided scelta).
  harvest_policy:
    domain: opt-in          # pattern di prototipazione settoriali, rari
    solution: auto          # soluzioni di navigazione/mock ricorrenti, capitalizzabili
    method: every-3-uses    # confronto fra approcci di prototipazione nel tempo
```

---

## Area 6 — Variabilità di input

```yaml
area_6_variabilita:
  parametri:
    - mode: guided (default) | headless    # yolo non distinguibile da guided qui
    - triage: lightweight | deep           # DICHIARATO DALL'UTENTE
  triage:
    tipo: manuale                          # triage_source: manual
    motivazione: >
      Lightweight (sporco, loop di validazione) vs Deep (curato, deliverable cliente)
      NON è una proprietà misurabile del flow in ingresso: lo stesso flow produce
      l'uno o l'altro a seconda di cosa serve. Ancorarlo a un segnale automatico
      sarebbe l'anti-pattern "triage su segnali non misurabili" (06-triage-complessita).
    lightweight:
      cura: minima, dati mock essenziali, stile neutro di sistema
      scopo: loop interno flow <-> prototype, si butta
      tempo_target: rapido
    deep:
      cura: dati mock realistici, layout presentabile, navigazione completa
      scopo: deliverable per validazione cliente (es. caso "contratto firmato")
      tempo_target: 150-200% del lightweight
    default: lightweight    # il loop di studio è il caso d'uso più frequente
  enumerazioni_stabili: nessuna -> non parametrica (no CSV)
```

**Punto chiave della tua decisione**: un solo atomo, due livelli di triage manuale.
Non due skill. La logica di trasformazione flow→HTML è identica; cambia solo la cura.

---

## Area 7 — Vincoli e dipendenze

```yaml
area_7_vincoli:
  vincoli:
    tempo: rapido in lightweight, esteso in deep
    stack: HTML/CSS/JS statico, nessun build step, apribile in browser
    output: cartella prototipo + sidecar metadata
  principi_L0:
    - 02-principio-handoff      # consuma handoff flow, produce handoff sidecar
    - 03-principio-atomi        # è un atomo, deve soddisfare i 4 criteri
    - 04-principio-livelli      # atomo L1 di dominio
  pattern_implementati:
    - 06-triage-complessita     # triage manuale a 2 livelli (lightweight/deep)
    - 08-modalita-esecuzione    # guided + headless
  atomi_candidati_riuso:
    - dome-design        # NON riusare come base: confine diverso (estetica vs struttura).
                         #   eventualmente invocabile A VALLE per la cura estetica.
    - dm-landing         # verificare se la logica di rendering HTML è riusabile
    - dm-slides          # idem, verificare overlap su generazione HTML statico
    # Regola anti-duplicazione (Area 7): cercato overlap. dm-landing/dm-slides
    # generano HTML ma per scopi diversi (landing, slide). Il rendering di
    # schermate-da-flow è abbastanza distinto da giustificare atomo nuovo,
    # MA verificare in 04-riusare-atomi se esiste un atomo di rendering condivisibile.
  database_L3:
    consumati: nessuno
    nota: >
      il domain-model in ingresso NON è un database L3: è un handoff upstream
      (artifact di dm-domain-modeler), consumato via catena, non via lookup.
  consuma_handoff:
    - flow (da dm-flows)                    # upstream obbligatorio
    - domain-model (da dm-domain-modeler)   # upstream opzionale, alza la qualità dei mock
  consumatori:                              # >=2 -> test del riuso soddisfatto (Q2)
    - utente diretto (standalone, fase di validazione)
    - flow-to-prototype (composite, post-validazione)
  pinning: no
```

---

## Criteri di completamento — check

- [x] 7 aree con risposta non vuota
- [x] Area 1 completa (nome, famiglia, intent, invocazione/output)
- [x] **Test del riuso (Q2) soddisfatto**: 2 consumatori dichiarati (standalone + flow-to-prototype)
- [~] Area 5: esempi rifiutati presenti (3); **esempi accettabili da compilare** con 3 prototipi reali
- [x] Area 5: harvest_policy sui 3 piani
- [x] Area 7: principi L0 + ricerca atomi riusabili effettuata
- [ ] **Azione aperta**: verificare in `04-riusare-atomi.md` se `dm-landing`/`dm-slides`
      espongono un atomo di rendering HTML condivisibile prima di scrivere il Drafting da zero
