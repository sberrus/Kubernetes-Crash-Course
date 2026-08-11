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