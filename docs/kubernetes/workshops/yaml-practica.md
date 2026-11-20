---
sidebar_position: 3
sidebar_label: "Workshop 3: Práctica YAML"
description: "Práctica individual con manifiestos YAML: escribir, aplicar, corregir labels, escalar y comprobar HTTP."
---

# Workshop 3: Práctica de manifiestos YAML

Práctica **individual** (~60–75 min) en **Killercoda** o **KodeKloud**. Aquí trabajas **solo**: escribes el YAML, aplicas, rompes, arreglas y entregas capturas.

Si hiciste el [Workshop 2 en clase](./yaml-en-clase.md), ya viste la anatomía del archivo. Si no, lee antes [Manifiestos YAML](../manifiestos-yaml.md).

Abre una **sesión nueva**. Trabaja solo dentro de `curso-k8s`.

---

## Recorrido

| Paso | Qué compruebas al terminar |
| ---- | -------------------------- |
| Entorno | Nodos `Ready` |
| Namespace | `curso-k8s` activo y limpio |
| Escribir YAML | Archivo `tienda.yaml` con Deployment + Service |
| Aplicar | 2 Pods Ready y Service ClusterIP |
| HTTP | Respuesta HTML vía `http://tienda` |
| Escalar en archivo | 3 réplicas tras `apply` (no solo con `scale`) |
| Labels rotos | Error al validar; luego corrección |
| Cierre | Namespace eliminado |

---

## 1. Entorno

```bash
kubectl version --client
kubectl config current-context
kubectl get nodes
```

Captura: nodos `Ready`.

---

## 2. Namespace limpio

```bash
kubectl create namespace curso-k8s
kubectl get namespace curso-k8s
```

Si ya existía de otra práctica:

```bash
kubectl delete namespace curso-k8s --wait=true
kubectl create namespace curso-k8s
```

Captura: namespace `Active`.

---

## 3. Escribe tu manifiesto

Crea `tienda.yaml` (nombre distinto a `web` a propósito). Debe declarar:

- Un **Deployment** `tienda` en `curso-k8s`
- Imagen `nginx:stable-alpine`
- **2** réplicas
- Labels `app: tienda` (selector **y** template iguales)
- Contenedor con `containerPort: 80`
- `resources.requests` y `resources.limits` (puedes copiar valores razonables de la lección)
- Una `readinessProbe` HTTP en `/` puerto 80
- Un **Service** `tienda`, tipo `ClusterIP`, selector `app: tienda`, `port` 80 y `targetPort` 80
- Los dos documentos separados con `---`

Puedes basarte en el ejemplo de [Manifiestos YAML](../manifiestos-yaml.md), pero cambia nombres y labels a `tienda`.

Valida sin crear:

```bash
kubectl apply --dry-run=server -f tienda.yaml
```

Captura: el archivo en el editor **o** `cat tienda.yaml`, más la salida del dry-run.

---

## 4. Aplicar y comprobar

```bash
kubectl apply -f tienda.yaml
kubectl rollout status deployment/tienda -n curso-k8s --timeout=120s
kubectl get deployments,pods,services -n curso-k8s
kubectl get endpointslices -n curso-k8s -l kubernetes.io/service-name=tienda
```

Captura: Deployment `2/2`, dos Pods `Running`, Service `tienda`.

---

## 5. Comprobar HTTP

```bash
kubectl run cliente-http -n curso-k8s --image=curlimages/curl:latest --restart=Never --rm -i --command -- \
  curl --fail --retry 5 --retry-connrefused http://tienda
```

Captura: HTML de bienvenida de Nginx (o al menos la evidencia del `curl` exitoso).

---

## 6. Escalar editando el archivo

1. En `tienda.yaml` cambia `replicas` de `2` a `3`.
2. Aplica de nuevo:

```bash
kubectl apply -f tienda.yaml
kubectl get pods -n curso-k8s -l app=tienda
```

**No uses** `kubectl scale` para esta captura: la idea es que el archivo mande.

Captura: tres Pods y el trozo del YAML con `replicas: 3`.

---

## 7. Romper y arreglar labels

1. Copia el manifiesto a `tienda-roto.yaml`.
2. En la **plantilla** del Deployment deja el selector en `app: tienda` pero cambia las labels del template a `app: tienda-mal`.
3. Intenta:

```bash
kubectl apply --dry-run=server -f tienda-roto.yaml
```

Captura: el **mensaje de error**.

4. Corrige `tienda.yaml` (si lo tocaste) o sigue con el archivo bueno y confirma:

```bash
kubectl apply -f tienda.yaml
kubectl get pods -n curso-k8s -l app=tienda
```

Captura: Pods otra vez correctos (`app=tienda`).

---

## 8. Self-healing (rápido)

Con 3 réplicas, borra un Pod del Deployment y observa el reemplazo:

```bash
kubectl get pods -n curso-k8s -l app=tienda
kubectl delete pod NOMBRE_DEL_POD -n curso-k8s
kubectl get pods -n curso-k8s -l app=tienda -w
```

Sal con **Ctrl+C** cuando vuelvan a ser tres. Captura: el Pod nuevo.

---

## 9. Cierre

```bash
kubectl delete namespace curso-k8s --wait=true
kubectl get namespace curso-k8s
```

Captura: `NotFound`.

Guarda `tienda.yaml` **fuera** del playground (copia/pega a tu computador): la sesión se borra.

---

## Entrega

Sube un **PDF** (`Apellido_Nombre_WorkshopYAML.pdf`) con:

1. Capturas de cada paso del recorrido (1–9).  
2. El contenido final de `tienda.yaml` (texto o captura legible).  
3. Dos frases:
   - Por qué el selector del Deployment y las labels del template deben coincidir.
   - Qué diferencia hay entre `kubectl scale` y cambiar `replicas` en el YAML + `apply`.

---

## Si algo falla

| Síntoma | Qué revisar |
| ------- | ----------- |
| Error de validación al apply | Indentación, `---` entre documentos, labels |
| Pods Pending | Capacidad del playground; espera o revisa `describe` |
| Service sin endpoints | Selector del Service ≠ labels de los Pods |
| `curl` no resuelve `tienda` | Mismo namespace; nombre exacto del Service |
| Imagen no baja | Tag correcto `nginx:stable-alpine`; red del playground |
