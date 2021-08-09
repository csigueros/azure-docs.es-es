---
title: 'Exploración de las visualizaciones mantenidas: Azure IoT Edge'
description: Uso de libros de Azure para visualizar y explorar las métricas integradas de IoT Edge
author: veyalla
manager: philmea
ms.author: veyalla
ms.date: 06/08/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0ca0d1fb7890f1a1a94419f58587f3a98957f41c
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904594"
---
# <a name="explore-curated-visualizations-preview"></a>Exploración de visualizaciones mantenidas (versión preliminar)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Puede visualizar y explorar las métricas recopiladas desde un dispositivo de IoT Edge directamente en Azure Portal mediante los libros de Azure Monitor. Los libros de supervisión seleccionados para IoT Edge se proporcionan en forma de plantillas públicas a las que puede acceder en la hoja **IoT Hub** de la página **Libros** (en la sección Supervisión).

Los libros seleccionados usan [métricas integradas](how-to-access-built-in-metrics.md) del entorno de ejecución de IoT Edge. Estas vistas no necesitan la instrumentación de métricas de los módulos de carga de trabajo.

## <a name="access-curated-workbooks"></a>Acceso a libros mantenidos

Los libros de Azure Monitor para IoT son un conjunto de plantillas que puede usar para empezar a visualizar las métricas de inmediato o que puede personalizar para adaptarlas a su solución.

Para obtener acceso a los libros seleccionados, siga los siguientes pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a IoT Hub.

1. Seleccione **Libros** en la sección **Supervisión** del menú.

1. Elija el libro que quiere explorar en la lista de plantillas públicas:

   * **Vista de flota de IoT Edge**: supervise la flota de dispositivos y explore en profundidad dispositivos específicos para obtener una instantánea de estado.
   * **Detalles del dispositivo de IoT Edge:** visualice los detalles del dispositivo en torno a la mensajería, los módulos y los componentes de host en un dispositivo de IoT Edge.
   * **Instantánea de estado de IoT Edge**: consulte el estado de un dispositivo en función de seis métricas de rendimiento comunes. Para acceder al libro de instantáneas de estado, comience en el libro de vista de flota y seleccione el dispositivo específico que quiera ver. El libro de vista de la flota pasa algunos parámetros necesarios a la vista de instantáneas de estado.

Puede explorar los libros por su cuenta o usar las secciones siguientes para obtener una vista previa del tipo de datos y visualizaciones que ofrece cada libro.

## <a name="iot-edge-fleet-view-workbook"></a>Libro de vista de flota de IoT Edge

El libro de vista de flota tiene dos vistas que puede usar:

* La vista **Dispositivos** muestra información general de los dispositivos activos.
* La vista **Alertas** muestra las alertas generadas a partir de las [reglas de alerta preconfiguradas](how-to-create-alerts.md).

Puede cambiar entre las vistas mediante las pestañas de la parte superior del libro.

# <a name="devices"></a>[Dispositivos](#tab/devices)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-fleet-view.gif" alt-text="Sección de dispositivos del libro de vista de flota." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-fleet-view.gif":::

Consulte la información general de los dispositivos activos que envían métricas en la vista **Dispositivos**. En esta vista se muestran los dispositivos asociados a la instancia actual de IoT Hub.

A la derecha encontrará la lista de dispositivos con barras compuestas, que muestran los mensajes locales y ascendentes enviados. Puede filtrar la lista por nombre de dispositivo y hacer clic en el vínculo del nombre de dispositivo para ver sus métricas detalladas.

A la izquierda, la visualización de celdas de subárbol muestra qué dispositivos son correctos o incorrectos. También muestra en momento en el que el dispositivo envió las últimas métricas. Los dispositivos que no han enviado métricas durante más de 30 minutos se muestran en azul. Haga clic en el nombre del dispositivo en la celda de subárbol para ver su instantánea de estado. Solo se tienen en cuenta las tres últimas medidas del dispositivo al determinar el estado. Solo se usan cuentas de datos recientes para picos temporales en las métricas notificadas.

