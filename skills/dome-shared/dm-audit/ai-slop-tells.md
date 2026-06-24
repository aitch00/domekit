---
title: AI Slop Tells — Catalogo + Correzioni Canoniche
type: contract
schema_version: 1.0.0
shared_by: [dm-audit-visual, dm-audit-direction]
---

# AI Slop Tells — Catalogo + Correzioni Canoniche (libreria condivisa)

Catalogo **condiviso** delle firme dei tell visivi del generico. È la fonte unica delle
*firme CSS* riconoscibili, usata da due dimensioni con scopi diversi:

- **`dm:audit-visual`** — rilevamento **universale**: questi tell sono slop su *qualsiasi*
  artefatto, a prescindere da una direzione. (asse *artefatto*)
- **`dm:audit-direction`** — abbina gli **`anti_patterns` dichiarati** nella `design-context`
  a queste firme: se la direzione vieta "gradient text", la firma del Tell 2 è ciò che cerca.
  (asse *derivazione*) — così non re-interpreta da capo cosa significhi un anti-pattern.

Per ogni tell: come riconoscerlo nel codice (firma), perché è un tell, come correggerlo.

**Principio guida:** lo slop e' convergenza verso il default. L'antidoto e' una scelta intenzionale ancorata a una direzione esplicita.

---

## Tell 1 — Palette Generica

### Riconoscere

Pattern nel codice:
- Ciano (`#06b6d4`, `#22d3ee`) come accento primario su sfondo dark
- Gradient `from-purple-* to-blue-*` o `from-pink-* to-blue-*`
- Neon su dark (qualsiasi colore con saturazione > 80% su `#0a0a0a` / `#111`)
- Pastel lavender + cyan + coral combinati senza brand reason

### Perche' e' un tell

Sono i colori che i modelli AI "scelgono" quando non hanno direzione. Sono percepiti come "tech", "moderno", "futuristico" → finiscono ovunque → diventano default.

### Correzione

```css
/* FAIL — palette generica AI */
--accent: #22d3ee;
.cta { background: linear-gradient(135deg, #a855f7, #3b82f6); }

/* PASS — colore ancorato a brand direction */
--accent: var(--brand-primary);  /* derivato da design-context.md */
.cta { background: var(--color-primary); }  /* solid, niente gradient */
```

**Strategia:** se la direzione richiede ciano/purple, usarli con saturazione ridotta (60-75%) e non in gradient. Se la direzione e' altro, sostituire del tutto.

---

## Tell 2 — Gradient Text

### Riconoscere

```css
background: linear-gradient(...);
-webkit-background-clip: text;
background-clip: text;
color: transparent;
```

O classi Tailwind: `bg-gradient-to-r from-* to-* bg-clip-text text-transparent`.

### Perche' e' un tell

Il gradient text e' il "wow effect" che i modelli AI usano per simulare emozione. Riduce leggibilita', e' indistinguibile tra brand diversi, e' diventato firma del template generico.

### Correzione

```css
/* FAIL */
.hero-title {
  background: linear-gradient(90deg, #6366f1, #ec4899);
  -webkit-background-clip: text;
  color: transparent;
}

/* PASS — solid color con weight enfatizzato */
.hero-title {
  color: var(--color-text-emphasis);
  font-weight: var(--font-extrabold);  /* o letter-spacing tighter */
}
```

**Eccezione:** un brand specifico potrebbe richiederlo (es. piattaforma creative) → giustificarlo nella direzione, usare con parsimonia (1 elemento per pagina).

---

## Tell 3 — Side-Stripe Border

### Riconoscere

```css
border-left: 4px solid var(--color-*);
/* o */
border-right: 4px solid ...;
```

Su card, alert, callout, blockquote.

### Perche' e' un tell

E' il pattern Bootstrap-style portato al livello successivo dai modelli AI. Visivamente "shouty", crea asimmetria innaturale, riduce la pulizia del design system.

### Correzione

```css
/* FAIL */
.alert-success {
  border-left: 4px solid var(--color-success);
  padding: 16px;
  background: white;
}

/* PASS — background subtile + bordo uniforme + icona */
.alert-success {
  background: var(--color-bg-success-subtle);
  border: 1px solid var(--color-border-success);
  padding: 16px 16px 16px 48px;  /* spazio per icona */
  border-radius: var(--radius-md);
}
.alert-success::before {
  content: '';  /* icona success via background-image o inline SVG */
  /* posizionata absolute a sinistra */
}
```

**Variante valida:** background-color invece di border-left (`background: var(--color-bg-success-subtle)`).

