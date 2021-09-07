---
title: Autorización basada en roles
titleSuffix: Azure Cognitive Search
description: Use el control de acceso basado en roles de Azure (Azure RBAC) para permisos pormenorizados en tareas de administración de servicios y con contenido.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/23/2021
ms.openlocfilehash: 6ffad57e87b61b9198102ddf8ae4ec8f1a9002ac
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122177830"
---
# <a name="use-role-based-authorization-in-azure-cognitive-search"></a>Uso de la autorización basada en roles en Azure Cognitive Search

Azure proporciona un [sistema de autorización de control de acceso basado en roles (RBAC)](../role-based-access-control/role-assignments-portal.md) global para todos los servicios que se ejecutan en la plataforma. En Cognitive Search, puede usar la autorización de roles para lo siguiente:

+ Permitir el acceso a las operaciones del plano de control, como agregar capacidad o rotar claves, en el propio servicio de búsqueda a través de los roles propietario, colaborador y lector.

+ Permitir el acceso a las operaciones del plano de datos, como crear o consultar índices. Esta funcionalidad se encuentra actualmente en versión preliminar pública ([por solicitud](https://aka.ms/azure-cognitive-search/rbac-preview)). Una vez que la suscripción esté incorporada, siga las instrucciones de este artículo para usar la característica.

+ Permitir que las conexiones salientes del indexador se establezcan mediante [una identidad administrada](search-howto-managed-identities-data-sources.md). Para un servicio de búsqueda que tenga asignada una identidad administrada, puede crear asignaciones de roles que amplíen los servicios de datos externos, como Azure Blob Storage, para permitir el acceso de lectura en blobs por parte del servicio de búsqueda de confianza.

Este artículo se centra en las dos primeras viñetas, roles para el plano de control y operaciones del plano de datos. Para más información sobre las llamadas de indexador salientes, comience con [Configuración de una identidad administrada](search-howto-managed-identities-data-sources.md).

Algunos escenarios de RBAC **no** se admiten directamente, entre otros:

+ [Roles personalizados](../role-based-access-control/custom-roles.md)

+ Acceso de identidad de usuario a través de los resultados de búsqueda (a veces denominado seguridad a nivel de fila o seguridad a nivel de documento)

  > [!Tip]
  > Para la seguridad a nivel de documento, una solución alternativa es usar [filtros de seguridad](search-security-trimming-for-azure-search.md) para recortar los resultados por identidad del usuario, y quitar los documentos para los que el solicitante no debe tener acceso.
  >

## <a name="roles-used-in-search"></a>Roles usados en Search

Los roles integrados incluyen roles disponibles con carácter general y de versión preliminar, cuya pertenencia asignada consta de usuarios y grupos de Azure Active Directory.

Las asignaciones de roles son acumulativas y generalizadas en todas las herramientas y bibliotecas cliente que se usan para crear o administrar un servicio de búsqueda. Los clientes incluyen Azure Portal, la API REST de administración, Azure PowerShell, la CLI de Azure y la biblioteca cliente de administración de los SDK de Azure. 

No hay ninguna restricción regional, de nivel o de precios para usar RBAC en Azure Cognitive Search, pero el servicio de búsqueda debe estar en la nube pública de Azure.

| Role | Estado | Se aplica a | Descripción |
| ---- | -------| ---------- | ----------- |
| [Propietario](../role-based-access-control/built-in-roles.md#owner) | Stable | Plano de control | Acceso total al recurso, incluida la capacidad de asignar roles de Azure. Los administradores de suscripciones son miembros de manera predeterminada. |
| [Colaborador](../role-based-access-control/built-in-roles.md#contributor) | Stable | Plano de control | El mismo nivel de acceso que el propietario, menos la capacidad de asignar roles. |
| [Lector](../role-based-access-control/built-in-roles.md#reader) | Stable | Plano de control | Acceso limitado a la información parcial del servicio. En el portal, el rol Lector puede acceder a la información de la página Información general del servicio, en la sección Essentials y en la pestaña Supervisión. Todas las demás pestañas y páginas están fuera de los límites. </br></br>Este rol tiene acceso a la información del servicio: grupo de recursos, estado del servicio, ubicación, nombre e identificador de la suscripción, etiquetas, dirección URL, plan de tarifa, réplicas, particiones y unidades de búsqueda. </br></br>Este rol también tiene acceso a las métricas del servicio: latencia de búsqueda, porcentaje de solicitudes limitadas y promedio de consultas por segundo. </br></br>No hay acceso a las claves de API, las asignaciones de roles, el contenido (índices o asignaciones de sinónimos) ni las métricas de contenido (almacenamiento consumido, número de objetos). |
| [Colaborador del servicio Search](../role-based-access-control/built-in-roles.md#search-service-contributor) | Vista previa | Plano de control | Brinda acceso total a las definiciones de objetos y servicios de búsqueda, pero sin acceso a los datos indexados. Este rol está destinado a los administradores de servicios que necesitan más información de la que proporciona el rol lector, pero que no deben tener acceso al contenido de los índices y asignaciones de sinónimos.|
| [Colaborador de datos de índice de búsqueda](../role-based-access-control/built-in-roles.md#search-index-data-contributor) | Vista previa | Plano de datos | Brinda acceso total a los datos de índices, pero nada más. Este rol es para desarrolladores o propietarios de índices que son responsables de crear y cargar contenido, pero que no deben tener acceso a la información del servicio de búsqueda. El ámbito son todos los recursos de nivel superior (indexadores, asignaciones de sinónimos, orígenes de datos, conjuntos de aptitudes) en el servicio de búsqueda. |
| [Lector de datos de índice de búsqueda](../role-based-access-control/built-in-roles.md#search-index-data-reader) | Vista previa | Plano de datos | Brinda acceso de solo lectura a los datos de índices. Este rol es para los usuarios que ejecutan consultas en un índice. El ámbito son todos los recursos de nivel superior (índices, asignaciones de sinónimos, indexadores, orígenes de datos, conjuntos de aptitudes) en el servicio de búsqueda. |

## <a name="scope-control-plane-and-data-plane"></a>Ámbito: plano de control y plano de datos

Los recursos de Azure tienen el concepto de categorías de [plano de control y plano de datos](../azure-resource-manager/management/control-plane-and-data-plane.md) de las operaciones. Los roles integrados para Cognitive Search se aplican a un plano u otro.

| Category | Operaciones |
|----------|------------|
| Plano de control | Las operaciones incluyen crear, actualizar y eliminar servicios, administrar claves de API, ajustar particiones y réplicas, etc. La [API REST de administración](/rest/api/searchmanagement/) y las bibliotecas cliente equivalentes definen las operaciones aplicables al plano de control. |
| Plano de datos | Operaciones en el punto de conexión del servicio de búsqueda, que abarca todos los objetos y datos hospedados en el servicio. La indexación, las consultas y todas las acciones asociadas tienen como destino el plano de datos, al que se accede a través de la [API REST de Search](/rest/api/searchservice/) y las bibliotecas cliente equivalentes. </br></br>Actualmente, no se pueden usar asignaciones de roles para restringir el acceso a índices individuales, asignaciones de sinónimos, indexadores, orígenes de datos o conjuntos de aptitudes. |

## <a name="configure-search-for-data-plane-authentication"></a>Configuración de Search para la autenticación del plano de datos

Si usa cualquiera de los roles de plano de datos de versión preliminar (colaborador de datos de índice de búsqueda o lector de datos de índice de búsqueda) y la autenticación Azure AD, el servicio de búsqueda debe configurarse para reconocer un encabezado de **autorización** en las solicitudes de datos que proporcionan un token de acceso de OAuth2. En esta sección se explica cómo configurar el servicio de búsqueda. Si usa roles de plano de control (propietario, colaborador, lector), puede omitir este paso.

Antes de comenzar, [suscríbase](https://aka.ms/azure-cognitive-search/rbac-preview) a la versión preliminar de RBAC. La suscripción debe inscribirse en el programa para poder usar esta característica. Las solicitudes de inscripción pueden tardar hasta dos días laborables en procesarse. Recibirá un correo electrónico cuando el servicio esté listo.

### <a name="azure-portal"></a>[**Azure Portal**](#tab/config-svc-portal)

1. Abra el portal con esta sintaxis: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true).

1. Vaya al servicio de búsqueda.

1. En el panel de navegación izquierdo, seleccione **Claves**.

1. Elija un mecanismo de **Control de acceso de API**. Si no ve estas opciones, compruebe la dirección URL del portal. Si no puede guardar la selección, hay un problema con la inscripción de la suscripción. 

   | Opción | Status | Descripción |
   |--------|--------|-------------|
   | Clave de API | Disponible con carácter general (predeterminado) | Requiere un [administrador o claves de API de consulta](search-security-api-keys.md) en el encabezado de solicitud para la autorización. No se usa ningún rol. |
   | Control de acceso basado en rol | Vista previa | Requiere la pertenencia a una asignación de roles para completar la tarea, según se describe en el paso siguiente. También requiere un encabezado de autorización. La elección de esta opción le limita a los clientes que admiten la API REST 2021-04-30-preview. |
   | Ambos | Vista previa | Las solicitudes son válidas mediante una clave de API o un token de autorización. |

Una vez que el servicio de búsqueda esté habilitado para RBAC, el portal necesitará la marca de característica en la dirección URL para asignar roles y ver contenido. **El contenido, como índices e indexadores, solo será visible en el portal si lo abre con la marca de característica.** Si quiere restaurar el comportamiento predeterminado en una fecha posterior, revierta la selección de claves de API a **Claves de API**.

### <a name="rest-api"></a>[**API DE REST**](#tab/config-svc-rest)

Use la API REST de administración, versión 2021-04-01-Preview, para configurar el servicio.

1. Llame a [Crear o actualizar servicio](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update).

1. Establezca [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) en `aadOrApiKey`. Consulte [este ejemplo](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchcreateorupdateserviceauthoptions) para conocer la sintaxis.

1. Establezca [AadAuthFailureMode](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#aadauthfailuremode) para especificar si se devuelven errores 401 o 403 cuando se produce un error en la autenticación.

---

## <a name="assign-roles"></a>Asignación de roles

Los roles se pueden asignar mediante cualquiera de los [enfoques admitidos](../role-based-access-control/role-assignments-steps.md) que se describen en la documentación del control de acceso basado en roles de Azure.

Debe ser propietario o tener permisos [Microsoft.Authorization/roleAssignments/write](/azure/templates/microsoft.authorization/roleassignments) para administrar las asignaciones de roles.

### <a name="azure-portal"></a>[**Azure Portal**](#tab/rbac-portal)

Establezca la marca de característica en la dirección URL del portal para que funcione con los roles en versión preliminar: colaborador del servicio de búsqueda, colaborador de datos de índice de búsqueda y lector de datos de índice de búsqueda.

1. Abra el portal con esta sintaxis: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true). Debería ver `feature.enableRbac=true` en la dirección URL.

1. Vaya al servicio de búsqueda.

1. Seleccione **Control de acceso (IAM)** en el panel de navegación izquierdo.

1. En el lado derecho, en **Conceder acceso a este recurso**, seleccione **Agregar asignación de roles**.

1. Busque un rol aplicable (propietario, colaborador, lector, colaborador del servicio de búsqueda, colaborador de datos de índice de búsqueda, lector de datos de índice de búsqueda) y, a continuación, asigne una identidad de usuario o grupo de Azure Active Directory.

### <a name="powershell"></a>[**PowerShell**](#tab/rbac-powershell)

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

## <a name="configure-requests-and-test"></a>Configuración de solicitudes y pruebas

Para realizar pruebas mediante programación, revise el código para usar una API REST de Search (cualquier versión compatible) y establezca el encabezado de autorización en las solicitudes. Si usa uno de los SDK de Azure, compruebe las versiones beta para ver si el encabezado de autorización está disponible. 

En función de la aplicación, se requiere una configuración adicional para registrar una aplicación en Azure Active Directory o para obtener y pasar tokens de autorización.

Como alternativa, puede usar Azure Portal y los roles asignados a usted mismo para la prueba:

1. Abra el portal con esta sintaxis: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true). 

   Aunque el servicio se haya habilitado para RBAC en un paso anterior, el portal requerirá la marca de características para invocar comportamientos de RBAC. **El contenido, como índices e indexadores, solo será visible en el portal si lo abre con la marca de característica.** Si quiere restaurar el comportamiento predeterminado, revierta la selección de claves de API a **Claves de API**.

1. Vaya al servicio de búsqueda.

1. En la página Información general, seleccione la pestaña **Índices**:

   + Para la pertenencia al lector de datos de índice de búsqueda, use el Explorador de búsqueda para consultar el índice. Puede usar cualquier versión de la API para comprobar el acceso.

   + Para un colaborador de datos de índice de búsqueda, seleccione **Nuevo índice** para crear un nuevo índice. Al guardar un nuevo índice, se comprobará el acceso de escritura en el servicio.

## <a name="disable-api-key-authentication"></a>Deshabilitación de la autenticación de clave de API

Las claves de API no se pueden eliminar, pero se pueden deshabilitar en el servicio. Si usa los roles colaborador de datos de índice de búsqueda y lector de datos de índice de búsqueda y la autenticación de Azure AD, puede deshabilitar las claves de API, lo que hace que el servicio de búsqueda rechace todas las solicitudes relacionadas con los datos que proporcionan una clave.

Use la API REST de administración en versión preliminar, versión 2021-04-01-preview, para esta tarea.

1. Establezca [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) en `aadOrApiKey`.

1. [Asigne roles](#assign-roles) y compruebe que funcionan correctamente.

1. Establezca `disableLocalAuth` en **True**.

Si revierte el último paso, estableciendo `disableLocalAuth` en **False**, el servicio de búsqueda reanudará la aceptación de las claves de API en la solicitud automáticamente (suponiendo que se especifiquen).