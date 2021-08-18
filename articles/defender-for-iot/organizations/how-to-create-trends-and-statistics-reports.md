---
title: Generación de informes de tendencias y estadísticas
description: Obtenga conclusiones sobre la actividad de red, las estadísticas y las tendencias mediante los widgets de tendencias y estadísticas de Defender for IoT.
ms.date: 2/21/2021
ms.topic: how-to
ms.openlocfilehash: b4539e20ff485f1b6be69fee8e6849298540adcb
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113020554"
---
# <a name="sensor-trends-and-statistics-reports"></a>Informes de tendencias y estadísticas del sensor

Puede crear gráficos de widgets y gráficos circulares para obtener información sobre estadísticas y tendencias de red. Los widgets se pueden agrupar en paneles definidos por el usuario.

> [!NOTE]
> Los administradores y analistas de seguridad pueden crear informes de tendencias y estadísticas.

El panel consta de widgets que describen gráficamente los siguientes tipos de información:

- Tráfico por puerto
- Tráfico principal por puerto
- Ancho de banda de canal
- Ancho de banda total
- Conexión TCP activa
- Ancho de banda principal por VLAN
- Dispositivos:
  - Dispositivos nuevos
  - Dispositivos ocupados
  - Dispositivos por proveedor
  - Dispositivos por SO
  - Número de dispositivos por VLAN
  - Dispositivo desconectados
- Caídas de conectividad por horas
- Alertas de incidentes por tipo
- Acceso de tablas de base de datos
- Widgets de disección de protocolos
- DELTAV
  - Distribución de operaciones de DeltaV ROC
  - Eventos de DeltaV ROC por nombre
  - Eventos de DeltaV por hora
- AMS
  - Tráfico de AMS por puerto de servidor
  - Tráfico de AMS por comando
- Dirección IP y Ethernet:
  - Tráfico de direcciones IP y Ethernet por servicio de CIP
  - Tráfico de direcciones IP y Ethernet por clase de CIP
  - Tráfico de direcciones IP y Ethernet por comando
- OPC:
  - Principales operaciones de administración de OPC
  - Principales operaciones de E/S de OPC
- Siemens S7:
  - Tráfico S7 por función de control
  - Tráfico S7 por subfunción
- VLAN
  - Número de dispositivos por VLAN
  - Ancho de banda principal por VLAN
- 60870-5-104
  - Tráfico IEC-60870 por ASDU
- BACNET
  - Servicios BACnet
- DNP3
  - Tráfico DNP3 por función
- SRTP:
  - Tráfico SRTP por código de servicio
  - Errores SRTP por día
- SuiteLink:
  - Principales etiquetas de SuiteLink consultadas
  - Comportamiento de las etiquetas numéricas de SuiteLink
- Tráfico IEC-60870 por ASDU
- Tráfico DNP3 por función
- Tráfico MMS por servicio
- Tráfico Modbus por función
- Tráfico OPC-UA por servicio

> [!NOTE]
>  La hora en los widgets se establece según la hora del sensor.

## <a name="create-reports"></a>Crear informes

Para ver paneles y widgets:

En el menú lateral, seleccione **Tendencias y estadísticas**.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Captura de pantalla de una investigación.":::

De forma predeterminada, los resultados se muestran para las detecciones de los últimos 7 días. Puede usar las herramientas de filtro para cambiar este intervalo. Por ejemplo, una búsqueda de texto libre.

## <a name="create-a-dashboard"></a>Creación de un panel

Para crear un panel, seleccione el menú desplegable **Panel**. Puede crear y agregar todos los widgets que quiera a un panel.

Puede crear paneles personalizados con las opciones siguientes:

- Adición de un widget al panel

- Eliminación de un widget del panel

- Modificación del filtro de un widget

- Cambio del tamaño de un widget

- Cambio de la ubicación de un widget

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="Cambio de la ubicación de un widget.":::

Para crear un panel personalizado:

1. Seleccione **Tendencias y estadísticas** en el panel de la izquierda.

