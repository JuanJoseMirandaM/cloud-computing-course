---
sidebar_position: 1
---

# Introducción a Kubernetes

## ¿Qué problema resuelve?

Docker permite ejecutar una aplicación en contenedores. Cuando hay varias réplicas distribuidas en máquinas diferentes, necesitamos decidir dónde ejecutarlas, reemplazarlas si fallan y permitir que se comuniquen. **Kubernetes**, también llamado **K8s**, automatiza la administración de esas aplicaciones.

Declaramos un **estado deseado**, por ejemplo dos réplicas de una web. Los controladores comparan ese estado con el estado actual y actúan para acercarlos. Este proceso se llama **reconciliación**.

Kubernetes puede desplegar, escalar y actualizar aplicaciones. No construye por sí solo nuestras imágenes ni sustituye las pruebas del pipeline de CI/CD. El pipeline puede publicar una imagen y después actualizar el despliegue del clúster.

## Alcance del módulo

Aprenderemos arquitectura, entorno de trabajo, Pods, Deployments, Services, YAML y diagnóstico. Relacionaremos estos conceptos con Docker Compose y terminaremos con una práctica integradora. Ingress, almacenamiento persistente y gestión de secretos quedan para módulos posteriores.

Todas las pruebas se realizan en **Killercoda o KodeKloud**, usando un playground de Kubernetes. La instalación local es una alternativa opcional.

## Desafío

Describe qué debería hacer el sistema cuando una aplicación necesita pasar de una a tres réplicas y una de ellas se elimina.

**Entrega:** un párrafo que diferencie imagen, contenedor y orquestador.

**Referencia:** [Descripción general de Kubernetes](https://kubernetes.io/docs/concepts/overview/).
