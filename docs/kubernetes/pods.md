---
sidebar_position: 5
---

# Pods: tu primera aplicación

## Unidad de ejecución

Un **Pod** contiene uno o varios contenedores que comparten red y pueden compartir volúmenes. En esta introducción usaremos un contenedor por Pod. Un Pod tiene un ciclo de vida: si se elimina, su identidad e IP no se conservan.

## Práctica en el playground

Prepara el namespace en Killercoda o KodeKloud:

```bash
kubectl create namespace curso-k8s --dry-run=client -o yaml | kubectl apply -f -
```

```bash
kubectl run primer-pod -n curso-k8s --image=nginx:stable-alpine --port=80
kubectl wait -n curso-k8s --for=condition=Ready pod/primer-pod --timeout=120s
kubectl get pods -n curso-k8s -o wide
kubectl describe pod primer-pod -n curso-k8s
kubectl logs primer-pod -n curso-k8s
kubectl exec primer-pod -n curso-k8s -- nginx -v
```

`--port=80` documenta el puerto del contenedor; no publica un puerto en tu computador. `Running` indica la fase del Pod; revisa también `READY` para conocer la preparación de sus contenedores.

## Eliminar y observar

```bash
kubectl delete pod primer-pod -n curso-k8s
kubectl get pods -n curso-k8s
```

Este Pod se creó directamente y no tiene un Deployment que lo reemplace. En el siguiente tema veremos esa diferencia.

## Desafío

Crea el Pod, identifica el nodo asignado, consulta sus logs y elimínalo.

**Entrega:** capturas antes y después de eliminarlo, con una explicación de por qué no reaparece.

**Referencia:** [Pods](https://kubernetes.io/docs/concepts/workloads/pods/).
