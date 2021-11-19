---
title: 'Inicio rápido: Exportación de datos desde Azure IoT Central'
description: 'Inicio rápido: Aprenda a usar la característica de exportación de datos de IoT Central para la integración con otros servicios en la nube.'
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 0728eed5a1deeee20554998512edd70f5e983491
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492318"
---
# <a name="quickstart-export-data-from-an-iot-central-application"></a>Inicio rápido: Exportación de datos desde una aplicación de IoT Central

En este inicio rápido, se muestra cómo exportar continuamente datos desde la aplicación de Azure IoT Central a otro servicio en la nube. Para realizar la configuración rápidamente, en este inicio rápido se usa [Azure Data Explorer](/azure/data-explorer/data-explorer-overview), un servicio de análisis de datos totalmente administrado para el análisis en tiempo real. Azure Data Explorer permite almacenar, consultar y procesar los datos de telemetría desde dispositivos como la aplicación para smartphones **IoT Plug and Play**.

En esta guía de inicio rápido:

- Use la característica de exportación de datos de IoT Central para exportar los datos de telemetría enviados por la aplicación de smartphone a una base de datos de Azure Data Explorer.
- Use Azure Data Explorer para ejecutar consultas en los datos de telemetría.

## <a name="prerequisites"></a>Prerrequisitos

- Antes de empezar, debe completar el primer [Inicio rápido: Creación de una aplicación de Azure IoT Central y uso de un smartphone para enviar datos de telemetría](./quick-deploy-iot-central.md). El segundo inicio rápido, [Configuración de reglas y acciones para el dispositivo en Azure IoT Central](quick-configure-rules.md), es opcional.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="install-azure-services"></a>Instalación de los servicios de Azure

