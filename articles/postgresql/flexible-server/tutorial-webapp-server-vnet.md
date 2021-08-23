---
title: 'Tutorial: Creación de un servidor flexible de Azure Database for PostgreSQL y una aplicación web de Azure App Service en la misma red virtual'
description: Guía de inicio rápido para crear un servidor flexible de Azure Database for PostgreSQL con una aplicación web en una red virtual
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/30/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 58e7357552be9c209dec81cc9ace23fcfd82ea2d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467141"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>Tutorial: Creación de un servidor flexible de Azure Database for PostgreSQL y una aplicación web de App Services en la misma red virtual

> [!IMPORTANT]
> El servidor flexible de Azure Database for PostgreSQL se encuentra en versión preliminar

En este tutorial se muestra cómo crear una aplicación web de Azure App Service con el servidor flexible (versión preliminar) de Azure Database for PostgreSQL dentro de una [red virtual](../../virtual-network/virtual-networks-overview.md).

En este tutorial, aprenderá a:
>[!div class="checklist"]
> * Creación de un servidor flexible de PostgreSQL en una red virtual
> * Creación de una aplicación web
> * Adición de una aplicación web a la red virtual
> * Conexión a Postgres desde la aplicación web 

## <a name="prerequisites"></a>Requisitos previos

- Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.
- [Instale la versión 2.0 o posterior de la CLI de Azure](/cli/azure/install-azure-cli) de forma local. Para ver la versión instalada, ejecute el comando `az --version`. 
- Inicie sesión en la cuenta mediante el comando [az login](/cli/azure/authenticate-azure-cli). Anote la propiedad **id** de la salida del comando para el nombre de la suscripción correspondiente.

  ```azurecli
  az login
  ```
- Si tiene varias suscripciones, elija la suscripción adecuada en la que se debe facturar el recurso. Seleccione el identificador de suscripción específico en su cuenta mediante el comando [az account set](/cli/azure/account).

  ```azurecli
  az account set --subscription <subscription ID>
  ```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Creación de un servidor flexible de PostgreSQL en una nueva red virtual

Cree un servidor flexible privado dentro de una red virtual (VNet) con el siguiente comando:

```azurecli
az postgres flexible-server create --resource-group demoresourcegroup --name demoserverpostgres --vnet demoappvnet --location westus2
```
Este comando realiza las siguientes acciones, que pueden tardar algunos minutos:

- Cree el grupo de recursos si todavía no existe.
- Genera un nombre de servidor, si no se proporciona alguno.
- Cree una red virtual para el nuevo servidor PostgreSQL y la subred dentro de esta red virtual para el servidor de bases de datos.
- Crea un nombre de usuario administrador y una contraseña para el servidor, si no se proporciona alguna.
- Crea una base de datos vacía llamada **postgres**.

Esta es la salida de ejemplo.

```json
Local context is turned on. Its information is saved in working directory /home/jane. You can run `az local-context off` to turn it off.
Command argument values from local context: --resource-group demoresourcegroup, --location: eastus
Command group 'postgres flexible-server' is in preview. It may be changed/removed in a future release.
Checking the existence of the resource group ''...
Creating Resource group 'demoresourcegroup ' ...
Creating new vnet "demoappvnet" in resource group "demoresourcegroup" ...
Creating new subnet "Subnet095447391" in resource group "demoresourcegroup " and delegating it to "Microsoft.DBforPostgreSQL/flexibleServers"...
Creating PostgreSQL Server 'demoserverpostgres' in group 'demoresourcegroup'...
Your server 'demoserverpostgres' is using sku 'Standard_D2s_v3' (Paid Tier). Please refer to https://aka.ms/postgres-pricing for pricing details
Make a note of your password. If you forget, you would have to resetyour password with 'az postgres flexible-server update -n demoserverpostgres --resource-group demoresourcegroup -p <new-password>'.
{
  "connectionString": "postgresql://generated-username:generated-password@demoserverpostgres.postgres.database.azure.com/postgres?sslmode=require",
  "host": "demoserverpostgres.postgres.database.azure.com",
  "id": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/demoresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/demoserverpostgres",
  "location": "East US",
  "password": "generated-password",
  "resourceGroup": "demoresourcegroup",
  "skuname": "Standard_D2s_v3",
  "subnetId": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/demoresourcegroup/providers/Microsoft.Network/virtualNetworks/VNET095447391/subnets/Subnet095447391",
  "username": "generated-username",
  "version": "12"
}
```

