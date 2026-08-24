# Rediseño F5 — Consistencia web model-catalog (OpenRouter)

**Fecha:** 2026-08-24 · **Ejecutor:** hy3 (subagent) · **Guardián:** main
**Estado:** ✅ Aplicado + commit + push verificado contra API GitHub

## Contexto

La F4 (commit `1c6a342`) añadió los **DATOS** de OpenRouter a la web (data.json con
`providers_3rd`, `price_matrix`, `chain_snapshot`, `status_badges`; health-state.json con
proveedores/chain/go_quota/balances). Pero la **capa de presentación** seguía anclada a
"Zen/Go": el título, el `h1`/`h2`, el README y varios textos visibles no mencionaban
OpenRouter, y otros datos (counts 72/62+26, "v2.0", "4 tabs") estaban desactualizados.

Héctor detectó que OpenRouter no aparecía en título/README/secciones y **aceptó la
propuesta de rediseño**. Esta F5 audita TODO el repo y lo hace consistente en
forma, estructura, datos y visualización.

## Auditoría — inconsistencias encontradas

| Archivo | Problema | Corregido |
|---|---|---|
| `index.html` `<title>` | "OpenCode Zen/Go" sin OpenRouter | ✅ → "Zen/Go/OpenRouter" |
| `index.html` `<meta description>` | "OpenCode Zen/Go" sin OpenRouter | ✅ → "Zen/Go/OpenRouter" |
| `index.html` `<h2>` | "🩺 Estado OpenCode" sin OR | ✅ → "Estado del catálogo (Zen/Go/OpenRouter)" |
| `index.html` footer | "catálogos opencode" (sin OR) | ✅ → "+ OpenRouter" |
| `index.html` pill-strip | Sin indicador OpenRouter | ✅ → pill 🟠 OR + leyenda `.hdot.or` |
| `index.html` quota section | Sin tarjeta OpenRouter | ✅ → tarjeta 🟠 OR (de `status_badges.providers.openrouter`) |
| `README.md` | CERO menciones openrouter; decía "Zen (62) + Go (26)", "72 modelos", "web v2.0", "4 tabs" | ✅ reescrito fiel |
| `data.json` counts | OK (78/64/30/9/14) — ya coherentes | — (sin cambio necesario) |
| `health-state.json` | OK (proveedores Go/Zen/OR, go_quota, balances) | — (sin cambio) |
| `status.json` | OK (incluye 3 ids openrouter/deepseek…) | — (sin cambio) |
| Filtro Provider (index.html) | Ya incluía `🟠 OpenRouter` (F4) | — (ya correcto) |
| Badges por proveedor (JS) | Ya incluía `🟠 OR` (F4) | — (ya correcto) |
| Secciones Matriz/Cadena/Cuotas | Ya existían (F4) | — (ya correcto) |

**Conclusión de la auditoría de datos:** la capa de datos (data.json / health-state.json /
status.json / counts) ya era coherente y cuadraba (go=30, zen=64, free=9, openrouter=14,
total=78, with_modelsdev=73). El problema era puramente de **presentación y documentación**.

## Cambios por archivo

1. **`index.html`** — título, meta description, h2, footer → "Zen/Go/OpenRouter"; añadido
   pill 🟠 OR al strip de estado + variable CSS `--orange` + `.hdot.or`/`.strip-pill.or`;
   añadida tarjeta OpenRouter en "Estado de cuotas" (lee `status_badges.providers.openrouter`);
   añadida entrada 🟠 OR a la leyenda de salud.
2. **`README.md`** — reescrito: counts reales (78/64/30/14/9/73), 3 proveedores, secciones
   nuevas (Matriz/Cadena/Cuotas), filtro OR, tabla de archivos actualizada, historial F5/F4.
3. **`docs/redesign-f5.md`** — este documento de propuesta/rediseño.
4. **`docs/plan-reingenieria-modelos.md`** — añadida FASE 5 (objetivo, checklist, gate G5,
   fecha, executor/guardian).

## Principios de diseño

- **Glassmorphism conservado**: no se rompió el estilo ni el responsive móvil (media queries
  de `.strip-pill` y `.hs-card` ya existentes se respetan; el pill OR se pliega igual en móvil).
- **Privacidad**: solo estados y precios públicos (MTok). Las guardias de saldo son booleanos;
  ningún saldo monetario crudo ni key expuesta (verificado con grep anti-secretos).
- **Nomenclatura coherente**: "OpenCode Zen/Go/OpenRouter" en todos los textos visibles;
  OR como "3er proveedor" / "🟠 OR".

## Verificaciones

- `jq` valida data.json / health-state.json / status.json (sin cambios en datos, ya válidos).
- grep "openrouter"/"OpenRouter" en index.html + README → presente en título, h2, footer,
  strip, leyenda, quota y README completo.
- Anti-secretos: cero coincidencias en todo el repo (excl. `.bak.`).
- Sin saldos monetarios crudos en archivos públicos (solo precios por MTok y límites de plan).
- Estructura HTML confirmada post-cambios (título, h1, h2, secciones con id/acc, footer).
