# Storage and volumes
Cómo ya sabemos, los pods son efimeros por lo que esto también afecta a los volumenes de los pods per sé.

Cuando estos se borran, toda la información interna desaparece; dependiendo del tipo de pod, hay veces en la que necesitamos gestionar el almacenamiento para que sea persistente, por lo que para este fín, kubernetes tiene la caracteristica de poder crear volumenes y almacenamiento persistente a traves de los Volumes.

Esta caracteristica nos permite alojar volumenes persistentes en el clúster para poder almacenar estos datos e indiferentemente del ciclo de vida de la aplicación, estos se mantendrán ahí resguardados.