---
title: 'Tutorial: Libros de Azure Monitor para IoT Edge'
description: Obtenga información sobre cómo supervisar módulos y dispositivos IoT Edge mediante Libros de Azure Monitor para IoT
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 08/13/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6ba79aa63700a35e79d49febad8510e283b35441
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122207601"
---
# <a name="tutorial-monitor-iot-edge-devices"></a>Tutorial: Supervisión de los dispositivos IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Use Azure Monitor Workbooks para supervisar el estado y el rendimiento de las implementaciones de Azure IoT Edge.

En este tutorial, aprenderá a:

> [!div class="checklist"]
>
> * Conozca las métricas que comparten los dispositivos IoT Edge y cómo las gestiona el módulo recopilador de métricas.
> * Implemente el módulo recopilador de métricas en un dispositivo IoT Edge.
> * Consultar visualizaciones seleccionadas de las métricas recopiladas del dispositivo.

## <a name="prerequisites"></a>Requisitos previos

Un dispositivo IoT Edge con el módulo de sensor de temperatura simulado implementado. Si no tiene un dispositivo a punto, siga los pasos descritos en [Implementación del primer módulo IoT Edge en un dispositivo virtual Linux](quickstart-linux.md) para crear uno con el uso de una máquina virtual.

## <a name="understand-iot-edge-metrics"></a>Información sobre las métricas de IoT Edge

Cada dispositivo IoT Edge se basa en dos módulos, los *módulos en tiempo de ejecución*, que administran el ciclo de vida y la comunicación de todos los demás módulos en un dispositivo. Los módulos se llaman **agente de IoT Edge** y **centro de IoT Edge**. Para obtener más información sobre estos módulos, consulte [Información del entorno de ejecución de Azure IoT Edge y su arquitectura](iot-edge-runtime.md).

Ambos módulos en tiempo de ejecución crean métricas que le permiten supervisar de forma remota el rendimiento de un dispositivo de IoT Edge o de sus módulos individuales. El agente de IoT Edge informa sobre el estado de los módulos individuales y el dispositivo host, por lo que crea métricas como cuánto tiempo se ha estado ejecutando correctamente un módulo o la cantidad de RAM y el porcentaje de CPU que se usa en el dispositivo. El centro de IoT Edge informa sobre las comunicaciones en el dispositivo, por lo que crea métricas como el número total de mensajes enviados y recibidos, o el tiempo que se tarda en resolver un método directo. Para obtener la lista completa de métricas disponibles, consulte [Acceso a métricas integradas](how-to-access-built-in-metrics.md).

