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

## Worker nodes

...

Cada uno de los worker nodes WN tiene instalado kubelet permite que los WN se comuniquen con el control plane y este pueda mandarle a los WN los comandos e instrucciones a realizar para el cluster.

Cada uno de los WN serán donde se ejecutarán los distintos contenedores que dan servicio. Los contenedores son desplegados en base a la carga que tengan los WN en ese momento.

