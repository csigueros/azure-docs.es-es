---
title: Acceso y revisión de los registros de auditoría
titleSuffix: Azure AD B2C
description: Explica cómo acceder a los registros de auditoría de Azure AD B2C mediante programación y en Azure Portal.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/20/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 16aae510920216a433e985654ac98b4a4a741ba5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043631"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Acceso a los registros de auditoría de Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) emite registros de auditoría que contienen información sobre los recursos de B2C, los tokens emitidos y el acceso de administrador. En este artículo se proporciona un breve resumen de la información disponible en los registros de auditoría e instrucciones sobre cómo acceder a estos datos del inquilino de Azure AD B2C.

Los eventos de registro de auditoría solo se conservan durante **siete días**. Si necesita un periodo de retención mayor, planee la descarga y el almacenamiento de los registros mediante alguno de los métodos que se muestran a continuación.

> [!NOTE]
> No puede ver inicios de sesión de usuario para aplicaciones individuales de Azure AD B2C en la sección **Usuarios** de la página **Azure Active Directory** o **Azure AD B2C** en Azure Portal. Los eventos de inicio de sesión muestran la actividad del usuario, pero no pueden correlacionarse de nuevo con la aplicación B2C en que el usuario inició sesión. Debe usar los registros de auditoría para ello, como se explica en este artículo.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Información general sobre las actividades disponibles en la categoría B2C de los registros de auditoría

La categoría **B2C** de los registros de auditoría contiene los siguientes tipos de actividades:

|Tipo de actividad |Descripción  |
|---------|---------|
|Authorization |Actividades relacionadas con la autorización de un usuario para acceder a los recursos de B2C (por ejemplo, un administrador que accede a una lista de directivas de B2C).         |
|Directorio |Actividades relacionadas con los atributos de directorio que se recuperan cuando un administrador inicia sesión con Azure Portal. |
|Application | Operaciones de creación, lectura, actualización y eliminación (CRUD) en aplicaciones B2C. |
|Clave |Operaciones CRUD en claves almacenadas en un contenedor de claves B2C. |
|Resource |Operaciones CRUD en recursos B2C. Por ejemplo, directivas y proveedores de identidades.
|Authentication |Validación de credenciales de usuario y emisión de tokens.|

Para las actividades CRUD de objeto de usuario, vea la categoría **Core Directory**.

## <a name="example-activity"></a>Actividad de ejemplo

En esta imagen de ejemplo de Azure Portal se muestran los datos capturados cuando un usuario inicia sesión con un proveedor de identidades externo, en este caso, Facebook:

![Ejemplo de la página Detalles de la actividad del Registro de auditoría de Azure Portal](./media/view-audit-logs/audit-logs-example.png)

El panel de detalles de actividad contiene la siguiente información pertinente:

|Sección|Campo|Descripción|
|-------|-----|-----------|
| Actividad | Nombre | Qué actividad tuvo lugar. Por ejemplo, *Emitir un valor de id_token a la aplicación*, que finaliza el inicio de sesión efectivo del usuario. |
| Iniciado por (actor) | ObjectId | El **identificador de objeto** de la aplicación B2C en que el usuario está iniciando sesión. Este identificador no es visible en Azure Portal, pero es accesible a través de Microsoft Graph API. |
| Iniciado por (actor) | Spn | El **identificador de aplicación** de la aplicación B2C en que el usuario está iniciando sesión. |
| Destinos | ObjectId | El **identificador de objeto** del usuario que está iniciando sesión. |
| Detalles adicionales | TenantId | El **identificador de inquilino** del inquilino de Azure AD B2C. |
| Detalles adicionales | PolicyId | El **identificador de directiva** del flujo de usuario (directiva) que se usa para iniciar la sesión del usuario. |
| Detalles adicionales | ApplicationId | El **identificador de aplicación** de la aplicación B2C en que el usuario está iniciando sesión. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Visualización de registros de auditoría en Azure Portal

Azure Portal proporciona acceso a los eventos de registro de auditoría en su inquilino de Azure AD B2C.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com)
1. Cambie al directorio que contiene el inquilino de Azure AD B2C y, a continuación, vaya a **Azure AD B2C**.
1. En **Actividades** en el menú de la izquierda, seleccione **Registros de auditoría**.

Se muestra una lista de los eventos de actividad registrados en los últimos siete días.

![Filtro de ejemplo con dos eventos de actividad en Azure Portal](./media/view-audit-logs/audit-logs-example-filter.png)

Hay varias opciones de filtrado disponibles, entre las que se incluyen:

* **Tipo de recurso de actividad**: filtre por los tipos de actividad que se muestran en la tabla de la sección [Información general sobre las actividades disponibles](#overview-of-activities-available-in-the-b2c-category-of-audit-logs).
* **Fecha**: filtre el intervalo de fechas de las actividades que se muestran.

Si selecciona una fila de la lista, se mostrarán los detalles de la actividad para el evento.

Para descargar la lista de eventos de actividad en un archivo de valores separados por comas (CSV), seleccione **Descargar**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Obtención de registros de auditoría con la API de generación de informes de Azure AD

Los registros de auditoría se publican en la misma canalización que otras actividades de Azure Active Directory, por lo que son accesibles a través de la [API de informes de Azure Active Directory](/graph/api/directoryaudit-list). Para más información, consulte [Introducción a la API de generación de informes de Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Habilitación del acceso a la API de generación de informes

Para permitir el acceso basado en script o en aplicaciones a la API de generación de informes de Azure AD, necesita una aplicación registrada en su inquilino de Azure AD B2C con los permisos de API siguientes. Puede habilitar estos permisos en un registro de aplicación existente dentro del inquilino de B2C, o bien crear uno nuevo específicamente para usarlo con la automatización de los registros de auditoría.

* Microsoft Graph > Permisos de aplicación > AuditLog > AuditLog.Read.All

Siga los pasos del siguiente artículo para registrar una aplicación con los permisos necesarios:

[Administrar Azure AD B2C con Microsoft Graph](microsoft-graph-get-started.md)

Después de registrar una aplicación con los permisos adecuados, consulte la sección de scripts de PowerShell que aparece más adelante en este artículo para ver un ejemplo de cómo puede obtener eventos de actividad con un script.

### <a name="access-the-api"></a>Acceso a la API

Para descargar los eventos de registro de auditoría de Azure AD B2C a través de la API, filtre los registros en la categoría `B2C`. Para filtrar por categoría, use el parámetro de cadena de consulta `filter` al llamar al punto de conexión de la API de generación de informes de Azure AD.

```http
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Script de PowerShell

En el script de PowerShell siguiente se muestra un ejemplo de cómo consultar la API de generación de informes de Azure AD. Después de consultar la API, se imprimen los eventos registrados en la salida estándar y, a continuación, se escribe la salida JSON en un archivo.

Puede probar este script en [Azure Cloud Shell.](overview.md) Asegúrese de actualizarlo con el identificador de la aplicación, el secreto de cliente y el nombre de su inquilino de Azure AD B2C.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Esta es la representación JSON del evento de actividad de ejemplo que se mostró anteriormente en el artículo:

```json
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Pasos siguientes

Puede automatizar otras tareas de administración, por ejemplo, [administrar cuentas de usuario de Azure AD B2C con Microsoft Graph](microsoft-graph-operations.md).