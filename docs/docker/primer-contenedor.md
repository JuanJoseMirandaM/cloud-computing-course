---
sidebar_position: 2
---

# Primer contenedor

Verifica tu instalación, ejecuta "Hello World" y lanza un contenedor real con Nginx. Luego inspecciona el contenedor.

---

## Comprobar versión de Docker

Antes de nada, confirma que Docker está instalado y accesible:

```bash
docker --version
```

Ejemplo de salida: `Docker version 24.0.x, build xxxxx`.

Para más detalle (cliente, servidor, motores):

```bash
docker version
```

---

## Hello World

El clásico primer contenedor:

```bash
docker run hello-world
```

Qué hace este comando:

1. Busca la imagen `hello-world` en tu máquina.
2. Si no existe, la descarga desde Docker Hub.
3. Crea un contenedor a partir de la imagen y lo ejecuta.
4. El contenedor imprime un mensaje y termina.

Si ves **"Hello from Docker!"** y un texto explicativo, todo está correcto.

---

## Algo más real: Nginx

Nginx es un servidor web. Ejecutarlo en un contenedor te da un servidor HTTP en segundos.

**Ejecutar Nginx en primer plano (bloquea la terminal):**

```bash
docker run nginx
```

Para probarlo en segundo plano y poder seguir usando la terminal, usa `-d` (detached):

```bash
docker run -d --name mi-nginx -p 8080:80 nginx
```

- `-d`: ejecuta en segundo plano.
- `--name mi-nginx`: nombre del contenedor.
- `-p 8080:80`: mapea el puerto 80 del contenedor al 8080 del host.

Abre en el navegador: **http://localhost:8080**. Deberías ver la página de bienvenida de Nginx.

**Detener y eliminar el contenedor:**

```bash
docker stop mi-nginx
docker rm mi-nginx
```

---

## Inspeccionar un contenedor

Con el contenedor creado (por ejemplo `mi-nginx`), puedes inspeccionarlo:

**Ver detalles en JSON (configuración, red, volúmenes, etc.):**

```bash
docker inspect mi-nginx
```

**Solo la dirección IP del contenedor:**

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mi-nginx
```

**Ver logs del contenedor:**

```bash
docker logs mi-nginx
```

**Seguir los logs en tiempo real:**

```bash
docker logs -f mi-nginx
```

**Estadísticas de uso (CPU, memoria):**

```bash
docker stats mi-nginx
```

**Ejecutar un comando dentro del contenedor (si tiene shell):**

```bash
docker exec -it mi-nginx sh
# o en imágenes basadas en Debian/Ubuntu:
docker exec -it mi-nginx bash
```

Para salir del shell: `exit`.

---

## Resumen de comandos de esta página

```bash
# Versión
docker --version
docker version

# Hello World
docker run hello-world

# Nginx en segundo plano
docker run -d --name mi-nginx -p 8080:80 nginx

# Inspección y logs
docker inspect mi-nginx
docker logs mi-nginx
docker logs -f mi-nginx
docker stats mi-nginx
docker exec -it mi-nginx sh

# Limpieza
docker stop mi-nginx
docker rm mi-nginx
```

---

## Reto del día

1. Ejecuta `docker run hello-world` y anota la primera línea del mensaje que imprime.
2. Crea un contenedor Nginx con nombre `web1` en el puerto **9090** del host. Abre http://localhost:9090 y haz una captura.
3. Con `docker inspect web1`, localiza el campo **"Image"** en el JSON y anota el valor.
4. Usa `docker exec` para entrar al contenedor `web1`, listar el directorio `/usr/share/nginx/html` con `ls` y salir. Documenta el comando que usaste.
