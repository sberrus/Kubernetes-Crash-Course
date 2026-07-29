# Workloads
Los workloads de Kubernetes son los recursos que definen y gestionan la ejecución de aplicaciones (Pods) dentro de un clúster, indicando cómo deben crearse, escalarse, actualizarse y mantenerse disponibles.

Los principales tipos de workloads son:

- `Pod`: la unidad básica que ejecuta uno o más contenedores.
- `Deployment`: gestiona aplicaciones sin estado (stateless), actualizaciones y escalado.
- `StatefulSet`: para aplicaciones con estado, como bases de datos.
- `DaemonSet`: asegura que un Pod se ejecute en todos (o algunos) los nodos.
- `Job`: ejecuta una tarea hasta completarla.
- `CronJob`: ejecuta tareas programadas de forma periódica.

En resumen, un workload describe qué aplicación debe ejecutarse y cómo Kubernetes debe administrarla.