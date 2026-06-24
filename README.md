# Domekit

Skill del **dome-method** (Domekit) per discovery, design e prototipazione di
prodotti digitali — **agnostiche**: funzionano su Claude Code, Cursor, Codex,
OpenCode, Gemini, Copilot e altri agenti. Il repo contiene le `skills/` (con
contratti e database in `skills/dome-shared/`), l'indice `SKILLS.md` e i manifest
plugin in `.claude-plugin/`. Le skill sono autocontenute.

## Installazione (agnostica — consigliata)

Con il CLI [`skills`](https://www.skills.sh/) (richiede Node.js 18+). Rileva
l'agente installato e mette le skill nella cartella giusta:

```bash
npx skills add aitch00/domekit
```

Aggiornamento:

```bash
npx skills update
```

Opzioni utili: `-a claude-code` (mira un agente specifico), `-g` (install
**globale** invece che nel progetto), `-y` (non interattivo, per CI).

## Installazione come plugin (scorciatoia per Claude Code)

Solo su Claude Code, in alternativa, puoi installarlo come plugin (globale, con
update che rimuove anche ciò che sparisce a monte):

```
/plugin marketplace add aitch00/domekit
/plugin install domekit
/plugin update domekit
```

---

Indice completo delle skill: [`SKILLS.md`](./SKILLS.md).
Storia dei rilasci per versione: [`CHANGELOG.md`](./CHANGELOG.md).
