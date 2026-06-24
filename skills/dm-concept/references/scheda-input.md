# Scheda input — dm:concept (provenienza)

Archivio della scheda a 7 aree compilata in fase di creazione (pipeline `dm:create`, modalità yolo).
Fonte di verità del bisogno: `docs/analysis/pipeline-gaps/03-ideation-concept.md` (sezione
"Skill da creare: dm-concept") + `docs/analysis/pipeline-gaps/README.md`.

| Area | Contenuto |
|------|-----------|
| **1. Problema** | Dopo la divergenza (dm:ideation) serve **convergere**: trasformare un insieme di idee in 2-3 approcci di prodotto strutturati e sceglierne uno con una decisione tracciata. Senza questo passaggio la selezione resta implicita e la prima idea vince per inerzia. |
| **2. Input** | `ideation_cards` (output dm:ideation, upstream primario) · `brief` (criteri di selezione) · `personas` (fit utente) · `selection_criteria` (opzionale, espliciti) · `n_concepts` (default 2-3). |
| **3. Output** | `docs/design/concepts/YYYY-MM-DD-concept-[nome]-01.md` (uno per concept: tagline, core interaction model, assunzioni critiche, trade-off, sketch testuale, rischi) + `concept-selection.md` (matrice criteri × concept, concept selezionato, elementi da salvare, decisioni aperte). |
| **4. Comportamento** | Deduci criteri + concept dalle idee/brief → presenta → verifica → seleziona (modello dm:journeys). Modalità guided/yolo/headless. Grounding corpus via dm:research-corpus (degrada a scansione diretta). |
| **5. Regole load-bearing** | (1) Concept ≠ flow ≠ PRD: resta ad alto livello, rimanda dettaglio a dm:flows / requisiti a dm:spec. (2) Due criteri inviolabili: mapping su ≥1 journey con miglioramento misurabile del peak/end + assunzione più rischiosa testabile. (3) Minimo 2 concept genuinamente alternativi (protezione dall'inerzia). Valutazione concentrata nella sola selection. |
| **6. Handoff** | Upstream: dm:ideation (primario), brief, personas, journeys. Downstream: dm:flows (il concept selezionato è il frame dei flow) + dm:spec (contesto strategico del PRD). |
| **7. Tipo e collocazione** | atom-dominio, domain: design, category: ideation. Collocata in `.claude/skills/dm-concept/` come comando dm-* (come dm:journeys/dm:flows), NON in `dome/`. |

**Anti-duplicazione.** `dm:ideation` diverge (genera idee, non valuta); `dm:concept` converge
(struttura + seleziona). `dm:spec` produce il PRD (requisiti verificabili, a valle); `dm:brief`
inquadra il prodotto a monte. `dm:concept` occupa lo spazio ideation→flow: la scelta dell'approccio,
sopra il flow e sotto il PRD. Coppia con `dm:ideation` a coprire la Fase 3 (divergenza/convergenza).
