---
sidebar_position: 8
---

# Manifiestos YAML

## Declarar en archivos

Los comandos anteriores son una forma imperativa de trabajar. Un manifiesto expresa el estado deseado en un archivo que podemos guardar y revisar en Git.

- `apiVersion`: versión de la API del recurso.
- `kind`: tipo de recurso.
- `metadata`: nombre, namespace y etiquetas.
- `spec`: configuración deseada.

Usa espacios, conserva la indentación y distingue listas de objetos. `---` separa documentos dentro de un mismo archivo.

## Práctica declarativa

En Killercoda o KodeKloud prepara el namespace:

```bash
kubectl create namespace curso-k8s --dry-run=client -o yaml | kubectl apply -f -
```

Si vienes de los ejercicios imperativos, elimina sus recursos `web` antes de cambiar a este ejemplo. Esto evita mezclar configuraciones de ejercicios distintos:

```bash
kubectl delete deployment web -n curso-k8s --ignore-not-found
kubectl delete service web -n curso-k8s --ignore-not-found
```

Crea `web.yaml` con el editor del playground o con `cat > web.yaml <<'EOF'`, pega el contenido siguiente y termina con una línea `EOF`:

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

El selector del Deployment coincide con las etiquetas de su plantilla. El Service usa esas mismas etiquetas para encontrar los Pods. `requests` solicita capacidad para scheduling; `limits` acota el uso de recursos. La prueba de preparación, **readinessProbe**, comprueba HTTP antes de que el Pod reciba tráfico del Service.

```bash
kubectl apply --dry-run=server -f web.yaml
kubectl apply -f web.yaml
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
kubectl get deployments,pods,services -n curso-k8s
```

El dry run valida con el servidor sin guardar los recursos. No demuestra que la imagen pueda descargarse o que la aplicación funcione: comprueba también el despliegue y HTTP con el comando de la lección Services.

## Desafío

Cambia `replicas` a `3` en el archivo y vuelve a ejecutar `apply`. Mantén el archivo como fuente de verdad: un cambio manual con `scale` puede ser reemplazado por un `apply` posterior.

**Entrega:** `web.yaml` y evidencia de tres réplicas disponibles. Guarda el archivo fuera del entorno temporal.

**Referencia:** [Administración declarativa](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/declarative-config/).
