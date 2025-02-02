---
title: 'Inicio rápido: Creación de una conexión de servicio en App Service desde Azure Portal'
description: Inicio rápido que muestra cómo crear una conexión de servicio en App Service desde Azure Portal
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fcab4ce8d1f0d4d6f2dd7d29b1de8f9c7519c9f1
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851883"
---
# <a name="quickstart-create-a-service-connection-in-app-service-from-azure-portal"></a>Inicio rápido: Creación de una conexión de servicio en App Service desde Azure Portal

En este inicio rápido, se muestra cómo crear una nueva conexión de servicio con el conector de servicio en App Service desde Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

En este inicio rápido, se da por supuesto que ya tiene al menos una instancia de App Service en ejecución en Azure. Si no tiene una instancia de App Service, [créela](../app-service/quickstart-dotnetcore.md).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com/](https://portal.azure.com/) con su cuenta de Azure.

## <a name="create-a-new-service-connection-in-app-service"></a>Creación de una nueva conexión de servicio en App Service

Para crear una nueva conexión de servicio en App Service, usará el conector de servicio.

1. Seleccione el botón **Todos los recursos** que se encuentra en el lado izquierdo de Azure Portal. Escriba **App Service** en el filtro y haga clic en la lista en el nombre de la instancia de App Service que desea usar.
2. Seleccione **Conector de servicio (versión preliminar)** en la tabla de contenido de la izquierda. Seleccione **Crear**.
3. Seleccione o escriba los siguientes valores.

    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Suscripción** | Una de sus suscripciones | La suscripción en la que se encuentra el servicio de destino (es decir, el servicio al que desea conectarse). El valor predeterminado es la suscripción en la que se encuentra esta instancia de App Service. |
    | **Tipo de servicio** | Blob Storage | El tipo de servicio de destino. Si no tiene un contenedor de Storage Blob, puede [crearlo](../storage/blobs/storage-quickstart-blobs-portal.md) o usar otro tipo de servicio. |
    | **Nombre de la conexión** | El nombre único generado | El nombre de conexión que identifica la conexión entre App Service y el servicio de destino  |
    | **Cuenta de almacenamiento** | Cuenta de almacenamiento | La cuenta de almacenamiento de destino a la que desea conectarse. Si elige otro tipo de servicio, seleccione la instancia del servicio de destino correspondiente. |
    | **Tipo de cliente** | La misma pila de aplicación de esta instancia de App Service | La pila de aplicación que funciona con el servicio de destino que ha seleccionado. El valor predeterminado procede de la pila en tiempo de ejecución de App Service. |

4. Seleccione **Siguiente: Autenticación**  para seleccionar el tipo de autenticación. A continuación, seleccione **Cadena de conexión** para usar la clave de acceso para conectarse a la cuenta de Blob Storage.

5. A continuación, seleccione **Siguiente: Revisar y crear** para revisar la información proporcionada. Después, seleccione **Crear** para crear la conexión de servicio. La operación puede tardar 1 minuto en completarse.

## <a name="view-service-connections-in-app-service"></a>Visualización de conexiones de servicio de App Service

1. En **Conector de servicio (versión preliminar)** , verá una conexión de App Service al servicio de destino.

1. Haga clic en el botón **>** para expandir la lista y ver las variables de entorno que necesita el código de la aplicación.

1. Haga clic en el botón **...** y seleccione **Validar** para ver los detalles de validación de la conexión en la hoja emergente de la derecha.

## <a name="next-steps"></a>Pasos siguientes

Complete los tutoriales que se indican a continuación para empezar a crear su propia aplicación con el conector de servicio.

> [!div class="nextstepaction"]
> [Tutorial: Aplicación web y Storage con la CLI de Azure](./tutorial-csharp-webapp-storage-cli.md)

> [!div class="nextstepaction"]
> [Tutorial: Aplicación web y PostgreSQL con la CLI de Azure](./tutorial-django-webapp-postgres-cli.md)
