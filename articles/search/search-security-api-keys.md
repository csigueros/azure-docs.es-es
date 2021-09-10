---
title: Autenticación mediante clave de API
titleSuffix: Azure Cognitive Search
description: Las claves de API controlan el acceso de entrada al punto de conexión de servicio. Las claves de administración conceden acceso de escritura. Las claves de consulta se pueden crear para el acceso de solo lectura.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/25/2021
ms.openlocfilehash: f452aa6ababd338ccc86b7c7c40854367ed46e41
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460630"
---
# <a name="use-api-keys-for-azure-cognitive-search-authentication"></a>Uso de claves de API para la autenticación de Azure Cognitive Search

Cognitive Search usa las claves de API como método de autenticación principal. Para las solicitudes entrantes a los servicios de búsqueda, como aquellas que crean o consultan un índice, las claves de API son la única opción de autenticación disponible. Algunos escenarios de solicitudes salientes, sobre todo los que implican indizadores, pueden usar identidades y roles de Azure Active Directory.

Las claves de API se generan cuando se crea el servicio. Pasar una clave de API válida en la solicitud se considera una prueba de que esta última procede de un cliente autorizado. Existen dos tipos de claves. Las *claves de administración* transmiten permisos de escritura en el servicio y también conceden derechos para consultar la información del sistema. Las *claves de consulta* transmiten permisos de lectura y las aplicaciones pueden usarlas para consultar un índice específico. 

> [!NOTE]
> La autorización para las operaciones del plano de datos mediante el control de acceso basado en roles (RBAC) de Azure está ahora en versión preliminar. Puede usar esta funcionalidad en versión preliminar para complementar o reemplazar las claves de API [por roles de Azure para Search](search-security-rbac.md). 

## <a name="using-api-keys-in-search"></a>Uso de las claves de API en la búsqueda

Al conectarse a un servicio de búsqueda, todas la solicitudes deben incluir una clave de API generada de forma específica para el servicio.

+ En las [soluciones de REST](search-get-started-rest.md), la clave de API se especifica normalmente en un encabezado de solicitud.

+ En las [soluciones de .NET](search-howto-dotnet-sdk.md), una clave suele especificarse como un valor de configuración y pasarse como un valor [AzureKeyCredential](/dotnet/api/azure.azurekeycredential).

