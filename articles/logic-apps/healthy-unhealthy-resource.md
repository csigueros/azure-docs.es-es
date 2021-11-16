---
title: Configuración del registro para supervisar aplicaciones lógicas en Azure Security Center
description: Supervise el estado de los recursos de Logic Apps en Azure Security Center mediante la configuración del registro de diagnóstico.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: aafa0584c03c2e7152ab13c5d2a89eb504ac9a14
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337157"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-microsoft-defender-for-cloud"></a>Configuración del registro para supervisar aplicaciones lógicas en Microsoft Defender for Cloud

Al supervisar los recursos de Logic Apps en [Microsoft Azure Security Center](../security-center/security-center-introduction.md), puede [revisar si las aplicaciones lógicas siguen las directivas predeterminadas](#view-logic-apps-health-status). Azure muestra el estado de mantenimiento de un recurso de Logic Apps después de habilitar el registro y configurar correctamente el destino de los registros. En este artículo se explica cómo configurar el registro de diagnóstico y asegurarse de que todas las aplicaciones lógicas son recursos correctos.

> [!TIP]
> Para buscar el estado actual del servicio Logic Apps, revise la página [Estado de Azure](https://status.azure.com/), que muestra el estado de los distintos productos y servicios en cada región disponible.

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de empezar, si no tiene una suscripción a Azure.
* Aplicaciones lógicas existentes con el [registro de diagnóstico habilitado](#enable-diagnostic-logging).
* Un área de trabajo de Log Analytics, necesaria para habilitar el registro en la aplicación lógica. Si no tiene ninguna, [cree el área de trabajo](../azure-monitor/logs/quick-create-workspace.md).

## <a name="enable-diagnostic-logging"></a>Activación del registro de diagnóstico

Para poder ver el estado de mantenimiento de los recursos de las aplicaciones lógicas, debe [configurar el registro de diagnóstico](monitor-logic-apps-log-analytics.md). Si ya tiene un área de trabajo de Log Analytics, puede habilitar el registro al crear la aplicación lógica o en las aplicaciones lógicas existentes.

> [!TIP]
> La recomendación predeterminada es habilitar los registros de diagnóstico en Logic Apps. Pero puede controlar esta configuración en las aplicaciones lógicas. Al habilitar los registros de diagnóstico en las aplicaciones lógicas, puede usar la información para ayudar a analizar los incidentes de seguridad.

### <a name="check-diagnostic-logging-setting"></a>Comprobación de la configuración del registro de diagnóstico

Si no está seguro de si las aplicaciones lógicas tienen habilitado el registro de diagnóstico, puede verlo en Defender for Cloud:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En la barra de búsqueda, escriba y seleccione **Defender for Cloud**.
1. En el menú del panel de protección de cargas de trabajo, en **General**, seleccione **Recomendaciones**.
1. En la tabla de sugerencias de seguridad, busque y seleccione **Habilitar auditoría y registro** &gt; **Los registros de diagnóstico de Logic Apps deben estar habilitados** en la tabla de controles de seguridad.
1. En la página Recomendación, expanda la sección **Pasos para la corrección** y revise las opciones. Para habilitar los diagnósticos de Logic Apps, seleccione el botón **Corrección rápida** o siga las instrucciones para la corrección manual.

## <a name="view-logic-apps-health-status"></a>Visualización del estado de mantenimiento de las aplicaciones lógicas

Después de [habilitar el registro de diagnóstico](#enable-diagnostic-logging), puede ver el estado de mantenimiento de las aplicaciones lógicas en Defender for Cloud.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En la barra de búsqueda, escriba y seleccione **Defender for Cloud**.
1. En el menú del panel de protección de cargas de trabajo, en **General**, seleccione **Inventario**.
1. En la página Inventario, filtre la lista de recursos para mostrar solo los de Logic Apps. En el menú Página, seleccione **Tipos de recursos** &gt; **Aplicaciones lógicas**.

   El contador **Recursos con estado incorrecto** muestra el número de aplicaciones lógicas que Defender for Cloud considera incorrectas.
1.  En la lista de recursos de Logic Apps, revise la columna **Recomendaciones**. Para revisar los detalles de estado de una aplicación lógica determinada, seleccione un nombre de recurso o el botón de puntos suspensivos ( **...** ) &gt; **Ver recurso**.
1.  Para corregir cualquier posible problema de estado de un recurso, siga los pasos indicados en las aplicaciones lógicas.

Si el registro de diagnóstico ya está habilitado, es posible que haya un problema con el destino de los registros. Revise [cómo corregir problemas con diferentes destinos de registro de diagnóstico](#fix-diagnostic-logging-for-logic-apps).

## <a name="fix-diagnostic-logging-for-logic-apps"></a>Corrección del registro de diagnóstico de aplicaciones lógicas

Si las [aplicaciones lógicas se muestran como incorrectas en Defender for Cloud](#view-logic-apps-health-status), abra la aplicación lógica en la vista Código de Azure Portal o mediante la CLI de Azure. Luego compruebe la configuración de destino de los registros de diagnóstico: [Azure Log Analytics](#log-analytics-and-event-hubs-destinations), [Azure Event Hubs](#log-analytics-and-event-hubs-destinations) o [una cuenta de Azure Storage](#storage-account-destination).

### <a name="log-analytics-and-event-hubs-destinations"></a>Destinos de Log Analytics y Event Hubs

Si usa Log Analytics o Event Hubs como destino de los registros de diagnóstico de Logic Apps, compruebe la siguiente configuración. 

1. Para confirmar que ha habilitado los registros de diagnóstico, compruebe que el campo `logs.enabled` de la configuración de diagnóstico está establecido en `true`. 
1. Para confirmar que no ha establecido una cuenta de almacenamiento como destino, compruebe que el campo `storageAccountId` está establecido en `false`.

Por ejemplo:

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>Destino de cuenta de almacenamiento

Si usa una cuenta de almacenamiento como destino de los registros de diagnóstico de Logic Apps, compruebe la siguiente configuración.

1. Para confirmar que ha habilitado los registros de diagnóstico, compruebe que el campo `logs.enabled` de la configuración de diagnóstico está establecido en `true`.
1. Para confirmar que ha habilitado una directiva de retención para los registros de diagnóstico, compruebe que el campo `retentionPolicy.enabled` está establecido en `true`.
1. Para confirmar que se establece un tiempo de retención de entre 0 y 365 días, compruebe que el campo `retentionPolicy.days` está establecido en un número incluido entre 0 y 365.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```
