---
name: oss-project-review
description: Evalúa la salud de un proyecto open source (repo GitHub, paquete npm/pip, o URL). Genera un brief estructurado con semáforo por categoría. Usar cuando el usuario quiera evaluar un nuevo proyecto, librería, o dependencia antes de usarlo.
metadata:
  author: Muno Labs
  version: 0.2
---

# OSS Project Review

Genera un brief de evaluación rápida de un proyecto open source. El objetivo es responder: **¿de qué se trata el proyecto? ¿vale la pena usar esto? ¿qué riesgos tiene?**

Estas tres preguntas son la brújula de todo el flujo. Cada dato que se recopila debería contribuir a responder al menos una de ellas. Si un dato no aporta a ninguna, no vale la pena buscarlo.

## Trigger

Usar cuando el usuario pida:
- `/oss-project-review <repo o paquete>`
- "revisame este proyecto"
- "qué tan sano está este repo"
- "quiero usar X, lo revisas"
- "dame un health check de este paquete"

---

## Paso 0: Detectar entorno de ejecución

Antes de recopilar datos, determinar qué herramientas están disponibles. Esto es importante porque el skill puede correr en entornos muy distintos (Claude Code con red, Claude.ai sin red en bash pero con web_search, etc.).

```bash
# Test rápido de conectividad
curl -s --max-time 5 "https://api.github.com/rate_limit" > /dev/null 2>&1 && echo "NETWORK=available" || echo "NETWORK=unavailable"
```

**Si la red está disponible en bash** → usar los comandos `curl` documentados abajo.
**Si la red NO está disponible** → usar las herramientas del host (`web_search`, `web_fetch`) para obtener la misma información. Adaptar las queries así:
- GitHub data: `web_fetch("https://api.github.com/repos/OWNER/REPO")` o `web_search("OWNER/REPO github stars forks")` y extraer del HTML
- npm data: `web_fetch("https://registry.npmjs.org/PACKAGE_NAME")` o `web_search("PACKAGE_NAME npm weekly downloads")`
- PyPI data: `web_fetch("https://pypi.org/pypi/PACKAGE_NAME/json")`
- Seguridad: `web_search("PACKAGE_NAME CVE vulnerability")`

El objetivo es el mismo independientemente del método: reunir stars, actividad, licencia, dependencias, seguridad y mantenedor. No dejar de evaluar una categoría solo porque un comando curl falló.

---

## Paso 1: Identificar el proyecto

Determinar qué tipo de input es:
- **GitHub URL**: `github.com/owner/repo` → usar GitHub API
- **npm package**: nombre sin `/` → usar `https://registry.npmjs.org/<package>`
- **PyPI package**: nombre + contexto Python → usar `https://pypi.org/pypi/<package>/json`
- **Solo nombre**: intentar resolver con búsqueda web

**Si el proyecto no se encuentra** (404, nombre incorrecto, repo privado): informar al usuario claramente qué se intentó y qué falló. Preguntar si el nombre es correcto o si puede proporcionar una URL directa. No inventar datos ni asumir valores.

## Paso 2: Recopilar datos

Ejecutar en paralelo cuando sea posible:

#### GitHub (si aplica)
```bash
# Datos básicos del repo
curl -s "https://api.github.com/repos/OWNER/REPO" | python3 -c "
import json, sys
d = json.load(sys.stdin)
print(f'Stars: {d[\"stargazers_count\"]}')
print(f'Forks: {d[\"forks_count\"]}')
print(f'Open issues: {d[\"open_issues_count\"]}')
print(f'Last push: {d[\"pushed_at\"]}')
print(f'Created: {d[\"created_at\"]}')
print(f'License: {d.get(\"license\", {}).get(\"spdx_id\", \"None\")}')
print(f'Description: {d[\"description\"]}')
print(f'Language: {d[\"language\"]}')
print(f'Archived: {d[\"archived\"]}')
print(f'Fork: {d[\"fork\"]}')
"

# Contributors activos (últimos 30 días)
# Por qué 30 días: es una ventana lo suficientemente corta para detectar abandono
# pero lo suficientemente larga para no penalizar proyectos estables con releases espaciados.
curl -s "https://api.github.com/repos/OWNER/REPO/commits?since=$(date -v-30d +%Y-%m-%dT%H:%M:%SZ 2>/dev/null || date -d '30 days ago' +%Y-%m-%dT%H:%M:%SZ)&per_page=100" | python3 -c "
import json, sys
commits = json.load(sys.stdin)
authors = set(c['commit']['author']['name'] for c in commits if c.get('commit'))
print(f'Commits last 30d: {len(commits)}')
print(f'Unique authors last 30d: {len(authors)}')
"

# Último release
curl -s "https://api.github.com/repos/OWNER/REPO/releases/latest" | python3 -c "
import json, sys
d = json.load(sys.stdin)
print(f'Latest release: {d.get(\"tag_name\", \"N/A\")} ({d.get(\"published_at\", \"N/A\")})')
"
```

