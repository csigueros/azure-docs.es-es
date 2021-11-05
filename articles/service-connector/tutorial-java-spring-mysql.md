---
title: 'Tutorial: Implementación de una aplicación Spring Cloud conectada a Azure Database for MySQL con Service Connector'
description: Cree una aplicación de Spring Boot conectada a Azure Database for MySQL con Service Connector.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5da530ff759bca62d8da679d493fe3c8d7360e32
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017425"
---
# <a name="tutorial-deploy-spring-cloud-application-connected-to-azure-database-for-mysql-with-service-connector"></a>Tutorial: Implementación de una aplicación Spring Cloud conectada a Azure Database for MySQL con Service Connector

En este tutorial, completará las siguientes tareas desde Azure Portal o la CLI de Azure. Ambos métodos se explican en los siguientes procedimientos.

> [!div class="checklist"]
> * Aprovisionamiento de una instancia de Azure Spring Cloud
> * Compilación e implementación de aplicaciones en Azure Spring Cloud
> * Integración de Azure Spring Cloud con Azure Database for MySQL con Service Connector

## <a name="1-prerequisites"></a>1. Prerrequisitos

* [Instale JDK 8 u 11](/azure/developer/java/fundamentals/java-jdk-install).
* [Registro para obtener una suscripción a Azure](https://azure.microsoft.com/free/)
* [Instale la versión 2.0.67 de la CLI de Azure, o una versión superior](/cli/azure/install-azure-cli), y la extensión de Azure Spring Cloud mediante el siguiente comando: `az extension add --name spring-cloud`

## <a name="2-provision-an-instance-of-azure-spring-cloud"></a>2. Aprovisionamiento de una instancia de Azure Spring Cloud

En el siguiente procedimiento se usa la extensión de la CLI de Azure para aprovisionar una instancia de Azure Spring Cloud.

1. Actualice la CLI de Azure con la extensión de Azure Spring Cloud.

    ```azurecli
    az extension update --name spring-cloud
    ```

1. Inicie sesión en la CLI de Azure y elija una suscripción activa.

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Prepare un nombre para el servicio Azure Spring Cloud.  El nombre debe tener entre 4 y 32 caracteres, y solo puede contener números, letras minúsculas y guiones.  El primer carácter del nombre del servicio debe ser una letra y el último debe ser una letra o un número.

1. Cree un grupo de recursos que contenga el servicio Azure Spring Cloud.  Cree una instancia del servicio Azure Spring Cloud.

    ```azurecli
    az group create --name ServiceConnector-tutorial-rg
    az spring-cloud create -n <service instance name> -g ServiceConnector-tutorial-rg
    ```

## <a name="3-create-an-azure-database-for-mysql"></a>3. Creación de una instancia de Azure Database for MySQL

En el procedimiento siguiente se usa la extensión de la CLI de Azure para aprovisionar una instancia de Azure Database for MySQL.

1. Instale la extensión [db-up](/cli/azure/ext/db-up/mysql).

```azurecli
az extension add --name db-up
```

Cree un servidor Azure Database for MySQL con el comando siguiente:

```azurecli
az mysql up --resource-group ServiceConnector-tutorial-rg --admin-user <admin-username> --admin-password <admin-password>
```

- Para *\<admin-username>* y *\<admin-password>* , especifique las credenciales para crear un usuario administrador de este servidor MySQL. El nombre de inicio de sesión del administrador no puede ser *azure_superuser*, *azure_pg_admin*, *admin*, *administrator*, *root*, *guest* ni *public*. No puede empezar por *pg_* . La contraseña debe contener entre **8 y 128 caracteres** de tres de las categorías siguientes: letras del alfabeto inglés mayúsculas, letras del alfabeto inglés minúsculas, números (0 a 9) y caracteres no alfanuméricos (por ejemplo, !, $, #, %). La contraseña no puede contener el nombre de usuario.

El servidor se crea con los siguientes valores predeterminados (a menos que los invalide manualmente):

**Configuración** | **Valor predeterminado** | **Descripción**
---|---|---
server-name | Generado por el sistema | Un nombre único que identifica al servidor de Azure Database for MySQL.
sku-name | GP_Gen5_2 | El nombre de la SKU. Sigue la convención {plan de tarifa}\_{generación de procesos}\_{núcleos virtuales} en forma abreviada. El valor predeterminado es un servidor de uso general Gen5 con 2 núcleos virtuales. Consulte nuestra [página de precios](https://azure.microsoft.com/pricing/details/mysql/) para más información acerca de los niveles.
backup-retention | 7 | Cuánto tiempo deben conservarse las copias de seguridad. La unidad es días.
geo-redundant-backup | Disabled | Si se deben habilitar las copias de seguridad con redundancia geográfica en este servidor o no.
ubicación | westus2 | La ubicación de Azure para el servidor.
ssl-enforcement | habilitado | Si SSL debe habilitarse o no en este servidor.
storage-size | 5120 | La capacidad de almacenamiento del servidor (la unidad es megabytes).
version | 5.7 | La versión principal de MySQL.


> [!NOTE]
> Para más información acerca del comando `az mysql up` y sus parámetros adicionales, consulte la [documentación de la CLI de Azure](/cli/azure/mysql#az_mysql_up).

Una vez que se crea el servidor, incluye la siguiente configuración:

- Se crea una regla de firewall denominada "devbox". La CLI de Azure intenta detectar la dirección IP de la máquina desde la que se ejecuta el comando `az mysql up` y permite esa dirección IP.
- "Permitir el acceso a servicios de Azure" está activado. Este valor configura el firewall del servidor para que acepte conexiones de todos los recursos de Azure, incluidos aquellos que no están en la suscripción.
- El parámetro `wait_timeout` se establece en 8 horas.
- Se crea una base de datos vacía denominada `sampledb`.
- Se crea un usuario denominado "root" con privilegios para `sampledb`.

## <a name="4-build-and-deploy-the-app"></a>4. Creación e implementación de la aplicación

1. Cree la aplicación con el punto de conexión público asignado. Si seleccionó la versión 11 de Java al generar el proyecto de Spring Cloud, incluya el modificador `--runtime-version=Java_11`.

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g ServiceConnector-tutorial-rg --assign-endpoint true
    ```

1. Cree conexiones de servicio entre Spring Cloud y la base de datos MySQL.

    ```azurecli
    az spring-cloud connection create mysql
    ```

1. Clonación del código de ejemplo

    ```bash
    git clone https://github.com/Azure-Samples/serviceconnector-springcloud-mysql-springboot.git
    ```

1. Cree el proyecto mediante Maven.

    ```bash
    cd serviceconnector-springcloud-mysql-springboot
    mvn clean package -DskipTests 
    ```

1. Implemente el archivo .jar para la aplicación (`target/demo-0.0.1-SNAPSHOT.jar`).

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g ServiceConnector-tutorial-rg  --artifact-path target/demo-0.0.1-SNAPSHOT.jar
    ```

1. Consulte el estado de la aplicación después de las implementaciones con el siguiente comando.

    ```azurecli
    az spring-cloud app list -o table
    ```

    El resultado debe ser parecido al siguiente.

    ```
    Name               Location    ResourceGroup    Production Deployment    Public Url                                           Provisioning Status    CPU    Memory    Running Instance    Registered Instance    Persistent Storage
    -----------------  ----------  ---------------  -----------------------  ---------------------------------------------------  ---------------------  -----  --------  ------------------  ---------------------  --------------------
    hellospring         eastus    <resource group>   default                                                                       Succeeded              1      2         1/1                 0/1                    -
    ```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
