---
title: Configuración de diagnósticos para la característica de escalabilidad automática de Azure Virtual Desktop
description: Configuración de informes de diagnóstico para el servicio de escalado en la implementación de Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b5621d829050c6749795df1191ea53d835e16487
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181571"
---
# <a name="set-up-diagnostics-for-the-autoscale-feature-preview"></a>Configuración de diagnósticos para la característica de escalabilidad automática (versión preliminar)

> [!IMPORTANT]
> La característica de escalado automático está actualmente en vista previa.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

Los diagnósticos le permiten supervisar posibles problemas y corregirlos antes de que interfieran con el plan de escalado de escalabilidad automática (versión preliminar).

Actualmente, puede enviar registros de diagnóstico para la característica de escalabilidad automática a una cuenta de Azure Storage o consumir registros con el centro de eventos. Si usa una cuenta de Azure Storage, asegúrese de que se encuentra en la misma región que el plan de escalado. Obtenga más información sobre la configuración de diagnóstico en [Creación de una configuración de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md). Para obtener más información sobre el tiempo de ingesta de datos del registro de recursos, consulte [Tiempo de la ingesta de datos de registro en Azure Monitor](../azure-monitor/logs/data-ingestion-time.md).

## <a name="enable-diagnostics-for-scaling-plans"></a>Habilitación de los diagnósticos para planes de escalado

Para habilitar los diagnósticos para el plan de escalado, siga estos pasos:

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com/).

2. Seleccione **Planes de escalado** y, a continuación, seleccione el plan de escalado cuyo seguimiento desea realizar en el informe.

3. Vaya a **Configuración de diagnóstico** y seleccione **Agregar configuración de diagnóstico**.

4. Escriba un nombre para la configuración de diagnóstico.

5. A continuación, seleccione **Escalabilidad automática** y elija **cuenta de almacenamiento** o **centro de eventos**, en función de dónde quiera enviar el informe.

6. Seleccione **Guardar**.

## <a name="set-log-location-in-azure-storage"></a>Establecimiento de la ubicación del registro en Azure Storage

Después de realizar la configuración de diagnóstico, puede encontrar los registros siguiendo estas instrucciones:

1. En Azure Portal, vaya al grupo de almacenamiento al que envió los registros de diagnóstico.

2. Seleccione **Contenedores**. Se debe abrir una carpeta denominada **insight-logs-autoscaling**.

3. Seleccione la **carpeta insight-logs-autoscaling** y abra el registro que desea revisar. Abra las carpetas dentro de esa carpeta hasta que vea el archivo JSON, seleccione todos los elementos de esa carpeta, haga clic con el botón derecho y descárguelos en el equipo local.

4. Por último, abra el archivo JSON en el editor de texto de su elección.

## <a name="view-diagnostic-logs"></a>Visualización de los registros de diagnóstico

Ahora que ha abierto el archivo JSON, vamos a realizar una presentación rápida de lo que significa cada parte del informe:

- **CorrelationID** es el identificador que debe mostrar al crear un caso de soporte técnico.

- **OperationName** es el tipo de operación que se ejecutaba cuando se produjo el problema.

- **ResultType** es el resultado de la operación. Este elemento puede mostrar dónde están los problemas si observa resultados incompletos.

- **Message** es el mensaje de error que proporciona información sobre la operación incompleta. Este mensaje puede incluir vínculos a documentación de solución de problemas importante, así que repáselo detenidamente.

El siguiente archivo JSON es un ejemplo de lo que verá al abrir un informe:

```json
{
    "host_Ring": "R0",
    "Level": 4,
    "ActivityId": "c1111111-1111-1111-b111-11111cd1ba1b1",
    "time": "2021-08-31T16:00:46.5246835Z",
    "resourceId": "/SUBSCRIPTIONS/AD11111A-1C21-1CF1-A7DE-CB1111E1D111/RESOURCEGROUPS/TEST/PROVIDERS/MICROSOFT.DESKTOPVIRTUALIZATION/SCALINGPLANS/TESTPLAN",
    "operationName": "HostPoolLoadBalancerTypeUpdated",
    "category": "Autoscale",
    "resultType": "Succeeded",
    "level": "Informational",
    "correlationId": "35ec619b-b5d8-5b5f-9242-824aa4d2b878",
    "properties": {
        "Message": "Host pool's load balancing algorithm updated",
        "HostPoolArmPath": "/subscriptions/AD11111A-1C21-1CF1-A7DE-CB1111E1D111/resourcegroups/test/providers/microsoft.desktopvirtualization/hostpools/testHostPool ",
        "PreviousLoadBalancerType": "BreadthFirst",
        "NewLoadBalancerType": "DepthFirst"
    }
}. L
```

## <a name="next-steps"></a>Pasos siguientes

- Revise cómo crear un plan de escalado en [Escalabilidad automática de hosts de sesión de Azure Virtual Desktop](autoscale-scaling-plan.md).
- [Asigne el plan de escalado a grupos de hosts nuevos o existentes](autoscale-new-existing-host-pool.md).
