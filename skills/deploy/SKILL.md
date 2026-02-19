---
name: deploy
description: Hace commit de los cambios actuales y push a GitHub para desplegar en Vercel automáticamente.
disable-model-invocation: true
---

## Deploy: Commit + Push

Sigue estos pasos en orden:

### 1. Verificar estado
- Ejecuta `git status` para ver qué archivos cambiaron
- Ejecuta `git diff --stat` para ver un resumen de cambios
- Si no hay cambios, informa al usuario y termina

### 2. Revisar cambios
- Ejecuta `git diff` para ver los cambios detallados
- Ejecuta `git log --oneline -5` para ver el estilo de commits recientes

### 3. Crear commit
- Agrega los archivos relevantes (NO uses `git add .` a menos que todo sea intencional)
- Escribe un mensaje de commit descriptivo siguiendo el estilo del proyecto
- Formato: `tipo(módulo): descripción breve`
- Tipos: feat, fix, refactor, docs, style, test, chore
- Incluye `Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>`

### 4. Push
- Ejecuta `git push` a la rama actual
- Si la rama no tiene upstream, usa `git push -u origin <rama>`

### 5. Reportar resultado
- Informa al usuario en español:
  - Qué rama se actualizó
  - Cuántos archivos cambiaron
  - El mensaje de commit usado
  - Que Vercel desplegará automáticamente
