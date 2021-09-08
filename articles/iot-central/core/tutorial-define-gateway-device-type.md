---
title: 'Tutorial: Definición de un nuevo tipo de dispositivo de puerta de enlace en Azure IoT Central | Microsoft Docs'
description: Este tutorial le muestra, como desarrollador, cómo definir un nuevo tipo de dispositivo de puerta de enlace de IoT en la aplicación de Azure IoT Central.
author: rangv
ms.author: rangv
ms.date: 08/18/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 44a659b5b10e1f97293e2cd2636d5774baaebd40
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426215"
---
# <a name="tutorial---define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Tutorial: Definición de un nuevo tipo de dispositivo de puerta de enlace de IoT en la aplicación de Azure IoT Central

Este tutorial le muestra cómo usar una plantilla de dispositivo de puerta de enlace para definir un dispositivo de puerta de enlace en la aplicación de IoT Central. A continuación, puede configurar varios dispositivos de nivel inferior que se conecten a la aplicación de IoT Central mediante el dispositivo de puerta de enlace. 

En este tutorial va a crear una plantilla de dispositivo de puerta de enlace **Smart Building**. Un dispositivo de puerta de enlace **Smart Building** tiene relaciones con otros dispositivos de nivel inferior.

![Diagrama de la relación entre el dispositivo de puerta de enlace y los dispositivos de nivel inferior](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Además de permitir que los dispositivos de nivel inferior se comuniquen con la aplicación de IoT Central, un dispositivo de puerta de enlace también puede:

* Enviar sus propios datos de telemetría como, por ejemplo, la temperatura.
* Responder a las actualizaciones de propiedades de escritura que realiza un operador. Por ejemplo, un operador podría cambiar el intervalo de envío de datos de telemetría.
* Responder a comandos como, por ejemplo, el reinicio del dispositivo.

En este tutorial aprenderá a:

> [!div class="checklist"]
>
> * Creación de plantillas de dispositivo de nivel inferior
> * Creación de una plantilla de dispositivo de puerta de enlace
> * Publicación de la plantilla de dispositivo
> * Creación de los dispositivos simulados

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este tutorial, necesitará lo siguiente:

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="create-downstream-device-templates"></a>Creación de plantillas de dispositivo de nivel inferior

En este tutorial se usan plantillas de dispositivo para un dispositivo con **sensor S1** y con **sensor de ocupación RS40** para generar dispositivos de nivel inferior simulados.

Para crear una plantilla para un dispositivo con **sensor S1**:

1. Seleccione **Device Templates** (Plantillas de dispositivo) en el panel izquierdo. A continuación, seleccione **+Nuevo** para empezar a agregar la plantilla.

1. Desplácese hacia abajo hasta que pueda ver el icono del dispositivo **Minew S1**. Seleccione el icono y, a continuación, seleccione **Siguiente: Customize** (Personalizar)

1. En la página **Revisar**, seleccione **Crear** para agregar la plantilla de dispositivo a la aplicación. 

Para crear una plantilla de dispositivo para un dispositivo **Sensor de ocupación RS40**:

1. Seleccione **Device Templates** (Plantillas de dispositivo) en el panel izquierdo. A continuación, seleccione **+Nuevo** para empezar a agregar la plantilla.

1. Desplácese hacia abajo hasta que vea el icono del dispositivo con el ***sensor de ocupación RS40**. Seleccione el icono y, a continuación, seleccione **Siguiente: Customize** (Personalizar)

1. En la página **Revisar**, seleccione **Crear** para agregar la plantilla de dispositivo a la aplicación. 

Ahora tiene plantillas de dispositivo para los dos tipos de dispositivos de nivel inferior:

![Plantillas de los dispositivos de nivel inferior](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Creación de una plantilla de dispositivo de puerta de enlace

En este tutorial se va a crear una plantilla para un dispositivo de puerta de enlace desde el principio. Puede usar esta plantilla más adelante para crear un dispositivo simulado de puerta de enlace en la aplicación.

Para agregar una nueva plantilla de dispositivo de puerta de enlace a la aplicación:

1. Seleccione **Device Templates** (Plantillas de dispositivo) en el panel izquierdo. A continuación, seleccione **+Nuevo** para empezar a agregar la plantilla.

1. En la página **Seleccionar tipo de plantilla**, seleccione el icono **Dispositivo IoT** y, después, seleccione **Siguiente: Customize** (Personalizar)

1. En la página **Customize device** (Personalizar dispositivo), active la casilla **This is a gateway device** (Este es un dispositivo de puerta de enlace).

1. Escriba **Smart Building gateway device** (Dispositivo de puerta de enlace de edificio inteligente) como nombre de la plantilla y, luego, seleccione **Siguiente: revisión**.

1. En la página **Revisar**, seleccione **Crear**. 



1. En la página **Create a model** (Crear un modelo), seleccione el icono **Custom model** (Personalizar modelo).

1. Seleccione **+Agregar funcionalidad** para agregar una funcionalidad.

1. Escriba **Enviar datos** como nombre para mostrar y, luego, seleccione **Propiedad** como tipo de funcionalidad.

1. Seleccione **+Agregar funcionalidad** para agregar otra funcionalidad. Escriba **Telemetría booleana** como nombre para mostrar, seleccione **Telemetría** como tipo de funcionalidad y, luego, elija **Booleano** como esquema.

1. Seleccione **Guardar**.

### <a name="add-relationships"></a>Adición de relaciones

A continuación, agregue relaciones a las plantillas para las plantillas de dispositivo de nivel inferior:

1. En la plantilla **Dispositivo de puerta de enlace de edificio inteligente** seleccione **Relaciones**.

1. Seleccione **+ Agregar relación**. Escriba **Sensor ambiental** como nombre para mostrar y seleccione **Sensor S1** como destino.

1. Seleccione **+ Agregar relación** de nuevo. Escriba **Sensor de ocupación** como nombre para mostrar y seleccione **Sensor de ocupación RS40** como destino.

1. Seleccione **Guardar**.

![Plantilla de dispositivo de puerta de enlace de edificio inteligente que muestra relaciones](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Adición de propiedades de nube

Una plantilla de dispositivo de puerta de enlace puede incluir propiedades de la nube. Las propiedades de la nube solo existen en la aplicación IoT Central y nunca se envían a un dispositivo ni se reciben de él.

Para agregar propiedades de la nube a la plantilla **Dispositivo de puerta de enlace de edificio inteligente**:

1. En la plantilla **Dispositivo de puerta de enlace de edificio inteligente** seleccione **Propiedades de la nube**.

1. Use la información de la tabla siguiente para agregar dos propiedades de la nube a la plantilla de dispositivo de puerta de enlace.

    | Nombre para mostrar      | Tipo semántico | Schema |
    | ----------------- | ------------- | ------ |
    | Fecha de la última revisión | None          | Date   |
    | Nombre del cliente     | None          | String |

1. Seleccione **Guardar**.

### <a name="create-views"></a>Creación de vistas

Como desarrollador, puede personalizar la aplicación para mostrar información pertinente sobre el dispositivo sensor ambiental a un operador. Las personalizaciones permiten al operador administrar estos dispositivo conectados a la aplicación. Puede crear dos tipos de vistas para que un operador interactúe con los dispositivos:

* Formularios para ver y editar las propiedades del dispositivo y la nube
* Vistas para visualizar dispositivos.

Para generar las vistas predeterminadas para la plantilla **Dispositivo de puerta de enlace de edificio inteligente**:

1. En la plantilla **Dispositivo de puerta de enlace de edificio inteligente** seleccione **Vistas**.

1. Seleccione el icono **Generar vistas predeterminadas** y asegúrese de que todas las opciones estén seleccionadas.

1. Seleccione **Generar vistas de panel predeterminadas**.

## <a name="publish-the-device-template"></a>Publicación de la plantilla de dispositivo

Para poder crear un dispositivo simulado de puerta de enlace, o conectar un dispositivo de puerta de enlace real, es preciso publicar la plantilla de dispositivo.

Para publicar la plantilla de dispositivo de puerta de enlace:

1. Seleccione la plantilla **Dispositivo de puerta de enlace de edificio inteligente** en la página **Plantillas de dispositivo**.

2. Seleccione **Publicar**.

3. En el cuadro de diálogo **Publish a Device Template** (Publicar una plantilla de dispositivo), elija **Publicar**.

Una vez publicada una plantilla de dispositivo, es visible en la página **Devices** (Dispositivos) y para el operador. El operador puede usar la plantilla para crear instancias de dispositivo o establecer las reglas y la supervisión. La edición de una plantilla publicada podría afectar al comportamiento en toda la aplicación.

Para más información sobre cómo modificar una plantilla de dispositivo después de publicarla, consulte [Edición de una plantilla de dispositivo existente](howto-edit-device-template.md).

## <a name="create-the-simulated-devices"></a>Creación de los dispositivos simulados

En este tutorial se usan dispositivos simulados de nivel inferior y un dispositivo simulado de puerta de enlace.

Para crear un dispositivo simulado de puerta de enlace:

1. En la página **Dispositivos**, seleccione **Dispositivo de puerta de enlace de edificio inteligente** en la lista de plantillas de dispositivo.

1. Seleccione **+Nuevo** para empezar a agregar un nuevo dispositivo.

1. Conserve el **Id. de dispositivo** y el **Nombre de dispositivo** que se han generado. Asegúrese de que el conmutador **Simulado** esté **Activado**. Seleccione **Crear**.

Para crear dispositivos simulados de nivel inferior:

1. En la página **Dispositivos**, seleccione **Sensor de ocupación RS40** en la lista de plantillas de dispositivo.

1. Seleccione **+Nuevo** para empezar a agregar un nuevo dispositivo.

1. Conserve el **Id. de dispositivo** y el **Nombre de dispositivo** que se han generado. Asegúrese de que el conmutador **Simulado** esté **Activado**. Seleccione **Crear**.

1. En la página **Dispositivos**, seleccione **Sensor S1** en la lista de plantillas de dispositivo.

1. Seleccione **+Nuevo** para empezar a agregar un nuevo dispositivo.

1. Conserve el **Id. de dispositivo** y el **Nombre de dispositivo** que se han generado. Asegúrese de que el conmutador **Simulado** esté **Activado**. Seleccione **Crear**.

![Dispositivos simulados en la aplicación](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Adición de relaciones de dispositivos de nivel inferior a un dispositivo de puerta de enlace

Ahora que tiene los dispositivos simulados en la aplicación, puede crear las relaciones entre los dispositivos de nivel inferior y el dispositivo de puerta de enlace:

1. En la página **Dispositivos**, seleccione **Sensor S1** en la lista de plantillas de dispositivo y, a continuación, seleccione el dispositivo simulado **Sensor S1**.

1. Seleccione **Attach to gateway** (Conectar a la puerta de enlace).

1. En el cuadro de diálogo **Attach to a gateway** (Conectar a la puerta de enlace), seleccione la plantilla **Smart Building gateway device** (Dispositivo de puerta de enlace de edificio inteligente) y, luego, elija la instancia simulada que creó anteriormente.

1. Seleccione **Adjuntar**.

1. En la página **Dispositivos**, seleccione **Sensor de ocupación RS40** en la lista de plantillas de dispositivo y, a continuación, seleccione el dispositivo simulado **Sensor de ocupación RS40**.

1. Seleccione **Conectar con la puerta de enlace**.

1. En el cuadro de diálogo **Conectar con la puerta de enlace**, seleccione la plantilla **Dispositivo de puerta de enlace de edificio inteligente** y, a continuación, seleccione la instancia simulada que creó anteriormente.

1. Seleccione **Adjuntar**.

Los dispositivos simulados de nivel inferior ahora están ya conectados al dispositivo simulado de puerta de enlace. Si va a la vista **Dispositivos de bajada** del dispositivo de puerta de enlace, podrá ver los dispositivos de nivel inferior relacionados:

![Vista Dispositivos de bajada](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

## <a name="connect-real-downstream-devices"></a>Conexión de dispositivos de bajada reales

En el tutorial [Creación y conexión de una aplicación cliente a la aplicación de Azure IoT Central](tutorial-connect-device.md), el código de ejemplo muestra cómo incluir el identificador de modelo de la plantilla de dispositivo en la carga de aprovisionamiento que envía el dispositivo. El identificador del modelo de IoT Central asocia el dispositivo con la plantilla de dispositivo correcta. Por ejemplo:

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
  provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
    provisioning_host=provisioning_host,
    registration_id=registration_id,
    id_scope=id_scope,
    symmetric_key=symmetric_key,
  )

  provisioning_device_client.provisioning_payload = {"modelId": model_id}
  return await provisioning_device_client.register()
```

Al conectar un dispositivo de bajada, puede modificar la carga de aprovisionamiento para incluir el identificador del dispositivo de puerta de enlace. El identificador del modelo de IoT Central asocia el dispositivo con la plantilla de dispositivo de bajada correcta. El identificador de puerta de enlace permite que IoT Central establezca la relación entre el dispositivo de bajada y su puerta de enlace. En este caso, la carga de aprovisionamiento que envía el dispositivo es similar al siguiente JSON:

```json
{
  "modelId": "dtmi:rigado:S1Sensor;2",
  "iotcGateway":{
    "iotcGatewayId": "gateway-device-001"
  }
}
```

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

* Crear una puerta de enlace de IoT como plantilla de dispositivo.
* Crear propiedades de la nube
* Crear personalizaciones
* Definir una visualización para la telemetría del dispositivo
* Agregar relaciones.
* Publicar la plantilla de dispositivo

A continuación, puede aprender a:

> [!div class="nextstepaction"]
> [Incorporación de un dispositivo Azure IoT Edge a la aplicación Azure IoT Central](tutorial-add-edge-as-leaf-device.md)
