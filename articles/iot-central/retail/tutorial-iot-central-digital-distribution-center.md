---
title: 'Tutorial: Centro de distribución digital de Azure IoT | Microsoft Docs'
description: En este tutorial se muestra cómo implementar y usar la plantilla de aplicación del centro de distribución digital para IoT Central.
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: 70ea24b64a1173d2a397d9603d020506f53be37e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434671"
---
# <a name="tutorial-deploy-and-walk-through-the-digital-distribution-center-application-template"></a>Tutorial: Implementación y recorrido por la plantilla de aplicación del centro de distribución digital

Utilice la plantilla de la aplicación del *centro de distribución digital* de IoT Central y la guía de este artículo para desarrollar una solución integral del centro de distribución digital.

   :::image type="content" source="media/tutorial-iot-central-ddc/digital-distribution-center-architecture.png" alt-text="Centro de distribución digital.":::

1. Conjunto de sensores de IoT que envían datos de telemetría a un dispositivo de puerta de enlace
2. Dispositivos de puerta de enlace que envían telemetría y conclusiones agregadas a IoT Central
3. Los datos se enrutan al servicio de Azure que se quiera para su manipulación
4. Se pueden usar servicios de Azure (como ASA o Azure Functions) para volver a formatear flujos de datos y enviarlos a las cuentas de almacenamiento que se desee
5. Los datos procesados se almacenan en un almacenamiento de acceso frecuente para las acciones casi en tiempo real o en un almacenamiento en frío para las mejoras de información adicionales basadas en Machine Learning o en el análisis por lotes. 
6. Logic Apps puede usarse para potenciar varios flujos de trabajo empresariales en aplicaciones empresariales de usuario final.

### <a name="video-cameras"></a>Videocámaras 

Las videocámaras son los sensores principales de este ecosistema de escala empresarial conectado digitalmente. Los avances en aprendizaje automático e inteligencia artificial permiten que el vídeo se convierta en datos estructurados y se procese en Edge antes de enviarlo a la nube. Se pueden usar cámaras IP para capturar imágenes, comprimirlas en la cámara y, a continuación, enviar los datos comprimidos a través de un proceso perimetral para la canalización de análisis de vídeo. También se pueden usar cámaras de visión GigE para capturar imágenes en el sensor y, a continuación, enviar estas imágenes directamente a Azure IoT Edge, que las comprime antes de procesarlas en una canalización de análisis de vídeo. 

### <a name="azure-iot-edge-gateway"></a>Puerta de enlace de Azure IoT Edge

Tanto las "cámaras como sensores" como las cargas de trabajo perimetrales se administran de forma local mediante Azure IoT Edge y la transmisión de la cámara la procesa la canalización de análisis. La canalización de procesamiento de análisis de vídeo en Azure IoT Edge ofrece muchas ventajas, como por ejemplo un menor tiempo de respuesta y poco consumo de ancho de banda, lo que da lugar a una latencia baja y, por ende, un rápido procesamiento de los datos. Solo se envían a la nube los metadatos, la información o las acciones más esenciales para realizar acciones o investigaciones posteriores. 

### <a name="device-management-with-iot-central"></a>Administración de dispositivos con IoT Central
 
Azure IoT Central es una plataforma de desarrollo de soluciones que simplifica la conectividad, la configuración y la administración de dispositivos IoT y la puerta de enlace de Azure IoT Edge. La plataforma reduce significativamente la carga y los costos de la administración, las operaciones y los desarrollos relacionados con los dispositivos IoT. Los clientes y asociados pueden crear soluciones empresariales completas para lograr un bucle de comentarios digital en los centros de distribución.

### <a name="business-insights-and-actions-using-data-egress"></a>Información y acciones de la empresa mediante la salida de datos 

La plataforma de IoT Central proporciona opciones de extensibilidad enriquecidas a través de la exportación continua de datos (CDE) y las API. La información de la empresa que se basa en el procesamiento de los datos de telemetría o de la telemetría sin procesar se suele exportar a una aplicación de línea de negocio preferida, lo que se puede lograr mediante un webhook, Service Bus, un centro de eventos o un almacenamiento de blobs para compilar, entrenar e implementar modelos de Machine Learning y mejorar aún más la información.

En este tutorial, aprenderá lo siguiente:

> [!div class="checklist"]

> * Crear una aplicación del centro de distribución digital.
> * Realizar un recorrido por la aplicación.

## <a name="prerequisites"></a>Prerrequisitos

* No se necesitan requisitos previos específicos para implementar esta aplicación.
* Se recomienda tener una suscripción de Azure, aunque puede probar las opciones sin ella.

