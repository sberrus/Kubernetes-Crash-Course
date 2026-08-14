# Observabilidad
Hablando de observabilidad, debemos entender que hay mecanismos del clúster que sirven para determinar el estado del ciclo de vida de los pos y de los componentes que estan corriendo. Mediante estos, el clúster es capaz de determinar si los pods esta corriendo como deberían.

Por defecto, un pod se determina como correcto cuando arranca y el proceso principal se encuentra started, en el caso de completed o terminated, se refiere a cuando el proceso principal ha terminado con exit code 0, de lo contrario, marca el pod como failed o error.

## kubelet
Cómo se ha comentado anteriormente, todas los nodos enrolados en el clúster, tienen kubelet configurado por defecto. Entre muchas otras cosas, kubelet es el encargado de realizar las probes a los pods que se encuentren corriendo en cada nodo. 

## Probes
Las probes son una característica que nos permite definir en los pods el estado de salud de los mismos. Hay varios tipos de probes que nos ayudan a determinar ciertos estados que pudiera tener el pod.

### Startup probes
El primero en actuar es `startup probe`, **hasta que este no este como success, los demás probes no actuan**.

Esto funciona de esta forma debido a que cómo hay ciertas aplicaciones que tardan mucho en arrancar, por lo que hasta que la aplicación no este up, se quedan en stand-by el resto de probes.

### Readiness probes
Son los que nos ayudan a indicarle al clúster cuando un pod esta "ready" y puede aceptar tráfico. 

Si este probe falla, el clúster marca el pod como indisponible y deja de enviarle tráfico al pod, pero no lo mata.

Esto esta confgiurado de esta forma debido a que por ejemplo, puedes tener un endpoint `/ready` que evalua varios componentes internos de la aplicación y comprueba que esten funcionando todos. EJ:

Caso A:
---
app -> ok
proveedor servicio A -> ok
conexión a BBDD -> ok
RESPUESTA PROBE: 200 OK

Caso B:
---
app -> ok
proveedor servicio A -> ok
conexión a BBDD -> fail
RESPUESTA PROBE: 504 service not available (FAILED)

Como vemos, en este caso, puede deberse a una caida puntual de la BBDD y no es necesario matar a todo el pod, se saca del pool y espera a que el pod vuelva a estar ok para volver a enviarle tráfico.

Este tipo de comprobaciones puede ser más lapsa, por lo que hay que para determinar que puntos de mi aplicación van a determinar que el servicio esta ready o no. Por ejemplo, si tienes una aplicación que envia estadisticas o telemetría, si este servicio esta caído, no debería afectar al readiness a menos que esa información sea crítica para la aplicación que se esta corriendo.

### Liveness probes
Este probe es más para determinar si la aplicación se encuentra running, este es para un tipo de probe más agresivo debido a que si este falla, el clúster reinicia el pod entero por lo que empieza desde 0 el ciclo de vida del mismo. 

Además, empieza de cero el ciclo de los probes entrando de nuevo startup en juego.

Readiness se debe pensar más para cuando hay una caída catastrofica de la aplicación que esta corriendo el pod. Se debe pensar en este tipo de probe más en el cómo determino que una aplicación esta completamente caída y debe reiniciarse el pod.

### Tipos acciones de los probes
- ExecAction: Ejecuta un comando en el pod
- TCPSocketAction: Realizar comprobaciones a través del protocolo TCP y comprueba que el puerto configurado se encuentra levantado y aceptando tráfico.
- HTTPGetAction: Realiza una petición HTTP GET al endpoint configurado.
- (experimental) gRPC: Realizar peticiones gRPC al endpoint definido.