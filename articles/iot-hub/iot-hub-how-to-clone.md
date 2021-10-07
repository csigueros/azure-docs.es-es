---
title: Procedimiento para clonar un centro de Azure IoT Hub
description: Procedimiento para clonar un centro de Azure IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: 4e66c71dd274f6096113992d9584645d5622f1c1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211137"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Procedimiento para clonar un centro de Azure IoT Hub en otra región

En este artículo se exploran las formas de clonar un centro de IoT Hub y se proporcionan algunas preguntas que debe responder antes de empezar. Estos son algunos de los motivos por los que podría querer clonar un centro de IoT Hub:
 
* Va a trasladar su empresa de una región a otra (por ejemplo, de Europa a Norteamérica o viceversa) y quiere que sus recursos y datos estén geográficamente cerca de la nueva ubicación, por lo que debe trasladar el centro.

* Va a configurar un centro para un entorno de desarrollo frente al de producción.

* Quiere realizar una implementación personalizada de alta disponibilidad de varios centros. Para obtener más información, consulte la sección [Lograr alta disponibilidad entre regiones](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Quiere aumentar el número de [particiones](iot-hub-scaling.md#partitions) configuradas para el centro. Este valor se establece cuando se crea el centro y no se puede cambiar. Puede usar la información de este artículo para clonar el centro y, cuando se cree el clon, aumentar el número de particiones.

Para clonar un centro, necesita una suscripción con acceso administrativo al centro original. Puede colocar el nuevo centro en un grupo de recursos y una región nuevos, en la misma suscripción que el centro original o incluso en una nueva suscripción. No puede usar el mismo nombre porque el nombre del centro debe ser único globalmente.

> [!NOTE]
> En este momento, no hay ninguna característica disponible para clonar una instancia de IoT Hub de forma automática. Se trata principalmente de un proceso manual, por lo que es bastante propenso a errores. La complejidad de clonar un centro es directamente proporcional a la del propio centro. Por ejemplo, clonar un centro de IoT Hub sin enrutamiento de mensajes es bastante sencillo. Si agrega el enrutamiento de mensajes como única complejidad, clonar el centro se convierte en, por lo menos, un orden de magnitud más complicado. Si también mueve los recursos usados para enrutar los puntos de conexión, es otro orden de magnitud más complicado. 

## <a name="things-to-consider"></a>Aspectos que se deben tener en cuenta:

Hay varios aspectos que se deben tener en cuenta antes de clonar un centro de IoT Hub.

* Asegúrese de que todas las características disponibles en la ubicación original también lo están en la nueva. Algunos servicios se encuentran en versión preliminar y no todas las características están disponibles en todas partes.

* No quite los recursos originales antes de crear y comprobar la versión clonada. Una vez que quita un centro, este deja de existir y no hay manera de recuperarlo para comprobar la configuración ni los datos para asegurarse de que el centro se ha replicado correctamente.

* Muchos recursos requieren nombres únicos globales, por lo que debe usar nombres diferentes para las versiones clonadas. También debe usar un nombre diferente para el grupo de recursos al que pertenece el centro clonado. 

* No se migran los datos del centro de IoT Hub original. Esto incluye los mensajes de telemetría, los comandos de la nube al dispositivo (C2D) y la información relacionada con el trabajo (como los programas y el historial). Tampoco se migran las métricas ni los resultados de los registros. 

* En el caso de los datos o los mensajes enrutados a Azure Storage, puede dejar los datos en la cuenta de almacenamiento original, transferir esos datos a una nueva cuenta de almacenamiento en la nueva región o dejar los datos antiguos en su lugar y crear una cuenta de almacenamiento en la nueva ubicación para los nuevos datos. Para obtener más información sobre cómo trasladar datos en Blob Storage, consulte [Introducción a AzCopy](../storage/common/storage-use-azcopy-v10.md).

* No se pueden migrar los datos de Event Hubs ni de temas y colas de Service Bus. Son datos de un momento dado y no se almacenan después de que se procesen los mensajes.

* Debe programar el tiempo de inactividad de la migración. La clonación de los dispositivos en el nuevo centro lleva tiempo. Si va a usar el método Import/Export, las pruebas comparativas han revelado que podría tardar aproximadamente dos horas en trasladar 500 000 dispositivos y cuatro horas en mover un millón de dispositivos. 

* Puede copiar los dispositivos en el centro nuevo sin apagarlos ni cambiarlos. 

    * Si los dispositivos se habían aprovisionado originalmente mediante DPS, al volver a aprovisionarlos se actualiza la información de conexión almacenada en cada dispositivo. 
    
    * En caso contrario, tiene que usar el método Import/Export para trasladar los dispositivos y, después, los dispositivos deben modificarse para usar el nuevo centro. Por ejemplo, puede configurar el dispositivo para que use el nombre de host de IoT Hub de las propiedades gemelas deseadas. El dispositivo tomará el nombre de host de IoT Hub, desconectará el dispositivo del centro antiguo y volverá a conectarlo al nuevo.
    
* Debe actualizar los certificados que esté usando para poder utilizarlos con los nuevos recursos. Además, es probable que tenga el centro definido en una tabla DNS en algún lugar, de modo que tendrá que actualizar esa información de DNS.

## <a name="methodology"></a>Metodología

Este es el método general que se recomienda para mover un centro de IoT Hub de una región a otra. En el caso del enrutamiento de mensajes, se supone que los recursos no se mueven a la nueva región. Para obtener más información, consulte la [sección sobre el enrutamiento de mensajes](#how-to-handle-message-routing).

   1. Exporte el centro y su configuración a una plantilla de Resource Manager. 
   
   1. Realice los cambios necesarios en la plantilla, por ejemplo, actualizar todas las apariciones del nombre y la ubicación del centro clonado. Si tiene algún recurso en la plantilla que se use para los puntos de conexión de enrutamiento de mensajes, actualice la clave de ese recurso en la plantilla.
   
   1. Importe la plantilla en un nuevo grupo de recursos en la nueva ubicación. De esta forma, se crea el clon.

   1. Realice la depuración pertinente según sea necesario. 
   
   1. Agregue todo lo que no se haya exportado a la plantilla. 
   
       Por ejemplo, los grupos de consumidores no se exportan a la plantilla. Tiene que agregar los grupos de consumidores a la plantilla manualmente o usar [Azure Portal](https://portal.azure.com) después de crear el centro. Hay un ejemplo de cómo agregar un grupo de consumidores a una plantilla en el artículo [Uso de una plantilla de Azure Resource Manager para configurar el enrutamiento de mensajes de IoT Hub](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Copie los dispositivos del centro original al clon. Esto se trata en la sección [Administración de los dispositivos registrados en el centro de IoT Hub](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Procedimiento para controlar el enrutamiento de mensajes

Si el centro usa el [enrutamiento personalizado](iot-hub-devguide-messages-read-custom.md), la exportación de la plantilla del centro incluye la configuración del enrutamiento, pero no incluye los recursos. Debe elegir si quiere mover los recursos de enrutamiento a la nueva ubicación o dejarlos en su lugar y seguir usándolos "tal cual". 

Por ejemplo, supongamos que tiene un centro en el Oeste de EE. UU. que enruta los mensajes a una cuenta de almacenamiento (también en el Oeste de EE. UU.) y quiere trasladar el centro al Este de EE. UU. Puede trasladar el centro y hacer que siga enrutando los mensajes a la cuenta de almacenamiento del Oeste de EE. UU., o bien puede trasladar el centro y la cuenta de almacenamiento. El enrutamiento de mensajes a los recursos del punto de conexión en una región diferente puede afectar un poco al rendimiento.

Puede trasladar un centro que use el enrutamiento de mensajes con bastante facilidad si no mueve también los recursos usados para los puntos de conexión de enrutamiento. 

Si el centro usa el enrutamiento de mensajes, tiene dos opciones. 

1. Mueva los recursos usados para los puntos de conexión de enrutamiento a la nueva ubicación.

    * Debe crear los nuevos recursos manualmente en [Azure Portal](https://portal.azure.com) o mediante plantillas de Resource Manager. 

    * Debe cambiar el nombre de todos los recursos al crearlos en la nueva ubicación, ya que tienen nombres únicos globales. 
     
    * Debe actualizar los nombres y las claves de los recursos en la plantilla del nuevo centro, antes de crear este nuevo centro. Los recursos deben estar presentes cuando se cree el nuevo centro.

1. No mueva los recursos usados para los puntos de conexión de enrutamiento. Úselos "donde están".

   * En el paso en el que edite la plantilla, tendrá que recuperar las claves de cada recurso de enrutamiento y colocarlas en la plantilla antes de crear el nuevo centro. 

   * El centro sigue haciendo referencia a los recursos de enrutamiento originales y enruta los mensajes a ellos tal y como se ha configurado.

   * Habrá un pequeño impacto en el rendimiento porque el centro y los recursos del punto de conexión de enrutamiento no están en la misma ubicación.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Preparación de la migración del centro a otra región

En esta sección se proporcionan instrucciones específicas para migrar el centro.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Busque el centro original y expórtelo a una plantilla de recursos.

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 

1. Vaya a **Grupos de recursos** y seleccione el grupo de recursos que contiene el centro que quiere trasladar. También puede ir a **Recursos** y buscar el centro de este modo. Seleccione el centro.

1. Seleccione **Exportar plantilla** en la lista de propiedades y opciones de configuración del centro. 

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-export-template.png" alt-text="Captura de pantalla en la que se muestra el comando para exportar la plantilla de IoT Hub." border="true":::

1. Seleccione **Descargar** para descargar la plantilla. Guarde el archivo en algún lugar donde pueda encontrarlo de nuevo. 

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-download-template.png" alt-text="Captura de pantalla en la que se muestra el comando para descargar la plantilla de IoT Hub." border="true":::

### <a name="view-the-template"></a>Ver la plantilla 

1. Vaya a la carpeta Descargas (o a la carpeta que haya usado al exportar la plantilla) y busque el archivo .zip. Extraiga el archivo .zip y busque el archivo denominado `template.json`. Selecciónelo y cópielo. Vaya a una carpeta diferente y pegue el archivo de plantilla (CTRL+V). Ya puede editarlo.
 
    El ejemplo siguiente es para un centro genérico sin configuración de enrutamiento. Es un centro de nivel S1 (con una unidad) denominado **ContosoHub** en la región **westus**. Esta es la plantilla exportada.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoHub_connectionString": {
                "type": "SecureString"
            },
            "IotHubs_ContosoHub_containerName": {
                "type": "SecureString"
            },
            "IotHubs_ContosoHub_name": {
                "defaultValue": "ContosoHub",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2021-07-01",
                "name": "[parameters('IotHubs_ContosoHub_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "identity": {
                    "type": "None"
                },
                "properties": {
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 4
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "[parameters('IotHubs_ContosoHub_connectionString')]",
                            "containerName": "[parameters('IotHubs_ContosoHub_containerName')]"
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None",
                    "disableLocalAuth": false,
                    "allowedFqdnList": []
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>Edición de la plantilla 

Tendrá que realizar algunos cambios antes de poder usar la plantilla para crear el nuevo centro en la nueva región. Use [VS Code](https://code.visualstudio.com) o un editor de texto para editar la plantilla.

#### <a name="edit-the-hub-name-and-location"></a>Edición del nombre y la ubicación del centro

1. Quite la sección del parámetro de nombre del contenedor en la parte superior. **ContosoHub** no tiene un contenedor asociado.

    ``` json
    "parameters": {
      ...
        "IotHubs_ContosoHub_containerName": {
            "type": "SecureString"
        },
      ...
    },
    ```

1. Quite la propiedad **storageEndpoints**.

    ```json
    "properties": {
      ...
        "storageEndpoints": {
        "$default": {
            "sasTtlAsIso8601": "PT1H",
            "connectionString": "[parameters('IotHubs_ContosoHub_connectionString')]",
            "containerName": "[parameters('IotHubs_ContosoHub_containerName')]"
        }
      },
      ...
    
    ```

1. En **recursos**, cambie la ubicación de "westus" a "eastus".

    Versión anterior:

    ``` json 
    "location": "westus",
    ```
    
    Nueva versión: 

    ``` json 
    "location": "eastus",
    ```
#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Actualización de las claves de los recursos de enrutamiento que no se van a trasladar

Cuando exporte la plantilla de Resource Manager de un centro que tenga configurado el enrutamiento, verá que las claves de esos recursos no se proporcionan en la plantilla exportada, sino que su ubicación se indica mediante asteriscos. Para rellenarlos, debe ir a esos recursos en el portal y recuperar las claves **antes** de importar la plantilla del nuevo centro y crear el centro. 

1. Recupere las claves necesarias para cualquiera de los recursos de enrutamiento y colóquelas en la plantilla. Puede recuperar las claves desde el recurso en [Azure Portal](https://portal.azure.com). 

   Por ejemplo, si va a enrutar mensajes a un contenedor de almacenamiento, busque la cuenta de almacenamiento en el portal. En la sección Configuración, seleccione **Claves de acceso** y, después, copie una de las claves. Este es el aspecto que tendrá la clave cuando exporte la plantilla por primera vez:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Después de recuperar la clave de la cuenta de almacenamiento, colóquela en la plantilla en la cláusula `AccountKey=****` en lugar de los asteriscos. 

1. En el caso de las colas de Service Bus, obtenga la clave de acceso compartido que coincida con SharedAccessKeyName. Esta es la clave y el valor de `SharedAccessKeyName` en JSON:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Lo mismo se aplica a los temas de Service Bus y las conexiones de Event Hub.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Creación de los nuevos recursos de enrutamiento en la nueva ubicación

Esta sección solo se aplica si va a trasladar los recursos que usa el centro para los puntos de conexión de enrutamiento.

Si quiere trasladar los recursos de enrutamiento, debe configurar manualmente los recursos en la nueva ubicación. Puede crear los recursos de enrutamiento mediante [Azure Portal](https://portal.azure.com), o bien puede exportar la plantilla de Resource Manager de cada uno de los recursos que usa el enrutamiento de mensajes, editarlos e importarlos. Una vez configurados los recursos, puede importar la plantilla del centro (que incluye la configuración de enrutamiento).

1. Cree cada recurso que usa el enrutamiento. Puede hacerlo manualmente mediante [Azure Portal](https://portal.azure.com) o crear los recursos con plantillas de Resource Manager. Si quiere usar plantillas, estos son los pasos que debe seguir:

    1. Exporte cada recurso que use el enrutamiento a una plantilla de Resource Manager.
    
    1. Actualice el nombre y la ubicación del recurso. 

    1. Actualice las referencias cruzadas que haya entre los recursos. Por ejemplo, si crea una plantilla para una nueva cuenta de almacenamiento, debe actualizar el nombre de la cuenta de almacenamiento en esa plantilla y en cualquier otra que haga referencia a él. En la mayoría de los casos, la sección de enrutamiento de la plantilla del centro es la otra única plantilla que hace referencia al recurso. 

    1. Importe cada una de las plantillas, lo que implementa cada recurso.

    Una vez que los recursos que use el enrutamiento estén configurados y en ejecución, puede continuar.

1. En la plantilla del centro de IoT Hub, cambie el nombre de cada uno de los recursos de enrutamiento por su nuevo nombre y actualice la ubicación si es necesario. 

Ya dispone de una plantilla que creará un nuevo centro que se parece casi exactamente al centro antiguo; dependerá de cómo haya decidido controlar el enrutamiento.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Traslado: carga de la plantilla para crear el nuevo centro en la nueva región

Cree el nuevo centro en la nueva ubicación mediante la plantilla. Si tiene recursos de enrutamiento que se van a trasladar, los recursos deben configurarse en la nueva ubicación y las referencias de la plantilla deben actualizarse para que coincidan. Si no va a trasladar los recursos de enrutamiento, deben estar en la plantilla con las claves actualizadas.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **Crear un recurso**. 

1. En el cuadro de búsqueda, escriba "template deployment" (implementación de plantillas) y seleccione "Entrar".

1. Seleccione **Template deployment (deploy using custom templates)** (Implementación de plantillas [implementar mediante plantillas personalizadas]). Esto le lleva a la pantalla Template Deployment. Seleccione **Crear**. Verá esta pantalla:

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png" alt-text="Captura de pantalla en la que se muestra el comando para crear su propia plantilla":::

1. Seleccione **Cree su propia plantilla en el editor**, lo que le permite cargar la plantilla desde un archivo. 

1. Seleccione **Cargar archivo**.

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-upload-file.png" alt-text="Captura de pantalla en la que se muestra el comando para cargar un archivo de plantilla":::

1. Busque la nueva plantilla que ha editado, selecciónela y después seleccione **Abrir**. La plantilla se carga en la ventana de edición. Seleccione **Guardar**. 

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-uploaded-file.png" alt-text="Captura de pantalla en la que se muestra la carga de la plantilla":::

1. Rellene los campos siguientes en la página de implementación personalizada.

   **Suscripción**: seleccione la suscripción que vaya a usar.

   **Grupo de recursos**: cree un grupo de recursos en una nueva ubicación. Si ya tiene uno configurado, puede seleccionarlo en lugar de crear otro.

   **Región**: si ha seleccionado un grupo de recursos existente, la región se rellena automáticamente para coincidir con la ubicación de ese grupo de recursos. Si ha creado un grupo de recursos, esta será su ubicación.

   **Cadena de conexión**: rellene la cadena de conexión del centro.

   **Nombre del centro**: asigne un nombre al nuevo centro de la nueva región.

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-custom-deployment-create.png" alt-text="Captura de pantalla que muestra la página de implementación personalizada":::

1. Seleccione el botón **Revisar y crear**.

1. Seleccione el botón **Crear**. El portal valida la plantilla e implementa el centro clonado. Si tiene datos de configuración de enrutamiento, se incluirán en el nuevo centro, pero apuntarán a los recursos de la ubicación anterior.

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-custom-deployment-final.png" alt-text="Captura de pantalla que muestra la última página de implementación personalizada":::

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Administración de los dispositivos registrados en el centro de IoT Hub

Ahora que tiene el clon listo y en funcionamiento, debe copiar todos los dispositivos del centro original en el clon. 

Hay varias formas de conseguirlo. Puede que haya usado [Service Device Provisioning (DPS)](../iot-dps/about-iot-dps.md) para aprovisionar los dispositivos o que no lo haya hecho. Si lo ha hecho, el proceso no es complicado. Si no lo ha hecho, puede ser muy complicado. 

Si no ha usado DPS para aprovisionar los dispositivos, puede omitir la sección siguiente y comenzar con [Uso de Import/Export para trasladar los dispositivos al nuevo centro](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Uso de DPS para volver a aprovisionar los dispositivos en el nuevo centro

Para usar DPS con el fin de trasladar los dispositivos a la nueva ubicación, consulte [Reaprovisionamiento de dispositivos](../iot-dps/how-to-reprovision.md). Cuando haya terminado, puede ver los dispositivos en [Azure Portal](https://portal.azure.com) y comprobar que están en la nueva ubicación.

Vaya al nuevo centro mediante [Azure Portal](https://portal.azure.com). Seleccione el centro y después **Dispositivos IoT**. Verá los dispositivos que se han vuelto a aprovisionar en el centro clonado. También podrá ver las propiedades del centro clonado. 

Si ha implementado el enrutamiento, haga pruebas y asegúrese de que los mensajes se enrutan correctamente a los recursos.

### <a name="committing-the-changes-after-using-dps"></a>Confirmación de los cambios después de usar DPS

El servicio DPS ha confirmado este cambio.

### <a name="rolling-back-the-changes-after-using-dps"></a>Reversión de los cambios después de usar DPS 

Si quiere revertir los cambios, vuelva a aprovisionar los dispositivos del nuevo centro al anterior.

Ya ha finalizado la migración del centro y sus dispositivos. Puede pasar a la sección [Limpieza](#clean-up).

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Uso de Import/Export para trasladar los dispositivos al nuevo centro

La aplicación tiene .NET Core como destino, por lo que puede ejecutarla en Windows o Linux. Puede descargar el ejemplo, recuperar las cadenas de conexión, establecer las marcas de los bits que quiere ejecutar y ejecutarla. Puede hacerlo sin necesidad de abrir el código.

### <a name="downloading-the-sample"></a>Descarga del ejemplo

1. Use los ejemplos de C# de IoT de esta página: [ejemplos de Azure IoT para C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Descargue el archivo .zip y descomprímalo en el equipo. 

1. El código pertinente está en ./iot-hub/Samples/service/ImportExportDevicesSample. No tiene que ver ni editar el código para ejecutar la aplicación.

1. Para ejecutar la aplicación, especifique tres cadenas de conexión y cinco opciones. Estos datos se pasan como argumentos de la línea de comandos o se usan variables de entorno, o bien se utiliza una combinación de ambos métodos. Vamos a pasar las opciones como argumentos de la línea de comandos y las cadenas de conexión como variables de entorno. 

   Esto se debe a que las cadenas de conexión son largas, poco manejables y no es probable que cambien, pero es posible que quiera cambiar las opciones y ejecutar la aplicación más de una vez. Para cambiar el valor de una variable de entorno, debe cerrar la ventana de comandos y Visual Studio o VS Code, lo que esté usando. 

### <a name="options"></a>Opciones

Estas son las cinco opciones que tiene que especificar cuando ejecute la aplicación. Las colocaremos en la línea de comandos dentro de un minuto.

*   **addDevices** (argumento 1): establézcalo en true si quiere agregar dispositivos virtuales que se generan automáticamente. Estos se agregan al centro de origen. Además, establezca **numToAdd** (argumento 2) para especificar el número de dispositivos que quiere agregar. El número máximo de dispositivos que puede registrar en un centro es un millón. La finalidad de esta opción es realizar pruebas; puede generar un número específico de dispositivos y, después, copiarlos en otro centro.

*   **copyDevices** (argumento 3): establézcalo en true para copiar los dispositivos de un centro a otro. 

*   **deleteSourceDevices** (argumento 4): establézcalo en true para eliminar todos los dispositivos registrados en el centro de origen. Se recomienda esperar hasta que se tenga claro que todos los dispositivos se han transferido antes de ejecutarlo. Una vez que se eliminan los dispositivos, no se pueden recuperar.

*   **deleteDestDevices** (argumento 5): establézcalo en true para eliminar todos los dispositivos registrados en el centro de destino (el clon). Puede que quiera hacer esto si quiere copiar los dispositivos más de una vez. 

El comando básico será *dotnet run*, que indica a .NET que compile el archivo csproj local y, después, lo ejecute. Agregará los argumentos de la línea de comandos al final antes de ejecutarla. 

La línea de comandos se parecerá a estos ejemplos:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Uso de las variables de entorno para las cadenas de conexión

1. Para ejecutar el ejemplo, necesita las cadenas de conexión a los centros de IoT antiguos y nuevos, y a una cuenta de almacenamiento que puede usar para los archivos de trabajo temporales. Estos valores se almacenarán en variables de entorno.

1. Para obtener los valores de las cadenas de conexión, inicie sesión en [Azure Portal](https://portal.azure.com). 

1. Coloque las cadenas de conexión en algún lugar donde pueda recuperarlas, como el Bloc de notas. Si copia lo siguiente, puede pegar las cadenas de conexión directamente donde vayan. No agregue espacios alrededor del signo igual o cambiará el nombre de la variable. Además, no necesita comillas dobles alrededor de las cadenas de conexión. Si coloca comillas alrededor de la cadena de conexión de la cuenta de almacenamiento, no funcionará.

   En Windows, esta es la forma de establecer las variables de entorno:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   En Linux, esta es la forma de definir las variables de entorno:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. En el caso de las cadenas de conexión del centro de IoT Hub, vaya a cada centro en el portal. Puede buscar el centro en **Recursos**. Si conoce el grupo de recursos, puede ir a **Grupos de recursos**, seleccionar el grupo y, luego, seleccionar el centro en la lista de recursos de ese grupo de recursos. 

1. Seleccione **Directivas de acceso compartido** en la configuración del centro, seleccione **iothubowner** y copie una de las cadenas de conexión. Haga lo mismo con el centro de destino. Agréguelos a los comandos SET adecuados.

1. En la cadena de conexión de la cuenta de almacenamiento, busque la cuenta de almacenamiento en **Recursos** o en su **grupo de recursos** y ábrala. 
   
1. En la sección Configuración, seleccione **Claves de acceso** y copie una de las cadenas de conexión. Coloque la cadena de conexión en el archivo de texto del comando SET adecuado. 

Ahora tiene las variables de entorno en un archivo con los comandos SET y sabe cuáles son los argumentos de la línea de comandos. Vamos a ejecutar el ejemplo.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Ejecución de la aplicación de ejemplo y uso de los argumentos de la línea de comandos

1. Abra una ventana de símbolo del sistema. Seleccione Windows y escriba `command prompt` para que aparezca la ventana del símbolo del sistema.

1. Copie los comandos que establecen las variables de entorno, de uno en uno, péguelos en la ventana del símbolo del sistema y seleccione Entrar. Cuando haya terminado, escriba `SET` en la ventana del símbolo del sistema para ver las variables de entorno y sus valores. Una vez que los haya copiado en la ventana del símbolo del sistema, no tendrá que volver a copiarlos, a menos que abra una nueva ventana del símbolo del sistema.

1. En la ventana del símbolo del sistema, cambie los directorios hasta que esté en ./ImportExportDevicesSample (donde está el archivo ImportExportDevicesSample.csproj). Después, escriba lo siguiente e incluya los argumentos de la línea de comandos.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    El comando dotnet compila y ejecuta la aplicación. Dado que está pasando las opciones al ejecutar la aplicación, puede cambiar los valores de cada vez que ejecute la aplicación. Por ejemplo, puede que quiera ejecutarla una vez y crear dispositivos, volver a ejecutarla y copiar esos dispositivos en un nuevo centro, etc. También puede realizar todos los pasos en la misma ejecución, aunque se recomienda que no elimine ningún dispositivo hasta que tenga claro que ha terminado con la clonación. Este es un ejemplo que crea 1000 dispositivos y luego los copia en el otro centro.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Después de comprobar que los dispositivos se han copiado correctamente, puede quitarlos del centro de origen de la siguiente manera:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Ejecución de la aplicación de ejemplo mediante Visual Studio

1. Si quiere ejecutar la aplicación en Visual Studio, cambie el directorio actual a la carpeta en la que se encuentra el archivo IoTHubServiceSamples.sln. Después, ejecute este comando en la ventana del símbolo del sistema para abrir la solución en Visual Studio. Debe hacerlo en la misma ventana de comandos en la que ha establecido las variables de entorno para que se conozcan esas variables.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Haga clic con el botón derecho en el proyecto *ImportExportDevicesSample* y seleccione **Establecer como proyecto de inicio**.    
    
1. Establezca las variables en la parte superior de Program.cs en la carpeta ImportExportDevicesSample para las cinco opciones.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Seleccione F5 para ejecutar la aplicación. Una vez finalizada la ejecución, puede ver los resultados.

### <a name="view-the-results"></a>Visualización de los resultados 

Puede ver los dispositivos en [Azure Portal](https://portal.azure.com) y comprobar que están en la nueva ubicación.

1. Vaya al nuevo centro mediante [Azure Portal](https://portal.azure.com). Seleccione el centro y después **Dispositivos IoT**. Verá los dispositivos que acaba de copiar del centro antiguo en el centro clonado. También podrá ver las propiedades del centro clonado. 

1. Para comprobar si hay errores de importación o exportación, vaya a la cuenta de almacenamiento de Azure en [Azure Portal](https://portal.azure.com) y busque `ImportErrors.log` en el contenedor `devicefiles`. Si este archivo está vacío (el tamaño es 0), no se han producido errores. Si intenta importar el mismo dispositivo más de una vez, se rechaza el dispositivo la segunda vez y se agrega un mensaje de error al archivo de registro.

### <a name="committing-the-changes"></a>Confirmación de los cambios 

En este momento, ha copiado el centro en la nueva ubicación y ha migrado los dispositivos al nuevo clon. Ahora debe realizar cambios para que los dispositivos funcionen con el centro clonado.

Para confirmar los cambios, estos son los pasos que debe realizar: 

* Actualice cada dispositivo para cambiar el nombre de host de IoT Hub a fin de que apunte al nuevo centro. Debe hacerlo con el mismo método que ha usado la primera vez que aprovisionó el dispositivo.

* Cambie cualquier aplicación que tenga que haga referencia al centro antiguo para que apunte al nuevo.

* Cuando haya terminado, el nuevo centro debe estar en funcionamiento. El centro antiguo no debe tener ningún dispositivo activo y debe estar desconectado. 

### <a name="rolling-back-the-changes"></a>Reversión de los cambios

Si decide revertir los cambios, estos son los pasos que debe realizar:

* Actualice cada dispositivo para cambiar el nombre de host de IoT Hub a fin de que apunte al centro antiguo. Debe hacerlo con el mismo método que ha usado la primera vez que aprovisionó el dispositivo.

* Cambie cualquier aplicación que tenga que haga referencia al centro nuevo para que apunte al antiguo. Por ejemplo, si usa los análisis de Azure, es posible que tenga que volver a configurar la [entrada de Azure Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Elimine el nuevo centro. 

* Si tiene recursos de enrutamiento, la configuración del centro antiguo debe seguir apuntando a la configuración de enrutamiento correcta y debe funcionar con esos recursos después de reiniciar el centro.

### <a name="checking-the-results"></a>Comprobación de los resultados 

Para comprobar los resultados, cambie la solución de IoT para que apunte al centro en la nueva ubicación y ejecútela. En otras palabras, realice las mismas acciones con el nuevo centro que ha realizado con el centro anterior y asegúrese de que funcionan correctamente. 

Si ha implementado el enrutamiento, haga pruebas y asegúrese de que los mensajes se enrutan correctamente a los recursos.

## <a name="clean-up"></a>Limpieza

No limpie hasta que tenga claro que el nuevo centro está listo y en funcionamiento y que los dispositivos funcionan correctamente. Asegúrese también de probar el enrutamiento si usa esa característica. Cuando tenga todo listo, limpie los recursos antiguos con estos pasos:

* Si todavía no lo ha hecho, elimine el centro antiguo. De esta forma, se quitan todos los dispositivos activos del centro.

* Si tiene recursos de enrutamiento que ha trasladado a la nueva ubicación, puede eliminar los recursos de enrutamiento antiguos.

## <a name="next-steps"></a>Pasos siguientes

Ha clonado un centro de IoT Hub en un nuevo centro de una nueva región, además de los dispositivos. Para obtener más información sobre cómo realizar operaciones masivas en el registro de identidades de una instancia de IoT Hub, consulte [Importación y exportación de identidades de dispositivo de IoT Hub de forma masiva](iot-hub-bulk-identity-mgmt.md).

Para obtener más información sobre IoT Hub y el desarrollo del centro, consulte los siguientes artículos.

* [Guía del desarrollador de IoT Hub](iot-hub-devguide.md)

* [Tutorial de enrutamiento de IoT Hub](tutorial-routing.md)

* [Información general sobre la administración de dispositivos de IoT Hub](iot-hub-device-management-overview.md)

* Si quiere implementar la aplicación de ejemplo, consulte [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/index).