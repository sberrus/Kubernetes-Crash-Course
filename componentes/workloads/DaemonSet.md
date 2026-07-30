# DaemonSet
Este workload se encarga de garantizar que haya por lo menos 1 pod desplegado en cada nodo del clúster. 

Todos los nodos que sean añadidos al clúster tedrán por defecto estos pods configurados.

Suelen usarse en ciertos casos como:
- Un daemon de almacenamiento.
- Daemon para recolección de logs.
- Daemon para monitorización de los nodos del clúster.

![Ilustración despliegue de Daemonset](<Ilustracion despliegue daemonset.png>).

Ejemplo de yaml daemonset para despliegue de fluentd-elasticsearch en todos los nodos del clúster 
``` yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      # these tolerations are to have the daemonset runnable on control plane nodes
      # remove them if your control plane nodes should not run pods
      - key: node-role.kubernetes.io/control-plane
        operator: Exists
        effect: NoSchedule
      - key: node-role.kubernetes.io/master
        operator: Exists
        effect: NoSchedule
      containers:
      - name: fluentd-elasticsearch
        image: quay.io/fluentd_elasticsearch/fluentd:v5.0.1
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
      # it may be desirable to set a high priority class to ensure that a DaemonSet Pod
      # preempts running Pods
      # priorityClassName: important
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
```

## Daemonset Cheatsheet
![daemonset cheatsheet](<DameonSet cheatsheet.png>)