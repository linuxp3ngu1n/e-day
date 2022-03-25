Configuración

### 1 Crear un clúster de GKE

Configurar la zona del clúster:

`gcloud config set compute/zone us-central1-f`


Habilitar la API de Container Registry:

`gcloud services enable container.googleapis.com` 


Crear un clúster de GKE llamado fancy-cluster con 3 nodos

`gcloud container clusters create fancy-cluster --num-nodes 3`

Listar las 3 instancias del clúster

`gcloud compute instances list`




### 2 Crear un contenedor de Docker

Clonar un repositorio

`cd ~`

`git clone https://github.com/googlecodelabs/monolith-to-microservices.git` (Generar Personal Access Token)


Cambie al directorio correspondiente. También instalará las dependencias de Node.js a fin de poder probar su aplicación antes de implementarla:

`cd ~/monolith-to-microservices`

`./setup.sh`

Comprobar que se está ejecutando la última versión de nvm:

`nvm install --lts`


Entrar en el directorio donde está la aplicación y ejecutarla:

`cd ~/monolith-to-microservices/monolith`

`npm start`

Aparecerá un mensaje del tipo: Monolith listening on port 8080!


Habilitar la API de Cloud Build:

`gcloud services enable cloudbuild.googleapis.com`


Compilar la aplicación:

`cd ~/monolith-to-microservices/monolith`

`gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:1.0.0 .`    --> asegurarse de tener la variable

Puede ver su historial de compilaciones o el proceso en tiempo real si hace clic en Menú de navegación, se desplaza hacia abajo hasta la sección Herramientas y hace clic en Cloud Build > Historial. Aquí puede ver una lista de todas sus compilaciones anteriores.

Ahora que creó un contenedor para su sitio web y lo envió a Google Container Registry, es hora de implementarlo en Kubernetes.


### 3 Implementar el contenedor en GKE

Para implementar y administrar aplicaciones en un clúster de GKE, debe comunicarse con el sistema de administración de clústeres de Kubernetes. Normalmente, esto se hace con la herramienta de línea de comandos kubectl.

Kubernetes representa aplicaciones como Pods, que son unidades que representan un contenedor (o grupo de contenedores estrechamente vinculados). Un Pod es la unidad más pequeña que se puede implementar en Kubernetes. En este clúster, cada Pod contiene solo su contenedor monolith.

Para implementar su aplicación, cree un recurso Deployment. Este recurso administra varias copias de su aplicación, llamadas réplicas, y las programa para que se ejecuten en los nodos individuales de su clúster. En este clúster, el recurso ejecutará solo un Pod de su aplicación. Los recursos Deployment crean un ReplicaSet para garantizar esto. El ReplicaSet debe asegurarse de que la cantidad especificada de réplicas siempre esté en ejecución.


El siguiente comando hará que Kubernetes cree un recurso Deployment llamado monolith en su clúster con 1 réplica.

`kubectl create deployment monolith --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:1.0.0`


Verifique que el recurso Deployment se haya creado correctamente por separado:
# Mostrar pods
`kubectl get pods`

# Mostrar deployments
`kubectl get deployments`

# Mostrar replica sets
`kubectl get rs`

# Incluso combinarlos
`kubectl get pods,deployments`


Verifique que el recurso Deployment se haya creado correctamente de forma global:

`kubectl get all`

En este resultado, se muestran varias cosas:

El recurso Deployment actual (CURRENT)
El ReplicaSet con el recuento de pods deseado (DESIRED) de 1
El Pod en ejecución (Running)

También puede ver sus recursos Deployment de Kubernetes desde Console. Menú de navegación > Kubernetes Engine > Cargas de trabajo.


Para depurar errores se puede realizar con los siguientes comandos:

`kubectl describe pod monolith`

`kubectl describe pod/monolith-7d8bc7bf68-2bxts`

`kubectl describe deployment monolith`

`kubectl describe deployment.apps/monolith`


Para comprobar la alta disponibilidad del clúster de Kubernetes, simule una falla del servidor: borre un pod y vea qué sucede.

`kubectl delete pod/<POD_NAME>`

`kubectl get all` 

El ReplicaSet vio que el pod tenía el estado Terminating y activó un pod nuevo para mantener el recuento deseado de réplicas


### 4 Exponer el contenedor mediante un servicio

Exponer la aplicación al tráfico de Internet de forma explícita mediante un recurso Service, que proporciona herramientas de redes y compatibilidad con IP a los Pods de la aplicación. GKE crea una IP externa y un balanceador de cargas para su aplicación.

`kubectl expose deployment monolith --type=LoadBalancer --port 80 --target-port 8080`

GKE asigna la dirección IP externa al recurso Service, no a Deployment. Para acceder al servicio a través de esa IP externa, con el comando:

`kubectl get service`

Para verificar que puede acceder a la IP externa cópiela y escriba la URL (como http://203.0.113.0) en su navegador.


### 5 Escalar el contenedor a varias réplicas

Se suele realizar cuando el tráfico ha crecido.
Para escalar el Deployment a máximo 3 réplicas ejecutar:

`kubectl scale deployment monolith --replicas=3`

Para verificar que se han creado ejecutar:

`kubectl get all`




### 6 Modificar el sitio web

Por ejemplo, cambiar la página principal de su sitio porque considera que debería ofrecer más información sobre la identidad de la empresa y el producto que vende.

En el equipo de desarrollo crea los cambios con el nombre de archivo index.js.new.
Después se sobreescribe este archivo en index.js.

`cd ~/monolith-to-microservices/react-app/src/pages/Home`

`mv index.js.new index.js`

Visualiza el contenido para comprobar que se han hecho los cambios:
`cat ~/monolith-to-microservices/react-app/src/pages/Home/index.js`


Compilar la aplicación de React y copiarla en el directorio público monolith:

`cd ~/monolith-to-microservices/react-app`

`npm run build:monolith` 

Ahora que se actualizó el código, debe volver a compilar el contenedor de Docker y publicarlo en Google Cloud Container Registry. Use el mismo comando que antes, pero esta vez actualice la etiqueta de la versión.

Ejecute el siguiente comando para activar una nueva Cloud Build con una versión de imagen actualizada de 2.0.0:

`cd ~/monolith-to-microservices/monolith`

`gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:2.0.0 .` 



### 7 Lanzar una versión nueva sin tiempo de inactividad

Use el siguiente comando a fin de indicarle a Kubernetes que desea actualizar la imagen para su implementación a una versión nueva:

`kubectl set image deployment/monolith monolith=gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:2.0.0`


Puede validar la actualización de su recurso Deployment ejecutando el siguiente comando:

`kubectl get pods`


Aquí verá que se crean 3 pods nuevos y se desactivan los viejos. La columna AGE indica cuáles son nuevos y cuáles son viejos. Al final, nuevamente verá solo 3 pods, que serán sus 3 pods actualizados.

Para verificar nuestros cambios, regrese a la pestaña de la página web de la aplicación y actualice la página. Observe que se actualizó su aplicación.

Ahora, su sitio web debería mostrar el texto que acaba de agregar al componente de la página principal.












