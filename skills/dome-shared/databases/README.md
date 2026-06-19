# Shared Databases

Database CSV interrogabili dalle skill dome (in particolare dalle L4 parametriche). Forniscono lookup deterministico di pattern, palette, font pairing e linee guida per dominio/stile/tipo prodotto.

## Origine

Questi CSV derivano dal progetto open-source [`nextlevelbuilder/ui-ux-pro-max-skill`](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill), licenza MIT. Sono stati copiati in questa cartella per renderli un'autonoma fonte dati delle skill dome (non dipendiamo da percorsi esterni).

**Licenza:** MIT — Copyright (c) 2024 Next Level Builder. Vedi NOTICE.

## Database disponibili

| File | Righe | Contenuto | Skill che lo usa |
|------|-------|-----------|-------------------|
| `products.csv` | 161 | Tipi prodotto con stile raccomandato, palette focus, considerations | `dome:design-for-product-type` (L4) |
| `styles.csv` | 84 | Stili UI con keywords, palette, effects, framework compatibility | `dome:apply-style-name` (L4) |
| `colors.csv` | 161 | Palette semantiche per tipo prodotto (1:1 con products) | `dome:design-for-product-type`, `dome:colorize` |
| `typography.csv` | 73 | Font pairing con mood, best for, Tailwind config | `dome:typeset`, `dome:visual-identity`, `dome:apply-style-name` |
| `ux-guidelines.csv` | 98 | Linee guida UX con severity, do/don't, code examples | `dome:ux-critique`, `dome:accessibility-audit` |
| `charts.csv` | 25 | Tipi grafico con use cases, accessibility, library reco | `dome:domain-visual` (per dashboard) |
| `app-interface.csv` | 29 | Pattern UI per app interfaces | `dome:domain-visual` |
| `icons.csv` | 104 | Icon usage patterns | (referenza) |
| `landing.csv` | 30 | Landing page patterns | (referenza) |
| `ui-reasoning.csv` | 161 | UI reasoning per tipo prodotto | (referenza) |

## Schema reference

### `products.csv`

```
No,Product Type,Keywords,Primary Style Recommendation,Secondary Styles,
Landing Page Pattern,Dashboard Style (if applicable),Color Palette Focus,
Key Considerations
```

Esempio:
```
1,SaaS (General),"app, b2b, cloud, general, saas, software, subscription",
Glassmorphism + Flat Design,"Soft UI Evolution, Minimalism",
Hero + Features + CTA,Data-Dense + Real-Time Monitoring,
Trust blue + accent contrast,
"Balance modern feel with clarity. Focus on CTAs."
```

### `styles.csv`

```
No,Style Category,Type,Keywords,Primary Colors,Secondary Colors,
Effects & Animation,Best For,Do Not Use For,
Light Mode ✓,Dark Mode ✓,Performance,Accessibility,Mobile-Friendly,
Conversion-Focused,Framework Compatibility,Era/Origin,Complexity,
AI Prompt Keywords,CSS/Technical Keywords,
Implementation Checklist,Design System Variables
```

### `colors.csv`

```
No,Product Type,Primary,On Primary,Secondary,On Secondary,
Accent,On Accent,Background,Foreground,Card,Card Foreground,
Muted,Muted Foreground,Border,Destructive,On Destructive,Ring,Notes
```

Mapping 1:1 con `products.csv` via `Product Type`.

### `typography.csv`

```
No,Font Pairing Name,Category,Heading Font,Body Font,
Mood/Style Keywords,Best For,Google Fonts URL,CSS Import,
Tailwind Config,Notes
```

### `ux-guidelines.csv`

```
No,Category,Issue,Platform,Description,Do,Don't,
Code Example Good,Code Example Bad,Severity
```

Severity: P0 / P1 / P2 / P3.

## Come interrogarli

Le skill che usano questi database fanno **lookup deterministico** (no LLM-fuzzy):

1. **Lookup esatto** per chiave (es. `Product Type == "SaaS (General)"`)
2. **Lookup keyword** scansionando colonna `Keywords` con substring/word-match
3. **Lookup parametri multipli** combinando filtri (es. style + framework + light-mode-ok)

Esempio pseudocodice:

```python
import csv

def find_product(product_type: str) -> dict:
    with open('products.csv') as f:
        for row in csv.DictReader(f):
            if row['Product Type'].lower() == product_type.lower():
                return row
    return None

def find_palette(product_type: str) -> dict:
    with open('colors.csv') as f:
        for row in csv.DictReader(f):
            if row['Product Type'].lower() == product_type.lower():
                return row
    return None
```

Le skill L4 incapsulano questa logica di lookup.

## Aggiornare

Per aggiornare i CSV upstream:

1. `git clone https://github.com/nextlevelbuilder/ui-ux-pro-max-skill`
2. `cp ui-ux-pro-max-skill/cli/assets/data/{products,styles,colors,typography,ux-guidelines,charts,app-interface,icons,landing,ui-reasoning}.csv ./`
3. Verificare schema invariato (le skill L4 si rompono se cambiano colonne)
4. Commit con message `Update shared databases from ui-ux-pro-max vX.Y`

## Skill che dipendono da questi database

Vedi colonna "Skill che lo usa" nella tabella sopra. Riassunto:

- **L1:** `colorize` (palette per dominio), `typeset` (pairing), `style-match` (keywords stile), `domain-patterns` (convenzioni)
- **L2:** `domain-visual` (3 CSV combinati), `visual-identity` (style + typography)
- **L4:** `design-for-product-type` (entry-point parametrico), `apply-style-name` (entry-point parametrico)

## NOTICE

```
ui-ux-pro-max
Copyright (c) 2024 Next Level Builder
Licensed under MIT License

Database CSV (products, styles, colors, typography, ux-guidelines, charts,
app-interface, icons, landing, ui-reasoning) sono stati copiati e ridistribuiti
in compliance con i termini della licenza MIT. Vedi LICENSE upstream:
https://github.com/nextlevelbuilder/ui-ux-pro-max-skill/blob/main/LICENSE
```
