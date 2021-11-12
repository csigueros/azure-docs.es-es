---
title: Ingesta de datos del centro de eventos en el Explorador de datos de Azure Synapse (versión preliminar)
description: Aprenda a ingerir (cargar) datos en el Explorador de datos de Azure Synapse desde el centro de eventos.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 624658dda4f78270e6e3da75920c2fe76e112fb6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478236"
---
# <a name="ingest-data-from-event-hub-into-azure-synapse-data-explorer"></a>Ingesta de datos del centro de eventos en el Explorador de datos de Azure Synapse

> [!div class="op_single_selector"]
> * [Portal](data-explorer-ingest-event-hub-portal.md)
> * [Un solo clic](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Plantilla de Azure Resource Manager](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

El Explorador de datos de Azure Synapse ofrece la ingesta (carga de datos) de Event Hubs, una plataforma de streaming de macrodatos y un servicio de ingesta de eventos. [Event Hubs](/azure/event-hubs/event-hubs-about) puede procesar millones de eventos por segundo prácticamente en tiempo real. En este artículo creará un centro de eventos, se conectará a él desde el Explorador de datos de Azure Synapse y verá el flujo de datos a través del sistema.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Creación de una tabla de destino a la que Event Hubs enviará datos
    1. En Synapse Studio, en el panel izquierdo, seleccione **Desarrollar**.
    1. En **KQL scripts** (Scripts de KQL), seleccione **&plus;** (Agregar un recurso nuevo) > **KQL script** (Script de KQL). En el panel derecho, puede asignar un nombre al script.
    1. En el menú **Conectarse a**, seleccione *contosodataexplorer*.
    1. En el menú **Use database** (Usar base de datos), seleccione *TestDatabase*.
    1. Pegue el siguiente comando y seleccione **Ejecutar** para crear la tabla.

        ```Kusto
        .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
        ```
    
        > [!TIP]
        > Compruebe que la tabla se creó correctamente. En el panel izquierdo, seleccione **Datos**, elija *contosodataexplorer* en el menú Más y seleccione **Actualizar**. En *contosodataexplorer*, expanda **Tablas** y asegúrese de que la tabla *TestTable* aparece en la lista.

    1. Copie el siguiente comando en la ventana y seleccione **Ejecutar** para asignar los datos JSON entrantes a los tipos de datos y los nombres de columna de la tabla (TestTable).
    
        ```Kusto
        .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp", "Properties": {"Path": "$.timeStamp"}},{"column":"Name", "Properties": {"Path":"$.name"}} ,{"column":"Metric", "Properties": {"Path":"$.metric"}}, {"column":"Source", "Properties": {"Path":"$.source"}}]'
        ```

- Se recomienda usar una identidad [administrada asignada por el usuario](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity) o una [identidad administrada asignada por el sistema](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) para la conexión de datos (opcional).
- [Una aplicación de ejemplo](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) que genera los datos y los envía a un centro de eventos. Descargue la aplicación de ejemplo en el sistema.
- [Visual Studio de 2019](https://visualstudio.microsoft.com/vs/) para ejecutar la aplicación de ejemplo.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Creación de un centro de eventos

Cree un centro de eventos mediante una plantilla de Azure Resource Manager en Azure Portal.

1. Con el fin de crear un centro de eventos, use el botón siguiente para iniciar la implementación. Haga clic con el botón derecho y seleccione **Abrir en una ventana nueva** para seguir el resto de los pasos de este artículo.

    [![Botón Implementación en Azure](../media/ingest-data-event-hub/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.eventhub%2Fevent-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    El botón **Deploy to Azure** (Implementar en Azure) le lleva a Azure Portal.

    ![Creación de un formulario de centro de eventos](../media/ingest-data-event-hub/deploy-to-azure.png)

1. Seleccione la suscripción en la que desea crear el centro de eventos y cree un grupo de recursos denominado *test-hub-rg*.

    ![Crear un grupo de recursos](../media/ingest-data-event-hub/create-resource-group.png)

1. Rellene el formulario con la siguiente información.

    Use los valores predeterminados para cualquier configuración que no aparezca en la tabla siguiente.

    **Configuración** | **Valor sugerido** | **Descripción del campo**
    |---|---|---|
    | Subscription | Su suscripción | Seleccione la suscripción de Azure que quiere usar para el centro de eventos.|
    | Resource group | *test-hub-rg* | Cree un nuevo grupo de recursos. |
    | Location | *Oeste de EE. UU.* | Seleccione *Oeste de EE. UU.* para este artículo. En un sistema de producción, seleccione la región que mejor se adapte a sus necesidades. Cree el espacio de nombres del centro de eventos en la misma ubicación que el clúster del Explorador de datos de Azure Synapse para mejorar el rendimiento (más importante para espacios de nombres de centro de eventos con alto rendimiento).
    | Nombre del espacio de nombres | Nombre único del espacio de nombres | Elija un nombre único que identifique el espacio de nombres. Por ejemplo, *mytestnamespace*. El nombre de dominio *servicebus.windows.net* se anexa al nombre que proporcione. El nombre solo puede contener letras, números y guiones. El nombre debe comenzar y terminar con una letra o un número. El valor debe tener entre 6 y 50 caracteres.
    | Nombre del centro de eventos | *test-hub* | El centro de eventos se encuentra bajo el espacio de nombres, que proporciona un contenedor de ámbito único. El nombre del centro de eventos tiene que ser único dentro del espacio de nombres. |
    | Nombre del grupo de consumidores | *test-group* | Los grupos de consumidores permiten consumir varias aplicaciones y que cada una tenga una vista independiente del flujo de eventos. |
    | | |

1. Seleccione **Revisar + crear**.

1. Revise el **resumen** de los recursos creados. Seleccione **Crear**, opción que permite confirmar que está creando recursos en su suscripción.

    :::image type="content" source="../media/ingest-data-event-hub/review-create.png" alt-text="Captura de pantalla de Azure Portal para revisar y crear un espacio de nombres del centro de eventos, un centro de eventos y un grupo de consumidores.":::

1. Seleccione **Notificaciones** en la barra de herramientas para supervisar el proceso de aprovisionamiento. La implementación puede tardar varios minutos en realizarse correctamente, pero puede continuar con el siguiente paso ahora.

    ![Icono de notificaciones](../media/ingest-data-event-hub/notifications.png)

### <a name="authentication-considerations"></a>Consideraciones de autenticación

En función del tipo de identidad que use para autenticarse con el centro de eventos, puede que necesite algunas configuraciones adicionales.

- Si se autentica con el centro de eventos mediante una identidad administrada asignada por el usuario, vaya al centro de eventos > **Redes** y, en **Permitir acceso desde**, seleccione **Todas las redes** y guarde los cambios.

    :::image type="content" source="../media/ingest-data-event-hub/configure-event-hub-all-networks.png" alt-text="Captura de pantalla de la página de redes del centro de eventos, en la que se muestra que se ha seleccionado la opción para permitir el acceso a todas las redes.":::

- Si se autentica con el centro de eventos mediante una identidad administrada asignada por el sistema, vaya al centro de eventos > **Redes**. Allí, permita el acceso desde todas las redes o bien, en **Permitir acceso desde**, elija **Redes seleccionadas**, seleccione Sí en **¿Quiere permitir que los servicios de confianza de Microsoft puedan omitir este firewall?** y guarde los cambios.

    :::image type="content" source="../media/ingest-data-event-hub/configure-event-hub-trusted-services.png" alt-text="Captura de pantalla de la página de redes del centro de eventos, en la que se muestra que se ha seleccionado la opción para permitir el acceso a servicios de confianza.":::

## <a name="connect-to-the-event-hub"></a>Conexión a Event Hubs

Ahora se conectará al centro de eventos desde el grupo del Explorador de datos. Cuando se efectúa esta conexión, los datos que fluyen al centro de eventos se transmiten a la tabla de prueba que creó anteriormente en este artículo.

1. Seleccione **Notificaciones** en la barra de herramientas para comprobar que la implementación del centro de eventos se ha realizado correctamente.

1. En el grupo del Explorador de datos que creó, seleccione **Bases de datos** > **TestDatabase**.

    :::image type="content" source="../media/ingest-data-event-hub/select-test-database.png" alt-text="Seleccionar la base de datos de prueba.":::

1. Seleccione **Conexiones de datos** y **Agregar la conexión de datos**.

    :::image type="content" source="../media/ingest-data-event-hub/event-hub-connection.png" alt-text="Seleccione Ingesta de datos y Agregar la conexión de datos.":::

### <a name="create-a-data-connection-preview"></a>Creación de una conexión de datos (versión preliminar)

Rellene el formulario con la siguiente información y, después, seleccione **Crear**.

:::image type="content" source="../media/ingest-data-event-hub/data-connection-pane.png" alt-text="Panel de conexión de datos del centro de eventos: Explorador de datos de Azure Synapse.":::

**Configuración** | **Valor sugerido** | **Descripción del campo**
|---|---|---|
| Nombre de la conexión de datos | *test-hub-connection* | Nombre de la conexión que quiere crear en el Explorador de datos de Azure Synapse.|
| Suscripción |      | Identificador de la suscripción donde se encuentra el recurso del centro de eventos. Este campo se rellena automáticamente. |
| Espacio de nombres del centro de eventos | Nombre único del espacio de nombres | Nombre elegido anteriormente que identifica el espacio de nombres. |
| Centro de eventos | *test-hub* | El centro de eventos que ha creado. |
| Grupo de consumidores | *test-group* | Grupo de consumidores definido en el centro de eventos que creó. |
| Propiedades del sistema de eventos | Seleccione las propiedades pertinentes. | [Propiedades del sistema del centro de eventos](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). Si hay varios registros por cada mensaje de evento, las propiedades del sistema se agregarán al primero de ellos. Cuando agregue las propiedades del sistema, [cree](/azure/data-explorer/kusto/management/create-table-command?context=/azure/synapse-analytics/context/context) o [actualice](/azure/data-explorer/kusto/management/alter-table-command?context=/azure/synapse-analytics/context/context) el esquema de tabla y la [asignación](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) para incluir las propiedades seleccionadas. |
| Compresión | *None* | Tipo de compresión de la carga de mensajes del centro de eventos. Tipos de compresión admitidos: *Ninguno, Gzip*.|
| Identidad administrada | Asignada por el sistema | La identidad administrada que usa el clúster de Data Explorer para el acceso de lectura del centro de eventos.<br /><br />**Nota**:<br />Cuando se crea la conexión de datos:<br/>\- Las *identidades asignadas por el sistema* se crean automáticamente si no existen.<br />\- A la identidad administrada se le asigna automáticamente el rol *Receptor de datos de Azure Event Hubs* y se agrega al clúster de Data Explorer. Se recomienda comprobar que el rol se asignó y que la identidad se agregó al clúster. |

#### <a name="target-table"></a>Tabla de destino

Hay dos opciones para el enrutamiento de los datos ingeridos: *estático* y *dinámico*.
En este artículo, usará el enrutamiento estático, en el que se especifican el nombre de la tabla, el formato de los datos y la asignación como valores predeterminados. Si el mensaje del centro de eventos incluye información de enrutamiento de datos, esta información de enrutamiento invalidará la configuración predeterminada.

1. Rellene la siguiente configuración de enrutamiento:

    :::image type="content" source="../media/ingest-data-event-hub/default-routing-settings.png" alt-text="Configuración de enrutamiento predeterminada para la ingesta de datos en el centro de eventos: Explorador de datos de Azure Synapse.":::

    |**Configuración** | **Valor sugerido** | **Descripción del campo**
    |---|---|---|
    | Nombre de la tabla | *TestTable* | La tabla que creó en **TestDatabase**. |
    | Formato de datos | *JSON* | Los formatos admitidos son Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO y W3CLOG. |
    | Asignación | *TestMapping* | [Asignación](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) que creó en **TestDatabase**, que asigna los datos entrantes a los nombres de columnas y tipos de datos de **TestTable**. Necesario para JSON, MULTILINE JSON y AVRO y opcional para otros formatos.|

    > [!NOTE]
    >
    > * No es necesario especificar todos los **valores de configuración de enrutamiento predeterminados**. También se aceptan configuraciones parciales.
    > * Solamente se ingieren los eventos en cola después de crear la conexión de datos.

1. Seleccione **Crear**.

### <a name="event-system-properties-mapping"></a>Asignación de propiedades del sistema de eventos

[!INCLUDE [event-hub-system-mapping](../includes/data-explorer-event-hub-system-mapping.md)]

Si ha seleccionado **Propiedades del sistema de eventos** en la sección **Origen de datos** de la tabla, debe incluir las [propiedades del sistema](data-explorer-ingest-event-hub-overview.md#system-properties) en el esquema de la tabla y la asignación.

## <a name="copy-the-connection-string"></a>Copiar la cadena de conexión

Al ejecutar la [aplicación de ejemplo](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) que se enumera en los requisitos previos, necesitará la cadena de conexión para el espacio de nombres del centro de eventos.

1. En el espacio de nombres del centro de eventos que creó, seleccione **Directivas de acceso compartido** y **RootManageSharedAccessKey**.

    ![Directivas de acceso compartido](../media/ingest-data-event-hub/shared-access-policies.png)

1. Copie **Cadena de conexión: clave principal**. Péguelo en la sección siguiente.

    ![Cadena de conexión](../media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Generación de datos de ejemplo

Use la [aplicación de ejemplo](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) que descargó para generar datos.

1. Abra la aplicación de ejemplo en Visual Studio.
1. En el archivo *program.cs*, actualice la constante `eventHubName` al nombre del centro de eventos y actualice la constante `connectionString` a la cadena de conexión que copió desde el espacio de nombres del centro de eventos.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Compile y ejecute la aplicación. La aplicación envía mensajes al centro de eventos e imprime su estado cada 10 segundos.
1. Cuando la aplicación haya enviado algunos mensajes, vaya al paso siguiente: revisión del flujo de datos en la tabla de prueba y el centro de eventos.

## <a name="review-the-data-flow"></a>Revisión del flujo de datos

Con la aplicación de generación de datos, ahora puede ver el flujo de datos desde el centro de eventos a la tabla del clúster.

1. En Azure Portal, en el centro de eventos, verá el pico de actividad cuando la aplicación se está ejecutando.

    ![Gráfico del centro de eventos.](../media/ingest-data-event-hub/event-hub-graph.png)

1. Ejecute la siguiente consulta en la base de datos de prueba para comprobar cuántos mensajes se han enviado a la base de datos hasta el momento.

    ```Kusto
    TestTable
    | count
    ```

1. Para ver el contenido de los mensajes, ejecute la siguiente consulta:

    ```Kusto
    TestTable
    ```

    El conjunto de resultados debe tener un aspecto similar al de la siguiente imagen:

    ![Conjunto de resultados de mensajes.](../media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    >
    > * El Explorador de datos de Azure Synapse tiene una directiva de agregación (procesamiento por lotes) para la ingesta de datos diseñada para optimizar dicho proceso. La directiva de procesamiento por lotes predeterminada está configurada para sellar un lote tan pronto como se cumpla una de las siguientes condiciones en el lote: un tiempo de retraso máximo de 5 minutos, un tamaño total de 1 G o 1000 blobs. Por lo tanto, puede experimentar una latencia. Para obtener más información, consulte la [directiva de procesamiento por lotes](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context).
    > * La ingesta de datos del centro de eventos incluye el tiempo de respuesta del centro de eventos de 10 segundos o 1 MB.
    > * Para reducir el retraso en el tiempo de respuesta, configure la tabla para que admita el streaming. Consulte la [directiva de streaming](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context).

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no piensa volver a usar el centro de eventos, limpie **test-hub-rg** para evitar incurrir en costos.

1. En Azure Portal, seleccione **Grupos de recursos** en el extremo izquierdo y luego seleccione el grupo de recursos que creó.

    Si el menú izquierdo está contraído, seleccione el ![Botón Expandir.](../media/ingest-data-event-hub/expand.png) para expandirlo.

   ![Selección del grupo de recursos que se eliminará.](../media/ingest-data-event-hub/delete-resources-select.png)

1. En **test-resource-group**, seleccione **Eliminar grupo de recursos**.

1. En la nueva ventana, escriba el nombre del grupo de recursos que quiere eliminar (*test-hub-rg*) y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

- [Análisis con el Explorador de datos](../../get-started-analyze-data-explorer.md)
- [Supervisión de grupos del Explorador de datos](../data-explorer-monitor-pools.md)
