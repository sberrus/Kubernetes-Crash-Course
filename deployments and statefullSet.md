# Deployments & StatefullSet
Esta caracteristica de los clúster kubernetes son la principal ventaja que tiene Kubernetes a la hora de manejar sistemas distribuidos.

La ventaja que tiene Kubernetes es que nos permite tener replicas de los servicios de manera que en el caso de que un servicio falle, no tengamos perdida de servicio.

Para esto Kubernetes nos ofrece los componentes de Deployment y StatefullSet.

## Deployment
Sabiendo que los pods mediante los servicios se comunican entre sí y además, sirve de balanceador para distribuir la carga entre los nodos, el cómo y cuantas replicas de los pods tendremos se configuran en los deployments.

Los deployments son una abstracción de los pods encargados de definir cuantas replicas tendrán entre otras configuraciones de distribución.

## StatefullSet
Para este componente, hay que entender el porqué del mismo.

Suponiendo que tenemos un servicio con pods de BBDD, sabiendo que las BBDD acceden generalmente al mismo spacio de almacenamiento, no pueden haber múltiples instancias de BBDD accediendo al mismo tiempo al mismo espacio de almacenamiento ya que genera una serie de problemas como inconsistencias en los datos. 

Para solucionar este mecanismo y a la vez mantener la alta disponibilidad de los pods de BBDD, K8s ofrece el componente de statefullset, el cual nos permite que los servicios puedan instanciarse con mecanismos que hacen que los pods no escriban y lean datos de forma desordenada. Si bien es cierto que Kubernetes tiene esta capacidad, lo cierto es que no es recomendable manejar estas caracteristicas dentro del cluster. 

Es preferible manejar con Kubernetes aplicaciones y servicios que no tengan estas limitaciones y manejar de forma externa la administración de las BBDDs ya que estas suelen tener mecanismos y caracteristicas para clusterizar.

Si bien es posible hacerlo con Kubernetes, es preferible no manejar esto dentro ya que puede ser una tarea bastante engorrosa y complicada.

## En palabras simples
Los deployments estan diseñados para aquellos pods que pueden correr en paralelo sin que esto sea un problema a nivel del manejo de los datos de la aplicación, tampoco estan pensados para almacenar datos de forma persistente.

Por ejemplo: Frontend web, backends rest, microservicios, jobs de procesamiento etc...

Por otro lado los statefulset, estan pensados para ser más persistentes dentro del cluster. Estos tienen una identidad única y estable, tienen nombres predecibles estan pensados para escribir y mantener estado como almacenamiento persistente entre otras cosas.

Ejemplos de estos son: Bases de datos, Kafka, Redis, Zookeeper, Elastic entre otros.

# Anotaciones finales
Esta de las características que vuelve potente el uso de kubernetes ya que al distribuir los sistemas, nos permite tener alta disponibilidad, mejorar las tareas de escalabilidad, etc, etc, etc...