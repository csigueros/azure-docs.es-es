---
title: 'Resolución de problemas relacionados con los cambios en la directiva de acceso condicional: Azure Active Directory'
description: Diagnostique los cambios en la directiva de acceso condicional con los registros de auditoría de Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7683b64449a32013d7ac5e548ea9acaf85c51666
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561629"
---
# <a name="troubleshooting-conditional-access-policy-changes"></a>Resolución de problemas relacionados con los cambios en la directiva de acceso condicional

El registro de auditoría de Azure Active Directory (Azure AD) es una fuente de información valiosa al solucionar problemas para determinar por qué y cómo se realizaron los cambios en la directiva de acceso condicional en su entorno.

Los datos del registro de auditoría solo se mantienen durante 30 días de manera predeterminada, un plazo que podría no ser suficiente para cada organización. Las organizaciones pueden almacenar datos durante períodos más prolongados cambiando la configuración de diagnóstico de Azure AD para: 

- Enviar datos a una área de trabajo de Log Analytics
- Archivar datos en una cuenta de almacenamiento
- Transmitir datos a un centro de eventos
- Enviar datos a una solución de asociado
 
Puede encontrar estas opciones en **Azure Portal** > **Azure Active Directory**, **Configuración de diagnóstico** > **Editar configuración**. Si no tiene una configuración de diagnóstico, siga las instrucciones que aparecen en el artículo [Creación de una configuración de diagnóstico para enviar los registros y las métricas de la plataforma a diferentes destinos](../../azure-monitor/essentials/diagnostic-settings.md) para crear una. 

## <a name="use-the-audit-log"></a>Uso del registro de auditoría

1. Inicie sesión en **Azure Portal** como administrador global, administrador de seguridad o administrador de acceso condicional.
1. Vaya a **Azure Active Directory** > **Registros de auditoría**.
1. Seleccione el intervalo de **Fechas** que desea consultar.
1. Seleccione **Actividad** y elija una de las opciones siguientes
   1. **Agregar directiva de acceso condicional**: esta actividad muestra las directivas recién creadas
   1. **Actualizar directiva de acceso condicional**: esta actividad muestra las directivas modificadas
   1. **Eliminar directiva de acceso condicional**: esta actividad muestra las directivas eliminadas

:::image type="content" source="media/troubleshoot-policy-changes-audit-log/old-and-new-policy-properties.png" alt-text="Entrada del registro de auditoría que muestra valores JSON anteriores y nuevos para la directiva de acceso condicional" lightbox="media/troubleshoot-policy-changes-audit-log/old-and-new-policy-properties.png":::

## <a name="use-log-analytics"></a>Uso de Log Analytics

Log Analytics permite que las organizaciones consulten datos mediante consultas integradas o consultas de Kusto personalizadas creadas. Para más información, consulte [Introducción a las consultas de registro en Azure Monitor](../../azure-monitor/logs/get-started-queries.md).

:::image type="content" source="media/troubleshoot-policy-changes-audit-log/log-analytics-new-old-value.png" alt-text="Consulta de Log Analytics para las actualizaciones en las directivas de acceso condicional que muestra la ubicación de valores nuevos y anteriores" lightbox="media/troubleshoot-policy-changes-audit-log/log-analytics-new-old-value.png":::

Una vez que se habilita, busque el acceso a Log Analytics en **Azure Portal** > **Azure AD** > **Log Analytics**. La tabla de mayor interés para los administradores del acceso condicional es **AuditLogs**.

```kusto
AuditLogs 
| where OperationName == "Update conditional access policy"
```

Puede encontrar los cambios en **TargetResources** > **modifiedProperties**.

## <a name="reading-the-values"></a>Lectura de los valores

Los valores anteriores y nuevos del registro de auditoría y Log Analytics están en formato JSON. Compare los dos valores para ver los cambios en la directiva.

Ejemplo de directiva anterior:

```json
{
    "conditions": {
        "applications": {
            "applicationFilter": null,
            "excludeApplications": [
            ],
            "includeApplications": [
                "797f4846-ba00-4fd7-ba43-dac1f8f63013"
            ],
            "includeAuthenticationContextClassReferences": [
            ],
            "includeUserActions": [
            ]
        },
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "servicePrincipalRiskLevels": [
        ],
        "signInRiskLevels": [
        ],
        "userRiskLevels": [
        ],
        "users": {
            "excludeGroups": [
                "eedad040-3722-4bcb-bde5-bc7c857f4983"
            ],
            "excludeRoles": [
            ],
            "excludeUsers": [
            ],
            "includeGroups": [
            ],
            "includeRoles": [
            ],
            "includeUsers": [
                "All"
            ]
        }
    },
    "displayName": "Common Policy - Require MFA for Azure management",
    "grantControls": {
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [
        ],
        "operator": "OR",
        "termsOfUse": [
            "a0d3eb5b-6cbe-472b-a960-0baacbd02b51"
        ]
    },
    "id": "334e26e9-9622-4e0a-a424-102ed4b185b3",
    "modifiedDateTime": "2021-08-09T17:52:40.781994+00:00",
    "state": "enabled"
}

```

Ejemplo de directiva actualizada:

```json
{
    "conditions": {
        "applications": {
            "applicationFilter": null,
            "excludeApplications": [
            ],
            "includeApplications": [
                "797f4846-ba00-4fd7-ba43-dac1f8f63013"
            ],
            "includeAuthenticationContextClassReferences": [
            ],
            "includeUserActions": [
            ]
        },
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "servicePrincipalRiskLevels": [
        ],
        "signInRiskLevels": [
        ],
        "userRiskLevels": [
        ],
        "users": {
            "excludeGroups": [
                "eedad040-3722-4bcb-bde5-bc7c857f4983"
            ],
            "excludeRoles": [
            ],
            "excludeUsers": [
            ],
            "includeGroups": [
            ],
            "includeRoles": [
            ],
            "includeUsers": [
                "All"
            ]
        }
    },
    "displayName": "Common Policy - Require MFA for Azure management",
    "grantControls": {
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [
        ],
        "operator": "OR",
        "termsOfUse": [
        ]
    },
    "id": "334e26e9-9622-4e0a-a424-102ed4b185b3",
    "modifiedDateTime": "2021-08-09T17:52:54.9739405+00:00",
    "state": "enabled"
}

``` 

En el ejemplo anterior, la directiva actualizada no incluye condiciones de uso en los controles de concesión.

### <a name="restoring-conditional-access-policies"></a>Restauración de directivas de acceso condicional

Para más información sobre cómo actualizar mediante programación las directivas de acceso condicional con Microsoft Graph API, consulte el artículo [Acceso condicional: Acceso mediante programación](howto-conditional-access-apis.md).

## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es la supervisión de Azure Active Directory?](../reports-monitoring/overview-monitoring.md)
- [Instalación y uso de las vistas de Log Analytics para Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [Acceso condicional: Acceso mediante programación](howto-conditional-access-apis.md)