# <a name="alerts"></a>[Alertas](#tab/alerts)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-alerts.gif" alt-text="Sección de alertas del libro de vista de flota." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-alerts.gif":::

Consulte las alertas generadas de las [reglas de alertas creadas previamente](how-to-create-alerts.md) en la vista **Alertas**. Esta vista le permite ver alertas de varias instancias de IoT Hub.

A la izquierda, hay una lista con las gravedades de las alertas y con su recuento. A la derecha, hay un mapa con las alertas totales por región.

Haga clic en una fila de gravedad para ver los detalles de las alertas. El vínculo **Regla de alerta** le lleva al contexto de la alerta y el vínculo **Dispositivo** abre el libro de métricas detalladas. Cuando se abre desde esta vista, el libro de detalles del dispositivo se ajusta automáticamente al intervalo de tiempo en torno al momento en que se desencadena la alerta.

---

## <a name="iot-edge-device-details-workbook"></a>Libro de detalles del dispositivo de IoT Edge

El libro de detalles del dispositivo tiene tres vistas que puede usar:

* La vista **Mensajería** visualiza las rutas de mensaje para el dispositivo e informa sobre el estado general del sistema de mensajería.
* La vista **Módulos** muestra el rendimiento de los módulos individuales de un dispositivo.
* La vista **Host** muestra información sobre el dispositivo host, incluida la información de versión para los componentes de host y el uso de recursos.

Puede cambiar entre las vistas mediante las pestañas de la parte superior del libro.

El libro de detalles del dispositivo también se integra con la experiencia de solución de problemas basada en el portal de IoT Edge para que pueda ver los **registros en directo** procedentes del dispositivo. Para acceder a esta experiencia, seleccione el botón **Solucionar problemas de \<device name> en directo** situado encima del libro.

# <a name="messaging"></a>[Mensajería](#tab/messaging)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-messaging-details.gif" alt-text="Sección de mensajería del libro de detalles del dispositivo." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-messaging-details.gif":::

La vista **Mensajería** incluye tres subsecciones: detalles de enrutamiento, un gráfico de enrutamiento y el estado de mensajería. Arrastre y suelte cualquier gráfico de tiempo para ajustar el rango de tiempo global al rango seleccionado.

La sección **Enrutamiento** muestra el flujo de mensajes entre el envío de módulos y la recepción de estos. Presenta información como el número de mensajes, la velocidad y el número de clientes conectados. Haga clic en un emisor o receptor para obtener más detalles. Al hacer clic en un emisor se muestra el gráfico de tendencias de latencia experimentada por este y el número de mensajes que envió. Al hacer clic en un receptor se muestra la tendencia de longitud de cola del receptor y el número de mensajes que recibió.

La sección **Gráfico** muestra una representación visual del flujo de mensajes entre módulos. Puede arrastrar y acercar el contenido para ajustar el gráfico.

En la sección **Estado** se presentan varias métricas relacionadas con el estado general del subsistema de mensajería. Puede profundizar progresivamente en los detalles si se crea algún error.

# <a name="modules"></a>[Módulos](#tab/modules)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-module-details.gif" alt-text="Sección de módulos del libro de detalles del dispositivo." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-module-details.gif":::

La vista **Módulos** presenta las métricas recopiladas del módulo edgeAgent, que informa sobre el estado de todos los módulos en ejecución en el dispositivo. Se incluye información como la siguiente:

* Disponibilidad del módulo.
* Uso de CPU y memoria por módulo.
* Uso de CPU y memoria en todos los módulos.
* Recuento de reinicios de módulos y escala de tiempo de reinicio.

# <a name="host"></a>[Host](#tab/host)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-host-details.gif" alt-text="Sección de host del libro de detalles del dispositivo." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-host-details.gif":::

La vista **Host** presenta las métricas del módulo edgeAgent. Se incluye información como la siguiente:

* Información sobre las versiones de componentes de host
* Tiempo de actividad
* Uso de CPU, memoria y espacio en disco en el nivel de host

