# Proyecto: parcial-docker-integrado

##  Estudiante
- **Nombre:** Cristian Alejandro Rivas Rodríguez
- **Expediente:** 25721
- **Código Estudiantil:** RR22-I04-001

---

##  Objetivo del Proyecto
Construir un servicio **Node.js funcional** y contenerizado con **Docker**, integrando PostgreSQL y Docker Compose.

---

## 📁 Estructura del Proyecto
parcial-docker-integrado/
├── server.js
├── package.json
├── package-lock.json
├── .dockerignore
├── Dockerfile
├── docker-compose.yml
└── .env

---

##  API Endpoints
- `GET /` → Devuelve datos personales del estudiante.  
- `GET /health` → Devuelve `{ "status": "OK" }`.

---

##  Instalación y Ejecución

### 1️ Configuración del proyecto Node.js
```bash
mkdir parcial-docker-integrado
cd parcial-docker-integrado
npm init -y
npm install express

```
### 2️ Crear imagen Docker y ejecutar contenedor
```bash
docker build -t parcial-api .
docker run -d -p 3000:3000 --name parcial-api parcial-api
```
### 3 Probar endpoints
```bash
curl http://localhost:3000/
curl http://localhost:3000/health
```
### 4 Crear y ejecutar contenedor PostgreSQL
```bash
docker volume create db_data

docker run -d \
  --name parcial-db \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=12345 \
  -e POSTGRES_DB=parcial_db \
  -v db_data:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres
```
### 5 Conectarse y crear tabla
```bash
docker exec -it parcial-db psql -U admin -d parcial_db

# Dentro de PostgreSQL
CREATE TABLE estudiantes (
  id SERIAL PRIMARY KEY,
  nombre VARCHAR(100),
  codigo VARCHAR(20)
);

INSERT INTO estudiantes (nombre, codigo) VALUES
('Cristian Rivas', 'TU_CODIGO_ESTUDIANTIL');

```
### 6 Verificar persistencia
```bash
docker restart parcial-db
docker exec -it parcial-db psql -U admin -d parcial_db -c "SELECT * FROM estudiantes;"
```
Integración con Docker Compose
Archivos principales

docker-compose.yml

.env

Ejemplo de docker-compose.yml

```bash
version: "3.8"

services:
  db:
    image: postgres
    container_name: parcial-db
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: ${POSTGRES_DB}
    volumes:
      - db_data:/var/lib/postgresql
    networks:
      - app_net
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER}"]
      interval: 10s
      timeout: 5s
      retries: 5

  api:
    build: .
    container_name: parcial-api
    ports:
      - "${PORT}:3000"
    depends_on:
      db:
        condition: service_healthy
    networks:
      - app_net
    environment:
      DB_HOST: db
      DB_USER: ${POSTGRES_USER}
      DB_PASSWORD: ${POSTGRES_PASSWORD}
      DB_NAME: ${POSTGRES_DB}

volumes:
  db_data:

networks:
  app_net:

```
Comandos Docker Compose
```bash
docker compose up -d --build
docker ps
docker inspect --format='{{json .State.Health}}' parcial-db
curl http://localhost:3000/
curl http://localhost:3000/health
```
Validaciones realizadas

docker-compose.yml funcional con versión 3.8.

Red app_net creada automáticamente.

Comunicación correcta entre servicios api y db.

Healthcheck de PostgreSQL funcionando (healthy).

API responde correctamente y se conecta a la base de datos.

Evidencias documentadas en docs/evidencias.

Referencias

Node.js

Docker

Docker Compose
