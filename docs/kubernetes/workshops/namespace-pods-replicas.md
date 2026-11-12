---
sidebar_position: 1
description: Práctica de una hora para crear un namespace, un Pod y réplicas con kubectl, antes de los manifiestos YAML.
---

# Workshop 1: Namespace, Pods y réplicas

Práctica de **60 minutos** en un playground de **Killercoda** o **KodeKloud**. Repasa lo anterior a los manifiestos YAML: crear un namespace, un Pod y réplicas de un Deployment. Todo se hace con `kubectl`. No escribas archivos YAML en esta sesión.

Abre una sesión nueva. Si el playground ya tiene recursos de otra práctica, indícalo en la entrega y trabaja solo dentro de `curso-k8s`.

---

## Recorrido

| Paso | Qué compruebas al terminar |
| ---- | -------------------------- |
| Entorno | Nodos `Ready` y contexto identificado |
| Namespace | `curso-k8s` en estado `Active` |
| Pods | Un Pod creado, inspeccionado y eliminado sin que vuelva |
| Réplicas | Deployment en 2 réplicas, luego en 3, y un Pod de reemplazo |
| Reto opcional | Una segunda aplicación falla al cambiar la imagen y vuelve a quedar lista |
| Cierre | Namespace eliminado |

---

## 1. Entorno

En la terminal del playground:

```bash
kubectl version --client
kubectl config current-context
kubectl cluster-info
kubectl get nodes
```

Sigue solo cuando los nodos que usarás estén `Ready`. Esta salida es la primera captura del PDF.

---

## 2. Namespace

Un namespace agrupa recursos del mismo clúster. No es otro clúster.

```bash
kubectl create namespace curso-k8s
kubectl get namespace curso-k8s
kubectl get pods -n curso-k8s
```

`curso-k8s` debe aparecer `Active`. La lista de Pods está vacía: todavía no hay cargas en ese namespace.

A partir de aquí cada comando lleva `-n curso-k8s`. Así no dependes del namespace activo de la terminal.

Si `create` responde que el namespace ya existe, no lo reutilices a ciegas. Revisa qué hay dentro y, si es de una práctica anterior tuya, espera a borrarlo antes de crearlo de nuevo:

```bash
kubectl delete namespace curso-k8s --wait=true
kubectl create namespace curso-k8s
```

---

## 3. Pods

Crea un Pod suelto. Este Pod no tiene un controlador: si lo borras, no vuelve.

```bash
kubectl run primer-pod -n curso-k8s --image=nginx:stable-alpine --port=80
kubectl wait -n curso-k8s --for=condition=Ready pod/primer-pod --timeout=120s
kubectl get pods -n curso-k8s -o wide
```

`--port=80` documenta el puerto del contenedor. No publica ese puerto en tu computador. En la salida, `Running` es la fase del Pod y `1/1` en `READY` indica que su contenedor está preparado. Anota el nodo de la columna `NODE`.

Inspecciona el Pod:

```bash
kubectl describe pod primer-pod -n curso-k8s
kubectl logs primer-pod -n curso-k8s
kubectl exec primer-pod -n curso-k8s -- nginx -v
```

En `describe`, localiza la imagen, el nodo y los eventos. `nginx -v` confirma que el proceso responde dentro del contenedor.

Elimínalo y comprueba que no reaparece:

```bash
kubectl delete pod primer-pod -n curso-k8s
kubectl get pods -n curso-k8s
```

La lista vuelve a estar vacía. Guarda esa captura: es la diferencia con el bloque de réplicas.

---

## 4. Réplicas

Un Deployment mantiene el número de Pods que declaras. Esos Pods sí se reemplazan.

```bash
kubectl create deployment web -n curso-k8s --image=nginx:stable-alpine --replicas=2
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
kubectl get deployments,replicasets,pods -n curso-k8s
```

Debes ver el Deployment `web` en `2/2`, un ReplicaSet y dos Pods `Running`. `kubectl create deployment` etiqueta esos Pods con `app=web`.

Escala a tres:

```bash
kubectl scale deployment/web -n curso-k8s --replicas=3
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
kubectl get pods -n curso-k8s -l app=web
```

Copia el nombre de uno de esos Pods y úsalo en lugar de `NOMBRE_DEL_POD`:

```bash
kubectl delete pod NOMBRE_DEL_POD -n curso-k8s
kubectl get pods -n curso-k8s -l app=web -w
```

Aparece un Pod nuevo, con otro nombre, hasta volver a tres preparados. Sal de la observación con **Ctrl+C**.

El Pod del bloque anterior no volvió porque lo creaste con `kubectl run`. Estos vuelven porque el Deployment pide tres réplicas.

---

## Reto opcional

No forma parte de la práctica obligatoria. Es para quien quiera intentar algo más difícil antes de borrar el namespace. Si lo logras, añade las capturas al final del mismo PDF.

Ya tienes `web` con tres réplicas. Crea otra aplicación en el mismo namespace y recupérala después de dejarla con una imagen que no existe. La salida fácil es borrar ese Deployment y crearlo otra vez: el reto es corregir la imagen y dejar `web` como está.

```bash
kubectl create deployment api -n curso-k8s --image=httpd:2.4-alpine --replicas=2
kubectl rollout status deployment/api -n curso-k8s --timeout=120s
kubectl get pods -n curso-k8s -l app=web
kubectl get pods -n curso-k8s -l app=api
```

`web` sigue en tres Pods y `api` en dos. Cada lista sale de su etiqueta.

El contenedor no se llama `api`. `kubectl create deployment` le pone el nombre de la imagen. Compruébalo en `Containers:`:

```bash
kubectl describe deployment api -n curso-k8s
```

Cambia solo ese contenedor a una etiqueta que no existe:

```bash
kubectl set image deployment/api -n curso-k8s httpd=httpd:etiqueta-inventada
kubectl get pods -n curso-k8s -l app=api
kubectl describe pod -n curso-k8s -l app=api
```

Los Pods de `api` quedan sin poder descargar la imagen. `web` sigue `Running`. En los eventos del `describe` se ve el fallo de descarga.

Recupera `api` con la imagen anterior, sin borrar el Deployment:

```bash
kubectl set image deployment/api -n curso-k8s httpd=httpd:2.4-alpine
kubectl rollout status deployment/api -n curso-k8s --timeout=120s
kubectl rollout history deployment/api -n curso-k8s
kubectl get deployments -n curso-k8s
```

`api` vuelve a `2/2`, el historial muestra más de una revisión y `web` sigue disponible.

---

## 5. Cierre

Si vas a intentar el reto, termínalo antes de estos comandos. Elimina solo el namespace de esta práctica:

```bash
kubectl delete namespace curso-k8s --wait=true
kubectl get namespace curso-k8s
```

Cuando termine el borrado, la última consulta responde `NotFound`.

---

## Entrega

Sube a la plataforma un **PDF** con una captura de cada paso: entorno, namespace, Pod creado, Pod eliminado, réplicas en 2 y en 3, Pod de reemplazo y namespace eliminado.

Si haces el reto opcional, añade al final de ese PDF la captura de `api` en fallo y la de `api` recuperado, con `web` todavía disponible.
