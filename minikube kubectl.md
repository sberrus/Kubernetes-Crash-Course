# Simulación de clúster kubernetes en entorno local
Para no tener que desplegar nodos maestros y worker nodes en un clúster. Vamos a usar **minikube** que es un programa que nos permite simular un cluster de kubernetes con un solo nodo donde estarán todos los recursos de este.

## kubectl
Si bien es cierto que hay varias apis para poder interactuar con el clúster, en este caso usaremos kubectl que es la herramienta de consola que nos ayuda con esta tarea.

La idea es poder interactuar con el clúster de forma pura. Pero en entornos profesionales lo más usual es usar otras apis como UI o herramientas que se cominican con la API REST del clúster.

La otra cosa es que kubectl es un estandar que nos permite simular la misma herramienta de CLI que tiene tanto kubernetes como minikube para aprender a manejar un clúster de kubernetes.

Documentación oficial de minikube para instalar
https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download

## pod in a pod in a pod
Una de las cosas que hay que tener en cuenta es que minikube se ejecuta dentro de un pod. 

En este pod se instala el driver de containerización (podman, docker, vm etc...) usualmente es docker. 

Y luego dentro del pod de minikube es que se instancian el resto de nodos del clúster per sé.

