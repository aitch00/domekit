# Cognitive Dimensions

4 dimensioni per profilare come una persona elabora le informazioni e prende decisioni.

---

## Decision Style (0–10)

Come la persona affronta scelte e problem solving.

| Score | Label | Descrizione |
|-------|-------|-------------|
| 0–2 | Strongly Intuitive | Gut feeling, riconoscimento di pattern, decisioni rapide. System 1 dominante (Kahneman). |
| 3–4 | Leaning Intuitive | Prevalentemente intuitivo, verifica occasionalmente con dati. Tollera l'ambiguità. |
| 5 | Balanced | Alterna intuizione e analisi in base alle stakes. Euristiche per basso rischio, analisi per alto rischio. |
| 6–7 | Leaning Analytical | Preferisce decisioni data-driven. Cerca evidenze prima di impegnarsi. |
| 8–10 | Strongly Analytical | Richiede dati completi prima di decidere. System 2 dominante. |

**UX implication:** Basso → smart defaults, ridurre il carico decisionale. Alto → dati comparativi, specifiche dettagliate, valutazione side-by-side.

---

## Cognitive Capacity (0–10)

Bandwidth della working memory — quante informazioni si possono processare simultaneamente.

| Score | Label | Descrizione |
|-------|-------|-------------|
| 0–2 | Low | 4–5 chunks. Sopraffatto da interfacce complesse. Ha bisogno di progressive disclosure. |
| 3–4 | Below average | 5–6 chunks. Gestisce complessità moderata con struttura chiara. |
| 5 | Average | ~7 chunks (baseline Miller's Law). Comfortable con interfacce standard. |
| 6–7 | Above average | 7–8 chunks. Naviga dashboard complesse e layout multi-panel. |
| 8–10 | High | 9+ chunks. Power user: dati densi, keyboard shortcuts, filtri avanzati. |

**UX implication:** Basso → contenuti in 3–5 item, flow multi-step, opzioni visibili minime. Alto → feature power-user, layout densi, operazioni batch.

---

## Attention (0–10)

Quanto a lungo e profondamente la persona si concentra su un task.

| Score | Label | Descrizione |
|-------|-------|-------------|
| 0–2 | Scattered | Focus <2 minuti. Context switching frequente. Facilmente distratto da notifiche. |
| 3–4 | Short attention | Focus 2–5 minuti. Ha bisogno di feedback frequente e progress indicator. |
| 5 | Moderate | Focus 5–10 minuti. Completa task di lunghezza media senza interruzioni. |
| 6–7 | Sustained | Focus 10–20 minuti. Comfortable con processi multi-step. |
| 8–10 | Deep focus | Focus >20 minuti. Preferisce flow state non interrotto. |

**UX implication:** Basso → task brevi, auto-save, progress visuale, input minimo per schermata. Alto → UI flow-optimized, meno interruzioni, workflow keyboard-driven.

---

## Processing Mode (categorico)

Formato preferito per ricevere e lavorare con le informazioni.

| Modalità | Preferisce | Occupazioni tipiche |
|----------|-----------|---------------------|
| **Visual** | Diagrammi, grafici, layout spaziali, color coding, icone | Designer, architetti, artisti, data viz specialist |
| **Verbal** | Testo, istruzioni scritte, liste step-by-step, label | Ingegneri, analisti, scrittori, avvocati, contabili |
| **Kinesthetic** | Interazione hands-on, trial-and-error, drag-and-drop | Field worker, trainer, meccanici, chirurghi |

**UX implication:** Visual → grafica ricca, gerarchia visuale, organizzazione spaziale. Verbal → label chiare, UI text-heavy, flow sequenziali. Kinesthetic → onboarding interattivo, drag-and-drop, haptic feedback.

---

## Radar Chart — Derivazione

Le 5 dimensioni del radar chart si derivano dalle 4 dimensioni cognitive secondo queste formule:

| Dimensione radar | Formula | Descrizione |
|-----------------|---------|-------------|
| `radar_working_memory` | `cognitive_capacity` | Diretta |
| `radar_attention` | `attention` | Diretta |
| `radar_processing_speed` | `cognitive_capacity` | Proxy della velocità di processing |
| `radar_analytical` | `decision_style` | Diretta |
| `radar_bias_resistance` | `10 − media(bias_1_severity, bias_2_severity, bias_3_severity)` | Inversa della severità media dei bias |
| `radar_flexibility` | kinesthetic → 8 / visual → 7 / verbal → 5 | Derivata dal processing mode |

**Nota:** I valori radar sono su scala 0–10 e vanno riportati nel documento persona per generare la visualizzazione.

---

## Occupation Correlation Table

Valori tipici per occupazione. Usare come punto di partenza per la deduzione — in modalità guided i valori dedotti vanno presentati e fatti correggere; in yolo/headless si usano direttamente.

| Occupazione | Decision Style | Cognitive Capacity | Attention | Processing Mode |
|------------|---------------|-------------------|-----------|----------------|
| Software Engineer | 7–8 | 8–9 | 7–8 | Verbal |
| Graphic Designer | 4–5 | 7 | 8–9 | Visual |
| Sales Representative | 3–4 | 6–7 | 5–6 | Verbal |
| Project Manager | 6–7 | 7–8 | 6–7 | Verbal |
| Marketing Manager | 5–6 | 6–7 | 5–6 | Visual |
| Nurse / Healthcare | 5–6 | 7–8 | 7–8 | Kinesthetic |
| Teacher / Educator | 5–6 | 7 | 6–7 | Visual |
| Freelancer / Creative | 3–4 | 6–7 | 5–6 | Visual |
| Accountant / Finance | 8–9 | 8–9 | 8–9 | Verbal |
| Retail Worker | 3–4 | 5–6 | 4–5 | Kinesthetic |

**Caveat:** sono euristiche, non profili deterministici. La variazione individuale è significativa.

---

## Linee Guida per la Deduzione

Regole per derivare le dimensioni dal profilo utente durante la Discovery:

- `decision_style`: tech_level alto → più analitico; ruoli creativi → più intuitivo
- `cognitive_capacity`: occupazioni tecniche/analitiche → alto; multitasking frequente → alto
- `attention`: ambiente on-the-go → basso; contesto di lavoro focalizzato → alto
- `processing_mode`: designer/artisti → visual; ingegneri/analisti → verbal; field worker/trainer → kinesthetic