## <a name="create-a-web-app"></a>Creación de una aplicación web
En esta sección, creará un host de aplicación en la aplicación de App Service, conectará esta aplicación a la base de datos de Postgres y, a continuación, implementará el código en ese host. Asegúrese de que se encuentra en la raíz del repositorio correspondiente al código de la aplicación en el terminal. Tenga en cuenta que el plan Básico no es compatible con Integración con red virtual. Utilice el nivel Estándar o Premium. 

Creación de una aplicación de App Service (el proceso de host) con el comando az webapp up

```azurecli
az webapp up --resource-group demoresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - Para el argumento --location, use la misma ubicación que usó para la base de datos en la sección anterior.
> - Reemplace <app-name> por un nombre que sea único en Azure. Los caracteres permitidos para <app-name> son A-Z, 0-9 y -. Un buen patrón es usar una combinación del nombre de la empresa y un identificador de la aplicación.

Este comando realiza las siguientes acciones, que pueden tardar algunos minutos:

- Cree el grupo de recursos si todavía no existe. (En este comando se usa el mismo grupo de recursos en el que se creó la base de datos anterior).
- Cree la aplicación de App Service, si no existe.
- Habilite el registro predeterminado de la aplicación, si aún no está habilitado.
- Cargue el repositorio mediante la implementación del archivo ZIP con la automatización de compilación habilitada.

### <a name="create-subnet-for-web-app"></a>Creación de una subred para Web Apps
Antes de habilitar la integración con la red virtual, debe tener una subred que esté delegada en App Service Web Apps. Antes de crear la subred, vea la dirección de la subred de la base de datos para evitar el uso del mismo prefijo de dirección para la subred de la aplicación web. 

```azurecli
az network vnet show --resource-group demoresourcegroup -n demoappvnet
```

Ejecute el siguiente comando para crear una nueva subred en la misma red virtual en la que se haya creado el servidor de bases de datos. **Actualice el prefijo de dirección para evitar conflictos con la subred de la base de datos.**

```azurecli
az network vnet subnet create --resource-group demoresourcegroup --vnet-name demoappvnet --name webappsubnet  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms
```

## <a name="add-the-web-app-to-the-virtual-network"></a>Adición de una aplicación web a la red virtual
Use el comando [az webapp vnet-integration](/cli/azure/webapp/vnet-integration) para agregar una integración de red virtual regional a una aplicación web. 

```azurecli
az webapp vnet-integration add --resource-group demoresourcegroup -n  mywebapp --vnet demoappvnet --subnet webappsubnet
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Configuración de las variables de entorno para conectarse a la base de datos
Con el código ahora implementado en App Service, el paso siguiente consiste en conectar la aplicación al servidor flexible en Azure. El código de la aplicación espera encontrar información de la base de datos en una serie de variables de entorno. Para establecer variables de entorno en App Service, use el comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set).

  
```azurecli
  
az webapp config appsettings set  --name mywebapp --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>" 
```
- Reemplace **postgres-server-name**,**username** y **password** por el comando del servidor flexible recién creado.
- Reemplace **<username>** y **<password>** por las credenciales que el comando también ha generado automáticamente.
- El grupo de recursos y el nombre de la aplicación se extraen de los valores almacenados en caché del archivo .azure/config.
- El comando crea valores de configuración denominados **DBHOST**, **DBNAME**, **DBUSER** y *DBPASS**. Si el código de la aplicación usa un nombre diferente para la información de la base de datos, use esos nombres para los valores de la aplicación, tal como se mencionó en el código.

Configure la aplicación web para permitir todas las conexiones salientes desde dentro de la red virtual.
```azurecli
az webapp config set --name mywebapp --resource-group demoresourcegroup --generic-configurations '{"vnetRouteAllEnabled": true}'
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Limpie todos los recursos que creó en el tutorial mediante el siguiente comando. Este comando elimina todos los recursos del grupo de recursos.

```azurecli
az group delete -n demoresourcegroup
```

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Asignación de un nombre DNS personalizado existente a Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
