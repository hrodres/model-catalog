# 🗂️ Model Catalog

Catálogo de modelos de **OpenCode** con tres proveedores: **opencode-zen** (64), **opencode-go** (30) y **openrouter** (14), con specs y benchmarks cruzados con [models.dev](https://models.dev) (MIT).

**URL:** https://hrodres.github.io/model-catalog/

## Contenido

| Archivo | Qué es |
|---|---|
| `index.html` | Web: tabla ordenable + filtros (incl. proveedor) + **sección Estado del catálogo** (8 acordeones: Salud, Límites y precios, Matriz de precios, Cadena de fallback, Estado de cuotas, Evolución, Cambios, Catálogo) + cards responsive móvil + topbar glassmorphism |
| `data.json` | Datos generados (78 modelos, 73 con ficha models.dev) — fuente única de aliases en `aliases.json` + metadata `modelsdev_fetched`/`modelsdev_count`; incluye `providers_3rd`, `price_matrix`, `chain_method`, `chain_snapshot`, `status_badges`, `counts` |
| `aliases.json` | Tabla de aliases (componente crítico — espejo de `sync-zen-models.py`) |
| `modelsdev.json` | Cache de models.dev (355 modelos) |
| `status.json` | Salud actual por modelo (200/401/503/…) — generado por `check-model-health.py` |
| `status_history.json` | Serie temporal diaria por modelo (retención 120 días) |
| `catalog-history.json` | Snapshots del catálogo público + eventos (added/removed) |
| `health-state.json` | Resumen de salud por proveedor (Go/Zen/OpenRouter) + cuota Go + guardias de saldo (solo booleanos) + cadena resuelta |
| `limits.json` | Límites/precios **manuales** con `verified_at` (OpenCode no expone API — 403) |
| `changelog-auto.json` | Eventos automáticos (cambios de estado detectados entre checks) |
| `changelog-manual.json` | Cambios manuales anotados (recortes de cuota, outages…) |

## Proveedores (snapshot 2026-08-24)

| Proveedor | Modelos | Rol | Notas |
|---|---|---|---|
| **opencode-go** | 30 | Suscripción (marginal 0) | Cuota por buckets (Continuo/Semanal/Mensual); 429 = cuota, no down |
| **opencode-zen** | 64 (9 free) | Créditos + free (pool común) | Free no se prueba (cuota global); paid cubre OpenRouter caído |
| **openrouter** | 14 | Backend 100% independiente, 4-9× más barato | Mismo modelo, precios reales API; tarjeta de estado en "Estado de cuotas" |

**Cuenta total:** 78 modelos en el catálogo (algunos comparten id lógico entre proveedores). Con ficha models.dev: 73.

## Secciones de la web

- **🩺 Estado del catálogo (Zen/Go/OpenRouter):** pill-strip de pulso (operativos ⚠️ caídos 🌱 free 🟠 OR) + acordeones:
  - **Salud** — estado hoy por modelo + racha de fallos + sparkline de últimos 14 días + resumen numérico
  - **Límites y precios** — suscripción Go, límites globales de gasto, rate limits
  - **Matriz de precios** — zen paid (off/peak) + OpenRouter reales (API)
  - **Cadena de fallback** — método canónico + snapshot de cadena activa con timestamp
  - **Estado de cuotas** — cuota Go (granular) + tarjeta OpenRouter + guardias de saldo (solo booleanos)
  - **Evolución** — altas/bajas del catálogo + serie temporal por modelo
  - **Cambios** — changelog combinado automático + manual con fecha
- **🗂️ Catálogo de modelos:** tabla con filtro por **Provider** (Todos / 🌱 Free / 🚀 Go / 💰 Zen pago / 💜 Zen todos / 🟠 OpenRouter), buscador, contexto mínimo (slider + presets 0/32K/128K/1M), checks de reasoning/tool/SWE-Bench; badges por proveedor; tocar fila → ficha con specs, precios (Go/OpenRouter) y benchmarks.

## Regenerar datos

```bash
python3 scripts/build-model-catalog.py
# → regenera model-catalog/data.json + aliases.json + modelsdev.json
#   incluye providers_3rd (Go/Zen/OpenRouter), price_matrix, chain_method,
#   chain_snapshot, status_badges y counts coherentes

python3 scripts/check-model-health.py
# → regenera status.json + status_history.json + catalog-history.json
#   + changelog-auto.json + health-state.json (proveedores Go/Zen/OR)
```

Ambos corren automáticamente cada día en el refresh (scripts/refresh-model-catalog.sh) — la web se despliega sola.

El script `build-model-catalog.py`:
1. Descarga `https://models.dev/models.json`
2. Descarga catálogos Zen (`/zen/v1/models`) y Go (`/zen/go/v1/models`)
3. Cruza por ID (mapeo manual `ID_MAP` + fuzzy matching por sufijo)
4. Añade precios OpenRouter desde `or-models-cache.json`
5. Genera `data.json` (con `counts` de los 3 proveedores) y `aliases.json`

## Añadir un modelo nuevo al mapeo

Si un modelo de opencode no tiene ficha en models.dev, añadir su ID al dict `ID_MAP` en
`scripts/build-model-catalog.py`:

```python
ID_MAP = {
    "mi-modelo-free": "lab/mi-modelo",   # id opencode → id models.dev
}
```

Si no existe en models.dev, la web lo muestra igual con badge ⚠️ (datos solo del provider).

## Desplegar (GitHub Pages)

```bash
cd model-catalog
git add -A && git commit -m "update catalog"
git push origin main
```

GitHub Pages sirve `main` automáticamente. **Nota PWA:** si se añade service worker,
bump de `CACHE_NAME` en cada deploy (lección documentada en TOOLS.md).

## Datos

- Fuente: [models.dev](https://models.dev) (MIT, repo anomalyco/models.dev) + catálogos opencode (Zen/Go) + OpenRouter (API de precios)
- Fecha snapshot: ver `data.json → snapshot`
- **Privacidad:** la web solo publica estados y precios públicos (por MTok). Nunca keys ni saldos monetarios crudos; las guardias de saldo son solo booleanos.

## Historial de versiones

- **F5 (2026-08-24):** rediseño de consistencia — OpenRouter visible en título, h1, h2, footer, pill-strip, leyenda y README; counts reales de 3 proveedores; secciones nuevas documentadas. Sin secretos.
- **F4 (2026-08-24):** OpenRouter como 3er proveedor en datos (data.json: `providers_3rd`, `price_matrix`, `chain_method`, `chain_snapshot`, `status_badges`; health-state.json: proveedores/chain/go_quota/balances) + secciones web nuevas (Matriz de precios, Cadena de fallback, Estado de cuotas) + filtro OR.
- **v2.0 (Estado OpenCode — 2026-08-17):** aprobada tras auditoría; tabs de estado (Salud, Límites y precios, Evolución, Cambios), badges limpios, benchmarks expandibles, responsive móvil.
- **v1.2 (UX/UI móvil moderna — 2026-08-15):** topbar sticky glassmorphism, filtros colapsables, skeleton loading, cards móviles.
