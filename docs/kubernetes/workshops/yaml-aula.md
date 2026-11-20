---
sidebar_position: 2
sidebar_label: "Workshop 2: YAML en el aula"
description: Sesión de aula para leer, validar y aplicar un manifiesto YAML de Deployment y Service juntos.
---

# Workshop 2: Entender YAML en el aula

Sesión **guiada por el docente** (~45–60 min) en **Killercoda** o **KodeKloud**. El objetivo no es “terminar solo”: es **entender** qué dice un manifiesto, por qué fallan los labels y qué hace `kubectl apply`.

Trabajáis en el mismo playground. El docente proyecta; los estudiantes ejecutan los mismos comandos.

:::tip Antes de empezar
Conviene haber visto [Manifiestos YAML](../manifiestos-yaml.md) y el [Workshop 1](./namespace-pods-replicas.md) (imperativo). Aquí pasáis a **declarativo**.
:::

---

## Recorrido (aula)

| Bloque | Qué entendemos |
| ------ | -------------- |
| 1. Preparación | Namespace limpio y por qué borramos `web` imperativo |
| 2. Anatomía del YAML | Cada bloque del archivo, en voz alta |
| 3. Validar sin crear | `dry-run=server` vs aplicar de verdad |
| 4. Labels rotos | Un error típico y cómo se lee el mensaje |
| 5. Aplicar y comprobar | Deployment + Service vivos |
| 6. Cambiar el archivo | `replicas: 3` y otra vez `apply` |

---

## 1. Preparación (5 min)

```bash
kubectl get nodes
kubectl create namespace curso-k8s --dry-run=client -o yaml | kubectl apply -f -
kubectl delete deployment web -n curso-k8s --ignore-not-found
kubectl delete service web -n curso-k8s --ignore-not-found
kubectl get all -n curso-k8s
```

**Pregunta al grupo:** ¿por qué borramos el Deployment del Workshop 1 antes de aplicar el YAML?

*(Respuesta esperada: evitar mezclar el objeto creado con `create` y el declarado en el archivo.)*

---

## 2. Anatomía del manifiesto (15 min)

Cread el archivo **juntos** (editor del playground o `cat > web.yaml <<'EOF'` … `EOF`):

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

### Lectura en voz alta (docente + estudiantes)

El docente señala cada zona; un estudiante responde qué hace:

| Zona | Pregunta en clase |
| ---- | ----------------- |
| `apiVersion` / `kind` | ¿Qué objeto pide este documento? |
| `metadata.name` + `namespace` | ¿Dónde vive y cómo se llama? |
| `replicas: 2` | ¿Cuántos Pods queremos? ¿Quién los mantiene? |
| `selector.matchLabels` | ¿Cómo sabe el Deployment cuáles Pods son “suyos”? |
| `template.metadata.labels` | ¿Por qué **deben** coincidir con el selector? |
| `image` + `containerPort` | ¿Qué corre dentro del Pod? |
| `requests` / `limits` | ¿Garantía vs tope de CPU/memoria? |
| `readinessProbe` | ¿Cuándo puede el Service enviarle tráfico? |
| `---` | ¿Qué separa? |
| Service `selector` / `port` / `targetPort` | ¿A qué Pods apunta? ¿Puerto del Service vs del contenedor? |

:::caution Indentación
YAML usa **espacios**, no tabuladores. Un espacio de más o de menos cambia el significado.
:::

---

## 3. Validar sin crear (5 min)

```bash
kubectl apply --dry-run=server -f web.yaml
```

**Pregunta:** ¿este comando dejó Pods en el clúster? Comprobad:

```bash
kubectl get pods -n curso-k8s
```

*(Debe seguir vacío o sin el Deployment nuevo. El dry-run valida contra la API; no demuestra que la imagen exista.)*

---

## 4. Demostración: labels rotos (10 min)

Copiad el YAML a `web-roto.yaml` y **cambia solo** la label de la plantilla:

```yaml
# dentro de template.metadata.labels — dejar el selector en app: web
      labels:
        app: web-mal
```

Intentad aplicar:

```bash
kubectl apply --dry-run=server -f web-roto.yaml
```

Leed el error en voz alta. **Pregunta:** ¿qué dos sitios tenían que coincidir?

Volved al `web.yaml` correcto (labels `app: web` en selector y template).

---

## 5. Aplicar y comprobar (10 min)

```bash
kubectl apply -f web.yaml
kubectl rollout status deployment/web -n curso-k8s --timeout=120s
kubectl get deployments,pods,services -n curso-k8s
kubectl get endpointslices -n curso-k8s -l kubernetes.io/service-name=web
```

Cliente HTTP (mismo namespace):

```bash
kubectl run cliente-http -n curso-k8s --image=curlimages/curl:latest --restart=Never --rm -i --command -- \
  curl --fail --retry 5 --retry-connrefused http://web
```

**Cierre del bloque:** un estudiante resume en una frase: *archivo → apply → ReplicaSet → Pods → Service*.

---

## 6. El archivo es la fuente de verdad (10 min)

En `web.yaml` cambiad `replicas: 2` → `replicas: 3` y:

```bash
kubectl apply -f web.yaml
kubectl get pods -n curso-k8s -l app=web
```

**Pregunta:** si alguien hace `kubectl scale ... --replicas=5` y luego otro aplica el YAML con `3`, ¿qué gana?  
*(El apply vuelve a imponer lo del archivo.)*

Opcional en aula (si hay tiempo):

```bash
kubectl scale deployment/web -n curso-k8s --replicas=5
kubectl get pods -n curso-k8s -l app=web
kubectl apply -f web.yaml
kubectl get pods -n curso-k8s -l app=web
```

---

## Cierre de clase

No hace falta borrar el namespace si la práctica del taller sigue en la misma sesión. Si la clase termina aquí:

```bash
kubectl delete namespace curso-k8s --wait=true
```

**Para el docente:** dejá apuntado que la práctica individual es el [Workshop 3](./yaml-practica.md).

---

## Checklist del aula (sin entrega formal)

- [ ] Se leyó el YAML por bloques  
- [ ] Se vio el dry-run sin crear recursos  
- [ ] Se reprodujo (y explicó) el error de labels  
- [ ] `apply` dejó Deployment + Service funcionando  
- [ ] Un cambio en el archivo se reaplicó con `apply`  
