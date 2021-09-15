---
title: Gestión automática de dispositivos mediante el servicio de aprovisionamiento de dispositivos
titleSuffix: Azure Digital Twins
description: Consulte cómo configurar un proceso automatizado para aprovisionar y retirar dispositivos IoT en Azure Digital Twins mediante Device Provisioning Service (DPS).
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7078c136ebb072b3834af07e69fbd7d3c3d82a2b
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771660"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Administración automática de dispositivos en Azure Digital Twins mediante Device Provisioning Service (DPS)

En este artículo, aprenderá a integrar Azure Digital Twins con [Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md).

La solución que se describe en este artículo le permitirá automatizar el proceso de **_aprovisionar_** y **_retirar_** dispositivos IoT Hub de Azure Digital Twins mediante Device Provisioning Service. 

Para más información sobre las etapas de _aprovisionamiento_ y _retirada_ y para comprender mejor el conjunto de etapas generales de administración de dispositivos que son comunes a todos los proyectos de IoT empresarial, consulte la sección [Ciclo de vida de dispositivo](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) de la documentación de administración de dispositivos IoT Hub.

## <a name="prerequisites"></a>Requisitos previos

Antes de configurar el aprovisionamiento, debe configurar los recursos siguientes:
* una **instancia de Azure Digital Twins**. Siga las instrucciones que se indican en [Configuración de una instancia de Azure Digital Twins y autenticación (portal)](how-to-set-up-instance-portal.md) para crear una instancia de gemelos digitales de Azure. Recopile el nombre de **_host_** de la instancia en el Azure portal ([instrucciones](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* un **centro de IoT**. Para obtener instrucciones, consulte la sección Creación de una instancia de IoT Hub del [inicio rápido de IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md).
* una [función de Azure](../azure-functions/functions-overview.md) que actualiza la información de doble digital basada en datos de IOT Hub. Siga las instrucciones de [Ingesta de datos de IoT Hub](how-to-ingest-iot-hub-data.md) para crear esta función de Azure. Recopile el **_nombre_** de la función para usarlo en este artículo.

En este ejemplo también se usa un **simulador de dispositivos** que incluye el aprovisionamiento mediante Device Provisioning Service. El simulador de dispositivos se encuentra aquí: [Ejemplo de integración de Azure Digital Twins e IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Para obtener el proyecto de ejemplo en la máquina, vaya al vínculo del ejemplo y seleccione el botón **Examinar código** situado debajo del título. Este botón le llevará al repositorio de GitHub del ejemplo, que puede descargar como archivo .zip si selecciona el botón **Código** y **Descargar archivo ZIP**. 

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png" alt-text="Captura de pantalla del repositorio digital-twins-iothub-integration en GitHub, donde están resaltados los pasos necesarios para descargarlo en forma de archivo ZIP." lightbox="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png":::

Descomprima la carpeta descargada.

Asegúrese de tener [Node.js](https://nodejs.org/download) instalado en la máquina. El simulador de dispositivos se basa en **Node.js**, versión 10.0.x u otra posterior.

## <a name="solution-architecture"></a>Arquitectura de la solución

Esta solución incluye los pasos necesarios para aprovisionar y retirar un dispositivo en Azure Digital Twins mediante Device Provisioning Service.

Para asignar dispositivos en la solución, fluyen datos entre un dispositivo termostato y DPS. Luego, los datos fluyen entre DPS e IoT Hub y Azure Digital Twins a través de una función de Azure.

Para retirar un dispositivo, los datos de eliminación manual del dispositivos fluyen a Azure Digital Twins a través de IoT Hub, Event Hubs y una función de Azure.

En la imagen siguiente se ilustra esta arquitectura.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/flows.png" alt-text="Diagrama de un dispositivo y varios servicios de Azure en un escenario de extremo a extremo que muestra el flujo de datos." lightbox="media/how-to-provision-using-device-provisioning-service/flows.png":::

Este artículo se divide en dos secciones, y cada una de ellas se centra en una parte de esta arquitectura completa:
* [Aprovisionamiento automático de un dispositivo mediante Device Provisioning Service](#auto-provision-device-using-device-provisioning-service)
* [Retirada automática del dispositivo mediante eventos de ciclo de vida de IoT Hub](#auto-retire-device-using-iot-hub-lifecycle-events)

## <a name="auto-provision-device-using-device-provisioning-service"></a>Aprovisionamiento automático de un dispositivo mediante Device Provisioning Service

En esta sección, va a conectar Device Provisioning Service a Azure Digital Twins para aprovisionar automáticamente los dispositivos a través de la ruta de acceso siguiente. Este diagrama es un extracto de la arquitectura completa que se ha mostrado [anteriormente](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/provision.png" alt-text="Diagrama de flujo de aprovisionamiento: un extracto del diagrama de la arquitectura de la solución que sigue los datos de un termostato en Azure Digital Twins." lightbox="media/how-to-provision-using-device-provisioning-service/provision.png":::

Esta es una descripción del flujo del proceso:
1. El dispositivo se pone en contacto con el punto de conexión de DPS y pasa la información de identificación para demostrar su identidad.
2. DPS valida la identidad del dispositivo mediante la validación del identificador y la clave de registro en comparación con la lista de inscripción y llama a una [función de Azure](../azure-functions/functions-overview.md) para realizar la asignación.
3. La función de Azure crea un nuevo [gemelo](concepts-twins-graph.md) en Azure Digital Twins para el dispositivo. El gemelo tendrá el mismo nombre que el **identificador de registro** del dispositivo.
4. DPS registra el dispositivo con un centro de IoT y rellena el estado deseado de los dispositivos gemelos.
5. El centro de IoT devuelve la información sobre la identidad del dispositivo y la conexión del centro de IoT al dispositivo. Ahora el dispositivo puede conectarse al centro de IoT.

En las siguientes secciones se describen los pasos necesarios para configurar este flujo de aprovisionamiento automático del dispositivo.

### <a name="create-a-device-provisioning-service"></a>Creación de una instancia de Device Provisioning Service

Cuando se aprovisiona un nuevo dispositivo mediante el servicio de aprovisionamiento de dispositivos, se puede crear un nuevo dispositivo gemelo para ese dispositivo en Azure Digital Twins con el mismo nombre como id. de registro.

Cree una instancia de Device Provisioning Service que se usará para aprovisionar dispositivos IoT. Puede usar las instrucciones de la CLI de Azure que aparecen a continuación o usar Azure Portal siguiendo las instrucciones de [Inicio rápido: Configuración de Azure IoT Hub Device Provisioning Service con Azure Portal](../iot-dps/quick-setup-auto-provision.md).

El siguiente comando de la CLI de Azure creará una instancia de Device Provisioning Service. Deberá especificar un nombre de servicio de aprovisionamiento de dispositivos, un grupo de recursos y una región. Para ver qué regiones admiten el Servicio de aprovisionamiento de dispositivos, visite [Productos de Azure disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).
El comando se puede ejecutar en [Cloud Shell](https://shell.azure.com), o bien localmente si tiene la [CLI de Azure está instalada en la máquina](/cli/azure/install-azure-cli).

```azurecli-interactive
az iot dps create --name <Device-Provisioning-Service-name> --resource-group <resource-group-name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Incorporación de una función que se va a usar con el servicio aprovisionamiento de dispositivos

En el proyecto de la aplicación de funciones que creó en la [sección de requisitos previos](#prerequisites) creará una función que se usará con Device Provisioning Service. Device Provisioning Service usará esta función en una [directiva de asignación personalizada](../iot-dps/how-to-use-custom-allocation-policies.md) para aprovisionar un nuevo dispositivo.

Para empezar, abra el proyecto de aplicación de función en Visual Studio en el equipo y siga estos pasos.

1. Primero, cree una función de tipo *HTTP-trigger* en el proyecto de aplicación de función en Visual Studio. Para instrucciones sobre cómo crear esta función, consulte [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project).

2. Agregue un nuevo paquete de NuGet al proyecto: [Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Es posible que también tenga que agregar más paquetes al proyecto si los paquetes usados en el código ya no forman parte del proyecto.

3. En el archivo de código de la función recién creada, pegue el código siguiente, cambie el nombre de la función a *DpsAdtAllocationFunc.cs* y guarde el archivo.

    :::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

4. Publique el proyecto con la función *DpsAdtAllocationFunc.cs* en la aplicación de funciones en Azure. Para instrucciones sobre cómo publicar el proyecto, consulte [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> La primera vez que se crea la aplicación de funciones en la [sección de requisitos previos](#prerequisites), es posible que ya se haya asignado un rol de acceso para la función y se haya configurado la aplicación para que acceda a la instancia de Azure Digital Twins. Estos deben realizarse una vez para toda la aplicación de función, por lo que debe comprobar que se han completado en la aplicación antes de continuar. Puede encontrar instrucciones en la sección [Configuración de la aplicación publicada](how-to-authenticate-client.md#configure-published-app) del artículo *Escritura de código de autenticación de aplicaciones*.

### <a name="create-device-provisioning-enrollment"></a>Creación de una inscripción de Device Provisioning Service

A continuación, deberá crear una inscripción en Device Provisioning Service mediante una **función de asignación personalizada**. Para crear una inscripción, siga las instrucciones de la sección [Creación de la inscripción](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) del artículo directivas de asignación personalizadas de la documentación del servicio aprovisionamiento de dispositivos.

Al pasar por ese flujo, asegúrese de seleccionar las opciones siguientes para vincular la inscripción a la función que ha creado.

* **Seleccione cómo quiere asignar los dispositivos a los centros**: Personalizado (usar una función de Azure).
* **Seleccione los centros de IOT a los que se puede asignar este grupo:** Elija el nombre del centro de IoT o seleccione el botón *vincular un nuevo centro de IOT* y elija su instancia de IOT Hub en la lista desplegable.

A continuación, elija el botón *Seleccionar una función nueva* para vincular la aplicación de función al grupo de inscripción. Después, rellene los siguientes valores:

* **Suscripción**: la suscripción de Azure se rellena automáticamente. Asegúrese de que es la suscripción correcta.
* **Aplicación de funciones**:elija el nombre de la aplicación de funciones.
* **Función**: Elija DpsAdtAllocationFunc.

Guarde sus detalles.                  

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Captura de pantalla de la ventana de detalles del grupo de inscripción Customs en Azure Portal." lightbox="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png":::

Después de crear la inscripción, la **clave principal** para la inscripción se usará más adelante para configurar el simulador de dispositivos de este artículo.

### <a name="set-up-the-device-simulator"></a>Configuración del simulador de dispositivos

En este ejemplo se usa un simulador de dispositivos que incluye el aprovisionamiento mediante Device Provisioning Service. El simulador de dispositivos se encuentra en el [ejemplo de integración de Azure Digital Twins e IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/) que descargó en la [sección de requisitos previos](#prerequisites).

#### <a name="upload-the-model"></a>Carga del modelo

El simulador de dispositivos es un dispositivo de tipo termostato que usa el modelo con este identificador: `dtmi:contosocom:DigitalTwins:Thermostat;1`. Tendrá que cargar este modelo en Azure Digital Twins para poder crear un gemelo de este tipo para el dispositivo.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Para más información sobre los modelos, consulte [Administración de modelos](how-to-manage-model.md#upload-models).

#### <a name="configure-and-run-the-simulator"></a>Configuración y ejecución del simulador

En la ventana de comandos, vaya hasta el ejemplo descargado *Integración de Azure Digital Twins y IoT Hub* que descomprimió anteriormente y, a continuación, en el directorio *Simulador de dispositivo*. Luego, instale las dependencias del proyecto mediante el siguiente comando:

```cmd
npm install
```

A continuación, en el directorio del simulador de dispositivos, copie el archivo. env.template en un nuevo archivo denominado. env y recopile los siguientes valores para rellenar la configuración:

* PROVISIONING_IDSCOPE: para obtener este valor, vaya al servicio de aprovisionamiento de dispositivos en [Azure Portal](https://portal.azure.com/), seleccione *Información general* en las opciones de menú y busque el *ámbito de identificador* de campo.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/id-scope.png" alt-text="Captura de pantalla de la vista de Azure Portal de la página información general de aprovisionamiento de dispositivos que resalta el valor de Ámbito de id." lightbox="media/how-to-provision-using-device-provisioning-service/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: puede elegir un identificador de registro para el dispositivo.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: esta variable de entorno es la clave principal de la inscripción que configuró anteriormente. Para obtener este valor de nuevo, vaya al servicio de aprovisionamiento de dispositivos en el Azure Portal, seleccione *administrar inscripciones* y, luego, seleccione el grupo de inscripción que creó anteriormente y copie la *clave principal*.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png" alt-text="Captura de pantalla de la vista de Azure Portal de la página de inscripciones de administración de Device Provisioning Service que resalta el valor de la clave principal de SAS." lightbox="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png":::

Ahora, use los valores anteriores para actualizar la configuración del archivo. env.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device-Provisioning-Service-Scope-ID>"
PROVISIONING_REGISTRATION_ID = "<Device-Registration-ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device-Provisioning-Service-enrollment-primary-SAS-key>"
```

Guarde y cierre el archivo.

### <a name="start-running-the-device-simulator"></a>Iniciar ejecución del simulador de dispositivos

Todavía en el directorio *device-simulator* de la ventana de comandos, inicie el simulador de dispositivos mediante el comando siguiente:

```cmd
node .\adt_custom_register.js
```

Debería ver que el dispositivo se registra y se conecta a IoT Hub y, a continuación, comienza a enviar mensajes.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/output.png" alt-text="Captura de pantalla de la ventana Comandos que muestra el registro de dispositivos y el envío de mensajes." lightbox="media/how-to-provision-using-device-provisioning-service/output.png":::

### <a name="validate"></a>Validación

El flujo que ha configurado en este artículo derivará en el registro automático del dispositivo en Azure Digital Twins. Use el siguiente comando de la [CLI de Azure Digital Twins](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) para buscar el gemelo del dispositivo de la instancia de Azure Digital Twins que ha creado.

```azurecli-interactive
az dt twin show --dt-name <Digital-Twins-instance-name> --twin-id "<Device-Registration-ID>"
```

Debería ver el gemelo del dispositivo que se encuentra en la instancia de Azure Digital Twins.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png" alt-text="Captura de pantalla del ventana Comandos mostrando gemelas recién creados." lightbox="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Retirada automática del dispositivo mediante eventos de ciclo de vida de IoT Hub

En esta sección va a asociar los eventos de ciclo de vida de IoT Hub a Azure Digital Twins para retirar automáticamente los dispositivos mediante la ruta de acceso siguiente. Este diagrama es un extracto de la arquitectura completa que se ha mostrado [anteriormente](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/retire.png" alt-text="Diagrama del flujo de retirada de dispositivo: un extracto del diagrama de la arquitectura de la solución, que sigue los datos de la eliminación de un dispositivo en Azure Digital Twins." lightbox="media/how-to-provision-using-device-provisioning-service/retire.png":::

Esta es una descripción del flujo del proceso:
1. Un proceso externo o manual desencadena la eliminación de un dispositivo en IoT Hub.
2. IoT Hub elimina el dispositivo y genera un evento de [ciclo de vida de dispositivo](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) que se va a enrutar a un [centro de eventos](../event-hubs/event-hubs-about.md).
3. Una función de Azure elimina el gemelo del dispositivo en Azure Digital Twins.

En las siguientes secciones se describen los pasos necesarios para configurar este flujo de retirada automática del dispositivo.

### <a name="create-an-event-hub"></a>Creación de un centro de eventos

A continuación, crearás un [centro de eventos](../event-hubs/event-hubs-about.md) de Azure para recibir los eventos del ciclo de vida de IoT Hub. 

Siga los pasos descritos en el inicio rápido para [crear un centro de eventos](../event-hubs/event-hubs-create.md). Asigne un nombre *lifecycleevents* al centro de eventos. Usará este nombre de centro de eventos al configurar la ruta IoT Hub y una función de Azure en las secciones siguientes.

En la captura de pantalla siguiente se muestra la creación del centro de eventos.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png" alt-text="Captura de pantalla de la ventana de Azure Portal que muestra cómo crear un centro de eventos con el nombre lifecycleevents." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Creación de una directiva SAS para el centro de eventos

A continuación, deberá crear una directiva de [firma de acceso compartido (SAS)](../event-hubs/authorize-access-shared-access-signature.md) para configurar el centro de eventos con la aplicación de función.
Para crear la directiva de SAS:
1. Vaya al centro de eventos que ha creado en Azure Portal y seleccione **Directivas de acceso compartido** en las opciones de menú de la izquierda.
2. Seleccione **Agregar**. En la ventana *Agregar Directiva SAS* que se abre, escriba un nombre de directiva de su elección y active la casilla *escuchar*.
3. Seleccione **Crear**.
    
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png" alt-text="Captura de pantalla de Azure Portal que muestra cómo agregar una directiva SAS del centro de eventos." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Configurar el centro de eventos con la aplicación de funciones

Después, configure la aplicación de funciones de Azure que configuró en la [sección de requisitos previos](#prerequisites) para trabajar con el nuevo centro de eventos. La función se configura al establecer una variable de entorno dentro de la aplicación de funciones con la cadena de conexión del centro de eventos.

1. Abra la directiva que ha creado y copie el valor de **Cadena de conexión: clave principal**.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png" alt-text="Captura de pantalla de Azure Portal que muestra cómo copiar la cadena de conexión: clave principal." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png":::

2. Agregue la cadena de conexión como una variable en la configuración de la aplicación de función con el siguiente comando de CLI de Azure. El comando se puede ejecutar en [Cloud Shell](https://shell.azure.com), o bien localmente si tiene la [CLI de Azure está instalada en la máquina](/cli/azure/install-azure-cli).

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event-Hubs-SAS-connection-string-Listen>" --resource-group <resource-group> --name <your-function-app-name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Agregar una función para retirar con eventos de ciclo de vida de IoT Hub

En el proyecto de aplicación de funciones que creó en la [sección de requisitos previos](#prerequisites), creará una función para retirar un dispositivo existente mediante eventos del ciclo de vida de IoT Hub.

Para más información sobre los eventos de ciclo de vida, consulte [Eventos que no son de telemetría de IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Para más información sobre cómo usar Event Hubs con funciones de Azure, consulte [Desencadenador de Azure Event Hubs para Azure Functions](../azure-functions/functions-bindings-event-hubs-trigger.md).

Para empezar, abra el proyecto de aplicación de función en Visual Studio en el equipo y siga estos pasos.

1. Primero, cree una función de tipo *Desencadenador del centro de eventos* en el proyecto de aplicación de funciones en Visual Studio. Para instrucciones sobre cómo crear esta función, consulte [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project).

2. Agregue un nuevo paquete de NuGet al proyecto: [Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Es posible que también tenga que agregar más paquetes al proyecto si los paquetes usados en el código ya no forman parte del proyecto.

3. En el archivo de código de la función recién creada, pegue el código siguiente, cambie el nombre de la función a *DeleteDeviceInTwinFunc.cs* y guarde el archivo.

    :::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

4. Publique el proyecto con la función *DeleteDeviceInTwinFunc.cs* en una aplicación de funciones en Azure. Para instrucciones sobre cómo publicar el proyecto, consulte [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> La primera vez que se crea la aplicación de funciones en la [sección de requisitos previos](#prerequisites), es posible que ya se haya asignado un rol de acceso para la función y se haya configurado la aplicación para que acceda a la instancia de Azure Digital Twins. Estos deben realizarse una vez para toda la aplicación de función, por lo que debe comprobar que se han completado en la aplicación antes de continuar. Puede encontrar instrucciones en la sección [Configuración de la aplicación publicada](how-to-authenticate-client.md#configure-published-app) del artículo *Escritura de código de autenticación de aplicaciones*.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Creación de una ruta de IoT Hub para eventos de ciclo de vida

Ahora configurará una ruta de IoT Hub para enrutar los eventos de ciclo de vida del dispositivo. En este caso, escuchará específicamente los eventos de eliminación de dispositivos, que se identifican mediante `if (opType == "deleteDeviceIdentity")`. Este evento desencadenará la eliminación del elemento gemelo digital, finalizando la retirada del dispositivo y de su gemelo digital.

En primer lugar, deberá crear un punto de conexión del centro de eventos en el centro de IoT. A continuación, agregará una ruta en IoT hub para enviar eventos de ciclo de vida a este punto de conexión del centro de eventos.
Siga estos pasos para crear un punto de conexión del centro de eventos:

1. En [Azure Portal](https://portal.azure.com/), vaya a la instancia de IoT Hub que creó en la [sección de requisitos previos](#prerequisites) y seleccione **Enrutamiento de mensajes** en las opciones de menú de la izquierda.
2. Seleccione la pestaña **Puntos de conexión personalizados**.
3. Seleccione **+ Agregar** y elija **Centro de eventos** para agregar un punto de conexión de tipo de centro de eventos.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png" alt-text="Captura de pantalla de la ventana de Visual Studio que muestra cómo agregar un punto de conexión personalizado del centro de eventos." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png":::

4. En la ventana *Agregar un punto de conexión del centro de eventos* que se abre, elija los siguientes valores:
    * **Nombre del punto de conexión**: elija un nombre de punto de conexión.
    * **Espacio de nombres del centro de eventos**: seleccione el espacio de nombres del centro de eventos en la lista desplegable.
    * **Instancia de centro de eventos**: elija el nombre del centro de eventos que creó en el paso anterior.
5. Seleccione **Crear**. Mantenga esta ventana abierta para agregar una ruta en el paso siguiente.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png" alt-text="Captura de pantalla de la ventana de Visual Studio que muestra cómo agregar un punto de conexión del centro de eventos." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png":::

A continuación, agregará una ruta que se conecta al punto de conexión que creó en el paso anterior, con una consulta de enrutamiento que envía los eventos de eliminación. Siga estos pasos para crear una ruta:

1. Vaya a la pestaña *rutas* y seleccione **Agregar** para agregar una ruta.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-message-route.png" alt-text="Captura de pantalla de la ventana de Visual Studio que muestra cómo agregar una ruta para enviar eventos." lightbox="media/how-to-provision-using-device-provisioning-service/add-message-route.png":::

2. En la página *Agregar una ruta* que se abre, elija los siguientes valores:

   * **Nombre**: elija un nombre para el módulo. 
   * **Punto de conexión**: elija el punto de conexión de centro de eventos que creó anteriormente en la lista desplegable.
   * **Origen de datos**: elija *eventos del ciclo de vida del dispositivo*.
   * **Consulta de enrutamiento**: Escriba `opType='deleteDeviceIdentity'`. Esta consulta limita los eventos de ciclo de vida del dispositivo para enviar solo los eventos de eliminación.

3. Seleccione **Guardar**.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png" alt-text="Captura de pantalla de la ventana de Azure Portal que muestra cómo agregar una ruta a los eventos de ciclo de vida de envío." lightbox="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png":::

Una vez que haya pasado por este flujo, todo estará listo para la completa retirada de los dispositivos.

### <a name="validate"></a>Validación

Para desencadenar el proceso de retirada, debe eliminar manualmente el dispositivo de IoT Hub.

Puede eliminar manualmente el dispositivo desde IoT Hub con un [comando de la CLI de Azure](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) o desde Azure Portal. Siga los siguientes pasos para eliminar el dispositivo en Azure Portal:

1. Vaya a IoT Hub y elija **dispositivos IoT** en las opciones de menú de la izquierda. 
2. Verá un dispositivo con el identificador de registro de dispositivo que eligió en la [primera mitad de este artículo](#auto-provision-device-using-device-provisioning-service). También puede elegir cualquier otro dispositivo que desee eliminar, siempre que tenga un gemelo en Azure Digital Twins, de modo que pueda comprobar que el gemelo se elimina automáticamente después de eliminar el dispositivo.
3. Seleccione el dispositivo y elija **Eliminar**.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png" alt-text="Captura de pantalla del Azure Portal que muestra cómo eliminar el dispositivo gemelo de los dispositivos IoT." lightbox="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png":::

Puede tardar unos minutos en ver los cambios reflejados en Azure Digital Twins.

Use el siguiente comando de la [CLI de Azure Digital Twins](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) para comprobar el gemelo del dispositivo de la instancia de Azure Digital Twins que ha eliminado.

```azurecli-interactive
az dt twin show --dt-name <Digital-Twins-instance-name> --twin-id "<Device-Registration-ID>"
```

Debería ver que el gemelo del dispositivo ya no se encuentra en la instancia de Azure Digital Twins.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png" alt-text="Captura de pantalla ventana Comandos que muestra que ya no se encuentra el gemelo." lightbox="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no necesite los recursos creados en este artículo, siga estos pasos para eliminarlos.

Con Azure Cloud Shell o la CLI de Azure local, puede eliminar todos los recursos de Azure de un grupo de recursos mediante el comando [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete). Este comando permite eliminar el grupo de recursos, la instancia de Azure Digital Twins, el centro de IoT y el registro del dispositivo del centro de conectividad, el tema de Event Grid y las suscripciones asociadas, y el espacio de nombres de los centros de eventos y ambas aplicaciones de Azure Functions, incluidos los recursos asociados, como el almacenamiento.

> [!IMPORTANT]
> La eliminación de un grupo de recursos es irreversible. El grupo de recursos y todos los recursos contenidos en él se eliminan permanentemente. Asegúrese de no eliminar por accidente el grupo de recursos o los recursos equivocados. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Por último, elimine la carpeta de ejemplo del proyecto que descargó de la máquina local.

## <a name="next-steps"></a>Pasos siguientes

Los gemelos digitales que se han creado para los dispositivos se almacenan como una jerarquía plana en Azure Digital Twins, pero se pueden enriquecer con la información del modelo y una jerarquía de varios niveles para la organización. Para más información sobre este concepto, consulte:

* [Gemelos digitales y el grafo de gemelos](concepts-twins-graph.md)

Para más información sobre cómo usar solicitudes HTTP con funciones de Azure, consulte:

* [Desencadenador de solicitud HTTP de Azure para Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md)

Puede escribir la lógica personalizada para proporcionar automáticamente esta información con los datos del modelo y del gráfico ya almacenados en Azure Digital Twins. Para más información sobre cómo administrar, actualizar y recuperar información del grafo de gemelos, consulte las guías paso a paso siguientes:

* [Administración de un gemelo digital](how-to-manage-twin.md)
* [Consulta del grafo de gemelos](how-to-query-graph.md)