# 🗂️ Model Catalog

Catálogo de modelos de **opencode-zen** (62) + **opencode-go** (26), con specs y benchmarks cruzados con [models.dev](https://models.dev) (MIT).

**URL:** https://hrodres.github.io/model-catalog/

## Contenido

| Archivo | Qué es |
|---|---|
| `index.html` | Web v2.0: tabla ordenable + filtros + **sección Estado OpenCode** (4 tabs: Salud, Límites y precios, Evolución, Cambios) + cards responsive móvil + topbar glassmorphism |
| `data.json` | Datos generados (72 modelos, 71 con ficha models.dev) — desde v1.1 **sin aliases** (fuente única: aliases.json) + metadata `modelsdev_fetched`/`modelsdev_count` |
| `aliases.json` | Tabla de aliases (componente crítico — espejo de `sync-zen-models.py`) |
| `modelsdev.json` | Cache de models.dev (339 modelos) |
| `status.json` | Salud actual por modelo (200/401/503/…) — generado por `check-model-health.py` |
| `status_history.json` | Serie temporal diaria por modelo (retención 120 días) |
| `catalog-history.json` | Snapshots del catálogo público + eventos (added/removed) |
| `limits.json` | Límites/precios **manuales** con `verified_at` (OpenCode no expone API — 403) |
| `changelog-auto.json` | Eventos automáticos (cambios de estado detectados entre checks) |
| `changelog-manual.json` | Cambios manuales anotados (recortes de cuota, outages…) |

## v2.0 (Estado OpenCode — consenso N2 2026-08-17)

Aprobada tras auditoría de 2 modelos (GLM 5.3 + Kimi K3, APROBAR CON CAMBIOS 2-0).
- **Tab Salud**: estado hoy por modelo + racha de fallos + sparkline de últimos 14 días + resumen numérico
- **Tab Límites y precios**: cuotas mensuales ($15/$60), rate limits Flash, precio peak/off-peak, límite global de gasto — con badge de caducidad (alert si >14 días sin verificar)
- **Tab Evolución**: eventos de catálogo (modelos nuevos/eliminados, diff del catálogo público) + serie temporal por modelo
- **Tab Cambios**: changelog combinado automático + manual con fecha
- `check-model-health.py` (NUEVO, independiente): prueba cada modelo con `max_tokens=1` (doble intento para 400), excluye Zen free (cuota global 100 req/día), semántica 200=✅/5xx=⚠️/401-403=ℹ️ no accesible/400-422=❓ no responde al probe, aborta si la auth global falla (conserva estado anterior), escrituras atómicas, timeouts 8s/modelo · 10 min global
- Integrado en el refresh diario (paso 1.5 pre-commit)

Aprobada tras auditoría de 2 modelos (GLM 5.2 ✅ APROBAR 9/10 + Kimi K3 ✅ APROBAR CON CAMBIOS 8/10, sin rework obligatorio). Cambios:
- **Provider sin duplicados** (badges limpios: 🌱/🚀/💜/⚠️)
- **Benchmarks expandibles**: click en fila → chips con todos los benchmarks de models.dev
- **Sufijo free** en nombre visible de variantes free
- **Contador con ficha** (X/72 con ficha models.dev) + tooltip en ⚠️
- **Filtro Zen (solo pago)** renombrado + nuevo **Zen (todos)**
- **Presets de contexto** (0/32K/128K/1M) junto al slider
- **Metadata**: `modelsdev_fetched` + `modelsdev_count` en data.json y footer
- **Responsive**: cards en móvil (≤760px) vía media query
- **aliases eliminados de data.json** — aliases.json es la única fuente

## v1.2 (UX/UI móvil moderna — 2026-08-15)

Implementada directamente (la auditoría GPT quedó bloqueada: gpt-5.6-luna en Go devuelve salida vacía en tareas reales, 7 intentos):
- Topbar sticky con glassmorphism (backdrop-blur) + logo gradiente + contador pill
- Filtros colapsables en móvil (botón "Filtros" con indicador de filtros activos)
- Skeleton loading (shimmer) mientras carga data.json
- Cards móviles rediseñadas: radius 14px, sombras, jerarquía clara, targets táctiles ≥44px
- Micro-interacciones: flecha expandible que rota, fade-in, hover/focus states
- Contador contextual ("X de 72" cuando hay filtros)

## Regenerar datos

```bash
python3 scripts/build-model-catalog.py
# → regenera model-catalog/data.json + aliases.json + modelsdev.json

python3 scripts/check-model-health.py
# → regenera status.json + status_history.json + catalog-history.json + changelog-auto.json
```

Ambos corren automáticamente cada día en el refresh (scripts/refresh-model-catalog.sh) — la web se despliega sola.

El script:
1. Descarga `https://models.dev/models.json`
2. Descarga catálogos Zen (`/zen/v1/models`) y Go (`/zen/go/v1/models`)
3. Cruza por ID (mapeo manual `ID_MAP` + fuzzy matching por sufijo)
4. Genera `data.json` y `aliases.json`

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

- Fuente: [models.dev](https://models.dev) (MIT, repo anomalyco/models.dev) + catálogos opencode
- Fecha snapshot: ver `data.json → snapshot`
