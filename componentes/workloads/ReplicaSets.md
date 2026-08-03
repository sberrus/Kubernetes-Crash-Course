# ReplicaSets
Este tipo de workload se encarga de gestionar el ciclo de vida de los pods y que las replicas se encuentren activas y funcionando en todo momento.

Aunque tiene ciertas caracteristicas, lo más recomendado es crear deployments. Es la forma recomendada.

Un fichero de replicaset, embede un fichero con la misma estructura de los pods, con la diferencia de que con este podemos definir la cantidad de replicas y ciertos comportamientos en el caso de que un pod falle.

[Documentación ReplicaSet kubernetes.io](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)

``` yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
      
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: us-docker.pkg.dev/google-samples/containers/gke/gb-frontend:v5
```

## Diferencia entre ReplicaSet and Deployment?
Los deployments son en sí son ReplicaSet, pero con capacidades adicionales que ayudan a gestionar mejor los recursos de la aplicación.

Cuando creas deployments, se crean de forma predeterminada los replicasets.