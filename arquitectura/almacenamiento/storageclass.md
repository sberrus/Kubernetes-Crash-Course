# Storage Class
Es una abstracción de los `persistent volumes` que nos permite crear volumenes persistentes usando de base `clases` que nos permiten interactuar con las diferentes interfaces que tienen los proveedores de almacenamiento.

Estos también son administrados por los administradores del clúster y son accesibles desde todo el clúster.

La principal caracteristica es que, teniendo esto, no es necesario crear los Persistent Volumes, por lo que no hay que definir una capacidad concreta para el volumen, a su vez, permitiendo que los volumenes ocupen la cantidad necesaria de espacio en dicho almacenamiento.

![storage class](<storage class.png>)

## Reclaim and access modes policies
Estas se mantienen tal cual con los valores por defecto que tienen dichas políticas en los `persistent volumes`.

## Ejemplo de uso Storage Class básico.
En el siguiente caso de uso, veremos como usar un `StorageClass` usando Azure como proveedor, luego configuraremos un pvc y montaremos el PVC en un Pod.

Configuración de Storage Class usando como almacenamiento Azure
---
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: Standard # identificador de recurso
provisioner: kubernetes.io/azure-disk # plugin del proveedor externo.
# Parámetros de configuración de los proveedores (Estos varian dependiendo del proveedor).
parameters:
  storageaccounttype: Standard_LRS
  kind: Managed
```

Configuración de PVC dinámico
---
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: AzureDiskPVC
spec:
  storageClassName: Standard # Este debe llevar como "class name" el nombre del "Storage Class" que hayas creado, en este caso "Standard".
  # Como "Storage Class" no tiene configurado.
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  # No se configura nada en "selector" debido a que nos estamos conectando directamente con Azure.
```

Configuración de Pod con volumen dinámico
---
**Esto se mantiene tal cual como se hace con los volumenes creados con `Persistent Volume`**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: MyPod
spec:
  containers:
    - name: MyFrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: AzureDiskVolume
  volumes:
    - name: AzureDiskVolume
      persistentVolumeClaim:
        claimName: AzureDiskPVC
```

## Cheatsheet
![storage class cheatsheet](<storage class cheatsheet.png>)