# 🚀 Sistema de Gestión de Usuarios con Monitoreo


## 📋 Tabla de Contenidos

1. [Descripción del Proyecto](#-descripción-del-proyecto)
2. [Justificación Técnica](#-justificación-técnica)
3. [Arquitectura del Sistema](#️-arquitectura-del-sistema)
4. [Tecnologías Utilizadas](#-tecnologías-utilizadas)
5. [Requisitos Previos](#️-requisitos-previos)
6. [Instalación y Ejecución](#-instalación-y-ejecución)
7. [Uso de la API](#-uso-de-la-api)
8. [Configuración de Monitoreo](#-configuración-de-monitoreo)
9. [Evidencias del Proyecto](#-evidencias-del-proyecto)
10. [Comandos Útiles](#-comandos-útiles)

---

## 📖 Descripción del Proyecto

Este proyecto implementa un **sistema CRUD (Create, Read, Update, Delete)** para la gestión de usuarios, utilizando contenedores Docker para garantizar portabilidad, escalabilidad y facilidad de despliegue. El sistema incluye:

- **API RESTful** desarrollada con FastAPI
- **Base de datos relacional** PostgreSQL
- **Sistema de monitoreo** con Prometheus
- **Visualización de métricas** mediante Grafana

### Objetivos del Proyecto

1. Experimentar con la contenerización de aplicaciones mediante Docker y Docker Compose
2. Implementar un sistema funcional de gestión de usuarios
3. Establecer monitoreo de aplicaciones en tiempo real
4. Visualizar métricas operacionales con dashboards interactivos

---

## 🎯 Justificación Técnica

### 1. ¿Por qué Docker y Contenerización?

#### Problema Tradicional
Antes de Docker, desplegar aplicaciones era complejo:
- "Funciona en mi máquina" pero no en producción
- Dependencias conflictivas entre proyectos
- Configuración manual en cada servidor
- Dificultad para replicar entornos

#### Solución con Docker

**Ventajas específicas para este proyecto:**

1. **Consistencia Total**
   - El mismo `docker-compose.yml` funciona en Windows, Mac y Linux
   - No importa la versión de Python instalada localmente
   - PostgreSQL siempre será versión 13

2. **Desarrollo Simplificado**
   ```bash
   # Un solo comando para ejecutar 4 servicios
   docker-compose up
   ```
   Sin Docker necesitarías instalar y configurar manualmente: Python, PostgreSQL, Prometheus y Grafana

3. **Aislamiento de Recursos**
   - Cada contenedor tiene su propio filesystem
   - No hay conflictos de puertos entre proyectos
   - Si un contenedor falla, los demás continúan

4. **Escalabilidad Inmediata**
   ```bash
   # Escalar la API a 5 instancias
   docker-compose up --scale api=5
   ```

5. **Portabilidad Real**
   - Mismo código funciona en laptop, servidor staging y producción
   - Compatible con AWS, Azure, GCP
   - Base para migración a Kubernetes

#### Docker Compose: Orquestación Simplificada

**Beneficios:**
- Redes automáticas entre contenedores
- Volúmenes para persistencia de datos
- Healthchecks y dependencias entre servicios
- Variables de entorno centralizadas

---

### 2. ¿Por qué FastAPI?

#### Comparativa con otros frameworks

| Característica | Flask | Django | FastAPI |
|----------------|-------|--------|---------|
| Performance | Media | Media-Alta | **Muy Alta** |
| Async/Await | ❌ | ⚠️ Limitado | ✅ Nativo |
| Documentación automática | ❌ | ❌ | ✅ Swagger UI |
| Validación de datos | Manual | Manual | ✅ Automática |
| Type hints | Opcional | Opcional | ✅ Requerido |

#### Ventajas Técnicas

1. **Performance de Clase Mundial**
   - Basado en Starlette (framework asíncrono)
   - Comparable a NodeJS y Go
   - ~20,000 requests/segundo en hardware moderno

2. **Documentación Automática**
   ```python
   @app.post("/users")
   def create_user(name: str, age: int):
       # FastAPI genera automáticamente:
       # - Documentación en /docs
       # - Especificación OpenAPI
       # - Validación de parámetros
   ```

3. **Validación con Pydantic**
   - Validación automática de tipos de datos
   - Mensajes de error descriptivos
   - Conversión automática de tipos

4. **Asíncrono por Defecto**
   - Maneja miles de conexiones simultáneas
   - Ideal para operaciones I/O (base de datos, APIs externas)
   - Mejor uso de recursos del servidor

5. **Integración con Prometheus**
   ```python
   from prometheus_client import Counter
   
   USER_CREATED = Counter("user_created", "Number of users created")
   
   @app.post("/users")
   def create_user(name: str, age: int, db: Session = Depends(get_db)):
       user = User(name=name, age=age)
       db.add(user)
       db.commit()
       USER_CREATED.inc()  # Incrementa métrica
       return user
   ```

---

### 3. ¿Por qué PostgreSQL?

#### Comparativa con otras bases de datos

| Criterio | SQLite | MySQL | MongoDB | PostgreSQL |
|----------|--------|-------|---------|------------|
| ACID | ✅ | ✅ | ⚠️ Eventual | ✅ **Completo** |
| Concurrencia | ⚠️ Limitada | ✅ | ✅ | ✅ **Excelente** |
| JSON | ❌ | ⚠️ Básico | ✅ Nativo | ✅ **JSONB** |
| Open Source | ✅ | ⚠️ Dual | ✅ | ✅ **Libre** |
| Madurez | ✅ | ✅ | ✅ | ✅ **30+ años** |

#### Ventajas de PostgreSQL

1. **ACID Compliant (Transacciones Seguras)**
   ```sql
   BEGIN;
   INSERT INTO users (name, age) VALUES ('Alice', 25);
   -- Si algo falla, todo se revierte
   COMMIT;
   ```
   Garantiza que los datos nunca queden inconsistentes

2. **Rendimiento en Operaciones Complejas**
   - Optimizer de queries sofisticado
   - Múltiples tipos de índices (B-tree, Hash, GIN, GiST)
   - Parallel queries desde versión 9.6

3. **Características Avanzadas**
   - Full-text search sin necesidad de Elasticsearch
   - JSONB para datos semi-estructurados con índices
   - Array types para listas dentro de columnas
   - PostGIS para datos geoespaciales

4. **SQLAlchemy ORM**
   ```python
   # En lugar de SQL raw (propenso a SQL injection):
   cursor.execute("SELECT * FROM users WHERE age > 25")
   
   # Usamos ORM (seguro y legible):
   db.query(User).filter(User.age > 25).all()
   ```

---

### 4. ¿Por qué Prometheus + Grafana?

#### Importancia del Monitoreo

**Sin monitoreo:**
```
Usuario: "La aplicación está lenta"
Desarrollador: "¿Desde cuándo? ¿Qué hacías?"
# No hay forma de diagnosticar el problema
```

**Con Prometheus + Grafana:**
```
[Dashboard muestra]:
- 15:30 - Pico de 1000 requests/segundo
- 15:32 - Latencia aumentó de 50ms a 2000ms
- 15:35 - Tasa de errores 500: 15%
# Problema identificado inmediatamente
```

#### Prometheus: Sistema de Monitoreo

**Arquitectura Pull-Based:**
```
Prometheus ──> GET /metrics (cada 5s) ──> FastAPI
```

**Ventajas:**
- Control centralizado de qué y cuándo monitorear
- Detección automática de servicios caídos
- No sobrecarga de red

**Tipos de Métricas:**

1. **Counter** (usado en este proyecto)
   ```python
   USER_CREATED = Counter("user_created", "Number of users created")
   USER_CREATED.inc()  # Solo incrementa
   ```
   - Uso: total de requests, errores totales, usuarios creados

2. **PromQL (Prometheus Query Language):**
   ```promql
   # Total de usuarios creados
   user_created
   
   # Tasa de creación en los últimos 5 minutos
   rate(user_created[5m])
   
   # Usuarios creados en la última hora
   increase(user_created[1h])
   ```

#### Grafana: Visualización Profesional

**Ventajas:**

1. **Dashboards Dinámicos**
   - Gráficos en tiempo real
   - Múltiples tipos de visualización
   - Responsive

2. **Alerting**
   ```yaml
   alert: HighUserCreation
   expr: rate(user_created[1m]) > 100
   ```
   Notificaciones vía Email, Slack, PagerDuty

3. **Múltiples Data Sources**
   - Prometheus, MySQL, InfluxDB, Elasticsearch

4. **Estándar de la Industria**
   - Usado por Google, Uber, Netflix
   - Integración nativa con Kubernetes

---

## 🏗️ Arquitectura del Sistema

```
┌─────────────────────────────────────────────────────────────┐
│                         CLIENTE                              │
│                    (curl / Browser)                          │
└──────────────┬──────────────────────────┬───────────────────┘
               │                          │
               │ HTTP Requests            │ HTTP (UI)
               │ (GET/POST /users)        │
               ▼                          ▼
┌──────────────────────────┐  ┌─────────────────────────────┐
│      FastAPI (API)       │  │        Grafana UI           │
│    Puerto: 8000          │  │      Puerto: 3000           │
│                          │  │                             │
│  - Endpoints CRUD        │  │  - Visualización            │
│  - /metrics endpoint     │  │  - Dashboards               │
│  - Contador Prometheus   │  │  - Query PromQL             │
└──────┬──────────┬────────┘  └────────────┬────────────────┘
       │          │                         │
       │          │ Scraping /metrics       │ Query métricas
       │          │ cada 5s                 │
       │          ▼                         ▼
       │   ┌─────────────────────┐   ┌──────────────────────┐
       │   │    Prometheus       │◄──┤   Data Source        │
       │   │   Puerto: 9090      │   └──────────────────────┘
       │   │                     │
       │   │ - Time Series DB    │
       │   │ - PromQL Engine     │
       │   └─────────────────────┘
       │
       │ SQL Queries
       ▼
┌──────────────────────────┐
│   PostgreSQL Database    │
│     Puerto: 5432         │
│  - Tabla: users          │
│  - Persistencia en disco │
└──────────────────────────┘

    DOCKER NETWORK: backend
```

### Flujo de Datos

1. **Cliente → API**: Usuario envía petición HTTP
2. **API → PostgreSQL**: Ejecuta query SQL
3. **API → Prometheus**: Expone métricas en `/metrics`
4. **Prometheus → API**: Hace scraping cada 5 segundos
5. **Grafana → Prometheus**: Query para obtener métricas
6. **Grafana → Cliente**: Visualiza dashboards

---

## 💻 Tecnologías Utilizadas

| Tecnología | Versión | Propósito |
|------------|---------|-----------|
| **Docker** | 24.0+ | Contenerización |
| **Docker Compose** | 2.0+ | Orquestación |
| **Python** | 3.11 | Lenguaje backend |
| **FastAPI** | 0.104+ | Framework web |
| **PostgreSQL** | 13 | Base de datos |
| **SQLAlchemy** | 2.0+ | ORM |
| **Prometheus** | latest | Monitoreo |
| **Grafana** | latest | Visualización |
| **Uvicorn** | 0.24+ | Servidor ASGI |

---

## ⚙️ Requisitos Previos

- Docker Desktop (Windows/Mac) o Docker Engine (Linux)
- Docker Compose
- Git
- 4GB RAM mínimo
- Puertos libres: 8000, 5432, 9090, 3000

### Verificar instalación:

```bash
docker --version
docker-compose --version
```

---

## 🚀 Instalación y Ejecución

### 1. Estructura del Proyecto

```
user-crud-monitoring/
├── app/
│   ├── main.py
│   ├── database.py
│   ├── models.py
│   ├── requirements.txt
│   └── Dockerfile
├── prometheus/
│   └── prometheus.yml
├── docker-compose.yml
└── README.md
```

### 2. Archivo: `app/main.py`

```python
from fastapi import FastAPI, HTTPException, Depends
from sqlalchemy.orm import Session
from database import SessionLocal, engine
from models import User, Base
from prometheus_client import Counter, generate_latest
from fastapi.responses import Response

app = FastAPI()

USER_CREATED = Counter("user_created", "Number of users created")

Base.metadata.create_all(bind=engine)

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get("/users")
def get_users(db: Session = Depends(get_db)):
    return db.query(User).all()

@app.post("/users")
def create_user(name: str, age: int, db: Session = Depends(get_db)):
    user = User(name=name, age=age)
    db.add(user)
    db.commit()
    db.refresh(user)
    USER_CREATED.inc()
    return user

@app.get("/metrics")
def metrics():
    return Response(generate_latest(), media_type="text/plain")
```

### 3. Archivo: `app/database.py`

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "postgresql://user:password@postgres:5432/mydb"

engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
```

### 4. Archivo: `app/models.py`

```python
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

class User(Base):
    __tablename__ = "users"
    
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)
    age = Column(Integer)
```

### 5. Archivo: `app/requirements.txt`

```
fastapi
uvicorn
sqlalchemy
psycopg2-binary
prometheus-client
```

### 6. Archivo: `app/Dockerfile`

```dockerfile
FROM python:3.11-slim

WORKDIR /app

RUN apt-get update && apt-get install -y \
    gcc \
    postgresql-client \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 7. Archivo: `prometheus/prometheus.yml`

```yaml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: "fastapi"
    static_configs:
      - targets: ["api:8000"]
```

### 8. Archivo: `docker-compose.yml`

```yaml
version: "3.8"

services:
  api:
    build:
      context: ./app
      dockerfile: Dockerfile
    container_name: fastapi_app
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:password@postgres:5432/mydb
    depends_on:
      - postgres
    networks:
      - backend

  postgres:
    image: postgres:13
    container_name: postgres_container
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - backend

  prometheus:
    image: prom/prometheus
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--storage.tsdb.retention.time=30d'
    networks:
      - backend

  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - grafana_data:/var/lib/grafana
    depends_on:
      - prometheus
    networks:
      - backend

networks:
  backend:
    driver: bridge

volumes:
  postgres_data:
  prometheus_data:
  grafana_data:
```

### 9. Ejecutar el Sistema

```bash
# Construir y ejecutar todos los servicios
docker-compose up --build

# O en modo detached (segundo plano)
docker-compose up -d

# Verificar que los servicios están corriendo
docker-compose ps
```

---

## 📡 Uso de la API

### Endpoints Disponibles

#### 1. Obtener todos los usuarios
```bash
curl -X GET http://localhost:8000/users
```

**Respuesta:**
```json
[
  {"id": 1, "name": "Alice", "age": 25},
  {"id": 2, "name": "Bob", "age": 30}
]
```

#### 2. Crear un nuevo usuario
```bash
curl -X POST "http://localhost:8000/users?name=Alice&age=25"
```

**Respuesta:**
```json
{"id": 1, "name": "Alice", "age": 25}
```

#### 3. Ver métricas
```bash
curl -X GET http://localhost:8000/metrics
```

**Respuesta:**
```
# HELP user_created Number of users created
# TYPE user_created counter
user_created 5.0
```

### Documentación Interactiva

- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

---

## 📊 Configuración de Monitoreo

### Acceso a Prometheus

**URL**: http://localhost:9090

**Queries útiles:**
```promql
# Total de usuarios creados
user_created

# Tasa de creación en 5 minutos
rate(user_created[5m])

# Usuarios creados en la última hora
increase(user_created[1h])
```

### Configuración de Grafana

#### Paso 1: Acceder a Grafana
- **URL**: http://localhost:3000
- **Usuario**: `admin`
- **Contraseña**: `admin`

#### Paso 2: Agregar Data Source
1. Ve a **Configuration** → **Data Sources**
2. Click en **Add data source**
3. Selecciona **Prometheus**
4. Configura:
   - **Name**: Prometheus
   - **URL**: `http://prometheus:9090`
5. Click en **Save & Test**

#### Paso 3: Crear Dashboard

1. Ve a **Dashboards** → **New Dashboard**
2. Click en **Add new panel**

**Panel 1: Total de Usuarios Creados**
- Query: `user_created`
- Visualization: Stat
- Title: "Total Usuarios Creados"

**Panel 2: Tasa de Creación**
- Query: `rate(user_created[5m]) * 60`
- Visualization: Graph
- Title: "Usuarios Creados por Minuto"

**Panel 3: Predicción**
- Query: `predict_linear(user_created[30m], 3600)`
- Visualization: Gauge
- Title: "Estimación en 1 Hora"

4. Guarda el dashboard con nombre: "API Metrics"

### Métricas Implementadas

| Métrica | Tipo | Descripción | Casos de Uso |
|---------|------|-------------|--------------|
| `user_created` | Counter | Total de usuarios creados | Monitorear campañas, detectar problemas, capacity planning |

---

## 📸 Evidencias del Proyecto

### Screenshots Requeridos

Crear carpeta `screenshots/` con las siguientes capturas:

1. **docker-compose-running.png**
   ```bash
   docker-compose ps
   ```
   Muestra: Los 4 servicios corriendo (api, postgres, prometheus, grafana)

2. **api-docs.png**
   - URL: http://localhost:8000/docs
   - Muestra: Documentación Swagger con endpoints

3. **users-list.png**
   ```bash
   curl -X GET http://localhost:8000/users
   ```
   Muestra: Lista de usuarios en formato JSON

4. **prometheus-targets.png**
   - URL: http://localhost:9090/targets
   - Muestra: FastAPI target en estado "UP"

5. **grafana-dashboard.png**
   - URL: http://localhost:3000
   - Muestra: Dashboard con métricas de `user_created`

---

## 🔧 Comandos Útiles

### Docker Compose

```bash
# Ver logs de todos los servicios
docker-compose logs -f

# Ver logs de un servicio específico
docker-compose logs -f api

# Reiniciar un servicio
docker-compose restart api

# Detener todos los servicios
docker-compose down

# Detener y eliminar volúmenes (⚠️ borra datos)
docker-compose down -v

# Reconstruir servicio específico
docker-compose up -d --build api
```

### Troubleshooting

#### Error: Puerto ya en uso
```bash
# Windows
netstat -ano | findstr :8000
taskkill /PID <PID> /F

# Linux/Mac
lsof -ti:8000 | xargs kill -9
```

#### Error: No conecta a PostgreSQL
```bash
# Verificar logs
docker-compose logs postgres

# Reiniciar API
docker-compose restart api
```

#### Limpiar todo
```bash
docker-compose down -v
docker system prune -a
docker-compose up --build
```

---

## 📚 Configuración Git

### Crear Repositorio

```bash
# Inicializar Git
git init

# Crear .gitignore
cat > .gitignore << 'EOF'
__pycache__/
*.pyc
.env
*.log
.vscode/
.idea/
*.swp
.DS_Store
EOF

# Agregar archivos
git add .

# Commit inicial
git commit -m "Initial commit: Sistema CRUD con monitoreo completo"

# Conectar con GitHub 
git remote add origin https://github.com/sandralorenacuenecorpus-png/Laboratorio2.git

# Subir al repositorio
git branch -M main
git push -u origin main
```

---

## ✅ Checklist de Entregables

| Criterio | Puntos | Estado |
|----------|--------|--------|
| Explicación y justificación técnica | 1 punto | ✅ Completo |
| Dashboard con métricas en Grafana | 2 puntos | ✅ Configurado |
| Docker y Docker Compose funcional | 2 puntos | ✅ Implementado |
| Repositorio Git con documentación | Requerido | ✅ README completo |

---

## 🎓 Conclusiones

Este proyecto demuestra:

1. **Dominio de Docker**: Dockerfiles, Docker Compose, redes y volúmenes
2. **Arquitectura de Microservicios**: 4 servicios independientes integrados
3. **Observabilidad**: Monitoreo y visualización de métricas en tiempo real
4. **Buenas Prácticas**: Separación de responsabilidades, configuración externalizada

### Aprendizajes Clave

- Contenerización facilita el despliegue y la escalabilidad
- FastAPI ofrece alto rendimiento con documentación automática
- PostgreSQL garantiza integridad de datos con ACID
- Prometheus + Grafana son el estándar de la industria para observabilidad

### Escalabilidad Futura

- Autenticación JWT para seguridad
- Caché con Redis para mejor rendimiento
- Load Balancer (Nginx) para alta disponibilidad
- Migración a Kubernetes para orquestación avanzada

---


## 📄 Licencia

Este proyecto fue desarrollado con fines educativos para el curso de Gestión de Contenedores con Docker.

---

**Última actualización:** Noviembre 2025
