Contenedor GitLab
====
Aquí explica el proceso de crear un contenedor con GitLab

Para crear un contenedor dentro de GitLab tenemos que hacer:

1. Prepara tu proyecto con Docker:

Asegúrate de tener un archivo Dockerfile en la raíz de tu repositorio. Este archivo define cómo se construye tu imagen Docker.

Ejemplo de Dockerfile
   FROM node:16
   WORKDIR /app
   COPY . .
   RUN npm install
   CMD ["npm", "start"]

2. Configura el archivo .gitlab-ci.yml:

Este archivo define los trabajos (jobs) que GitLab CI/CD ejecutará.

A continuación se muestra un ejemplo de cómo construir y ejecutar un contenedor Docker en GitLab CI:

   stages:
      - build
      - deploy

Trabajo de construcción
   build_image:
   stage: build
   script:
        - docker build -t my-app:$CI_COMMIT_SHA .
   tags:
        - docker

 Trabajo de despliegue (opcional)
   deploy:
   stage: deploy
   script:
        - docker run -d -p 8080:8080 my-app:$CI_COMMIT_SHA
   only:
        - main

  Explicación:
   build_image: Este trabajo construye la imagen Docker utilizando el Dockerfile y etiqueta la imagen con el CI_COMMIT_SHA (hash del commit) para que cada construcción tenga un identificador único.
   deploy: Este trabajo (opcional) ejecuta el contenedor Docker resultante en un entorno de producción o pruebas. En este caso, el contenedor se ejecuta en segundo plano y se mapea el puerto 8080.

3. Usar runners de GitLab con soporte Docker:

Asegúrate de que el GitLab Runner que se encargue de ejecutar los trabajos tenga Docker instalado y configurado para ejecutar contenedores. Generalmente, puedes usar un runner de tipo docker para estos casos.

4. Pujar la imagen a un registro (opcional): Si quieres almacenar la imagen en un registro de Docker (como el GitLab Container Registry), puedes agregar un paso para hacer push a dicho registro.

   push_image:
   stage: deploy
   script:
        - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
        - docker push $CI_REGISTRY/my-group/my-project/my-app:$CI_COMMIT_SHA
   only:
        - main

Aquí se hace login al registro de GitLab usando variables de entorno y se empuja la imagen construida.

5. Pujar el archivo .gitlab-ci.yml y otros cambios:
Sube el archivo .gitlab-ci.yml y el Dockerfile a tu repositorio en GitLab.

Resumen de los pasos:

   Prepara un Dockerfile en tu repositorio.
   Crea un archivo .gitlab-ci.yml que defina los trabajos de construcción y despliegue.
   Configura GitLab Runner para usar Docker.
   (Opcional) Sube las imágenes al GitLab Container Registry.