Para poder exportar datos desde la aplicación IoT Central, necesita un clúster de Azure Data Explorer y una base de datos. En este inicio rápido, se utiliza el entorno Bash en [Azure Cloud Shell](https://shell.azure.com) para crearlos y configurarlos.

Ejecute el siguiente script en Azure Cloud Shell. Antes de ejecutar el script reemplace el valor `clustername` por un nombre único para el clúster:

> [!IMPORTANT]
> El script tarda al menos 10 minutos en ejecutarse.

```azurecli
clustername="<A unique name for your cluster>"
databasename="phonedata"
location="eastus"
resourcegroup="IoTCentralExportData"

az extension add -n kusto

# Create a resource group for the Azure Data Explorer cluster
az group create --location $location \
    --name $resourcegroup

# Create the Azure Data Explorer cluster
# This command takes at least 10 minutes to run
az kusto cluster create --cluster-name $clustername \
    --sku name="Standard_D11_v2"  tier="Standard" \
    --enable-streaming-ingest=true \
    --enable-auto-stop=true \
    --resource-group $resourcegroup --location $location

# Crete a database in the cluster
az kusto database create --cluster-name $clustername \
    --database-name $databasename \
    --read-write-database location=$location soft-delete-period=P365D hot-cache-period=P31D \
    --resource-group $resourcegroup

# Create a service principal to use when authenticating from IoT Central
SP_JSON=$(az ad sp create-for-rbac --skip-assignment --name spforiotcentral)

az kusto database-principal-assignment create --cluster-name $clustername \
                                              --database-name $databasename \
                                              --principal-id $(jq -r .appId <<< $SP_JSON) \
                                              --principal-assignment-name spforiotcentral \
                                              --resource-group $resourcegroup \
                                              --principal-type App \
                                              --role Admin

echo "Azure Data Explorer URL: $(az kusto cluster show --name $clustername --resource-group $resourcegroup --query uri -o tsv)"
echo "Client ID: $(jq -r .appId <<< $SP_JSON)"
echo "Tenant ID: $(jq -r .tenant <<< $SP_JSON)"
echo "Client secret: $(jq -r .password <<< $SP_JSON)" 
```

Anote la **dirección URL de Azure Data Explorer** y los valores de **Id. de cliente**, **Id. del inquilino** y **Secreto de cliente**. Estos valores se usarán más adelante en este inicio rápido.

## <a name="configure-the-database"></a>Configuración de la base de datos

Para agregar una tabla a la base de datos para almacenar los datos del acelerómetro desde la aplicación para smartphones **IoT Plug and Play**:

1. Use la **dirección URL de Azure Data Explorer** de la sección anterior para ir a su entorno de Azure Data Explorer.

1. Expanda el nodo del clúster y seleccione la base de datos **phonedata**. El tamaño de la ventana de consulta cambia a `Scope:yourclustername.eastus/phonedata`.

1. Pegue el siguiente script de Kusto en el editor de consultas y seleccione **Ejecutar**:

    ```kusto
    .create table acceleration (
      EnqueuedTime:datetime,
      Device:string,
      X:real,
      Y:real,
      Z:real
    );
    ```

    El resultado es similar al de la siguiente captura de pantalla:

    :::image type="content" source="media/quick-export-data/azure-data-explorer-create-table.png" alt-text="Captura de pantalla que muestra los resultados de crear la tabla en Azure Data Explorer.":::

1. En Azure Data Explorer, abra una nueva pestaña y pegue el siguiente script de Kusto. El script habilita la entrada de streaming para la tabla de **aceleración**:

    ```kusto
    .alter table acceleration policy streamingingestion enable;
    ```

Mantenga la página de Azure Data Explorer abierta en el explorador. Después de configurar la exportación de datos en la aplicación de IoT Central, ejecutará una consulta para ver la telemetría del acelerómetro almacenada en la tabla de **aceleración**.

## <a name="configure-data-export"></a>Configuración de la exportación de datos

Para configurar el destino de la exportación de datos desde IoT Central:

1. Vaya a la página **Exportación de datos** de la aplicación de IoT Central.
1. Seleccione la pestaña **Destinos** y, a continuación, **Agregar un destino**.
1. Escriba *Azure Data Explorer* como nombre de destino. Seleccione **Azure Data Explorer** como tipo de destino.
1. En **URL del clúster**, escriba la **dirección URL de Azure Data Explorer** que anotó anteriormente.
1. En **Nombre de base de datos**, escriba *phonedata*.
1. En **Nombre de tabla**, escriba *acceleration*.
1. En **Id. de cliente**, escriba el **identificador de cliente** que anotó anteriormente.
1. En **Id. de inquilino**, escriba el **identificador de inquilino** que anotó anteriormente.
1. En **Secreto de cliente**, escriba el **secreto de cliente** que anotó anteriormente.
1. Seleccione **Guardar**.

Para configurar la exportación de datos:

1. En la página **Exportación de datos**, seleccione la pestaña **Exportaciones** y, a continuación, **Agregar una exportación**.
1. Escriba *Acelerómetro de teléfono* como nombre de exportación.
1. Seleccione **Telemetría** como el tipo de datos que se van a exportar.
1. Use la información de la tabla siguiente para agregar dos filtros:

    | Name | Operador | Valor |
    | ---- | -------- | ----- |
    | Plantilla de dispositivo | Equals | IoT Plug and Play para dispositivos móviles |
    | Sensores/aceleración/X | Exists | N/D |

    Asegúrese de que está establecida la opción para exportar los datos si se dan todas las condiciones.

1. Agregue **Azure Data Explorer** como destino.
1. Agregue una transformación de datos al destino. Agregue la siguiente consulta en el campo **2. Build transformation query** (Compilar consulta de transformación) en la página **Data transformation page** (Página de transformación de datos):

    ```json
    import "iotc" as iotc;
    {
        Device: .device.id,
        EnqueuedTime: .enqueuedTime,
        X: .telemetry | iotc::find(.name == "accelerometer").value.x,
        Y: .telemetry | iotc::find(.name == "accelerometer").value.y,
        Z: .telemetry | iotc::find(.name == "accelerometer").value.z
    }
    ```

    :::image type="content" source="media/quick-export-data/data-transformation-query.png" alt-text="Captura de pantalla que muestra la consulta de transformación de datos para la exportación.":::

    Si desea ver cómo funciona la transformación y experimentar con la consulta, pegue el siguiente mensaje de telemetría de ejemplo en **1. Add your input message** (Agregar un mensaje de entrada):

    ```json
    {
      "messageProperties": {},
      "device": {
        "id": "8hltz8xa7n",
        "properties": {
          "reported": []
        },
        "approved": true,
        "types": [],
        "name": "8hltz8xa7n",
        "simulated": false,
        "provisioned": true,
        "modules": [],
        "templateId": "urn:modelDefinition:vlcd3zvzdm:y425jkkpqzeu",
        "templateName": "IoT Plug and Play mobile",
        "organizations": [],
        "cloudProperties": [],
        "blocked": false
      },
      "component": "sensors",
      "applicationId": "40a97c91-50cc-44f0-9f63-71386613facc",
      "messageSource": "telemetry",
      "telemetry": [
        {
          "id": "dtmi:azureiot:PhoneSensors:__accelerometer;1",
          "name": "accelerometer",
          "value": {
            "x": 0.09960123896598816,
            "y": 0.09541380405426025,
            "z": 9.907781600952148
          }
        }
      ],
      "enqueuedTime": "2021-11-12T10:01:30.588Z",
      "enrichments": {}
    }
    ```

1. Guarde la transformación. Luego, guarde la definición de exportación de datos.

Espere hasta que el estado de exportación muestre **Correcto**:

:::image type="content" source="media/quick-export-data/healthy-export.png" alt-text="Captura de pantalla que muestra una exportación de datos en ejecución con el estado correcto.":::

## <a name="query-exported-data"></a>Consulta de los datos exportados

En Azure Data Explorer, abra una nueva pestaña y pegue la siguiente consulta de Kusto y, después, seleccione **Ejecutar** para trazar la telemetría del acelerómetro:

```kusto
['acceleration'] 
    | project EnqueuedTime, Device, X, Y, Z
    | render timechart 
```

Es posible que tenga que esperar varios minutos para poder recopilar suficientes datos. Pruebe a mantener el teléfono en distintas orientaciones para ver el cambio en los valores de telemetría:

:::image type="content" source="media/quick-export-data/acceleration-plot.png" alt-text="Captura de pantalla de los resultados de la consulta para la telemetría del acelerómetro.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

Para quitar la instancia de Azure Data Explorer de la suscripción y evitar que se facturen innecesariamente, elimine el grupo de recursos **IoTCentralExportData** desde [Azure Portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) o ejecute el siguiente comando en Azure Cloud Shell:

```azurecli
az group delete --name IoTCentralExportData
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a exportar datos continuamente desde IoT Central a otro servicio de Azure.

Ahora que sabe cómo exportar los datos, el siguiente paso que se recomienda es:

> [!div class="nextstepaction"]
> [Cree y administre una plantilla de dispositivo](howto-set-up-template.md).
