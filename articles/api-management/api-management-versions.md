---
title: Versiones de Azure API Management | Microsoft Docs
description: Información acerca del concepto de versiones de Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 02/10/2021
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 6b055cb6771dc76557c61220a52120a5fbbd7940
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129811373"
---
# <a name="versions-in-azure-api-management"></a>Versiones de Azure API Management

Las versiones le permiten presentar grupos de API relacionadas a sus desarrolladores. Puede usar versiones para controlar los cambios importantes con seguridad en la API. Los clientes pueden optar por usar la nueva versión de la API cuando está lista, mientras que los clientes existentes siguen usando una versión anterior. Las versiones se diferencian a través de un identificador de versión (que es cualquier valor de cadena que elija), y un esquema de control de versiones permite a los clientes identificar la versión de una API que quieren usar.

En la mayoría de los casos, cada versión de la API se puede considerar como su propia API independiente. Dos versiones de API diferentes pueden tener conjuntos de operaciones y directivas diferentes.

Las versiones le permiten lo siguiente:

- Publicar varias versiones de la API al mismo tiempo.
- Use una ruta de acceso, una cadena de consulta o un encabezado para diferenciar entre las versiones.
- Use cualquier valor de cadena que desee para identificar la versión, que puede ser un número, una fecha o un nombre.
- Muestre las versiones de API agrupadas en el portal para desarrolladores.
- Tome una API existente (sin versión) y cree una nueva versión sin interrumpir a los clientes existentes.

[Para empezar a usar las versiones, siga nuestro tutorial.](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>Esquemas de control de versiones

Diferentes desarrolladores de API tienen requisitos de control de versiones diferentes. Azure API Management no prescribe un enfoque único para el control de versiones, sino que proporciona varias opciones.

### <a name="path-based-versioning"></a>Control de versiones basado en rutas de acceso

Cuando se usa el esquema de control de versiones de ruta de acceso, el identificador de versión debe incluirse en la ruta de acceso de la dirección URL de cualquier solicitud de API.

Por ejemplo, `https://apis.contoso.com/products/v1` y `https://apis.contoso.com/products/v2` podrían hacer referencia a la misma API `products`, pero a las versiones `v1` y `v2` respectivamente.

El formato de una dirección URL de solicitud de API cuando se usa el control de versiones basado en rutas de acceso es: `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}`.

### <a name="header-based-versioning"></a>Control de versiones basado en encabezados

Cuando se usa el esquema de control de versiones de encabezado, el identificador de la versión debe incluirse en un encabezado de solicitud HTTP para todas las solicitudes de API. Puede especificar el nombre del encabezado de solicitud HTTP.

Por ejemplo, puede crear un encabezado personalizado denominado `Api-Version`, y los clientes pueden especificar `v1` o `v2` en el valor de este encabezado.

### <a name="query-string-based-versioning"></a>Control de versiones basado en cadenas de consulta

Cuando se usa el esquema de control de versiones de cadena de consulta, el identificador de la versión debe incluirse en un parámetro de cadena de consulta para todas las solicitudes de API. Puede especificar el nombre del parámetro de la cadena de consulta.

El formato de una dirección URL de solicitud de API cuando se usa el control de versiones basado en cadenas de consulta es: `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}`.

Por ejemplo, `https://apis.contoso.com/products?api-version=v1` y `https://apis.contoso.com/products/api-version=v2` podrían hacer referencia a la misma API `products`, pero a las versiones `v1` y `v2` respectivamente.

## <a name="original-versions"></a>Versiones originales

Si agrega una versión a una API sin versión, se creará automáticamente una versión `Original` y responderá a la dirección URL predeterminada, sin un identificador de versión especificado. La versión `Original` garantiza que los autores de llamada no sufran ninguna interrupción a causa del proceso de adición de la versión. Si crea una API con las versiones habilitadas desde el principio, no se crea una versión `Original`.

## <a name="how-versions-are-represented"></a>Cómo se representan las versiones

Azure API Management mantiene un recurso denominado *conjunto de versiones*, que representa un conjunto de versiones para una única API lógica. Cualquier conjunto de versiones contiene el nombre para mostrar de la API con versión y el [esquema de control de versiones utilizado](#versioning-schemes) para dirigir las solicitudes a las versiones especificadas.

Cada versión de una API se mantiene como su propio recurso de API, que después se asocia a un conjunto de versiones. Un conjunto de versiones puede contener varias API con distintas operaciones o directivas. Puede realizar cambios significativos entre las versiones de un conjunto.

Azure Portal crea conjuntos de versiones de forma automática, y tanto los nombres como las descripciones de estos conjuntos se pueden modificar también en Azure Portal.

Los conjuntos de versiones se puede ver y administrar directamente mediante la [CLI de Azure](/cli/azure/apim/api/versionset), [Azure PowerShell](/powershell/module/az.apimanagement/#api-management), las [plantillas de Resource Manager](/azure/templates/microsoft.apimanagement/service/apiversionsets) o [Azure Resource Manager API](/rest/api/apimanagement/2020-12-01/api-version-set).

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>Migración de una API sin versión a una API con versión

Cuando se usa Azure Portal para habilitar el control de versiones en una API existente, se realizan los siguientes cambios en los recursos de API Management:

 * Se crea un nuevo conjunto de versiones.
 * La versión existente se mantiene y [se configura como la versión de la API `Original`](#original-versions). La API se vincula al conjunto de versiones, pero no requiere que se especifique un identificador de versión.
 * La nueva versión se crea como una nueva API y se vincula al conjunto de versiones. Es necesario acceder a esta nueva API mediante el identificador y el esquema de control de versiones.

## <a name="versions-and-revisions"></a>Versiones y revisiones

Las versiones y las revisiones son características distintas. Cada versión puede tener varias revisiones, como si fuera una API sin versión. Se pueden usar revisiones sin utilizar versiones y viceversa. Normalmente, las versiones se usan para diferenciar aquellas versiones de API que tienen cambios importantes, mientras que las revisiones se pueden usar para cambios menores y no importantes en una API.

Si observa que su revisión tiene cambios importantes o desea convertir formalmente una revisión en una versión beta o de prueba, puede crear una versión a partir de una revisión. En Azure Portal, haga clic en "Crear versión a partir de esta revisión" en el menú contextual de la revisión en la pestaña Revisiones.

## <a name="developer-portal"></a>Portal para desarrolladores

En el [portal para desarrolladores](./api-management-howto-developer-portal.md) se enumera cada versión de una API por separado.

![Portal para desarrolladores de API Management con una lista de las API con versión](media/api-management-versions/portal-list.png)

Los detalles de una API también muestran una lista de todas las versiones de esta. Una versión `Original` se muestra sin un identificador de versión.

![Portal para desarrolladores de API Management con los detalles de una API y una lista de las versiones de dicha API](media/api-management-versions/portal-details.png)

> [!TIP]
> Las versiones de la API deben agregarse a un producto antes de mostrarse en el portal para desarrolladores.
