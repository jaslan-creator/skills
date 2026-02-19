# Guia de Instalacion de Skills

Procedimientos detallados para instalar skills desde diferentes fuentes.

## Paso 1: Determinar ubicacion

Preguntar SIEMPRE al usuario. Nunca asumir.

### Global (todos los proyectos)
```bash
mkdir -p ~/.claude/skills/{nombre-del-skill}
```

### Por proyecto (solo proyecto actual)
```bash
mkdir -p .claude/skills/{nombre-del-skill}
```

## Paso 2: Verificar si ya existe

```bash
ls -la {ubicacion-elegida}/{nombre-del-skill}/ 2>/dev/null
```

Si existe, preguntar al usuario: sobreescribir, renombrar, o cancelar.

## Paso 3: Copiar archivos segun la fuente

### Desde repos de GitHub

**Metodo A — Archivos individuales con gh CLI (preferido, mas rapido):**

```bash
# Obtener lista de archivos del skill
gh api repos/{owner}/{repo}/git/trees/main?recursive=1 --jq '.tree[] | select(.path | startswith("{ruta-del-skill}/")) | .path'

# Descargar cada archivo
gh api repos/{owner}/{repo}/contents/{ruta-del-archivo} --jq '.content' | base64 -d > {destino}/{nombre-archivo}
```

**Metodo B — curl con URL raw (si gh no esta disponible):**

```bash
# Descargar archivo individual
curl -sL "https://raw.githubusercontent.com/{owner}/{repo}/main/{ruta}" -o "{destino}/{nombre}"
```

**Metodo C — git sparse-checkout (para skills con muchos archivos):**

```bash
# Clonar solo lo necesario
git clone --depth 1 --filter=blob:none --sparse "https://github.com/{owner}/{repo}.git" /tmp/skill-scout-temp
cd /tmp/skill-scout-temp
git sparse-checkout set {ruta-del-skill}
cp -r {ruta-del-skill}/* {destino}/
rm -rf /tmp/skill-scout-temp
```

### Desde skills.sh

```bash
# Instalar usando el CLI de skills.sh
npx skillsadd {owner}/{skill-name}
```

Nota: Verificar donde se instala por defecto y mover si es necesario a la ubicacion elegida por el usuario.

### Desde aitmpl.com

```bash
# Instalar usando el CLI de aitmpl
npx claude-code-templates@latest
```

Nota: Este CLI es interactivo. Guiar al usuario en la seleccion.

### Desde un skill hibrido (generado por Flujo 4)

El contenido ya esta en memoria. Simplemente escribir los archivos:
- Escribir SKILL.md con el contenido generado
- Crear carpetas adicionales si el hibrido incluye scripts o referencias

## Paso 4: Crear estructura de subdirectorios si es necesario

Algunos skills tienen subcarpetas. Crearlas antes de copiar:

```bash
# Si el skill tiene scripts/
mkdir -p {destino}/scripts

# Si tiene references/
mkdir -p {destino}/references

# Si tiene assets/
mkdir -p {destino}/assets
```

## Paso 5: Verificar integridad

Despues de copiar, verificar:

```bash
# Verificar que SKILL.md existe
test -f {destino}/SKILL.md && echo "SKILL.md existe" || echo "ERROR: SKILL.md no encontrado"

# Verificar que tiene frontmatter valido (las primeras lineas deben ser ---)
head -1 {destino}/SKILL.md
```

Checklist de verificacion:
- [ ] SKILL.md existe
- [ ] SKILL.md tiene frontmatter con `name:` y `description:`
- [ ] Todos los archivos referenciados en SKILL.md existen
- [ ] Si hay scripts, tienen permisos de ejecucion (`chmod +x`)

## Paso 6: Detectar dependencias

Buscar en los archivos del skill:

### Python
```bash
# Buscar requirements.txt
test -f {destino}/requirements.txt && echo "Encontrado: requirements.txt"

# Buscar pip install en archivos
grep -r "pip install" {destino}/ 2>/dev/null
```

### Node.js
```bash
# Buscar package.json
test -f {destino}/package.json && echo "Encontrado: package.json"

# Buscar npm install en archivos
grep -r "npm install" {destino}/ 2>/dev/null
```

### Sistema
```bash
# Buscar brew install en archivos
grep -r "brew install" {destino}/ 2>/dev/null
```

### PARAR — Mostrar al usuario que dependencias se encontraron y pedir aprobacion antes de instalar.

## Paso 7: Instalar dependencias (con aprobacion)

### Python
```bash
pip install -r {destino}/requirements.txt
```

### Node.js
```bash
cd {destino} && npm install
```

### Sistema (macOS)
```bash
brew install {paquete}
```

Verificar instalacion exitosa despues de cada comando.

## Paso 8: Detectar necesidades de configuracion

Buscar en los archivos del skill:

```bash
# Variables de entorno referenciadas
grep -rE '\$[A-Z_]+|process\.env\.|os\.environ|os\.getenv' {destino}/ 2>/dev/null

# Archivos de configuracion mencionados
grep -rE '\.env|\.json|\.yaml|\.toml|config|connections' {destino}/SKILL.md 2>/dev/null
```

### PARAR — Si hay configuracion necesaria, ofrecer guiar al usuario paso a paso.

## Paso 9: Guiar configuracion (si acepta)

Para cada archivo de configuracion requerido:

1. Explicar que es y para que sirve (en espanol simple)
2. Si existe un archivo de ejemplo (*.example, *.sample), usarlo como base
3. Pedir al usuario cada valor necesario uno por uno
4. Crear el archivo de configuracion con los valores proporcionados
5. Si contiene credenciales, establecer permisos restrictivos:
   ```bash
   chmod 600 {archivo-de-config}
   ```
6. Verificar que la configuracion es valida

## Paso 10: Verificacion final

Mostrar resumen al usuario:

```
## Instalacion Completa

  Skill: {nombre}
  Ubicacion: {ruta completa}
  Archivos: {cantidad} archivos instalados
  Dependencias: {instaladas / no necesarias}
  Configuracion: {completa / no necesaria}

Ya puedes usar el skill con: /{nombre}
```

## Limpieza

Si se usaron archivos temporales durante la instalacion:

```bash
rm -rf /tmp/skill-scout-temp 2>/dev/null
```

## Errores Comunes

| Error | Causa | Solucion |
|---|---|---|
| "Not Found" (404) | Repo o archivo no existe | Verificar URL, puede ser privado |
| "Forbidden" (403) | Rate limiting o repo privado | Esperar 1 hora o autenticar con `gh auth login` |
| "Permission denied" | Sin permisos de escritura | Verificar permisos del directorio destino |
| "Command not found: gh" | GitHub CLI no instalado | Usar curl como alternativa o instalar con `brew install gh` |
| "Command not found: npx" | Node.js no instalado | Instalar Node.js desde nodejs.org o con `brew install node` |
