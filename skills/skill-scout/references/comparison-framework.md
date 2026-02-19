# Framework de Comparacion de Skills

Usa este framework cuando necesites comparar 2 o mas skills lado a lado.

## Criterios de Evaluacion

### 1. Funcionalidad

Evalua QUE hace cada skill:
- Lista todas las capacidades de cada skill
- Identifica capacidades unicas (que solo tiene uno) vs compartidas (que tienen ambos)
- Evalua completitud: cubre todo lo que necesita el usuario?
- Nota si algun skill hace cosas extra que podrian ser utiles

### 2. Complejidad

Evalua QUE TAN COMPLICADO es:
- Cantidad de archivos en el skill
- Longitud del SKILL.md (lineas)
- Cantidad de scripts ejecutables
- Dependencias externas requeridas

Escala:
- **Simple**: Solo SKILL.md, menos de 50 lineas, sin dependencias
- **Media**: SKILL.md + algunos archivos, 50-200 lineas, pocas dependencias
- **Compleja**: Muchos archivos, 200+ lineas, multiples scripts y dependencias

### 3. Calidad

Evalua QUE TAN BIEN esta hecho:
- Instrucciones claras y bien organizadas?
- Scripts con manejo de errores?
- Documentacion interna suficiente?
- Sigue las mejores practicas?
  - Frontmatter correcto (name + description minimo)
  - SKILL.md bajo 500 lineas
  - Usa progressive disclosure si es complejo (archivos de referencia)
  - Terminologia consistente

Puntuacion:
- 5/5: Excelente — claro, completo, bien documentado, sigue best practices
- 4/5: Bueno — funcional y claro, con detalles menores por mejorar
- 3/5: Aceptable — funciona pero le falta documentacion o claridad
- 2/5: Regular — funciona parcialmente, instrucciones confusas
- 1/5: Malo — incompleto, confuso, o no funciona

### 4. Seguridad

Aplica el checklist de `security-checklist.md` y asigna nivel:
- SEGURO
- CON PRECAUCION
- PELIGROSO

### 5. Mantenimiento

Evalua si el skill esta activo y mantenido:
- Ultima actualizacion del repositorio (fecha)
- Estrellas y forks en GitHub (indicador de comunidad)
- Issues abiertos vs cerrados
- Frecuencia de commits recientes
- Tiene autor/organizacion conocida?

Escala:
- **Activo**: Actualizado en los ultimos 3 meses, comunidad activa
- **Estable**: No se actualiza frecuentemente pero funciona bien
- **Abandonado**: Sin actualizaciones en 6+ meses, issues sin responder

### 6. Compatibilidad

Evalua si funciona en el entorno del usuario:
- Funciona en macOS? (prioridad para este usuario)
- Requiere herramientas especificas instaladas?
- Compatible con la version actual de Claude Code?
- Tiene dependencias de sistema operativo?

---

## Formato de Presentacion

Usa esta tabla para la comparacion:

```
| Criterio | [Skill A] | [Skill B] |
|---|---|---|
| Funcionalidad | [descripcion corta] | [descripcion corta] |
| Complejidad | Simple / Media / Compleja | ... |
| Archivos | [cantidad y tipos] | ... |
| Dependencias | [lista o "ninguna"] | ... |
| Seguridad | Seguro / Precaucion / Peligroso | ... |
| Calidad | [X/5] | ... |
| Mantenimiento | Activo / Estable / Abandonado | ... |
| Compatibilidad | [notas] | ... |
```

Seguido de:
- Lo mejor de cada uno (fortalezas)
- Lo peor de cada uno (debilidades)
- Recomendacion final clara en espanol simple

## Recomendacion Final

Basarse en esta prioridad:
1. Que skill cubre mejor la necesidad especifica del usuario
2. Cual es mas seguro
3. Cual es mas simple (menos complejidad = menos problemas)
4. Cual tiene mejor calidad y mantenimiento
