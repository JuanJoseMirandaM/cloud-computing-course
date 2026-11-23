---
sidebar_position: 8
---

# Manifiestos YAML

## Declarar en archivos

Los comandos `create` / `expose` / `scale` son una forma **imperativa**. En producción (y en Git) preferimos la forma **declarativa**: un manifiesto YAML describe el estado deseado y `kubectl apply` lo aplica.

Campos comunes:

| Campo | Significado |
| ----- | ----------- |
| `apiVersion` | Versión de la API del recurso (`apps/v1`, `v1`, …). |
| `kind` | Tipo de objeto (`Deployment`, `Service`, `Pod`, …). |
| `metadata` | Nombre, namespace, labels. |
| `spec` | Configuración deseada. |

Usa **espacios** (no tabuladores), conserva la indentación y distingue listas (`-`) de mapas. `---` separa varios documentos en el mismo archivo.

## Práctica declarativa

En Killercoda o KodeKloud prepara el namespace:

```bash
kubectl create namespace curso-k8s --dry-run=client -o yaml | kubectl apply -f -
```

Si vienes de los ejercicios imperativos, elimina sus recursos `web` antes de cambiar a este ejemplo (evita mezclar configuraciones):

```bash
kubectl delete deployment web -n curso-k8s --ignore-not-found
kubectl delete service web -n curso-k8s --ignore-not-found
```

Crea `web.yaml` con el editor del playground o con `cat > web.yaml <<'EOF'`, pega el contenido y termina con una línea `EOF`:

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
          resources:
            requests:
              cpu: 50m
              memory: 32Mi
            limits:
              cpu: 200m
              memory: 128Mi
          readinessProbe:
            httpGet:
              path: /
              port: 80
            initialDelaySeconds: 2
            periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: web
  namespace: curso-k8s
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 80
  type: ClusterIP
```

### Lectura del manifiesto

| Fragmento | Qué hace |
| --------- | -------- |
| `apiVersion` / `kind` | Qué objeto crear. |
| `spec.replicas` | Cuántas copias del Pod quieres. |
| `spec.selector.matchLabels` | Qué Pods “pertenecen” al Deployment. |
| `spec.template` | Plantilla (blueprint) de cada Pod. |
| `template.metadata.labels` | **Deben coincidir** con el selector del Deployment. |
| `resources.requests` / `limits` | Capacidad garantizada vs tope (evita que un contenedor agote el nodo). |
| `readinessProbe` | HTTP de preparación antes de recibir tráfico del Service. |
| Service `selector: app: web` | Mismas labels para encontrar los Pods. |

:::caution Labels desalineados
Si `selector.matchLabels` del Deployment **no** coincide con `template.metadata.labels`, el objeto falla la validación. Si el selector del Service no coincide con las labels de los Pods, el Service no tiene endpoints.
:::

```bash
kubectl apply --dry-run=server -f web.yaml
kubectl apply -f web.yaml
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
kubectl get deployments,pods,services -n curso-k8s
```

El dry run valida con el servidor sin guardar. No demuestra que la imagen se descargue: comprueba también el despliegue y HTTP (lección Services).

## Desafío

Cambia `replicas` a `3` en el archivo y vuelve a ejecutar `apply`. Mantén el archivo como fuente de verdad: un `scale` manual puede ser reemplazado por un `apply` posterior.

**Entrega:** `web.yaml` y evidencia de tres réplicas disponibles. Guarda el archivo fuera del entorno temporal.

**Workshops:** en clase, [Workshop 2 — YAML en clase](./workshops/yaml-en-clase.md); para practicar solo, [Workshop 3 — Práctica de manifiestos YAML](./workshops/yaml-practica.md).

**Referencia:** [Administración declarativa](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/declarative-config/).
