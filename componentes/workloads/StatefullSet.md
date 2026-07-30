# StatefulSet
Este tipo de despliegue es para pods que necesiten ser persistentes y mantener los estados. Son los usados principalmente por bases de datos para tener en el clúster alta disponibilidad.

Tiene ciertas caracteristicas como las siguientes:
- Los identificadores son persistentes.
- Cada pod de este workload se identifica con la siguiente nomenclatura: name-#
- Si el pod muere, es reemplazado por otro con el mismo identificador.
- Para las series de pods, el identificador anexa un número indicando la instancia empezando por cero. [from 0 to X]
- A la hora de eliminar las instancias de la BBDD, va eliminandolos usando el identificador empezando desde el valor más alto y bajando [from X to 0].

Estos workloads son necesarios para cuando a nivel del clúster, necesitan acceder a los pods de forma estable como con `identificadores de red` o `almacenamiento persistente de BBDD`.

## Acceso a los pods directamente a traves de headless service.
Antes hay que definir que es un `headless service` y cual es la diferencia entre este y un servicio normal (ClusterIP).

La diferencia es en el cómo estos resuelven los dominios dentro del proxy de kubernetes.

### ClusterIP
Cuando defines un servicio como `ClusterIP` este genera una IP virtual el cual hace que el clúster pueda realizar el balanceo de carga entre los pods a los que apunta.

### Headless Service
Por otro lado, los `headless services` son servicios que tienen el valor de `ClusterIP` como `none`; haciendo que el clúster no cree la IP virtual para el servicio. Esto nos permite poder acceder directamente a los pods sin que el servicio balancee la carga entre los pods que conforman el clúster.

En el ejemplo de las BBDDs, donde teníamos 4 replicas, pero solo 1 de los pods tiene escritura y lectura y el resto son solo de lectura.

Sabiendo que uno de los pods es el que va a poder leer y escribir, para realizar tareas de escritura en la BBDD debemos apuntar directamente al pod que tiene permitida la escritura mediante el selector dns `[pod name].[service name]` => `mysql-0.mysql` haciendo referencia al dominio el cual se usa para acceder a mysql-0. Por otro lado, a la hora de leer, como todos los pods tiene acceso de lectura a la BBDD, si se puede acceder directamente como hostname al servicio per sé; en este caso `mysql`.

## Consideraciones con las BBDD
Si bien es cierto que es posible. Administrar BBDDs en clústers de kubernetes suele ser una tarea bastante complicada de realizar. Usualmente los DBAs suelen configurar estos clústers con herramientas dedicadas creadas para este fin fuera de kubernetes.

Los pods son stateless por defect, con este workload, podemos definir el pod como statefull para este tipo de casos concretos.

## kubectl cheatsheet
![statefullset kubectl cheatsheet](<statefullset cheatsheet.png>)