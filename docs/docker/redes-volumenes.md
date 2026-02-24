---
sidebar_position: 4
---

# Redes y volúmenes

Los contenedores necesitan comunicarse entre sí (redes) y conservar datos más allá del ciclo de vida del contenedor (volúmenes).

---

## Redes en Docker

Docker crea una **red virtual por defecto** (`bridge`) para cada contenedor. Los contenedores en la misma red pueden resolverse por **nombre**.

**Listar redes:**

```bash
docker network ls
```

**Crear una red propia:**

```bash
docker network create mi-red
```

**Ejecutar contenedores en esa red:**

```bash
docker run -d --name web --network mi-red nginx
docker run -d --name db --network mi-red alpine sleep 3600
```

Desde dentro de `web`, puedes hacer `ping db` (si el contenedor tiene ping) o usar `http://db:80` porque Docker resuelve el nombre `db` a la IP del contenedor.

**Inspeccionar una red:**

```bash
docker network inspect mi-red
```

**Conectar un contenedor ya existente a una red:**

```bash
docker network connect mi-red nombre-contenedor
```

**Desconectar:**

```bash
docker network disconnect mi-red nombre-contenedor
```

**Eliminar una red (sin contenedores conectados):**

```bash
docker network rm mi-red
```

---

## Tipos de red

| Tipo       | Uso típico                          |
| ---------- | ------------------------------------ |
| **bridge** | Red por defecto en el host (localhost) |
| **host**   | Contenedor usa la red del host directamente |
| **none**   | Sin red                              |

Ejemplo con red `host` (el contenedor escucha en los puertos del host sin mapeo):

```bash
docker run -d --network host nginx
```

---

## Volúmenes persistentes

Por defecto, los datos dentro de un contenedor se pierden al eliminar el contenedor. Los **volúmenes** permiten persistir datos.

**Crear un volumen con nombre:**

```bash
docker volume create mi-datos
```

**Ejecutar un contenedor usando ese volumen:**

```bash
docker run -d --name app -v mi-datos:/data alpine sleep 3600
```

Todo lo que se escriba en `/data` dentro del contenedor se guarda en el volumen `mi-datos`.

**Listar volúmenes:**

```bash
docker volume ls
```

**Inspeccionar un volumen (ruta en disco):**

```bash
docker volume inspect mi-datos
```

**Eliminar un volumen (no debe estar en uso):**

```bash
docker volume rm mi-datos
```

---

## Bind mount (carpeta del host)

En lugar de un volumen con nombre, puedes montar una **carpeta del host** en el contenedor:

```bash
mkdir -p ./html
echo "<h1>Pagina desde el host</h1>" > ./html/index.html
docker run -d --name web -p 8080:80 -v $(pwd)/html:/usr/share/nginx/html:ro nginx
```

- `-v $(pwd)/html:/usr/share/nginx/html:ro`: monta la carpeta local `html` en el directorio de Nginx; `:ro` es solo lectura para el contenedor.

Abre http://localhost:8080 y verás tu `index.html`. Si editas el archivo en el host, el cambio se refleja al recargar.

**Eliminar contenedor y probar que los datos siguen en `./html`:**

```bash
docker rm -f web
ls html/
```

---

## Resumen de comandos

```bash
# Redes
docker network ls
docker network create mi-red
docker run -d --name web --network mi-red nginx
docker network inspect mi-red
docker network connect mi-red contenedor
docker network rm mi-red

# Volúmenes con nombre
docker volume create mi-datos
docker run -d -v mi-datos:/data --name app alpine sleep 3600
docker volume ls
docker volume inspect mi-datos
docker volume rm mi-datos

# Bind mount
docker run -d -v $(pwd)/html:/usr/share/nginx/html:ro -p 8080:80 --name web nginx
```

---

## Reto del día

1. Crea una red `backend` y dos contenedores en ella: uno con `nginx` (nombre `web`) y otro con `alpine` en ejecución (`sleep 3600`, nombre `helper`). Comprueba con `docker network inspect backend` que ambos aparecen. Desde `docker exec -it helper sh` ejecuta `wget -qO- http://web` (o `curl http://web` si Alpine tiene curl) y anota la salida.
2. Crea un volumen `datos-web` y un contenedor Nginx que monte ese volumen en `/usr/share/nginx/html`. Escribe un `index.html` dentro del contenedor (usando `docker exec` y `echo` o un editor) y verifica en el navegador. Elimina el contenedor, crea otro que use el mismo volumen en `/usr/share/nginx/html` y comprueba que la página sigue ahí.
3. Usa un bind mount con una carpeta local `public` y un contenedor Nginx. Modifica un archivo en `public` desde el host y recarga el navegador sin reiniciar el contenedor.