#### npm (si aplica)
```bash
curl -s "https://registry.npmjs.org/PACKAGE_NAME" | python3 -c "
import json, sys
d = json.load(sys.stdin)
latest = d['dist-tags']['latest']
v = d['versions'][latest]
print(f'Latest version: {latest}')
print(f'License: {v.get(\"license\", \"N/A\")}')
deps = v.get('dependencies', {})
dev_deps = v.get('devDependencies', {})
print(f'Direct dependencies: {len(deps)}')
print(f'Dev dependencies: {len(dev_deps)}')
print(f'Dependencies: {list(deps.keys())}')
"

# Descargas semanales + mensuales
# Por qué ambas: la semanal puede tener picos artificiales (bots, CI);
# la mensual da una mejor idea de adopción real. Comparar ambas ayuda a detectar anomalías.
curl -s "https://api.npmjs.org/downloads/point/last-week/PACKAGE_NAME" | python3 -c "
import json, sys; d = json.load(sys.stdin); print(f'Weekly downloads: {d.get(\"downloads\", \"N/A\")}')
"
curl -s "https://api.npmjs.org/downloads/point/last-month/PACKAGE_NAME" | python3 -c "
import json, sys; d = json.load(sys.stdin); print(f'Monthly downloads: {d.get(\"downloads\", \"N/A\")}')
"
```

#### PyPI (si aplica)
```bash
curl -s "https://pypi.org/pypi/PACKAGE_NAME/json" | python3 -c "
import json, sys
d = json.load(sys.stdin)
info = d['info']
print(f'Latest version: {info[\"version\"]}')
print(f'License: {info.get(\"license\", \"N/A\")}')
print(f'Requires: {info.get(\"requires_dist\", [])}')
print(f'Home page: {info.get(\"home_page\", \"N/A\")}')
print(f'Author: {info.get(\"author\", \"N/A\")}')
"
```

#### Seguridad
```bash
# CVEs conocidos — buscar en OSV database
# Por qué OSV: es la base de datos abierta más completa, cubre npm, PyPI, Go, Rust, etc.
# y es mantenida por Google. No requiere auth y tiene buena cobertura de advisories de GitHub.
curl -s -X POST "https://api.osv.dev/v1/query" \
  -H "Content-Type: application/json" \
  -d '{"package": {"name": "PACKAGE_NAME", "ecosystem": "PyPI"}}' | python3 -c "
import json, sys
d = json.load(sys.stdin)
vulns = d.get('vulns', [])
print(f'Known CVEs/vulnerabilities: {len(vulns)}')
for v in vulns[:3]:
    print(f'  - {v.get(\"id\")}: {v.get(\"summary\", \"\")[:80]}')
"
# Cambiar ecosystem a "npm" para paquetes npm
```

### Paso 2.5: Preguntar formato de salida

Antes de generar el brief, preguntar al usuario cómo prefiere recibirlo:

> *"¿En qué formato querés el review?"*
> 1. **Chat** — directamente en la conversación
> 2. **Markdown (.md)** — archivo descargable, ideal para guardar en el repo o en docs
> 3. **HTML visual** — reporte visual con semáforos de colores, guardado como archivo .html

Si el usuario no responde o dice "como sea", usar **Chat** como default.

### Paso 3: Generar el brief

Producir un brief con este formato exacto. Adaptar la presentación al formato elegido por el usuario (ver sección "Formatos de salida" más abajo).

---

## 🔍 OSS Review: `<nombre del proyecto>`

> <descripción de 1 línea>

**Evaluado**: <fecha>
**Fuente**: <URL del repo o paquete>

---

### 📊 Resumen

