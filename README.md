# Proyecto: parcial-docker-integrado

## Estudiante
- **Nombre:** Cristian Alejandro Rivas Rodríguez
- **Expediente:** TU_EXPEDIENTE
- **Código Estudiantil:** TU_CODIGO_ESTUDIANTIL

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
