# Changelog — landing.csv

Governance semver applicata allo **schema** (vedi `landing.schema.yaml`), non al contenuto:
- **major**: colonna rimossa o tipo cambiato (rompe i consumatori; richiede coordinated update).
- **minor**: colonna aggiunta non obbligatoria (i consumatori esistenti continuano a funzionare).
- **patch**: righe aggiunte/corrette/rimosse, schema invariato.

## 1.0.0 — 2026-05-30
- Primo schema dichiarato per `landing.csv` (27 pattern, 8 colonne).
- Promozione formale a database L3: secondo consumatore emerso (`dm-landing-design`)
  oltre a `dm-content` → soddisfatto il criterio dei ≥2 consumatori.
- Colonne: `No`, `Pattern Name` (chiave), `Keywords` (lookup per intent), `Section Order`,
  `Primary CTA Placement`, `Color Strategy`, `Recommended Effects`, `Conversion Optimization`.
- Nessuna colonna enum: tutte free-text descrittive.
