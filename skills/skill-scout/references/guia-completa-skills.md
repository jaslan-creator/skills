# Guia Completa: Skills en Claude Code

Documento de referencia sobre que son los skills, como funcionan, como se instalan, seguridad, y como usar skill-scout.

---

## Que son los Skills

Los skills son **archivos de instrucciones en markdown** que le ensenan a Claude a hacer tareas especificas de forma estandarizada. Piensa en ellos como "recetas" que Claude sigue.

Cada skill es una carpeta con al menos un archivo `SKILL.md` dentro:

```
nombre-del-skill/
└── SKILL.md          ← archivo requerido (instrucciones)
```

Los skills mas complejos pueden tener archivos adicionales:

```
nombre-del-skill/
├── SKILL.md              ← instrucciones (requerido)
├── scripts/              ← codigo ejecutable (opcional)
│   └── script.py
├── references/           ← documentacion extra (opcional)
│   └── detalle.md
├── assets/               ← recursos (opcional)
└── requirements.txt      ← dependencias (opcional)
```

---

## Donde se instalan

Hay dos niveles de instalacion:

| Nivel | Ruta | Alcance | Cuando usarlo |
|---|---|---|---|
| **Global** | `~/.claude/skills/nombre/` | Todos los proyectos | Skills que usas siempre (deploy, test, review) |
| **Proyecto** | `.claude/skills/nombre/` | Solo ese proyecto | Skills especificos de un proyecto |

Si un skill existe en ambos niveles con el mismo nombre, el global tiene prioridad.

---

## El archivo SKILL.md

### Estructura basica

```yaml
---
name: nombre-del-skill
description: "Descripcion de que hace y cuando usarlo"
---

# Titulo del Skill

Instrucciones que Claude sigue cuando se activa este skill.

## Cuando usar
- Caso 1
- Caso 2

## Como funciona
1. Paso 1
2. Paso 2
3. Paso 3
```

### Campos del frontmatter (YAML)

| Campo | Requerido | Que hace |
|---|---|---|
| `name` | Si | Identificador del skill (letras, numeros, guiones) |
| `description` | Si | Describe que hace. Claude usa esto para decidir cuando activarlo |
| `disable-model-invocation` | No | Si es `true`, solo el usuario puede activarlo (no Claude automaticamente) |
| `user-invocable` | No | Si es `false`, solo Claude puede usarlo (no el usuario directamente) |
| `argument-hint` | No | Muestra pistas de uso al escribir `/nombre-del-skill` |

### Como se activan

- **Automaticamente**: Claude lee la `description` y si tu pedido coincide, lo usa solo
- **Manualmente**: Escribes `/nombre-del-skill` en Claude Code
- **Con argumentos**: `/nombre-del-skill argumento` — el skill recibe `$ARGUMENTS`

---

## Como instalar un skill manualmente

### Skill simple (solo SKILL.md)

```bash
# 1. Crear carpeta
mkdir -p ~/.claude/skills/nombre-del-skill

# 2. Crear SKILL.md (o copiar el contenido)
# Pegar el contenido del SKILL.md en esa carpeta

# 3. Listo — Claude Code lo detecta automaticamente
```

### Skill complejo (con scripts, dependencias, etc.)

```bash
# 1. Crear carpeta con subdirectorios
mkdir -p ~/.claude/skills/nombre-del-skill/scripts

# 2. Copiar todos los archivos del skill

# 3. Instalar dependencias si las tiene
pip install -r ~/.claude/skills/nombre-del-skill/requirements.txt

# 4. Configurar si necesita (archivos .json, .env, etc.)

# 5. Dar permisos a scripts si es necesario
chmod +x ~/.claude/skills/nombre-del-skill/scripts/*.sh
```

---

## Seguridad

### Niveles de riesgo

