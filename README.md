# 🗂️ Model Catalog

Catálogo de modelos de **opencode-zen** (62) + **opencode-go** (26), con specs y benchmarks cruzados con [models.dev](https://models.dev) (MIT).

**URL:** https://hrodres.github.io/model-catalog/

## Contenido

| Archivo | Qué es |
|---|---|
| `index.html` | Web v1: tabla ordenable + filtros (provider, búsqueda, reasoning, tool call, contexto mínimo, SWE-Bench) |
| `data.json` | Datos generados (72 modelos, 71 con ficha models.dev) — desde v1.1 **sin aliases** (fuente única: aliases.json) + metadata `modelsdev_fetched`/`modelsdev_count` |
| `aliases.json` | Tabla de aliases (componente crítico — espejo de `sync-zen-models.py`) |
| `modelsdev.json` | Cache de models.dev (339 modelos) |

## v1.1 (auditoría N2 — 2026-08-15)

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
```

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