# <a name="live-logs"></a>[Registros en vivo](#tab/livelogs)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-troubleshoot-live.gif" alt-text="Obtenga acceso a los registros en vivo a través del libro de detalles del dispositivo." lightbox="./media/how-to-explore-curated-visualizations/how-to-troubleshoot-live.gif":::

Este libro se integra directamente con la experiencia de solución de problemas basada en el portal. Haga clic en el botón **Solucionar problemas en vivo** para ir a la pantalla de solución de problemas. En esta pantalla puede ver fácilmente los registros de módulos que se extrajo del dispositivo, a petición. El intervalo de tiempo se establece automáticamente en el intervalo de tiempo del libro, por lo que se encuentra inmediatamente en contexto temporal. También puede reiniciar cualquier módulo desde esta experiencia.

---

## <a name="iot-edge-health-snapshot-workbook"></a>Libro de la instantánea de estado de IoT Edge

Se puede acceder al libro de instantáneas de estado desde el libro de vista de la flota. El libro de vista de flota pasa algunos parámetros necesarios para inicializar la vista de instantánea de estado. Seleccione un nombre de dispositivo en la celda de subárbol para ver la instantánea de estado de ese dispositivo.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-access-health-snapshot.png" alt-text="Para acceder al libro de instantáneas de estado, seleccione un dispositivo en el libro de vista de la flota" lightbox="./media/how-to-explore-curated-visualizations/how-to-access-health-snapshot.png":::.

La instantánea de estado ya está lista para usarse, y consta de seis señales:

* Mensajes ascendentes
* Mensajes locales
* Longitud de la cola
* Uso del disco
* Uso de la CPU de nivel de host
* Uso de la memoria de nivel de host

Estas señales se miden según umbrales configurables para determinar si un dispositivo está en buen estado o no. Los umbrales se pueden ajustar, o se pueden agregar nuevas señales si se edita el libro. Consulte la sección siguiente para obtener información sobre las personalizaciones de libros.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-health-snapshot.gif" alt-text="Consulte el libro de instantáneas de estado." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-health-snapshot.gif":::

## <a name="customize-workbooks"></a>Personalización de libros

Los [libros de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md) son muy personalizables. Puede editar las plantillas públicas para que se adapten a sus requisitos. Todas las visualizaciones están controladas por consultas [KQL](https://aka.ms/kql) centradas en recursos en la tabla [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics). Consulte el ejemplo siguiente que edita los umbrales de estado.

Para empezar a personalizar un libro, primero debe entrar en el modo de edición. Seleccione el botón **Editar** en la barra de menús del libro.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-access-edit-mode.png" alt-text="Escriba el modo de edición de un libro" lightbox="./media/how-to-explore-curated-visualizations/how-to-access-edit-mode.png":::.

Los libros mantenidos usan ampliamente los grupos de libros. Es posible que tenga que hacer clic en **Editar** en varios grupos anidados antes de poder ver una consulta de visualización.

A continuación, puede guardar los cambios como un libro nuevo. Puede [compartir](../azure-monitor/visualize/workbooks-access-control.md) el libro guardado con su equipo o [implementarlo mediante programación](../azure-monitor/visualize/workbooks-automate.md) como parte de las implementaciones de recursos de su organización.

Por ejemplo, puede cambiar los umbrales que indican si un dispositivo está en buen estado o no. Para ello, puede profundizar en los detalles de la plantilla del libro de vista de la flota hasta llegar al elemento de consulta **device-health-graph**, que incluye todos los umbrales de métricas con los que este libro compara un dispositivo.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-edit-thresholds.gif" alt-text="Siga abriendo el modo de edición de los componentes anidados hasta llegar al elemento de consulta" lightbox="./media/how-to-explore-curated-visualizations/how-to-edit-thresholds.gif":::.

## <a name="next-steps"></a>Pasos siguientes

Personalice la solución de supervisión con [reglas de alertas y](how-to-create-alerts.md) [métricas de módulos personalizados](how-to-add-custom-metrics.md).
