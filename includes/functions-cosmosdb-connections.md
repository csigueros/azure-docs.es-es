---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 11/12/2021
ms.author: mahender
ms.openlocfilehash: 4a78d64e2a66f5d3025407b6d7d7907d085b1fe6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529840"
---
## <a name="connections"></a>Conexiones

Las propiedades `connectionStringSetting`/`connection` y `leaseConnectionStringSetting`/`leaseConnection` son referencias a la configuración del entorno que especifica cómo se debe conectar la aplicación a Azure Cosmos DB. Pueden especificar:

- El nombre de una configuración de la aplicación que contiene una [cadena de conexión](#connection-string)
- El nombre de un prefijo compartido para varias configuraciones de la aplicación que juntas definen una [conexión basada en identidad](#identity-based-connections). Esta opción solo está disponible para las versiones `connection` y `leaseConnection` de la [versión 4.x o posterior de la extensión].

Si el valor configurado es tanto una coincidencia exacta de una única configuración como una coincidencia de prefijo de otras configuraciones, se usa la coincidencia exacta.

### <a name="connection-string"></a>Cadena de conexión

La cadena de conexión de la cuenta de base de datos debe almacenarse en una configuración de la aplicación con un nombre que coincida con el valor especificado por la propiedad de conexión de la configuración de enlace.

### <a name="identity-based-connections"></a>Conexiones basadas en identidades

Si usa la [versión 4.x o posterior de la extensión], en lugar de usar una cadena de conexión con un secreto, puede hacer que la aplicación use una [identidad de Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md). Para ello, defina la configuración con un prefijo común que se asigne a la propiedad de conexión en la configuración de desencadenador y enlace.

En este modo, la extensión requiere las siguientes propiedades:

| Propiedad                  | Plantilla de variable de entorno                       | Descripción                                | Valor de ejemplo                                        |
|---------------------------|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| Punto de conexión de la cuenta | `<CONNECTION_NAME_PREFIX>__accountEndpoint` | El identificador URI del punto de conexión de la cuenta de Azure Cosmos DB. | https://<nombre_de_cuenta_de_base_de_dats>.documents.azure.com:443/ |

Se pueden establecer propiedades adicionales para personalizar la conexión. Vea [Propiedades comunes para conexiones basadas en identidades](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-cosmos-permissions](./functions-cosmos-permissions.md)]

[versión 4.x o posterior de la extensión]: ../articles/azure-functions/functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher
