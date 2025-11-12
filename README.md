# ============================================
# GUÍA COMPLETA GIT PARA EL PROYECTO
# ============================================

# --------------------------------------------
# OPCIÓN 1: CREAR NUEVO REPOSITORIO EN GITHUB
# --------------------------------------------

# 1. Ve a GitHub.com y crea un nuevo repositorio
#    - Nombre: user-crud-monitoring
#    - Descripción: Sistema CRUD con FastAPI, PostgreSQL, Prometheus y Grafana
#    - Público o Privado (según prefieras)
#    - NO inicialices con README (ya lo tienes local)

# 2. En tu terminal, dentro del proyecto:
cd user-crud-monitoring

# 3. Inicializar Git (si no lo has hecho)
git init

# 4. Crear archivo .gitignore
cat > .gitignore << 'EOF'
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
env/
venv/
ENV/
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# Docker
.env
*.log

# IDE
.vscode/
.idea/
*.swp
*.swo
.DS_Store

# Base de datos
*.db
*.sqlite3

# Volumes (no subir datos persistentes)
postgres_data/
prometheus_data/
grafana_data/
EOF

# 5. Agregar todos los archivos
git add .

# 6. Verificar qué se va a commitear
git status

# 7. Hacer el primer commit
git commit -m "Initial commit: Sistema CRUD con monitoreo completo

- Implementación de API FastAPI con endpoints CRUD
- Configuración de PostgreSQL con SQLAlchemy
- Integración de Prometheus para métricas
- Setup de Grafana para visualización
- Docker Compose para orquestación de servicios
- Documentación completa en README.md y JUSTIFICACION.md"

# 8. Conectar con el repositorio remoto (reemplaza TU-USUARIO)
git remote add origin https://github.com/TU-USUARIO/user-crud-monitoring.git

# 9. Verificar que el remote está configurado
git remote -v

# 10. Subir al repositorio (primera vez)
git branch -M main
git push -u origin main

# --------------------------------------------
# OPCIÓN 2: CLONAR REPOSITORIO EXISTENTE
# --------------------------------------------

# Si ya creaste el repositorio en GitHub CON README:

# 1. Clonar el repositorio
git clone https://github.com/TU-USUARIO/user-crud-monitoring.git
cd user-crud-monitoring

# 2. Copiar todos tus archivos del proyecto aquí

# 3. Agregar archivos
git add .

# 4. Commit
git commit -m "Añadir implementación completa del proyecto"

# 5. Push
git push origin main

# --------------------------------------------
# COMANDOS ÚTILES DURANTE EL DESARROLLO
# --------------------------------------------

# Ver el estado actual
git status

# Ver diferencias antes de commit
git diff

# Agregar archivos específicos
git add app/main.py
git add docker-compose.yml

# Commit con mensaje descriptivo
git commit -m "Agregar endpoint DELETE para usuarios"

# Ver historial de commits
git log --oneline

# Subir cambios
git push

# Crear una rama para experimentar
git checkout -b feature/mejoras-dashboard
git push -u origin feature/mejoras-dashboard

# Volver a la rama principal
git checkout main

# --------------------------------------------
# VERIFICACIÓN FINAL
# --------------------------------------------

# Asegúrate de que estos archivos estén en el repo:
# ✅ README.md
# ✅ JUSTIFICACION.md
# ✅ docker-compose.yml
# ✅ .gitignore
# ✅ app/main.py
# ✅ app/database.py
# ✅ app/models.py
# ✅ app/requirements.txt
# ✅ app/Dockerfile
# ✅ prometheus/prometheus.yml

# Verificar en GitHub que todos los archivos se subieron correctamente

# --------------------------------------------
# COMANDOS PARA AGREGAR SCREENSHOTS
# --------------------------------------------

# 1. Crear carpeta para evidencias
mkdir screenshots
cd screenshots

# 2. Tomar screenshots y guardarlos aquí con nombres descriptivos:
#    - docker-compose-running.png
#    - api-docs.png
#    - prometheus-targets.png
#    - grafana-dashboard.png
#    - users-list.png

# 3. Agregar al repositorio
cd ..
git add screenshots/
git commit -m "Agregar screenshots de evidencias del proyecto"
git push

# --------------------------------------------
# ESTRUCTURA FINAL DEL REPOSITORIO
# --------------------------------------------

# user-crud-monitoring/
# ├── README.md                    ← Documentación principal
# ├── JUSTIFICACION.md             ← Justificación técnica detallada
# ├── .gitignore                   ← Archivos a ignorar
# ├── docker-compose.yml           ← Orquestación de servicios
# ├── app/
# │   ├── main.py                  ← API FastAPI
# │   ├── database.py              ← Configuración BD
# │   ├── models.py                ← Modelos SQLAlchemy
# │   ├── requirements.txt         ← Dependencias Python
# │   └── Dockerfile               ← Imagen de la API
# ├── prometheus/
# │   └── prometheus.yml           ← Config de Prometheus
# └── screenshots/                 ← Evidencias visuales
#     ├── docker-compose-running.png
#     ├── api-docs.png
#     ├── prometheus-targets.png
#     ├── grafana-dashboard.png
#     └── users-list.png

# --------------------------------------------
# TIPS FINALES
# --------------------------------------------

# 1. Commits frecuentes con mensajes claros
# 2. Nunca subir contraseñas reales (usar .env para producción)
# 3. Documentar cambios importantes en el README
# 4. Usar branches para features experimentales
# 5. Mantener el .gitignore actualizado

# --------------------------------------------
# PARA COMPARTIR CON EL PROFESOR
# --------------------------------------------

echo "URL del repositorio:"
echo "https://github.com/TU-USUARIO/user-crud-monitoring"
echo ""
echo "Para clonar:"
echo "git clone https://github.com/TU-USUARIO/user-crud-monitoring.git"
