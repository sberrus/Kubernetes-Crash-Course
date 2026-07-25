# Workloads
Un workload son aplicaciones corriendo en kubernetes. Todos los contenedores que se despliegan deben formar parte de un workload. Es la abstracción lógica que se encarga de gestionar los recursos que necesitan los pods para dar servicio.

- Pods: Todos los pods deben pertener a un workload. Es el workload lo que los crea.
- ReplicaSet y Deployment: Estos se encargan de definir las capacidades y los recursos para los pods como las replicas, los recursos de cada pod, etc...
- StatefulSet y DaemonSet: son tipos especiales de workloads que tienen características adicionales para la gestión de los pods. EJ: Manejo especial del disco o de la red.
- Job and Cronjobs: Son workloads que se encargan de realizar tareas puntuales y de corta duración.


En palabras simples, dependiendo del tipo de despliegue que se desee hacer, se configura un tipo de workload.