## <a name="create-digital-distribution-center-application-template"></a>Creación de una plantilla de aplicación del centro de distribución digital

Cree la aplicación mediante los pasos siguientes:

1. Navegue al sitio de [Compilación Azure IoT Central](https://aka.ms/iotcentral). Después, inicie sesión con una cuenta Microsoft personal, profesional o educativa. Seleccione **Compilar** en la barra de navegación de la izquierda y la pestaña **Venta al por menor**:

   :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-home-page.png" alt-text="Captura de pantalla en la que se muestra cómo crear una aplicación.":::

1. Seleccione **Crear aplicación** en el **centro de distribución digital**.

Para obtener más información, vea [Creación de una aplicación de Azure IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Recorrido por la aplicación. 

Las secciones siguientes le guiarán por las características clave de la aplicación:

### <a name="dashboard"></a>Panel

El panel predeterminado es un portal centrado en el operador del centro de distribución. Northwind Traders es un proveedor de soluciones para centros de distribución ficticio que administra sistemas de cintas transportadoras. 

En este panel, se mostrará una puerta de enlace y una cámara que actúa como dispositivo de IoT. La puerta de enlace proporciona datos de telemetría sobre paquetes; por ejemplo, si son válidos, no válidos, no identificados y su tamaño, junto con propiedades asociadas de dispositivo gemelo. Todos los comandos de nivel inferior se ejecutan en dispositivos IoT, como una cámara. Este panel está preconfigurado para mostrar la actividad crítica de las operaciones de dispositivo del centro de distribución.

El panel está organizado lógicamente para mostrar las funcionalidades de administración de la puerta de enlace de Azure IoT y el dispositivo de IoT.  

* Puede realizar tareas de control y organización de la puerta de enlace.
* Administre todas las cámaras que forman parte de la solución.
* Administre todas las cámaras que forman parte de la solución.
* Administre todas las cámaras que forman parte de la solución.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-dashboard.png" alt-text="Captura de pantalla en la que se muestra el panel del centro de distribución digital.":::

### <a name="device-template"></a>Plantilla de dispositivo

Haga clic en la pestaña Plantillas de dispositivo y verá el modelo de funcionalidad de la puerta de enlace. Un modelo de funcionalidad está estructurado en torno a dos interfaces diferentes, la **cámara** y la **puerta de enlace de distribución digital**.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-devicetemplate1.png" alt-text="Captura de pantalla en la que se muestra la plantilla de dispositivo de puerta de enlace de distribución digital en la aplicación.":::

**Cámara**: esta interfaz organiza todas las funciones de comando específicas de la cámara. 

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-camera.png" alt-text="Captura de pantalla en la que se muestra la interfaz de la cámara en la plantilla de dispositivo de puerta de enlace de distribución digital.":::

**Puerta de enlace de distribución digital**: esta interfaz representa todos los datos de telemetría procedentes de la cámara, las propiedades de dispositivos gemelos definidas en la nube y la información de la puerta de enlace.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-devicetemplate1.png" alt-text="Captura de pantalla en la que se muestra la interfaz de la puerta de enlace de distribución digital en la plantilla de dispositivo de puerta de enlace de distribución digital.":::

### <a name="gateway-commands"></a>Comandos de puerta de enlace

Esta interfaz organiza todas las funciones de comando de la puerta de enlace.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-camera.png" alt-text="Captura de pantalla en la que se muestra la interfaz de comandos de puerta de enlace en la plantilla de dispositivo de puerta de enlace de distribución digital.":::

### <a name="rules"></a>Reglas

Seleccione la pestaña Reglas para ver dos reglas diferentes que existen en esta plantilla de aplicación. Estas reglas se configuran para enviar notificaciones por correo electrónico a los operadores para realizar más investigaciones.

 **Too many invalid packages alert** (demasiadas alertas de paquetes no válidos): esta regla se desencadena cuando la cámara detecta un número elevado de paquetes no válidos que fluyen a través del sistema de cintas transportadoras.

**Large package** (paquete grande): esta regla se desencadenará si la cámara detecta un paquete grande del que no se puede inspeccionar la calidad. 

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-rules.png" alt-text="Captura de pantalla en la que se muestra la lista de reglas en la aplicación del centro de distribución digital.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine la plantilla de la aplicación. Para ello, vaya a **Administración** > **Configuración de la aplicación** y haga clic en **Eliminar**.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-cleanup.png" alt-text="Captura de pantalla en la que se muestra cómo eliminar la aplicación cuando haya terminado con ella.":::

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la arquitectura de la solución de centro de distribución digital:

> [!div class="nextstepaction"]
> [concepto de centro de distribución digital](./architecture-digital-distribution-center.md)
