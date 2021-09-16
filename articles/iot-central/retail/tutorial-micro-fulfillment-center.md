---
title: 'Tutorial: Centro de microcumplimiento de Azure IoT | Microsoft Docs'
description: En este tutorial se muestra cómo implementar y usar la plantilla de aplicación del centro de microcumplimiento para Azure IoT Central.
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 09/01/2021
ms.openlocfilehash: 9eb70a08679427af9e91ffd6df3cd8f827cc76cd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123476311"
---
# <a name="tutorial-deploy-and-walk-through-the-micro-fulfillment-center-application-template"></a>Tutorial: Implementación y recorrido de la plantilla de aplicación del centro de microcumplimiento

Utilice la plantilla de la aplicación del *centro de microcumplimiento* de IoT Central y la guía de este artículo para desarrollar una solución integral del centro de microcumplimiento.

![Análisis de tienda de Azure IoT Central](./media/tutorial-micro-fulfillment-center-app/micro-fulfillment-center-architecture-frame.png)

1. Conjunto de sensores de IoT que envían datos de telemetría a un dispositivo de puerta de enlace
2. Dispositivos de puerta de enlace que envían telemetría y conclusiones agregadas a IoT Central
3. Exportación continua de datos al servicio de Azure que se quiera para su manipulación
4. Los datos se pueden estructurar en el formato que se quiera y enviarse a un servicio de almacenamiento
5. Las aplicaciones empresariales pueden consultar datos y generar conclusiones que impulsen las operaciones comerciales

### <a name="robotic-carriers"></a>Operadores robóticos

Es probable que una solución de centro de micrologística tenga un gran conjunto de operadores robóticos que generen diferentes tipos de señales de telemetría. Estas señales se pueden ingerir mediante un dispositivo de puerta de enlace, agregar y enviar a IoT Central como se refleja en el lado izquierdo del diagrama de la arquitectura.  

### <a name="condition-monitoring-sensors"></a>Sensores de supervisión del estado

Una solución de IoT comienza con un conjunto de sensores que capturan señales significativas desde dentro del centro de logística. Se refleja en diferentes tipos de sensores en el extremo izquierdo del diagrama de arquitectura anterior.

### <a name="gateway-devices"></a>Dispositivos de puerta de enlace

Muchos sensores de IoT pueden alimentar señales sin procesar directamente en la nube o en un dispositivo de puerta de enlace situado cerca de ellas. El dispositivo de puerta de enlace realiza la agregación de datos en el perímetro antes de enviar conclusiones de resumen a una aplicación de IoT Central. Los dispositivos de puerta de enlace también son responsables de la retransmisión de operaciones de comando y control a los dispositivos de sensor cuando proceda. 

### <a name="iot-central-application"></a>Aplicación de IoT Central

La aplicación de Azure IoT Central ingiere datos de diferentes tipos de sensores de IoT, robots, así como dispositivos de puerta de enlace, en el entorno del centro de logística y genera un conjunto de conclusiones significativas.

Azure IoT Central también ofrece una experiencia personalizada para el operador de tienda que le permite supervisar y administrar de forma remota los dispositivos de infraestructura.

### <a name="data-transform"></a>Transformación de datos

Puede configurarse la aplicación de Azure IoT Central dentro de una solución para exportar conclusiones sin procesar o agregadas a un conjunto de servicios de plataforma como servicio (PaaS) de Azure que pueden realizar manipulación de datos y enriquecer estas conclusiones antes de colocarlas en una aplicación empresarial. 

### <a name="business-application"></a>Aplicación empresarial

Los datos de IoT se pueden usar para potenciar diferentes tipos de aplicaciones empresariales implementadas en un entorno minorista. El administrador del centro de logística o un empleado pueden utilizar estas aplicaciones para visualizar conclusiones empresariales y tomar medidas significativas en tiempo real. Para obtener información sobre cómo crear un panel de Power BI en tiempo real para el equipo comercial, consulte el [tutorial](./tutorial-in-store-analytics-create-app.md).

En este tutorial, aprenderá:

> [!div class="checklist"]

> * Procedimientos para implementar la plantilla de la aplicación
> * Procedimientos para usar la plantilla de aplicación

## <a name="prerequisites"></a>Requisitos previos

* No se necesitan requisitos previos específicos para implementar esta aplicación.
* Puede usar el plan gratuito o una suscripción de Azure.

## <a name="create-micro-fulfillment-application"></a>Creación de una aplicación de micrologística

