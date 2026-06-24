# Scheda input — dm:ia (provenienza)

Archivio della scheda a 7 aree compilata in fase di creazione (pipeline `dm:create`, modalità yolo).
Fonte di verità del bisogno: `docs/analysis/pipeline-gaps/04-information-architecture.md` +
`docs/analysis/pipeline-gaps/README.md`.

| Area | Contenuto |
|------|-----------|
| **1. Problema** | Il flow modella un task singolo, ma un prodotto reale è un insieme di flow collegati. La struttura che li tiene insieme (come ci si muove tra flow, come si chiamano le sezioni, cosa si vede da un deep link) non è definita da nessuna skill. Senza IA ogni flow decide la navigazione in modo indipendente → incoerenze che crescono. |
| **2. Input** | `flows` (tutti gli output di dm:flows, fonte primaria) · `domain_model` (entità navigabili) · `personas` (mental model) · `brief` (vincoli piattaforma) · `platform` (mobile/desktop/responsive). |
| **3. Output** | `docs/design/ia/YYYY-MM-DD-ia-map-01.md` a 7 sezioni: (1) inventario oggetti navigabili, (2) navigation model, (3) content types, (4) labeling system, (5) routing/URL, (6) stati globali, (7) findability gaps. |
| **4. Comportamento** | Deduci la struttura da tutti i flow + domain-model → presenta sezione per sezione → verifica/correggi (modello dm:flows). Modalità guided/yolo/headless. Grounding corpus via dm:research-corpus (degrada a scansione diretta). Gate soft di necessità (bassa sotto 2-3 flow). |
| **5. Regole load-bearing** | (1) Limite predittivo: la skill non predice il mental model reale → findability gaps come ipotesi, non verdetti, con test reale proposto (tree/card sorting ~5 utenti). (2) Completezza domain-model: ogni entità navigabile ha un entry, altrimenti gap di prodotto. (3) Mobile vs desktop: navigation model può divergere per breakpoint. (4) Feedback ai flow su incongruenze IA↔flow (l'IA non riscrive i flow). |
| **6. Handoff** | Upstream: dm:flows (primario), dm:domain-modeler, personas, brief. Downstream: dm:prototype (navigation+routing = layer strutturale), dm:spec (content type model → PRD), dm:flows (feedback su incongruenze). |
| **7. Tipo e collocazione** | atom-dominio, domain: design, category: information-architecture. Collocata in `.claude/skills/dm-ia/` come comando dm-* (come dm:flows), NON in `dome/`. |

**Anti-duplicazione.** `dm:flows` modella il singolo task (step, screen inventory di una feature);
`dm:domain-modeler` modella le entità; `dm:product` materializza l'app-shell multi-flow a valle.
Nessuna skill costruisce oggi il *navigation model / content type / labeling / routing* che connette i
flow prima del prototipo. `dm:ia` occupa lo spazio flows→prototype.
