---
title: 'Tutorial: Administración inteligente del inventario de Azure IoT | Microsoft Docs'
description: En este tutorial, se muestra cómo implementar y usar la plantilla de aplicación de administración inteligente del inventario para IoT Central.
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: 747bcaffd1e24937580dcf95f352a34f66c3314e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437515"
---
# <a name="tutorial-deploy-and-walk-through-the-smart-inventory-management-application-template"></a>Tutorial: Implementación y recorrido por la plantilla de aplicación de administración inteligente del inventario

Use la aplicación de *administración inteligente del inventario* de IoT Central y las instrucciones de este artículo para desarrollar una solución completa de administración inteligente del inventario.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-architecture.png" alt-text="Administración inteligente del inventario.":::

1. Conjunto de sensores de IoT que envían datos de telemetría a un dispositivo de puerta de enlace
2. Dispositivos de puerta de enlace que envían telemetría y conclusiones agregadas a IoT Central
3. Los datos se enrutan al servicio de Azure que se quiera para su manipulación
4. Se pueden usar servicios de Azure (como ASA o Azure Functions) para volver a formatear flujos de datos y enviarlos a las cuentas de almacenamiento que se desee 
5. Los datos procesados se almacenan en un almacenamiento de acceso frecuente para las acciones casi en tiempo real o en un almacenamiento en frío para las mejoras de información adicionales basadas en Machine Learning o en el análisis por lotes. 
6. Logic Apps puede usarse para potenciar varios flujos de trabajo empresariales en aplicaciones empresariales de usuario final.

### <a name="details"></a>Detalles

En la siguiente sección se describen las partes de la ingesta de datos de telemetría de la arquitectura conceptual a partir de las etiquetas de identificación por radiofrecuencia (RFID) y las etiquetas de Bluetooth de baja energía (BLE).

### <a name="rfid-tags"></a>Etiquetas RFID

Las etiquetas RFID transmiten datos sobre un elemento a través de ondas de radio. Normalmente, las etiquetas RFID no tienen batería, salvo que se especifique. Las etiquetas reciben energía de las ondas de radio generadas por el lector y transmiten una señal de vuelta hacia el lector RFID.

### <a name="ble-tags"></a>Etiquetas BLE

La baliza de energía emite paquetes de datos a intervalos regulares. Los lectores de BLE o los servicios instalados en los smartphones detectan los datos de la baliza y, a continuación, los transmiten a la nube.

### <a name="rfid--ble-readers"></a>Lectores de BLE y RFID

El lector de RFID convierte las ondas de radio en una forma de datos más utilizable. Posteriormente, la información que se recopila de las etiquetas se almacena en un servidor perimetral local o se envía a la nube mediante JSON-RPC 2.0 a través de MQTT.
Los lectores de BLE, también conocidos como puntos de acceso (AP), son similares al lector de RFID. Se usa para detectar señales Bluetooth cercanas y retransmitir su mensaje a una instancia de Azure IoT Edge local o en la nube mediante un JSON-RPC 2.0 a través de MQTT.
Muchos lectores son capaces de leer señales de RFID y de baliza, y de proporcionar una funcionalidad adicional del sensor relacionada con la temperatura, la humedad, el acelerómetro y el giroscopio.

### <a name="azure-iot-edge-gateway"></a>Puerta de enlace de Azure IoT Edge

El servidor de Azure IoT Edge proporciona un lugar para preprocesar los datos localmente antes de enviarlos a la nube. También podemos implementar cargas de trabajo en la nube de inteligencia artificial, Azure y servicios de terceros o de lógica de negocios mediante contenedores estándar.

### <a name="device-management-with-iot-central"></a>Administración de dispositivos con IoT Central 

Azure IoT Central es una plataforma de desarrollo de soluciones que simplifica la conectividad, configuración y administración de dispositivos IoT. La plataforma reduce significativamente la carga y los costos de la administración, las operaciones y los desarrollos relacionados con los dispositivos IoT. Los clientes y partners pueden crear soluciones empresariales integrales para lograr un bucle de comentarios digital en la administración del inventario.

### <a name="business-insights--actions-using-data-egress"></a>Información y acciones empresariales mediante la salida de datos 

La plataforma de IoT Central proporciona opciones de extensibilidad enriquecidas a través de la exportación continua de datos (CDE) y las API. La información empresarial basada en el procesamiento de datos de telemetría o la telemetría sin procesar se suele exportar a una aplicación de línea de negocio preferida. Para lograrlo se usa un webhook, bus de servicio, centro de eventos o almacenamiento de blobs para compilar, entrenar e implementar modelos de Machine Learning y mejorar aún más la información.

En este tutorial, aprenderá lo siguiente:

> [!div class="checklist"]

> * Creación de una aplicación de administración inteligente de inventario. 
> * Recorrido por la aplicación. 

## <a name="prerequisites"></a>Prerrequisitos

* No se necesitan requisitos previos específicos para implementar esta aplicación.
* Se recomienda tener una suscripción de Azure, aunque puede probar las opciones sin ella.

## <a name="create-smart-inventory-management-application"></a>Creación de una aplicación de administración inteligente del inventario

Cree la aplicación mediante los pasos siguientes:

