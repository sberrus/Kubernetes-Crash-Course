# Storage and volumes
Cómo ya sabemos, los pods son efimeros por lo que esto también afecta a los volumenes de los pods per sé.

Cuando estos se borran, toda la información interna desaparece; dependiendo del tipo de pod, hay veces en la que necesitamos gestionar el almacenamiento para que sea persistente, por lo que para este fín, kubernetes tiene la caracteristica de poder crear volumenes y almacenamiento persistente a traves de los Volumes.

Esta caracteristica nos permite alojar volumenes persistentes en el clúster para poder almacenar estos datos e indiferentemente del ciclo de vida de la aplicación, estos se mantendrán ahí resguardados.

# Volumes
Los volumenes son almacenamiento persistente que configuramos en el clúster. Esto nos permite darle persistencia de datos a los pods que necesiten dicha persistencia.

Es posible crear almacenamiento `dinámico` y `estático`.

# Storage
Para crear los volumenes, primero debemos configurar como va a ser el almacenamiento, dependiendo del vendor, habrán ciertos plugins que nos ayuden a comunicarnos con (google cloud, AWS, Azure, entre otros,,,) que tienen sus propios sistemas de almacenamiento por lo que con los `Container Storage Interface` podemos configurar dicho almacenamiento.

Luego, con esto, podemos configurar componentes de kubernetes como `Persistent Volume` o `Storage Class`.

![Storage and pluggins diagram](<Storage and pluggins diagram.png>)

# Persistent Volumes and Claims
Estos son componentes que nos permiten configurar almacenamiento persistente en el clúster.

## Persistent Volumes
Los PV son un componente configurado por los administradores, que nos permite crear un almacenamiento persistente accesible por todo el clúster.

Hay muchos tipos de almacenamiento persistente los cuales dependiendo del proveedor, se tendrá que configurar un plugin u otro.
![tipos de almacenamiento persistente](<tipos de almacenamiento persistente.png>)

## Persistent Volume Claim
Cuando se ha configurado un PV, luego a nivel de pod, podemos configurar un PVC, que permite separar dentro del cluster un espacio en concreto dentro de un PV. 

Al realizar esto, podemos hacer Claims del volumen para poder asignar un espacio en concreto de un volumen para que estos puedan ser consumidos por los pods.

En palabras simples, son volumenes que se configuran a partir de un PV, accesibles desde todo el clúster y que nos permite proveer de almacenamiento persistente a los pods.

![PVC diagram](<PVC diagram.png>)

Es importante destacar que los PVC, la configuración del access mode debe ser identica a la que contenga el PV al que se le quiere hacer el claim.

![config PVC](<config PVC.png>)


### Reclaim Policies
Las RP son configuraciones que nosotros definimos a la hora de configurar un PVC; con esto podemos definir como va a ser el ciclo de vida de los PVC y cómo se gestionan sus datos.

- Delete: Este borra todos los datos cuando el PVC es liberado. Cuando ningún pod consume el dato, este lo elimina. (default)
- Retain: Mnatiene la información indiferentemente de si hay algún pod consumiendo los recursos.

### Access Modes
Con esto, podemos definir quienes tiene permisos de lectura o escritura en los PVC.

- ReadWriteMany: Permite que múltiples pods puedan montar este volumen como read-write.
- ReadOnlyMany: Permite que múltiples pods puedan montar este volumen como read-only.
- ReadWriteOnce: Este permite que un único pod tenga permisos de read-write, y el resto solo read-only. Útil para cuando necesitas que múltiples pods puedan leer recursos y uno solo sea el encargado de escribir como en las BBDDs.
