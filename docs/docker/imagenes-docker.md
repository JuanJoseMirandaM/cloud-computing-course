---
sidebar_position: 5
---

# Imágenes Docker

Las imágenes son plantillas inmutables con las que se crean contenedores. Aprende a listarlas, descargarlas, etiquetar y entender capas.

---

## Qué es una imagen

Una **imagen** es un sistema de archivos de solo lectura por capas más metadatos (comando por defecto, variables de entorno, etc.). Un **contenedor** es una instancia en ejecución de una imagen (con una capa de escritura encima).

- **Imagen** = plantilla.
- **Contenedor** = proceso(es) corriendo a partir de esa plantilla.

---

## Listar y buscar imágenes

**Imágenes locales:**

```bash
docker images
# o
docker image ls
```

**Buscar imágenes en Docker Hub:**

```bash
docker search nginx
docker search python
```

**Descargar (pull) sin ejecutar:**

```bash
docker pull nginx
docker pull nginx:1.25-alpine
docker pull python:3.11-slim
```

Si no indicas etiqueta, se usa `latest`.

---

## Etiquetas (tags)

Las imágenes se identifican por **nombre:etiqueta**. Ejemplos:

```bash
docker pull ubuntu:22.04
docker pull ubuntu:latest
docker pull nginx:1.25-alpine
```

**Ver todas las etiquetas de una imagen local:**

```bash
docker images nginx
```

---

## Inspeccionar una imagen

**Metadatos (capas, tamaño, historial):**

```bash
docker image inspect nginx
```

**Historial de capas (cómo se construyó):**

```bash
docker history nginx
```

Cada línea es una capa; las imágenes se construyen por capas superpuestas, lo que permite reutilización y caché.

---

## Eliminar imágenes

**Por nombre o ID:**

```bash
docker rmi nginx
docker rmi abc123def456
```

**Forzar si hay contenedores usando la imagen:**

```bash
docker rmi -f nginx
```

**Eliminar imágenes sin usar (dangling y sin referencias):**

```bash
docker image prune
```

**Eliminar todas las imágenes no usadas por ningún contenedor:**

```bash
docker image prune -a
```

---

## Crear una imagen: Dockerfile (resumen)

Un **Dockerfile** define los pasos para construir una imagen. Ejemplo mínimo:

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

Construir la imagen y darle nombre/etiqueta:

```bash
docker build -t mi-web:1.0 .
```

El `.` indica el contexto (carpeta actual). Luego puedes ejecutar:

```bash
docker run -d -p 8080:80 mi-web:1.0
```

Construir de nuevo aprovecha la **caché de capas**: solo se reconstruyen los pasos que cambian.

---

## Subir a un registro (Docker Hub)

**Login:**

```bash
docker login
```

**Etiquetar la imagen con tu usuario y repositorio:**

```bash
docker tag mi-web:1.0 tu-usuario/mi-web:1.0
```

**Subir (push):**

```bash
docker push tu-usuario/mi-web:1.0
```

---

## Resumen de comandos

```bash
# Listar y buscar
docker images
docker search nginx
docker pull nginx:alpine

# Inspección
docker image inspect nginx
docker history nginx

# Eliminar
docker rmi nginx
docker image prune -a

# Construir
docker build -t mi-web:1.0 .
docker run -d -p 8080:80 mi-web:1.0

# Registrar
docker tag mi-web:1.0 usuario/repo:1.0
docker push usuario/repo:1.0
```

---

## Reto del día

1. Descarga la imagen `python:3.11-slim`. Con `docker history python:3.11-slim` cuenta cuántas capas tiene y anota el tamaño de la primera capa (SIZE).
2. Crea un Dockerfile que use `FROM nginx:alpine` y copie un archivo `hola.html` a `/usr/share/nginx/html/`. Construye la imagen con etiqueta `mi-nginx:v1`. Ejecuta un contenedor y comprueba en el navegador que se sirve `hola.html`. Modifica `hola.html`, vuelve a construir con la misma etiqueta y comprueba que el contenedor nuevo sirve el contenido actualizado.
3. (Opcional) Crea una cuenta en Docker Hub, haz `docker login`, etiqueta tu imagen `mi-nginx:v1` como `tu-usuario/mi-nginx:v1` y haz `docker push`. Comprueba en hub.docker.com que la imagen aparece en tu repositorio.
