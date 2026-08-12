# Persistent Volumes
Los PV son un componente configurado por los administradores, que nos permite crear un almacenamiento persistente accesible por todo el clúster.

Hay muchos tipos de almacenamiento persistente los cuales dependiendo del proveedor, se tendrá que configurar un plugin u otro.
![tipos de almacenamiento persistente](<tipos de almacenamiento persistente.png>)

Ejemplo de PV básico

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
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: "/data/"
```

## Estados de los PV
Los PV pueden tener los siguientes estados:
- Available: Es un recurso que esta disponible y no ha sido reclamado.
- Bound: El recurso ya ha sido reclamado.
- Released: El recurso ha sido liberado, pero sigue estando en el clúster.
- Failed: El recurso ha fallado al intentar hacer el reclamo del mismo.

## cheatsheet
![pv and pvc cheatsheet](<pv and pvc cheatsheet.png>)