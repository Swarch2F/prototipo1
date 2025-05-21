# GRADEX Prototipo 1

## Introducción

GRADEX es un prototipo vertical compuesto por tres servicios Docker claramente identificados por los nombres definidos en el `docker-compose.yml`. Cada servicio tiene un propósito específico dentro de la arquitectura:

* **gx\_be\_estcur:** API REST desarrollada con Django que utiliza PostgreSQL.
* **gx\_db\_estcur:** Base de datos PostgreSQL para almacenar información relacional.
* **gx\_db\_proasigcal:** Base de datos NoSQL MongoDB utilizada por la API GraphQL.
* **gx\_be\_proasigcal:** API GraphQL desarrollada con Spring Boot que se conecta con MongoDB.
* **gx\_fe\_gradex:** Aplicación frontend desarrollada con NestJS que interactúa con las APIs.

Cada servicio fue configurado como un submódulo Git dentro del directorio `components/`, permitiendo mantener separados y reutilizables los distintos componentes del proyecto.

## Repositorio del proyecto

Para utilizar el proyecto simplemente clona el repositorio principal, el cual ya incluye todos los submódulos necesarios:

```bash
git clone --recursive https://github.com/Swarch2F/prototipo1.git
cd prototipo1
```

## Información sobre submódulos (Solo informativo)

Los submódulos fueron agregados inicialmente con estos comandos, pero no necesitas ejecutarlos nuevamente:

```bash
git submodule add https://github.com/Swarch2F/component-1.git components/component-1
git submodule add https://github.com/Swarch2F/component-2.git components/component-2
git submodule add https://github.com/Swarch2F/component-3.git components/component-3
```

Para actualizar los submódulos en caso de modificaciones (solo referencia):

```bash
git submodule update --remote --merge --recursive
```

## Levantar el prototipo con Docker Compose

El proyecto utiliza Docker Compose para gestionar la ejecución de todos los servicios.

### Archivo `docker-compose.yml`

```yaml
services:
  # ----- API REST con Postgres -----
  gx_be_estcur:
    build:
      context: ./components/component-1
      dockerfile: Dockerfile
    image: gx_be_estcur:latest
    container_name: gx_be_estcur
    command: gunicorn
    restart: always
    volumes:
      - ./components/component-1:/app
    ports:
      - "8000:8000"
    environment:
      - DB_HOST=gx_db_estcur
      - DB_NAME=sia_colegios
      - DB_USER=postgres
      - DB_PASSWORD=postgres
      - DB_PORT=5432
      - LOAD_INITIAL_DATA=true
    depends_on:
      - gx_db_estcur
    networks:
      - sia_network

  # ----- Base de datos relacional Postgres -----
  gx_db_estcur:
    image: postgres:17-alpine
    container_name: gx_db_estcur
    volumes:
      - postgres_data:/var/lib/postgresql/data/
      - ./components/component-1/init-db.sh:/docker-entrypoint-initdb.d/init-db.sh
    environment:
      - POSTGRES_DB=sia_colegios
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
      - POSTGRES_ENCODING=UTF8
      - POSTGRES_LC_COLLATE=es_CO.utf8
      - POSTGRES_LC_CTYPE=es_CO.utf8
    ports:
      - "5432:5432"
    networks:
      - sia_network

  # ----- Base de datos NoSQL MongoDB -----
  gx_db_proasigcal:
    image: mongo:6.0
    container_name: gx_db_proasigcal
    restart: always
    ports:
      - "27018:27017"
    volumes:
      - mongo_data:/data/db
    networks:
      - sia_network

  # ----- API GraphQL con MongoDB -----
  gx_be_proasigcal:
    build:
      context: ./components/component-2
      dockerfile: Dockerfile
    image: gx_be_proasigcal:latest
    container_name: gx_be_proasigcal
    ports:
      - "8080:8080"
    environment:
      - SPRING_DATA_MONGODB_URI=mongodb://gx_db_proasigcal:27017/db
      - SPRING_GRAPHQL_SCHEMA_LOCATION=classpath:/graphql/
    depends_on:
      - gx_db_proasigcal
    networks:
      - sia_network

  # ----- Aplicación frontend NestJS -----
  gx_fe_gradex:
    build:
      context: ./components/component-3
      dockerfile: Dockerfile
    image: gx_fe_gradex:latest
    container_name: gx_fe_gradex
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    networks:
      - sia_network

volumes:
  mongo_data:
  postgres_data:

networks:
  sia_network:
    driver: bridge
```

### Ejecución rápida

Una vez clonado el proyecto, ejecuta:

```bash
docker compose up --build
```

## Acceso a servicios

Puedes acceder a cada servicio desde tu navegador en las siguientes rutas:

* **API REST (gx\_be\_estcur):** `http://localhost:8000/`
* **API GraphQL (gx\_be\_proasigcal):** `http://localhost:8080/graphql`
* **Aplicación Frontend (gx\_fe\_gradex):** `http://localhost:3000/`

Para verificar el estado de los contenedores utiliza:

```bash
docker ps
```

---

**© 2025 Swarch2F. GRADEX Prototipo 1**
