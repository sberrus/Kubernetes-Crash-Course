# Updates
Cuando tenemos un componente desplegado como un `deployment` sabemos que estos tienen entre muchas otras cosas el cómo se realizan los cambios y las actualizaciones de los pods que gestiona. 

Dentro de esto tenemos el campo `strategy` que nos ayuda a realizar estos cambios de forma controlada de la manera que nosotros necesitemos según sea el caso.

En este apartado, vamos a tratar el cómo se gestiona en un clúster los cambios de versiones de los componentes para ver que estrategias hay y cómo es la forma correcta de hacer que el cluster funcione correctamente con el menor downtime posible si es que llegase a ser inexistente.

# Strategy
Este campo nos ayuda a determinar, cómo se va a comportar el ciclo de vida de los pods que estan dando servicio.

Puedes elegir entre que el clúster vaya matando pods y creando nuevos reemplazando los antiguos con la versión nueva de forma ordenada; o podemos hacer que todos los pods antiguos sean eliminados antes de que los nuevos entren a trabajar.

Entre otras cosas, podemos controlar cosas cómo.

- (maxSurge): La cantidad de pods máxima que podemos tener dando servicio. Suponiendo que tenemos 3 pods dando servicio, con esta configuración, podemos definir, cuantos pods se levantan para realizar la actualización de los pods antiguos. 

Como sabemos que los pods viejos son reemplazados por los nuevos, con `maxSurge` podemos controlar cuantos pods nuevos pueden estar disponibles a la hora de hacer el update, pudiendo tener 5 (2 nuevos y 3 antiguos) y que luego se vayan reemplazando de manera ordenada.

- (maxUnavailable): lo contrario, define cuantos pods máximo pueden estar indisponibles al mismo tiempo. Por si llegasemos a necesitar que siempre este corriendo por lo menos uno de los pods.

## Rolling Update

Otra cosa que hay que tener en cuenta es que kubernetes, mantiene el anterior `ReplicaSet` cuando se ha realizado el update correctamente. Kubernetes no lo elimina por defecto por si llegasemos a tener la necesidad de volver a la versión anterior de forma rápida por si llegase a haber un problema con la nueva versión.

![Rolling update illustration](<rolling update.png>)

## Proceso de Rolling Update
Ahora, suponiendo que se realiza un cambio en al versión del contenedor que se esta ejecutando y se piensa realizar un Rolling Update, al aplicar el `kubectl apply ...`, se realiza el Rolling Update; de ahí en adelante, los pasos que realizan los pods son los siguientes:

- Se crea un replicaset con la configuración actualizada (se mantiene le replicaset anterior por si es necesario un roll back y los nodos mantienen cacheada la imágen anterior).
- Realiza el cambio de tráfico de los pods antiguos a los pods nuevos en el orden definido en la estrategia.
---
Con los siguientes comandos, podemos ver información relevante para los rolling updates:
- `kubectl rollout status <deployment>`: Muestra de forma resumida el estado del update. En el caso de que este realizado correctament, muestra el mensaje de que esta todo ok.
- `kubectl rollout history <deployment>`: Muestra los cambios que se han realizado.

## Rolling Back
Suponiendo que algo no sale como se esperaba a la hora de hacer el rolling update, ya kubernetes tiene mecanismos para volver al estado anterior de forma rápida y eficiente.

En sí el mecanismo que realizar el clúster por debajo es parecido al rolling update, pero a al inversa, se configura el clúster para activar el replicaset anterior al update.

Esto lo realizamos con los siguientes comandos:

- `kubectl rollout undo <deployment>`: se hace rollback a la versión anterior.
- `kubectl rollout undo <deployment> --to-revision <version>`: se hace rollback a la versión especificada en la flag. Las versiones son las que podemos observar cuando hacemos un `kubectl rollout history <deployment>`.

# cheatsheet de rolling updates
![cheatsheet rolling updates](<cheatsheet rolling updates.png>)