---

## Tell 4 — Glassmorphism Decorativo

### Riconoscere

```css
backdrop-filter: blur(...);
background: rgba(255, 255, 255, 0.1-0.3);
border: 1px solid rgba(255, 255, 255, 0.2);
```

Applicato a card, sidebar, header senza funzione di "vedere attraverso".

### Perche' e' un tell

Il glassmorphism funziona quando c'e' contenuto sottostante che vale la pena intuire (overlay su immagine, modal su contenuto). Decorativo (su sfondo solido o gradient) e' puro effetto, non comunica nulla.

### Correzione

```css
/* FAIL — glass su sfondo solido */
.card {
  background: rgba(255, 255, 255, 0.08);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.12);
}

/* PASS — background solido con elevation chiara */
.card {
  background: var(--color-bg-elevated);
  border: 1px solid var(--color-border-subtle);
  box-shadow: var(--shadow-sm);
}
```

**Quando glassmorphism e' valido:** modal su contenuto sottostante, navbar fissa che lascia intravedere lo scroll, overlay su immagine.

---

## Tell 5 — Font Reflex

### Riconoscere

`font-family` o classi che includono:
- Inter, Roboto, Plus Jakarta Sans, DM Sans, Outfit
- Syne, Space Grotesk, Manrope
- Poppins (per "playful"), Montserrat (per "elegante")

### Perche' e' un tell

Sono i font che i modelli AI suggeriscono quando non sanno cosa usare. Tutti professionali, tutti corretti, tutti uguali. Convergenza al default.

### Correzione

Dipende dalla direzione (`design-context.md`):
- **Editorial / serious:** Sostituire con un serif (Source Serif Pro, IBM Plex Serif, Newsreader)
- **Personality / playful:** Display font con carattere (Fraunces, Cabinet Grotesk, Marfa)
- **Minimalist / luxury:** Geometric sans con dettaglio (Söhne, ABC Diatype, Söhne Mono)
- **Technical / data:** Mono + sans pairing (Berkeley Mono + Aktiv Grotesk)

```css
/* FAIL — Inter per default */
body { font-family: 'Inter', sans-serif; }

/* PASS — pairing con razionale */
body { font-family: 'Newsreader', Georgia, serif; }
.ui { font-family: 'IBM Plex Sans', sans-serif; }
```

**Nota:** se il progetto e' a budget zero e Inter e' l'unica scelta licensa-free disponibile, mitigare con: weight uncommon (Inter Tight, weight 300/700 invece di 400/600), letter-spacing custom, dettagli tipografici (OpenType features).

---

## Tell 6 — Grid di Card Identiche

### Riconoscere

```jsx
<div className="grid grid-cols-3 gap-6">
  {items.map(item => (
    <Card key={item.id} {...item} />  // tutte le card con stessa taglia/struttura
  ))}
</div>
```

Card con icon + heading + body uniformi, in griglia regolare.

### Perche' e' un tell

E' il template feature-grid che ogni landing page AI-generated produce. Comunica nulla: tutte le card hanno la stessa importanza, nessuna gerarchia, nessuna narrativa.

### Correzione

```jsx
/* PASS — gerarchia visiva */
<div className="grid grid-cols-3 gap-6">
  <Card variant="featured" colSpan={2} /> {/* card piu' grande, primo focus */}
  <Card variant="standard" />
  <Card variant="standard" />
  <Card variant="standard" />
  <Card variant="cta" />              {/* call-to-action diversa */}
</div>
```

**Strategie:** mix di taglie (1×2, 2×1, 1×1), una card "hero" che rompe il pattern, sequencing narrativo (card 1 = problema, card 2 = soluzione, card 3 = risultato).

---

## Tell 7 — Hero Metric Templato

### Riconoscere

```jsx
<div className="hero-metric">
  <span className="text-6xl font-bold gradient-text">99.9%</span>
  <span className="text-sm text-gray-500">uptime</span>
</div>
```

Big number + small label, spesso con gradient text e percentuale.

### Perche' e' un tell

E' il pattern "trust badge" che ogni SaaS landing AI-generated usa. Numeri impressionanti senza contesto, gradient per "rendere wow".

### Correzione

```jsx
/* PASS — contesto narrativo */
<div className="hero-stat">
  <p className="text-base">
    Negli ultimi 90 giorni, abbiamo processato
    <strong className="text-3xl tabular-nums"> 12.4M </strong>
    transazioni con un uptime del
    <strong className="tabular-nums"> 99.97% </strong>
    — meno di 4 minuti di downtime totale.
  </p>
</div>
```