Ambos módulos exponen automáticamente estas métricas para que pueda crear sus propias soluciones para acceder e informar sobre ellas. Para facilitar este proceso, Microsoft proporciona el [módulo azureiotedge-metrics-collector](https://hub.docker.com/_/microsoft-azureiotedge-metrics-collector) que controla este proceso para aquellos que no tienen o no quieren una solución personalizada. El módulo recopilador de métricas recopila métricas de los dos módulos en tiempo de ejecución y de cualquier otro módulo que quiera supervisar y los transporta fuera del dispositivo.

El módulo recopilador de métricas funciona de dos formas para enviar las métricas a la nube. La primera opción, que usaremos en este tutorial, es enviar las métricas directamente a Log Analytics. La segunda opción, que solo se recomienda si las directivas de red lo requieren, es enviar las métricas a través de IoT Hub y, a continuación, configurar una ruta para pasar los mensajes de métricas a Log Analytics. En cualquier caso, una vez que las métricas están en el área de trabajo de Log Analytics, están disponibles para ver a través de los Libros de Azure Monitor.

## <a name="create-a-log-analytics-workspace"></a>Creación de un área de trabajo de Log Analytics

Un área de trabajo de Log Analytics es necesaria para recopilar los datos de métricas y proporciona un lenguaje de consulta e integración con Azure Monitor para permitirle supervisar los dispositivos.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Busque **Áreas de trabajo de Log Analytics** y selecciónelo.

1. Seleccione **Crear** y, a continuación, siga las instrucciones para crear una nueva área de trabajo.

1. Cuando se cree el área de trabajo, seleccione **Ir al recurso**.

1. En el menú principal, en **Configuración**, seleccione **Administración de agentes**.

1. Copie los valores de **Id. de área de trabajo** y **Clave principal**. Usará estos dos valores más adelante en el tutorial para configurar el módulo recopilador de métricas para enviar las métricas a esta área de trabajo.

## <a name="retrieve-your-iot-hub-resource-id"></a>Recuperación del id. de recurso del centro de IoT

Al configurar el módulo recopilador de métricas, le asigna el id. de recurso de Azure Resource Manager de su centro de IoT. Recupere ese id. ahora.

1. Desde Azure Portal, vaya a su centro de IoT.

1. En **Configuración** del menú de la izquierda, seleccione **Propiedades**.

1. Copie el valor de **Id. de recurso**. Debe tener el formato `/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Devices/IoTHubs/<iot_hub_name>`.

## <a name="deploy-the-metrics-collector-module"></a>Implementación del módulo recopilador de métricas

Implemente el módulo recopilador de métricas en todos los dispositivos que quiera supervisar. Se ejecuta en el dispositivo como cualquier otro módulo y observa sus puntos de conexión asignados para recopilar y enviar métricas a la nube.

Siga estos pasos para implementar y configurar el módulo recopilador:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya al centro de IoT.

1. En el menú de la izquierda, en **Administración automática de dispositivos**, seleccione **IoT Edge**.

1. Seleccione el id. de dispositivo del dispositivo de destino de la lista de dispositivos de IoT Edge para abrir la página de detalles del mismo.

1. De la barra de menú superior, seleccione **Establecer módulos** para abrir la página de implementación del módulo de tres pasos.

1. El primer paso para implementar módulos desde el portal es declarar qué **Módulos** deben estar en un dispositivo. Si está utilizando el mismo dispositivo que creó en el inicio rápido, ya debería ver **SimulatedTemperatureSensor** en la lista. Si no está, agréguela ahora:

   1. Seleccione **Agregar** y elija **Módulo de marketplace** en la lista desplegable.

   1. Busque y seleccione **SimulatedTemperatureSensor**.

1. Agregue y configure el módulo recopilador de métricas:

   1. Seleccione **Agregar** y elija **Módulo de marketplace** en la lista desplegable.
   1. Busque y seleccione **Recopilador de métricas de IoT Edge**.
   1. Seleccione el módulo recopilador de métricas de la lista de módulos para abrir su página de detalles de configuración.
   1. Vaya a la pestaña **Variables de entorno**.
   1. Actualice los siguientes valores:

      | Nombre | Value |
      | ---- | ----- |
      | **ResourceId** | El id. de recurso del centro de IoT que recuperó en una sección anterior. |
      | **UploadTarget** | `AzureMonitor` |
      | **LogAnalyticsWorkspaceId** | El id. del área de trabajo de Log Analytics que recuperó en una sección anterior. |
      | **LogAnalyticsSharedKey** | La clave de Log Analytics que recuperó en una sección anterior. |

   1. Elimine la variable de entorno **OtherConfig**, que es un marcador de posición para las opciones de configuración adicionales que puede agregar en el futuro. No es necesario para este tutorial.
   1. Para guardar los cambios, seleccione **Actualizar**.

1. Seleccione **Siguiente: Rutas** para continuar con el segundo paso para implementar el módulo.

1. El portal agrega automáticamente una ruta para el recopilador de métricas. Usará esta ruta si configuró el módulo recopilador para enviar las métricas a través de IoT Hub, pero en este tutorial se envían las métricas directamente a Log Analytics, por lo que no la necesita. Elimine la ruta **FromMetricsCollectorToUpstream**.

1. Seleccione **Revisar y crear** para continuar al paso final e implementar el módulo.

1. Seleccione **Crear** para finalizar la implementación.

Después de completar la implementación del módulo, volverá a la página de detalles del dispositivo, donde puede ver cuatro módulos enumerados como **Especificado en Implementación**. Los cuatro módulos pueden tardar unos minutos en aparecer como **Notificados por el dispositivo**, lo que significa que se han iniciado correctamente y han notificado su estado a IoT Hub. Actualice la página para ver el estado más reciente.

## <a name="monitor-device-health"></a>Supervisar el estado del dispositivo

Los libros de supervisión de dispositivos pueden tardar hasta quince minutos en poder visualizarse. Una vez que implemente el módulo recopilador de métricas, empezará a enviar mensajes de métricas a Log Analytics, donde se organizan dentro de una tabla. El id. de recurso de IoT Hub que proporcionó vincula las métricas que se ingieren al centro al que pertenecen. Como resultado, los libros seleccionados de IoT Edge pueden recuperar métricas al consultar en la tabla de métricas mediante el id. de recurso.

Azure Monitor proporciona tres plantillas de libro predeterminadas para IoT:

* El libro de **vista de flota de IoT Edge** muestra información general de los dispositivos activos para que pueda identificar los dispositivos incorrectos y explorar en profundidad cómo funciona cada dispositivo. Este libro también muestra las alertas generadas a partir de las reglas de alertas que puede crear.
* El libro de **detalles del dispositivo de IoT Edge** proporciona visualizaciones en torno a tres categorías: mensajería, módulos y host. La vista de mensajería visualiza las rutas de mensaje para un dispositivo e informa sobre el estado general del sistema de mensajería. La vista de módulos muestra el rendimiento de los módulos individuales de un dispositivo. La vista de host muestra información sobre el dispositivo host, incluida la información de versión para los componentes de host y el uso de recursos.
* El libro de **instantáneas de mantenimiento de IoT Edge** mide las señales de dispositivo frente a umbrales configurables para determinar si un dispositivo está en estado de mantenimiento o no. Solo se puede acceder a este libro desde el libro de vista de flota, que pasa los parámetros necesarios para inicializar la instantánea de mantenimiento de un dispositivo determinado.

### <a name="explore-the-fleet-view-and-health-snapshot-workbooks"></a>Exploración de la vista de flota y los libros de instantáneas de mantenimiento

El libro de vista de flota muestra todos los dispositivos y le permite seleccionar dispositivos específicos para ver sus instantáneas de mantenimiento. Siga estos pasos para explorar las visualizaciones del libro:

1. Vuelva a la página del centro de IoT en Azure Portal.

1. Desplácese hacia abajo en el menú principal para encontrar la sección **Supervisión** y seleccione **Libros**.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/workbooks-gallery.png" alt-text="Seleccione Libros para abrir la galería de libros de Azure Monitor.":::

1. Seleccione el libro de **vista de flota de IoT Edge**.

1. Debería ver el dispositivo que ejecuta el módulo recopilador de métricas. El dispositivo aparece en la lista como **correcto** o **incorrecto**.

1. Seleccione el nombre del dispositivo para abrir la **instantánea de mantenimiento de IoT Edge** y ver detalles específicos sobre el mantenimiento del dispositivo.

1. En cualquiera de los gráficos de tiempo, use los iconos de flecha bajo el eje X o haga clic en el gráfico y arrastre el cursor para cambiar el intervalo de tiempo.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/health-snapshot-custom-time-range.png" alt-text="Haga clic y arrastre o use los iconos de flecha de cualquier gráfico para cambiar el intervalo de tiempo.":::

1. Cierre el libro de instantáneas de mantenimiento. Seleccione **Libros** del libro de vista de flota para volver a la galería de libros.

### <a name="explore-the-device-details-workbook"></a>Explore el libro de detalles del dispositivo

El libro de detalles del dispositivo muestra los detalles de rendimiento de un dispositivo individual. Siga estos pasos para explorar las visualizaciones del libro:

1. En la galería de libros, seleccione el libro de **detalles del dispositivo IoT Edge**.

1. La primera página que ve en el libro de detalles del dispositivo es la vista de **mensajería** con la pestaña de **enrutamiento** seleccionada.

   A la izquierda, una tabla muestra las rutas del dispositivo, organizadas por punto de conexión. Para nuestro dispositivo, vemos que el punto de conexión **ascendente**, que es el término especial utilizado para el enrutamiento a IoT Hub, recibe mensajes de la salida **temperatureOutput** del módulo de sensor de temperatura simulado.

   A la derecha, un gráfico realiza un seguimiento del número de clientes conectados a lo largo del tiempo. Puede hacer clic y arrastrar el gráfico para cambiar el intervalo de tiempo.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-messaging-routing.png" alt-text="Seleccione la vista de mensajería para ver el estado de las comunicaciones en el dispositivo.":::

1. Seleccione la pestaña **gráfico** para ver una visualización diferente de las rutas. En la página del gráfico, puede arrastrar y colocar los distintos puntos de conexión para reorganizar el gráfico. Esta característica es útil cuando tiene muchas rutas para visualizar.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-messaging-graph.png" alt-text="Seleccione la vista de gráfico para ver un gráfico interactivo de las rutas del dispositivo.":::

1. La pestaña **mantenimiento** informa de cualquier problema con la mensajería, como mensajes que se anulan o clientes desconectados.

1. Seleccione la vista **módulos** para ver el estado de todos los módulos implementados en el dispositivo. Puede seleccionar cada uno de los módulos para ver qué cantidad de CPU y memoria usan.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-modules-availability.png" alt-text="Seleccione la vista módulos para ver el estado de todos los módulos implementados en el dispositivo.":::

1. Seleccione la vista de **host** para ver información sobre el dispositivo host, incluido su sistema operativo, la versión del demonio de IoT Edge y el uso de recursos.

## <a name="view-module-logs"></a>Vista de los registros del módulo

Después de ver las métricas de un dispositivo, puede profundizar más e inspeccionar los módulos individuales. IoT Edge proporciona asistencia para la solución de problemas en Azure Portal con una característica de registro de módulos en directo.

1. En el libro de detalles del dispositivo, seleccione **Solucionar problemas en directo**.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-troubleshoot-live.png" alt-text="Seleccione el botón de Solucionar problemas en directo en la parte superior derecha del libro de detalles del dispositivo.":::

1. La página de solución de problemas se abre con los registros de **edgeAgent** del dispositivo de IoT Edge. Si seleccionó un intervalo de tiempo específico en el libro de detalles del dispositivo, esa configuración se pasa a la página de solución de problemas.

1. Use el menú desplegable para cambiar a los registros de otros módulos que se ejecutan en el dispositivo. Utilice el botón **Reiniciar** para reiniciar el módulo.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/troubleshoot-device.png" alt-text="Use el menú desplegable para ver los registros de distintos módulos y use el botón de reiniciar para reiniciar los módulos.":::

También se puede acceder a la página de solución de problemas desde la página de detalles de un dispositivo de IoT Edge. Para más información, consulte [Solución de problemas de dispositivos IoT Edge desde Azure Portal](troubleshoot-in-portal.md).

## <a name="next-steps"></a>Pasos siguientes

A medida que explore el resto de los tutoriales, mantenga el módulo recopilador de métricas en los dispositivos y vuelva a estos libros para ver cómo cambia la información a medida que agrega módulos y enrutamiento más complejos.

Pase al siguiente tutorial, donde configurará el entorno de desarrollador para empezar a implementar módulos personalizados en los dispositivos.

> [!div class="nextstepaction"]
> [Desarrollo de módulos IoT Edge para contenedores de Linux](tutorial-develop-for-linux.md)
