# Claude Code Skills Collection

Coleccion curada de skills para [Claude Code](https://docs.anthropic.com/en/docs/claude-code), el CLI de Anthropic. Incluye skills originales en espanol y skills seleccionados de repositorios open source, todos verificados y organizados por categoria.

**24 skills** organizados en **8 categorias** que cubren el ciclo completo de desarrollo de software.

---

## Indice por Categoria

### Flujo de Trabajo (ES)
Skills en espanol para el dia a dia del desarrollo.

| Skill | Comando | Descripcion |
|-------|---------|-------------|
| [deploy](skills/deploy/) | `/deploy` | Hace commit y push a GitHub para deploy automatico en Vercel |
| [fix](skills/fix/) | `/fix` | Describe un bug en espanol y Claude investiga, encuentra la causa y lo arregla |
| [pr](skills/pr/) | `/pr` | Crea un Pull Request en GitHub con titulo y descripcion auto-generados |
| [review](skills/review/) | `/review` | Revisa cambios pendientes buscando errores, seguridad y mejoras antes de deploy |
| [status](skills/status/) | `/status` | Muestra el estado actual del proyecto: rama, cambios pendientes, ultimo commit |
| [resumen](skills/resumen/) | `/resumen` | Resume los cambios de la sesion en espanol simple, sin jerga tecnica |
| [test](skills/test/) | `/test` | Detecta el framework de tests, ejecuta los tests y reporta resultados en espanol |
| [reglas-dev](skills/reglas-dev/) | *(automatico)* | Reglas y buenas practicas de desarrollo. Se aplica automaticamente al escribir codigo |

### Planificacion
Disenar y planificar antes de codificar.

| Skill | Comando | Descripcion |
|-------|---------|-------------|
| [brainstorming](skills/brainstorming/) | `/brainstorming` | Explora ideas, requisitos y diseno antes de implementar. Propone 2-3 enfoques con trade-offs |
| [writing-plans](skills/writing-plans/) | `/writing-plans` | Crea planes de implementacion detallados con tareas bite-sized, TDD y commits frecuentes |
| [executing-plans](skills/executing-plans/) | `/executing-plans` | Ejecuta planes paso a paso en batches con checkpoints de revision |

### Desarrollo
Metodologias de desarrollo probadas.

| Skill | Comando | Descripcion |
|-------|---------|-------------|
| [test-driven-development](skills/test-driven-development/) | `/test-driven-development` | Flujo TDD estricto: Red-Green-Refactor. Test primero, codigo despues |
| [systematic-debugging](skills/systematic-debugging/) | `/systematic-debugging` | Debugging metodico en 4 fases: investigar causa raiz antes de intentar fixes |
| [subagent-driven-development](skills/subagent-driven-development/) | `/subagent-driven-development` | Ejecuta planes con un subagente fresco por tarea + revision en dos etapas |
| [dispatching-parallel-agents](skills/dispatching-parallel-agents/) | `/dispatching-parallel-agents` | Despacha un agente por dominio independiente para resolver problemas en paralelo |

### Calidad
Verificacion y revision de codigo.

| Skill | Comando | Descripcion |
|-------|---------|-------------|
| [verification-before-completion](skills/verification-before-completion/) | `/verification-before-completion` | Exige evidencia antes de afirmar que algo esta completo. Sin atajos |
| [requesting-code-review](skills/requesting-code-review/) | `/requesting-code-review` | Despacha subagente revisor para detectar issues antes de merge |
| [receiving-code-review](skills/receiving-code-review/) | `/receiving-code-review` | Procesa feedback de code review con rigor tecnico, sin acuerdo performativo |

### Git
Flujos de trabajo con Git.

| Skill | Comando | Descripcion |
|-------|---------|-------------|
| [using-git-worktrees](skills/using-git-worktrees/) | `/using-git-worktrees` | Crea worktrees aislados con seleccion inteligente de directorio y verificacion |
| [finishing-a-development-branch](skills/finishing-a-development-branch/) | `/finishing-a-development-branch` | Completa trabajo en branch: merge, PR, mantener o descartar |

### Testing
Testing de aplicaciones.

| Skill | Comando | Descripcion |
|-------|---------|-------------|
| [webapp-testing](skills/webapp-testing/) | `/webapp-testing` | Testing de apps web con Playwright: screenshots, logs, automatizacion de UI |

### Seguridad
Auditoria y testing de seguridad.

| Skill | Comando | Descripcion |
|-------|---------|-------------|
| [security-audit](skills/security-audit/) | `/security-audit` | Auditoria completa: OWASP Top 10:2025, codigo, headers, APIs, dependencias, IA/LLM |

### Herramientas
Constructores y utilidades.

| Skill | Comando | Descripcion |
|-------|---------|-------------|
| [mcp-builder](skills/mcp-builder/) | `/mcp-builder` | Guia completa para crear servidores MCP de alta calidad (Python/TypeScript) |
| [skill-scout](skills/skill-scout/) | `/skill-scout` | Descubre, analiza seguridad, compara e instala skills desde GitHub |

---

## Instalacion

### Instalar todos los skills (global)

```bash
git clone https://github.com/jaslan-creator/skills.git /tmp/skills-install
cp -r /tmp/skills-install/skills/* ~/.claude/skills/
cp -r /tmp/skills-install/lib ~/.claude/skills/superpowers-lib
rm -rf /tmp/skills-install
```

### Instalar un skill individual

```bash
git clone --depth 1 https://github.com/jaslan-creator/skills.git /tmp/skills-install
cp -r /tmp/skills-install/skills/<nombre-del-skill> ~/.claude/skills/
rm -rf /tmp/skills-install
```

### Instalar una categoria completa

```bash
git clone --depth 1 https://github.com/jaslan-creator/skills.git /tmp/skills-install

# Ejemplo: instalar solo skills de planificacion
for skill in brainstorming writing-plans executing-plans; do
  cp -r /tmp/skills-install/skills/$skill ~/.claude/skills/
done

rm -rf /tmp/skills-install
```

---

## Estructura del Repositorio

```
skills/
├── brainstorming/              # Planificacion
│   └── SKILL.md
├── deploy/                     # Flujo de Trabajo (ES)
│   └── SKILL.md
├── dispatching-parallel-agents/ # Desarrollo
│   └── SKILL.md
├── executing-plans/            # Planificacion
│   └── SKILL.md
├── finishing-a-development-branch/ # Git
│   └── SKILL.md
├── fix/                        # Flujo de Trabajo (ES)
│   └── SKILL.md
├── mcp-builder/                # Herramientas
│   ├── SKILL.md
│   ├── LICENSE.txt
│   ├── reference/              # Guias de implementacion
│   └── scripts/                # Evaluacion y conexiones
├── pr/                         # Flujo de Trabajo (ES)
│   └── SKILL.md
├── receiving-code-review/      # Calidad
│   └── SKILL.md
├── reglas-dev/                 # Flujo de Trabajo (ES)
│   └── SKILL.md
├── requesting-code-review/     # Calidad
│   ├── SKILL.md
│   └── code-reviewer.md
├── resumen/                    # Flujo de Trabajo (ES)
│   └── SKILL.md
├── review/                     # Flujo de Trabajo (ES)
│   └── SKILL.md
├── security-audit/             # Seguridad
│   └── SKILL.md
├── skill-scout/                # Herramientas
│   ├── SKILL.md
│   ├── sources.json
│   └── references/
├── status/                     # Flujo de Trabajo (ES)
│   └── SKILL.md
├── subagent-driven-development/ # Desarrollo
│   ├── SKILL.md
│   ├── implementer-prompt.md
│   ├── spec-reviewer-prompt.md
│   └── code-quality-reviewer-prompt.md
├── systematic-debugging/       # Desarrollo
│   ├── SKILL.md
│   ├── root-cause-tracing.md
│   ├── defense-in-depth.md
│   ├── condition-based-waiting.md
│   └── find-polluter.sh
├── test/                       # Flujo de Trabajo (ES)
│   └── SKILL.md
├── test-driven-development/    # Desarrollo
│   ├── SKILL.md
│   └── testing-anti-patterns.md
├── using-git-worktrees/        # Git
│   └── SKILL.md
├── verification-before-completion/ # Calidad
│   └── SKILL.md
├── webapp-testing/             # Testing
│   ├── SKILL.md
│   ├── LICENSE.txt
│   ├── examples/
│   └── scripts/
└── writing-plans/              # Planificacion
    └── SKILL.md
lib/
└── skills-core.js              # Libreria compartida (superpowers)
```

---

## Flujo de Trabajo Recomendado

Estos skills estan disenados para funcionar juntos. El flujo ideal de desarrollo:

```
brainstorming → writing-plans → using-git-worktrees → subagent-driven-development → verification-before-completion → finishing-a-development-branch
```

1. **brainstorming** — Explora la idea, propone enfoques, obtiene aprobacion
2. **writing-plans** — Convierte el diseno aprobado en plan de implementacion
3. **using-git-worktrees** — Crea workspace aislado para la feature
4. **subagent-driven-development** — Ejecuta el plan con subagentes + reviews
5. **verification-before-completion** — Verifica con evidencia antes de cerrar
6. **finishing-a-development-branch** — Merge, PR, o cleanup del branch

Para el dia a dia rapido, usa los skills en espanol:
- `/fix algo no funciona` → investiga y arregla
- `/test` → ejecuta tests
- `/review` → revisa cambios
- `/deploy` → commit + push
- `/status` → donde estamos
- `/resumen` → que hicimos hoy

---

## Origenes

| Skills | Origen | Licencia |
|--------|--------|----------|
| deploy, fix, pr, review, status, resumen, test, reglas-dev, skill-scout, security-audit | Originales (jaslan-creator) | MIT |
| brainstorming, writing-plans, executing-plans, test-driven-development, systematic-debugging, subagent-driven-development, dispatching-parallel-agents, verification-before-completion, requesting-code-review, receiving-code-review, using-git-worktrees, finishing-a-development-branch | [obra/superpowers](https://github.com/obra/superpowers) | MIT |
| webapp-testing, mcp-builder | [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) | Apache 2.0 |

---

## Licencia

MIT License. Ver [LICENSE](LICENSE) para detalles.

Los skills de terceros mantienen sus licencias originales (ver LICENSE.txt dentro de cada skill).
