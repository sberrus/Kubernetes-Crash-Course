# Pods
Son la unidad mínima dentro del clúster. Es una abstracción que arropa a lo que consideramos un contenedor. Por lo que cada pod tendrá dentro un contenedor ya sea podman o docker en el cual se van a correr las aplicaciones.

Usualmente los pods solo contienen una aplicación.

Los pods se alojan en los nodos para dar servicio y los control nodes son los encargados de manejar los despliegues y las configuraciones de los pods.

Los pods se les asigna una IP virtual dentro de todo el clúster para poder comunicarse entre ellos.

Hay que tener en cuenta es que los pods son efímeros, por lo que estos pueden ser destruidos fácilmente. Cada vez que se genera un nuevo pod, este le asigna una IP nueva, para controlar como se conectan los pods tomando este aspecto y muchos otros más, estan los servicios.

# Multicontainer pods

Los pods tienen la capacidad de tener varios contenedores dentro. No suele ser lo más habitual, pero dependiendo de la complejidad del proyecto, es posible que necesites tener diferentes estrategias a la hora de definir contenedores para los pods. Ahora ahondaremos en algunos ejemplos:

La idea principal es la de tener contenedores que son independientes del contenedor que tiene la lógica principal de la aplicación en otro lugar. Por ejemplo: suponiendo que tienes un contenedor que es intensivo en la escritura de logs, puedes tener otro contendor que se encargue de recoger esos logs y mandarlos a un backend de logs o si tienes que hacer validaciones o suscripciones del pod antes de que el contenedor principal arranque. Hay muchos casos y distintas estrategias de despliegue para los pods.

## Estrategias

- sidecar: Son contenedores que ejecutan junto al contenedor principal y se encargan de realizar tareas paralelas. Sin interferir con la lógica del pod principal. EJ: Pod para recoger logs de la aplicación.
- adapter: Esta estrategia permite realizar tareas complejas paralelamente del contendor principal, la idea es la de poder tener un contenedor que nos permita darle más utilidades al contenedor principal sin tener que modificar la lógica del mismo. EJ: Suponiendo que tenemos una pieza que tiene una información que queremos transformar en datos para prometheus, podemos tener un adapter que se encargue de ser el puente entre el contendor principal y el backend de moniorización haciendo de puente y realizando la lógica para transformar la información de formato local a prometheus.
- embassador: Es lo opuesto a adapter. Siendo el contenedor el que se encarga de enviar datos al embassador y el embassador es el que realiza las tareas para exportar estos datos. El flujo es al reves. Siendo el origen o el caracter de intención desde el contenedor principal.

``` yaml
apiVersion: v1
kind: Pod
metadata:
    name: two-containers
spec:
    restartPolicy: Always
    containers:
    # bloque contenedor #1
    - name: mynginx
        image: nginx
        ports:
            - containerPort: 80
    # bloque contenedor #2
    - name: mybox
        image: busybox
        ports:
            - containerPort: 81
        command:
            - sleep
            - "3600"
```

## Volumenes
Un pod puede tener múltiples contenedores, estos contenedores, pueden compartir el mismo volumen dentro del pod.

# Pod Networking
Las ips de los pods son efímeras, por lo que cada vez que se crea un nuevo pod, este recibe una dirección IP nueva.

Los contenedores dentro del pod, comparten la misma dirección del pod que los aloja.

Todos los pods dentro del contenedor son accesibles entre sí siempre y cuando se haya configurado correctamente el "service".

Para que sea accesible desde fuera, se debe tener configurado el proxy para que se alcance a los nodos donde esta desplegado el servicio.

# Administración de los pods
``` shell
# Accedemos al a consola del contenedor
kubectl exec -it [pod-name] --container [container-name] -- /bin/sh

# Matamos un pod ya desplegado. --force y --grace-period es para matar el pod de forma forzosa.
kubectl delete -f [fichero-despliegue] --force --grace-period=0
```

