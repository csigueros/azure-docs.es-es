---
title: 'Tutorial: uso de Acciones de GitHub para implementar en App Service y conectarse a una base de datos'
description: Implementación de una aplicación de ASP.NET Core con un back-end de base de datos en Azure con Acciones de GitHub
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jukullam
ms.custom: github-actions-azure
ms.openlocfilehash: 01d4293b33eb0756dec8bc9ae870a972959ced89
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128702188"
---
# <a name="tutorial-use-github-actions-to-deploy-to-app-service-and-connect-to-a-database"></a>Tutorial: uso de Acciones de GitHub para implementar en App Service y conectarse a una base de datos

Aprenda a configurar un flujo de trabajo de Acciones de GitHub para implementar una aplicación ASP.NET Core con un back-end de [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md). Cuando haya terminado, tendrá una aplicación ASP.NET en ejecución en Azure y conectada a SQL Database. Primero usará una [plantilla de ARM](/azure/azure-resource-manager/templates/overview) para crear recursos.

En este tutorial no se usan los contenedores. Si quiere implementar contenido en una aplicación contenedorizada de ASP.NET Core, consulte [Uso de las Acciones de GitHub para implementar contenido en App Service para contenedores conectarse a una base de datos ](app-service-sql-github-actions.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
>
> - Usar un flujo de trabajo de Acciones de GitHub para agregar recursos a Azure con una plantilla de Azure Resource Manager (plantilla ARM).
> - Use un flujo de trabajo de Acciones de GitHub para crear una aplicación ASP.NET Core

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una cuenta de GitHub. Si no tiene ninguna, regístrese [gratis](https://github.com/join).
  - Un repositorio de GitHub para almacenar las plantillas de Resource Manager y los archivos del flujo de trabajo. Para crear uno, vea [Creación de un repositorio](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository).

## <a name="download-the-sample"></a>Descarga del ejemplo

[Bifurque el proyecto de ejemplo](https://github.com/Azure-Samples/dotnetcore-sqldb-ghactions) en el repositorio de muestras de Azure.

```
https://github.com/Azure-Samples/dotnetcore-sqldb-ghactions
```

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

Abra Azure Cloud Shell en https://shell.azure.com. También puede usar la CLI de Azure si la ha instalado localmente. (Para obtener más información sobre Cloud Shell, consulte el artículo de [información general de Cloud Shell](../cloud-shell/overview.md)).

```azurecli-interactive
az group create --name {resource-group-name} --location {resource-group-location}
```

## <a name="generate-deployment-credentials"></a>Genere las credenciales de implementación.

Deberá autenticarse con una entidad de servicio para que funcione el script de implementación de recursos. Puede crear una [entidad de servicio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) mediante el comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) de la [CLI de Azure](/cli/azure/). Puede ejecutar este comando mediante [Azure Cloud Shell](https://shell.azure.com/) en Azure Portal o haciendo clic en el botón **Probar**.

```azurecli-interactive
    az ad sp create-for-rbac --name "{service-principal-name}" --sdk-auth --role contributor --scopes /subscriptions/{subscription-id}
```

En este ejemplo, reemplace los marcadores de posición por su id. de suscripción, el nombre del grupo de recursos y el nombre de la entidad de servicio. La salida es un objeto JSON con las credenciales de asignación de roles que proporcionan acceso a la aplicación de App Service. Copie este objeto JSON para más adelante. Para obtener ayuda, consulte la [configuración de las credenciales de implementación](https://github.com/Azure/login#configure-deployment-credentials).

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret-for-authentication"></a>Configuración del secreto de GitHub para la autenticación

En [GitHub](https://github.com/), examine el repositorio y seleccione **Configuración > Secretos > Agregar un nuevo secreto**.

Para utilizar las [credenciales de nivel de usuario](#generate-deployment-credentials), pegue toda la salida JSON del comando CLI de Azure en el campo de valor del secreto. Asigne al secreto el siguiente nombre: `AZURE_CREDENTIALS`.

## <a name="add-github-secrets-for-your-build"></a>Incorporación de secretos de GitHub a la compilación

1. Cree [dos nuevos secretos](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository) en el repositorio de GitHub para `SQLADMIN_PASS` y `SQLADMIN_LOGIN`. Asegúrese de elegir una contraseña compleja; de lo contrario, se producirá un error en el paso de creación del servidor de base de datos de SQL. Tenga en cuenta que no podrá volver a acceder a esta contraseña, así que guárdela por separado.

2. Cree un secreto `AZURE_SUBSCRIPTION_ID` para el id. de suscripción de Azure. Si no conoce el id. de suscripción, use este comando en Azure Shell para encontrarlo. Copie el valor en la columna `SubscriptionId`.
    ```azurecli
    az account list -o table
    ```
 
## <a name="create-azure-resources"></a>Creación de recursos de Azure

El flujo de trabajo de creación de recursos de Azure ejecuta una [plantilla de ARM](/azure/azure-resource-manager/templates/overview) para implementar recursos en Azure. El flujo de trabajo:

- Restaura el código fuente con la [acción de restauración](https://github.com/marketplace/actions/checkout).
- Inicia sesión en Azure con la [acción de inicio de sesión de Azure](https://github.com/marketplace/actions/azure-login) y recopila información del entorno y los recursos de Azure.
- Implementa recursos con la [acción para implementar Azure Resource Manager](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template).

Para ejecutar el flujo de trabajo de creación de recursos de Azure:

1. Abra el archivo `infraworkflow.yml` en `.github/workflows` en el repositorio.

1. Actualice el valor de `AZURE_RESOURCE_GROUP` al nombre de su grupo de recursos.

1. Establezca la entrada de `region` en las acciones de implementación de ARM de su región. 
    1. Abra `templates/azuredeploy.resourcegroup.parameters.json` y actualice la propiedad `rgLocation` en su región.
 
1. Vaya a **Acciones** y seleccione **Ejecutar flujo de trabajo**.

   :::image type="content" source="media/github-actions-workflows/github-actions-run-workflow.png" alt-text="Ejecute el flujo de trabajo de Acciones de GitHub para agregar recursos.":::

1. Compruebe que su acción se haya ejecutado correctamente; para ello, compruebe si hay una marca de verificación verde en la página **Acciones**.

   :::image type="content" source="media/github-actions-workflows/create-resources-success.png" alt-text="Ejecución exitosa de creación de recursos. ":::

1. Una vez que haya creado los recursos, vaya a **Acciones**, seleccione Crear recursos de Azure y deshabilite el flujo de trabajo. 
 
    :::image type="content" source="media/github-actions-workflows/disable-workflow-github-actions.png" alt-text="Deshabilite el flujo de trabajo de creación de recursos de Azure.":::

## <a name="create-a-publish-profile-secret"></a>Creación de un secreto de perfil de publicación

1. En Azure Portal, abra la nueva instancia de almacenamiento provisional de App Service (ranura) que creó con el flujo de trabajo `Create Azure Resources`.

1. Seleccione **Obtener perfil de publicación**.

1. Abra el archivo del perfil de publicación en un editor de texto y copie el contenido. 

1. Cree un nuevo secreto de GitHub para `AZURE_WEBAPP_PUBLISH_PROFILE`. 

## <a name="build-and-deploy-your-app"></a>Compilación e implementación de la aplicación

Para ejecutar la compilación e implementar el flujo de trabajo:

1. Abra el archivo `workflow.yaml` en `.github/workflows` en el repositorio.

1. Compruebe que las variables de entorno de `AZURE_RESOURCE_GROUP`, `AZURE_WEBAPP_NAME`, `SQLSERVER_NAME` y `DATABASE_NAME` coinciden con las de `infraworkflow.yml`.

1. Compruebe que la aplicación se implementó visitando la dirección URL de la opción de salida Cambiar al espacio de producción. Debería ver una aplicación de ejemplo, denominada Mi aplicación TodoList. 
 
## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita el proyecto de ejemplo, elimine el grupo de recursos de Azure Portal y elimine el repositorio en GitHub. 

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre la integración de Azure y GitHub](/azure/developer/github/)
