---
sidebar_position: 10
---

# De Docker Compose a Kubernetes

## Conectar lo aprendido

En Compose describimos servicios que suelen ejecutarse en un host Docker. Kubernetes administra recursos de un clúster y reconcilia su estado. No existe una equivalencia exacta entre todos sus campos.

- **`services.web` de Compose:** su carga de trabajo puede representarse con un Deployment y sus Pods.
- **`image`:** sigue identificando una imagen publicada en un registro accesible para los nodos.
- **`ports`:** Kubernetes separa puertos del contenedor, Service y mecanismos de acceso externo.
- **Nombre de servicio:** en Kubernetes un Service permite descubrir la aplicación sin depender de la IP de sus Pods.
- **Réplicas:** se declaran en el Deployment y su controlador mantiene la cantidad deseada.
- **Red:** no copiamos una red de Compose; usamos la red de Pods y los Services del clúster.
- **`depends_on`:** no tiene una traducción directa que garantice que otra aplicación esté lista. Diseña reintentos y comprobaciones de preparación.
- **Bind mounts y volúmenes:** no se trasladan automáticamente; el almacenamiento de Kubernetes requiere decisiones adicionales, fuera de este módulo.

## Ejemplo de origen

```yaml title="compose.yaml"
services:
  web:
    image: nginx:stable-alpine
    ports:
      - "8080:80"
```

El manifiesto `web.yaml` del tema anterior representa esta carga con un Deployment y un Service ClusterIP. Para acceder por `8080` desde la terminal del playground podemos usar `port-forward`; un Service ClusterIP por sí solo no publica ese puerto hacia el exterior.

## Relación con CI/CD

El pipeline construye y prueba una imagen, la publica en un registro y actualiza su referencia en el manifiesto. Kubernetes descarga esa imagen y ejecuta el despliegue. Los playgrounds temporales del curso no deben convertirse en destinos permanentes del pipeline ni requerir que publiquemos sus credenciales.

## Desafío en Killercoda o KodeKloud

Despliega `web.yaml` siguiendo la lección de manifiestos. Comprueba HTTP desde un Pod cliente y explica cómo se relaciona con el ejemplo Compose.

**Entrega:** manifiesto y cinco correspondencias explicadas, incluida una diferencia de red. No es necesario ejecutar Docker Compose en el playground.

**Referencias:** [Workloads](https://kubernetes.io/docs/concepts/workloads/), [Services](https://kubernetes.io/docs/concepts/services-networking/service/).
