# Jobs
Los jobs son componentes efimeros que nos permiten crear tareas de corta duración que ejecutan y mueren.

Estos pueden crear uno o multiples pods y se asegura de que un número definido de ellos acabe de forma satisfactoria.

Cuando un determinado número de jobs termina de ejecutar, se considera como completado.

Si se necesita más de un pod, se puede definir que los pods se creen secuencialmente como los StatefulSet o todos al mismo tiempo definiendo paralelismo en la configuración de los jobs.

Ejemplo de jobs de [Jobs kubernetes.io](https://kubernetes.io/docs/concepts/workloads/controllers/job/)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl:5.34.0
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```

## Job commands cheatsheet
![Jobs commands cheatsheet](<Jobs commands cheatsheet.png>)