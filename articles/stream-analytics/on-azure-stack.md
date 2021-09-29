---
title: Ejecución de Azure Stream Analytics en Azure Stack
description: Cree un trabajo perimetral de Azure Stream Analytics e impleméntelo en Azure Stack Hub mediante el runtime de Azure IoT Edge.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 544abb1e39710330d8d2529a83349c8669deae40
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638261"
---
# <a name="run-azure-stream-analytics-on-azure-stack"></a>Ejecución de Azure Stream Analytics en Azure Stack

Puede ejecutar Azure Stream Analytics en Azure Stack Hub como un módulo de Azure IoT Edge. Las configuraciones agregadas al módulo de IoT Edge le permiten interactuar con Azure Blob Storage, Azure Event Hubs y Azure IoT Hub que se ejecutan en una suscripción de Azure Stack Hub, ya que permiten las direcciones URL personalizadas que se encuentran en cada implementación de Azure Stack Hub.

Con Stream Analytics en Azure Stack, puede crear arquitecturas realmente híbridas para el procesamiento de flujos de datos en su propia nube privada autónoma. La nube se puede conectar o desconectar con aplicaciones nativas de nube mediante el uso de servicios de Azure coherentes en el entorno local. 

En este artículo, se muestra cómo transmitir datos desde IoT Hub o Event Hubs a otro centro de eventos o Blob Storage en una suscripción de Azure Stack Hub y procesarlos con Stream Analytics.

## <a name="set-up-environments"></a>Configurar entornos

Stream Analytics es un servicio híbrido en Azure Stack Hub. Se trata de un módulo de IoT Edge que se configura en Azure, pero que se puede ejecutar en Azure Stack Hub.

Si no está familiarizado con Azure Stack Hub o IoT Edge, siga las instrucciones que se indican a continuación para configurar los entornos.

### <a name="prepare-the-azure-stack-hub-environment"></a>Preparación del entorno de Azure Stack Hub

Cree una suscripción a Azure Stack Hub. Para más información, consulte [Tutorial: Creación y prueba de una suscripción en Azure Stack Hub](/azure-stack/user/azure-stack-subscribe-services/).

Si quiere evaluar Azure Stack Hub en su propio servidor, puede usar el Kit de desarrollo de Azure Stack (ASDK). Para más información sobre ASDK, consulte la [información general sobre ASDK](/azure-stack/asdk/).

### <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

Para ejecutar Stream Analytics en Azure Stack Hub, el dispositivo debe tener el entorno de ejecución de IoT Edge y conectividad de red con Azure Stack Hub o ser una máquina virtual que se ejecuta en Azure Stack Hub. El entorno de ejecución de IoT Edge permite que los trabajos perimetrales de Stream Analytics se integren con Azure Storage y Azure Event Hubs que se ejecutan en Azure Stack Hub. Para más información, consulte [Azure Stream Analytics en IoT Edge](stream-analytics-edge.md). 

Además de tener acceso de red a los recursos de Azure Stack Hub, el dispositivo IoT Edge o la máquina virtual necesitan acceso a IoT Hub en la nube pública de Azure para configurar el módulo de Stream Analytics. 

En los artículos siguientes se muestra cómo configurar el entorno de ejecución de IoT Edge en el dispositivo o la máquina virtual:

