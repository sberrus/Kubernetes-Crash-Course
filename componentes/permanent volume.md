# Persistent Volume
Los Persistent volumes, son volumenes gestionados por kubernetes que nos permiten tener un volumen persistente de datos para los componentes del clúster.

Esto es realmente útil para configurar persistencia de datos o por ejemplo, para poder hacer que varios pods puedan acceder al mismo contenido dentro del clúster. 

Un ejemplo de uso podría ser un fichero html para nginx que pueda ser accedido por varios pods. Si el fichero se modifica, todos los pods acceden a la misma información.

Otro ejemplo también podría ser el almacenamiento persistente necesario para BBDDs; por ejemplo, el fichero .dat de sqlite. Indiferentemente de la cantidad de pods, todos pueden acceder a la información que tiene ese fichero data.

Hay muchas formas de configurar `PV`, ya sea en los hosts workers, a tráves de NAS, proveedores externos, etc...

Los `PV` se configuran por parte de los administradores del sistema; luego, mediante los `PVC` se definen los `claims` que son particiones dentro de la `PV` donde se definen cuanto espacio debe tener cada `claim` entre muchas otras cosas.

Lo más habitual es que los `PV` se generen dinámicamente a la hora de crear un PVC, pero en entornos profesionales, primero se configuran los `PV` antes que los `PVC`.

Cómo analogía, se podría decir que los `PV` son parecidos a como conceptualmente trabajan los `Logical Volume` en sistemas UNIX y los PVC como los `Logical Volumes`.

# Persistent Volume Claim (PVC)
Los PVC es una solicitud que se hace desde StatefullSet al clúster para que los pods puedan acceder al `PV`.

Esto con el fin de darle persistencia de datos a los pods, ya que estos son efímeros y al crearse uno nuevo, se crea todo el sistema de ficheros interno del pod.

También mediante los PVC, podemos definir la configuración del cómo se van a montar estos volumenes en los pods (configuración de conexión con provedores externos, como pueden acceder a los datos los pods, quienes pueden escribir).