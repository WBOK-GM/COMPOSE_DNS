# Ejercicio #1 – Docker Compose con Flask API y Neo4j

## Descripción
Este ejercicio consiste en desplegar dos servicios con **Docker Compose**:
- Una **API en Flask** con endpoints `GET` y `POST`.
- Una **base de datos Neo4j**.
---

## Servicios
1. **Flask API (`flask_api`)**  
   - Expone en el puerto `5000`.  
   - Endpoints:  
     - `GET /peliculas?page=N`: devuelve los datos paginados (50 por página).  
     - `GET /peliculas/all`: devuelve todos los registros.  
     - `POST /peliculas`: crea una nueva película con datos aleatorios.  

2. **Neo4j (`neo4j_db`)**  
   - Imagen: `neo4j:5.22.0-community`  
   - Expone puertos `7474` (HTTP) y `7687` (Bolt).  
---

## Paso 2 – Conexión usando red *bridge* por defecto

👉 En este caso, los contenedores pueden comunicarse usando el **nombre del servicio como DNS**.  

contenedores Corriendo.
<img width="624" height="258" alt="image" src="https://github.com/user-attachments/assets/d706baef-4d44-4f70-a39a-f7d0c3b5bf7d" />


Evidencia del docker-compose.yml

services:
  neo4j-walter:
    image: neo4j:5.22.0-community
    container_name: neo4j_db_walter
    restart: always
    environment:
      NEO4J_AUTH: "${NEO4J_USER}/${NEO4J_PASSWORD}"
    ports:
      - "7474:7474"  # Panel web
      - "7687:7687"  # Bolt (para clientes externos)
    volumes:
      - neo4j_data:/data

  backend:
    build: ./backend
    container_name: express_backend
    restart: always
    depends_on:
      - neo4j-walter
    env_file:
      - .env
    ports:
      - "${EXPRESS_PORT:-3000}:3000"

  flask_api:
    build: ./flask_api
    container_name: flask_api
    restart: always
    depends_on:
      - neo4j-walter
    env_file:
      - .env
    ports:
      - "5000:5000"

volumes:
  neo4j_data:
    driver: local

El .env se modifica usando el nombre "DNS" del neo4j 

NEO4J_URI=bolt://neo4j-walter:7687
NEO4J_USER=neo4j
NEO4J_PASSWORD=supersecurepassword

Con esto ya queda con usando DNS, para evitar colocar la ip siempre que se reinicie. 

Al hacer las pruebas para obtener las peliculas nos funciona con normalidad

<img width="624" height="258" alt="image" src="https://github.com/user-attachments/assets/ee22503c-6611-4fc9-b02b-b4007a42d785" />

El mismo caso para la creación peliculas.

<img width="624" height="258" alt="image" src="https://github.com/user-attachments/assets/276feea8-3686-44a4-9902-e540f33b315d" />



