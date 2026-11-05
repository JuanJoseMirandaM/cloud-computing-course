---
sidebar_position: 2
---

# Arquitectura básica

## Clúster y nodos

Un **clúster** agrupa máquinas llamadas **nodos**. El **plano de control** administra el clúster y los nodos ejecutan las cargas de trabajo. En un laboratorio una misma máquina puede cumplir ambos papeles.

## Componentes principales

- **API server:** recibe las solicitudes de `kubectl` y expone la API.
- **etcd:** almacena el estado y la configuración del clúster.
- **Scheduler:** selecciona un nodo para los Pods que todavía no tienen uno asignado.
- **Controller manager:** ejecuta controladores que reconcilian los recursos.
- **kubelet:** agente de cada nodo que vela por los contenedores de sus Pods.
- **Runtime de contenedores:** ejecuta contenedores, por ejemplo containerd.
- **Red del clúster:** permite la comunicación entre Pods; la implementación depende del entorno.

`kubectl` es un cliente: se comunica con el API server, no ejecuta directamente los contenedores. Kubernetes puede ejecutar imágenes que construimos con Docker sin necesitar Docker Engine como runtime.

## Del comando al contenedor

1. Enviamos una definición al API server.
2. Se registra el estado deseado.
3. Los controladores crean los recursos necesarios.
4. El scheduler asigna los Pods a nodos.
5. El kubelet solicita al runtime ejecutar los contenedores.

## Desafío

Identifica quién recibe una solicitud, quién elige el nodo y quién ejecuta el contenedor. Explica por qué un Pod sin nodo asignado requiere investigar el scheduling.

**Entrega:** cinco pasos con tus palabras.

**Referencia:** [Componentes del clúster](https://kubernetes.io/docs/concepts/overview/components/).
