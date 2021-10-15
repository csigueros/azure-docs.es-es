---
title: Autorización basada en roles
titleSuffix: Azure Cognitive Search
description: Use el control de acceso basado en roles de Azure (Azure RBAC) para permisos pormenorizados en tareas de administración de servicios y con contenido.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 1968bb34d124fa37a51b296071ee24b3eae47772
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273730"
---
# <a name="use-role-based-authorization-in-azure-cognitive-search"></a>Uso de la autorización basada en roles en Azure Cognitive Search

Azure proporciona un [sistema de autorización de control de acceso basado en roles (RBAC)](../role-based-access-control/role-assignments-portal.md) global para todos los servicios que se ejecutan en la plataforma. En Cognitive Search, puede usar roles de las siguientes maneras:

+ Use roles disponibles con carácter general para la administración de servicios.

+ Use nuevos roles de versión preliminar para la administración de contenido (creación y administración de índices y otros objetos de nivel superior), [**disponibles mediante solicitud**](https://aka.ms/azure-cognitive-search/rbac-preview).

> [!NOTe]
> Colaborador de servicio de búsqueda es un rol "disponible con carácter general", con funcionalidades en "versión preliminar". Es el único rol que admite un verdadero híbrido de tareas de administración de contenido y servicios, por lo que permite todas las operaciones en un servicio de búsqueda determinado. Para tener acceso a las funcionalidades en versión preliminar de la administración de contenido en este rol, [**regístrese para obtener dicha versión**](https://aka.ms/azure-cognitive-search/rbac-preview).

Algunos escenarios de RBAC **no** se admiten o no se tratan en este artículo:

+ Las conexiones de indexador salientes se documentan en [Configuración de una conexión de indexador a un origen de datos mediante una identidad administrada](search-howto-managed-identities-data-sources.md). En el caso de un servicio de búsqueda que tenga asignada una identidad administrada, puede crear asignaciones de roles que proporcionan a servicios de datos externos, como Azure Blob Storage, acceso de lectura en blobs por parte del servicio de búsqueda de su confianza.

+ No se admiten [roles personalizados](../role-based-access-control/custom-roles.md).

+ No se admite el acceso de identidad de usuario a través de los resultados de búsqueda (a veces denominado seguridad de nivel de fila o de nivel de documento). Para la seguridad a nivel de documento, una solución alternativa es usar [filtros de seguridad](search-security-trimming-for-azure-search.md) para recortar los resultados por identidad del usuario, y quitar los documentos para los que el solicitante no debe tener acceso.

## <a name="built-in-roles-used-in-search"></a>Roles integrados usados en la búsqueda

En Cognitive Search, los roles integrados incluyen roles disponibles con carácter general y en versión preliminar, entre cuyos miembros asignados están usuarios y grupos de Azure Active Directory.

Las asignaciones de roles son acumulativas y generalizadas en todas las herramientas y bibliotecas cliente que se usan para crear o administrar un servicio de búsqueda. Estos clientes incluyen Azure Portal, la API de REST de administración, Azure PowerShell, la CLI de Azure y la biblioteca cliente de administración de los SDK de Azure.

Los roles se aplican al servicio de búsqueda en su conjunto y los debe asignar un propietario. No se pueden asignar roles a índices específicos u otros objetos de nivel superior.

No hay ninguna restricción regional, de nivel o de precios para usar RBAC en Azure Cognitive Search, pero el servicio de búsqueda debe estar en la nube pública de Azure.

| Role | Se aplica a | Descripción |
| ---- | ---------- | ----------- |
| [Propietario](../role-based-access-control/built-in-roles.md#owner) | Operaciones de servicio (disponible con carácter general) | Acceso total al recurso de búsqueda, incluida la capacidad de asignar roles de Azure. Los administradores de suscripciones son miembros de manera predeterminada. |
| [Colaborador](../role-based-access-control/built-in-roles.md#contributor) | Operaciones de servicio (disponible con carácter general) | El mismo nivel de acceso que Propietario, menos la capacidad de asignar roles o cambiar opciones de autorización. |
| [Lector](../role-based-access-control/built-in-roles.md#reader) | Operaciones de servicio (disponible con carácter general) | Acceso limitado a la información parcial del servicio. En el portal, el rol Lector puede acceder a la información de la página Información general del servicio, en la sección Essentials y en la pestaña Supervisión. Todas las demás pestañas y páginas están fuera de los límites. </br></br>Este rol tiene acceso a la información del servicio: grupo de recursos, estado del servicio, ubicación, nombre e identificador de la suscripción, etiquetas, dirección URL, plan de tarifa, réplicas, particiones y unidades de búsqueda. </br></br>Este rol también tiene acceso a las métricas del servicio: latencia de búsqueda, porcentaje de solicitudes limitadas y promedio de consultas por segundo. </br></br>No hay acceso a las claves de API, las asignaciones de roles, el contenido (índices o asignaciones de sinónimos) ni las métricas de contenido (almacenamiento consumido, número de objetos). |
| [Colaborador del servicio Search](../role-based-access-control/built-in-roles.md#search-service-contributor) | Operaciones de servicio (disponible con carácter general) y objetos de nivel superior (versión preliminar) | Este rol es una combinación de Colaborador en el nivel de servicio, pero con acceso total a todas las acciones con índices, mapas de sinónimos, indexadores, orígenes de datos y conjuntos de aptitudes a través de [`Microsoft.Search/searchServices/*`](/azure/role-based-access-control/resource-provider-operations#microsoftsearch). Este rol está pensado para administradores de servicios de búsqueda que necesitan administrar por completo el servicio. </br></br>Al igual que Colaborador, los miembros de este rol no pueden realizar ni administrar asignaciones de roles ni cambiar opciones de autorización. |
| [Colaborador de datos de índice de búsqueda](../role-based-access-control/built-in-roles.md#search-index-data-contributor) | Colección de documentos (versión preliminar) | Proporciona acceso completo al contenido en todos los índices del servicio de búsqueda. Este rol está pensado para desarrolladores o propietarios de índices que necesitan importar, actualizar o consultar la colección de documentos de un índice. |
| [Lector de datos de índice de búsqueda](../role-based-access-control/built-in-roles.md#search-index-data-reader) | Colección de documentos (versión preliminar) | Proporciona acceso de solo lectura a los índices de búsqueda en el servicio de búsqueda. Este rol está pensado para aplicaciones y usuarios que ejecutan consultas. |

> [!NOTE]
> Los recursos de Azure tienen el concepto de categorías de [plano de control y plano de datos](../azure-resource-manager/management/control-plane-and-data-plane.md) de las operaciones. En Cognitive Search, "plano de control" hace referencia a cualquier operación que se admita en la [API de REST de administración](/rest/api/searchmanagement/) o bibliotecas cliente equivalentes. "Plano de datos" hace referencia a operaciones con relación al punto de conexión del servicio de búsqueda, como la indexación o las consultas, o cualquier otra operación que se especifique en la [API de REST de búsqueda](/rest/api/searchservice/) o bibliotecas cliente equivalentes. La mayoría de los roles se aplican a un solo plano. La excepción es Colaborador de servicio de búsqueda, que admite acciones en ambos.

## <a name="step-1-preview-sign-up"></a>Paso 1: Registro para obtener la versión preliminar

**Se aplica a:** Colaborador de datos de índice de búsqueda, Lector de datos de índice de búsqueda y Colaborador de servicio de búsqueda.

Omita este paso si usa roles disponibles con carácter general (Propietario, Colaborador y Lector) o solo las acciones de nivel de servicio de Colaborador de servicio de búsqueda.

Los nuevos roles en versión preliminar integrados proporcionan un conjunto granular de permisos sobre el contenido en el servicio de búsqueda. Aunque los roles integrados siempre están visibles en Azure Portal, es necesaria la inscripción en el servicio para que estén operativos.

Para inscribirse en el programa en versión preliminar, haga lo siguiente:

+ Rellene [este formulario](https://aka.ms/azure-cognitive-search/rbac-preview).

Las solicitudes de inscripción pueden tardar hasta dos días laborables en procesarse. Recibirá un correo electrónico cuando el servicio esté listo.

## <a name="step-2-preview-configuration"></a>Paso 2: Configuración de la versión preliminar

**Se aplica a:** Colaborador de datos de índice de búsqueda, Lector de datos de índice de búsqueda y Colaborador de servicio de búsqueda.

Omita este paso si usa roles disponibles con carácter general (Propietario, Colaborador y Lector) o solo las acciones de nivel de servicio de Colaborador de servicio de búsqueda.

En este paso, configure su servicio de búsqueda para que reconozca un encabezado **Authorization** en solicitudes de datos que proporcionen un token de acceso de OAuth2.

### <a name="azure-portal"></a>[**Azure Portal**](#tab/config-svc-portal)

1. Abra el portal con esta sintaxis: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true).

1. Vaya al servicio de búsqueda.

1. En el panel de navegación izquierdo, seleccione **Claves**.

1. Elija un mecanismo de **Control de acceso de API**. 

   | Opción | Status | Descripción |
   |--------|--------|-------------|
   | Clave de API | Disponible con carácter general (predeterminado) | Requiere un [administrador o claves de API de consulta](search-security-api-keys.md) en el encabezado de solicitud para la autorización. No se usa ningún rol. |
   | Control de acceso basado en rol | Vista previa | Requiere la pertenencia a una asignación de roles para completar la tarea, según se describe en el paso siguiente. También requiere un encabezado de autorización. La elección de esta opción le limita a los clientes que admiten la API REST 2021-04-30-preview. |
   | Ambos | Vista previa | Las solicitudes son válidas mediante una clave de API o un token de autorización. |

Si no aparecen estas opciones, compruebe la dirección URL del portal.

Si no puede guardar la selección u obtiene el mensaje "No se ha actualizado el control de acceso de API para el servicio de búsqueda `<name>`. DisableLocalAuth se encuentra en versión preliminar y no está habilitado para esta suscripción", no se ha iniciado o procesado la inscripción de su suscripción.

### <a name="rest-api"></a>[**API DE REST**](#tab/config-svc-rest)

Use la API de REST de administración versión 2021-04-01-Preview, [servicio Create Or Update](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update), para configurar su servicio.

Si usa Postman u otra herramienta de pruebas web, consulte la sugerencia de la parte inferior para obtener ayuda con la configuración de la solicitud.

1. Establezca [AuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) en "aadOrApiKey".

   También puede establecer [AadAuthFailureMode](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#aadauthfailuremode) para especificar si se devuelve 401 en lugar de 403 cuando se produzca un error en la autenticación. El valor predeterminado de "disableLocalAuth" es "false", por lo que no es necesario establecerlo. No obstante, se muestra a continuación para resaltar que debe ser "false" siempre que se establezcan authOptions.

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "disableLocalAuth": false,
        "authOptions": {
          "aadOrApiKey": {
            "aadAuthFailureMode": "http401WithBearerChallenge"
          }
        }
      }
   }
    ```

1. [Asigne roles](#assign-roles) en el servicio y compruebe que funcionen correctamente en el plano de datos.

> [!TIP]
> Las llamadas a la API REST de administración se autentican mediante Azure Active Directory. Para obtener instrucciones sobre cómo configurar una solicitud y un principio de seguridad, consulte esta entrada de blog sobre [API REST de Azure con Postman (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/). El ejemplo anterior se probó con las instrucciones y la colección de Postman proporcionadas en esa entrada de blog.

---

<a name="assign-roles"></a>

## <a name="step-3-assign-roles"></a>Paso 3: Asignación de roles

Los roles se pueden asignar mediante cualquiera de los [enfoques admitidos](../role-based-access-control/role-assignments-steps.md) que se describen en la documentación del control de acceso basado en roles de Azure.

Debe ser **Propietario** o tener permisos [Microsoft.Authorization/roleAssignments/write](/azure/templates/microsoft.authorization/roleassignments) para administrar asignaciones de roles.

### <a name="azure-portal"></a>[**Azure Portal**](#tab/roles-portal)

1. En el caso de roles en versión preliminar, abra el portal con esta sintaxis: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true). Debería ver `feature.enableRbac=true` en la dirección URL.

   > [!NOTE]
   > En el caso de los usuarios y grupos asignados a un rol en versión preliminar, el contenido del portal, como índices e indexadores, solo estará visible si se abre con la marca de característica. 

1. Vaya al servicio de búsqueda.

1. Seleccione **Control de acceso (IAM)** en el panel de navegación izquierdo.

1. En el lado derecho, en **Conceder acceso a este recurso**, seleccione **Agregar asignación de roles**.

1. Encuentre un rol aplicable y, a continuación, asigne una identidad de usuario o grupo de Azure Active Directory:

   + Propietario
   + Colaborador
   + Lector
   + Colaborador del servicio de búsqueda
   + Colaborador de datos de índice de búsqueda (versión preliminar)
   + Lector de datos de índice de búsqueda (versión preliminar)

### <a name="powershell"></a>[**PowerShell**](#tab/roles-powershell)

Al [usar PowerShell para asignar roles](../role-based-access-control/role-assignments-powershell.md), indique el nombre de usuario o grupo de Azure y el ámbito de la asignación para llamar a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

Antes de empezar, asegúrese de cargar los módulos Azure y AzureAD y conectarse a Azure:

```powershell
Import-Module -Name Az
Import-Module -Name AzureAD
Connect-AzAccount
```

Si el ámbito es el servicio, la sintaxis debe ser similar al ejemplo siguiente:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>"
```

Si el ámbito es un índice individual:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>/indexes/<index-name>"
```

Recuerde que solo puede establecer el ámbito del acceso a recursos de nivel superior, como índices, asignaciones de sinónimos, indexadores, orígenes de datos y conjuntos de aptitudes. No puede controlar el acceso a los documentos de búsqueda (contenido de índices) con roles de Azure.

---

## <a name="step-4-test"></a>Paso 4: Prueba

### <a name="azure-portal"></a>[**Azure Portal**](#tab/test-portal)

1. En el caso de roles en versión preliminar, abra el portal con esta sintaxis: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true). 

   > [!NOTE]
   > En el caso de los usuarios y grupos asignados a un rol en versión preliminar, el contenido del portal, como índices e indexadores, solo estará visible si se abre con la marca de característica. 

1. Vaya al servicio de búsqueda.

1. En la página Información general, seleccione la pestaña **Índices**:

   + Los miembros de Lector de datos de índice de búsqueda pueden usar el Explorador de búsqueda para consultar el índice. Puede usar cualquier versión de la API para comprobar el acceso. Debería poder emitir consultas y ver resultados, pero no ver la definición del índice.

   + Los miembros de Colaborador de datos de índice de búsqueda pueden seleccionar **Nuevo índice** para crear uno. Al guardar un nuevo índice, se comprobará el acceso de escritura en el servicio.

### <a name="rest-api"></a>[**API DE REST**](#tab/test-rest)

+ Registre la aplicación con Azure Active Directory.

+ Revise su código para usar una [API de REST de búsqueda](/rest/api/searchservice/) (cualquier versión compatible) y establezca el encabezado **Authorization** en las solicitudes, sustituyendo el encabezado **api-key**.

  :::image type="content" source="media/search-security-rbac/rest-authorization-header.png" alt-text="Captura de pantalla de una solicitud HTTP con un encabezado &quot;Authorization&quot;" border="true":::

Para obtener más información sobre cómo adquirir un token para un entorno específico, consulte [Bibliotecas de autenticación de la Plataforma de identidad de Microsoft](/azure/active-directory/develop/reference-v2-libraries).

### <a name="net-sdk"></a>[**SDK de .NET**](#tab/test-dotnet)

El SDK de Azure para .NET admite un encabezado "Authorization" en el paquete [NuGet Gallery | Azure.Search.Documents 11.4.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.4.0-beta.2).

Se requieren pasos de configuración adicionales para registrar una aplicación en Azure Active Directory y para obtener y pasar tokens de autorización.

+ Al obtener el token de OAuth, el ámbito es "https://search.azure.com/.default". El SDK requiere que el público sea "https://search.azure.com". ".default" es una convención de Azure AD.

+ El SDK valida que el usuario tenga el ámbito "user_impersonation", el cual debe conceder su aplicación, pero solo pide "https://search.azure.com/.default".

Ejemplo de uso de una [credencial de secreto de cliente](/dotnet/api/azure.core.tokencredential):

```csharp
var tokenCredential =  new ClientSecretCredential(aadTenantId, aadClientId, aadSecret);
SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, tokenCredential);
```

Puede encontrar más información sobre el uso de la [autenticación de AAD con el SDK de Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity) en el repositorio de GitHub del SDK.

> [!NOTE]
> Si obtiene un error 403, compruebe que el servicio de búsqueda esté inscrito en el programa en versión preliminar y que su servicio esté configurado para asignaciones de roles en versión preliminar.

---

## <a name="disable-api-key-authentication"></a>Deshabilitación de la autenticación de clave de API

Las claves de API no se pueden eliminar, pero se pueden deshabilitar en el servicio. Si usa los roles Colaborador de servicio de búsqueda, Colaborador de datos de índice de búsqueda y Lector de datos de índice de búsqueda junto con la autenticación de Azure AD, puede deshabilitar las claves de API. Esta acción provoca que el servicio de búsqueda rechace todas las solicitudes relacionadas con datos que pasen una clave de API en el encabezado en el caso de solicitudes relacionadas con contenido.

Para deshabilitar la [autenticación basada en claves](search-security-api-keys.md), use la API de REST de administración versión 2021-04-01-Preview y envíe dos solicitudes consecutivas para el [servicio Update](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update).

Para deshabilitar características se necesitan permisos de Propietario o Colaborador. Use Postman u otra herramienta de pruebas web para completar los siguientes pasos (consulte la sugerencia en la parte inferior):

1. En la primera solicitud, establezca [AuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) en "aadOrApiKey" para habilitar la autenticación de Azure AD. Observe que la opción indica la disponibilidad de cualquiera de los enfoques: Azure AD o las claves de API nativas.

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "authOptions": {
          "aadOrApiKey": {
            "aadAuthFailureMode": "http401WithBearerChallenge"
          }
        }
      }
   }
    ```

1. En la segunda solicitud, establezca [disableLocalAuth](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#request-body) en "true". Este paso desactiva la parte de la clave de API de la opción "aadOrApiKey", lo que le deja únicamente con la autenticación de Azure AD.

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "disableLocalAuth": true
      }
    }
    ```

No se pueden combinar los pasos uno y dos. En el paso uno, "disableLocalAuth" debe ser "false" con el fin de cumplir los requisitos para establecer "AuthOptions", mientras que el paso dos cambia dicho valor a "true".

Para volver a habilitar la autenticación de clave, ejecute de nuevo la última solicitud, estableciendo "disableLocalAuth" en "false". El servicio de búsqueda reanudará la aceptación de las claves de API en la solicitud automáticamente (suponiendo que se especifiquen).

> [!TIP]
> Las llamadas a la API REST de administración se autentican mediante Azure Active Directory. Para obtener instrucciones sobre cómo configurar una solicitud y un principio de seguridad, consulte esta entrada de blog sobre [API REST de Azure con Postman (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/). El ejemplo anterior se probó con las instrucciones y la colección de Postman proporcionadas en esa entrada de blog.