| Tipo de archivo | Riesgo | Por que |
|---|---|---|
| `.md` (instrucciones) | Bajo | Es solo texto, no se ejecuta |
| `.json`, `.yaml` (configs) | Bajo | Configuracion, no ejecutable |
| `.py`, `.sh`, `.js` (scripts) | Medio-Alto | Se ejecuta en tu maquina |
| Dependencias (pip, npm) | Medio | Paquetes de terceros |
| Binarios (.exe, .bin) | Alto | No se pueden analizar |

### Reglas de seguridad

1. **Solo instala de fuentes conocidas** — repos populares con muchas estrellas
2. **Revisa el codigo antes de instalar** — especialmente scripts
3. **Claude Code pide permiso** — antes de ejecutar cualquier comando
4. **Cuidado con repos random** — si alguien te manda un link por redes sociales, desconfia
5. **Usa `/skill-scout analizar URL`** — para revisar seguridad automaticamente

### Senales de alerta

- Scripts que descargan cosas de internet sin explicar
- Acceso a archivos sensibles (~/.ssh, ~/.aws, credenciales)
- Codigo ofuscado o ilegible
- Envio de datos a servidores desconocidos
- Nombres de paquetes parecidos a populares (typosquatting)

---

## Fuentes de skills disponibles

### Repos de GitHub

