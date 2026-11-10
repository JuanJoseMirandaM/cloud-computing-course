---
sidebar_position: 6
---

# Deployments y réplicas

## Mantener un estado deseado

Un **Deployment** administra un conjunto de Pods idénticos. Defines el estado deseado —por ejemplo “3 réplicas de mi web con la imagen `nginx:stable-alpine`”— y el controlador se encarga de crearlos, actualizarlos y reemplazarlos.

Por debajo usa **ReplicaSets**: mantienen el número deseado de Pods. Si eliminas uno, el controlador ve que hay 2 en lugar de 3 y lanza un reemplazo. Eso es **self-healing** (auto-recuperación).

No garantiza magia absoluta: si el clúster no tiene capacidad o la aplicación está mal configurada, el estado deseado no se alcanza.

![](https://i.sstatic.net/idLaH.gif)

## Crear y escalar

En una sesión Kubernetes de Killercoda o KodeKloud:

```bash
kubectl create namespace curso-k8s --dry-run=client -o yaml | kubectl apply -f -
```

```bash
kubectl create deployment web -n curso-k8s --image=nginx:stable-alpine --replicas=2
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
kubectl get deployments,replicasets,pods -n curso-k8s
kubectl scale deployment/web -n curso-k8s --replicas=3
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
kubectl get pods -n curso-k8s -l app=web
```

Si `web` ya existe de una práctica anterior, inspecciónalo antes de continuar; no vuelvas a ejecutar `create`. Para repetir desde cero elimina solo ese Deployment en tu laboratorio y créalo otra vez.

### Prueba 1: self-healing

Copia el nombre de uno de sus Pods y úsalo en lugar de `NOMBRE_DEL_POD`:

```bash
kubectl get pods -n curso-k8s -l app=web
kubectl delete pod NOMBRE_DEL_POD -n curso-k8s
kubectl get pods -n curso-k8s -l app=web -w
```

En cuanto desaparece el Pod, el Deployment detecta que el conteo actual (2) no coincide con el deseado (3) y crea uno nuevo. Sal de la observación con **Ctrl+C**.

### Prueba 2: escalar arriba y abajo

```bash
kubectl scale deployment/web -n curso-k8s --replicas=5
kubectl get pods -n curso-k8s -l app=web
kubectl scale deployment/web -n curso-k8s --replicas=2
kubectl get pods -n curso-k8s -l app=web
```

Kubernetes termina de forma ordenada los Pods sobrantes. Más adelante preferiremos cambiar `replicas` en el YAML y hacer `kubectl apply` (fuente de verdad en Git).

## Actualizar y volver atrás

```bash
kubectl set image deployment/web -n curso-k8s nginx=nginx:alpine
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
kubectl rollout history deployment/web -n curso-k8s
kubectl rollout undo deployment/web -n curso-k8s
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
```

El cambio de imagen crea una **revisión**. En un pipeline reproducible usa versiones explícitas o digests. Aquí estudiamos el mecanismo de actualización y reversión, no una versión concreta de Nginx.

## Desafío

Escala a tres réplicas, elimina un Pod y comprueba que vuelve a haber tres disponibles. Realiza una actualización y revierte la revisión.

**Entrega:** evidencia de réplicas y del historial de despliegue. Si continúas con Services, conserva `web`.

**Referencia:** [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).
