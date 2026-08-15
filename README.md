# 🗂️ Model Catalog

Catálogo de modelos de **opencode-zen** (62) + **opencode-go** (26), con specs y benchmarks cruzados con [models.dev](https://models.dev) (MIT).

**URL:** https://hrodres.github.io/model-catalog/

## Contenido

| Archivo | Qué es |
|---|---|
| `index.html` | Web v1: tabla ordenable + filtros (provider, búsqueda, reasoning, tool call, contexto mínimo, SWE-Bench) |
| `data.json` | Datos generados (72 modelos, 71 con ficha models.dev) |
| `aliases.json` | Tabla de aliases (componente crítico — espejo de `sync-zen-models.py`) |
| `modelsdev.json` | Cache de models.dev (339 modelos) |

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
