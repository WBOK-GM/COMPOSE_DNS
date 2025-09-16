# Ejercicio #1 – Docker Compose con Flask API y Neo4j

## Descripción
Este ejercicio consiste en desplegar dos servicios con **Docker Compose**:
- Una **API en Flask** con endpoints `GET` y `POST`.
- Una **base de datos Neo4j**.

---

## Servicios
### 1. Flask API (`flask_api`)  
- Expone en el puerto `5000`.  
- Endpoints implementados:  
  - `GET /peliculas?page=N` → devuelve los datos paginados (50 por página).  
  - `GET /peliculas/all` → devuelve todos los registros.  
  - `POST /peliculas` → crea una nueva película con datos aleatorios.  

### 2. Neo4j (`neo4j-walter`)  
- Imagen usada: `neo4j:5.22.0-community`.  
- Expone los puertos:  
  - `7474` → HTTP (interfaz web).  
  - `7687` → Bolt (para clientes externos).  

---

## 🔹 Paso 2 – Conexión usando red *bridge* por defecto

👉 En este escenario, los contenedores pueden comunicarse usando el **nombre del servicio como DNS** en lugar de la IP.  

### 🐳 Contenedores corriendo
<p align="center">
  <img width="624" height="258" alt="Contenedores corriendo" src="https://github.com/WBOK-GM/COMPOSE_DNS/blob/main/Images/Screenshot%20From%202025-09-16%2007-16-02.png" />
</p>

---

## 📄 Evidencia del `docker-compose.yml`

```yaml
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
```

El .env se modifica usando el nombre "DNS" del neo4j 

NEO4J_URI=bolt://neo4j-walter:7687
NEO4J_USER=neo4j
NEO4J_PASSWORD=supersecurepassword

Con esto ya queda con usando DNS, para evitar colocar la ip siempre que se reinicie. 

Al hacer las pruebas para obtener las peliculas nos funciona con normalidad

<img width="624" height="258" alt="image" src="https://github.com/WBOK-GM/COMPOSE_DNS/blob/main/Images/Screenshot%20From%202025-09-16%2007-31-05.png" />

El mismo caso para la creación peliculas.

<img width="624" height="258" alt="image" src="https://github.com/WBOK-GM/COMPOSE_DNS/blob/main/Images/Screenshot%20From%202025-09-16%2007-32-10.png" />



