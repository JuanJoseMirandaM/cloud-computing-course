---
sidebar_position: 6
---

# Docker Compose

Docker Compose permite definir y ejecutar **aplicaciones multi-contenedor** con un único archivo YAML y el comando `docker compose`.

---

## Qué es Docker Compose

- Lee un archivo **`docker-compose.yml`** (o `compose.yaml`).
- Crea redes por defecto para que los servicios se vean por nombre.
- Gestiona volúmenes y variables de entorno.
- Un solo comando para levantar o bajar todos los servicios.

Ideal para: app + base de datos, frontend + backend, varios microservicios en local.

---

## Instalación

El plugin **Docker Compose V2** suele venir con Docker Desktop y con la instalación reciente en Linux:

```bash
docker compose version
```

Si no está, en Linux puedes instalar el plugin:

```bash
sudo apt-get install docker-compose-plugin
```

---

## Ejemplo: web + base de datos

Estructura de proyecto:

```
mi-proyecto/
  docker-compose.yml
  html/
    index.html
```

**`docker-compose.yml`:**

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
    depends_on:
      - db

  db:
    image: alpine
    command: sleep 3600
```

Aquí `db` no es realmente una base de datos; es un contenedor que se mantiene vivo. Para una DB real usarías por ejemplo `postgres:15-alpine` o `mysql:8`.

**Levantar todo:**

```bash
cd mi-proyecto
docker compose up -d
```

**Ver logs:**

```bash
docker compose logs -f
docker compose logs -f web
```

**Parar y eliminar contenedores y red:**

```bash
docker compose down
```

**Reconstruir si usas `build:` (ver más abajo):**

```bash
docker compose up -d --build
```

---

## Servicios con build (imagen desde Dockerfile)

Si quieres construir la imagen desde un Dockerfile:

```yaml
services:
  web:
    build: .
    image: mi-web:1.0
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
```

`build: .` usa el Dockerfile del directorio actual. La imagen se guarda como `mi-web:1.0`.

---

## Variables de entorno y volúmenes con nombre

```yaml
services:
  app:
    image: mi-app:1.0
    environment:
      - NODE_ENV=production
      - DB_HOST=db
    env_file:
      - .env
    volumes:
      - datos-app:/app/data
    depends_on:
      - db

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: mydb
    volumes:
      - datos-db:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  datos-app:
  datos-db:
```

Los volúmenes con nombre (`datos-app`, `datos-db`) persisten entre `docker compose down` y `docker compose up`.

---

## Comandos útiles

```bash
# Levantar en segundo plano
docker compose up -d

# Ver estado
docker compose ps

# Logs
docker compose logs -f

# Ejecutar comando en un servicio
docker compose exec web sh
docker compose exec db psql -U user -d mydb -c "SELECT 1;"

# Parar
docker compose stop

# Parar y eliminar contenedores y red (volúmenes se mantienen)
docker compose down

# Parar y eliminar también volúmenes
docker compose down -v
```

---

## Resumen de comandos

```bash
docker compose version
docker compose up -d
docker compose ps
docker compose logs -f
docker compose exec web sh
docker compose stop
docker compose down
docker compose down -v
docker compose up -d --build
```

---

## Reto del día

1. Crea una carpeta `compose-demo` con un `docker-compose.yml` que defina dos servicios: `frontend` (imagen `nginx:alpine`, puerto 8080, montando una carpeta `./public` en `/usr/share/nginx/html`) y `backend` (imagen `alpine`, comando `sleep 3600`). Crea la carpeta `public` y un `index.html` que diga "Hola Compose". Ejecuta `docker compose up -d`, abre http://localhost:8080 y comprueba que ves la página. Ejecuta `docker compose ps` y haz una captura.
2. Añade un volumen con nombre `backend-cache` al servicio `backend`, montado en `/cache`. Con `docker compose exec backend sh` crea un archivo en `/cache/test.txt`. Haz `docker compose down` y luego `docker compose up -d`. Comprueba con `docker compose exec backend cat /cache/test.txt` que el archivo sigue ahí.
3. Añade un servicio `redis` con la imagen `redis:alpine`. Sin exponer puertos al host, comprueba desde `backend` que puedes conectar a Redis ejecutando (si tienes `redis-cli` en backend, o desde otro contenedor temporal) `redis-cli -h redis ping` y que responde `PONG`.
