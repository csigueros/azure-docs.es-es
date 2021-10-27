---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 288539542c5b811206bd3e128dbceca317b2a27a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130137858"
---
## <a name="connections"></a>Conexiones

La propiedad `connection` es una referencia a la configuración del entorno que especifica cómo se debe conectar la aplicación a Service Bus. Puede especificar lo siguiente:

- El nombre de una configuración de la aplicación que contiene una [cadena de conexión](#connection-string).
- El nombre de un prefijo compartido para varias configuraciones de la aplicación que juntas definen una [conexión basada en identidad](#identity-based-connections).

Si el valor configurado es tanto una coincidencia exacta de una única configuración como una coincidencia de prefijo de otras configuraciones, se usa la coincidencia exacta.

### <a name="connection-string"></a>Cadena de conexión

Para obtener la cadena de conexión, siga los pasos mostrados en [Obtención de las credenciales de administración](../articles/service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). La cadena de conexión debe ser para un espacio de nombres de Service Bus y no estar limitada a una cola o un tema concretos.

La cadena de conexión debe almacenarse en una configuración de la aplicación con un nombre que coincida con el valor especificado por la propiedad `connection` de la configuración de enlace.

Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre. Por ejemplo, si establece `connection` en "MyServiceBus", el entorno de ejecución de Functions busca una configuración de aplicación llamada "AzureWebJobsMyServiceBus". Si deja el valor de `connection` vacío, el entorno de ejecución de Functions usa la cadena de conexión de Service Bus predeterminada en la configuración de aplicación que se denomina "AzureWebJobsServiceBus".

### <a name="identity-based-connections"></a>Conexiones basadas en identidades

Si usa la [versión 5.x o posterior de la extensión](../articles/azure-functions/functions-bindings-service-bus.md#service-bus-extension-5x-and-higher), en lugar de usar una cadena de conexión con un secreto, puede hacer que la aplicación use una [identidad de Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md). Para ello, definiría la configuración con un prefijo común que se asigne a la propiedad `connection` en la configuración de desencadenador y enlace.

En este modo, la extensión requiere las siguientes propiedades:

| Propiedad                  | Plantilla de variable de entorno                       | Descripción                                | Valor de ejemplo |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| Espacio de nombres completo | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | Espacio de nombres completo de Service Bus. | <service_bus_namespace>.servicebus.windows.net  |

Se pueden establecer propiedades adicionales para personalizar la conexión. Consulte [Propiedades comunes para conexiones basadas en identidades](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-service-bus-permissions](./functions-service-bus-permissions.md)]