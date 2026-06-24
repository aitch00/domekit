# Domekit — indice delle skill

_9 skill. Generato da `tools/build-dome-method.py`._

| Skill | Descrizione |
|---|---|
| `dm:concept` | Converge le idea cards di dm:ideation in 2-3 concept di prodotto alternativi, strutturati, con trade-off espliciti, e seleziona quello da sviluppare con una decisione documentata. Un concept è l'approccio ad alto livello — non un flow, non un PRD. Usa dopo dm:ideation, prima di dm:flows. |
| `dm:domain-modeler` | Modella il dominio applicativo producendo un entity-relationship model con Mermaid erDiagram. Deduce le entità dai contenuti condivisi (brief, brainstorm, flows, glossario) e le fa verificare a gate. Usa pre-sviluppo per allineare design e sviluppo sul vocabolario condiviso. |
| `dm:flows` | Documenta user flow con diagramma Mermaid e analisi cognitiva automatica (Hick's Law, Miller 7±2, context switching). Deduce l'happy path dai contenuti condivisi (journeys, personas, domain-model, brief) e lo fa verificare. Usa per mappare come gli utenti usano il prodotto. |
| `dm:ia` | Costruisce l'architettura informativa che connette i flow del prodotto: inventario degli oggetti navigabili, navigation model, content types, labeling, routing/URL, stati globali e findability gaps. Deduce la struttura dall'insieme dei flow e la fa verificare. Usa quando il prodotto supera i 2-3 flow, prima di dm:prototype. |
| `dm:ideation` | Genera soluzioni alternative divergenti a partire dai pain point/opportunità delle journey, lungo pattern di design diversi (self-service vs guidato, push vs pull, gamification, analogia…). Riformula i pain point in HMW e produce idea cards. Pura divergenza: non valuta né sceglie — quello è dm:concept. Usa dopo le journey, prima di convergere sul concept. |
| `dm:journeys` | Mappa customer journey con analisi Peak-End (Kahneman) e Sentiment Timeline. Deduce le fasi dai contenuti condivisi (personas, brief, flows) e le fa verificare. Usa dopo aver definito le personas per mappare l'esperienza vissuta dagli utenti. |
| `dm:personas` | Crea user personas con profilo cognitivo (4 dimensioni, bias, radar chart). Deduce le personas dai contenuti condivisi (brief, brainstorm, journeys) e le fa verificare. Usa per definire gli utenti target prima del design di flow e journey. |
| `dm:prototype` | Materializza un flow in un prototipo HTML navigabile con dati mock, per vedere il flow funzionare prima di costruirlo. Consuma un flow (e opzionalmente un domain-model) e produce un prototipo apribile in browser. Usa dopo dm:flows per validare un percorso o preparare un walkthrough cliente. |
| `dm:revise` | Rivede un flow editando il suo prototipo: sessione guidata in cui chiedi modifiche, le vedi applicate all'HTML, e mentre le applica le registra mappate a schermata/step. Su 'soddisfatto' esporta un changeset e lo fa assorbire dal flow (via dm:flows ingest). Usa per raffinare un flow vedendo le modifiche, non riscrivendo l'md a mano. |
