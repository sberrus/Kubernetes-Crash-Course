# Configuración de aplicación en entorno de Kubernetes
Para este paso vamos a crear los ficheros necesarios para la creación de un proyecto en kubernetes de forma que podamos hacer la instalación y configuración del mismo dentro de nuestros entornos.

Lo que haremos es usar la imagen https://hub.docker.com/r/nanajanashia/k8s-demo-app y la configuraremos con las instrucciones del curso.

# Ficheros de configuración del cluster.

Para este ejemplo usaremos 4 ficheros de configuración de componentes.
- ConfigMap: Para poder exponer los endpoints para poder comunicarnos con un pod de mongodb.
- secret: Para almacenar y exponer los secretos de nuestras aplicaciones.
- Deployment and Service (MongoDB): Con esto configuramos MongoDB y sus respectivos despligues y lo agrupamos en un servicio.
- Deployment and Service (Aplicación): Lo mismo que con mongo, pero montando una imagen de una aplicación.