| Categoría | Estado | Detalle |
|-----------|--------|---------|
| ⭐ Comunidad | 🟢/🟡/🔴 | Stars, forks, contributors |
| 🕐 Actividad | 🟢/🟡/🔴 | Último commit, último release |
| 🔒 Seguridad | 🟢/🟡/🔴 | CVEs conocidos, historial |
| 📦 Dependencias | 🟢/🟡/🔴 | Cantidad directas + transitivas |
| ⚖️ Licencia | 🟢/🟡/🔴 | Tipo y restricciones |
| 🏢 Mantenedor | 🟢/🟡/🔴 | Empresa/persona, activo/abandonado |

**Veredicto general**: 🟢 Usar / 🟡 Usar con precaución / 🔴 Evitar

---

### 🟢 Puntos fuertes
- ...

### ⚠️ Riesgos / Red flags
- ...

### 💡 Recomendación
<1-2 párrafos con la recomendación concreta>

**Alternativas** (si las hay): ...

---

## Criterios de semáforo

Estos umbrales son defaults razonables, no reglas absolutas. Un proyecto de nicho con 200 stars pero 15 contributors activos y backing de una empresa puede ser verde. Un proyecto con 5K stars pero sin commits en 8 meses puede ser rojo. Usar el contexto del ecosistema y el caso de uso del usuario para ajustar el juicio.

### ⭐ Comunidad
Los stars y forks son proxies de adopción — no garantizan calidad, pero un proyecto sin comunidad significa que si falla, el usuario está solo.
- 🟢 >1K stars, múltiples contributors activos, empresa detrás
- 🟡 100-1K stars, 1-2 maintainers activos
- 🔴 <100 stars, 1 persona, sin actividad reciente

### 🕐 Actividad
La actividad reciente indica que alguien está al volante. Un proyecto sin commits recientes probablemente no va a parchear un bug crítico que afecte al usuario.
- 🟢 Commit en <30 días, release en <6 meses
- 🟡 Commit en <6 meses, release en <1 año
- 🔴 Sin commits en >6 meses o repo archivado

### 🔒 Seguridad
Las vulnerabilidades conocidas son el riesgo más concreto y medible. Un CVE sin parchear en una dependencia directa es una bomba de tiempo en producción.
- 🟢 0 CVEs conocidos, security policy documentada
- 🟡 CVEs menores conocidos y parchados, sin historial de supply chain attacks
- 🔴 CVEs críticos sin parchear, historial de supply chain attacks, mantenedor desconocido

### 📦 Dependencias
Cada dependencia es superficie de ataque y punto de fallo. No es solo cantidad — es que cada dep transitiva es código que el usuario ejecuta sin haberlo revisado. Menos deps = menos riesgo de supply chain attack y menos posibilidad de breaking changes en cascada.
- 🟢 <10 dependencias directas, pocas transitivas
- 🟡 10-30 dependencias directas
- 🔴 >30 dependencias directas, o dependencias con historial de vulnerabilidades

### ⚖️ Licencia
La licencia determina qué se puede hacer legalmente con el código. Un proyecto sin licencia es técnicamente "todos los derechos reservados" — usarlo en producción es un riesgo legal real.
- 🟢 MIT, Apache 2.0, BSD — uso libre
- 🟡 LGPL, MPL — verificar compatibilidad con el proyecto del usuario
- 🔴 GPL, AGPL, Comercial, Sin licencia — riesgo legal

### 🏢 Mantenedor
El "bus factor" importa: si el único maintainer desaparece, el proyecto muere. Un equipo o empresa detrás significa que hay incentivos y recursos para mantenerlo vivo más allá de una sola persona.
- 🟢 Empresa conocida o proyecto con +3 maintainers activos
- 🟡 1-2 maintainers activos, sin empresa detrás
- 🔴 1 persona, sin actividad, o empresa desconocida sin track record

---

## Filosofía de evaluación

Inspirado en el enfoque de Andrej Karpathy: **preferir escribir funcionalidad propia (con LLMs) sobre depender de paquetes cuando la función es simple y acotada**.

Preguntar siempre: ¿podría el usuario "yoink" esta funcionalidad en 50 líneas con un LLM en lugar de traer 40 dependencias transitivas?

Si la respuesta es sí → sugerir hacerlo propio como alternativa y explicar por qué podría ser mejor en ese caso específico.

---

## Formatos de salida

### Chat (default)
Mostrar el brief directamente en la conversación usando el formato markdown de arriba.

