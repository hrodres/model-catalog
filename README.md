# 🗂️ Model Catalog

Catálogo de modelos del gateway OpenClaw con **tres proveedores**: **opencode-go**, **opencode-zen** y **openrouter**. Cruza los catálogos de opencode con las specs y benchmarks de [models.dev](https://models.dev) (MIT), y añade precios comparativos y estado de salud por modelo.

**Web pública:** https://hrodres.github.io/model-catalog/

## Qué es

- **Inventario unificado** de los modelos disponibles en cada proveedor del gateway, con specs (contexto, output, reasoning, tool-call) y benchmarks (SWE-Bench).
- **Precios comparativos** por MTok: plan Go, Zen (off-peak/peak) y OpenRouter (API real).
- **Salud por modelo**: probes de disponibilidad + historial 14 días.
- **Cadena de fallback** del gateway (6 eslabones) y **cuota Go** (buckets continuo/semanal/mensual).
- Todo se genera automáticamente en cada refresh; la web se despliega sola.

## Estructura del repo

| Archivo | Qué es |
|---|---|
| `index.html` | Web (SPA vanilla JS, sin dependencias) |
| `data.json` | Catálogo fusionado con specs, precios, cadena y resúmenes (generado por build) |
| `aliases.json` | Mapeo de aliases opencode ↔ models.dev (crítico: espejo del build) |
| `modelsdev.json` | Cache de models.dev |
| `status.json` | Salud actual por modelo (generado por check-health) |
| `status_history.json` | Serie temporal diaria (retención 120 días) |
| `catalog-history.json` | Snapshots del catálogo + altas/bajas |
| `health-state.json` | Resumen por proveedor + cuota Go + guardias de saldo (booleanos) |
| `limits.json` | Límites/precios manuales (OpenCode no expone API) |
| `changelog-auto.json` / `changelog-manual.json` | Eventos automáticos y manuales |

## Regenerar datos

```bash
python3 scripts/build-model-catalog.py   # → data.json + aliases.json + modelsdev.json
python3 scripts/check-model-health.py    # → status*.json + health-state.json + changelogs
```

Ambos corren automáticamente en cada refresh (`scripts/refresh-model-catalog.sh`), que además commitea y hace push — GitHub Pages despliega `main` solo.

El build:
1. Descarga `models.dev/models.json`
2. Descarga los catálogos de opencode (Zen `/zen/v1/models`, Go `/zen/go/v1/models`)
3. Cruza por ID (`ID_MAP` manual + fuzzy matching por sufijo)
4. Añade precios OpenRouter
5. Genera `data.json` + `aliases.json`

## Añadir un modelo al mapeo

Si un modelo de opencode no tiene ficha en models.dev, añade su ID al dict `ID_MAP` en `scripts/build-model-catalog.py`:

```python
ID_MAP = {
    "mi-modelo-free": "lab/mi-modelo",   # id opencode → id models.dev
}
```

Si no existe en models.dev, la web lo muestra igual con badge ⚠️ (datos solo del provider).

## Desplegar

```bash
git add -A && git commit -m "update catalog" && git push origin main
```

GitHub Pages sirve `main` automáticamente. **Nota PWA:** si se añade service worker, hay que hacer bump de `CACHE_NAME` en cada deploy (lección en TOOLS.md).

## Datos dinámicos

Los números vivos (modelos por proveedor, disponibilidad, cuotas, snapshots) cambian en cada refresh — **consúltalos en la web**, no en este README, para no leer información obsoleta.

- Fuente: models.dev (MIT) + catálogos opencode + OpenRouter (API de precios)
- **Privacidad:** la web solo publica estados y precios públicos (por MTok). Nunca keys ni saldos crudos; las guardias de saldo son booleanos.