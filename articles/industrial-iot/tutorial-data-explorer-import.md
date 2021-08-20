---
title: Uso de IoT industrial para extraer datos en Azure Data Explorer
description: En este tutorial, aprenderá a mover datos de IIoT a ADX y a crear un panel para verlos.
author: v-condav
ms.author: v-condav
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 6/16/2021
ms.openlocfilehash: 4ab409c067d83e44cb22c3a22589eca0a4a7785a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291278"
---
# <a name="tutorial-using-industrial-iot-to-pull-data-into-azure-data-explorer"></a>Tutorial: Uso de Industrial IoT para extraer datos en Azure Data Explorer

La plataforma Azure Industrial IoT (IIoT) combina módulos perimetrales y microservicios en la nube con servicios de plataforma como servicio (PaaS) de Azure para proporcionar funcionalidades de detección de recursos industriales y adquisición de datos de recursos. En un entorno industrial, el estándar de facto para esta tarea es OPC UA. En este tutorial se muestra cómo configurar Azure para su uso con OPC UA, con un caso de uso simple como ejemplo: la supervisión de condiciones basada en la nube.

En este tutorial, aprenderá a:
- Obtener una cuenta de Azure e implementar una instancia de IoT Hub.
- Implementar IoT Edge
- Instalar y configurar OPC Publisher
- Configurar una base de datos de serie temporal.
- Crear un panel.
- Crear un grafo de datos entrantes.

## <a name="requirements"></a>Requisitos