1. Navegue al sitio de [Compilación Azure IoT Central](https://aka.ms/iotcentral). Después, inicie sesión con una cuenta Microsoft personal, profesional o educativa. Seleccione **Crear** en la barra de navegación izquierda y, a continuación, seleccione la pestaña **Venta minorista**: :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc-retail-home-page.png" alt-text="Captura de pantalla que muestra cómo crear una aplicación a partir de la plantilla de aplicación de administración inteligente del inventario":::

1. Seleccione **Crear aplicación** en **Administración inteligente del inventario**.

Para obtener más información, vea [Creación de una aplicación de Azure IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Recorrido por la aplicación.

Las secciones siguientes le guiarán por las características clave de la aplicación:

### <a name="dashboard"></a>Panel 

Después de implementar correctamente la plantilla de la aplicación, el panel predeterminado es un portal centrado en la administración inteligente de inventarios. Northwind Traders es un proveedor ficticio de inventarios inteligentes que administra el almacenamiento con Bluetooth de baja energía (BLE) y la tienda de venta minorista con la identificación por radiofrecuencia (RFID). En este panel, se mostrarán dos puertas de enlace diferentes que proporcionan datos de telemetría sobre el inventario, junto con comandos, trabajos y acciones asociadas que puede realizar. Este panel está preconfigurado para mostrar la actividad crítica de las operaciones de administración inteligente del inventario.
El panel se divide lógicamente entre dos operaciones diferentes de administración de dispositivos de la puerta de enlace. 
   * El almacén se implementa con una puerta de enlace BLE fija y etiquetas BLE en los palés para realizar un seguimiento y localizar el inventario en instalaciones más grandes.
   * La tienda de venta minorista está implementada con una puerta de enlace RFID fija y etiquetas RFID en un nivel de elemento individual para realizar el seguimiento y localizar las existencias en un punto de venta.
  * Visualización de la [ubicación](../core/howto-use-location-data.md) de la puerta de enlace, el estado y los detalles relacionados 

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-dashboard-1.png" alt-text="Captura de pantalla que muestra la mitad superior del panel de administración inteligente del inventario.":::

    * Puede supervisar fácilmente el número total de puertas de enlace, así como las etiquetas activas y desconocidas.
    * Puede realizar operaciones de administración de dispositivos, como actualizar el firmware, deshabilitar el sensor, habilitar el sensor, actualizar el umbral del sensor, actualizar los intervalos de telemetría y actualizar los contratos del servicio de dispositivo.
    * Los dispositivos de puerta de enlace pueden realizar la administración de inventario a petición con un análisis completo o incremental.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-dashboard-2.png" alt-text="Captura de pantalla que muestra la mitad inferior del panel de administración inteligente del inventario.":::

### <a name="device-template"></a>Plantilla de dispositivo

Haga clic en la pestaña Plantillas de dispositivo y verá el modelo de funcionalidad de la puerta de enlace. Un modelo de funcionalidad está estructurado en torno a dos interfaces diferentes **Telemetría y propiedad de puerta de enlace** y **Comandos de puerta de enlace**.

**Telemetría y propiedades de puerta de enlace**: esta interfaz representa todos los datos de telemetría relacionados con los sensores, la ubicación y la información del dispositivo, así como las funcionalidades de las propiedades de dispositivo gemelo, tales como los umbrales de puerta de enlace y los intervalos de actualización.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-device-template-1.png" alt-text="Captura de pantalla que muestra la plantilla de dispositivo de puerta de enlace del inventario en la aplicación.":::

**Comandos de puerta de enlace**: esta interfaz organiza todas las funciones de comando de la puerta de enlace.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-device-template-2.png" alt-text="Captura de pantalla que muestra la interfaz de comandos de puerta de enlace en la plantilla de dispositivo de puerta de enlace del inventario.":::

### <a name="rules"></a>Reglas

Seleccione la pestaña Reglas para ver dos reglas diferentes que existen en esta plantilla de aplicación. Estas reglas se configuran para enviar notificaciones por correo electrónico a los operadores para realizar más investigaciones.

**Puerta de enlace sin conexión**: Esta regla se desencadenará si la puerta de enlace no se comunica con la nube durante un período prolongado. La puerta de enlace podría no responder porque está en modo de batería baja, ha perdido la conectividad o debido al estado del dispositivo.

**Etiquetas desconocidas**: Es fundamental realizar el seguimiento de todas las etiquetas RFID y BLE asociadas a un recurso. Si la puerta de enlace detecta demasiadas etiquetas desconocidas, es un claro indicador de que hay problemas de sincronización con las aplicaciones que generan etiquetas.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-rules.png" alt-text="Captura de pantalla que muestra la lista de reglas de la aplicación de administración inteligente del inventario.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine la plantilla de la aplicación. Para ello, vaya a **Administración** > **Configuración de la aplicación** y haga clic en **Eliminar**.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-cleanup.png" alt-text="Captura de pantalla en la que se muestra cómo eliminar la aplicación cuando haya terminado con ella.":::

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la administración inteligente del inventario:

> [!div class="nextstepaction"]
> [Concepto de administración inteligente del inventario](./architecture-smart-inventory-management.md)
