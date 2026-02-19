---
name: security-audit
description: "Auditoria de seguridad completa para aplicaciones web. Cubre OWASP Top 10:2025, analisis de codigo, deteccion de vulnerabilidades, pentesting guiado, headers/cookies/APIs, dependencias, y seguridad de IA/LLM. Usar con: /security-audit [url|directorio|descripcion]"
argument-hint: "[URL de la app | ruta del proyecto | 'full' para auditoria completa]"
---

# Security Audit — Auditoria de Seguridad Completa

## Reglas Generales

1. **Idioma**: Responde en espanol. Terminos tecnicos en ingles entre parentesis si es necesario.
2. **Autorizacion**: Solo auditar aplicaciones que el usuario posee o tiene permiso para probar.
3. **No romper nada**: Nunca ejecutar ataques destructivos. Solo analisis y pruebas controladas.
4. **Evidencia**: Cada hallazgo debe incluir ubicacion exacta, codigo afectado y prueba.
5. **Prioridad**: Reportar hallazgos de critico a informativo.

## Detectar Modo

Analiza `$ARGUMENTS` para determinar que tipo de auditoria ejecutar:

| Input del usuario | Modo |
|---|---|
| URL (localhost, dominio) | Modo 1: Auditoria Externa (headers, endpoints, cookies) |
| Ruta de proyecto o "codigo" | Modo 2: Auditoria de Codigo (analisis estatico) |
| "full" o "completa" | Modo 3: Auditoria Completa (todos los modos) |
| "dependencias" o "deps" | Modo 4: Solo dependencias |
| "api" o "endpoints" | Modo 5: Solo API security |
| "llm" o "ia" o "agente" | Modo 6: Seguridad de IA/LLM |
| Sin argumentos | Mostrar menu de opciones |

### Menu (sin argumentos)

```
Que tipo de auditoria necesitas?

1. Auditoria completa (codigo + endpoints + dependencias + headers)
2. Solo analisis de codigo (OWASP, vulnerabilidades, secrets)
3. Solo endpoints/API (headers, cookies, autenticacion)
4. Solo dependencias (CVEs, versiones vulnerables)
5. Seguridad de IA/LLM (bias, data leakage, prompt injection)

Escribe el numero o describe que necesitas.
```

---

## Modo 1: Auditoria Externa

Analizar la aplicacion desde afuera (como un atacante).

### 1.1 Headers de Seguridad

Verificar que la respuesta HTTP incluya:

| Header | Valor esperado | Riesgo si falta |
|--------|---------------|-----------------|
| `Strict-Transport-Security` | `max-age=31536000; includeSubDomains` | Man-in-the-middle |
| `Content-Security-Policy` | Politica restrictiva (no `unsafe-inline`) | XSS |
| `X-Content-Type-Options` | `nosniff` | MIME sniffing |
| `X-Frame-Options` | `DENY` o `SAMEORIGIN` | Clickjacking |
| `Referrer-Policy` | `strict-origin-when-cross-origin` | Data leakage |
| `Permissions-Policy` | Restringir camera, microphone, geolocation | Abuso de permisos |
| `X-XSS-Protection` | `0` (desactivar, usar CSP) | Falsa seguridad |

```bash
# Comando para verificar headers
curl -sI https://URL | grep -iE "(strict|content-security|x-content|x-frame|referrer|permissions)"
```

### 1.2 Cookies

Verificar que todas las cookies sensibles tengan:

- [ ] `HttpOnly` — No accesibles desde JavaScript
- [ ] `Secure` — Solo se envian por HTTPS
- [ ] `SameSite=Strict` o `Lax` — Proteccion contra CSRF
- [ ] `Path=/` restrictivo
- [ ] Sin datos sensibles en el valor (no tokens en texto plano)

### 1.3 CORS

Verificar configuracion:

```bash
curl -sI -H "Origin: https://evil.com" https://URL | grep -i "access-control"
```

