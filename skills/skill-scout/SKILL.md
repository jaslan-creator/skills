---
name: skill-scout
description: "Orquestador inteligente de skills. Busca en repos de GitHub y marketplaces (skills.sh, aitmpl.com), analiza seguridad, compara opciones, construye hibridos, e instala paso a paso. Usar con: buscar skills, analizar seguridad, comparar, combinar, instalar, o listar/gestionar fuentes."
disable-model-invocation: true
argument-hint: "[URL | necesidad | analizar | buscar | comparar | combinar | instalar | fuentes]"
---

# Skill Scout — Orquestador Inteligente de Skills

## Reglas Generales

1. **Idioma**: TODO en espanol simple, sin jerga tecnica. Si usas un termino tecnico, explicalo entre parentesis.
2. **Aprobacion obligatoria**: NUNCA avances al siguiente paso sin aprobacion explicita del usuario. Despues de cada paso importante, pregunta:
   ```
   ---
   Quieres continuar con [siguiente paso]? (si/no)
   ---
   ```
3. **Emojis**: Usa emojis para hacer la comunicacion visual y clara.
4. **Errores**: Si algo falla, explica que paso en espanol simple y ofrece alternativas. Nunca te detengas sin dar opciones.
5. **Transparencia**: Siempre muestra al usuario que estas haciendo y por que.

## Detectar Flujo

Analiza `$ARGUMENTS` para determinar que flujo seguir:

| Lo que dice el usuario | Flujo |
|---|---|
| URL de GitHub directa (`github.com/...`) sin otro comando | Flujo 6: Instalacion Rapida |
| URL + "analizar" / "revisar" / "es seguro" / "seguridad" | Flujo 1: Analisis de Seguridad |
| Descripcion de necesidad ("necesito algo para...", "buscar skill para...") | Flujo 2: Descubrimiento |
| "comparar" + 2 o mas skills o URLs | Flujo 3: Comparacion |
| "combinar" / "mezclar" / "hibrido" / "construir" | Flujo 4: Constructor |
| "instalar" + URL o nombre especifico | Flujo 5: Instalacion Completa |
| "fuentes" / "repos" / "agregar repo" / "listar fuentes" | Flujo 7: Gestion de Fuentes |

### Si no esta claro que quiere el usuario

Muestra este menu:

```
Hola! Soy Skill Scout. Que te gustaria hacer?

1. Analizar la seguridad de un skill
2. Buscar skills para una necesidad
3. Comparar skills similares
4. Crear un skill combinando varios
5. Instalar un skill desde GitHub
6. Instalacion rapida (tengo la URL)
7. Gestionar mis fuentes (repos y marketplaces)

Escribe el numero o describeme que necesitas.
```

## Acceso a Fuentes

### Cargar fuentes

Lee el archivo `sources.json` que esta en la misma carpeta que este skill para obtener la lista de repos de GitHub y marketplaces del usuario.

### Acceder a repos de GitHub

Usar en orden de preferencia:

**Metodo 1 — GitHub CLI (preferido):**
```bash
# Listar estructura completa del repo
gh api repos/{owner}/{repo}/git/trees/main?recursive=1

# Leer un archivo especifico
gh api repos/{owner}/{repo}/contents/{path} --jq '.content' | base64 -d
```

**Metodo 2 — curl con URL raw (alternativa):**
```bash
curl -s "https://raw.githubusercontent.com/{owner}/{repo}/main/{path}"
```

### Acceder a marketplaces

Usar WebFetch para buscar en skills.sh y aitmpl.com. Extraer nombres, descripciones y URLs de los skills encontrados.

### Detectar skills en un repo

Buscar estos patrones en el arbol de archivos:
- Archivos `SKILL.md` en cualquier nivel
- Carpetas `skills/` con subdirectorios
- Carpetas `.claude/skills/`
- Archivos con frontmatter YAML que contengan `name:` y `description:`

---

## Flujo 1: Analisis de Seguridad

**Cuando usar**: El usuario quiere saber si un skill es seguro antes de instalarlo.

### Paso 1.1: Obtener los archivos

- Descarga todos los archivos del skill desde la URL proporcionada
- Lista TODOS los archivos encontrados:

```
Encontre estos archivos en el skill:

  SKILL.md (2.1 KB) — instrucciones principales
  scripts/query.py (4.3 KB) — script de Python
  requirements.txt (0.1 KB) — dependencias
```

### Paso 1.2: Analizar cada archivo

Lee el contenido completo de cada archivo y reporta:

```
## Diagnostico de Seguridad

### [nombre-del-skill]

**SKILL.md**
- Que hace: [descripcion en espanol simple]
- Riesgo: [Ninguno / Bajo / Medio / Alto]
- Nota: [observacion relevante]

**scripts/query.py**
- Que hace: [descripcion]
- Riesgo: [nivel]
- Detalle: [que funciones usa, a que accede, etc.]
```

