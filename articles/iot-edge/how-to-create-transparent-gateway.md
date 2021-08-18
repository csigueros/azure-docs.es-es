---
title: Creación de un dispositivo de puerta de enlace transparente con Azure IoT Edge | Microsoft Docs
description: Uso de un dispositivo Azure IoT Edge como una puerta de enlace transparente que puede procesar información para dispositivos de bajada
author: kgremban
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 76d04b8d55b25c3fd5c96f34ad38088472c504b5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740655"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configuración de un dispositivo IoT Edge para que actúe como puerta de enlace transparente

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Este artículo proporciona instrucciones detalladas para configurar un dispositivo IoT Edge de modo que funcione como una puerta de enlace transparente para que otros dispositivos se comuniquen con IoT Hub. En este artículo, el término *puerta de enlace IoT Edge* hace referencia a un dispositivo IoT Edge configurado como una puerta de enlace transparente. Para más información, consulte [Uso de un dispositivo IoT Edge como puerta de enlace](./iot-edge-as-gateway.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

>[!NOTE]
>En IoT Edge versiones 1.1 y anteriores, un dispositivo IoT Edge no puede ser de bajada de una puerta de enlace de IoT Edge.
>
>Los dispositivos de bajada no pueden usar la carga de archivos.

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Los dispositivos de bajada no pueden usar la carga de archivos.

::: moniker-end

Hay tres pasos generales para configurar una conexión de puerta de enlace transparente correcta. En este artículo se describe el primer paso:

1. **Configure el dispositivo de puerta de enlace como servidor para que los dispositivos de bajada puedan conectarse a él de forma segura. Configure la puerta de enlace para recibir mensajes de los dispositivos de bajada y enrutarlos al destino adecuado.**
2. Cree una identidad de dispositivo para el dispositivo de bajada para que pueda autenticarse en IoT Hub. Configure el dispositivo de bajada para enviar mensajes a través del dispositivo de puerta de enlace. Para conocer estos pasos, consulte [Autenticación de un dispositivo de bajada en Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Conecte el dispositivo de bajada al dispositivo de puerta de enlace y empiece a enviar mensajes. Para conocer estos pasos, consulte [Conexión de un dispositivo de bajada a una puerta de enlace Azure IoT Edge](how-to-connect-downstream-device.md).

Para que un dispositivo funcione como puerta de enlace, tiene que conectarse de forma segura a sus dispositivos de bajada. Azure IoT Edge le permite usar una infraestructura de clave pública (PKI) para configurar conexiones seguras entre los dispositivos. En este caso, vamos a permitir que un dispositivo de bajada se conecte a un dispositivo IoT Edge que actúa como puerta de enlace transparente. Para mantener una seguridad razonable, el dispositivo de bajada debe confirmar la identidad del dispositivo de puerta de enlace. Esta comprobación de identidad evita que los dispositivos se conecten a puertas de enlace que pueden ser malintencionadas.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Un dispositivo de bajada puede ser cualquier aplicación o plataforma que tenga una identidad creada con el servicio en la nube [Azure IoT Hub](../iot-hub/index.yml). Estas aplicaciones suelen usar el [SDK de dispositivo IoT de Azure](../iot-hub/iot-hub-devguide-sdks.md). Un dispositivo de bajada podría ser incluso una aplicación que se ejecuta en el propio dispositivo de puerta de enlace IoT Edge. Sin embargo, un dispositivo IoT Edge no puede ser inferior a una puerta de enlace de IoT Edge.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Un dispositivo de bajada puede ser cualquier aplicación o plataforma que tenga una identidad creada con el servicio en la nube [Azure IoT Hub](../iot-hub/index.yml). Estas aplicaciones suelen usar el [SDK de dispositivo IoT de Azure](../iot-hub/iot-hub-devguide-sdks.md). Un dispositivo de bajada podría ser incluso una aplicación que se ejecuta en el propio dispositivo de puerta de enlace IoT Edge.
:::moniker-end
<!-- end 1.2 -->

Puede crear cualquier infraestructura de certificados que permita la confianza necesaria para la topología de la puerta de enlace de dispositivo. En este artículo se da por hecho que usa la misma configuración de certificado que usaría para habilitar la [seguridad de entidad de certificación X.509](../iot-hub/iot-hub-x509ca-overview.md) en IoT Hub, lo que implica un certificado de entidad de certificación X.509 asociado a un centro de IoT específico (la entidad de certificación raíz del centro de IoT), una serie de certificados firmados con esta entidad de certificación y una entidad de certificación para el dispositivo IoT Edge.

>[!NOTE]
>El término *certificado de CA raíz* usado en estos artículos hace referencia al certificado público de entidad de nivel superior de la cadena de certificados de PKI y no necesariamente a la raíz del certificado de una entidad de certificación sindicada. En muchos casos, se trata realmente de un certificado público intermedio de la entidad de certificación.

Los pasos siguientes le guían por el proceso de crear los certificados e instalarlos en los lugares adecuados de la puerta de enlace. Puede usar cualquier máquina para generar los certificados y, a continuación, copiarlos en el dispositivo IoT Edge.

## <a name="prerequisites"></a>Requisitos previos

Un dispositivo Linux o Windows con IoT Edge instalado.

Si no tiene listo un dispositivo, puede crear uno en una máquina virtual de Azure. Siga los pasos descritos en [Implementación del primer módulo de IoT Edge en un dispositivo virtual Linux](quickstart-linux.md) para crear una instancia de IoT Hub, crear una máquina virtual y configurar el entorno de ejecución de Azure IoT Edge.

## <a name="set-up-the-device-ca-certificate"></a>Configuración del certificado de CA de dispositivo

Todas las puertas de enlace de IoT Edge necesitan un certificado de CA de dispositivo instalado en ellas. El demonio de seguridad de IoT Edge usa el certificado de entidad de certificación de dispositivo IoT Edge para firmar un certificado de entidad de certificación de carga de trabajo, que a su vez firma un certificado de servidor para el centro de IoT Edge. La puerta de enlace presenta su certificado de servidor al dispositivo de bajada durante el inicio de la conexión. El dispositivo de bajada realiza comprobaciones para asegurarse de que el certificado de servidor forma parte de una cadena de certificados que se acumula en el certificado de entidad de certificación raíz. Este proceso permite que el dispositivo de bajada confirme que la puerta de enlace procede de un origen de confianza. Para obtener más información, consulta [Información sobre los certificados de Azure IoT Edge](iot-edge-certs.md).

![Configuración del certificado de puerta de enlace](./media/how-to-create-transparent-gateway/gateway-setup.png)

El certificado de CA raíz y el certificado de CA de dispositivo (con su clave privada) deben estar presentes en el dispositivo de puerta de enlace de IoT Edge y configurarse en el archivo de configuración de IoT Edge. Recuerde que, en este caso, *certificado de CA raíz* significa la entidad de certificación de nivel superior para este escenario de IoT Edge. El certificado de CA de dispositivo de puerta de enlace y los certificados de dispositivo de bajada deben acumularse en el mismo certificado de CA raíz.

>[!TIP]
>El proceso de instalación del certificado de CA raíz y el certificado de CA de dispositivo en un dispositivo IoT Edge también se explica más detalladamente en [Administración de certificados en un dispositivo IoT Edge](how-to-manage-device-certificates.md).

Tenga listos los archivos siguientes:

* Certificado de entidad de certificación raíz
* Certificado de entidad de certificación de dispositivo
* Clave privada de entidad de certificación del dispositivo

En escenarios de producción, debe generar estos archivos con su propia entidad de certificación. En escenarios de desarrollo y pruebas, puede usar certificados de demostración.

Si no tiene su propia entidad de certificación y quiere usar certificados de demostración, siga las instrucciones de [Creación de certificados de demostración para probar las características del dispositivo IOT Edge](how-to-create-test-certificates.md) para crear sus archivos. En esa página, debe seguir los pasos a continuación:

   1. Para empezar, configure los scripts para generar certificados en el dispositivo.
   2. Cree un certificado de CA raíz. Al final de esas instrucciones, tendrá un archivo de certificado de CA raíz:
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.
   3. Cree certificados de CA de dispositivo IoT Edge. Al final de esas instrucciones, tendrá un certificado de CA de dispositivo y su clave privada:
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` y
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

Si creó los certificados en otra máquina, cópielos en el dispositivo IoT Edge y, a continuación continúe con los pasos siguientes.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Abra el archivo de configuración del demonio de seguridad en el dispositivo IoT Edge.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. Busque la sección **Configuración de certificado** del archivo. Quite la marca de comentario de las cuatro líneas que empiezan por **certificates:** y proporcione los URI de archivo a los tres archivos como valores de las siguientes propiedades:
   * **device_ca_cert**: certificado de CA de dispositivo
   * **device_ca_pk**: clave privada de CA de dispositivo
   * **trusted_ca_certs**: certificado de CA raíz

   Asegúrese de que la línea **certificates:** no tenga ningún espacio en blanco delante y de que las otras líneas tengan una sangría de dos espacios.

1. Guarde y cierre el archivo.

1. Reinicie IoT Edge.
   * Windows: `Restart-Service iotedge`
   * Linux: `sudo systemctl restart iotedge`
:::moniker-end
<!-- end 1.1 -->

<!--1.2 -->
:::moniker range=">=iotedge-2020-11"

1. En el dispositivo IoT Edge, abra el archivo de configuración: `/etc/aziot/config.toml`

   >[!TIP]
   >Si el archivo de configuración todavía no existe en el dispositivo, use `/etc/aziot/config.toml.edge.template` como plantilla para crear uno.

1. Busque el parámetro `trust_bundle_cert`. Quite la marca de comentario de esta línea y proporcione el URI de archivo al certificado de CA raíz en el dispositivo.

1. Busque la sección `[edge_ca]` (Aprovisionamiento) del archivo. Quite la marca de comentario de las tres líneas de esta sección y proporcione los URI de los archivos de certificado y clave como valores para las siguientes propiedades:
   * **cert**: certificado de CA del dispositivo
   * **pk**: clave privada de CA del dispositivo

1. Guarde y cierre el archivo.

1. Aplique los cambios.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="deploy-edgehub-and-route-messages"></a>Implementación de edgeHub y enrutado de mensajes

Los dispositivos de nivel inferior envían datos de telemetría y mensajes al dispositivo de puerta de enlace, donde el módulo del centro de IoT Edge es responsable de enrutar la información a otros módulos o a IoT Hub. Para preparar el dispositivo de puerta de enlace para esta función, asegúrese de que:

* El módulo del centro de IoT Edge se implementa en el dispositivo.

  Cuando se instala IoT Edge por primera vez en un dispositivo, se inicia automáticamente un único módulo del sistema: el agente de IoT Edge. Una vez que se crea la primera implementación para un dispositivo, también se inicia el segundo módulo del sistema, el centro de IoT Edge. Si el módulo **edgeHub** no está en ejecución en el dispositivo, cree una implementación para el dispositivo.

* El módulo del centro de IoT Edge tiene rutas configuradas para administrar los mensajes entrantes de los dispositivos de nivel inferior.

  El dispositivo de puerta de enlace tiene que tener una ruta para administrar los mensajes de los dispositivos de nivel inferior o, de lo contrario, los mensajes no se procesarán. Puede enviar los mensajes a los módulos del dispositivo de puerta de enlace o directamente a IoT Hub.

Para implementar el módulo del centro de IoT Edge y configurarlo con rutas para administrar los mensajes entrantes de los dispositivos de nivel inferior, siga estos pasos:

1. En Azure Portal, vaya hasta el centro de IoT.

2. Vaya a **IoT Edge** y seleccione el dispositivo IoT Edge que quiere usar como puerta de enlace.

3. Seleccione **Set modules** (Establecer módulos).

4. En la página **Módulos**, puede agregar cualquier módulo que desee implementar en el dispositivo de puerta de enlace. Para los fines de este artículo, nos centramos en la configuración e implementación del módulo edgeHub, que no es necesario establecer explícitamente en esta página.

5. Seleccione **Siguiente: Rutas**.

6. En la página **Rutas**, asegúrese de que hay una ruta para controlar los mensajes procedentes de los dispositivos de nivel inferior. Por ejemplo:

   * Ruta que envía todos los mensajes, ya sea desde un módulo o desde un dispositivo de nivel inferior, a IoT Hub:
       * **Nombre**: `allMessagesToHub`
       * **Valor**: `FROM /messages/* INTO $upstream`

   * Una ruta que envía todos los mensajes de todos los dispositivos de nivel inferior a IoT Hub:
      * **Nombre**: `allDownstreamToHub`
      * **Valor**: `FROM /messages/* WHERE NOT IS_DEFINED ($connectionModuleId) INTO $upstream`

      Esta ruta funciona porque, a diferencia de los mensajes de Ios módulos de IoT Edge, los mensajes de los dispositivos de nivel inferior no tienen un identificador de módulo asociado. El uso de la cláusula **WHERE** de la ruta nos permite filtrar los mensajes que contengan esa propiedad del sistema.

      Para obtener más información sobre el enrutamiento de mensajes, vea [Implementar módulos y establecer rutas](./module-composition.md#declare-routes).

7. Una vez creadas las rutas, seleccione **Revisar y crear**.

8. En la página **Revisar y crear**, seleccione **Crear**.

## <a name="open-ports-on-gateway-device"></a>Apertura de puertos en el dispositivo de puerta de enlace

Los dispositivos IoT Edge estándar no necesitan conectividad entrante a para funcionar, porque toda la comunicación con IoT Hub se realiza a través de conexiones salientes. Los dispositivos de puerta de enlace son diferentes porque deben recibir mensajes de sus dispositivos de bajada. Si hay un firewall entre los dispositivos de bajada y el dispositivo de puerta de enlace, la comunicación también debe ser posible a través de él.

Para que un escenario de puerta de enlace funcione, al menos uno de los protocolos admitidos en el centro de IoT Edge debe estar abierto para el tráfico entrante procedente de los dispositivos de bajada. Los protocolos admitidos son MQTT, AMQP, HTTPS, MQTT sobre WebSockets y AMQP sobre WebSockets.

| Port | Protocolo |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene un dispositivo IoT Edge configurado como puerta de enlace transparente, debe configurar los dispositivos de bajada para que confíen en la puerta de enlace y le envíen mensajes. Continúe con [Autenticación de un dispositivo de bajada en Azure IoT Hub](how-to-authenticate-downstream-device.md) para ver los pasos siguientes en la configuración del escenario de puerta de enlace transparente.