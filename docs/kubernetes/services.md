---
sidebar_position: 7
---

# Services y acceso a la aplicación

## Una dirección estable

Los Pods se reemplazan y sus IP pueden cambiar. Un **Service** proporciona un punto de acceso estable y selecciona Pods mediante etiquetas. El selector debe coincidir con las etiquetas de los Pods.

- **ClusterIP:** acceso dentro del clúster; es el tipo predeterminado.
- **NodePort:** publica también un puerto en los nodos; el acceso externo depende de la red del laboratorio.
- **LoadBalancer:** solicita un balanceador externo si el entorno tiene una integración que lo provea. En un playground puede quedar pendiente.

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

El nombre `web` se resuelve dentro del mismo namespace. Desde otro namespace se puede usar `web.curso-k8s.svc.cluster.local` si el clúster utiliza el dominio predeterminado `cluster.local`.

## Acceso desde la terminal remota

Opcionalmente, ejecuta `kubectl port-forward -n curso-k8s service/web 8080:80` y, en otra terminal **del mismo playground**, `curl http://localhost:8080`. Detén el reenvío con Ctrl+C. Ese `localhost` no es el de tu computador; para abrirlo en el navegador necesitarías el mecanismo de acceso a puertos de la plataforma.

## Desafío

Obtén una respuesta HTTP usando el Service. Explica la diferencia entre `port`, `targetPort` y el puerto local de `port-forward`.

**Entrega:** respuesta HTML y evidencia del selector y los Pods seleccionados.

**Referencia:** [Services](https://kubernetes.io/docs/concepts/services-networking/service/).
