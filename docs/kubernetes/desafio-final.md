---
sidebar_position: 11
---

# Desafío final de Kubernetes

## Objetivo

Desplegar una web en Kubernetes, comprobar su acceso, escalarla y diagnosticar un error. Realiza toda la práctica en un **playground de Killercoda o KodeKloud**.

## Preparación

1. Inicia una sesión Kubernetes y registra la plataforma, fecha y contexto.
2. Verifica que los nodos estén preparados.
3. Crea el namespace `curso-k8s` como se explicó en la lección de plataformas.
4. Recupera `web.yaml` del tema de manifiestos o créalo siguiendo ese ejemplo. Si hay recursos `web` de las prácticas imperativas, realiza primero la limpieza indicada allí.

## Actividades

1. Aplica el manifiesto con un Deployment de **dos réplicas**, un Service **ClusterIP** y etiquetas coincidentes. Comprueba el rollout y el estado de los Pods.
2. Consulta el Service y sus EndpointSlices. Ejecuta el Pod cliente de la lección Services y guarda la respuesta HTTP.
3. Cambia a **tres réplicas en YAML**, aplica y comprueba el resultado.
4. Elimina un Pod del Deployment. Registra su nombre y el del reemplazo; verifica que vuelvan a existir tres disponibles.
5. Reproduce el fallo de imagen en el Deployment independiente `diagnostico`, como en la lección de diagnóstico. Guarda el evento, corrige la imagen y verifica su recuperación.
6. Guarda tus archivos y evidencias fuera del playground.
7. Elimina el namespace del curso en tu laboratorio:

   ```bash
   kubectl delete namespace curso-k8s
   kubectl get namespace curso-k8s
   ```

   El resultado esperado de la última consulta, una vez terminada la eliminación, es `NotFound`.

## Entregables

- `web.yaml` final, con tres réplicas.
- Un `README.md` con plataforma utilizada, contexto, pasos para reproducir, comprobación HTTP y limpieza.
- Evidencias de dos y tres réplicas, respuesta HTTP, reemplazo del Pod, fallo y corrección, y namespace eliminado.
- Un párrafo que explique la relación **Deployment → ReplicaSet → Pods**, el papel del Service y dos diferencias respecto de Compose.

## Criterios de revisión

La práctica está completa cuando el manifiesto se puede reproducir en una sesión nueva, la web responde, el Deployment recupera las réplicas y el diagnóstico tiene evidencia de causa y corrección. La limpieza y la conservación de los archivos forman parte de la entrega.

No se requiere Ingress, almacenamiento persistente, una cuenta de nube ni conectar GitHub Actions al playground.
