---
name: status
description: Muestra el estado actual del proyecto — rama, cambios pendientes, último commit. Útil cuando el usuario pregunta "en qué estamos", "qué rama es", o "hay cambios pendientes".
---

## Estado del Proyecto

### Proceso

Ejecuta estos comandos y presenta los resultados de forma simple en español:

1. `git branch --show-current` — rama actual
2. `git status --short` — cambios pendientes
3. `git log --oneline -3` — últimos 3 commits
4. `git remote -v` — repositorio remoto
5. `git log origin/HEAD..HEAD --oneline 2>/dev/null` — commits sin pushear

### Formato de respuesta

```
## Estado del Proyecto

📍 Rama: [nombre de la rama]
📦 Repositorio: [nombre del repo]

### Cambios pendientes
- [X archivos modificados / Sin cambios pendientes]

### Últimos cambios
1. [descripción simple del commit más reciente]
2. [segundo más reciente]
3. [tercero]

### Push
- [Todo pushado ✅ / X commits sin pushear ⚠️]
```

### Reglas
- Responde en español
- Mantén las descripciones de commits simples y entendibles
- Si no hay cambios, dilo claramente
- Si hay commits sin push, advierte al usuario
