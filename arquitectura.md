# Arquitectura de un cluster de Kubernetes

En esta sección veremos los principales componentes que componen un cluster en kubernetes.

## Master node (Control Plane)
Este es el cerebro del cluster.

Se trata del nodo que contiene los binarios que se encargan de realizar las tareas de manejo de los WN.

En este nodo nos encontraremos con unos contenedores que se encargan de realizar las tareas de administración del cluster. Los contenedores que nos encontramos son los siguientes:

- API SERVER: En este contenedor, se encuentra correindo un servidor que se encarga de la comunicación entre los WN y el control plane; además es donde se monta la UI, la API y la linea de comandos.
- Controller Manager: Este contenedor es el que se encarga de comunicarse constantemente con los contenedores y los nodos WN para ver y gestionar los contenedores que estan corriendo y realizar las tareas de gestión de los mismos.
- Scheduler: En este contenedor se corre la lógica encargada de distribuir los contenedores en los nodos en base a la carga que estos tengan.
- etcd: Es la BBDD del cluster. Siendo este el componente principal para el almacenamiento del estado del cluster. Esto trae como ventaja el poder hacer backups de esta BBDD estando aquí la información del cluster; permitiendonos poder recuperar el sistema tal cual como estaba a partir de un snapshot realizado anteriormente.

### Garantizando HA en el cluster.

El control plane es la parte crítica del cluster siendo este el cerebro de nuestra infraestructura de Kubernetes.

Tomando esto en cuenta, como mínimo deberíamos tener 2 control plane para poder tener un mínimo de HA en el sistema evitando caídas y posibles fallas de acceso al sistema; aunque en entornos productivos, lo suyo es que hayan multiples master para evitar una perdida de servicio por inaccesibilidad al sistema.

## Virtual network

Virtual Network nos permite crear un red virtual para todos los nodos que componen el cluster de manera que podamos gestionar la lógica del cómo se trata la red interna del cluster en todo momento. 

Digamos que es la autopista de comunicación que tiene el cluster y el cual es accesible para todos los nodos que compongan nuestro cluster.

## Worker nodes (Nodos)

...

Cada uno de los worker nodes WN tiene instalado kubelet permite que los WN se comuniquen con el control plane y este pueda mandarle a los WN los comandos e instrucciones a realizar para el cluster.

Cada uno de los WN serán donde se ejecutarán los distintos contenedores que dan servicio. Los contenedores son desplegados en base a la carga que tengan los WN en ese momento.

Los nodos son el hardware donde se van a correr los despliegues de kubernetes.

Estos pueden ser máquinas físicas o virtuales que son las que enrolamos en el clústes para dar servicio a las aplicaciones que vayamos a desplegar.

## Pods
Son la unidad mínima dentro del clúster. Es una abstracción que arropa a lo que consideramos un contenedor. Por lo que cada pod tendrá dentro un contenedor ya sea podman o docker en el cual se van a correr las aplicaciones.

Usualmente los pods solo contienen una aplicación.

Los pods se alojan en los nodos para dar servicio y los control nodes son los encargados de manejar los despliegues y las configuraciones de los pods.

Los pods se les asigna una IP virtual dentro de todo el clúster para poder comunicarse entre ellos.

Hay que tener en cuenta es que los pods son efímeros, por lo que estos pueden ser destruidos fácilmente. Cada vez que se genera un nuevo pod, este le asigna una IP nueva, para controlar como se conectan los pods tomando este aspecto y muchos otros más, estan los servicios.

## Service and Network

Los servicios son un mecanismo que tiene Kubernetes que permite que ordenar de forma lógica la comunicación de red entre los pods que la componen. 

Esto permite poder agrupar pods y configurar el cómo se comunican entre ellos de forma dínamica viendo que cada vez que se genera un pod, este obtiene una IP nueva.

### External Service and Internal Service
Con estos componentes, permitimos que un servicio pueda comunicarse con fuentes externas de forma explícita y segura.

Lo bueno de esto es que podemos definir de forma explícita el cómo y a que se va a tener acceso desde fuera hacía el servicio.

Por ejemplo: Tenemos una backend con acceso a una api y una BBDD MySQL donde almacenaremos los datos. Podemos configurar que sea únicamente accesible los endpoints de la API y no exponemos al público la BBDD que esta corriendo en el mismo servicio.

### Ingress
Este componente nos permite exponer nuestra aplicación fuera del ambito de kubernetes.

Con este configuramos un servidor web entre Kubernetes y el mundo exterior. Ya podemos exponerlo a internet entre otras cosas según se necesite. 

Los Ingress según la configuración, se pueden usar las siguientes tecnologías para controlar el servidor NGINX, Traefik, HAProxy, Envoy, entre otras.