- [ ] No refleja origenes arbitrarios
- [ ] No usa `Access-Control-Allow-Origin: *` con credenciales
- [ ] `Access-Control-Allow-Methods` es restrictivo

### 1.4 SSL/TLS

- [ ] TLS 1.2+ (no SSLv3, TLS 1.0, TLS 1.1)
- [ ] Certificado valido y no expirado
- [ ] HSTS habilitado
- [ ] No cipher suites debiles

---

## Modo 2: Auditoria de Codigo

Analisis estatico del codigo fuente.

### 2.1 OWASP Top 10:2025 — Checklist Rapida

| # | Vulnerabilidad | Que buscar | Prevencion |
|---|---------------|------------|------------|
| A01 | Broken Access Control | Endpoints sin auth, IDOR, falta de verificacion de ownership | Deny by default, verificar auth en cada request |
| A02 | Security Misconfiguration | Debug=true en prod, CORS *, defaults inseguros | Hardening, deshabilitar defaults, minimizar |
| A03 | Supply Chain | Dependencias sin lockfile, versiones sin pinear | Lock versions, verificar integridad, auditar deps |
| A04 | Cryptographic Failures | MD5/SHA1 para passwords, secrets en codigo, sin TLS | Argon2/bcrypt, AES-256-GCM, TLS everywhere |
| A05 | Injection | String concatenation en queries, eval(), exec() | Queries parametrizadas, input validation, safe APIs |
| A06 | Insecure Design | Sin rate limit, sin threat model, sin validacion | Threat modeling, rate limit, design security controls |
| A07 | Auth Failures | Sin MFA, passwords debiles, sesiones largas | MFA, check breached passwords, sesiones cortas |
| A08 | Integrity Failures | CDN sin SRI, deserializacion insegura | SRI, firmar paquetes, safe serialization |
| A09 | Logging Failures | Sin logs de eventos de seguridad, logs con data sensible | Logging estructurado, alertas, no loguear secrets |
| A10 | Exception Handling | Stack traces al usuario, fail-open en errores | Fail-closed, ocultar internals, log con contexto |

### 2.2 Busqueda Automatizada de Vulnerabilidades

Ejecutar estas busquedas en el codigo:

**Secrets hardcodeados:**
```
Grep: (password|secret|api[_-]?key|token|private[_-]?key)\s*[:=]\s*['"][^'"]{8,}['"]
```

**SQL Injection:**
```
Grep: (execute|query|raw)\s*\(.*['"]\s*[\+\$\{]
Grep: f['"](SELECT|INSERT|UPDATE|DELETE)
```

**Command Injection:**
```
Grep: (os\.system|subprocess.*shell\s*=\s*True|exec\(|eval\()
Grep: (child_process|\.exec\(|\.execSync\()
```

**XSS potencial:**
```
Grep: (innerHTML|dangerouslySetInnerHTML|document\.write|v-html)
```

**Deserializacion insegura:**
```
Grep: (pickle\.loads|yaml\.load\(|Marshal\.load|BinaryFormatter|unserialize\()
```

**Fail-open patterns:**
```
Grep: (\.get\(.*\)\s*or\s*['"]|getenv.*\|\||ENV\.fetch.*default)
```

**Debug en produccion:**
```
Grep: (DEBUG\s*=\s*True|debug:\s*true|NODE_ENV.*development)
```

**Credenciales por defecto:**
```
Grep: (admin.*admin|root.*root|password.*password|test.*test)
```

### 2.3 Patrones de Codigo Seguro vs Inseguro

**SQL Injection:**
```python
# INSEGURO
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")

# SEGURO
cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
```

**Command Injection:**
```python
# INSEGURO
os.system(f"convert {filename} output.png")

# SEGURO
subprocess.run(["convert", filename, "output.png"], shell=False)
```

**Password Storage:**
```python
# INSEGURO
hashlib.md5(password.encode()).hexdigest()

# SEGURO
from argon2 import PasswordHasher
PasswordHasher().hash(password)
```

