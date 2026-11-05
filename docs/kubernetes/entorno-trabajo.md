---
sidebar_position: 3
---

# Preparar el entorno de trabajo

## Herramientas necesarias

Necesitamos un clúster y el cliente `kubectl`. En las prácticas usaremos la terminal del navegador de Killercoda o KodeKloud, donde el entorno seleccionado debe proporcionar ambos. No es necesario instalar Kubernetes en tu computador.

## Verificar la conexión

En la terminal del playground ejecuta:

```bash
kubectl version --client
kubectl config current-context
kubectl cluster-info
kubectl get nodes -o wide
```

El **contexto** indica a qué clúster, usuario y namespace apunta el cliente. Compruébalo antes de crear o eliminar recursos. Espera a que los nodos que usarás estén `Ready`; si no, consulta el estado de preparación del playground.

Si aparece un error de conexión, comprueba que seleccionaste un entorno Kubernetes y que la sesión no expiró. No reemplaces la configuración del laboratorio con credenciales de otro clúster.

## Alternativa local opcional

Para practicar fuera de las sesiones puedes instalar `kubectl` y crear un clúster con **Minikube** o **Kind**, siguiendo sus requisitos oficiales. No necesitamos instalar ambos y esta opción no es requisito de las entregas.

- [Instalar kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [Kind](https://kind.sigs.k8s.io/docs/user/quick-start/)

## Desafío

Comprueba la versión del cliente, el contexto activo y el estado de los nodos.

**Entrega:** captura de los comandos y una frase indicando qué clúster estás usando.
