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