**Access Control:**
```python
# INSEGURO — Sin verificacion de autorizacion
@app.route('/api/user/<user_id>')
def get_user(user_id):
    return db.get_user(user_id)

# SEGURO — Autorizacion en cada request
@app.route('/api/user/<user_id>')
@login_required
def get_user(user_id):
    if current_user.id != user_id and not current_user.is_admin:
        abort(403)
    return db.get_user(user_id)
```

**Error Handling:**
```python
# INSEGURO — Fail-open
def check_permission(user, resource):
    try:
        return auth_service.check(user, resource)
    except Exception:
        return True  # PELIGROSO

# SEGURO — Fail-closed
def check_permission(user, resource):
    try:
        return auth_service.check(user, resource)
    except Exception as e:
        logger.error(f"Auth check failed: {e}")
        return False  # Denegar en error
```

### 2.4 Deteccion de Sharp Edges (APIs Peligrosas)

Buscar APIs donde el camino facil lleva a inseguridad:

**Footguns criptograficos:**
- Parametros de seleccion de algoritmo (`algorithm`, `mode`, `cipher`)
- JWT con `alg: none` o confusion RS256/HS256
- Uso de MD5, SHA1, DES, RC4, ECB en contextos de seguridad

**Defaults peligrosos:**
- `timeout=0` (infinito? inmediato?)
- `verify=False` en requests HTTP
- `shell=True` en subprocess
- `debug=True` como default

**Fallos silenciosos:**
- Funciones que retornan `True` cuando falta una key
- Catch blocks vacios en operaciones de seguridad
- Verificaciones que "pasan" con input malformado

### 2.5 Quirks por Lenguaje

| Lenguaje | Riesgos principales | Buscar |
|----------|---------------------|--------|
| **JavaScript/TS** | Prototype pollution, XSS, eval | `eval()`, `innerHTML`, `__proto__`, `Object.assign(target, userInput)` |
| **Python** | Pickle RCE, shell injection, format strings | `pickle.loads`, `eval()`, `os.system()`, `shell=True` |
| **Java** | Deserialization RCE, XXE, JNDI | `ObjectInputStream`, `Runtime.exec()`, XML parsers sin proteccion |
| **PHP** | Type juggling, file inclusion, object injection | `==` vs `===`, `include($_GET)`, `unserialize()` |
| **Go** | Race conditions, template injection | `template.HTML()`, goroutine data races, `unsafe` package |
| **Ruby** | Mass assignment, YAML RCE, regex DoS | `YAML.load`, `Marshal.load`, `.permit!` |
| **C/C++** | Buffer overflow, use-after-free, format string | `strcpy`, `sprintf`, `gets`, `printf(userInput)` |
| **Rust** | Unsafe blocks, integer overflow en release | `unsafe {}`, `.unwrap()` en input no confiable |
| **Shell** | Command injection, word splitting | Variables sin comillas, `eval`, backticks con user input |

---

## Modo 3: Auditoria Completa

Ejecuta todos los modos en secuencia:

1. **Modo 2**: Auditoria de codigo (primero, para entender la app)
2. **Modo 4**: Dependencias (CVEs conocidos)
3. **Modo 1**: Auditoria externa (si hay URL)
4. **Modo 5**: API security
5. Generar reporte consolidado

---

## Modo 4: Auditoria de Dependencias

### 4.1 Detectar gestor de paquetes

```bash
# Node.js
test -f package.json && npm audit 2>/dev/null
test -f package-lock.json && npm audit --json 2>/dev/null

# Python
test -f requirements.txt && pip audit -r requirements.txt 2>/dev/null

# Go
test -f go.sum && govulncheck ./... 2>/dev/null

# Ruby
test -f Gemfile.lock && bundle audit check 2>/dev/null

# Rust
test -f Cargo.lock && cargo audit 2>/dev/null
```

### 4.2 Verificar

- [ ] Lockfile existe y esta commiteado
- [ ] No hay dependencias con CVEs conocidos (critico/alto)
- [ ] Versiones estan pineadas (no `^` ni `*` en produccion)
- [ ] No hay typosquatting (nombres similares a paquetes populares)
- [ ] Dependencias son necesarias (no hay bloat)
- [ ] No hay dependencias abandonadas (sin updates > 2 anos)

