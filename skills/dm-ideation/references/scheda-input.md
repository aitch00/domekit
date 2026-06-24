# Scheda input — dm:ideation (provenienza)

Archivio della scheda a 7 aree compilata in fase di creazione (pipeline `dm:create`, modalità yolo).
Fonte di verità del bisogno: `docs/analysis/pipeline-gaps/03-ideation-concept.md` (sezione
"Skill da creare: dm-ideation") + `docs/analysis/pipeline-gaps/README.md`.

| Area | Contenuto |
|------|-----------|
| **1. Problema** | Il pipeline va da journey a flow quasi direttamente: il flow presuppone già scelta la soluzione. Manca il momento di **divergenza** — generare alternative prima di convergere. Senza ideazione, la prima idea diventa il prodotto. |
| **2. Input** | `journeys` (output dm:journeys, fonte primaria delle opportunità) · `personas` (ancoraggio al contesto utente) · `brief` (opzionale, vincoli) · `focus_area` (opzionale) · `n_ideas_per_area` (default 3-5). |
| **3. Output** | `docs/design/ideation/YYYY-MM-DD-ideation-cards-01.md`: per ogni area di opportunità un HMW statement + N idea cards (nome, descrizione orientata all'utente, pattern di design, assunzione chiave, rischio, analogia). |
| **4. Comportamento** | Deduci aree+idee dalle fonti → presenta → verifica/correggi (modello dm:journeys). 10 pattern di divergenza. Modalità guided/yolo/headless. Grounding corpus via dm:research-corpus (degrada a scansione diretta). |
| **5. Regole load-bearing** | (1) Divergenza pura: non valuta/scarta/sceglie — è dm:concept a convergere; mischiarli = convergenza prematura (anti-pattern centrale). (2) HMW come gate: se il pain point non si riformula in HMW sensato, dichiaralo. (3) Diversità > quantità: il self-check verifica la varietà dei pattern, non il numero. |
| **6. Handoff** | Upstream: dm:journeys, dm:personas, brief. Downstream: dm:concept (consuma le idea cards per convergere). |
| **7. Tipo e collocazione** | atom-dominio, domain: design, category: ideation. Collocata in `.claude/skills/dm-ideation/` come comando dm-* (come dm:journeys/dm:flows), NON in `dome/`. |

**Anti-duplicazione.** Nessuna skill esistente copre la divergenza: `dm:variants` diverge sulla
*struttura di una schermata* a estetica/soluzione invariata (a valle, sul prototipo); `dm:brainstorm`
facilita sessioni creative generiche ma non è ancorato a journey/pain point né produce idea cards
strutturate per pattern. `dm:ideation` occupa lo spazio journey→concept, prima del flow.
