---
sidebar_position: 7
---

# Desafío final Docker

Integra todo lo visto: contenedores, redes, volúmenes, imágenes y Docker Compose en un único proyecto.

---

## Objetivo

Montar una **mini aplicación** formada por:

- Un **servidor web** (Nginx) que sirva una página estática.
- Opcional: un **backend** o una **base de datos** en contenedor (por ejemplo Redis o un script que genere datos).
- Uso de **red**, **volúmenes** (o bind mount) y **Docker Compose** para orquestarlo.

---

## Requisitos mínimos

1. **Docker Compose** con al menos un servicio que sirva contenido web en un puerto del host.
2. Los archivos de la web deben estar en una **carpeta del host** (bind mount) para poder editarlos sin reconstruir la imagen.
3. Si usas más de un servicio, deben estar en la **misma red** (Compose lo hace por defecto) y poder comunicarse por nombre.
4. Documenta en un **README.md**:
   - Cómo levantar el proyecto (`docker compose up -d`).
   - Qué URL usar (ej. http://localhost:8080).
   - Un párrafo explicando la arquitectura (qué hace cada servicio y cómo se conectan).

---

## Nivel básico

- Un solo servicio: **Nginx** con un `index.html` en una carpeta local.
- `docker-compose.yml` con ese servicio, puerto mapeado y volumen (bind mount) a la carpeta con el HTML.

Comandos de referencia:

```bash
mkdir -p mi-desafio/public
echo "<h1>Desafio Docker</h1>" > mi-desafio/public/index.html
cd mi-desafio
```

Crea un `docker-compose.yml` con un servicio `web` (imagen `nginx:alpine`, puerto 8080, volumen `./public:/usr/share/nginx/html:ro`). Luego:

```bash
docker compose up -d
```

Comprueba http://localhost:8080.

---

## Nivel intermedio

- **Dos servicios**: por ejemplo `web` (Nginx) y `api` (un contenedor que responda algo, p. ej. un pequeño servidor en Python/Node o un `alpine` con `nc` que escuche en un puerto).
- Red creada por Compose; la web puede llamar a la API por nombre de servicio (ej. `http://api:3000`).
- Opcional: volumen con nombre para persistir datos de la “API” o de una base de datos.

---

## Nivel avanzado

- **Tres servicios**: frontend (Nginx o estático), backend (API) y base de datos (PostgreSQL, MySQL o Redis).
- Variables de entorno en `docker-compose.yml` o en un `.env` (ej. contraseña de la DB, URL del backend).
- Volúmenes con nombre para la base de datos y, si aplica, para datos del backend.
- Un **Dockerfile** para construir la imagen del backend (o del frontend si lo generas con build).

---

## Entregables sugeridos

1. Carpeta del proyecto con:
   - `docker-compose.yml`
   - Carpeta con el contenido web (y Dockerfile(s) si los usas).
   - `README.md` con instrucciones y descripción de la arquitectura.
2. Captura de `docker compose ps` con todos los servicios en ejecución.
3. Captura del navegador mostrando la página (y, si aplica, una respuesta de la API o de la DB).

---

## Reto del día (resumen)

Elige **al menos** el nivel básico:

1. Crea el proyecto con `docker-compose.yml` y la carpeta de contenido web.
2. Levanta con `docker compose up -d` y verifica que la página se ve en el navegador.
3. Escribe el README con pasos para levantar el proyecto y una breve descripción de la arquitectura.
4. (Opcional) Añade un segundo servicio (API o DB) y documenta cómo se conectan.

Cuando lo tengas, comparte la estructura de tu proyecto (o el repo) y las capturas indicadas.
