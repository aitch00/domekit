# Domekit — plugin

Plugin installabile delle skill del **dome-method** (Domekit). Questo repo è un
**marketplace di plugin**: contiene `.claude-plugin/` (`marketplace.json` +
`plugin.json`), le `skills/` (con contratti e database in `skills/dome-shared/`)
e l'indice `SKILLS.md`. Le skill sono autocontenute.

## Installazione (plugin — consigliata)

Installato come plugin, Domekit vive nello store globale del tuo agente: le skill
sono invocabili in **qualsiasi progetto** senza copiarne i file nel repo.

**Claude Code:**

```
/plugin marketplace add aitch00/domekit
/plugin install domekit
```

Aggiornamento (sincronizza davvero: aggiunge, aggiorna e rimuove ciò che non c'è più):

```
/plugin update domekit
```

## Installazione alternativa (set flat via npx)

Se preferisci copiare le skill dentro il progetto (Node.js 18+):

```bash
npx skills add aitch00/domekit
npx skills update
```

Le skill atterrano nella cartella convenzionale dell'agente (`.claude/skills/`
per Claude Code, `.agents/skills/` per Cursor/Codex/Copilot).

Indice completo delle skill: [`SKILLS.md`](./SKILLS.md).
Storia dei rilasci per versione: [`CHANGELOG.md`](./CHANGELOG.md).
