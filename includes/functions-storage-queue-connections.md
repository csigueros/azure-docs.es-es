---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 965a2abb8a56894efc700cea75097526daf4e544
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132530256"
---
## <a name="connections"></a>Conexiones

La propiedad `connection` es una referencia a la configuración del entorno que especifica cómo se debe conectar la aplicación a las colas de Azure. Puede especificar lo siguiente:

- El nombre de una configuración de la aplicación que contiene una [cadena de conexión](#connection-string).
- El nombre de un prefijo compartido para varias configuraciones de la aplicación que juntas definen una [conexión basada en identidad](#identity-based-connections).

Si el valor configurado es tanto una coincidencia exacta de una única configuración como una coincidencia de prefijo de otras configuraciones, se usa la coincidencia exacta.

### <a name="connection-string"></a>Cadena de conexión

Para obtener una cadena de conexión, siga los pasos mostrados en [Administración de las claves de acceso de la cuenta de almacenamiento](../articles/storage/common/storage-account-keys-manage.md).

La cadena de conexión debe almacenarse en una configuración de la aplicación con un nombre que coincida con el valor especificado por la propiedad `connection` de la configuración de enlace.

Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre aquí. Por ejemplo, si establece `connection` en "MyStorage", el entorno en tiempo de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyStorage". Si deja `connection` vacía, el entorno en tiempo de ejecución de Functions usa la cadena de conexión de almacenamiento predeterminada en la configuración de aplicación que se denomina `AzureWebJobsStorage`.

### <a name="identity-based-connections"></a>Conexiones basadas en identidades

Si usa la [versión 5.x o posterior de la extensión](../articles/azure-functions/functions-bindings-storage-queue.md#storage-extension-5x-and-higher), en lugar de usar una cadena de conexión con un secreto, puede hacer que la aplicación use una [identidad de Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md). Para ello, definiría la configuración con un prefijo común que se asigne a la propiedad `connection` en la configuración de desencadenador y enlace.

En este modo, la extensión requiere las siguientes propiedades:

| Propiedad                  | Plantilla de variable de entorno                       |Descripción                                | Valor de ejemplo |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| URI del servicio Queue | `<CONNECTION_NAME_PREFIX>__queueServiceUri`<sup>1</sup>  | Uri del plano de datos del servicio de cola al que se conecta, mediante el esquema HTTPS. | https://<storage_account_name>.queue.core.windows.net |

<sup>1</sup> `<CONNECTION_NAME_PREFIX>__serviceUri` se puede usar como alias. Si se proporcionan ambos formularios, se usará el formulario `queueServiceUri`. El formulario `serviceUri` no se puede usar cuando la configuración de conexión general se va a usar en blobs, colas o tablas.

Se pueden establecer propiedades adicionales para personalizar la conexión. Consulte [Propiedades comunes para conexiones basadas en identidades](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-queue-permissions](./functions-queue-permissions.md)]