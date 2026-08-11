# Storage
Para crear los volumenes, primero debemos configurar como va a ser el almacenamiento, dependiendo del vendor, habrán ciertos plugins que nos ayuden a comunicarnos con (google cloud, AWS, Azure, entre otros,,,) que tienen sus propios sistemas de almacenamiento por lo que con los `Container Storage Interface` podemos configurar dicho almacenamiento.

Luego, con esto, podemos configurar componentes de kubernetes como `Persistent Volume` o `Storage Class`.

![Storage and pluggins diagram](<Storage and pluggins diagram.png>)
