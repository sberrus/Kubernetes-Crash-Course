# Deployments
Los deployments son un tipo de componente que nos permite controlar pods y gestionar el ciclo de vida de los mismos. 

Como se ha comentado en la clase de `replicaset`, los deployments son en si una forma de gestionar `replicasets` ya que al crear deployments, los componentes que crea kuberentes son `replicasets`.

Los deployments son el método recomendado a la hora de gestionar despliegues de aplicaciones ya que funcionan dandole caracteristicas adicionales a los replicasets, que ya son los encargados de controlar los recursos y el ciclo de vida de los pods dentro del clúster.

## Pods vs Deployments
Los pods son efímeros, por lo que estos no se gestionan por sí solos. Ya este comportamiento se define más en profundidad en otras lecturas; pero la idea principal es que los pods se encargan unicamente de correr la aplicación. En el caso de que la aplicación falle, estos no realizan otra cosa más que matar la instancia y luego es sustituido por otro pod sano.

Los pods por si solos no escalan, ni se actualizan, ni puedes hacerles rollbacks.

En cambio, los deployments si tienen todas las caracteristicas mencionadas anteriormente.

En el caso de que la aplicación desplegada se necesite actualizar, en los deployments, es fácil poder hacer el cambio controlado de la aplicación sin downtime. 

Los deployments se encargan de controlar los pods, dependiendo de la configuración, se controla a su vez, como estos van a ser desplegados, la estrategia de escalado, cuantos pods deben recibir tráfico al mismo tiempo, en que host dentro del clúster se alojaran, entre muchas otras cosas.

A nivel de jerarquia podemos decir lo siguiente

Las capas se manejan de la siguiente forma:
![Capas de responsabilidad de los deployments](<deployments responsability architecture.png>)

Los `deployments` se encargan de gestionar las actualizaciones y los rollbacks.
Los `ReplicaSets` se encargan de gestionar el ciclo de vida de los pods y la escalabilidad.
Los `Pods` son los encargados de gestionar los contenedores, por ende la aplicación en sí.

Ejemplo de manifiesto de un deployment desde [Documentación de deployments kubernetes.io](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
# Dentro de las specs definimos las caracteristicas de este deployment (replicas, cuantas versiones de la aplicación se mantienen cargadas en el clúster,estrategias de despliegue, rotado y actualización, entro otras cosas)
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx

# Dentro del bloque template, definimos las caracteristicas que va a tener el pod en sí. Imagen, labels, puertos, etc... Son las mismas configuraciones que tiene el componente Pod
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

![Cheatsheet de kubectl para los deployments](<deployment management cheatsheet.png>)