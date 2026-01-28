# Configuración
Los nodos `Control Plane` es donde gestionamos y controlamos la infraestructura de nuestro clúster.

Para nosotros poder interactura con el clúster, en los nodos de control, tenemos 3 formas de acceder a la configuración que nos proporciona la herramienta API SERVER que hemos descrito anteriormente. Los métodos que tenemos para acceder son los siguientes:

- UI: Se despliega una interfaz web en la cual podemos ver multiples configuraciones y datos relacionados a nuestro clúster.
- API: Una API REST para poder interactuar con nuestro nodo a travás de peticiones http.
- CLI Tool (kubectl): También en el nodo, podemos realizar estas acciones mediante una CLI con la cual podemos interactuar con el clúster.

Toda la configuración se basa en ficheros yaml en los cuales podremos definir la configuración de nuestro clúster de forma **declarativa**. Sabiendo que lo que tiene la configuración es lo que debería hacer.

El `controller manager` es el encargado de garantizar que el estado deseado del clúster sea el estado actual del mismo en base a las configuraciones definidas en los ficheros yaml de configiración.

## Anatomía de un fichero de configuración yaml
Todos los ficheros de configuración de kubernetes tienen 3 partes:

Antes que nada, las primeras líneas definen el tipo de componente que se va a utilizar. Para esto, los primeros campos que todo componente debe tener son:

- apiVersion: Son agrupaciones de los componentes, cada componente tiene su propio `apiVersion` el cual deberemos configurar.
- kind: Es el componente per sé. En este definimos los diferentes tipos de componentes que se van a utilizar como deployment, statefulSet, Service, ingress, etc...

Luego, tendremos bloques de configuración que detallamos a continuación:

1. Metadata: En estos definimos los metadatos del componente que estamos configurando. Como el nombre del componente o los labels.
2. Specification (spec): En este bloque, definimos las especificaciones del componente. En estos, dependiendo del tipo de componente, definimos las configuraciones del estado deseado que debe tener el componente dentro del clúster.
3. Status: Esta última parte va a ser anexada, configurada y gestionada directamente por kubernetes. Sabiendo que los ficheros de configuración son declarativos, lo que hace kubernetes es comprobar constantemente si el estado deseado del componente y el estado actual del mismo son iguales. En el caso de que no lo sean, el clúster siempre va a auto-corregirse de manera que siempre cumpla con el estado deseado del componente.

Suele recomendarse tener un repositorio de GIT para mantener controladas los ficheros de configuración de forma ordenada y gestionando las versiones de la misma.

## etcd. El cerebro del clúster.
Esta pieza es la encargada de almacenar los estados del cluster y de sus componentes desplegados. En este se encuentran los distintos estados en los que se encuentra el clúster y las configuraciones del mismo. De manera que desde aquí, se gestiona todo el estado general del clúster.