Antes de empezar este tutorial, necesitará una cuenta de Azure. Si no tiene una, puede obtenerla gratis si [hace clic aquí](https://azure.microsoft.com/free/).

## <a name="deploy-an-s1-iot-hub"></a>Implementación de una instancia de IoT Hub S1

Lo primero que debe hacer con la suscripción de Azure es ir al [portal](../azure-portal/azure-portal-quickstart-center.md) e [implementar una instancia de IoT Hub S1](../iot-hub/iot-hub-create-through-portal.md). La cuenta gratuita de Azure le proporciona 400 000 mensajes de 4K al día. Como puede insertar unas 15 etiquetas de datos de OPC UA PubSub en un único mensaje de 4K, tendrá 6 millones de etiquetas de datos al día, lo suficiente para este caso de uso. Después de implementar la instancia de IoT Hub, copie la cadena de conexión principal *iothubowner* (en directivas de acceso compartido, iothubowner). La necesitará para la instalación de IoT Edge.

## <a name="deploy-iot-edge"></a>Implementación de IoT Edge

Implemente IoT Edge mediante el [Instalador de IoT Edge](https://lnkd.in/ga6Ew4X) en un equipo o máquina virtual Windows 10. Este equipo o máquina virtual debe tener conectividad a Internet y también tener acceso a las máquinas o al software del adaptador de conectividad industrial (dataFEED, Zenon, Kepware, etc.) que quiera usar. Puede anidar dispositivos de IoT Edge, en caso de que en la instalación se usen varias capas de red desde la red OT (donde se encuentran las máquinas) a la conexión a Internet. También puede ejecutar IoT Edge de forma nativa en Linux mediante el instalador [on GitHub](https://github.com/Azure/Industrial-IoT-Gateway-Installer/blob/master/Releases/Linux.zip).

Al ejecutar el instalador de IoT Edge, active la casilla **Use Docker Desktop** (Usar Docker Desktop), mantenga seleccionado el botón de radio **Configure IoT Edge for Azure Industrial Cloud Platform (PaaS)** (Configurar IoT Edge para Azure Industrial Cloud Platform [PaaS]) y pegue la cadena de conexión *iothubowner* en el cuadro de texto proporcionado. De forma automática, el instalador rellena previamente el nombre del equipo en el que se realiza la ejecución con el nombre de la instancia de Edge, pero puede cambiarlo si es necesario. Presione **Instalar**. Si no se detecta Docker Desktop, el instalador lo descargará automáticamente. Instálelo y asegúrese de configurarlo con Subsistema de Windows para Linux V2 (WSL2) a fin de obtener el mejor rendimiento. Una vez que se complete la instalación de Docker Desktop, reinicie el instalador de IoT Edge, configúrelo de nuevo y presione Instalar una segunda vez. Después, vaya a la pestaña **IoT Edge** de la página **IoT Hub** en Azure Portal y espere hasta que se muestre el dispositivo de IoT Edge recién instalado (es posible que tenga que actualizar varias veces).

## <a name="install-and-configure-opc-publisher"></a>Instalación y configuración de OPC Publisher

A continuación, instale y configure OPC Publisher, que se usa para estandarizar y normalizar todos los datos de telemetría mediante OPC UA PubSub en Edge. Se usará la codificación JSON de OPC UA, ya que se puede ingerir directamente en bases de datos en la nube sin necesidad de un servicio en la nube independiente para traducir el formato binario de nuevo a algo que la base de datos en la nube pueda entender. El uso de este formato estándar del sector reduce la sobrecarga de administración y el costo en la nube. Este es un punto importante: muchos le dirán que han encontrado una codificación de telemetría más eficaz, pero invariablemente menosprecian el costo y el aspecto de administración de la traducción de la telemetría a un formato listo para la base de datos en la nube a escala en la nube. 

Ahora vaya a la página **Getting Started** (Introducción) de OPC Publisher y siga las instrucciones. En **Opciones de creación del contenedor**, especifique lo siguiente.

```
{
    "Hostname": "OPCPublisher",
    "Cmd": [
        "PkiRootPath=/appdata/pki", 
        "--lf=/appdata/publisher.log", 
        "--pf=/appdata/publishednodes.json",
        "--aa",
        "--di=60",
        "--me=Json",
        "--mm=PubSub"
    ],
    "HostConfig": {
        "Binds": [
            "/c/IoTEdgeMapping:/appdata"
        ]
    }
}
```

A continuación, configure los nodos de datos OPC UA de las máquinas (o el software del adaptador de conectividad) desde los que se generan mensajes de telemetría de serie temporal en la nube. Para ello, copie el archivo de plantilla ***publishednodes.json** _ desde aquí en _*_C:\IoTEdgeMapping_*_ y rellénelo con el valor _ *EndpointURL** del servidor OPC UA y los identificadores de los nodos OPC UA de los datos que quiera enviar a la nube. Si no sabe qué enviar, el nodo de datos con el identificador ns=0;i=2258 es la hora actual del servidor, que cambia cada segundo. OPC UA solo publica los cambios de datos si los datos se han modificado. Pero OPC Publisher también admite la lectura y el envío de datos a intervalos específicos, aunque no hayan cambiado.

Cuando haya terminado de editar el archivo, reinicie OPC Publisher mediante el comando de línea de comandos "**iotedge restart OPCPublisher**"; OPC Publisher intentará conectarse a todos los servidores de OPC UA que haya especificado y publicar todos los nodos enumerados. La experiencia ha demostrado que primero debe usar un cliente de OPC UA como UA Expert desde el equipo en el que se ejecuta IoT Edge para asegurarse de que puede conectarse a los servidores de OPC UA y también probar la dirección IP del servidor OPC UA en EndpointURL en lugar del nombre de host, en caso de que el DNS no funcione. Ahora vuelva a la página de la instancia de **IoT Hub** en Azure Portal y compruebe que puede ver los mensajes de **Dispositivo a nube** que se reciben en la pestaña **Información general**.

## <a name="set-up-the-time-series-database"></a>Configuración de la base de datos de serie temporal

Ahora configure la base de datos de serie temporal en la nube que recibirá la telemetría de OPC UA. Es mejor usar Azure Data Explorer para esto, ya que utiliza varias capas de base de datos para obtener un rendimiento óptimo, incluye funcionalidades enriquecidas de análisis y panel, e incluso admite predicciones y detección de anomalías. Implemente una instancia desde Azure Portal, seleccione la SKU de carga de trabajo de desarrollo/pruebas y no olvide habilitar la ingesta de streaming en las configuraciones.

OPC UA identifica de forma única una etiqueta de datos de telemetría mediante los valores del servidor de OPC UA **DatasetWriterID** y **ExpandedNodeID** (que contiene el espacio de nombres y el identificador del nodo). Además, la marca de tiempo cuando el servidor de OPC UA lee los datos antes de que estén disponibles para un cliente conectado se denomina *SourceTimeStamp*. Por tanto, para crear una serie temporal de telemetría de OPC UA, hay que proporcionar los valores **DatasetWriterID** y **ExpandedNodeID**, el valor de datos leído y **SourceTimeStamp** en una tabla de base de datos. Esto es lo que creará ahora, mediante Azure Data Explorer. Tenga en cuenta que el tipo de valor de datos (float, int, etc.) también se puede incluir en los mensajes de OPC UA PubSub; esto se denomina *codificación reversible* en la especificación de OPC UA.

En primer lugar, cree una base de datos con el período de retención de datos de 10 años predeterminado, que debería ser suficiente para este caso de uso. Después, configure la ingesta de streaming directamente desde la instancia de IoT Hub; para ello, haga clic en **Crear conexión de datos** y seleccione **IoT Hub**. Asigne un nombre a la conexión y elija la instancia de IoT Hub en la lista desplegable. Seleccione *iothubowner* en **Directiva de acceso compartido** y *$Default* para el **Grupo de consumidores**. Deje **Nombre de tabla** en blanco por ahora, pero seleccione *MULTILINE JSON* en **Formato de datos** y escriba *opcua_mapping* en **Nombre de asignación**. Seleccione **Crear**.

## <a name="create-the-database-tables"></a>Creación de las tablas de base de datos

Cree las tablas necesarias en la nueva base de datos. En primer lugar, cree una tabla de almacenamiento provisional con una sola columna para recibir los mensajes de telemetría JSON de OPC UA PubSub. Azure Data Explorer usa el lenguaje de consulta Kusto (KQL) integrado, que se documenta aquí. Seleccione la pestaña **Consulta** y escriba lo siguiente.

```
.create table opcua_raw(payload: dynamic)

```

Cree una asignación a la ingesta de telemetría *opcua_mapping* mediante la instancia de IoT Hub que ha configurado antes mediante el comando siguiente.

```
.create table opcua_raw ingestion json mapping "opcua_mapping" @'[{"column":"payload","path":"$","datatype":"dynamic"}]'

```

Actualice la ingesta de telemetría con el nombre de la tabla que acaba de crear; para ello, seleccione la pestaña **Ingesta de datos**, después el nombre de la ingesta de datos, seleccione **Editar**, escriba *opcua_raw* en **Nombre de tabla** y presione **Actualizar**. Luego, vuelva a la pestaña **Consulta** y espere unos minutos a que los primeros mensajes de telemetría entren en la tabla. Para comprobarlo, consulte 10 filas de la tabla.

```
opcua_raw
| take 10
```

OPC UA PubSub admite el procesamiento por lotes de telemetría en un único mensaje mediante el uso de conjuntos de datos, que se identifican mediante **DataSetWriterID**. Estos conjuntos de datos "no deben estar dispuestos en lotes" en filas independientes de la base de datos. Para ello, use el operador *mv-expand* y expanda el código JSON ingerido en una tabla intermedia que primero tendrá que crear.

```
.create table opcua_intermediate(DataSetWriterID: string, payload: dynamic)
```

Ahora cree la siguiente función para realizar la expansión.

```
.create-or-alter function OPCUARawExpand() {
    opcua_raw
    |mv-expand records = payload.Messages
    |project 
        DataSetWriterID = tostring(records["DataSetWriterId"]),
        Payload = todynamic(records["Payload"])
}
```

Después, aplique la nueva función para actualizar la tabla intermedia.

```
.alter table opcua_intermediate policy update @'[{"Source": "opcua_raw", "Query": "OPCUARawExpand()", "IsEnabled": "True"}]'
```

Compruebe que la nueva tabla se rellena correctamente.

```
opcua_intermediate
| take 10
```

 Cree la tabla de telemetría de OPC UA final y, para rellenarla, expanda aún más cada entrada del conjunto datos de OPC UA.

```
.create table opcua_telemetry (DataSetWriterID: string, ExpandedNodeID: string, Value: dynamic, SourceTimestamp: datetime)
```

Cree la función que se muestra a continuación.

```
.create-or-alter function OPCUADatasetExpand() {
    opcua_intermediate
    | mv-apply payload on (
        extend key = tostring(bag_keys(payload)[0])
        | extend p = payload[key]
        | project ExpandedNodeId = key, Value = todynamic(p.Value), SourceTimestamp = todatetime(p.SourceTimestamp)
    )
}
```

Aplique la nueva función para actualizar la tercera y última tabla.

```
.alter table opcua_telemetry policy update @'[{"Source": "opcua_intermediate", "Query": "OPCUADatasetExpand()", "IsEnabled": "True"}]'
```

Para examinar la tabla de telemetría de OPC UA, use el comando siguiente.

```
opcua_telemetry
| take 10
```

## <a name="create-line-graph-of-the-data"></a>Creación de un gráfico de líneas de los datos

Ahora, se creará un gráfico de líneas de los datos mediante su conversión en números de punto flotante. Si se produce un error en la conversión, los datos se omiten. Para esta operación, use el panel Azure Data Explorer hospedado. Haga clic en **Open in Web UI** (Abrir en la interfaz de usuario web) y, en la nueva ventana, haga clic en **Paneles**, seleccione **Crear nuevo panel** y elija **Agregar icono**. Después, seleccione **Origen de datos** y escriba el nombre de la instancia de Azure Data Explorer con el formato https://<YourInstanceName>.<Your RegionName>.kusto.windows.net. Seleccione la base de datos y presione **aplicar**. Después, escriba la siguiente consulta.

```
opcua_telemetry
| project SourceTimestamp, yaxis = todouble(Value), DataSetWriterID,  ExpandedNodeID
```

Haga clic en la pestaña **Visual** y seleccione *yaxis* para **Y Columns** (Columnas Y), *SourceTimeStamp* para **X Columns** (Columnas X) y *DataSetWriterID* para **Series columns** (Columnas de serie).

Ahora tiene un atractivo panel de telemetría para la supervisión de condiciones de los datos de casos de uso en la nube.