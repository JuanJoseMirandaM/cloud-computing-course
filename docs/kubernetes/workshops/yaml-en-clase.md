---
sidebar_position: 2
sidebar_label: "Workshop 2: YAML en clase"
description: "Laboratorio de una hora, en clase, para leer las partes de un manifiesto YAML y aplicar un namespace, un Pod y un Deployment."
---

# Workshop 2: Manifiestos YAML en clase

Laboratorio de **una hora** para hacer **con el grupo** en Killercoda o KodeKloud. Cada bloque explica una parte del YAML, se escribe en un archivo y se comprueba en el clúster antes de seguir.

Cada persona abre una sesión nueva. El docente lee el bloque, el grupo escribe el archivo y se espera a ver la misma salida. Si en esa sesión ya existe un Deployment `web`, elimínalo antes de aplicar los archivos de hoy:

```bash
kubectl delete deployment web -n curso-k8s --ignore-not-found
```

---

## La hora

| Minutos | Paso | Qué aprendemos |
| ------- | ---- | -------------- |
| 0–5 | Entorno | El clúster responde |
| 5–15 | Leer un manifiesto | Indentación, mapas y las cuatro partes |
| 15–25 | Namespace | El archivo más corto y `kubectl apply -f` |
| 25–40 | Pod | `spec` y las listas de contenedores |
| 40–52 | Deployment | `replicas`, `selector` y `template` |
| 52–58 | Editar el archivo | El archivo manda y un Pod borrado vuelve |
| 58–60 | Cierre | Guardar los YAML y borrar el namespace |

---

## 1. Entorno

```bash
kubectl version --client
kubectl config current-context
kubectl get nodes
```

**Pausa.** Sigan cuando los nodos estén `Ready`.

---

## 2. Cómo se lee un YAML

Un manifiesto es texto. Una línea `clave: valor` guarda un dato. Lo que queda más a la derecha pertenece a la clave de arriba. En este curso usamos **dos espacios** por nivel.

Estas son las cuatro partes que vamos a ver en todos los archivos:

| Parte | Qué dice |
| ----- | -------- |
| `apiVersion` | Qué grupo de la API de Kubernetes describe el recurso |
| `kind` | Qué objeto es: Namespace, Pod, Deployment |
| `metadata` | Cómo se identifica: nombre, namespace y etiquetas |
| `spec` | El estado que queremos. Algunos recursos, como este namespace, no lo llevan |

`v1` es la API del núcleo: ahí viven Namespace y Pod. `apps/v1` es otra API: ahí vive el Deployment. El `kind` tiene que existir en esa `apiVersion`.

---

## 3. Namespace

El namespace agrupa recursos del mismo clúster. Su archivo solo necesita las tres primeras partes. `name` está dentro de `metadata` porque va indentado.

Crea `namespace.yaml`:

```yaml title="namespace.yaml"
apiVersion: v1
kind: Namespace
metadata:
  name: curso-k8s
```

Antes de guardarlo en el clúster, Kubernetes puede revisar el archivo y no crear nada:

```bash
kubectl apply --dry-run=server -f namespace.yaml
kubectl apply -f namespace.yaml
kubectl get namespace curso-k8s
```

`curso-k8s` queda `Active`. Aplicar otra vez el mismo archivo no crea un segundo namespace: Kubernetes acerca el clúster a lo que el archivo declara.

**Pausa.** Pregunta de cierre: ¿qué valor tiene `kind` y en qué parte está el nombre?

---

## 4. Pod

Un Pod lleva `spec`. Dentro, `containers` es una **lista**: cada elemento empieza con `-`. Las claves de ese elemento (`name`, `image`, `ports`) se alinean entre sí, un espacio a la derecha del guion.

`metadata.namespace` coloca el Pod dentro de `curso-k8s`. `containerPort` documenta el puerto del contenedor. No lo abre en tu computador.

Crea `pod.yaml`:

```yaml title="pod.yaml"
apiVersion: v1
kind: Pod
metadata:
  name: primer-pod
  namespace: curso-k8s
spec:
  containers:
    - name: nginx
      image: nginx:stable-alpine
      ports:
        - containerPort: 80
```

`ports` es otra lista. Hoy tiene un solo elemento, `containerPort: 80`.

```bash
kubectl apply -f pod.yaml
kubectl wait -n curso-k8s --for=condition=Ready pod/primer-pod --timeout=120s
kubectl get pods -n curso-k8s -o wide
```

**Pausa.** `primer-pod` está `1/1` y `Running`. Señalen en el archivo dónde está la imagen.

Este Pod no tiene un controlador. Al borrar el recurso del archivo, no vuelve:

```bash
kubectl delete -f pod.yaml
kubectl get pods -n curso-k8s
```

**Pausa.** La lista queda vacía. Esta salida se compara con la del Deployment.

---

## 5. Deployment

El Deployment mantiene copias de un Pod. Tres zonas de `spec` hacen ese trabajo:

| Zona | Para qué sirve |
| ---- | -------------- |
| `replicas` | Cuántos Pods quieres |
| `selector.matchLabels` | Con qué etiquetas el Deployment reconoce a sus Pods |
| `template` | El modelo de Pod que se va a copiar. Su `spec` es la misma idea que `pod.yaml` |

`selector.matchLabels` y `template.metadata.labels` llevan la misma etiqueta, `app: web`. Esa pareja es la que une el Deployment con los Pods que crea.

Crea `web.yaml`:

```yaml title="web.yaml"
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  namespace: curso-k8s
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: nginx
          image: nginx:stable-alpine
          ports:
            - containerPort: 80
```

`apiVersion` cambió a `apps/v1` porque el `kind` es Deployment. Dentro de `template.spec` reaparece la lista `containers` del Pod.

```bash
kubectl apply -f web.yaml
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
kubectl get deployments,replicasets,pods -n curso-k8s
```

**Pausa.** `web` está `2/2`. En la salida hay un ReplicaSet: es el objeto que el Deployment usa para sostener esas dos copias. Pregunta de cierre: ¿qué etiqueta comparten `selector` y `template`?

---

## 6. El archivo manda

Abran `web.yaml` y cambien solo esta línea:

```yaml
replicas: 3
```

El resto del archivo se queda igual. Vuelvan a aplicar el mismo archivo:

```bash
kubectl apply -f web.yaml
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
kubectl get pods -n curso-k8s -l app=web
```

`-l app=web` usa la etiqueta que escribieron en `template.metadata.labels`. Kubernetes muestra los Pods que la tienen.

**Pausa.** Hay tres Pods preparados.

Copia el nombre de uno y úsalo en lugar de `NOMBRE_DEL_POD`:

```bash
kubectl delete pod NOMBRE_DEL_POD -n curso-k8s
kubectl get pods -n curso-k8s -l app=web -w
```

Aparece un Pod nuevo hasta volver a tres. Sal de la observación con **Ctrl+C**. El Pod de `pod.yaml` no volvió. Este sí, porque `replicas: 3` sigue escrito en `web.yaml`.

---

## 7. Cierre

Guarden `namespace.yaml`, `pod.yaml` y `web.yaml` fuera del playground. Esos tres archivos son la práctica: cada uno muestra una parte distinta del manifiesto.

```bash
kubectl delete namespace curso-k8s --wait=true
kubectl get namespace curso-k8s
```

La última consulta responde `NotFound`.

**Siguiente:** práctica individual — [Workshop 3: Práctica YAML](./yaml-practica.md).
