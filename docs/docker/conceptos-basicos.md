---
sidebar_position: 1
---

# Conceptos básicos de contenedores Docker

Aprende qué es Docker, cómo funciona y por qué es fundamental en DevOps y cloud.

---

## ¿Por qué aprender Docker?

En desarrollo trabajamos con varios entornos: desarrollo, pruebas, staging, producción. Lo que funciona en una máquina puede fallar en otra. **Docker** unifica el entorno.

> Con Docker empaquetas una aplicación **con todo lo que necesita** para que funcione igual en cualquier sitio.

- ❌ "En mi máquina sí funciona..."
- ✅ "Con Docker funciona en todas."

---

## ¿Qué es Docker?

**Docker** es una plataforma open-source para desarrollar, enviar y ejecutar aplicaciones dentro de **contenedores**. Un contenedor es una unidad ligera y portable que incluye todo lo necesario para ejecutar la aplicación: código, runtime, librerías y variables de entorno.

Consulta siempre la [documentación oficial de Docker](https://docs.docker.com/) para guías, referencias de comandos y buenas prácticas.

---

## Arquitectura de Docker

| Componente            | Descripción                                                                 |
| --------------------- | --------------------------------------------------------------------------- |
| **Docker Engine**     | Motor que permite crear y ejecutar contenedores                              |
| **Docker Daemon**     | Servicio en segundo plano que administra contenedores e imágenes             |
| **CLI (`docker`)**    | Línea de comandos para interactuar con Docker                               |
| **Docker Hub**        | Registro público donde se almacenan y comparten imágenes de contenedores    |

### Cómo funciona por dentro

1. **Imágenes**: plantillas inmutables (sistema de archivos, dependencias, comandos).
2. **Contenedores**: instancias en ejecución de una imagen.
3. **Volúmenes**: persisten datos aunque se elimine el contenedor.
4. **Redes**: redes virtuales para que los contenedores se comuniquen entre sí.

---

## Docker vs máquinas virtuales

| Característica       | Contenedor (Docker)   | Máquina virtual    |
| -------------------- | --------------------- | ------------------ |
| Arranque             | Rápido (segundos)     | Lento (minutos)    |
| Tamaño               | Ligero (MB)           | Pesado (GB)        |
| Aislamiento          | Comparten kernel      | Kernel propio      |
| Uso de recursos      | Eficiente             | Mayor              |
| Portabilidad         | Alta                  | Limitada           |

---

## Instalación rápida

**Linux (Debian/Ubuntu):**

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo usermod -aG docker $USER
```

**Windows / macOS:** instala [Docker Desktop](https://www.docker.com/products/docker-desktop/).

**Comprobar instalación:**

```bash
docker --version
```

---

## Comandos básicos (resumen)

| Acción                   | Comando                 |
| ------------------------- | ----------------------- |
| Ver contenedores activos  | `docker ps`             |
| Ver todos los contenedores| `docker ps -a`          |
| Descargar una imagen      | `docker pull <nombre>`  |
| Ejecutar una imagen       | `docker run <nombre>`   |
| Detener un contenedor     | `docker stop <id>`      |
| Eliminar un contenedor    | `docker rm <id>`        |
| Eliminar una imagen       | `docker rmi <id>`       |

---

## Reto del día

1. Instala Docker siguiendo la guía oficial o los pasos anteriores.
2. Ejecuta `docker run hello-world` y comprueba que ves el mensaje de bienvenida.
3. Prueba `docker ps`, `docker images`, `docker pull nginx` y `docker run nginx` (o `alpine`).
4. Comparte una captura de tu primer contenedor funcionando (por ejemplo `docker ps` con un contenedor listado).
