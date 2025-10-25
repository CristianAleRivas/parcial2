# Proyecto: parcial-docker-integrado

## Estudiante
- **Nombre:** Cristian Alejandro Rivas Rodríguez
- **Expediente:** 25721
- **Código Estudiantil:** RR22-I04-001

---

## 🧪 Ejercicio 1 – Servicio Base con Dockerfile

### Objetivo
Construir un servicio Node.js funcional y contenerizado con un Dockerfile optimizado.

### Estructura del proyecto
parcial-docker-integrado/
├── server.js
├── package.json
├── .dockerignore
├── Dockerfile

### Endpoints implementados
- `GET /` → Devuelve datos personales del estudiante.
- `GET /health` → Devuelve `{ status: "OK" }`.

### Comandos utilizados

```bash
# Crear proyecto y archivos
mkdir parcial-docker-integrado
cd parcial-docker-integrado
npm init -y
npm install express

# Crear imagen Docker
docker build -t parcial-api .

# Ejecutar contenedor
docker run -d -p 3000:3000 --name parcial-api parcial-api

# Probar endpoints
curl http://localhost:3000/
curl http://localhost:3000/health

# Crear volumen
docker volume create db_data

# Ejecutar contenedor PostgreSQL
docker run -d \
  --name parcial-db \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=12345 \
  -e POSTGRES_DB=parcial_db \
  -v db_data:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres

# Conectarse a la base de datos
docker exec -it parcial-db psql -U admin -d parcial_db

# Crear tabla y agregar datos
CREATE TABLE estudiantes (
  id SERIAL PRIMARY KEY,
  nombre VARCHAR(100),
  codigo VARCHAR(20)
);

INSERT INTO estudiantes (nombre, codigo) VALUES
('Cristian Rivas', 'TU_CODIGO_ESTUDIANTIL');

# Verificar persistencia
docker restart parcial-db
docker exec -it parcial-db psql -U admin -d parcial_db -c "SELECT * FROM estudiantes;"

### Ejercicio 3 – Integración con Docker Compose
Objetivo
Integrar los servicios en un único archivo docker-compose.yml con red, dependencias y healthcheck.
Archivos creados
.env

docker-compose.yml
YAMLservices:  db:    image: postgres    container_name: parcial-db    environment:      POSTGRES_USER: ${POSTGRES_USER}      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}      POSTGRES_DB: ${POSTGRES_DB}    volumes:      - db_data:/var/lib/postgresql    networks:      - app_net    healthcheck:      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER}"]      interval: 10s      timeout: 5s      retries: 5  api:    build: .    container_name: parcial-api    ports:      - "${PORT}:3000"    depends_on:      db:        condition: service_healthy    networks:      - app_net    environment:      DB_HOST: db      DB_USER: ${POSTGRES_USER}      DB_PASSWORD: ${POSTGRES_PASSWORD}      DB_NAME: ${POSTGRES_DB}volumes:  db_data:networks:  app_net:Mostrar más líneas
Comandos utilizados
Shelldocker compose up -d --builddocker psdocker inspect --format='{{json .State.Health}}' parcial-dbcurl http://localhost:3000/curl http://localhost:3000/healthMostrar más líneas
Validaciones realizadas

docker-compose.yml funcional con versión 3.8 (aunque se recomienda omitir version en versiones recientes).
Red app_net creada automáticamente.
Servicios api y db comunicándose correctamente.
healthcheck de PostgreSQL funcionando (healthy).
API responde correctamente y se conecta a la base de datos.
Evidencias documentadas en docs/evidencias.
