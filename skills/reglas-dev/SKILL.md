---
name: reglas-dev
description: Reglas y buenas prácticas de desarrollo. Usar SIEMPRE que se escriba, edite, o refactorice código en cualquier proyecto. Aplica a código nuevo, bug fixes, features, y refactors.
user-invocable: false
---

## Reglas Obligatorias de Desarrollo

Estas reglas aplican a TODO el código que generes. No son opcionales.

### Arquitectura
- **SoC**: UI solo renderiza, lógica en hooks/lib, datos en services/API routes
- **Inmutabilidad**: Nunca mutar estado directamente — siempre spread/copia (`[...arr, item]`, `{...obj, key: val}`)
- **Wrappers**: Al usar librerías externas, crear wrapper en `lib/` para desacoplar
- **SOLID simplificado**: Una función = una responsabilidad. Composición sobre herencia

### Nomenclatura (TODO en inglés)
- Carpetas: `kebab-case` — Componentes: `PascalCase` — Hooks: `camelCase` — Constantes: `SCREAMING_CASE`
- Booleanos: prefijo `is/has/can/should` (`isLoading`, `hasPermission`)
- Handlers: prefijo `handle` (`handleSubmit`, `handleDeleteTask`)
- Nombres auto-documentados, sin abreviaciones. Si necesitas un comentario para explicar qué hace una variable, renómbrala

### UI/UX
- **Mobile-first**: Diseñar para móvil, adaptar para desktop. Touch targets mínimo 44x44px
- **Design tokens**: Usar variables CSS / clases Tailwind — nunca hardcodear colores ni spacing
- **4 estados obligatorios**: Loading, Empty, Error, Data Overflow en todo componente que muestre datos
- Extraer componente si: se repite, tiene >20 líneas visuales, o tiene lógica interna

### Estado
- Si solo un componente lo necesita → `useState`
- Si se comparte padre-hijo → prop drilling
- Si es global pero cambia poco → Context
- Si debe persistir en URL → search params
- No derivar estado de otro estado — calcular en render con `useMemo`

### Seguridad
- Nunca confiar en datos del cliente — validar TODO en servidor
- Auth en cada API route (`requireTenantContext` o equivalente)
- Autorización granular (no basta con estar autenticado)
- Nunca exponer secrets, DB URLs, o API keys en frontend
- Multi-tenant: SIEMPRE filtrar por `tenantId`

### Performance
- Lazy loading para componentes pesados
- Code splitting — importar solo lo necesario de librerías
- `useMemo` para cómputos costosos, `useCallback` para handlers pasados como props
- DB: paginar, select solo lo necesario, evitar N+1, crear índices

### Errores
- **Nunca silenciar un error** — si no puedes manejarlo, propágalo
- **Early return**: Validar condiciones negativas primero, happy path al final
- Respuestas de error consistentes con `{ error, code }` y status HTTP correcto

### Git
- Commits atómicos — cada commit compila y funciona independientemente
- Formato: `tipo: descripción` (feat, fix, refactor, style, docs, chore)
- Nunca push directo a `main`
- No commitear `.env`, `node_modules/`, `.next/`

### Multi-Agente (Chesterton's Fence)
- Antes de borrar o refactorizar código existente, entender POR QUÉ existía
- Cada cambio debe ser completo y funcional — no dejar TODOs críticos
- Comunicar: QUÉ cambias, POR QUÉ, y QUÉ IMPACTO tiene en otros archivos