* [Instalación del entorno de ejecución de Azure IoT Edge en Windows](../iot-edge/how-to-install-iot-edge.md)
* [Instalación del entorno de ejecución de Azure IoT Edge en sistemas Linux basados en Debian](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Creación de un trabajo perimetral de Azure Stream Analytics

Los trabajos perimetrales de Stream Analytics se ejecutan en contenedores implementados en dispositivos IoT Edge. Constan de dos partes:
* Una parte en la nube que es responsable de la definición del trabajo: los usuarios definen las entradas, la salida, las consultas y otros valores, como los eventos que no funcionan, en la nube.
* Un módulo que se ejecuta en dispositivos IoT. Contiene el motor de Stream Analytics y recibe la definición del trabajo de la nube.

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Cuando se crea un trabajo de Stream Analytics para su ejecución en un dispositivo IoT Edge, se debe almacenar de forma que se pueda llamar desde el dispositivo. Puede usar una cuenta de Azure Storage o crear una nueva ahora.
1. En Azure Portal, vaya a **Crear un recurso** > **Storage** > **Cuenta de almacenamiento: blob, archivo, tabla, cola**.
1. Especifique los siguientes valores para crear la cuenta de almacenamiento.

   | Campo | Value |
   | --- | --- |
   | Nombre | Especifique un nombre único para la cuenta de almacenamiento. |
   | Location | Elija una ubicación cercana a usted.|
   | Suscripción | Elija la misma suscripción que IoT Hub.|
   | Grupo de recursos | Utilice el mismo grupo de recursos que para todos los recursos de prueba que se crean en las [guías de inicio rápido de IoT Edge](../iot-edge/quickstart.md) y los tutoriales. Por ejemplo, utilice **IoTEdgeResources**. |

1. Mantenga los valores predeterminados en los restantes campos y seleccione **Crear**.


### <a name="create-a-new-job"></a>Crear un trabajo

1. En Azure Portal, vaya a **Crear un recurso** > **Internet de las cosas** > **Stream Analytics Job**.
1. Especifique los siguientes valores para crear la cuenta de almacenamiento.

   | Campo | Value |
   | --- | --- |
   | Nombre del trabajo | Especifique el nombre del trabajo. Por ejemplo, **IoTEdgeJob**. |
   | Suscripción | Elija la misma suscripción que IoT Hub.|
   | Grupo de recursos | Utilice el mismo grupo de recursos que para todos los recursos de prueba que se crean en las [guías de inicio rápido de IoT Edge](../iot-edge/quickstart.md) y los tutoriales. Por ejemplo, utilice **IoTEdgeResources**. |
   | Location | Elija una ubicación cercana a usted. |
   | Entorno de hospedaje | Seleccionar **Edge**. |

1. Seleccione **Crear**.

### <a name="configure-your-job"></a>Configuración del trabajo

Después de crear el trabajo de Stream Analytics en Azure Portal, configúrelo con una entrada, una salida y una consulta para su ejecución en los datos que pasan por él. Puede especificar manualmente las entradas desde un centro de IoT o un centro de eventos de una suscripción de Azure Stack Hub.

1. Vaya al trabajo de Stream Analytics en Azure Portal.
1. En **Configurar**, seleccione **Configuración de cuenta de almacenamiento** y elija la cuenta de almacenamiento que creó en el paso anterior.

   :::image type="content" source="media/on-azure-stack/storage-account-settings.png" alt-text="Captura de pantalla que muestra la configuración de cuenta de almacenamiento del trabajo." lightbox="media/on-azure-stack/storage-account-settings.png":::

1. En **Topología de trabajo**, seleccione **Entradas** > **Agregar entrada de flujo**.
1. Seleccione **Centro de IoT**, **Centro de eventos** o **Centro de Microsoft Edge** en la lista desplegable. 
1. Si la entrada es un centro de eventos o un centro de IoT de una suscripción de Azure Stack Hub, proporcione la información manualmente, como se muestra a continuación.

   #### <a name="event-hub"></a>Centro de eventos

   | Campo | Value |
   | --- | --- |
   | Alias de entrada | Nombre descriptivo que se usará en la consulta del trabajo para hacer referencia a esta entrada. |
   | Espacio de nombres de Service Bus | El espacio de nombres es un contenedor para un conjunto de entidades de mensajería. Al crear un centro de eventos, también se crea el espacio de nombres. Un ejemplo es `sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com`. |
   | Nombre del centro de eventos | Nombre del centro de eventos que se usa como entrada. |
   | Nombre de la directiva del centro de eventos | Directiva de acceso compartido que proporciona acceso al centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. Esta opción se rellena automáticamente, a menos que seleccione la opción de proporcionar la configuración del centro de eventos manualmente. |
   | Clave de la directiva del Centro de eventos | Clave de acceso compartido que se usa para autorizar el acceso al centro de eventos. Esta opción se rellena automáticamente, a menos que seleccione la opción de proporcionar la configuración del centro de eventos manualmente. Puede encontrarla en la configuración del centro de eventos. |
   | Grupo de consumidores del centro de eventos (opcional) | Utilice un grupo de consumidores distinto para cada trabajo de Stream Analytics. Esta cadena identifica el grupo de consumidores que se usa para la ingesta de datos desde el centro de eventos. Si no se especifica ningún grupo de consumidores, el trabajo de Stream Analytics usa el grupo de consumidores $Default. |
   | Recuento de particiones | El recuento de particiones es el número de particiones de un centro de eventos. |

   :::image type="content" source="media/on-azure-stack/event-hub-input.png" alt-text="Captura de pantalla que muestra las entradas del centro de eventos." lightbox="media/on-azure-stack/event-hub-input.png":::
   
   #### <a name="iot-hub"></a>centro de IoT

   | Campo | Value |
   | --- | --- |
   | Alias de entrada | Nombre descriptivo que se usará en la consulta del trabajo para hacer referencia a esta entrada. |
   | IoT Hub | Nombre de la instancia de IoT Hub que se usa como entrada. Un ejemplo es `<IoT Hub Name>.shanghai.azurestack.corp.microsoft.com`. |
   | El nombre de la directiva de acceso compartido | Directiva de acceso compartido que proporciona acceso a IoT Hub. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
   | Clave de directiva de acceso compartido | Clave de la directiva de acceso compartido que se usa para autorizar el acceso a IoT Hub. Esta opción se rellena automáticamente, a menos que seleccione la opción de proporcionar la configuración del centro de IoT manualmente. |
   | Grupo de consumidores (opcional) | Utilice un grupo de consumidores distinto para cada trabajo de Stream Analytics. El grupo de consumidores se usa para ingerir datos desde el centro de IoT. Stream Analytics usa el grupo de consumidores $Default, a menos que se especifique lo contrario. |
   | Recuento de particiones | El recuento de particiones es el número de particiones de un centro de eventos. |

   :::image type="content" source="media/on-azure-stack/iot-hub-input.png" alt-text="Captura de pantalla que muestra las entradas del centro de IoT." lightbox="media/on-azure-stack/iot-hub-input.png" :::

1. Mantenga los valores predeterminados de los demás campos y seleccione **Guardar**.
1. En **Topología de trabajo**, abra **Salidas** y, a continuación, seleccione **Agregar**.
1. Seleccione **Blob Storage**, **Centro de eventos** o **Centro de Microsoft Edge** en la lista desplegable.
1. Si la salida es un centro de eventos o Blob Storage en una suscripción de Azure Stack Hub, proporcione la información manualmente, como se muestra a continuación.

   #### <a name="event-hub"></a>Centro de eventos

   | Campo | Value |
   | --- | --- |
   | Alias de salida | Nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este centro de eventos. |
   | Espacio de nombres de Service Bus | Contenedor para un conjunto de entidades de mensajería. Al crear un nuevo centro de eventos, también se crea un espacio de nombres de Service Bus. Un ejemplo es `sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com`. |
   | Nombre del centro de eventos | Nombre de la salida del centro de eventos. |
   | Nombre de la directiva del centro de eventos | Directiva de acceso compartido, que puede crear en la pestaña **Configurar** del centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
   | Clave de la directiva del Centro de eventos | Clave de acceso compartido usada para autenticar el acceso al espacio de nombres del centro de eventos. |

   :::image type="content" source="media/on-azure-stack/event-hub-output.png" lightbox="media/on-azure-stack/event-hub-output.png" alt-text="Captura de pantalla que muestra las salidas del centro de eventos.":::
   #### <a name="blob-storage"></a>Blob Storage 

   | Campo | Value |
   | --- | --- |
   | Alias de salida | Un nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este almacenamiento de blobs. |
   | Cuenta de almacenamiento | Nombre de la cuenta de almacenamiento a donde está enviando la salida. Un ejemplo es `<Storage Account Name>.blob.shanghai.azurestack.corp.microsoft.com`. |
   | Clave de cuenta de almacenamiento | La clave secreta asociada con la cuenta de almacenamiento. Esta opción se rellena automáticamente, a menos que seleccione la opción de proporcionar la configuración de Blob Storage manualmente. |

> [!NOTE]
> El formato Parquet no se admite para los trabajos perimetrales en Azure Stack Hub. Para las filas **mínimas** y el tiempo **máximo**, especifique **0** o deje la opción en blanco.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Implementación de Stream Analytics en una máquina virtual o un dispositivo conectado a Azure Stack

1. En Azure Portal, abra IoT Hub. Vaya a **IoT Edge** y seleccione el dispositivo o máquina virtual que va a ser el destino de esta implementación.
1. Seleccione **Establecer módulos** >  **+ Agregar** y, a continuación, seleccione **Módulo de Azure Stream Analytics**. 
1. Seleccione la suscripción y el trabajo perimetral de Stream Analytics que creó. Seleccione **Guardar** y, a continuación, seleccione **Siguiente: Rutas**.

   :::image type="content" source="media/on-azure-stack/edge-modules.png" lightbox="media/on-azure-stack/edge-modules.png" alt-text="Captura de pantalla que muestra la incorporación de módulos.":::

1. Seleccione **Revisar y crear**.
1. En la pestaña **Revisar + crear**, seleccione **Crear**.

   :::image type="content" source="media/on-azure-stack/module-content.png" lightbox="media/on-azure-stack/module-content.png" alt-text="Captura de pantalla que muestra el manifiesto.":::

1. Confirme que el módulo se agrega a la lista.

   :::image type="content" source="media/on-azure-stack/edge-deployment.png" lightbox="media/on-azure-stack/edge-deployment.png" alt-text="Captura de pantalla en la que se muestra la página de la implementación.":::
## <a name="next-steps"></a>Pasos siguientes
- [Azure Stream Analytics en IoT Edge](./stream-analytics-edge.md)
- [Desarrollo de trabajos perimetrales de Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
