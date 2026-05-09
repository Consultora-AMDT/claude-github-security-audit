# Security Patterns — Checklist Completo

## 1. Secrets y Credenciales

### Patrones a buscar en código fuente

```
# Strings literales sospechosos
password\s*=\s*["'][^"']+["']
api[_-]?key\s*=\s*["'][^"']+["']
secret\s*=\s*["'][^"']+["']
token\s*=\s*["'][^"']+["']
aws[_-]?access[_-]?key
AKIA[0-9A-Z]{16}                    # AWS Access Key ID
sk-[a-zA-Z0-9]{48}                  # OpenAI API Key
ghp_[a-zA-Z0-9]{36}                 # GitHub Personal Access Token
xox[bpas]-[a-zA-Z0-9-]+             # Slack Token

# Archivos que NO deberían estar en el repo
.env
.env.local
.env.production
*.pem
*.key
id_rsa
credentials.json
service-account*.json
```

### Archivos a revisar

- `.gitignore` — ¿incluye `.env`, `*.key`, `*.pem`?
- `.github/workflows/*.yml` — ¿secrets manejados correctamente?
- `docker-compose.yml` — ¿passwords en texto plano?
- `config/` — ¿valores por defecto inseguros?

### Scoring Secrets

| Hallazgo | Score | Notas |
|----------|-------|-------|
| Sin secrets, .gitignore completo | 10 | |
| .gitignore correcto, sin hallazgos | 8 | |
| Valores de ejemplo/placeholder obvios | 6 | Aceptable si son claramente fake |
| .env.example con valores reales | 2 | Preocupante |
| Secrets reales en código o historial | 0 | **Override: AVOID automático** |

---

## 2. GitHub Actions Security

### Patrones peligrosos en workflows

```yaml
# PELIGROSO — permisos excesivos
permissions: write-all

# PELIGROSO — ejecuta código de PRs de forks con secrets
on: pull_request_target
  # + checkout del PR + ejecución

# PELIGROSO — inyección de script
run: echo "${{ github.event.issue.title }}"
# El título del issue se ejecuta como shell

# PELIGROSO — Actions de terceros sin pin a SHA
uses: some-action@main
# Correcto: uses: some-action@abc123sha

# SOSPECHOSO — upload de artefactos con paths amplios
uses: actions/upload-artifact@v4
with:
  path: /
```

### Buenas prácticas esperadas

- [ ] Actions pinneados a SHA (no a tags)
- [ ] `permissions` explícitos y mínimos por job
- [ ] No usar `pull_request_target` + checkout del PR
- [ ] Secrets no expuestos en logs (`::add-mask::`)
- [ ] Dependabot o Renovate configurado
- [ ] No ejecutar inputs no sanitizados en `run:`

---

## 3. Ejecución Dinámica de Código

### Python

```python
eval()                    # Nunca en input de usuario
exec()                    # Nunca en input de usuario
subprocess.call(shell=True)  # Inyección de comandos
os.system()               # Inyección de comandos
pickle.loads()            # Deserialización insegura
yaml.load()               # Sin Loader= es inseguro (usar yaml.safe_load)
__import__()              # Carga dinámica peligrosa
compile() + exec()        # Ejecución dinámica
```

### JavaScript/TypeScript

```javascript
eval()
Function()               // Constructor de funciones
new Function()
setTimeout(string)       // Con string, no función
setInterval(string)
document.write()
innerHTML =              // XSS
dangerouslySetInnerHTML   // React — requiere justificación
require(variable)        // Carga dinámica
child_process.exec()     // Sin sanitización = inyección
```

### Rust/Go

```rust
// Rust es generalmente seguro, buscar:
unsafe { }               // Bloques unsafe — requieren justificación
std::process::Command    // Ejecución de comandos — verificar sanitización

// Go:
os/exec                  // Ejecución de comandos
reflect                  // Uso excesivo de reflection
```

### Scoring Ejecución Dinámica

| Hallazgo | Score |
|----------|-------|
| Sin patrones peligrosos | 10 |
| Uso justificado y sanitizado | 7 |
| Uso sin sanitización clara | 3 |
| eval/exec en input de usuario sin validación | 0 |

---

## 4. Dependencias

### Señales de riesgo en dependencias

- **Typosquatting**: paquete con nombre similar a uno popular
- **Dependencia abandonada**: sin updates en >2 años
- **Single maintainer dependency**: paquete del que dependen miles pero con 1 persona
- **Versión no fijada**: `*`, `latest`, rangos amplios sin lock file
- **Lock file ausente**: sin `package-lock.json`, `poetry.lock`, `Cargo.lock`

### Archivos a revisar

| Ecosistema | Manifest | Lock file |
|-----------|----------|-----------|
| Node.js | `package.json` | `package-lock.json` / `yarn.lock` / `pnpm-lock.yaml` |
| Python | `requirements.txt` / `pyproject.toml` | `poetry.lock` / `uv.lock` |
| Rust | `Cargo.toml` | `Cargo.lock` |
| Go | `go.mod` | `go.sum` |
| Ruby | `Gemfile` | `Gemfile.lock` |

### Scoring Dependencias

| Hallazgo | Score |
|----------|-------|
| Lock file presente, versiones fijadas, pocas deps | 10 |
| Lock file presente, deps razonables | 7 |
| Sin lock file pero versiones fijadas | 4 |
| Muchas deps, sin lock, rangos amplios | 2 |
| Dependencias conocidas como comprometidas | 0 |

---

## 5. Configuración Insegura por Defecto

Patrón inspirado en Trail of Bits `insecure-defaults`:

### Principio: Fail-Secure vs Fail-Open

- **Fail-secure** ✅: Si falta configuración, el sistema se detiene o usa valores seguros
- **Fail-open** ❌: Si falta configuración, el sistema arranca con defaults inseguros

### Ejemplos concretos

```python
# ❌ FAIL-OPEN — JWT secret por defecto
JWT_SECRET = os.environ.get("JWT_SECRET", "changeme")

# ✅ FAIL-SECURE — Falla si no hay secret
JWT_SECRET = os.environ["JWT_SECRET"]  # KeyError si no existe

# ❌ FAIL-OPEN — Debug mode por defecto
DEBUG = os.environ.get("DEBUG", "true")

# ✅ FAIL-SECURE — Debug off por defecto
DEBUG = os.environ.get("DEBUG", "false")

# ❌ FAIL-OPEN — CORS permisivo por defecto
CORS_ORIGINS = os.environ.get("CORS_ORIGINS", "*")

# ✅ FAIL-SECURE — Sin CORS por defecto
CORS_ORIGINS = os.environ.get("CORS_ORIGINS", "")
```

### Scoring Insecure Defaults

| Hallazgo | Score |
|----------|-------|
| Fail-secure en toda la configuración | 10 |
| Mayormente fail-secure, 1-2 exceptions documentadas | 7 |
| Mix de fail-secure y fail-open | 4 |
| Fail-open generalizado | 1 |

---

## Cálculo de Fase 2

```
F2 = promedio_ponderado(
  secrets × 0.30,
  actions × 0.15,
  exec_dinamica × 0.20,
  dependencias × 0.20,
  insecure_defaults × 0.15
)
```