Cree la aplicación mediante los pasos siguientes:

1. Navegue al sitio de [Compilación Azure IoT Central](https://aka.ms/iotcentral). Después, inicie sesión con una cuenta Microsoft personal, profesional o educativa. Seleccione **Compilar** en la barra de navegación de la izquierda y la pestaña **Venta al por menor**:

   :::image type="content" source="media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png" alt-text="Captura de pantalla en la que se muestra cómo crear una aplicación.":::

1. Seleccione **Crear aplicación** en el **centro de microcumplimiento**.

## <a name="walk-through-the-application"></a>Recorrido por la aplicación. 

Las secciones siguientes le guiarán por las características clave de la aplicación:

Después de implementar correctamente la plantilla de aplicación, verá al **panel del centro de micrologística de Northwind Traders**. Northwind Traders es un minorista ficticio que tiene un centro de micrologística que se administrando en esta aplicación de Azure IoT Central. En este panel se ven información y datos de telemetría de los dispositivos de esta plantilla junto con un conjunto de comandos, trabajos y acciones que se pueden realizar. El panel se divide lógicamente en dos secciones. A la izquierda, es posible supervisar las condiciones del entorno dentro de la estructura logística y, a la derecha, se puede supervisar el estado de un operador robótico dentro de la instalación.  

Desde el panel se puede:
   * Consultar los datos de telemetría de dispositivos, como el número de recogidas, el número de pedidos procesados y propiedades como el estado del sistema de la estructura.  
   * Ver el plan de la planta y la ubicación de los operadores robóticos en la estructura logística.
   * Desencadenar comandos, como restablecer el sistema de control, actualizar el firmware del operador y volver a configurar la red.

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/mfc-dashboard-1.png" alt-text="Captura de pantalla que muestra la mitad superior del panel del centro de micrologística de Northwind Traders.":::
   * Vea un ejemplo del panel que los operadores pueden usar para supervisar las condiciones del centro logístico.
   * Supervise el estado de las cargas que se ejecutan en el dispositivo de puerta de enlace del centro logístico.

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/mfc-dashboard-2.png" alt-text="Captura de pantalla que muestra la mitad inferior del panel del centro de micrologística de Northwind Traders.":::


### <a name="device-template"></a>Plantilla de dispositivo

Si seleccione la pestaña de plantillas de dispositivos, verá que hay dos tipos de dispositivo diferentes que forman parte de la plantilla: 
   * **Operador robótico**: esta plantilla de dispositivo representa la definición de un operador robótico en funcionamiento que se ha implementado en la estructura logística y realiza las operaciones adecuadas de almacenamiento y recuperación. Si selecciona la plantilla, verá que el robot envía datos del dispositivo, como la temperatura y la posición del eje, y propiedades, como el estado del operador robótico. 
   * **Supervisión de las condiciones de estructura**: esta plantilla de dispositivo representa una recopilación de dispositivos que permite supervisar la condición del entorno, así como el dispositivo de puerta de enlace que hospeda varias cargas de trabajo perimetrales para potenciar el centro de micrologística. El dispositivo envía datos de telemetría, como la temperatura, el número de recogidas y el número de pedidos. También envía información sobre el estado y el estado de las cargas de trabajo de proceso que se ejecutan en el entorno. 

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/device-templates.png" alt-text="Plantillas de dispositivo del centro de microcumplimiento.":::



Si selecciona la pestaña del grupo dispositivos, también verá que estas plantillas de dispositivo crean automáticamente grupos de dispositivos.

### <a name="rules"></a>Reglas

En la pestaña **Rules** (Reglas), se ve una regla de ejemplo que existe en la plantilla de aplicación para supervisar las condiciones de temperatura del operador robótico. Esta regla se puede usar para avisar al operador si un robot específico de la instalación se está sobrecalentando y se debe desconectar para mantenimiento. 

Use la regla de ejemplo como inspiración para definir las reglas más adecuadas para sus funciones empresariales.

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/rules.png" alt-text="Captura de pantalla de la pestaña Reglas":::

### <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine la plantilla. Vaya a **Administration** > **Application settings** (Administración > Configuración de la aplicación) y seleccione **Delete** (Eliminar).

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/delete.png" alt-text="Captura de pantalla de la página de configuración de la aplicación del centro de microcumplimiento.":::

## <a name="next-steps"></a>Pasos siguientes

Más información sobre:

> [!div class="nextstepaction"]
> [arquitectura de la solución de centro de micrologística](./architecture-micro-fulfillment-center.md)
