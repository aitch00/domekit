# Prototype Contract — output HTML navigabile

Contratto dell'artefatto prodotto da `dm:prototype`. Definisce *cosa* deve garantire il payload, non *come* deve essere bello (l'estetica di marca è di `dome-design`, a valle).

## Principio guida

Il prototipo serve a **vedere il flow funzionare**. Ogni decisione di rendering è subordinata a una domanda: *"questo aiuta a validare che il flow regga, o è decorazione?"*. Se è decorazione, in `lightweight` si omette; in `deep` è ammessa solo se presentabile, mai brand.

## Struttura su disco

```
docs/design/prototypes/YYYY-MM-DD-[flow-slug]-01/
├── index.html          # payload: punto d'ingresso navigabile
├── assets/
│   └── mock.json       # opzionale: dati mock se non inline
└── index.meta.yaml     # sidecar handoff (l'handoff vero e proprio)
```

## Requisiti del payload (`index.html`)

1. **No build step.** Apribile con doppio click / `file://`. Niente bundler, niente import di moduli che richiedono un server, niente dipendenze da rete obbligatorie. CSS/JS inline o in file locali referenziati relativamente.
   - ⚠️ **Mock inline di default.** `fetch('assets/mock.json')` è bloccato da molti browser sotto `file://` (CORS su origin `null`): i dati non si popolano se il prototipo è solo aperto da disco. Quindi in `lightweight` i dati mock vanno **inline** nell'HTML (oggetto JS o `<script type="application/json">`), garantendo l'apertura pura da `file://`. Il file esterno `assets/mock.json` è ammesso **solo** quando il prototipo verrà *servito* (es. `deep` destinato a una demo via server statico) — in quel caso dichiararlo nel sidecar e nel report.
2. **Una schermata per riga di Screen Inventory.** Il flow (conforme al
   `flow-handoff-contract`) espone uno Screen Inventory: una schermata navigabile per ogni
   `sc.<slug>`, **non** per step — gli step che condividono una schermata la riusano (no
   duplicati). Le schermate possono essere sezioni mostrate/nascoste (SPA semplice con JS) o
   pagine collegate — purché la navigazione sia reale.
3. **Tutti gli stati dichiarati, sempre.** Ogni schermata renderizza **tutti** gli "stati da
   materializzare" del suo Screen Inventory (default/popolato · vuoto · loading · errore ·
   success), con uno state-switcher per percorrerli. Vale anche in `lightweight`: gli stati
   sono struttura, non decorazione. Gli stati off-path sono ciò che altrimenti si inventa.
4. **CTA con stato e feedback reale.** Ogni CTA rispetta *condizione di enable* e
   *comportamento in-flight* (loading allo click, mai disabilitare in submit); errori di campo
   **inline accanto al campo**, focus sul primo. La **System Response** dichiarata è mostrata
   dopo l'azione — non inventata. Le azioni distruttive richiedono conferma esplicita.
5. **Navigazione funzionante.** Link/azioni con `<a href>` o `<button>` reali (no `div`
   cliccabili). Ogni azione transita verso lo Screen ID dell'`Outcome →`. Percorsi
   alternativi/errori raggiungibili; ogni stato `errore` è agganciato al suo trigger+recovery.
6. **Nessun vicolo cieco non dichiarato.** Schermata terminale o ramo troncato → indicato in
   pagina (badge "fine flow" / "non implementato"), non muto.
7. **HTML semantico.** `header/nav/main/section/footer`, un solo `<h1>` per schermata,
   gerarchia heading corretta, controlli reali. Stessa disciplina di `dm-landing-render`.
8. **Mock coerenti.** I mock rispettano lo **Screen Inventory** (Stato dati) e, se presente, il
   domain-model (entità/campi/relazioni — niente campi inventati). Limiti residui in `weak_sections`.

## Livelli di triage

| Aspetto | `lightweight` (default) | `deep` |
|--------|--------------------------|--------|
| Scopo | loop interno flow ⇄ prototype, si butta | deliverable per validazione cliente |
| Mock | essenziali, anche placeholder | realistici, coerenti tra schermate |
| Stile | neutro di sistema, minimo CSS | layout presentabile, navigazione completa |
| Cura | minima | alta (ma **strutturale**, non brand) |
| Tempo target | rapido | 150–200% del lightweight |

In entrambi i livelli i 6 requisiti del payload restano invariati: cambia la **cura**, non il contratto di navigabilità.

## Confine con `dome-design` (da non superare)

- ❌ palette di marca, token, componenti riusabili, motion curato → `dome-design`
- ❌ accessibilità formale WCAG certificata → `accessibility-audit`
- ❌ logica backend reale, codice di produzione
- ✅ struttura navigabile, stati, transizioni, mock coerenti → **qui**

Se durante la generazione emerge la tentazione di "renderlo bello", fermarsi: il prototipo resta strutturale. La cura estetica si aggiunge invocando `dome-design` sul prototipo, non gonfiando questo atomo.

## Esempi rifiutati

- Prototipo esteticamente curato che **salta 2 step** del flow → falsa il test.
- Schermate **statiche non navigabili** → è un mockup, non un prototipo.
- Dati mock **incoerenti** col domain-model (campi inventati, relazioni rotte).