---

## Modo 5: API Security

### 5.1 Autenticacion

- [ ] Tokens con expiracion corta (JWT: 15min access, 7d refresh)
- [ ] Refresh tokens rotados despues de cada uso
- [ ] Rate limiting en login (max 5 intentos / 15 min)
- [ ] Passwords minimo 12 caracteres
- [ ] Check contra listas de passwords comprometidos
- [ ] MFA disponible para operaciones sensibles

### 5.2 Autorizacion

- [ ] Verificacion en CADA endpoint (no solo en middleware global)
- [ ] IDOR protegido (no IDs secuenciales expuestos sin validacion)
- [ ] Privilege escalation revisado (usuario normal no puede acceder a admin)
- [ ] Multi-tenant: SIEMPRE filtrar por tenant/organizacion

### 5.3 Input Validation

- [ ] Validacion server-side en todos los endpoints
- [ ] Content-Type validado
- [ ] Tamano de body limitado
- [ ] Rate limiting por IP y por usuario
- [ ] No aceptar input sin sanitizar en queries, comandos o templates

### 5.4 Response Security

- [ ] No exponer stack traces en errores
- [ ] No incluir informacion de version del servidor
- [ ] Paginacion con limites (no permitir `?limit=999999`)
- [ ] No exponer datos sensibles en responses (filtrar passwords, tokens, IDs internos)

---

## Modo 6: Seguridad de IA/LLM

### 6.1 OWASP Agentic AI (2026)

| Riesgo | Descripcion | Mitigacion |
|--------|-------------|------------|
| ASI01: Goal Hijack | Prompt injection altera objetivos del agente | Sanitizar inputs, limites de objetivos, monitoreo |
| ASI02: Tool Misuse | Herramientas usadas de formas no intencionadas | Minimo privilegio, permisos granulares, validar I/O |
| ASI03: Privilege Abuse | Escalacion de credenciales entre agentes | Tokens cortos y scoped, verificacion de identidad |
| ASI04: Supply Chain | Plugins o MCP servers comprometidos | Verificar firmas, sandbox, allowlist de plugins |
| ASI05: Code Execution | Generacion/ejecucion de codigo inseguro | Sandbox, analisis estatico, aprobacion humana |
| ASI06: Memory Poisoning | Datos corruptos en RAG/contexto | Validar contenido almacenado, segmentar por confianza |
| ASI07: Agent Comms | Spoofing entre agentes | Autenticar, encriptar, verificar integridad |
| ASI08: Cascading Failures | Errores se propagan entre sistemas | Circuit breakers, degradacion graceful, aislamiento |
| ASI09: Trust Exploitation | Ingenieria social via IA | Etiquetar contenido IA, educacion, verificacion |
| ASI10: Rogue Agents | Agentes comprometidos actuando maliciosamente | Monitoreo de comportamiento, kill switches, anomaly detection |

### 6.2 Checklist de Seguridad para Agentes

- [ ] Inputs del agente sanitizados y validados
- [ ] Herramientas operan con minimo privilegio
- [ ] Credenciales son de corta duracion y scoped
- [ ] Plugins de terceros verificados y en sandbox
- [ ] Ejecucion de codigo en entornos aislados
- [ ] Comunicacion entre agentes autenticada y encriptada
- [ ] Circuit breakers entre componentes
- [ ] Aprobacion humana para operaciones sensibles
- [ ] Monitoreo de comportamiento para anomalias
- [ ] Kill switch disponible para sistemas de agentes

### 6.3 Testing de LLM

**Prompt Injection:**
- Probar si el sistema es vulnerable a instrucciones inyectadas en datos
- Verificar que el system prompt no se puede extraer
- Probar bypass de guardrails con tecnicas de divergencia

**Data Leakage:**
- Verificar que el modelo no filtra datos de entrenamiento
- Probar extraccion de PII (informacion personal)
- Verificar que no retiene datos entre sesiones

