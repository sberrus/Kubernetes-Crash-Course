# CronJob
Los CronJobs son un superset de los jobs, los cuales permite la ejecución de estos realizando ejecuciones con un intervalo de tiempo.

Ejemplo de cronjob
``` yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "* * * * *" # Esto siempre es UTC
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```

## Historial
Cada vez que se ejecuta el job, lo que hace este componente es crear pods individuales por cada ejecución. 

Por defecto, se mantienen los pods de las últimas 3 ejecuciones y el último que haya fallado. 

Este comportamiento se puede controlar mediante la definición del componente.

## cronjobs cheatsheet
![Cronjob cheatsheet](<cronjob cheatsheet.png>)