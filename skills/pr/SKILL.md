---
name: pr
description: Crea un Pull Request en GitHub con título y descripción auto-generados.
disable-model-invocation: true
argument-hint: "[rama-base] (default: main)"
---

## Crear Pull Request

Sigue estos pasos en orden:

### 1. Determinar rama base
- Si el usuario pasó un argumento (`$ARGUMENTS`), úsalo como rama base
- Si no, usa `main` como default
- Verifica que la rama actual NO sea la rama base

### 2. Revisar cambios
- Ejecuta `git log <base>..HEAD --oneline` para ver todos los commits del PR
- Ejecuta `git diff <base>...HEAD --stat` para ver archivos cambiados
- Si no hay diferencias con la base, informa al usuario y termina

### 3. Verificar push
- Ejecuta `git status` para ver si hay cambios sin commit
- Si hay cambios sin commit, pregunta al usuario si quiere hacer commit primero
- Verifica que la rama local esté pushada al remoto

### 4. Crear el PR
- Usa `gh pr create` con:
  - `--base <rama-base>`
  - `--title` corto y descriptivo (máximo 70 caracteres)
  - `--body` con formato:
    ```
    ## Summary
    - Bullet points de los cambios principales

    ## Test plan
    - Pasos para verificar los cambios

    🤖 Generated with Claude Code
    ```

### 5. Reportar resultado
- Informa al usuario en español:
  - URL del PR creado
  - Título del PR
  - Rama origen → rama destino
