# Services
Los servicios nos ayudan a definir una política para poder acceder a los pods que lo conforman.

Como ya se ha explicado, los pods en kubernetes son efímeros, y cada vez que se crea un nuevo pod, se le asigna una nueva IP.

Para poder gestionar el cómo nos comunicamos con los pods que conforman un servicio, utilizamos el componente Service.