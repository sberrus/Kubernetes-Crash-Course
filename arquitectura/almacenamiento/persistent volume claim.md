# Persistent Volume Claim
Cuando se ha configurado un PV, luego a nivel de pod, podemos configurar un PVC, que permite separar dentro del cluster un espacio en concreto dentro de un PV. 

Al realizar esto, podemos hacer Claims del volumen para poder asignar un espacio en concreto de un volumen para que estos puedan ser consumidos por los pods.

En palabras simples, son volumenes que se configuran a partir de un PV, accesibles desde todo el clúster y que nos permite proveer de almacenamiento persistente a los pods.

![PVC diagram](<PVC diagram.png>)

Es importante destacar que los PVC, la configuración del access mode debe ser identica a la que contenga el PV al que se le quiere hacer el claim.

![config PVC](<config PVC.png>)

En el ejemplo anterior, podemos ver que hay un PVC que consume del PV 8Gi mediante `selector.matchLabels` definimos de donde el PVC va a coger este almacenamiento.

## Configuración y bindeo de PV <-> PVC

Una cosa importante a tener en cuenta es que los PVC lo que hacen es solicitar los recurso para que `al menos` coja 8Gi, no es que coge los 8Gi exactos.

En los casos en los que configuramos un PV manual, el PVC coge todo el espacio que haya en el PV así sobre espacio en el PV al que se le esta haciendo el claim. 

Normalmente lo que se hace es tener almacenamiento dinámico para evitar estos casos en los que quede bindeado un Persistent Volume el cual un PVC tenga bindeado una parte y no todo el espacio disponible del PV.

Para esto se configuran los `plugins` para los vendors externos y los `almacenamientos dinámicos`, los cuales nos permiten realizar estos claims a sistemas de almacenamiento que proveenel  espacio que realmente se esta solicitando el PVC.

### Reclaim Policies
Las RP son configuraciones que nosotros definimos a la hora de configurar un PVC; con esto podemos definir como va a ser el ciclo de vida de los PVC y cómo se gestionan sus datos.

- Delete: Este borra todos los datos cuando el PVC es liberado. Cuando ningún pod consume el dato, este lo elimina. (default)
- Retain: Mnatiene la información indiferentemente de si hay algún pod consumiendo los recursos.

### Access Modes
Con esto, podemos definir quienes tiene permisos de lectura o escritura en los PVC.

- ReadWriteMany: Permite que múltiples pods puedan montar este volumen como read-write.
- ReadOnlyMany: Permite que múltiples pods puedan montar este volumen como read-only.
- ReadWriteOnce: Este permite que un único pod tenga permisos de read-write, y el resto solo read-only. Útil para cuando necesitas que múltiples pods puedan leer recursos y uno solo sea el encargado de escribir como en las BBDDs.

# Ejemplo de configuración.
Para entender mejor el uso del almacenamiento persistente, veremos un ejemplo simple de los 3 componentes principales que intervienen en este tipo de configuraciones: PV, PVC y Pod.

Configuración de PV: Se configura un PV con una capacidad de 10Gi.
---
``` yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv001
  labels:
    type: local
spec:
  storageClassName: ssd
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce # El accessMode de los PVC deben coincidir con el del PV.
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: "/data/"
```

Configuración de PVC: Se crea un PVC para hacer un claim de por lo menos 8Gi.
---
``` yaml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce # El accessMode de los PVC deben coincidir con el del PV.
  resources:
    requests:
      # Esto no es que se va a pedir 8Gi, esto indica al clúster, que este PVC solicita por lo menos 8Gi para poder funcionar correctamente, si hay algún PV que contenga más de los requeridos por el PVC, este va a ocupar lo solicitado, pero el resto del almacenamiento no va a poder ser utilizado por otros PVCs.  
      storage: 8Gi 
  storageClassName: ssd

  # Para los selectores, lo que va a hacer el clúster es buscar de todos los PV que hayan disponibles, uno que cumpla con los requisitos de este PVC.
  selector:
    matchLabels:
      type: local
```

Configuración del Pod consumidor del PVC.
---
``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd # Este name indica la referencia que va a utilizar para el PVC que se montará en el pod. Igual a como funcionan las referencias en los servicios para definir un nombre para un puerto en concreto para solo modificarlo en un solo sitio de ser necesario. Si "volumes.[?].name" cambia su configuración, como estamos accediendo en base a una referencia, cambiará para el pod también.
  volumes:
    - name: mypd # asignamos un nombre a la referencia de este volumen a montar en el/los pods.
      persistentVolumeClaim:
        claimName: myclaim # nombre del PVC al que queremos montar en el pod.
```

Resultado
---
Tenemos entonces un PV de 10Gi en local, que es consumido por un PVC que se utiliza para montar el volumen en los pods donde se referencian estos PVC.

Si se ven los detalles del PVC, podemos ver que este usa los 10Gi de el PV indistintamente de que le hayamos pedido 8Gi, por lo que los 8Gi del requests del PVC no indica que solo se van a coger 8Gi, sino que por lo menos debe tener 8Gi de almacenamiento y el PVC utilizará todo lo disponible en el PV.
