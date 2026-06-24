# Finding Schema — il contratto fondante dell'ecosistema audit

Reference **condivisa** (L3) fra gli atomi-dimensione (`dm:audit-a11y`, `dm:audit-perf`,
`dm:audit-visual`, `dm:audit-ux`), il composto orchestratore (`dm:audit`) e il composto di
correzione (`dm:refine`). Definisce **una sola cosa**: come si descrive un *finding* e come si
aggrega in un *audit-report*. È il perno che permette a audit diversi di toccare gli stessi atomi
e a `dm:refine` di consumare qualunque audit senza sapere chi l'ha prodotto.

> **Regola d'oro:** un atomo-dimensione **non inventa** il proprio formato. Emette `findings[]`
> conformi a questo schema. Se un campo non si applica, lo omette — non lo rinomina.

## Il finding (oggetto atomico)

```yaml
- dimension: a11y              # slug della dimensione che ha prodotto il finding
  severity: P0                 # P0 | P1 | P2 | P3  (vedi rubrica)
  location: "header > nav"     # dove: schermata / step / selettore CSS / riga
  problem: "I link di nav sono <div> cliccabili, non raggiungibili da tastiera."
  recommendation: "Sostituisci con <a>/<button> nativi."
  effort: S                    # S | M | L  (vedi rubrica)
  evidence: "index.html:142"   # opzionale: citazione puntuale (path:riga, selettore, screenshot)
  degraded: false              # opzionale: true se è un finding informativo di degrado REF
```

Cinque campi sono **obbligatori**: `dimension`, `severity`, `location`, `problem`,
`recommendation`. `effort` è obbligatorio per i finding correggibili (serve a `dm:refine` per
ordinare i fix). `evidence` e `degraded` sono opzionali.

Un finding senza `location` o senza `recommendation` è **rifiutato**: l'audit produce riscontri
*azionabili*, non giudizi vaghi. "Migliorerei la UX" non è un finding.

## Severità (impact)

| Severità | Criterio | Effetto su `dm:refine` |
|----------|----------|------------------------|
| **P0** blocker | Falsa lo scopo dell'artefatto: percorso interrotto, controllo non raggiungibile, contenuto inaccessibile | Bloccante: la convergenza richiede zero P0 |
| **P1** grave | Degrada seriamente affidabilità/uso: nessuna visibilità di stato, heading non semantici, dato incoerente | Bloccante: la convergenza richiede zero P1 |
| **P2** medio | Difetto reale non bloccante: spacing incoerente, empty-state assente su ramo secondario | Quick-win se effort basso, altrimenti opzionale |
| **P3** minore | Nit/polish o **finding informativo di degrado** (`degraded: true`): micro-inconsistenze, perf marginale | Non bloccante, non entra nello score |

## Sforzo (effort)

| Effort | Criterio |
|--------|----------|
| **S** | edit puntuale, < 15 min (attributo, label, link, classe) |
| **M** | riorganizzazione di una schermata o di un pattern ripetuto |
| **L** | ripensare struttura o rientrare in un artefatto upstream (rigenerazione) |

`effort` è il segnale che `dm:refine` usa per decidere il **meccanismo di fix** (patch chirurgico
vs rigenerazione): vedi `dm-refine/references/refine-loop.md`.

## Registro delle dimensioni

| Slug | Nome | Classe | Atomo invocabile? | Sorgente richiesta |
|------|------|--------|-------------------|--------------------|
| `a11y` | Accessibilità | SELF | ✅ `dm:audit-a11y` | solo l'artefatto |
| `perf` | Performance | SELF | ✅ `dm:audit-perf` | solo l'artefatto (analisi statica) |
| `visual` | Visual-Polish | SELF | ✅ `dm:audit-visual` | solo l'artefatto |
| `ux` | UX-Heuristic | SELF | ✅ `dm:audit-ux` | solo l'artefatto |
| `flow-fidelity` | Fedeltà al flow | REF | ❌ locale (1 consumatore) | `source_flow` |
| `mock-coherence` | Coerenza mock | REF | ❌ locale (1 consumatore) | `domain-model` |
| `direction` | Fedeltà alla direzione | REF | ✅ `dm:audit-direction` | `design-context` + `tokens.css` |
| `validation` | Validazione con utenti | EMP | ✅ `dm:validate` (ingest) | sessioni reali (note/registrazioni) |

- **SELF** = serve solo l'artefatto → gira sempre, anche su input non-dome. Le 4 SELF sono **atomi
  invocabili** (riuso ≥2 audit): vivono come skill `dm:audit-<slug>` standalone e dispatchabili.
- **REF** = serve un handoff a monte → **degrada con nota** (finding `degraded: true`, P3) se la
  sorgente manca, non blocca. Le REF restano **locali** al target che le usa finché non emerge un
  secondo consumatore (test del riuso, `03-principio-atomi`); allora si promuovono ad atomo.
  `direction` è la prima REF promossa ad **atomo dall'inizio**: il riuso è *intrinseco* (ogni
  artefatto renderizzato ha una direzione), non emergente. È anche l'unica dimensione sull'asse
  **derivazione** (fedeltà alla direzione dichiarata, `design-foundations/02-fedelta-e-craft`),
  mentre le SELF stanno sull'asse *artefatto* (qualità intrinseca).