1. Seleccione el menú desplegable **Seleccionar panel** y después el botón **Crear panel**.

1. Escriba un nombre descriptivo para el nuevo panel y seleccione **Crear**.

1. Seleccione **Agregar widget** en la parte superior izquierda de la página.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Selección del widget en el almacén de widgets.":::

1. Los widgets de **seguridad** y **operativos** están disponibles en la parte superior derecha de la ventana. Elija entre varias categorías y protocolos. Con cada widget aparece una breve descripción con un gráfico en miniatura. Use la barra de desplazamiento para ver todos los widgets disponibles.

1. Seleccione un widget con el botón **Haga clic para agregar**. El widget se muestra inmediatamente en el panel.

Para eliminar un panel:

1. Seleccione el nombre del panel en el menú desplegable.

1. Seleccione el icono **Eliminar** y, después, **Aceptar**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="Selección del icono Eliminar para eliminar el panel.":::

Para editar el nombre de un panel:

1. Seleccione el nombre del panel en el menú desplegable.

1. Seleccione el icono **Editar**.
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="Selección del icono Editar para editar el nombre del panel.":::

1. Escriba un nombre nuevo para el panel y seleccione **Guardar**.
 
Para establecer el panel predeterminado:

1. Seleccione el nombre del panel en el menú desplegable.

1. Seleccione el icono de **estrella** para seleccionar el panel que se va a establecer como el predeterminado.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="Selección del icono de estrella para elegir el panel predeterminado."::: 

Para modificar el filtrado de datos en un widget:

1. Seleccione el icono **Filtro**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="Selección del icono Filtro para establecer los parámetros del widget.":::

1. Edite los parámetros necesarios.

1. Seleccione **Aceptar**.

Para eliminar un widget:

- Seleccione el icono :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false":::.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="Selección de la X para eliminar el widget.":::

## <a name="creating-widgets"></a>Creación de widgets 

El almacén de widgets le permite seleccionar widgets por categoría y protocolo. Puede mostrar los widgets de **seguridad** u **operativos** disponibles si los selecciona.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Selección del widget en el almacén de widgets.":::

Cada widget contiene información específica sobre el tráfico del sistema, estadísticas de red y protocolo, dispositivo e información de alertas. Cuando no hay datos para un widget se muestra un mensaje.

Puede quitar un segmento en un gráfico circular para ver la importancia relativa de los segmentos restantes con mayor claridad. Seleccione el nombre del segmento en la leyenda de la parte inferior de la pantalla para hacerlo.

En las secciones siguientes se presentan ejemplos de casos de uso para algunos de los widgets.

### <a name="busy-devices-widget"></a>Widget de dispositivos ocupados

En este widget se enumeran los cinco dispositivos más ocupados. En el modo de **edición**, puede filtrar por protocolos conocidos.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="Vista del widget de dispositivos ocupados.":::

### <a name="total-bandwidth-widget"></a>Widget de ancho de banda total

Este widget realiza el seguimiento del ancho de banda en Mbps (megabits por segundo). El ancho de banda se indica en el eje Y y la fecha aparece en el eje X. El modo de **edición** le permite filtrar los resultados según el cliente, el servidor, el puerto del servidor o la subred. Cuando se mantiene el cursor sobre el gráfico, aparece una información sobre herramientas.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="Vista del widget de ancho de banda total.":::

### <a name="channels-bandwidth-widget"></a>Widget de ancho de banda de canales

En este widget se muestran los cinco canales de tráfico principales. Puede filtrar por dirección y establecer el número de resultados presentados. Seleccione la flecha abajo para mostrar más canales.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="Vista del widget de ancho de banda de canales.":::

### <a name="traffic-by-port-widget"></a>Widget de tráfico por puerto

En este widget se muestra el tráfico por puerto, que se indica mediante un gráfico circular con cada puerto designado con un color diferente. La cantidad de tráfico en cada puerto es proporcional al tamaño de su parte del gráfico circular.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="Vista del widget de tráfico por puerto.":::

