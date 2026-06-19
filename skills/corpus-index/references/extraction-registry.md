# Registro di estrazione — `type → kind → dove sono gli elementi`

Per ogni `type` di handoff-artifact: dove vivono gli elementi consumabili e dove sta l'anchor.
È il dato che rende l'atomo **universale**: un nuovo `type` aggiunge una riga, il meccanismo non cambia.
A regime gli anchor sono inline (lettura uniforme); il legacy si estrae con le euristiche qui sotto e si
può fare `--stamp`.

| type | role | elementi (kind) | dove stanno | carrier dell'anchor |
|------|------|------------------|-------------|---------------------|
| flow | single | step (`s`) | righe della tabella **Happy Path** | colonna `id` (`s.<slug>`) |
| flow | single | branch (`b`) | Percorsi Alternativi / rami del diagramma | `{#b.<slug>}` sul titolo |
| flow | single | red-flag (`rf`) | sezione **Cognitive Analysis** (`⚠ … — Step N`) | `{#rf.<slug>}` |
| flow | overview | finding (`finding`) | heading H3 sotto **Cross-Flow Cognitive Findings** | `{#finding.<slug>}` |
| journey | single | fase (`p`) | righe della tabella **Journey Map** | colonna `id` (`p.<slug>`) |
| journey | single | recommendation (`r`) | lista numerata sotto **Design Recommendations** | `{#r.<slug>}` |
| journey | overview | finding (`finding`) | heading H3 (Cross-Journey Insights) | `{#finding.<slug>}` |
| persona | single | bias (`bias`) | righe di **Key Biases** | colonna `id` (`bias.<slug>`) |
| persona | single | dimensione cognitiva (`cog`) | righe di **Cognitive Profile** | colonna `id` (`cog.<slug>`) |
| persona | single | tratto (`trait`) | `**Goals/Pain points/Behaviors:**` | `{#trait.<slug>}` |
| persona | overview | finding (`finding`) | heading H3 | `{#finding.<slug>}` |
| domain-model | single | entità (`e`) | nodi dell'erDiagram + tabella entità | nome entità (`e.<nome>`) |
| domain-model | single | relazione (`rel`) | righe relazioni / archi erDiagram | `rel.<A>-<B>` |
| domain-model | single | termine glossario (`g`) | tabella **Business Glossary** | colonna `id` (`g.<slug>`) |
| brief / distillate | single | requisito (`req`) | heading di sezione + bullet | `{#req.<slug>}` su heading; id su bullet |

## Menzioni grezze di schermata
Oltre agli elementi, estrai le **menzioni di schermata** (es. colonna *Schermata* dei flow, *Touchpoint*
dei journey). Non sono elementi indicizzati con anchor: sono **input grezzo** per l'elenco-pagine che il
consumatore (es. `dm:blueprint`) sintetizza a valle. Vanno nell'output come lista a parte.

## Regola di grana
In dubbio, **indicizza di più** (un bullet ambiguo → elemento). Vedi SKILL §Regole: over-indicizzare è
sicuro, under-indicizzare è perdita silenziosa. La consolidazione di grana è del consumatore, non
dell'indice.

## Type ignoto
Un `type` non in tabella → bucket `unclassified`: indicizza comunque a grana di heading/bullet e
**segnala** nel report. Non scartare.
