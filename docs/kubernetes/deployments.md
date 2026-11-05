---
sidebar_position: 6
---

# Deployments y réplicas

## Mantener un estado deseado

Un **Deployment** administra ReplicaSets; estos mantienen el número deseado de Pods. Si eliminas uno de esos Pods, el controlador crea un reemplazo. Esto no garantiza disponibilidad si el clúster no tiene capacidad o la aplicación está mal configurada.

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

Copia el nombre de uno de sus Pods y úsalo en lugar de `NOMBRE_DEL_POD`:

```bash
kubectl delete pod NOMBRE_DEL_POD -n curso-k8s
kubectl get pods -n curso-k8s -l app=web -w
```

Observa la creación del reemplazo. Sal de la observación con **Ctrl+C**.

## Actualizar y volver atrás

```bash
kubectl set image deployment/web -n curso-k8s nginx=nginx:alpine
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
kubectl rollout history deployment/web -n curso-k8s
kubectl rollout undo deployment/web -n curso-k8s
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
```

El cambio de referencia de imagen crea una revisión; esas etiquetas pueden apuntar a versiones variables. En un pipeline reproducible usa versiones explícitas o digests. Aquí estudiamos el mecanismo de actualización y reversión, no una versión concreta de Nginx.

## Desafío

Escala a tres réplicas, elimina un Pod y comprueba que vuelve a haber tres disponibles. Realiza una actualización y revierte la revisión.

**Entrega:** evidencia de réplicas y del historial de despliegue. Si continúas con Services, conserva `web`.

**Referencia:** [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).
