---
name: test
description: Detecta el framework de tests del proyecto, ejecuta los tests, y reporta resultados en español simple.
disable-model-invocation: true
---

## Ejecutar Tests

### Proceso

#### 1. Detectar framework de tests
Busca en `package.json` el script de test, o detecta archivos de configuración:
- `vitest.config.ts` → Vitest (`npm test`)
- `jest.config.*` → Jest (`npm test`)
- `pytest.ini` / `pyproject.toml` → pytest (`pytest`)
- `Cargo.toml` → Rust (`cargo test`)
- Si no encuentra nada, informa al usuario que no hay tests configurados

#### 2. Ejecutar tests
- Ejecuta el comando de tests del proyecto
- Captura la salida completa

#### 3. Reportar resultados

Usa este formato:

```
## Resultados de Tests

### Resumen
- ✅ X tests pasaron
- ❌ X tests fallaron (si aplica)
- ⏭️ X tests saltados (si aplica)

### Tests fallidos (si los hay)
Para cada test fallido:
- Nombre del test
- Qué se esperaba vs qué pasó (en español simple)
- Sugerencia de cómo arreglarlo

### Conclusión
- Todo bien ✅ / Hay problemas que arreglar ❌
```

### Reglas
- Reporta en español
- Si hay tests fallidos, explica de forma simple qué pasó
- No muestres stack traces completos — solo lo relevante
- Si el usuario pasa argumentos (`$ARGUMENTS`), úsalos como filtro de tests
