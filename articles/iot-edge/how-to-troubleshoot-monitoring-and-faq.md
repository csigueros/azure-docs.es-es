---
title: 'Solución de problemas de supervisión y preguntas más frecuentes: Azure IoT Edge'
description: Solución de problemas de la integración de Azure Monitor y preguntas más frecuentes
author: veyalla
manager: philmea
ms.author: veyalla
ms.date: 06/09/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
zone_pivot_groups: how-to-troubleshoot-monitoring-and-faq-zpg
ms.openlocfilehash: 09475cf4ee2c78596e3c93f408fc88c16e1a751e
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904599"
---
# <a name="faq-and-troubleshooting"></a>Preguntas más frecuentes y solución de problemas

:::zone pivot="metrics-collection"

## <a name="collector-module-is-unable-to-collect-metrics-from-built-in-endpoints"></a>El módulo recopilador no puede recopilar métricas de puntos de conexión integrados

### <a name="check-if-modules-are-on-the-same-docker-network"></a>Comprobación de si los módulos están en la misma red de Docker

El módulo metrics-collector se basa en el solucionador de DNS integrado de Docker para redes definidas por el usuario. El solucionador de DNS proporciona la dirección IP de los puntos de conexión de métricas que incluyen el nombre del módulo. Por ejemplo, *http://**edgeHub**:9600/metrics*.

Cuando los módulos no se ejecutan en el mismo espacio de nombres de red, se producirá un error en este mecanismo. Por ejemplo, algunos escenarios requieren la ejecución de módulos en la red host. En estos escenarios, se produce un error en la recopilación si el módulo metrics-collector está en una red diferente.

### <a name="verify-that-httpsettings__enabled-environment-variable-isnt-set-to-false"></a>Compruebe que la variable de entorno *httpSettings__enabled* no esté establecida en *false*.

Los puntos de conexión integrados de métricas usan el protocolo HTTP. No estarán disponibles, ni siquiera dentro de la red del módulo, si HTTP se ha deshabilitado explícitamente en la configuración de la variable de entorno.

### <a name="set-no_proxy-environment-variable-if-using-http-proxy-server"></a>Establezca la variable de entorno *NO_PROXY* si se usa el servidor proxy HTTP.

Para más información, vea las [consideraciones sobre proxy](how-to-collect-and-transport-metrics.md#proxy-considerations).

## <a name="how-do-i-collect-logs-along-with-metrics"></a>¿Cómo se recopilan los registros junto con métricas?

Puede usar las [características integradas de extracción de registros](how-to-retrieve-iot-edge-logs.md). Hay disponible una solución de ejemplo que usa las características de recuperación de registros integradas en [ **https://aka.ms/iot-elms** ](https://aka.ms/iot-elms).

## <a name="why-cant-i-see-device-metrics-in-the-metrics-page-in-azure-portal"></a>¿Por qué no puedo ver las métricas del dispositivo en la página de métricas de Azure Portal?

La tecnología de [métricas nativa](../azure-monitor/essentials/data-platform-metrics.md) de Azure Monitor aún no admite el formato de datos Prometheus directamente. Las métricas basadas en registros actualmente están más indicadas para las métricas de IoT Edge debido a lo siguiente:

* Compatibilidad nativa con el formato de métricas Prometheus mediante la tabla *insightsMetrics* estándar.
* Procesamiento avanzado de datos a través de [KQL](https://aka.ms/kql) para visualizaciones y alertas.

El uso de Log Analytics como base de datos de métricas es la razón por la que las métricas aparecen en la página **Registros** de Azure Portal en lugar de en **Métricas**.

## <a name="how-do-i-configure-metrics-collector-in-a-layered-deployment"></a>¿Cómo puedo configurar metrics-collector en una implementación por capas?

El recopilador de métricas no tiene ninguna funcionalidad de detección de servicios. Se recomienda incluir el módulo en la capa de implementación base o *inferior*. En la configuración del módulo, incluya todos los puntos de conexión de métricas con los que se podría implementar el módulo. Si un módulo no aparece en una implementación final, pero su punto de conexión aparece en la lista de recopilación, el recopilador intentará recopilar, producir un error y continuar.

:::zone-end

:::zone pivot="custom-metrics"

## <a name="how-do-i-augment-the-monitoring-solution-with-custom-metrics"></a>¿Cómo puedo aumentar la solución de supervisión con métricas personalizadas?

Consulte el artículo sobre [métricas personalizadas](how-to-add-custom-metrics.md).

## <a name="how-can-i-tell-which-device-a-particular-metric-belongs-to"></a>¿Cómo puedo saber a qué dispositivo pertenece una métrica determinada?

Codifique la información del dispositivo en las etiquetas de métricas. Para más información, consulte [Convenciones de nomenclatura](how-to-add-custom-metrics.md#naming-conventions).

:::zone-end

:::zone pivot="alerts"

## <a name="how-do-i-create-a-alert-rule-that-spans-devices-from-multiple-iot-hubs"></a>¿Cómo puedo crear una regla de alerta que abarque dispositivos de varios centros de IoT?

Al [crear una regla de alerta](how-to-create-alerts.md#create-an-alert-rule), puede [cambiar su ámbito](how-to-create-alerts.md#select-alert-rule-scope) a un grupo de recursos o una suscripción. A continuación, la regla de alertas se aplicará a todos los centros de IoT en ese ámbito.

## <a name="alerts-arent-firing-when-they-should"></a>Las alertas no se activan cuando deberían

Al crear una regla de alertas, consulte el gráfico de vista previa para comprobar que la lógica de la alerta se desencadenará.

Si no encuentra el problema, cree un [incidencia de soporte técnico](https://azure.microsoft.com/support/create-ticket/) para el servicio **Log Analytics**.

:::zone-end

:::zone pivot="workbooks"

## <a name="my-device-isnt-showing-up-in-the-monitoring-workbook"></a>Mi dispositivo no aparece en el libro de supervisión

El libro se basa en las métricas del dispositivo que se vinculan al centro de IoT correcto mediante *ResourceId*. Confirme que metrics-collector está [configurado](how-to-collect-and-transport-metrics.md#metrics-collector-configuration) con el valor correcto *ResourceId*.

Use los registros del módulo metrics-collector para confirmar que el dispositivo envió métricas durante el intervalo de tiempo seleccionado.

Tenga en cuenta que puede haber un retraso en la ingesta de unos minutos antes de que se muestren las métricas.

## <a name="i-found-a-bug-or-have-a-question-about-metrics-being-shown-in-the-workbook"></a>He encontrado un error o tengo una pregunta sobre las métricas que se muestran en el libro

Abra una incidencia en [repositorio de Azure IoT Edge en GitHub](https://github.com/azure/iotedge/issues) con "[monitor-workbook]" en el título.

La plantilla de los libros está [disponible públicamente en GitHub](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks/IoTHub). Las solicitudes de incorporación de cambios con mejoras o correcciones son bienvenidas.

## <a name="i-cannot-see-the-workbooks-in-the-public-templates"></a>No puedo ver los libros en las plantillas públicas

Asegúrese de que está viendo la página **Libros** en la página de IoT Hub en el portal, no en el área de trabajo de Log Analytics.

Si sigue sin ver los libros, pruebe a usar el entorno de preproducción de Azure Portal: [`https://ms.portal.azure.com`](https://ms.portal.azure.com). A veces, las actualizaciones de los libros requieren más tiempo para aparecer en el entorno de producción, pero estarán disponibles en preproducción.

:::zone-end
