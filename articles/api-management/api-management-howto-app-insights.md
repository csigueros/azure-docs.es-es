---
title: Integración de Azure API Management con Azure Application Insights
titleSuffix: Azure API Management
description: Obtenga información acerca de cómo registrar y ver los eventos de Azure API Management en Azure Application Insights.
services: api-management
author: dlepow
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/27/2021
ms.author: danlep
ms.openlocfilehash: 0833e80c17294bc50489f37b21a0b605a0b0ac9d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717513"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Cómo integrar Azure API Management con Azure Application Insights

Puede integrar fácilmente Azure Application Insights con Azure API Management. Azure Application Insights es un servicio extensible para desarrolladores web que compilan y administran aplicaciones en varias plataformas. En esta guía, hará lo siguiente:
* Recorrer cada paso de la integración de Application Insights en API Management.
* Aprender estrategias para reducir el impacto en el rendimiento de la instancia del servicio API Management.

## <a name="prerequisites"></a>Prerrequisitos

Necesita una instancia de Azure API Management. [Cree una](get-started-create-service-instance.md) primero.

## <a name="create-an-application-insights-instance"></a>Crear una instancia de Application Insights

Para usar Application Insights, [cree una instancia del servicio Application Insights](../azure-monitor/app/create-new-resource.md). Para crear una instancia mediante Azure Portal, vea [Recursos de Application Insights basados en áreas de trabajo](../azure-monitor/app/create-workspace-resource.md).

## <a name="create-a-connection-between-application-insights-and-api-management"></a>Creación de una conexión entre Application Insights y API Management

1. Vaya a la **instancia del servicio Azure API Management** en **Azure Portal**.
1. Seleccione **Application Insights** en el menú de la izquierda.
1. Seleccione **+Agregar**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Captura de pantalla en la que se muestra dónde agregar una conexión nueva":::.
1. Seleccione la instancia de **Application Insights** que ha creado antes y proporcione una descripción breve.
1. Para habilitar la [supervisión de la disponibilidad](../azure-monitor/app/monitor-web-app-availability.md) de su instancia de API Management en Application Insights, seleccione la casilla **Add availability monitor** (Agregar supervisión de la disponibilidad).
    * Este parámetro valida periódicamente si el punto de conexión de servicio de API Management está respondiendo. 
    * Los resultados aparecerán en el panel **Disponibilidad** de la instancia de Application Insights.
1. Seleccione **Crear**.
1. Compruebe que el nuevo registrador de Application Insights con una clave de instrumentación aparece ahora en la lista.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Captura de pantalla en la que se muestra dónde ver el registrador de Application Insights recién creado con la clave de instrumentación":::

> [!NOTE]
> En segundo plano, se crea la entidad [Logger](/rest/api/apimanagement/2020-12-01/logger/create-or-update) en la instancia de API Management, que contiene la clave de instrumentación de la instancia de Application Insights.

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
1. Deje el resto de los valores sin modificar.

    > [!WARNING]
    > Reemplazar el valor predeterminado **0** en el campo **Number of payload bytes to log** (Número de bytes de carga que se van a registrar) puede disminuir considerablemente el rendimiento de las API.

1. Seleccione **Guardar**.
1. En segundo plano, se crea una entidad [Diagnostic](/rest/api/apimanagement/2020-12-01/diagnostic/create-or-update) (Diagnóstico) con el nombre `applicationinsights` en el nivel de API.

> [!NOTE]
> Las solicitudes se completan correctamente una vez que API Management envía toda la respuesta al cliente.


| Nombre del valor                        | Tipo de valor                        | Descripción                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Habilitar                              | boolean                           | Especifica si el registro de esta API está habilitado.                                                                                                                                                                                                                                                                                                |
| Destination                         | Registrador de Azure Application Insights | Especifica el registrador de Azure Application Insights que se va a usar.                                                                                                                                                                                                                                                                                           |
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
> Puede especificar registradores en distintos niveles: 
> + Registrador de una API.
> + Registrador para todas las API.
>  
> Especificación de los *dos*:
> + si son registradores distintos, se usarán los dos (registros de multiplexación);
> + si son el mismo registrador con configuraciones diferentes, el correspondiente a una sola API (nivel más granular) invalidará al correspondiente a todas las API.

## <a name="what-data-is-added-to-application-insights"></a>Qué datos se agregan a Application Insights

Application Insights recibe:

| Elemento de telemetría | Descripción |
| -------------- | ----------- |
| *Solicitud* | Para cada solicitud entrante: <ul><li>*solicitud de front-end*</li><li>*respuesta de front-end*</li></ul> |
| *Dependencia* | Para cada solicitud reenviada a un servicio de back-end: <ul><li>*solicitud de back-end*</li><li>*respuesta de back-end*</li></ul> |
| *Exception* | Para cada solicitud con error: <ul><li>No se pudo procesar debido a una conexión cliente cerrada</li><li>Se ha desencadenado una sección *on-error* (al producirse un error) de las directivas de la API</li><li>Tiene un código de estado HTTP de respuesta que coincide con 4xx o 5xx</li></ul> |
| *Seguimiento* | Si configura una directiva de [seguimiento](api-management-advanced-policies.md#Trace). <br /> El valor `severity` de la directiva `trace` debe ser igual o mayor que el valor `verbosity` del registro de Application Insights. |

### <a name="emit-custom-metrics"></a>Emisión de métricas personalizadas
Puede emitir métricas personalizadas si configura la directiva [`emit-metric`](api-management-advanced-policies.md#emit-metrics). 

Para que las métricas agregadas previamente de Application Insights estén disponibles en API Management, tendrá que habilitar manualmente las métricas personalizadas en el servicio.
1. Use la directiva [`emit-metric`](api-management-advanced-policies.md#emit-metrics) con la [API de creación o actualización](/rest/api/apimanagement/2021-04-01-preview/api-diagnostic/create-or-update).
1. Agregue `"metrics":true` a la carga, junto con cualquier otra propiedad.

> [!NOTE]
> Consulte los [límites de Application Insights](../azure-monitor/service-limits.md#application-insights) para más información sobre el tamaño máximo y el número de métricas y eventos por instancia de Application Insights.

## <a name="performance-implications-and-log-sampling"></a>Consecuencias en el rendimiento y muestreo de registros

> [!WARNING]
> Registrar todos los eventos puede tener graves consecuencias en el rendimiento, según la tasa de solicitudes de entrada.

En función de las pruebas de carga internas, habilitar la característica de registro ha provocado una reducción del 40 % al 50 % en el rendimiento cuando la tasa de solicitudes superaba las 1000 solicitudes por segundo. Application Insights se ha diseñado para evaluar el rendimiento de las aplicaciones mediante análisis estadísticos. No se ha creado para:
* Ser un sistema de auditoría.
* Registrar cada solicitud individual para API de gran volumen.

Puede manipular el número de solicitudes que se registran si [ajusta el valor **Muestreo**](#enable-application-insights-logging-for-your-api). Un valor del 100 % significa que se registran todas las solicitudes, mientras que un 0 % indica que no se registra nada. 

**Muestreo** ayuda a reducir el volumen de telemetría, lo que evita de manera eficaz una degradación significativa en el rendimiento, a la vez que brinda las ventajas del registro.

Para mejorar los problemas de rendimiento, omita lo siguiente:
* Encabezados de respuesta y solicitud.
* Registro del cuerpo.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Pasos siguientes

+ Más información sobre [Azure Application Insights](/azure/application-insights/).
+ Tenga en cuenta el [registro con Azure Event Hubs](api-management-howto-log-event-hubs.md).