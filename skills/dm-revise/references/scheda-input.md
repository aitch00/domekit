# Scheda input — dm:revise (provenienza)

> Compilata in fase Discovery/Scheda della pipeline `dm:create` (run `2026-06-15-dm-revise`).
> Context-map completa: `.notes/analysis/edit-to-flow-process-spec.md` (nel repo di authoring).
> Archiviata col manifest come provenienza permanente (`01-scheda-input.md`).

## Area 1 — Identità
- **Nome:** `dm-revise` (comando `dm:revise`).
- **Famiglia:** dominio `design` → `.claude/skills/dm-revise/` (convenzione comandi dm-*).
- **Intent:** rivedere un flow editando il suo prototipo, vedendo le modifiche, senza riscrivere l'md a mano.
- **Chi la invoca / cosa produce:** utente finale; produce un `changeset-*.md` (handoff) e, via
  `dm:flows --ingest`, un `flow-*.md` aggiornato.

## Area 2 — Dominio e perimetro
- **Dominio:** design (round-trip edit→flow). **Scope-in:** catturare delta di *contratto* da una
  sessione di edit sul prototipo ed esportarli come changeset. **Scope-out:** scrivere il flow
  (è di `dm:flows` ingest); generare il prototipo (`dm:prototype`); esplorare alternative
  strutturali (`dm:variants`); validare con utenti reali (`dm:validate`).
- **Vocabolario:** changeset, delta, anchor (`sc.<slug>`/`s.<slug>`), contratto-vs-materializzazione,
  target_flow, ingest, anchor-preserving.

## Area 3 — Fasi e flusso
- **Fase canonica coperta:** fuori-fase (atomo-dominio): loop edit → export → handoff.
- **Ordine:** non sequenza-fissa multi-fase (non pipeline); non lookup CSV (non parametrica);
  trasformazione singola coerente con handoff downstream → **atomo-dominio** (vedi Area 7).
- **Output:** `changeset` (`changeset-contract@1.0.0`); effetto a valle via handoff.

## Area 4 — Prospettive
- Una sola prospettiva operativa (l'editor guidato). Nessun reviewer multi-persona → niente `agents/`.

## Area 5 — Qualità attesa
- Il changeset contiene **solo** delta di contratto (filtro decisione-vs-ritocco affidabile).
- Ogni delta è ancorato correttamente (mappa flow caricata a inizio sessione).
- Il flow risultante è anchor-preserving (garanzia dell'ingest, non di questo atomo).

## Area 6 — Variabilità
- Modalità `guided` / `yolo` / `headless` (come `dm:flows`). `--no-apply` per fermarsi al changeset.
- Materializzazione applicata all'HTML per *vedere* ma non loggata (effimera).

## Area 7 — Vincoli, riuso, harvest
- **Atomi riusati (anti-duplicazione):** nessun atomo nuovo creato (β: il decision tree esclude il
  composite a 1-atomo). **Riusa via contratto** `changeset-contract@1.0.0` e `flow-handoff-contract@1.0.0`;
  **handoff downstream** a `dm:flows` (ramo ingest, deliverable B); **upstream** `dm:prototype`.
- **Principi:** `03-principio-atomi@1.1.0`, `02-principio-handoff@1.1.0`.
- **harvest_policy:** `opt-in` su tutti e tre i piani (default atomo-dominio).
- **Vincolo forte:** editing solo *attraverso* l'atomo (mappa flow↔HTML viva in sessione); HTML
  editato fuori non supportato.