- **EMP** = empirica: il finding nasce da **osservazione di utenti reali**, non da euristica.
  L'`evidence` è obbligatoria (citazione/osservazione di sessione + n. sessioni che convergono).
  Un finding EMP con una sola sessione a supporto resta P3-informativo finché non converge (≥2).
  I finding EMP correggibili sull'artefatto sono consumabili da `dm:refine` come gli altri;
  gli insight di processo (revisioni a journey/flow) NON sono finding e viaggiano a parte.

## L'audit-report (handoff aggregato)

`dm:audit` aggrega i `findings[]` di tutte le dimensioni eseguite in un unico handoff markdown,
consumato da `dm:refine`. Frontmatter canonico (`02-principio-handoff`) + estensioni:

```yaml
---
title: Audit — [target-slug]
date: [YYYY-MM-DD]
type: audit-report
status: complete                 # l'audit È completo anche se trova problemi
process: dm-audit
produced_by: dm:audit@[version]
produced_at: [ISO-8601]
principle_versions: atomi@1.0.0, handoff@1.0.0
upstream: [path del target auditato]
consumed_by: dm:refine
target: [path artefatto]
target_type: [prototype | other]
dimensions_run: [a11y, perf, visual, ux, flow-fidelity]
dimensions_skipped: [mock-coherence]    # con motivo nel corpo
score: [0-100]
issues: { p0: [n], p1: [n], p2: [n], p3: [n] }
weak_sections: ["domain-model assente: coerenza mock non verificata" | "nessuno"]
next_step: [azione consigliata in linguaggio naturale]
---
```

Corpo: tabella riepilogo per dimensione (score parziale, n. finding), lista `findings[]`
prioritizzata (P0 → P3), sezione **"what's working"** (2–3 cose fatte bene), motivo dei degradi REF.

## Scoring (0–100)

Due livelli, per non confondere "quanto è grave una dimensione" con "quanto è pronto l'artefatto".

**Score di dimensione** — lo calcola ogni atomo/dimensione sui *propri* finding (i `degraded: true`
non entrano):
```
score_dim = max(0, 100 − 25·P0 − 10·P1 − 4·P2 − 1·P3)
```

**Score aggregato** — lo calcola `dm:audit` per l'audit-report, come **media degli score delle
dimensioni eseguite** (escluse le degradate/saltate), arrotondata:
```
score = round( mean(score_dim su ogni dimensione eseguita) )
```
con una **regola di blocco**: se esiste *anche un solo* P0 in qualunque dimensione, lo score
aggregato è limitato a **max 60** (un bloccante = non pronto, qualunque sia la media).

Perché la media e non la somma delle penalità su un unico 100: sommare i finding di *tutte* le
dimensioni comprime lo score col solo volume di nit minori (P2/P3) sparsi, e nasconde il segnale
vero ("0 P0, struttura sana"). La media isola ogni dimensione; la regola di blocco preserva
l'intento "i bloccanti dominano". *(Learning: smoke test task-tracker 2026-06-08 — pooled 35 vs
media dimensioni 87 sullo stesso prototipo.)*

Lo score va **sempre** accompagnato dalla tabella `dimensions_run` con gli score parziali e da
`dimensions_skipped`: è relativo a cosa è stato misurato. Un numero senza la tabella → non
interpretabile, rifiutato.

## Prioritizzazione

1. Tutti i **P0** in testa (l'artefatto non serve al suo scopo finché restano).
2. Poi **quick-wins**: alto impatto × basso effort (P1/S, P1/M) prima dei P2/P3.
3. I finding informativi di degrado REF (`degraded: true`) chiudono la lista — non sono difetti.

## Esempi rifiutati (cosa NON è un finding valido)

- "Migliorerei un po' la UX" → manca dimension/location/recommendation.
- Imporre una **palette di marca** su un artefatto neutro → fuori scope (estetica, non audit).
- Score senza `dimensions_run` → non interpretabile.
- Bloccare l'intero audit perché manca una sorgente REF → le SELF devono comunque girare.

## Versione corrente

Contratto condiviso L3 (vedi `dome-method/documentation/00-foundations/05-principio-database.md`
§Contratti condivisi). Consumatori: `dm:audit-a11y`, `dm:audit-perf`, `dm:audit-visual`,
`dm:audit-ux`, `dm:audit`, `dm:refine`, `dm:validate` (dimensione EMP). I consumatori dichiarano `consumes_contracts` con
`schema_version`.

- Versione: `0.2.0`
- Stato: in stabilizzazione

### Evolution log

| Versione | Data       | Tipo    | Motivazione |
|----------|------------|---------|-------------|
| 0.2.0    | 2026-06-10 | minor   | Registrata la dimensione `validation` (classe **EMP**: empirica, da sessioni con utenti reali) con la nascita di dm:validate (piano C5). Evidence obbligatoria per i finding EMP; soglia di convergenza ≥2 sessioni per severità ≠ P3. Retrocompatibile: i consumatori >=0.1.0 restano validi. |
| 0.1.0    | 2026-06-08 | initial | Contratto fondante dell'ecosistema audit (finding + audit-report + scoring). Versione esplicitata 2026-06-10 con la formalizzazione dei contratti condivisi in `05-principio-database` 1.1.0. |
