---
title: 'Tutorial: Implementación de la aplicación de Spring Boot en un clúster de AKS con servidor flexible de MySQL dentro de una red virtual'
description: Aprenda a compilar e implementar rápidamente una aplicación de Spring Boot en AKS con el servidor flexible de Azure Database for MySQL, con conectividad segura dentro de una red virtual.
ms.service: mysql
author: shreyaaithal
ms.author: shaithal
ms.topic: tutorial
ms.date: 11/11/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8b07ffd9ea4d9cbcba5cf36bcac048f1abccee37
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557194"
---
# <a name="tutorial-deploy-a-spring-boot-application-on-aks-cluster-with-mysql-flexible-server-in-a-vnet"></a>Tutorial: Implementación de la aplicación de Spring Boot en un clúster de AKS con servidor flexible de MySQL en una red virtual

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

En este tutorial, aprenderá a implementar una aplicación de [Spring Boot](https://spring.io/projects/spring-boot) en un clúster de [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md) con [servidor flexible de Azure Database for MySQL](overview.md) en el back-end, comunicándose de forma segura entre sí dentro de una [red virtual de Azure](../../virtual-network/virtual-networks-overview.md). 

> [!NOTE]
> En este tutorial se da por supuesto que tiene un conocimiento básico de los conceptos de Kubernetes, Spring Boot de Java y MySQL.

## <a name="prerequisites"></a>Requisitos previos

- Una suscripción a Azure [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- La [Interfaz de la línea de comandos (CLI) de Azure](/cli/azure/install-azure-cli).
- Un [kit de desarrollo de Java](/azure/developer/java/fundamentals/java-support-on-azure) compatible, versión 8 (incluido en Azure Cloud Shell).
- La herramienta de compilación [Apache Maven](https://maven.apache.org/).
- Un cliente [Git](https://github.com/).
- Un cliente de [Docker](https://www.docker.com/).

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Creación de un Servidor flexible de Azure Database for MySQL 

### <a name="create-a-resource-group"></a>Crear un grupo de recursos
Un grupo de recursos de Azure es un grupo lógico en el que se implementan y administran recursos de Azure. Vamos a crear un grupo de recursos, *rg-mysqlaksdemo*, mediante el comando [az group create](/cli/azure/group#az_group_create) en la ubicación *eastus*.

1. Abra el símbolo del sistema. 
1. Inicie sesión en la cuenta de Azure.
    ```azurecli-interactive
    az login
    ```
1. Elija la suscripción de Azure.
    ```azurecli-interactive
    az account set -s <your-subscription-ID>
    ```    
1. Cree el grupo de recursos.
    ```azurecli-interactive
    az group create --name rg-mysqlaksdemo --location eastus
    ```

### <a name="create-a-mysql-flexible-server"></a>Creación de un servidor flexible MySQL

Ahora crearemos un servidor flexible en una red virtual (método de conectividad de acceso privado).

1. Cree una red virtual de Azure *vnet-mysqlaksdemo* para todos los recursos de este tutorial, y una subred *subnet-mysql* para el servidor flexible de MySQL.

    ```azurecli-interactive
    az network vnet create \
    --resource-group rg-mysqlaksdemo \
    --name vnet-mysqlaksdemo \
    --address-prefixes 155.55.0.0/16 \
    --subnet-name subnet-mysql \
    --subnet-prefix 155.55.1.0/24 
    ```

1. Cree un servidor flexible Azure Database for MySQL *mysql-mysqlaksdemo* en la subred creada anteriormente, mediante el comando [az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create). Reemplace los valores de nombre de usuario y contraseña de administrador.

    ```azurecli-interactive
    az mysql flexible-server create \
    --name mysql-mysqlaksdemo \
    --resource-group rg-mysqlaksdemo \
    --location eastus \
    --admin-user <your-admin-username> \
    --admin-password <your-admin-password> \
    --vnet vnet-mysqlaksdemo \
    --subnet subnet-mysql
    ```
    
    Ahora ha creado un servidor flexible en la región eastus con proceso de B1MS ampliable, almacenamiento de 32 GB, período de retención de copia de seguridad de 7 días, y en la subred proporcionada *subnet-mysql*. Esta subred no debe tener ningún otro recurso implementado y se delega a Microsoft.DBforMySQL/flexibleServers.

1. Configure una nueva base de datos MySQL ```demo``` que se usará con la aplicación de Spring Boot.

    ```azurecli-interactive
    az mysql flexible-server db create \
    --resource-group rg-mysqlaksdemo \
    --server-name mysql-mysqlaksdemo \
    --database-name demo
    ```

## <a name="create-an-azure-container-registry"></a>Creación de un registro de contenedor de Azure 

Crear una instancia de Azure Container Registry en el grupo de recursos. Este tutorial inserta la aplicación de ejemplo en forma de imagen Docker en este registro en los pasos posteriores. Reemplace ```mysqlaksdemoregistry``` por un nombre único para el registro.

```azurecli-interactive
az acr create --resource-group rg-mysqlaksdemo \
--location eastus \
--name mysqlaksdemoregistry \
--sku Basic
```

## <a name="code-the-application"></a>Incorporación del código de la aplicación

En esta sección, incorporaremos el código de la aplicación de demostración. Si desea ir más rápido, puede descargar la aplicación codificada disponible en [https://github.com/Azure-Samples/tutorial-springboot-mysql-aks](https://github.com/Azure-Samples/tutorial-springboot-mysql-aks) y pasar a la sección siguiente: [Compilación de la imagen e inserción en ACR](#build-the-image-and-push-to-acr). 

1. Generación de la aplicación con Spring Initializr. 

    ```bash
    curl https://start.spring.io/starter.tgz \
    -d dependencies=web,data-jdbc,mysql \
    -d baseDir=springboot-mysql-aks \
    -d bootVersion=2.5.6.RELEASE \
    -d artifactId=springboot-mysql-aks \
    -d description="Spring Boot on AKS connecting to Azure DB for MySQL" \
    -d javaVersion=1.8 | tar -xzvf -
    ```
    
    Se generará una aplicación Spring Boot de base dentro de la carpeta ```springboot-mysql-aks```.
    
    Use su editor de texto favorito, como [VSCode](https://code.visualstudio.com/docs) o cualquier IDE, para los pasos siguientes.

1. Configuración de Spring Boot para el uso del servidor flexible de Azure Database for MySQL.

    Abra el archivo src/main/resources/application.properties y agregue el siguiente fragmento. Este código lee el host y el nombre de la base de datos, el nombre de usuario y la contraseña del archivo de manifiesto de Kubernetes.
    
    ```properties
    logging.level.org.springframework.jdbc.core=DEBUG
    spring.datasource.url=jdbc:mysql://${DATABASE_HOST}:3306/${DATABASE_NAME}?serverTimezone=UTC
    spring.datasource.username=${DATABASE_USERNAME}
    spring.datasource.password=${DATABASE_PASSWORD}
    spring.datasource.initialization-mode=always
    ```
    >[!Warning]
    > La propiedad de configuración ```spring.datasource.initialization-mode=always``` significa que, cada vez que se inicie el servidor, Spring Boot generará automáticamente un esquema de base de datos con el archivo ```schema.sql``` que crearemos más adelante. Esto es excelente para las pruebas, pero recuerde que se eliminarán los datos en cada reinicio, por lo que no debe usarse en producción.

    >[!Note]
    >Anexamos ```?serverTimezone=UTC``` a la propiedad de configuración ```spring.datasource.url``` para indicar al controlador de JDBC que use el formato de fecha UTC (hora universal coordinada) al conectarse a la base de datos. De lo contrario, el servidor Java no usaría el mismo formato de fecha que la base de datos, y daría lugar a un error.

1. Creación del esquema de la base de datos.
    
    Spring Boot ejecutará automáticamente ```src/main/resources/schema.sql``` para poder crear un esquema de base de datos. Cree ese archivo con el siguiente contenido:

    ```sql
    DROP TABLE IF EXISTS todo;
    CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
    ```
1. Programación de la aplicación de Spring Boot de Java.
    
    Agregue el código Java que usará JDBC para almacenar y recuperar datos del servidor de MySQL. Cree la nueva clase de Java ```Todo```, junto a la clase ```DemoApplication``` y agregue el siguiente código:

    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.data.annotation.Id;
    
    public class Todo {
    
        public Todo() {
        }
    
        public Todo(String description, String details, boolean done) {
            this.description = description;
            this.details = details;
            this.done = done;
        }
    
        @Id
        private Long id;
    
        private String description;
    
        private String details;
    
        private boolean done;
    
        public Long getId() {
            return id;
        }
    
        public void setId(Long id) {
            this.id = id;
        }
    
        public String getDescription() {
            return description;
        }
    
        public void setDescription(String description) {
            this.description = description;
        }
    
        public String getDetails() {
            return details;
        }
    
        public void setDetails(String details) {
            this.details = details;
        }
    
        public boolean isDone() {
            return done;
        }
    
        public void setDone(boolean done) {
            this.done = done;
        }
    }
    ```
    Esta clase es un modelo de dominio asignado a la tabla ```todo``` que creó antes.

    Para administrar esa clase, necesitará un repositorio. Defina una nueva interfaz ```TodoRepository``` en el mismo paquete:
    
    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.data.repository.CrudRepository;
    
    public interface TodoRepository extends CrudRepository<Todo, Long> {
    }
    ```

    Este es un repositorio que Spring Data JDBC administra.

    Finalice la aplicación mediante la creación de un controlador que pueda almacenar y recuperar datos. Implemente la clase ```TodoController``` en el mismo paquete y agregue el código siguiente:
    
    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.http.HttpStatus;
    import org.springframework.web.bind.annotation.*;
    
    @RestController
    @RequestMapping("/")
    public class TodoController {
    
        private final TodoRepository todoRepository;
    
        public TodoController(TodoRepository todoRepository) {
            this.todoRepository = todoRepository;
        }
    
        @PostMapping("/")
        @ResponseStatus(HttpStatus.CREATED)
        public Todo createTodo(@RequestBody Todo todo) {
            return todoRepository.save(todo);
        }
    
        @GetMapping("/")
        public Iterable<Todo> getTodos() {
            return todoRepository.findAll();
        }
    }
    ```

1. Cree un Dockerfile en el directorio base *springboot-mysql-aks* y copie este fragmento de código.

    ```dockerfile
    FROM openjdk:8-jdk-alpine
    RUN addgroup -S spring && adduser -S spring -G spring
    USER spring:spring
    ARG DEPENDENCY=target/dependency
    COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
    COPY ${DEPENDENCY}/META-INF /app/META-INF
    COPY ${DEPENDENCY}/BOOT-INF/classes /app
    ENTRYPOINT ["java","-cp","app:app/lib/*","com.example.springbootmysqlaks.DemoApplication"]
    ```

1. Vaya al archivo *pom.xml* y actualice la colección ```<properties>``` del archivo pom.xml con el nombre de su registro de Azure Container Registry y la última versión de ```jib-maven-plugin```.
    Nota: Si el nombre de ACR contiene caracteres en mayúsculas, asegúrese de escribirlos en minúsculas.

    ```xml
    <properties>
        <docker.image.prefix>mysqlaksdemoregistry.azurecr.io</docker.image.prefix>
        <jib-maven-plugin.version>3.1.4</jib-maven-plugin.version>
        <java.version>1.8</java.version>
    </properties>
    ```

1. Actualice la colección ```<plugins>``` del archivo *pom.xml* para que haya un elemento ```<plugin>``` que contenga una entrada para el ```jib-maven-plugin```, como se muestra a continuación. Tenga en cuenta que se va a usar una imagen base de Microsoft Container Registry (MCR): ```mcr.microsoft.com/java/jdk:8-zulu-alpine```, que contiene un JDK admitido oficialmente para Azure. En el caso de otras imágenes base de MCR con JDK admitidos oficialmente, consulte [JDK de Java SE](https://hub.docker.com/_/microsoft-java-jdk), [JRE de Java SE](https://hub.docker.com/_/microsoft-java-jre), [JRE sin periféricos de Java SE](https://hub.docker.com/_/microsoft-java-jre-headless) y [JDK de Java SE y Maven](https://hub.docker.com/_/microsoft-java-maven).
    
    ```xml
    <plugin>
        <artifactId>jib-maven-plugin</artifactId>
        <groupId>com.google.cloud.tools</groupId>
        <version>${jib-maven-plugin.version}</version>
        <configuration>
            <from>
                <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
            </from>
            <to>
                <image>${docker.image.prefix}/${project.artifactId}</image>
            </to>
        </configuration>
    </plugin>
    ```

## <a name="build-the-image-and-push-to-acr"></a>Compilación de la imagen e inserción en ACR

En el símbolo del sistema, vaya a la carpeta *springboot-mysql-aks* y ejecute los siguientes comandos para establecer primero el nombre predeterminado para Azure Container Registry (de lo contrario, deberá especificar el nombre en ```az acr login```), compile la imagen y, a continuación, insértela en el registro.

Asegúrese de que el demonio de Docker está activo mientras se ejecuta este paso.

```azurecli-interactive
az config set defaults.acr=mysqlaksdemoregistry
az acr login && mvn compile jib:build
```

## <a name="create-a-kubernetes-cluster-on-aks"></a>Crear un clúster de Kubernetes en AKS

Ahora crearemos un clúster de AKS en la red virtual *vnet-mysqlaksdemo*. 

En este tutorial, usaremos las redes de Azure CNI en AKS. Si en su lugar quiere configurar redes kubenet, consulte [Uso de redes kubenet en AKS](../../aks/configure-kubenet.md#create-a-service-principal-and-assign-permissions).  

1. Cree una subred *subnet-aks* para que la use el clúster de AKS.

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group rg-mysqlaksdemo \
    --vnet-name vnet-mysqlaksdemo \
    --name subnet-aks \
    --address-prefixes 155.55.2.0/24
    ```

1. Obtenga el id. del recurso de subred.

    ```azurecli-interactive
    SUBNET_ID=$(az network vnet subnet show --resource-group rg-mysqlaksdemo --vnet-name vnet-mysqlaksdemo --name subnet-aks --query id -o tsv)
    ```
    
1. Cree un clúster de AKS en la red virtual, con *mysqlaksdemoregistry* de Azure Container Registry (ACR) adjunto.

    ```azurecli-interactive
        az aks create \
        --resource-group rg-mysqlaksdemo \
        --name aks-mysqlaksdemo \
        --network-plugin azure \
        --service-cidr 10.0.0.0/16 \
        --dns-service-ip 10.0.0.10 \
        --docker-bridge-address 172.17.0.1/16 \
        --vnet-subnet-id $SUBNET_ID \
        --attach-acr mysqlaksdemoregistry \
        --dns-name-prefix aks-mysqlaksdemo \
        --generate-ssh-keys
    ```

    Los siguientes intervalos de direcciones IP también se definen como parte del proceso de creación del clúster:
    
    - *--service-cidr* se usa para asignar servicios internos en una dirección IP del clúster de AKS. Puede usar cualquier intervalo de direcciones privado que cumpla los requisitos siguientes:
        - No debe estar dentro del intervalo de direcciones IP de la red virtual del clúster.
        - No deben superponerse con ninguna otra red virtual del mismo nivel que la red virtual del clúster.
        - No debe superponerse con ninguna dirección IP local.
        - No debe estar dentro de los intervalos 169.254.0.0/16, 172.30.0.0/16, 172.31.0.0/16, or 192.0.2.0/24
    
    - La dirección *--dns-service-ip* es la dirección IP del servicio DNS del clúster. Esta dirección debe estar dentro del  *intervalo de direcciones del servicio Kubernetes*. No use la primera dirección IP del intervalo de direcciones. La primera dirección del intervalo de la subred se usa para la dirección *kubernetes.default.svc.cluster.local*.
    
    - La *--docker-bridge-address* es la dirección de red del puente de Docker que representa la dirección de red del puente de *docker0* predeterminada presente en todas las instalaciones de Docker. Debe elegir un espacio de direcciones que no entre en conflicto con el resto de los CIDR de las redes, incluidos el CIDR de servicio del clúster y el CIDR del pod.

## <a name="deploy-the-application-to-aks-cluster"></a>Implementar la aplicación en el clúster de AKS

1. Vaya al recurso de clúster de AKS en Azure Portal.

1. Seleccione **Agregar** y **Agregar con YAML** desde cualquiera de las vistas de recursos (espacio de nombres, cargas de trabajo, servicios y entradas, almacenamiento o configuración). 

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-resource-blade.png" alt-text="Captura de pantalla que muestra la vista de recursos de Azure Kubernetes Service en Azure Portal.":::

1. Pegue el siguiente código YAML. Reemplace los valores de nombre de usuario y contraseña de administrador del servidor flexible de MySQL.

    ```yml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: springboot-mysql-aks
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: springboot-mysql-aks
      template:
        metadata:
          labels:
            app: springboot-mysql-aks
        spec:
          containers:
          - name: springboot-mysql-aks
            image: mysqlaksdemoregistry.azurecr.io/springboot-mysql-aks:latest
            env:
            - name: DATABASE_HOST
              value: "mysql-mysqlaksdemo.mysql.database.azure.com"
            - name: DATABASE_USERNAME
              value: "<your-admin-username>"
            - name: DATABASE_PASSWORD
              value: "<your-admin-password>"
            - name: DATABASE_NAME    
              value: "demo"     
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: springboot-mysql-aks
    spec:
      type: LoadBalancer
      ports:
      - port: 80
        targetPort: 8080
      selector:
        app: springboot-mysql-aks
    ```
1. Seleccione **Agregar** en la parte inferior del editor YAML para implementar la aplicación.

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-add-with-yaml.png" alt-text="Captura de pantalla que muestra Agregar con el editor de YAML.":::

1. Una vez agregado el archivo YAML, el visor de recursos muestra la aplicación de Spring Boot. Anote la dirección IP externa vinculada incluida en el servicio externo.

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-external-ip.png" alt-text="Captura de pantalla que muestra la vista de Azure Portal de la dirección IP externa del servicio de clúster de Azure Kubernetes.":::

## <a name="test-the-application"></a>Prueba de la aplicación

Puede usar cURL para probar la aplicación.

En primer lugar, cree un nuevo elemento "todo" en la base de datos con el siguiente comando.

```bash
curl --header "Content-Type: application/json" \
--request POST \
--data '{"description":"configuration","details":"congratulations, you have deployed your application correctly!","done": "true"}' \
http://<AKS-service-external-ip>
```

A continuación, recupere los datos mediante una nueva solicitud cURL o escriba la *dirección IP externa* del clúster en el explorador.

```bash
curl http://<AKS-service-external-ip>
```

Este comando devolverá la lista de elementos "todo", incluido el elemento que ha creado.

```json
[{"id":1,"description":"configuration","details":"congratulations, you have deployed your application correctly!","done":true}]
```

Esta es una captura de pantalla de estas solicitudes cURL: :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-curl-output.png" alt-text="captura de pantalla que muestra la salida de la línea de comandos de las solicitudes cURL":::

Puede ver una salida similar a través del explorador: :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-browser-output.png" alt-text="captura de pantalla que muestra la salida de la solicitud del explorador.":::


¡Enhorabuena! Ha implementado correctamente una aplicación Spring Boot en un clúster Azure Kubernetes Service (AKS) con el servidor flexible de Azure Database for MySQL en el back-end. 


## <a name="clean-up-the-resources"></a>Limpiar los recursos

Para evitar los cargos de Azure, se recomienda limpiar los recursos que no sean necesarios.  Cuando el clúster ya no se necesite, puede usar el comando [az group delete](/cli/azure/group#az_group_delete) para eliminar el grupo de recursos, el servicio de contenedor y todos los recursos relacionados.

```azurecli-interactive
az group delete --name rg-mysqlaksdemo
```

> [!NOTE]
> Cuando elimina el clúster, la entidad de servicio Azure Active Directory que utiliza el clúster de AKS no se quita. Para conocer los pasos que hay que realizar para quitar la entidad de servicio, consulte [Consideraciones principales y eliminación de AKS](../../aks/kubernetes-service-principal.md#additional-considerations). Si usó una identidad administrada, esta está administrada por la plataforma y no requiere que la quite.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación de una aplicación de WordPress en AKS con MySQL](tutorial-deploy-wordpress-on-aks.md)

> [!div class="nextstepaction"]
> [Compilación de una aplicación web PHP (Laravel) con MySQL](tutorial-php-database-app.md)