### Markdown (.md)
Guardar el brief como archivo `.md` en `/mnt/user-data/outputs/oss-review-<nombre>-<fecha>.md` y presentar al usuario con `present_files`.

### HTML visual
Generar un archivo `.html` autónomo con el brief renderizado visualmente. Incluir:
- Semáforos como badges de colores (verde/amarillo/rojo) en lugar de emojis
- Tabla de resumen estilizada
- Secciones colapsables para el deep review (si aplica)
- Estilo limpio, responsive, sin dependencias externas (todo inline)
- Dark mode por default con opción de light mode

Guardar en `/mnt/user-data/outputs/oss-review-<nombre>-<fecha>.html` y presentar con `present_files`.

---

## 🔬 Deep Review (opcional)

Al finalizar el brief estándar, preguntar:

> *"¿Querés un deep review? Escanea dependencias transitivas y postura de seguridad del repo (~30s más)"*

Solo proceder si el usuario confirma.

### Paso D1 — OSSF Scorecard (postura de seguridad del repo)

Solo aplica a repos GitHub públicos. El Scorecard de OpenSSF evalúa prácticas de seguridad del repositorio — no del código en sí, sino de cómo se gestiona el proyecto. Es útil porque un repo sin branch protection o sin CI puede recibir commits maliciosos más fácilmente.

```bash
curl -s "https://api.securityscorecards.dev/projects/github.com/OWNER/REPO" | python3 -c "
import json, sys
d = json.load(sys.stdin)
score = d.get('score', 'N/A')
print(f'Score general: {score}/10')
checks = d.get('checks', [])
for c in sorted(checks, key=lambda x: x.get('score', 10)):
    print(f'  {c[\"name\"]}: {c.get(\"score\", \"N/A\")}/10 — {c.get(\"reason\", \"\")}')
"
```

### Paso D2 — Árbol de dependencias transitivas (deps.dev)

Las dependencias transitivas son las que el usuario no eligió pero ejecuta de todas formas. Un paquete con 5 deps directas puede tener 200 transitivas — y cualquiera de ellas puede tener una vulnerabilidad.

```bash
# Para npm
curl -s "https://api.deps.dev/v3alpha/systems/npm/packages/PACKAGE_NAME/versions/VERSION/dependencies" | python3 -c "
import json, sys
d = json.load(sys.stdin)
nodes = d.get('nodes', [])
print(f'Total deps (directas + transitivas): {len(nodes)}')
for n in nodes[:20]:
    print(f'  {n.get(\"versionKey\", {}).get(\"name\")} {n.get(\"versionKey\", {}).get(\"version\")}')
"

# Para PyPI — cambiar system a 'pypi'
curl -s "https://api.deps.dev/v3alpha/systems/pypi/packages/PACKAGE_NAME/versions/VERSION/dependencies"
```

### Paso D3 — OSV batch sobre todas las deps transitivas

```bash
# Construir lista de paquetes del paso D2 y hacer query batch a OSV
curl -s -X POST "https://api.osv.dev/v1/querybatch" \
  -H "Content-Type: application/json" \
  -d '{
    "queries": [
      {"package": {"name": "DEP1", "ecosystem": "npm"}},
      {"package": {"name": "DEP2", "ecosystem": "npm"}}
    ]
  }' | python3 -c "
import json, sys
d = json.load(sys.stdin)
results = d.get('results', [])
with_vulns = [(i, r) for i, r in enumerate(results) if r.get('vulns')]
print(f'Deps con vulnerabilidades conocidas: {len(with_vulns)}/{len(results)}')
for i, r in with_vulns:
    for v in r['vulns'][:2]:
        print(f'  [{i}] {v.get(\"id\")}: {v.get(\"summary\", \"\")[:80]}')
"
```

### Output del Deep Review

Agregar sección al brief:

```
## 🔬 Deep Review

### OSSF Scorecard: X.X/10
| Check | Score | Detalle |
|-------|-------|---------|
| Branch Protection | X/10 | ... |
| Code Review | X/10 | ... |
| CI Tests | X/10 | ... |
| Signed Releases | X/10 | ... |
| ... | | |

### Dependencias transitivas: N total
- ⚠️ X deps con CVEs conocidos: [lista]
- ✅ Y deps sin vulnerabilidades

### Conclusión deep
<párrafo con riesgos reales encontrados o confirmación de que el proyecto está limpio>
```
