# KUBERNETES

Kubernetes es un orquestador de contenedores desarrollado por Google. 

Permite mantener el estado de los contenedores y entre muchisimas otras cosas poder manejar el como se gestionan, como comparten red, como manejan el fs, etc, etc, etc.

Pero el concepto principal es el de que sirve para manejar aplicaciones contenerizadas.

## Paso de aplicaciones monolíticas a microservicios

A medida que un proyecto o una tecnología avanza, vamos a ir encontrando ciertos problemas a la hora de orquestar nuestras aplicaciones a medida que esta va creciendo. Llega el punto en el que tenemos que empezar a pasar nuestras aplicaciones de monolitos a microservicios.

Al pasar las aplicaciones a microservicios ya podemos tener containers que nos permita tener instancias de nuestra aplicación y con kubernetes, podemos orquestar multiples contenedores para dar servicio a una aplicación en concreto.

La idea es la de poder crear, gestionar, actualizar y destruir contenedores bajo demanda y bajo ciertas políticas que nosotros definamos.

En resumidas. Lo que nos ayuda kubernetes es en el poder gestionar de forma dinámica todos estos contenedores para ser gestionados.

## Principales ventajas que nos ofrece la orquestación.

- High Availability: Podemos realizar tareas de mantenimiento y deploy sin tener downtime garantizando que seguimos dando el servicio.
- Escalabilidad: Al poder añadir multiples hosts a un cluster, esto nos permite poder aumentar los recursos que dan soporte a nuestros servicios de manera sencilla; o que estos sean capaz de alocar ciertos contenedores dependiendo de la carga que tengan estos.
- Disaster Recovery: Nos ofrece mecanismos para de ser necesario volver a una versión anterior de nuestra aplicación, esta tarea no sea un drama.