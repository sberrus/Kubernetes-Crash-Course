# Services
Los servicios nos ayudan a definir rutas que mediante los tags de los pods, podemos hacer que el tráfico llegue a unos pods en concreto.

Como ya se ha explicado, los pods en kubernetes son efímeros, y cada vez que se crea un nuevo pod, se le asigna una nueva IP.

Internamente, los servicios lo que hacen es que junto con el resto de piezas de kubernetes como los services controller, nos ayudan a configurar esas rutas para que podamos acceder a los pods que necesitemos.

![label selector](<label selector.png>)
![label selector2](<label selector2.png>)

Cuando se enrola un nuevo nodo en el clúster, cada nodo tiene un componente llamado kube-proxy que es el que se encarga de dirigir el tráfico a los pods que esten en dichos hosts.

Para poder gestionar el cómo nos comunicamos con los pods que conforman un servicio, utilizamos el componente Service.

Dependiendo de la configuración, los servicios pueden tener varias formas de trabajar a nivel de red para poder acceder a ciertos pods o cómo se realiza el balanceo de carga entre otras cosas.

Otra cosa a tener en cuenta es que en los services, se define tanto qué pods son los que van a formar parte del servicio y los puertos. Esto es útil tomar en cuenta porque pueden haber multiples servicios dirigiendo el tráfico a los mismos pods, pudiendo a su vez tener 2 servicios que vayan a los pods uno de ellos yendo al puerto 80-443 para el servidor web, y otro service yendo al puerto 9090 un MCP por ejemplo expuesto en el mismo pod.

En el manifiesto en [services-clusterip.yaml](../k8s/services/services-clusterip.yaml) se puede observar varios apuntes interesantes de los services, como se comunican, los labels, entre otras cosas.

# Red FQDN y dominios

## matadata.name
Este es el campo principal del service que nos ayuda a identificarlo y además, es el fqdn que se va a registra en el clúster para poder acceder a los recursos de este servicio.

Este fqdn es accesible a nivel del clúster, no de los pods, por lo que si intentas 

# Tipos de servicios
Como hemos comentado antes, los services tienen diferentes formas en las cuales se va a interactuar con la red de los pods, el cómo se exponen y cómo se acceden a estos. Hay multiples caracteristicas a tener en cuenta que detallaremos a continuación.

## Internal services (ClusterIp)
Por defecto todos los servicios son internal (ClusterIp). Por lo que son accedidos dentro del mismo clúster sin posibilidad de poder conectarse desde el exterior. Para poder conectarnos desde fuera del clúster a los servicios, deberemos cambiar el tipo de servicio a `ClusterIp`.

La idea es poder exponer un servicio en el clúster el cual sea accesible de forma persistente independientemente de la ip que el clúster le haya asignado al pod.

Lo que se hace con este tipo de servicios es que se crea una `IP virtual` el cual tiene como hostname el nombre del servicio. Esto nos permite que indiferentemente de la IP del pod, siempre podamos acceder a los recursos que los pods objetivo tienen.

La IP virtual vintulada al servicio es estática, esta se registra automaticamente en todos los kube-proxy del clúster y nos permite poder acceder a los pods de dicho servicio a tráves de un FQDN estable.

El balanceo de la carga es Round Robin, aunque este comportamiento es configurable.

### Cluster IP cheatsheet
![custer ip cheatsheet](<custer ip cheatsheet.png>)

## NodePorts 
Nodeports es una extensión que tiene kubernetes del servicio clusterIP. Este permite conexiones desde dentro del clúster y externas también.

Este componente recibe básicamente la misma configuración que un servicio ClusterIP, pero con unas características adicionales.

### Definición de puertos
Hay que tener en cuenta que por defecto los nodePort disponibles deben estar dentro del rango 30000 y 32767.

### Accesibilidad de los servicios
Cuando se configuran los servicios como NodePort, todos los worker nodes que forman el clúster van a exponer el puerto definido en nodePort, pero este comportamiento se puede configurar para que funcione diferente dependiendo del caso.

En el caso de minikube, al ser un contenedor, podemos coger la IP para poder acceder mediante el comando `kubectl get node -o wide` desplegando la información de los nodos que conforman el clúster.

Otra cosa que debemos tener en cuenta es que la flag `-o wide` se puede pasar a cualqueir componente para ver más detalles de los mismos.

### Acceso externo
Cómo se comento, los nodeports permiten que los workernodes que forman parte del clúster abran los puertos definidos en nodePort, por lo que en teoría se podría configurar para que desde internet, sea posible acceder al clúster. Si bine es posible, no es lo recomendado ya que al hacer esto, si la petición cae en un nodo que no es el que esta alojando el pod con el servicio expuesto, va a haber un salto entre el host que recibe la petición al host que tiene el pod per sé, haciendo que haya una carga de red innecesaria. 

Para estos casos, es mejor configurar configurar componentes más dedicados para este fin como puede ser 

# tags y metadatos
## spec.selector
En los services, nosotros definimos mediante los `spec.selector` a donde queremos redirigir el tráfico.

Los services lo que hacen es que apuntan el tráfico a los pods que contengan los tags definidos en el campo `spec.selector`. 

Los deployments crean los replicaset, los replicaset crean los pods con las etiquetas y los pods reciben el tráfico solo si los tags machean con los tags que se definen en el servicio en el campo `spec.selector`.

Hay que tener en cuenta que los tags, son agrupaciones lógicas de los componentes dentro del clúster. Dependiendo de cada tipo de componente, se usan los tags para una cosa u otra.