**Strategie:** numeri come parte di una frase, contestualizzazione concreta (cosa rappresenta?), niente gradient, weight tipografico per enfatizzare.

---

## Tell 8 — Dark Mode con Glow Accent

### Riconoscere

```css
.button {
  background: var(--accent);
  box-shadow: 0 0 20px var(--accent);  /* o filter: drop-shadow(...) */
}
```

Su sfondo scuro, accent con glow/halo luminoso.

### Perche' e' un tell

Il glow simula "energy" / "futuristic" senza ragione funzionale. E' il default visivo dei mockup AI per dark mode → diventa cliché.

### Correzione

```css
/* FAIL */
.cta-dark {
  background: #06b6d4;
  box-shadow: 0 0 24px rgba(6, 182, 212, 0.5);
}

/* PASS — contrasto via tono, niente glow */
.cta-dark {
  background: var(--color-primary);  /* es. #f5f5dc su dark per contrasto caldo */
  border: 1px solid var(--color-border-emphasis);
}
```

**Strategia dark mode:** contrasto via differenza di tono (non di luce), saturazione ridotta, weight tipografico piu' marcato per gerarchia.

---

## Tell 9 — Spacing/Radius/Shadow Generici

### Riconoscere

Valori "famosi": `padding: 16px`, `border-radius: 8px`, `box-shadow: 0 4px 6px rgba(0,0,0,0.1)` ovunque, identici per ogni componente.

### Perche' e' un tell

Sono i default Tailwind/Bootstrap. Funzionali ma indistinguibili tra prodotti diversi.

### Correzione

Almeno **una scelta intenzionale per progetto**:
- Radius distintivo (es. `--radius-card: 4px` per editorial, `20px` per friendly, `0` per brutalist)
- Shadow sottile e custom (es. shadow ambient + key separati invece del default Tailwind)
- Spacing scale custom (es. base 6 invece di 4 per density, oppure base 10 per arieggiato)

```css
/* PASS — scelte intenzionali documentate in design-context.md */
:root {
  --radius-card: 2px;          /* editorial, sharp */
  --shadow-card: 0 1px 0 rgba(0,0,0,0.04), 0 8px 24px -12px rgba(0,0,0,0.08);
  --space-base: 6px;           /* density piu' alta */
}
```

---

## Tell 10 — "Modern" Typography Mismatch

### Riconoscere

- Heading con `font-weight: 700` e display font geometrico (cliché "modern SaaS")
- Body con `line-height: 1.5` neutrale e `letter-spacing: 0`
- Niente OpenType features (no `tabular-nums`, no `ss01`, no contextual alternates)

### Perche' e' un tell

E' il setup default che ogni framework propone. Funziona ma non comunica.

### Correzione

- Heading: weight non standard (300 light + tight letter-spacing per editorial, 800/900 per impatto)
- Body: line-height ottimizzato per leggibilita' (1.6 per testo lungo, 1.4 per UI density)
- OpenType: `font-feature-settings: "ss01", "tnum"` per dettagli professionali
- Optical sizing dove disponibile (Inter Variable, Newsreader)

```css
/* PASS — tipografia con dettaglio */
.heading {
  font-family: 'Newsreader', serif;
  font-weight: 300;
  letter-spacing: -0.02em;
  font-feature-settings: "ss01", "lnum";
}
.body {
  font-family: 'Inter Tight', sans-serif;
  line-height: 1.55;
  font-feature-settings: "cv11", "ss03";
}
.metric {
  font-feature-settings: "tnum";  /* numeri tabulari */
}
```

---

## Decision Matrix: Fix Inline vs Skip

Quando un tell richiede correzione:

| Situazione | Azione |
|-----------|--------|
| Fix locale (CSS/className) senza decisione di brand | Fix inline in Stage 3 |
| Fix richiede sostituzione font con licensa | Skip → segnalare per `dome:typeset` |
| Fix richiede ridisegno palette completa | Skip → segnalare per `dome:colorize` |
| Fix richiede ristrutturazione layout | Skip → segnalare per `dome:layout-improve` |
| Fix richiede decisione di brand non chiara | Skip → segnalare nel report come "richiede direzione" |

---

## Files Sorgente

- `methodologies/00-foundations/anti-ai-slop.md`
- `methodologies/02-ux-evaluation-and-critique/heuristic-critique.md` (sezione AI Slop Detection)
- `methodologies/04-visual-design-and-polish/`
- `dome/design-context/references/` (per Brand Personality / Aesthetic Direction)
