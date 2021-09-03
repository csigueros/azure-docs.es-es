---
title: Integración de Azure API Management con Azure Application Insights
titleSuffix: Azure API Management
description: Obtenga información acerca de cómo registrar y ver los eventos de Azure API Management en Azure Application Insights.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/04/2021
ms.author: apimpm
ms.openlocfilehash: f67da2c2090dd99730324512248854d5e2fee259
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182083"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Cómo integrar Azure API Management con Azure Application Insights

Azure API Management le permite una integración sencilla con Azure Application Insights, un servicio extensible para desarrolladores web que compilan y administran aplicaciones en varias plataformas. En esta guía, verá todos los pasos para dicha integración; además, se describen las estrategias para reducir el impacto en el rendimiento en la instancia de servicio de API Management.

## <a name="prerequisites"></a>Prerrequisitos

Para seguir esta guía, debe tener una instancia de Azure API Management. Si no tiene una, complete antes el [tutorial](get-started-create-service-instance.md).

## <a name="create-an-application-insights-instance"></a>Crear una instancia de Application Insights

Antes de poder usar Application Insights, debe crear una instancia del servicio. Para conocer los pasos para crear una instancia mediante Azure Portal, consulte [Recursos de Application Insights basados en áreas de trabajo](../azure-monitor/app/create-workspace-resource.md).
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Creación de una conexión entre Application Insights y API Management

1. Vaya a la **instancia del servicio Azure API Management** en **Azure Portal**.
1. Seleccione **Application Insights** en el menú de la izquierda.
1. Seleccione **+Agregar**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Captura de pantalla en la que se muestra dónde agregar una conexión nueva":::.
1. Seleccione la instancia de **Application Insights** creada anteriormente y escriba una descripción breve.
1. Para habilitar la [supervisión de la disponibilidad](../azure-monitor/app/monitor-web-app-availability.md) de su instancia de API Management en Application Insights, seleccione la casilla **Add availability monitor** (Agregar supervisión de la disponibilidad).

    Este parámetro valida periódicamente si el punto de conexión de servicio de API Management está respondiendo. Los resultados aparecerán en el panel **Disponibilidad** de la instancia de Application Insights.
1. Seleccione **Crear**.
1. Acaba de crear un registrador de Application Insights con una clave de instrumentación. Ahora debería aparecer en la lista.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Captura de pantalla en la que se muestra dónde ver el registrador de Application Insights recién creado con la clave de instrumentación":::

> [!NOTE]
> En segundo plano, se crea la entidad [Logger](/rest/api/apimanagement/2019-12-01/logger/createorupdate) en la instancia de API Management, que contiene la clave de instrumentación de la instancia de Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Habilitación del registro Application Insights para la API

1. Vaya a la **instancia del servicio Azure API Management** en **Azure Portal**.
1. Seleccione **API** del menú de la izquierda.
1. Haga clic en la API, en este caso, **Demo Conference API**. Si está configurado, seleccione una versión.
1. Vaya a la pestaña **Configuración** de la barra superior.
1. Desplácese hacia abajo hasta la sección **Diagnostics Logs** (Registros de diagnóstico).  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="Registrador de App Insights":::
1. Marque la casilla **Habilitar**.
1. Seleccione el registrador adjunto en el menú desplegable **Destino**.
1. Escriba **100** como **Sampling (%)** [Muestreo (%)] y seleccione la casilla **Always log errors** (Registrar errores siempre).
1. Seleccione **Guardar**.

> [!WARNING]
> Reemplazar el valor predeterminado **0** en el campo **Number of payload bytes to log** (Número de bytes de carga que se van a registrar) puede disminuir considerablemente el rendimiento de las API.

> [!NOTE]
> En segundo plano, se crea la entidad [Diagnostic](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) en el nivel de API denominada "applicationinsights".

