---
sidebar_position: 3
---

# Gestión de contenedores

Aprende a ejecutar contenedores en segundo plano, su ciclo de vida y cómo copiar archivos entre el host y el contenedor.

---

## Contenedor en segundo plano (detached)

Por defecto, `docker run` une la salida del contenedor a tu terminal. Para dejarlo corriendo en segundo plano usa **`-d`**:

```bash
docker run -d --name servidor nginx
```

Comprobar que está en ejecución:

```bash
docker ps
```

Para volver a ver la salida de un contenedor que está en segundo plano:

```bash
docker logs servidor
docker logs -f servidor   # seguir en tiempo real (Ctrl+C para salir)
```

---

## Ciclo de vida de un contenedor

Un contenedor puede estar en varios estados. Comandos típicos:

**Crear y ejecutar:**

```bash
docker run -d --name mi-app nginx
```

**Pausar (suspende los procesos sin terminar el contenedor):**

```bash
docker pause mi-app
docker unpause mi-app
```

**Detener (envía SIGTERM y luego SIGKILL si hace falta):**

```bash
docker stop mi-app
```

**Iniciar de nuevo un contenedor ya existente:**

```bash
docker start mi-app
```

**Reiniciar:**

```bash
docker restart mi-app
```

**Eliminar (el contenedor debe estar parado):**

```bash
docker stop mi-app
docker rm mi-app
```

**Forzar eliminación de un contenedor en ejecución:**

```bash
docker rm -f mi-app
```

---

## Copiar archivos: host → contenedor

**`docker cp`** copia archivos o directorios entre el host y un contenedor.

**De host a contenedor:**

```bash
# Crear un archivo de prueba en el host
echo "<h1>Hola desde el host</h1>" > index.html

# Copiarlo al contenedor (reemplaza la página por defecto de Nginx)
docker cp index.html mi-nginx:/usr/share/nginx/html/index.html
```

Si el contenedor está parado, puedes arrancarlo después:

```bash
docker start mi-nginx
```

**Copiar un directorio entero al contenedor:**

```bash
docker cp ./mi-carpeta/ mi-nginx:/usr/share/nginx/html/
```

---

## Copiar archivos: contenedor → host

**De contenedor a host:**

```bash
docker cp mi-nginx:/usr/share/nginx/html/index.html ./index-desde-contenedor.html
```

**Copiar un directorio del contenedor al host:**

```bash
docker cp mi-nginx:/etc/nginx/ ./nginx-config-backup
```

---

## Listar y limpiar contenedores

**Contenedores en ejecución:**

```bash
docker ps
```

**Todos los contenedores (incluidos parados):**

```bash
docker ps -a
```

**Solo los ID de contenedores parados (útil para limpiar):**

```bash
docker ps -aq -f status=exited
```

**Eliminar todos los contenedores parados:**

```bash
docker container prune
```

---

## Resumen de comandos

```bash
# Segundo plano
docker run -d --name servidor nginx
docker logs -f servidor

# Ciclo de vida
docker start servidor
docker stop servidor
docker restart servidor
docker pause servidor
docker unpause servidor
docker rm -f servidor

# Copiar host → contenedor
docker cp index.html servidor:/usr/share/nginx/html/index.html
docker cp ./carpeta/ servidor:/ruta/destino/

# Copiar contenedor → host
docker cp servidor:/ruta/archivo.txt ./
docker cp servidor:/ruta/carpeta/ ./backup/

# Limpieza
docker ps -a
docker container prune
```

---

## Reto del día

1. Crea un contenedor Nginx en segundo plano con nombre `web2`. Sin usar `docker exec`, copia un archivo `hola.html` desde tu máquina a `/usr/share/nginx/html/` del contenedor. Recarga http://localhost (o el puerto que hayas mapeado) y comprueba que se sirve tu página.
2. Usa `docker cp` para copiar el archivo de configuración por defecto de Nginx (`/etc/nginx/nginx.conf`) del contenedor a tu host en un archivo `nginx.conf.backup`. Abre el archivo y anota la primera directiva que aparece.
3. Ejecuta `docker pause web2`, comprueba con `docker ps` que aparece como "Paused", y luego haz `docker unpause web2`. Documenta la diferencia entre `docker stop` y `docker pause` en una frase.
