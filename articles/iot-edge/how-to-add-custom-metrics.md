---
title: 'Cómo agregar métricas personalizadas: Azure IoT Edge'
description: Aumento de las métricas integradas con métricas específicas del escenario a partir de módulos personalizados
author: veyalla
manager: philmea
ms.author: veyalla
ms.date: 06/08/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 12defc783be6fb1a87b815284b1fbf019d8c8817
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904576"
---
# <a name="add-custom-metrics-preview"></a>Adición de métricas personalizadas (versión preliminar)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Recopile las métricas personalizadas de los módulos de IoT Edge, además de las métricas integradas que proporcionan los módulos del sistema. Las [métricas integradas](how-to-access-built-in-metrics.md) proporcionan una excelente visibilidad de línea de base sobre el estado de la implementación. Sin embargo, es posible que necesite información adicional de los módulos personalizados para completar la imagen. Los módulos personalizados se pueden integrar en la solución de supervisión mediante la [biblioteca cliente de Prometheus](https://prometheus.io/docs/instrumenting/clientlibs/) adecuada para emitir métricas. Esta información adicional puede habilitar nuevas vistas o alertas especializadas en sus requisitos.

## <a name="sample-modules-repository"></a>Repositorio de módulos de ejemplo

Consulte el [repositorio azure-samples](https://github.com/Azure-Samples/iotedge-module-prom-custom-metrics) para obtener ejemplos de módulos personalizados instrumentados para emitir métricas. Incluso si un ejemplo en el idioma elegido aún no está disponible, el enfoque general puede ayudarle.

## <a name="naming-conventions"></a>Convenciones de nomenclatura

Consulte los [procedimientos recomendados](https://prometheus.io/docs/practices/naming/) de la documentación de Prometheus para obtener instrucciones generales. Las siguientes recomendaciones adicionales pueden ser útiles para escenarios de IoT Edge.

* Incluya el nombre del módulo al principio del nombre de la métrica para dejar claro qué módulo ha emitido la métrica.

* Incluya el nombre del centro de IoT, el id. de dispositivo de IoT Edge y el id. del módulo como etiquetas (también denominadas *dimensiones de*/*etiquetas*) en cada métrica. Esta información está disponible como variables de entorno para todos los módulos que haya iniciado el agente de IoT Edge. El enfoque se [muestra](https://github.com/Azure-Samples/iotedge-module-prom-custom-metrics/blob/b6b8501adb484521b76e6f317fefee57128834a6/csharp/Program.cs#L49) en el ejemplo del repositorio de ejemplos. Sin este contexto, es imposible asociar un valor de métrica determinado a un dispositivo determinado.

* Incluya un id. de instancia en las etiquetas. Un id. de instancia puede ser cualquier identificador único, como un [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) que se genera durante el inicio del módulo. La información del id. de instancia puede ayudarle a conciliar los reinicios del módulo, ya que procesa las métricas de un módulo en el back-end.

## <a name="configure-the-metrics-collector-to-collect-custom-metrics"></a>Configuración del recopilador de métricas para obtener métricas personalizadas

Una vez que un módulo personalizado emite métricas, el siguiente paso es configurar el [módulo del recopilador de métricas](how-to-collect-and-transport-metrics.md#metrics-collector-module) para recopilar y transportar métricas personalizadas.

La variable de entorno `MetricsEndpointsCSV` debe actualizarse para que incluya la dirección URL del punto de conexión de métricas del módulo personalizado. Al actualizar la variable de entorno, asegúrese de incluir los puntos de conexión del módulo del sistema, tal como se muestra en el ejemplo de [configuración del recopilador de métricas](how-to-collect-and-transport-metrics.md#metrics-collector-configuration).

>[!NOTE]
>De forma predeterminada, no es necesario asignar el punto de conexión de métricas de un módulo personalizado a un puerto host para permitir que el recopilador de métricas acceda a él. A menos que se invalide explícitamente, en el caso de Linux, ambos módulos se inician en una [red de tipo puente de Docker definida por el usuario](https://docs.docker.com/network/bridge/#differences-between-user-defined-bridges-and-the-default-bridge) y denominada *azure-iot-edge*.
>
>Las redes de Docker definidas por el usuario incluyen una resolución DNS predeterminada que permite la comunicación entre módulos mediante nombres de módulo (contenedor). Por ejemplo, si un módulo personalizado denominado *module1* emite métricas en el puerto HTTP *9600* en la ruta de acceso */metrics*, el recopilador debe configurarse para recopilar desde el punto de conexión *http://module1:9600/metrics* .

Ejecute el siguiente comando en el dispositivo de IoT Edge para probar si las métricas que ha emitido un módulo personalizado en el puerto HTTP *9600* en la ruta de acceso */metrics* son accesibles:

```bash
sudo docker exec replace-with-metrics-collector-module-name curl http://replace-with-custom-module-name:9600/metrics
```

## <a name="add-custom-visualizations"></a>Adición de visualizaciones personalizadas

Una vez que reciba métricas personalizadas en Log Analytics, puede crear visualizaciones y alertas personalizadas. Los libros de supervisión se pueden aumentar para agregar visualizaciones con respaldo de consulta.

Cada métrica está asociada al id. de recurso de IoT Hub. Por este motivo, puede comprobar si las métricas personalizadas se ingieren correctamente desde la página **Registros** del centro de IoT asociado, en lugar del área de trabajo de Log Analytics de respaldo. Use esta consulta básica de KQL para comprobar lo siguiente:

```KQL
InsightsMetrics
| where Name == 'replace-with-custom-metric-name'
```

Una vez que haya confirmado la ingesta, puede crear un libro nuevo o aumentar uno existente. Use los [documentos de libros](../azure-monitor/visualize/workbooks-overview.md) y las consultas de los [libros de IoT Edge](how-to-explore-curated-visualizations.md) mantenidos como guía.

Cuando esté satisfecho con el resultado, puede [compartir el libro](../azure-monitor/visualize/workbooks-access-control.md) con su equipo o [implementarlo mediante programación](../azure-monitor/visualize/workbooks-automate.md) como parte de las implementaciones de recursos de su organización.

## <a name="next-steps"></a>Pasos siguientes

Explore opciones de visualización de métricas adicionales [con libros mantenidos](how-to-explore-curated-visualizations.md).
