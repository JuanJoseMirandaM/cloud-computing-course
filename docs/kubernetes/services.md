---
sidebar_position: 7
---

# Services y acceso a la aplicación

## Una dirección estable

Las IP de los Pods son **transitorias**: cambian al recrearse. Las aplicaciones no pueden depender de esas IP crudas.

Un **Service** ofrece un endpoint estable (IP virtual + nombre DNS) hacia un grupo de Pods seleccionados por **etiquetas**. El selector del Service debe coincidir con las labels de los Pods.

| Tipo | Uso típico |
| ---- | ---------- |
| **ClusterIP** | Acceso **dentro** del clúster (predeterminado). |
| **NodePort** | También abre un puerto en los nodos; el acceso externo depende de la red del laboratorio. |
| **LoadBalancer** | Pide un balanceador externo si el entorno lo integra. En un playground a menudo queda pendiente. |

## Preparación

En Killercoda o KodeKloud, usa el Deployment `web` de la lección anterior. Si comienzas una sesión nueva:

```bash
kubectl create namespace curso-k8s --dry-run=client -o yaml | kubectl apply -f -
```

```bash
kubectl create deployment web -n curso-k8s --image=nginx:stable-alpine --replicas=2
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
```

## Exponer y comprobar

```bash
kubectl expose deployment web -n curso-k8s --port=80 --target-port=80 --type=ClusterIP
kubectl get service web -n curso-k8s
kubectl get pods -n curso-k8s --show-labels
kubectl get endpointslices -n curso-k8s -l kubernetes.io/service-name=web
kubectl run cliente-http -n curso-k8s --image=curlimages/curl:latest --restart=Never --rm -i --command -- curl --fail --retry 5 --retry-connrefused http://web
```

Ejecuta `expose` una sola vez; si el Service ya existe, inspecciónalo. La respuesta debe contener el HTML de bienvenida de Nginx. El Pod temporal `cliente-http` se elimina al terminar.

El nombre `web` se resuelve dentro del mismo namespace. Desde otro namespace puedes usar `web.curso-k8s.svc.cluster.local` si el clúster usa el dominio predeterminado `cluster.local`.

| Campo | Significado |
| ----- | ----------- |
| **port** | Puerto del Service (lo que usan los clientes dentro del clúster). |
| **targetPort** | Puerto del contenedor en el Pod. |
| **selector** | Labels que deben tener los Pods para recibir tráfico. |

## Acceso desde la terminal remota

Opcionalmente, ejecuta `kubectl port-forward -n curso-k8s service/web 8080:80` y, en otra terminal **del mismo playground**, `curl http://localhost:8080`. Detén el reenvío con Ctrl+C. Ese `localhost` no es el de tu computador; para abrirlo en el navegador necesitarías el mecanismo de acceso a puertos de la plataforma.

## Desafío

Obtén una respuesta HTTP usando el Service. Explica la diferencia entre `port`, `targetPort` y el puerto local de `port-forward`.

**Entrega:** respuesta HTML y evidencia del selector y los Pods seleccionados.

**Referencia:** [Services](https://kubernetes.io/docs/concepts/services-networking/service/).
