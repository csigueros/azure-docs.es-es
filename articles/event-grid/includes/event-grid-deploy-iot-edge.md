---
title: Archivo de inclusión
description: Archivo de inclusión
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 05d436ae7ae9e92d0bbdce4d540b2d7bda34c2dd
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112412729"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Implementación del módulo de Event Grid en IoT Edge

Hay varias maneras de implementar módulos en un dispositivo IoT Edge, y todas ellas funcionan con Azure Event Grid en IoT Edge. En este artículo se describen los pasos para implementar Event Grid en IoT Edge desde Azure Portal.

>[!NOTE]
> En este tutorial, implementará el módulo de Event Grid sin persistencia. Esto significa que los temas y las suscripciones que cree en este tutorial se eliminarán al volver a implementar el módulo. Para más información sobre cómo configurar la persistencia, consulte los artículos siguientes: [Persistencia del estado en Linux](../edge/persist-state-linux.md) o [Persistencia del estado en Windows](../edge/persist-state-windows.md). En el caso de las cargas de trabajo de producción, se recomienda instalar el módulo de Event Grid con persistencia.

>[!IMPORTANT]
> En este tutorial, se implementará con la autenticación de cliente desactivada y permitirá suscriptores HTTP. En el caso de las cargas de trabajo de producción, se recomienda habilitar solo las solicitudes y los suscriptores HTTPS que tengan habilitada la autenticación de cliente. Para más información sobre cómo configurar el módulo de Event Grid de forma segura, consulte [Seguridad y autenticación](../edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Selección del dispositivo IoT Edge

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com)
1. Vaya a su instancia de IoT Hub.
1. Seleccione **IoT Edge** en el menú de la sección **Administración automática de dispositivos**. 
1. Haga clic en el identificador del dispositivo de destino en la lista de dispositivos.
1. Seleccione **Set modules** (Establecer módulos). Mantenga la página abierta. Continuará con los pasos de la siguiente sección.

### <a name="configure-a-deployment-manifest"></a>Configuración de un manifiesto de implementación

Un manifiesto de implementación es un documento JSON que describe qué módulos se van a implementar, cómo fluyen los datos entre los módulos y las propiedades deseadas de los módulos gemelos. Azure Portal tiene un asistente que le guía en la creación de un manifiesto de implementación, en lugar de crear el documento JSON de forma manual.  Consta de tres pasos: **Adición de módulos**, **Especificación de rutas** y **Revisión de la implementación**.

### <a name="add-modules"></a>Adición de módulos

1. En la sección **Módulos de implementación**, seleccione **Agregar**.
1. En los tipos de módulos de la lista desplegable, seleccione **Módulo IoT Edge**
1. Proporcione el nombre, la imagen y las opciones de creación del contenedor:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Nombre**: eventgridmodule
   * **URI de imagen**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opciones de creación del contenedor**:

```json
    {
      "Env": [
        "inbound__clientAuth:clientCert__enabled=false",
        "outbound__webhook__httpsOnly=false"
      ],
      "HostConfig": {
        "PortBindings": {
          "4438/tcp": [
            {
              "HostPort": "4438"
            }
          ]
        }
      }
    }
```

 1. Haga clic en **Guardar**
 1. Seleccione **Siguiente** para ir a la sección de rutas

    > [!NOTE]
    > Si va a usar una máquina virtual de Azure como dispositivo perimetral, agregue una regla de puerto de entrada para permitir tráfico entrante en el puerto 4438. Para instrucciones sobre cómo agregar la regla, consulte [Apertura de puertos en una máquina virtual](../../virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Configuración de rutas

 Mantenga las rutas predeterminadas y seleccione **Siguiente** para pasar a la sección de revisión.

### <a name="review-deployment"></a>Revisión de la implementación

1. La sección de revisión le muestra el manifiesto de implementación en formato JSON que se ha creado en función de las selecciones de las dos secciones anteriores. Confirme que ve los dos módulos **$edgeAgent** y **$edgeHub** en la lista. Estos dos módulos constituyen el entorno de ejecución de IoT Edge y son valores predeterminados necesarios en todas las implementaciones.
1. Revise la información de implementación y seleccione **Enviar**.

### <a name="verify-your-deployment"></a>Comprobación de la implementación

1. Después de enviar la implementación, regresará a la página de IoT Edge del centro de IoT.
1. Seleccione el **dispositivo IoT Edge** elegido como destino con la implementación para abrir sus detalles.
1. En los detalles del dispositivo, compruebe que el módulo de Event Grid aparece como **Especificado en la implementación** y **Notificado por el dispositivo**.

Puede tardar unos minutos para que el módulo se inicie en el dispositivo y, a continuación, notifique a IoT Hub. Actualice la página para ver el estado actualizado.