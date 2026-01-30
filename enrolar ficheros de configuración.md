# Enrolar ficheros de configuración
Para añadir los ficheros de configuración al clúster. Debemos ejecutar el comando `kubectl apply -f <ruta_fichero_configuracion_componente>`.

## Orden de configuración
Una de las cosas que hay que tener en cuenta es el orden en el que vamos a hacer el enrolamiento de las configuraciones. Como se trabaja haciendo referencia entre componentes, lo primero que debemos cargar son los componentes secret y configmap para que luego el resto de componentes sea capaz de conectarse entre sí.

Dependiendo de la complejidad y de los componentes que esten interconecados, deberemos enrolarlos con cierto orden para que estos carguen correctamente.

## Revisar despliegue
Cuando ya se haya enrolado todo y se haya configurado el clúster para dar servicio. Podemos comprobar el estado con el comando `kubectl get <node|pod|all>` entre otras opciones. Para ver todo lo que esta enrolado, podemos usar `kubectl get all` de manera que con esto, podamos ver todos los componentes que estan dando servicio.

Veriamos algo como lo sigiente:

```
NAME                                      READY   STATUS    RESTARTS   AGE
pod/mongodb-deployment-7bfd89c8d6-ccsgv   1/1     Running   0          101s
pod/webapp-deployment-67c784bbc6-n89bq    1/1     Running   0          66s
pod/webapp-deployment-67c784bbc6-pw454    1/1     Running   0          66s

NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/kubernetes       ClusterIP   10.96.0.1        <none>        443/TCP          2d
service/mongo-service    ClusterIP   10.105.157.2     <none>        27017/TCP        12m
service/webapp-service   NodePort    10.108.100.144   <none>        3000:30100/TCP   66s

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mongodb-deployment   1/1     1            1           101s
deployment.apps/webapp-deployment    2/2     2            2           66s

NAME                                            DESIRED   CURRENT   READY   AGE
replicaset.apps/mongodb-deployment-7bfd89c8d6   1         1         1       101s
replicaset.apps/webapp-deployment-67c784bbc6    2         2         2       66s
```

Para ver más detalles de un componente podemos hacer uso del comando `kubectl describe <component> <id_component>`

```
Name:                     webapp-service
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=webapp
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.108.100.144
IPs:                      10.108.100.144
Port:                     <unset>  3000/TCP
TargetPort:               3000/TCP
NodePort:                 <unset>  30100/TCP
Endpoints:                10.244.0.6:3000,10.244.0.7:3000
Session Affinity:         None
External Traffic Policy:  Cluster
Internal Traffic Policy:  Cluster
Events:                   <none>
```

