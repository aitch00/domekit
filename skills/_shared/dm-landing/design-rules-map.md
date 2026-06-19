# Indice delle regole di design (single source of truth)

`dm-landing` **non duplica** le logiche di design: le applica leggendole dalle fonti autorevoli del
repo. Questo file è l'indice operativo. Se una regola cambia, cambia **una volta** alla fonte.

> Le fasi `dm-landing-design`, `dm-landing-render` e `dm-landing-qa` consultano questi file on-demand.

## Cervello di design (trasversale)
| Tema | Fonte |
|------|-------|
| Authority hierarchy, Modulo A (landing), pre-build plan, litmus, verifica visiva, anti-pattern | `.inspiration/plugins/compound-engineering/skills/frontend-design/SKILL.md` |

## Logiche atomiche (regole verticali)
| Logica | Fonte |
|--------|-------|
| Colore: ruoli semantici, scala 11-step, WCAG AA, dark mode | `dome/colorize/references/color-roles-and-tokens.md` (+ `dome/colorize/SKILL.md`) |
| Tipografia: pairing, type scale, OpenType | `dome/typeset/SKILL.md` |
| Layout/griglia: grid, spacing rhythm, gerarchia | `dome/layout-improve/SKILL.md` |
| Responsive: mobile-first, breakpoint, clamp, touch 44px | `dome/responsive/SKILL.md` ; coord. perf `dome/layout-responsive/references/layout-perf-coordination.md` |
| Visual polish: spacing/radius/shadow/border/states | `dome/visual-polish/references/visual-rules-checklist.md` |
| Motion: durate, easing, stagger, prefers-reduced-motion | `dome/animate/SKILL.md` ; `dome/animate/references/motion-rules-summary.md` |
| Elevazione: tonal elevation (luminanza in dark), surface+shadow accoppiati | `dome/colorize/references/color-roles-and-tokens.md` (esteso) ; `dome/design-system/references/token-architecture.md` |
| Profondità/stratificazione: assi materiale·elevazione·composizione, budget anti-slop, stacking-context | reference proprie di dm-landing (vedi sotto) |
| Microcopy: label, error, empty state, tono | `dome/microcopy/SKILL.md` |
| Accessibilità: WCAG 2.1 AA checklist | `dome/accessibility-audit/references/accessibility-checklist.md` |
| Anti-slop: 10 tell + fix canonici | `dome/anti-slop-fix/references/ai-slop-tells.md` |
| Token architecture (3 livelli) | `dome/design-system/references/token-architecture.md` |
| Brand direction (`.impeccable.md`) | `dome/design-context/references/context-template.md` |

## Database L3
| Asset | Fonte |
|-------|-------|
| Pattern di landing (27) | `../databases/landing.csv` — schema `landing.schema.yaml` v1.0.0, changelog `landing.changelog.md`; lookup per intent sulla colonna `Keywords` |

## Regola d'uso
- Non ricopiare il contenuto di questi file dentro `dm-landing`: linkarli e applicarli.
- Se una regola risultasse mancante o cambiata, aggiornare la fonte e (se serve) bumpare la skill di
  design corrispondente, non duplicare qui.
- Le reference proprie di `dm-landing` contengono solo il *come applicare* le regole nel contesto
  landing, non le regole stesse:
  - `dm-landing-design/references/landing-patterns.md` — lookup pattern su `landing.csv`
  - `dm-landing-design/references/surface-treatments.csv` — catalogo materiali di superficie (Step 2.5)
  - `dm-landing-design/references/elevation-scale.md` — scala di elevazione semantica (token)
  - `dm-landing-render/references/render-contract.md` — contratto di output + esecuzione profondità (§C)
  - `dm-landing-qa/references/qa-checklist.md` — checklist QA (incl. sez. E depth)
- `surface-treatments.csv` ha **un solo consumatore** (dm-landing-design) → è una **reference privata**,
  non un database L3 (criterio dei ≥2 consumatori). Se un secondo consumatore emerge, va promossa a
  `_shared/` con schema + changelog.
