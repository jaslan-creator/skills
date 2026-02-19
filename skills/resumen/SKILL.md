---
name: resumen
description: Resume los cambios hechos en la sesión actual en español simple, sin jerga técnica. Útil cuando el usuario pregunta "qué hicimos", "resumen de hoy", o "qué cambió".
---

## Resumen de Sesión

### Instrucciones

1. **Idioma**: Todo en español, sin términos técnicos
2. **Audiencia**: Usuario no técnico — explica qué se logró, no cómo

### Proceso

#### 1. Recopilar información
- Ejecuta `git log --oneline -20` para ver commits recientes
- Ejecuta `git diff --stat HEAD~10..HEAD 2>/dev/null` para ver archivos cambiados
- Revisa el historial de la conversación actual para contexto

#### 2. Crear resumen

Usa este formato:

```
## Resumen de lo que hicimos

### Lo que se logró
- [descripción simple de cada cambio/feature/fix en lenguaje no técnico]

### Archivos modificados
- X archivos cambiaron

### Próximos pasos
- [si hay algo pendiente, mencionarlo]

### Estado
- Rama: [nombre]
- Último cambio: [hace cuánto]
- Desplegado: Sí/No (si se hizo push)
```

### Reglas
- NO mencionar nombres de funciones o variables
- NO mostrar código
- Describir los cambios desde la perspectiva del usuario final
- Ejemplo bueno: "Se arregló el problema donde los filtros no se guardaban"
- Ejemplo malo: "Se refactorizó useFilterParams para persistir state en URL params"
