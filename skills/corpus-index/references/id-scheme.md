# Schema dell'anchor-ID

Lo schema è definito in via normativa in `02-principio-handoff` (sezione "Identità degli elementi
consumabili"). Qui il riassunto operativo per l'indicizzazione.

## Forma

```
<doc-id>#<kind>.<anchor>
```
- `doc-id`: dal frontmatter `id` del documento (stabile, slegato dal path). Fallback legacy: slug del
  nome file senza prefisso data.
- `kind`: tipo dell'elemento nel documento (vedi `extraction-registry.md`).
- `anchor`: slug **content-derived** dal titolo dell'elemento; doc-scoped (unico nel documento).

Esempio: `flow-approvazione#s.consulta-audit-trail` (alias-display derivabile: `F2.S4`).

## Ciclo di vita (stabilità)
- **minted-at-birth:** coniato alla creazione dell'elemento e **persistito** nel documento.
- **frozen:** non si ricalcola mai più; editare il testo NON cambia l'anchor.
- **never-renumber:** delete → ID **ritirato** (mai riusato; segnala staleness); insert → nuovo anchor;
  split → l'originale tiene l'anchor.
- **collisioni** nello stesso documento → suffisso `-2`, congelato.

## Lettura vs stamp
- **A regime:** il documento porta gli anchor inline → lettura uniforme, type-agnostic.
- **Legacy (senza anchor):** l'anchor si calcola dal contenuto; con `--stamp` lo si **riscrive** nel
  documento (idempotente: al run successivo è già presente).

## Rigenerazione anchor-preserving
È responsabilità dei **generatori** a monte (non di questo atomo): quando rigenerano un documento,
ri-agganciano gli anchor esistenti per similarità di titolo invece di rislugare. corpus-index, leggendo
gli anchor persistiti, eredita la stabilità.

## Alias-display
`<kind><n>` posizionale (es. `F2.S4`) è **derivato** e cosmetico, per la prosa leggibile dei
consumatori. **Non** è la fonte di verità: il gate e le citazioni usano l'ID-macchina.