| Nombre del valor                        | Tipo de valor                        | Descripción                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Habilitar                              | boolean                           | Especifica si el registro de esta API está habilitado.                                                                                                                                                                                                                                                                                                |
| Destination                         | Registrador de Azure Application Insights | Especifica el registrador de Azure Application Insights que se usará                                                                                                                                                                                                                                                                                           |
| Sampling (%) [Muestreo (%)]                        | Decimal                           | Valores de 0 a 100 (porcentaje). <br/> Especifica el porcentaje de solicitudes que se registrarán en Application Insights. Un muestreo del 0 % significa que no se registrará ninguna solicitud, mientras que un muestreo del 100 % se traduce en que se registrarán todas las solicitudes. <br/> Use esta opción para reducir el efecto sobre el rendimiento al registrar las solicitudes en Application Insights. Consulte [Consecuencias en el rendimiento y muestreo de registros](#performance-implications-and-log-sampling). |
| Always log errors (Registrar errores siempre)                   | boolean                           | Si se selecciona esta opción, se registrarán todos los errores en Application Insights, independientemente del valor de **Sampling** (Muestreo).   
| Registro de la dirección IP del cliente | |  Si se selecciona esta opción, la dirección IP del cliente para las solicitudes de API se registrará en Application Insights.                                         |
| Nivel de detalle         |                                   | Especifica el nivel de detalle. Solo se registrarán los seguimientos personalizados de mayor nivel de gravedad. Valor predeterminado: información.      | 
| Protocolo de correlación |  |  Seleccione el protocolo usado para correlacionar la telemetría enviada por varios componentes. Valor predeterminado: heredado <br/>Para más información, consulte [Correlación de Telemetría en Application Insights](../azure-monitor/app/correlation.md).  |
| Opciones básicas: encabezados que se van a registrar              | list                              | Especifica los encabezados que se registrarán en Application Insights para las solicitudes y las respuestas.  Predeterminado: no se registra ningún encabezado.                                                                                                                                                                                                             |
| Opciones básicas: número de bytes de carga que se van a registrar | integer                           | Especifica cuántos primeros bytes del cuerpo se registrarán en Application Insights para las solicitudes y las respuestas.  Valor predeterminado: 0.                                                                                                                                                                                                    |
| Opciones avanzadas: Solicitud de front-end  |                                   | Especifica si las *solicitudes de front-end* se registran en Application Insights y cuántas se registran. *Frontend request* es una solicitud que entra al servicio de Azure API Management.                                                                                                                                                                        |
| Opciones avanzadas: Respuesta de front-end |                                   | Especifica si las *respuestas de front-end* se registran en Application Insights y cuántas se registran. *Frontend response* es una respuesta que sale del servicio de Azure API Management.                                                                                                                                                                   |
| Opciones avanzadas: Solicitud de back-end   |                                   | Especifica si las *solicitudes de back-end* se registran en Application Insights y cuántas se registran. *Backend request* es una solicitud que sale del servicio de Azure API Management.                                                                                                                                                                        |
| Opciones avanzadas: Respuesta de back-end  |                                   | Especifica si las *respuestas de back-end* se registran en Application Insights y cuántas se registran. *Backend response* es una respuesta que entra al servicio de Azure API Management.                                                                                                                                                                       |

> [!NOTE]
> Puede especificar registradores en distintos niveles: registrador para una API o registrador para todas las API.
>  
> Si especifica ambos:
> + si son registradores distintos, se usarán ambos (registros de multiplexación);
> + si son el mismo registrador, pero tienen configuraciones diferentes, el correspondiente a una sola API (nivel más granular) invalidará al que corresponde a todas las API.

## <a name="what-data-is-added-to-application-insights"></a>Qué datos se agregan a Application Insights

Application Insights recibe:

+ El elemento de telemetría *Solicitud*; para cada solicitud entrante, recibe lo siguiente:
    + *solicitud de front-end* y *respuesta de front-end*
+ El elemento de telemetría *Dependencia*; para cada solicitud reenviada a un servicio de back-end, recibe lo siguiente:
    + *solicitud de back-end* y *respuesta de back-end*
+ elemento de telemetría *Excepción*, para cada solicitud con error:
    + no se pudo procesar debido a una conexión cerrada con el cliente
    + desencadenó una sección *on-error* (al producirse un error) de las directivas de la API
    + tiene un código de estado HTTP de respuesta que coincide con 4xx o 5xx
+ elemento de telemetría *Seguimiento*, si configura una directiva de [seguimiento](api-management-advanced-policies.md#Trace). 
    + El valor `severity` de la directiva `trace` debe ser igual o mayor que el valor `verbosity` del registro de Application Insights.

También puede emitir métricas personalizadas configurando la directiva [`emit-metric`](api-management-advanced-policies.md#emit-metrics).

> [!NOTE]
> Consulte los [límites de Application Insights](../azure-monitor/service-limits.md#application-insights) para más información sobre el tamaño máximo y el número de métricas y eventos por instancia de Application Insights.

## <a name="performance-implications-and-log-sampling"></a>Consecuencias en el rendimiento y muestreo de registros

> [!WARNING]
> Registrar todos los eventos puede tener graves consecuencias en el rendimiento, según la tasa de solicitudes de entrada.

En función de las pruebas de carga internas, habilitar esta característica provocó una reducción del 40 % al 50 % en el rendimiento cuando la tasa de solicitudes superaba las 1000 solicitudes por segundo. Application Insights está diseñada para usar análisis estadísticos para evaluar el rendimiento de las aplicaciones. No está destinado a ser un sistema de auditoría y no es apta para registrar cada solicitud individual para API de gran volumen.

Para manipular el número de solicitudes que se registran, puede ajustar la opción **Sampling** (Muestreo) que se explica en los pasos anteriores. Un valor del 100 % significa que se registran todas las solicitudes, mientras que un 0 % indica que no se registra nada. **Sampling** (Muestreo) ayuda a reducir el volumen de telemetría, lo que evita de manera eficaz una degradación significativa en el rendimiento, a la vez que brinda las ventajas del registro.

Omitir el registro de encabezados y del cuerpo de las solicitudes y respuestas también tendrá un efecto positivo para evitar problemas de rendimiento.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Pasos siguientes

+ Más información sobre [Azure Application Insights](/azure/application-insights/).
+ Tenga en cuenta el [registro con Azure Event Hubs](api-management-howto-log-event-hubs.md).
