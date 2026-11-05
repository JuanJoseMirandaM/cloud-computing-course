---
sidebar_position: 9
---

# Diagnóstico básico

## Observar antes de cambiar

Trabaja en un playground propio de Killercoda o KodeKloud. Ante un fallo, empieza por identificar el recurso, su estado y sus eventos:

```bash
kubectl get pods -n curso-k8s -o wide
kubectl describe pod NOMBRE_DEL_POD -n curso-k8s
kubectl logs NOMBRE_DEL_POD -n curso-k8s
kubectl get events -n curso-k8s --sort-by=.metadata.creationTimestamp
```

Sustituye `NOMBRE_DEL_POD` por el nombre real. Si hay varios contenedores, selecciona uno con `-c NOMBRE_CONTENEDOR`. Para consultar una instancia anterior de un contenedor que reinició, usa `kubectl logs ... --previous`.

## Interpretar síntomas

- **Pending:** revisa los eventos; puede faltar capacidad o existir una restricción de scheduling. No todos los Pending tienen la misma causa.
- **ErrImagePull / ImagePullBackOff:** comprueba nombre, etiqueta, acceso al registro y conectividad.
- **CrashLoopBackOff:** revisa logs, comando de inicio y configuración. El proceso se está reiniciando repetidamente.
- **Running pero sin respuesta:** revisa `READY`, puertos, selector del Service y EndpointSlices.

## Fallo controlado: imagen inexistente

Prepara el namespace:

```bash
kubectl create namespace curso-k8s --dry-run=client -o yaml | kubectl apply -f -
```

```bash
kubectl create deployment diagnostico -n curso-k8s --image=nginx:etiqueta-inexistente-curso
kubectl get pods -n curso-k8s -l app=diagnostico
```

Espera a observar el error de descarga. Copia el nombre del Pod y consulta `describe`; `logs` puede no estar disponible porque el contenedor nunca arrancó.

Corrige la imagen y comprueba la recuperación:

```bash
kubectl set image deployment/diagnostico -n curso-k8s nginx=nginx:stable-alpine
kubectl rollout status deployment/diagnostico -n curso-k8s --timeout=120s
kubectl get pods -n curso-k8s -l app=diagnostico
kubectl delete deployment diagnostico -n curso-k8s
```

Si la corrección tampoco descarga, revisa la conectividad del playground y sus eventos en lugar de repetir cambios sin evidencia.

## Desafío

Reproduce el fallo, identifica la causa y corrígelo.

**Entrega:** síntoma, evento relevante, acción realizada y estado final. No basta con una captura de Pods en verde.

**Referencia:** [Depurar Pods](https://kubernetes.io/docs/tasks/debug/debug-application/debug-pods/).