**Bias Testing:**
- Probar respuestas con diferentes generos, nacionalidades, etnias
- Verificar que no reproduce estereotipos
- Documentar sesgos encontrados

---

## Formato del Reporte

Al terminar cualquier auditoria, generar reporte en este formato:

```
## Reporte de Seguridad

**Proyecto:** [nombre]
**Fecha:** [fecha]
**Tipo de auditoria:** [modo usado]
**Alcance:** [que se audito]

---

### Resumen Ejecutivo
- X hallazgos criticos
- X hallazgos altos
- X hallazgos medios
- X hallazgos bajos
- X informativos

**Veredicto:** [Seguro / Necesita mejoras / Riesgo alto]

---

### Hallazgos Criticos

#### [CRITICO-001] Nombre del hallazgo
- **Ubicacion:** archivo:linea
- **Descripcion:** Que es el problema
- **Impacto:** Que puede pasar si se explota
- **Evidencia:** Codigo o comando que lo demuestra
- **Remediacion:** Como arreglarlo (con ejemplo de codigo)
- **Referencia:** OWASP A0X / CWE-XXX

[Repetir para cada hallazgo]

---

### Hallazgos Altos
[misma estructura]

### Hallazgos Medios
[misma estructura]

### Hallazgos Bajos
[misma estructura]

### Informativos
[misma estructura]

---

### Recomendaciones Priorizadas
1. [Arreglar primero — criticos]
2. [Arreglar pronto — altos]
3. [Planificar — medios]
4. [Cuando se pueda — bajos]

### Proximos Pasos
- [ ] Arreglar hallazgos criticos
- [ ] Re-ejecutar auditoria despues de fixes
- [ ] Implementar monitoreo continuo
```

---

## Clasificacion de Severidad

| Severidad | Criterio | Ejemplo |
|-----------|----------|---------|
| **Critico** | Explotable remotamente, impacto severo | SQL injection en login, secrets en codigo, RCE |
| **Alto** | Facil de explotar, impacto significativo | XSS almacenado, IDOR, auth bypass |
| **Medio** | Dificultad moderada, impacto moderado | CSRF, headers faltantes, cookies inseguras |
| **Bajo** | Dificil de explotar, impacto limitado | Information disclosure menor, version expuesta |
| **Informativo** | Sin impacto directo de seguridad | Mejores practicas, optimizaciones recomendadas |

---

## Herramientas Recomendadas

Si el usuario necesita herramientas externas para profundizar:

| Herramienta | Uso | Instalacion |
|-------------|-----|-------------|
| `npm audit` | Vulnerabilidades en dependencias Node.js | Incluido con npm |
| `pip audit` | Vulnerabilidades en dependencias Python | `pip install pip-audit` |
| `semgrep` | Analisis estatico multi-lenguaje | `pip install semgrep` |
| `trivy` | Escaneo de containers y dependencias | `brew install trivy` |
| `nuclei` | Escaneo de vulnerabilidades web | `brew install nuclei` |
| `sqlmap` | Testing de SQL injection | `pip install sqlmap` |
| `OWASP ZAP` | Proxy de testing web completo | `brew install --cask zap` |

---

## Fuentes y Referencias

Este skill combina conocimiento de:
- [OWASP Top 10:2025](https://owasp.org/Top10/)
- [OWASP ASVS 5.0](https://owasp.org/www-project-application-security-verification-standard/)
- [OWASP Agentic AI Security 2026](https://owasp.org/www-project-agentic-ai-security-initiative/)
- [Trail of Bits Security Skills](https://github.com/trailofbits/skills) (CC BY-SA 4.0)
- [agamm/claude-code-owasp](https://github.com/agamm/claude-code-owasp) (MIT)
- [Eyadkelleh/awesome-claude-skills-security](https://github.com/Eyadkelleh/awesome-claude-skills-security) (MIT)
- [CWE Top 25](https://cwe.mitre.org/top25/)