| Repo | Estrellas | Skills | Enfoque |
|---|---|---|---|
| [Superpowers](https://github.com/obra/superpowers) | 55K | 14 | Desarrollo, agentes, workflows |
| [Claude Code Templates](https://github.com/davila7/claude-code-templates) | 21K | 100+ | Templates, config, MCP |
| [Awesome Claude Skills](https://github.com/ComposioHQ/awesome-claude-skills) | 36K | 32+ | General, apps, automatizacion |

### Marketplaces web

| Sitio | Skills | Plataformas | Instalacion |
|---|---|---|---|
| [skills.sh](https://skills.sh) | 67,000+ | 20+ IAs (Claude, Cursor, Copilot, etc.) | `npx skillsadd` |
| [aitmpl.com](https://aitmpl.com) | 100+ | Claude Code | `npx claude-code-templates@latest` |

### Estos repos y marketplaces estan configurados en:
```
~/.claude/skills/skill-scout/sources.json
```

Para agregar o quitar fuentes: `/skill-scout fuentes`

---

## Skill Scout — Tu gestor de skills

### Que es

Un skill que crearmos para gestionar todo el ciclo de vida de los skills: buscar, analizar seguridad, comparar, combinar, e instalar.

### Donde esta

```
~/.claude/skills/skill-scout/
├── SKILL.md                              ← Orquestador principal
├── sources.json                          ← Tus fuentes (repos + marketplaces)
└── references/
    ├── security-checklist.md             ← Checklist de seguridad detallada
    ├── comparison-framework.md           ← Framework de comparacion
    ├── installation-guide.md             ← Guia de instalacion multi-fuente
    └── guia-completa-skills.md           ← Este documento
```

### Como usarlo

| Comando | Que hace |
|---|---|
| `/skill-scout` | Muestra menu de opciones |
| `/skill-scout fuentes` | Lista tus repos y marketplaces |
| `/skill-scout analizar URL` | Analiza seguridad de un skill |
| `/skill-scout necesito algo para X` | Busca skills en todas tus fuentes |
| `/skill-scout comparar` | Compara 2+ skills lado a lado |
| `/skill-scout combinar` | Crea un skill hibrido con lo mejor de varios |
| `/skill-scout instalar URL` | Instala un skill paso a paso |
| `/skill-scout URL` | Instalacion rapida (seguridad + instalar) |

### Los 7 flujos

1. **Analisis de seguridad** — Revisa cada archivo, da veredicto: SEGURO / CON PRECAUCION / PELIGROSO
2. **Descubrimiento** — Busca en todos tus repos y marketplaces segun tu necesidad
3. **Comparacion** — Tabla lado a lado con 6 criterios: funcionalidad, complejidad, calidad, seguridad, mantenimiento, compatibilidad
4. **Constructor de hibridos** — Combina las mejores partes de varios skills en uno nuevo
5. **Instalacion completa** — Paso a paso: ubicacion, archivos, dependencias, configuracion
6. **Instalacion rapida** — URL directa: seguridad automatica + instalar
7. **Gestion de fuentes** — Agregar, listar, quitar repos y marketplaces

**Regla clave**: Cada flujo se detiene y pide tu aprobacion antes de avanzar al siguiente paso.

---

## Portabilidad a otros IAs e IDEs

Las instrucciones de un skill son markdown — funcionan en cualquier IA. Lo que cambia es donde se ponen:

| Plataforma | Donde poner el skill | Como se activa |
|---|---|---|
| **Claude Code** | `~/.claude/skills/nombre/SKILL.md` | `/nombre` (automatico) |
| **Cursor** | `.cursor/rules/nombre.md` | Se lee como contexto automatico |
| **Windsurf** | `.windsurfrules` o `.md` en el proyecto | Se lee como contexto automatico |
| **ChatGPT** | Custom Instructions o crear un GPT | Copiar/pegar instrucciones |
| **Otros IAs** | Pasar como contexto/prompt | Copiar/pegar instrucciones |

**Lo que es portable**: Las instrucciones de analisis, comparacion, decision (el texto).
**Lo que NO es portable**: Comandos de terminal (git, curl, npm). Claude Code y Cursor los ejecutan. ChatGPT no.

---

## API de GitHub — Limites y costos

| Aspecto | Detalle |
|---|---|
| **Costo** | Gratis |
| **Sin autenticar** | 60 peticiones/hora |
| **Con `gh auth login`** | 5,000 peticiones/hora |
| **Busqueda tipica (5 repos)** | ~25 peticiones |
| **Descargar 1 skill** | ~5 peticiones |

Para autenticarse (gratis, recomendado):
```bash
gh auth login
```

---

## Skills que ya tienes instalados

Ubicacion: `~/.claude/skills/`

| Skill | Que hace | Invocacion |
|---|---|---|
| `deploy` | Commit + push a GitHub para deploy en Vercel | `/deploy` |
| `fix` | Investiga y arregla bugs que describes | `/fix descripcion` |
| `pr` | Crea Pull Requests en GitHub | `/pr [rama-base]` |
| `reglas-dev` | Reglas de desarrollo (se aplica automaticamente) | Automatico |
| `resumen` | Resume los cambios de la sesion en espanol simple | `/resumen` |
| `review` | Revisa cambios pendientes antes de deploy | `/review` |
| `status` | Muestra rama, cambios pendientes, ultimo commit | `/status` |
| `test` | Detecta framework de tests y los ejecuta | `/test [filtro]` |
| `skill-scout` | Gestor inteligente de skills | `/skill-scout` |

---

## Glosario

| Termino | Que significa |
|---|---|
| **Skill** | Archivo de instrucciones que Claude sigue para hacer una tarea |
| **SKILL.md** | El archivo principal de un skill (requerido) |
| **Frontmatter** | La seccion YAML al inicio del SKILL.md (entre `---`) con nombre y descripcion |
| **Global** | Instalado en `~/.claude/skills/` — funciona en todos los proyectos |
| **Por proyecto** | Instalado en `.claude/skills/` — solo funciona en ese proyecto |
| **Marketplace** | Sitio web donde puedes buscar y descargar skills |
| **Hibrido** | Un skill nuevo creado combinando partes de varios skills existentes |
| **Frontmatter** | Metadatos al inicio del SKILL.md entre lineas `---` |
| **Progressive disclosure** | Tecnica de mantener el SKILL.md corto y poner detalles en archivos de referencia |
| **$ARGUMENTS** | Placeholder que recibe lo que el usuario escribe despues de `/nombre-del-skill` |
| **Typosquatting** | Paquetes maliciosos con nombres parecidos a populares (ej: `reqeusts` vs `requests`) |