Puede ver y administrar las claves de API en [Azure Portal](https://portal.azure.com), o a través de [PowerShell](/powershell/module/az.search), la [CLI de Azure](/cli/azure/search)o la [API de REST](/rest/api/searchmanagement/).

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Página del portal, recuperar configuración, sección de claves" border="false":::

## <a name="what-is-an-api-key"></a>¿Qué es una clave de API?

Una clave de API es una cadena única formada por números y letras generados aleatoriamente que se pasan en cada solicitud al servicio de búsqueda. El servicio aceptará la solicitud, si tanto la solicitud como la clave son válidas. 

Se usan dos tipos de claves para obtener acceso al servicio de búsqueda: administración (lectura y escritura) y consulta (solo lectura).

|Clave|Descripción|límites|  
|---------|-----------------|------------|  
|Administración|Concede derechos completos para todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indexadores y orígenes de datos.<br /><br /> Cuando se crea el servicio, se generan dos claves de administración, a las que se hace referencia como claves *principal* y *secundaria* en el portal. También se pueden volver a generar individualmente a petición. Tener dos claves le permite la rotación de una clave mientras se usa la segunda clave para un acceso continuado al servicio.<br /><br /> Las claves de administración solo se especifican en los encabezados de la solicitud HTTP. No se puede colocar una clave de API de administración en una dirección URL.|Máximo 2 por servicio|  
|Consultar|Conceden acceso de solo lectura a índices y documentos y, normalmente, se distribuyen entre las aplicaciones cliente que emiten solicitudes de búsqueda.<br /><br /> Las claves de consulta se crean bajo petición.<br /><br /> Las claves de consulta se pueden especificar en un encabezado de solicitud HTTP para operaciones de búsqueda y sugerencias. Como alternativa, puede pasar una clave de consulta como un parámetro en una dirección URL. En función de cómo formule la solicitud la aplicación cliente, puede resultar más fácil pasar la clave como un parámetro de consulta:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 por servicio|  

 Visualmente, no hay distinción entre las claves de administración y de consulta. Ambas claves son cadenas formadas por 32 caracteres alfanuméricos generados aleatoriamente. Si necesita saber qué tipo de clave se especifica en la aplicación, puede [comprobar los valores de las claves en el portal](https://portal.azure.com).  

> [!NOTE]  
> Se considera una práctica poco segura pasar datos confidenciales, como una `api-key` en el identificador URI de la solicitud. Por este motivo, Azure Cognitive Search solo acepta una clave de consulta como `api-key` en la cadena de consulta, y debe evitar hacerlo a menos que el contenido del índice deba estar disponible públicamente. Como regla general, se recomienda pasar la `api-key` como un encabezado de solicitud.  

## <a name="find-existing-keys"></a>Encontrar las claves existentes

Puede obtener las claves de acceso en el portal o mediante la [PowerShell](/powershell/module/az.search), la [CLI de Azure](/cli/azure/search) o la [API de REST](/rest/api/searchmanagement/).

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Obtenga la lista de los [servicios de búsqueda](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) de su suscripción.
1. Seleccione el servicio y, en la página de información general, haga clic en **Configuración** >**Claves** para ver las claves de administración y de consulta.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Página del portal, ver configuración, sección de claves" border="false":::

## <a name="create-query-keys"></a>Creación de claves de consulta

Las claves de consulta se usan para el acceso de solo lectura a los documentos dentro de un índice para las operaciones destinadas a una colección de documentos. Las consultas de búsqueda, filtro y sugerencias son todas las operaciones que toman una clave de consulta. Cualquier operación de solo lectura que devuelve definiciones de objetos o datos del sistema, como una definición del índice o el estado del indexador, requiere una clave de administración.

Restringir el acceso y las operaciones en las aplicaciones cliente es esencial para proteger los activos de búsqueda en el servicio. Use siempre una clave de consulta en lugar de una clave de administración para cualquier consulta originada desde una aplicación cliente.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Obtenga la lista de los [servicios de búsqueda](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) de su suscripción.
3. Seleccione el servicio y, en la página de información general, haga clic en **Configuración** >**Claves**.
4. Haga clic en **Administrar claves de consulta**.
5. Use la clave de consulta ya generada para el servicio o cree hasta 50 claves de consulta nuevas. La clave de consulta predeterminada no tiene nombre, pero se puede asignar un nombre a las claves de consulta adicionales para facilitar la administración.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Creación o uso de una clave de consulta" border="false":::

> [!Note]
> Un ejemplo de código que muestra el uso de claves de consulta puede encontrarse en [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Regeneración de claves de administración

Se crean dos claves de administración para cada servicio para que pueda rotar una clave principal mediante la clave secundaria para la continuidad empresarial.

1. En la página **Configuración** >**Claves**, copie la clave secundaria.
2. Para todas las aplicaciones, actualice la configuración de la clave de API para usar la clave secundaria.
3. Regenere la clave principal
4. Actualice todas las aplicaciones para usar la nueva clave principal.

Si regenera las claves al mismo tiempo sin querer, se producirá un error HTTP 403 Prohibido en todas las solicitudes de cliente que usen esas claves. Sin embargo, no se elimina el contenido ni se le bloquea permanentemente. 

Todavía puede acceder al servicio a través del portal o mediante programación. Las funciones de administración están operativas a través de un identificador de suscripción, no un servicio de claves de API; por lo tanto, seguirán estando disponibles aunque las claves de API no lo estén. 

Después de crear nuevas claves a través del portal o la capa de administración, se restaura el acceso al contenido (índices, indexadores, orígenes de datos, las asignaciones de sinónimos) una vez que tiene las nuevas claves y las proporciona en las solicitudes.

## <a name="secure-api-keys"></a>Protección de las claves de API

Las [asignaciones de roles](search-security-rbac.md) determinan quiénes puede leer y administrar las claves. Los miembros de los roles siguientes pueden ver y regenerar las claves: Propietario, Colaborador, [Colaboradores de Search Service](../role-based-access-control/built-in-roles.md#search-service-contributor). El rol Lector no tiene acceso a las claves de API.

Los administradores de suscripciones pueden ver y volver a generar todas las claves de API. Como medida de precaución, revise las asignaciones de roles para conocer quién tiene acceso a las claves de administración.

1. Vaya a la página del servicio de búsqueda de Azure Portal.
1. En el panel de navegación izquierdo, seleccione **Control de acceso (IAM)** y después seleccione la pestaña **Asignaciones de rol**.
1. Establezca **Ámbito** en **Este recurso** para ver las asignaciones de roles para el servicio.

## <a name="see-also"></a>Consulte también

+ [Seguridad en Azure Cognitive Search](search-security-overview.md)
+ [Control de acceso basado en roles de Azure en Azure Cognitive Search](search-security-rbac.md)
+ [Administración mediante PowerShell](search-manage-powershell.md) 