---
sidebar_position: 4
---

# Prácticas con Killercoda y KodeKloud

## Plataformas del curso

Usaremos **[Killercoda](https://killercoda.com/)** y **[KodeKloud](https://kodekloud.com/playgrounds)** para todas las pruebas del módulo. Sus entornos permiten practicar desde el navegador. El acceso, la duración y los laboratorios disponibles dependen de la plataforma y, en KodeKloud, de los permisos o plan de tu cuenta.

## Iniciar una sesión

1. Accede a la plataforma indicada por el docente e inicia sesión si lo solicita.
2. En Killercoda selecciona un **playground de Kubernetes**. En KodeKloud abre **Playgrounds** y selecciona un entorno Kubernetes disponible para tu cuenta; un laboratorio guiado puede tener restricciones adicionales.
3. Inicia el entorno y espera a que termine su preparación.
4. Usa la terminal que tenga configurado `kubectl`; verifica `kubectl cluster-info` y `kubectl get nodes`.
5. Si el acceso a KodeKloud no está habilitado, realiza la práctica equivalente en Killercoda e indica la plataforma utilizada.

## Espacio de trabajo de las prácticas

Cada lección práctica comienza creando un namespace propio. Los comandos usan `-n curso-k8s` para evitar depender del namespace activo:

```bash
kubectl create namespace curso-k8s --dry-run=client -o yaml | kubectl apply -f -
kubectl get pods -n curso-k8s
```

Un namespace agrupa recursos dentro del clúster; no es otro clúster. Todas las actividades pueden repetirse en una sesión nueva siguiendo sus pasos de preparación.

## Acceso y conservación del trabajo

`localhost` en la terminal corresponde a la máquina remota del laboratorio. Para comprobar HTTP usaremos `curl` desde un Pod; no hace falta abrir puertos al navegador.

Los entornos son temporales: guarda tus manifiestos y evidencias fuera del playground antes de que termine la sesión. No supongas que tus archivos o recursos seguirán presentes al volver a entrar.

Para terminar, elimina únicamente el namespace del curso en tu propio laboratorio:

```bash
kubectl delete namespace curso-k8s
```

## Desafío

Inicia una sesión, crea el namespace y muestra sus Pods (al principio la lista estará vacía). Guarda una captura con el nombre de la plataforma y la fecha de la práctica.

**Entrega:** evidencia del entorno preparado y explicación de dónde guardarás tus archivos.

**Referencias:** [Killercoda: entornos interactivos](https://killercoda.com/about), [KodeKloud: playgrounds](https://kodekloud.com/playgrounds).
