---
name: fix
description: El usuario describe un problema o bug en español y Claude investiga el código, encuentra la causa, y lo arregla.
argument-hint: "descripción del problema"
---

## Arreglar Bug

El usuario ha descrito un problema: $ARGUMENTS

### Instrucciones

1. **Comunicación**: Siempre responde en español, sin jerga técnica
2. **No pidas al usuario que haga nada técnico** — investiga y arregla tú mismo

### Proceso

#### Paso 1: Entender el problema
- Lee la descripción del usuario
- Si no está claro, pregunta en español qué pasa exactamente (qué esperaba vs qué pasó)

#### Paso 2: Investigar
- Busca archivos relevantes usando Glob y Grep
- Lee los archivos sospechosos
- Identifica la causa raíz

#### Paso 3: Arreglar
- Haz el cambio mínimo necesario para resolver el problema
- No refactorices código que no está relacionado
- No agregues funcionalidad extra

#### Paso 4: Verificar
- Revisa que el cambio no rompa nada más
- Si hay tests, ejecútalos
- Si hay TypeScript, verifica que compile

#### Paso 5: Reportar
- Explica en español simple:
  - Cuál era el problema
  - Qué lo causaba
  - Cómo se arregló
  - Qué archivos se modificaron
