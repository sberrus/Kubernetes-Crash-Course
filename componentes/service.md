# Services
Los servicios nos ayudan a definir una política para poder acceder a los pods que lo conforman.

Como ya se ha explicado, los pods en kubernetes son efímeros, y cada vez que se crea un nuevo pod, se le asigna una nueva IP.

Para poder gestionar el cómo nos comunicamos con los pods que conforman un servicio, utilizamos el componente Service.

# Internal services (ClusterIp)
Por defecto todos los servicios son internal (ClusterIp). Por lo que son accedidos dentro del mismo clúster sin posibilidad de poder conectarse desde el exterior. Para poder conectarnos desde fuera del clúster a los servicios, deberemos cambiar el tipo de servicio a `ClusterIp`.

# External services (NodePort) 
Los NodePort services si nos permiten poder comunicarnos desde fuera del clúster hacía adentro. Es la parte que nos ayuda a definir como nos vamos a conectar desde el exterior hacía dentro del clúster.

Una cosa que hay que tener en cuenta repecto a los NodePort es que estos hay que asignarles en el bloque `ports` el campo nodePort ...

Hay que tener en cuenta que por defecto los nodePort disponibles son dentro del rango 30000 y 32767.

Cuando se configuran los servicios como NodePort, todos los worker nodes que forman el clúster van a exponer el puerto definido en nodePort, pero este comportamiento se puede configurar para que funcione diferente dependiendo del caso.

En el caso de minikube, al ser un contenedor, podemos coger la IP para poder acceder mediante el comando `kubectl get node -o wide` desplegando la información de los nodos que conforman el clúster.

Otra cosa que debemos tener en cuenta es que la flag `-o wide` se puede pasar a cualqueir componente para ver más detalles de los mismos.