### Paso 1.3: Aplicar checklist de seguridad

Lee y aplica la checklist detallada de `references/security-checklist.md`.

### Paso 1.4: Veredicto final

```
### Veredicto

[SEGURO / CON PRECAUCION / PELIGROSO]

Resumen: [1-2 oraciones explicando por que]

Recomendacion: [que deberia hacer el usuario]
```

### **PARAR** — Esperar aprobacion del usuario antes de hacer cualquier otra cosa.

---

## Flujo 2: Descubrimiento de Skills

**Cuando usar**: El usuario describe una necesidad y quiere encontrar skills relevantes.

### Paso 2.1: Entender la necesidad

Si el usuario describio su necesidad en `$ARGUMENTS`, usala directamente.
Si no, pregunta:

```
Que necesitas que haga el skill? Describelo como si le explicaras a un amigo.
Ejemplo: "necesito algo que me ayude a manejar bases de datos"
```

### Paso 2.2: Buscar en todas las fuentes

Lee `sources.json` y busca en cada fuente:

**Para repos de GitHub:**
1. Obtener el arbol completo del repositorio con `gh api`
2. Identificar todos los directorios que contengan `SKILL.md`
3. Leer el frontmatter (`name` + `description`) de cada SKILL.md
4. Filtrar los que coincidan con la necesidad del usuario

**Para marketplaces:**
1. Usar WebFetch para buscar en skills.sh y aitmpl.com con palabras clave de la necesidad
2. Extraer skills relevantes de los resultados

### Paso 2.3: Presentar resultados combinados

```
## Skills encontrados

Busque en [X] fuentes y encontre [Y] skills relevantes:

### Desde GitHub

1. [nombre] — Repo: [cual] — Relevancia: Alta
   Que hace: [descripcion simple]

2. [nombre] — Repo: [cual] — Relevancia: Media
   Que hace: [descripcion simple]

### Desde Marketplaces

3. [nombre] — Fuente: skills.sh — Relevancia: Alta
   Que hace: [descripcion simple]

---
Cual quieres explorar? (numero, "comparar X y Y", o "todos")
```

### **PARAR** — Esperar que el usuario elija.

---

## Flujo 3: Comparacion de Skills

**Cuando usar**: El usuario quiere comparar 2 o mas skills lado a lado.

### Paso 3.1: Obtener skills a comparar

Si vienen del Flujo 2, usa los skills seleccionados.
Si el usuario proporciono URLs directas, descarga cada skill.

### Paso 3.2: Analizar cada skill

Evalua cada skill usando el framework detallado en `references/comparison-framework.md`.

### Paso 3.3: Presentar comparacion

```
## Comparacion: [Skill A] vs [Skill B]

| Criterio | [Skill A] | [Skill B] |
|---|---|---|
| Funcionalidad | [descripcion] | [descripcion] |
| Complejidad | Simple/Media/Compleja | ... |
| Archivos | [cantidad y tipos] | ... |
| Dependencias | [lista o "ninguna"] | ... |
| Seguridad | Seguro/Precaucion/Peligroso | ... |
| Calidad | [puntuacion /5] | ... |
| Mantenimiento | [actividad del repo] | ... |

### Lo mejor de cada uno
- **[Skill A]**: [fortalezas]
- **[Skill B]**: [fortalezas]

### Lo peor de cada uno
- **[Skill A]**: [debilidades]
- **[Skill B]**: [debilidades]

### Mi recomendacion
[cual elegir y por que, en espanol simple]
```

### **PARAR** — Esperar decision del usuario.

---

## Flujo 4: Constructor de Skills Hibridos

**Cuando usar**: El usuario quiere combinar las mejores partes de varios skills en uno nuevo.

### Paso 4.1: Identificar fuentes

Si vienen del Flujo 3, usa los skills ya analizados.
Si no, pide URLs de los skills que quiere combinar y descargalos.

### Paso 4.2: Analizar fortalezas de cada skill

```
## Analisis de Componentes

### [Skill A]
- Mejor en: [que hace bien]
- Partes utiles: [secciones o archivos especificos]
- Lo que tomaria: [que elementos combinar]

### [Skill B]
- Mejor en: [que hace bien]
- Partes utiles: [secciones o archivos especificos]
- Lo que tomaria: [que elementos combinar]
```

### **PARAR** — Confirmar con el usuario que partes combinar.

### Paso 4.3: Generar borrador del skill hibrido

Crea un nuevo SKILL.md que combine las mejores partes:
- Frontmatter con nombre y descripcion propuestos
- Instrucciones combinadas de las fuentes seleccionadas
- Lista de scripts y referencias necesarios (si aplica)