### <a name="new-devices-widget"></a>Widget de dispositivos nuevos

En este widget se muestra el gráfico de barras de nuevos dispositivos, que indica cuántos dispositivos nuevos se han detectado en una fecha concreta.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="Vista del widget de dispositivos nuevos.":::

### <a name="protocol-dissection-widgets"></a>Widgets de disección de protocolos

En este widget se muestra un gráfico circular que proporciona una visión del tráfico por protocolo, dividido por códigos de función y servicios. El tamaño de cada segmento del gráfico circular es proporcional a la cantidad de tráfico en relación con los demás segmentos.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="Vista del widget de disección de protocolos.":::

### <a name="active-tcp-connections-widget"></a>Widget de conexiones TCP activas

En este widget se muestra un gráfico que representa el número de conexiones TCP activas en el sistema.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="Vista del widget de conexiones TCP activas.":::

### <a name="incident-by-type-widget"></a>Widget de incidente por tipo

En este widget se muestra un gráfico circular que representa el número de incidentes por tipo. Es el número de alertas generadas por cada motor durante un período de tiempo predefinido.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="Vista del widget de incidentes por tipo.":::

## <a name="devices-by-vendor-widget"></a>Widget de dispositivos por proveedor

En este widget se muestra un gráfico circular que representa el número de dispositivos por proveedor. El número de dispositivos para un proveedor específico es proporcional al tamaño de la parte del proveedor de ese dispositivo en relación con otros.

## <a name="number-of-devices-per-vlan-widget"></a>Widget de número de dispositivos por VLAN

En este widget se muestra un gráfico circular que representa el número de dispositivos detectados por VLAN. El tamaño de cada segmento del gráfico circular es proporcional al número de dispositivos detectados en relación con los demás segmentos.

Cada VLAN aparece con la etiqueta VLAN asignada por el sensor o el nombre que haya agregado manualmente.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="Vista del widget de número de dispositivos.":::

### <a name="top-bandwidth-by-vlan-widget"></a>Widget de ancho de banda principal por VLAN

En este widget se muestra el consumo de ancho de banda por VLAN. De forma predeterminada, el widget muestra cinco VLAN con el mayor uso de ancho de banda.

Puede filtrar los datos por el período presentado en el widget. Seleccione la flecha abajo para mostrar más resultados.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="Vista del widget de ancho de banda principal por VLAN.":::

## <a name="system-report"></a>Informe del sistema

Para descargar el informe del sistema: 

1. En el menú lateral, seleccione **Tendencias y estadísticas**.

1. Seleccione **Informe del sistema** en la esquina superior derecha. El informe se descargará de forma automática.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="Selección del botón Informe del sistema para descargar una copia del informe del sistema.":::

El informe del sistema es un archivo PDF que contiene todos los datos del sistema:

  - Dispositivos

  - Alertas

  - Información de directivas de red

## <a name="devices-in-a-system-report"></a>Dispositivos en un informe del sistema 

En el informe del sistema se muestra una lista de todos los dispositivos y su información. Por ejemplo, el tipo, el nombre y los protocolos usados. En el informe del sistema también se muestra una lista de dispositivos por proveedor.

## <a name="alerts-in-system-report"></a>Alertas en el informe del sistema 

En el informe del sistema se muestra una lista de todas las alertas con su información, por ejemplo, la fecha y la gravedad.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="Vista de las alertas de los informes del sistema.":::

## <a name="network-information-in-system-report"></a>Información de red en el informe del sistema 

En el informe del sistema se muestra la línea de base de red en detalle. Por ejemplo, el código de función DNP3 y los puertos abiertos por conexión.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="Vista de la función DNP3 del informe del sistema.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="Vista del informe de puerto abierto por conexión.":::

## <a name="see-also"></a>Vea también

[Informes de evaluación de riesgos](how-to-create-risk-assessment-reports.md)
[Consultas de minería de datos del sensor](how-to-create-data-mining-queries.md)
[Informes de vectores de ataque](how-to-create-attack-vector-reports.md)
