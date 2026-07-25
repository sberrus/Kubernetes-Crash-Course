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

## Deployments keys
En los deployments tenemos unos bloques particulares que debemos configurar para cualquier servicio.

### Labels
Los labels son etiquetas que podemos asignarle a kubernetes de forma que podamos darle contexto y metadatos a los componentes, los pods etc...

De forma que a nivel de administración del clúster, sea más sencillo el poder gestionar y relacionar los distintos componentes que forman parte del clúster. Aparte ayudan a poder agrupar de forma lógica los componentes dentro del clúster y aplicar configuraciones en base a estos.

Los labels no estan pensados para dar valores únicos, todo lo contrario, son etiquetas que su objetivo es que esten compartidas para poder agrupar ya sean componentes o los pods.

Internamente, dependiendo del tipo de despliegue que se vaya a hacer, se utiliza una estrategia u otra. Por lo que podemos tener pods que estan nombrados de forma única por el cluster y luego esos pods tendrán labels que compartiran todos aquellos que formen parte del servicio al que pertenecen o al grupo de componentes.

Por ejemplo, en el ejemplo que tenemos de los pods que vamos a configurar para la imagen de mongodb, tenemos los siguientes tags en el `Deployment`.

```yaml
...
spec:
  replicas: 3
  # En el selector indicamos al cúster en base a los labels de los pods a que grupo de pods se aplicará esta configuración. De forma que en selector.matchLabels.app definimos el nombre de la label que usará el cllúster para identificar que pods son los que pertenecen a este componente Deployment.

  # En este ejemplo podemos ver la configuración de este deployment va a aplicarse a todos los templates que tengan la label "app: mongodb"
  selector:
    matchLabels:
        app: mongodb

  # En este caso, vamos a pasar definir que los pods que se levanten de este deployment, van a compartir la label "app: mongodb" y todos los pods de esta template van a tener la configuración de despliegue que se defina en el bloque selector.
  template:
    metadata:
        labels:
            app: mongodb
    spec:
        containers:
        - name: mongodb 
            image: mongo:noble
            ports:
            - containerPort: 27017
...
```

Si bien es cierto que en el resto de componentes, los labels son opcionales, es buena práctica tenerlos configurados para darle más contexto a nuestros componentes en el despliegue.

Si bien es cierto que no hay restricciones a nivel del nombrado de los labels, se suelen seguir ciertas practicas para la definición como pueden ser `app` para identificar a los pods/componentes, `env` para el entorno, `tier`, `version` etc, etc, etc...

## bloque selector
Como se definio anteriormente, los bloques `selector` nos ayudan a indicar al clúster que pods pertenecen a que Deployment, pero hay ciertos aspectos que hay que tener en cuenta como:

- Los selector de los deployments son inmutables. Por lo que al aplicar una configuración de un selector, este no puede modificarse. 

En el caso de que se necesitase cambiar algo por cualquier motivo a nivel de los selectores, habría que crear un nuevo Deployment con la nueva configiración y luego progresivamente en base al Service (que este si puede ser modificado sin problemas) ir moviendo el tráfico de un Deployment a otro. 

Dependiendo de la criticidad del cambio de despliegue, se pueden hacer muchas estrategias para este fin.