### Paso 4.4: Mostrar borrador

```
## Borrador del Skill Hibrido

Nombre propuesto: [nombre]
Descripcion: [descripcion]

### Contenido del SKILL.md:

[mostrar contenido completo del archivo]

### Archivos adicionales que se crearian:
[lista de scripts, referencias, etc. — o "ninguno"]

---
Que te parece? Quieres cambiar algo?
```

### **PARAR** — Esperar aprobacion o ediciones del usuario.

### Paso 4.5: Cuando el usuario apruebe, continuar con Flujo 5 (Instalacion).

---

## Flujo 5: Instalacion Completa

**Cuando usar**: El usuario quiere instalar un skill (nuevo, hibrido, o desde URL).

### Paso 5.1: Elegir ubicacion (SIEMPRE preguntar)

```
Donde quieres instalar este skill?

1. Global — disponible en TODOS tus proyectos
   Ubicacion: ~/.claude/skills/[nombre]/

2. Solo en este proyecto
   Ubicacion: .claude/skills/[nombre]/

Escribe 1 o 2.
```

### **PARAR** — Esperar respuesta.

### Paso 5.2: Verificar si ya existe

Si ya existe un directorio con ese nombre en la ubicacion elegida:

```
Ya existe un skill llamado "[nombre]" en esa ubicacion. Que quieres hacer?

1. Sobreescribir (reemplazar el existente)
2. Renombrar el nuevo (ej: [nombre]-v2)
3. Cancelar
```

### **PARAR** — Esperar respuesta.

### Paso 5.3: Copiar archivos

Seguir los procedimientos de `references/installation-guide.md`.

Mostrar al usuario que se instalo:

```
Archivos instalados:

  ~/.claude/skills/[nombre]/SKILL.md
  ~/.claude/skills/[nombre]/scripts/query.py
  ...

Todo copiado correctamente.
```

### Paso 5.4: Dependencias

Revisar si el skill necesita dependencias:
- Buscar `requirements.txt`, `package.json` en los archivos
- Buscar comandos `pip install`, `npm install`, `brew install` en SKILL.md o scripts

Si hay dependencias:

```
Este skill necesita estas herramientas:

  pip install psycopg2-binary (libreria para PostgreSQL)
  npm install marked (procesador de Markdown)

Quieres que las instale? (si/no)
```

### **PARAR** — Esperar aprobacion.

### Paso 5.5: Configuracion

Si el skill requiere configuracion (archivos .env, .json con credenciales, API keys, etc.):

```
Este skill necesita configuracion:

  Archivo: connections.json
  Necesita: URL de base de datos, usuario, contrasena

Te guio paso a paso? (si/no)
```

### **PARAR** — Guiar configuracion solo si acepta.

### Paso 5.6: Verificacion final

```
## Instalacion Completa

  Skill: [nombre]
  Ubicacion: [ruta completa]
  Archivos: [cantidad] archivos instalados
  Dependencias: [instaladas / no necesarias]
  Configuracion: [completa / no necesaria]

Ya puedes usar el skill con: /[nombre]
```

---

## Flujo 6: Instalacion Rapida

**Cuando usar**: El usuario da una URL directa de GitHub a un skill.

### Paso 6.1: Obtener el skill
Descargar todos los archivos del skill desde la URL.

### Paso 6.2: Revision rapida de seguridad
Ejecutar automaticamente el Flujo 1 (Analisis de Seguridad) y mostrar el veredicto.

### **PARAR** — Esperar aprobacion de seguridad.

### Paso 6.3: Si aprueba, ejecutar Flujo 5 desde el Paso 5.1 (elegir ubicacion).

---

## Flujo 7: Gestion de Fuentes

**Cuando usar**: El usuario quiere ver, agregar, o quitar repos/marketplaces de su lista.

### Listar fuentes

Lee `sources.json` y muestra:

```
## Tus fuentes de skills

### Repos de GitHub
1. Superpowers — github.com/obra/superpowers — [dev, agents, workflows]
2. Claude Templates — github.com/davila7/claude-code-templates — [templates, config, mcp]
3. Composio Skills — github.com/ComposioHQ/awesome-claude-skills — [general, apps, automation]

### Marketplaces
4. Skills.sh — skills.sh — [universal, 67k+]
5. AITMPL — aitmpl.com — [claude-code, templates]

---
Quieres agregar, quitar, o editar alguna fuente?
```

### Agregar fuente

Pide al usuario la URL y un nombre. Edita `sources.json` para agregarla.

### Quitar fuente

Pide al usuario cual quitar (por numero o nombre). Edita `sources.json` para eliminarla.

### **PARAR** despues de cada cambio — confirmar que se guardo correctamente.
