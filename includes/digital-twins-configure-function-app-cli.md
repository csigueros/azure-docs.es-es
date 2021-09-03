---
author: baanders
description: 'Archivo de inclusión que describe cómo configurar una función de Azure para que trabaje con Azure Digital Twins: instrucciones de la CLI'
ms.service: digital-twins
ms.topic: include
ms.date: 7/20/2021
ms.author: baanders
ms.openlocfilehash: fffa878ca69b0704417c7db7492eb5523217e4e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722341"
---
Ejecute los comandos siguientes en [Azure Cloud Shell](https://shell.azure.com) o en una [interfaz de la línea de comandos (CLI) local de Azure](/cli/azure/install-azure-cli).

#### <a name="assign-an-access-role"></a>Asignación de un rol de acceso

La función de Azure requiere que se le pase un token de portador. Para asegurarse de que el token de portador se haya pasado, conceda a la aplicación de funciones el rol de **propietario de datos de Azure Digital Twins** para la instancia de Azure Digital Twins, lo que dará permiso a la aplicación de funciones para realizar actividades del plano de datos en la instancia.

[!INCLUDE [digital-twins-permissions-required.md](digital-twins-permissions-required.md)]

1. Use el comando siguiente para ver los detalles de la [identidad administrada por el sistema](../articles/active-directory/managed-identities-azure-resources/overview.md) de la función. Anote el valor del campo `principalId` de la salida. Este identificador se usará para hacer referencia a la función de forma que pueda concederle permisos en el paso siguiente.

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-function-app-name> 
    ```

    >[!NOTE]
    > Si el resultado está vacío en lugar de mostrar los detalles de la identidad, cree una nueva identidad administrada por el sistema para la función con este comando:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-function-app-name>  
    >```
    >
    > La salida muestra los detalles de la identidad, incluido el valor de `principalId` requerido para el siguiente paso. 

1. Use el valor `principalId` en el comando siguiente para asignar a la función el rol de **propietario de datos de Azure Digital Twins** en la instancia de Azure Digital Twins.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

#### <a name="configure-application-settings"></a>Configuración de la aplicación

A continuación, haga que la dirección URL de la **instancia de Azure Digital Twins** sea accesible para la función mediante el establecimiento de una [variable de entorno](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal#use-application-settings) para ella.

> [!TIP]
> Para crear la dirección URL de la instancia de Azure Digital Twins, agregue *https://* al principio del nombre de host de la instancia. Para ver el nombre de host, junto con todas las propiedades de la instancia, ejecute `az dt show --dt-name <your-Azure-Digital-Twins-instance>`.

El comando siguiente establece una variable de entorno para la dirección URL de la instancia que la función usará siempre que necesite acceder a la instancia.

```azurecli-interactive 
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```