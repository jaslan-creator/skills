---
name: review
description: Revisa todos los cambios pendientes buscando errores, problemas de seguridad, y mejoras antes de hacer deploy.
disable-model-invocation: true
---

## Revisar Cambios

Revisa todos los cambios pendientes antes de deploy.

### Proceso

#### 1. Obtener cambios
- Ejecuta `git diff` para cambios no staged
- Ejecuta `git diff --cached` para cambios staged
- Ejecuta `git log origin/HEAD..HEAD --oneline 2>/dev/null` para commits no pushados
- Si no hay cambios, informa al usuario y termina

#### 2. Analizar cada archivo cambiado
Para cada archivo, revisa:

- **Errores**: Bugs lógicos, null checks faltantes, variables sin usar
- **Seguridad**: Credenciales expuestas, SQL injection, XSS, validación faltante
- **TypeScript**: Tipos incorrectos, `any` innecesarios, imports faltantes
- **Rendimiento**: Queries N+1, re-renders innecesarios, memory leaks
- **Estilo**: Código muerto, console.logs olvidados, TODOs sin resolver

#### 3. Reportar en español

Usa este formato:

```
## Revisión de Cambios

### ✅ Todo bien
- [lista de cosas correctas]

### ⚠️ Advertencias
- [cosas que podrían mejorar pero no son críticas]

### ❌ Problemas
- [cosas que deberían arreglarse antes de deploy]

### Resumen
- X archivos revisados
- X problemas encontrados
- Recomendación: Listo para deploy / Necesita correcciones
```

Si no hay problemas, simplemente di que todo se ve bien y está listo para deploy.
