---
sidebar_position: 9
---

# Diagnóstico básico

## Observar antes de cambiar

Trabaja en un playground propio de Killercoda o KodeKloud. Ante un fallo, identifica el recurso, su estado y sus eventos:

```bash
kubectl get pods -n curso-k8s -o wide
kubectl describe pod NOMBRE_DEL_POD -n curso-k8s
kubectl logs NOMBRE_DEL_POD -n curso-k8s
kubectl get events -n curso-k8s --sort-by=.metadata.creationTimestamp
```

Sustituye `NOMBRE_DEL_POD` por el nombre real. Con varios contenedores usa `-c NOMBRE_CONTENEDOR`. Si el contenedor reinició, prueba `kubectl logs ... --previous`.

## Errores frecuentes de Pods

### 1. ImagePullBackOff / ErrImagePull

| | |
| --- | --- |
| **Significado** | Kubernetes no puede descargar la imagen del registro. |
| **Causas habituales** | Typo en nombre o tag, registro privado sin credenciales, fallo de red. |
| **Cómo depurar** | `kubectl describe pod <nombre>` → sección **Events** al final. |

### 2. CrashLoopBackOff

| | |
| --- | --- |
| **Significado** | El contenedor arranca, falla y Kubernetes reintenta con backoff exponencial. |
| **Causas habituales** | Variables mal configuradas, falta un archivo de arranque, dependencia de runtime. |
| **Cómo depurar** | `kubectl logs <pod>` y, si murió demasiado rápido, `kubectl logs <pod> --previous`. |

### 3. Pending

| | |
| --- | --- |
| **Significado** | El Pod no puede programarse en ningún nodo. |
| **Causas habituales** | Requests de CPU/memoria mayores que la capacidad libre; selectores/affinity que no coinciden. |
| **Cómo depurar** | `kubectl describe pod <nombre>` y lee el mensaje del scheduler. |

### 4. Running pero sin respuesta

Revisa `READY`, puertos del contenedor, **selector** del Service y **EndpointSlices**. Un Pod Running puede no estar Ready o no estar seleccionado por el Service.

## Fallo controlado: imagen inexistente

Prepara el namespace:

```bash
kubectl create namespace curso-k8s --dry-run=client -o yaml | kubectl apply -f -
```

```bash
kubectl create deployment diagnostico -n curso-k8s --image=nginx:etiqueta-inexistente-curso
kubectl get pods -n curso-k8s -l app=diagnostico
```

Espera el error de descarga. Copia el nombre del Pod y consulta `describe`; `logs` puede no estar disponible porque el contenedor nunca arrancó.

Corrige la imagen y comprueba la recuperación:

```bash
kubectl set image deployment/diagnostico -n curso-k8s nginx=nginx:stable-alpine
kubectl rollout status deployment/diagnostico -n curso-k8s --timeout=120s
kubectl get pods -n curso-k8s -l app=diagnostico
kubectl delete deployment diagnostico -n curso-k8s
```

Si la corrección tampoco descarga, revisa la conectividad del playground y sus eventos en lugar de repetir cambios sin evidencia.

## Errores comunes a evitar

1. **Pods sueltos en “producción”** — sin Deployment no hay recreación automática.
2. **Labels que no coinciden** — Deployment selector ≠ template labels, o Service selector ≠ labels de los Pods.
3. **Sin resource limits** — un contenedor sin tope puede consumir toda la memoria/CPU del nodo y desestabilizarlo.

## Desafío

Reproduce el fallo de imagen, identifica la causa y corrígelo.

**Entrega:** síntoma, evento relevante, acción realizada y estado final. No basta con una captura de Pods en verde.

**Referencia:** [Depurar Pods](https://kubernetes.io/docs/tasks/debug/debug-application/debug-pods/).
