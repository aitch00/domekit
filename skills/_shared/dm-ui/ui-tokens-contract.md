# UI Tokens Contract — il contratto-token (unica fonte di verità visiva)

> Contratto condiviso L3 (05-principio-database §Contratti condivisi). **Produttori**: `dm:ui-styles` (lookup parametrico), design-context manuali/Figma, `tokens.neutral.css` (fallback). **Consumatore**: `dm:product` (e futuri materializer). Promosso da `dm-product/references/ui-tokens.md` il 2026-06-10 (C2) quando è nato il primo produttore conforme.

Regola fondante della coerenza visiva di `dm:product`: **ogni** colore, spaziatura, raggio e valore
tipografico vive come **variabile CSS** in `styles/tokens.css`. I componenti referenziano `var(--…)`,
**mai** un valore letterale. Cambiare l'estetica = cambiare i token, non i componenti.

> Distillato (spirito, non stack) da una reference di progetto Tailwind v4 `@theme`. Qui l'output è
> **statico no-build**: niente Tailwind, niente `@theme`, niente classi generate — solo CSS custom
> properties native.

## Questo file È il contratto, non il produttore

`dm:product` dipende da **questo contratto-token**, non da chi lo produce. Un'estetica è "valida" se
popola il set di variabili dichiarato sotto. I produttori sono **intercambiabili**:

- il `design_context` prodotto da `dome:design-context` (brand reale),
- un export da Figma o un design-system esistente,
- un file scritto a mano,
- il **baseline neutro** `tokens.neutral.css` (fallback di default).

Conseguenza: deprecare o sostituire qualunque skill produttrice **non rompe** `dm:product`. Il legame è
al *contratto*, mai al produttore. L'implementazione di riferimento del contratto è
`references/tokens.neutral.css` (è anche il fallback quando manca `--design`).

## Il set di variabili (contratto minimo)
Ogni `styles/tokens.css` — neutro o brand — DEVE definire in `:root` almeno questo set (vedi
`tokens.neutral.css` per i valori neutri di riferimento):
```css
:root {
  /* Tipografia */
  --font-sans: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif; /* sovrascritto da design_context */
  --text-xs: 12px;  --text-sm: 14px;  --text-base: 16px;  --text-lg: 19px;  --text-xl: 30px;
  --leading-tight: 1.2; --leading-normal: 1.5;
  --weight-regular: 400; --weight-medium: 500; --weight-semibold: 600; --weight-bold: 700;

  /* Spacing (scala coerente) */
  --space-1: 4px; --space-2: 8px; --space-3: 12px; --space-4: 16px; --space-6: 24px; --space-8: 32px;

  /* Raggi */
  --radius-sm: 4px; --radius-md: 8px; --radius-lg: 12px; --radius-xl: 16px; --radius-full: 9999px;

  /* Superfici e bordi (neutri di default) */
  --color-background: #f6f7fb;
  --color-surface: #ffffff;
  --color-surface-secondary: #f9fafb;
  --color-border: #e7eaf3;

  /* Testo */
  --color-text-primary: #101828;
  --color-text-secondary: #6a7282;
  --color-text-muted: #99a1af;

  /* Accento + semantici (default neutri; brand → da design_context) */
  --color-accent: #4b5563;          /* neutro: grigio; brand reale lo sovrascrive */
  --color-accent-foreground: #ffffff;
  --color-success: #10b981;
  --color-info: #3b82f6;
  --color-warning: #f59e0b;
  --color-error: #ef4444;

  /* Tint semantici (badge/pillole di stato): sfondo + testo */
  --color-success-tint-bg: #e7f7ef; --color-success-tint-fg: #0a7a4f;
  --color-warning-tint-bg: #fdf3e3; --color-warning-tint-fg: #9a6206;
  --color-error-tint-bg: #fdeaea;   --color-error-tint-fg: #b42318;

  /* Ombre */
  --shadow-card: 0 1px 3px rgba(0,0,0,.1), 0 1px 2px -1px rgba(0,0,0,.1);
}
```

## Come dm:product produce `styles/tokens.css`
- **Senza `--design`** → **copia** `references/tokens.neutral.css` in `styles/tokens.css`. Deterministico,
  identico a ogni run (nessuna rigenerazione AI del neutro → niente slop).
- **Con `--design <path>`** → parte dallo stesso set di variabili e **popola i valori** con quelli del
  design_context (palette, type scale, radius, font). La struttura dei componenti non cambia: cambia solo
  il contenuto di `tokens.css`.

In entrambi i casi `styles/tokens.css` resta l'**unico** file con valori grezzi; tutti gli altri CSS usano
solo `var(--…)`.

## Mappatura token → uso (orientativa)
| Elemento | Token |
|---|---|
| Sfondo pagina | `--color-background` |
| Card / superficie | `--color-surface` |
| Bordo default | `--color-border` |
| Heading / testo primario | `--color-text-primary` |
| Label / testo secondario | `--color-text-secondary` |
| Placeholder / muted | `--color-text-muted` |
| Azione primaria | `--color-accent` + `--color-accent-foreground` |
| Stati (ok/info/warn/err) | `--color-success` / `--color-info` / `--color-warning` / `--color-error` |

## Invarianti
- Mai un hex/px grezzo nei componenti — sempre `var(--…)`.
- Un solo punto di verità: se un valore serve in due posti, è un token, non una ripetizione.
- La scala (spacing, type, radius) è discreta: usa i gradini definiti, non valori arbitrari.
- I colori di stato (es. score-bar) derivano da token semantici, mai da hex inline.

## Versione corrente

- Versione: `0.1.0`
- Stato: in stabilizzazione

### Evolution log

| Versione | Data | Tipo | Motivazione |
|---|---|---|---|
| 0.1.0 | 2026-06-10 | initial | Promozione a contratto condiviso (C2): il set di variabili era la reference privata di dm-product; con la nascita di dm:ui-styles (primo produttore) il legame produttore↔consumatore passa per il contratto. Set di variabili invariato rispetto alla reference originaria. |
