---
title: 'Event Hubs: Capturar eventos de streaming mediante Azure Portal'
description: En este artículo se describe cómo habilitar la captura de eventos que se transmiten en secuencias a través de Azure Event Hubs mediante Azure Portal.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: b603af17ca4e46b8004b7b2550cddd87164da157
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253642"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Habilitación de la captura de eventos que se transmiten por streaming mediante Azure Event Hubs

Azure [Event Hubs Capture][capture-overview] permite entregar automáticamente los datos de streaming de Event Hubs a la cuenta de [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) o [Azure Data Lake Storage Gen1 o Gen2](https://azure.microsoft.com/services/data-lake-store/) que prefiera.

Puede configurar la funcionalidad de captura en el momento de creación del centro de eventos mediante [Azure Portal](https://portal.azure.com). Puede capturar los datos para un contenedor de [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) o para una cuenta de [Azure Data Lake Storage Gen1 o Gen2](https://azure.microsoft.com/services/data-lake-store/).

Para más información, consulte [Introducción a Event Hubs Capture][capture-overview].

> [!IMPORTANT]
> La cuenta de almacenamiento de destino (Azure Storage o Azure Data Lake Storage) debe estar en la misma suscripción que el centro de eventos.

## <a name="capture-data-to-azure-storage"></a>Captura de datos para Azure Storage

Al crear un centro de eventos, puede habilitar Capture haciendo clic en el botón **On** de la pantalla del portal **Creación de un centro de eventos**. Después, para especificar una cuenta de almacenamiento y un contenedor, haga clic en **Azure Storage** en el cuadro **Capture Provider** (Proveedor de Capture). Dado que la captura de Event Hubs utiliza la autenticación de servicio a servicio con el almacenamiento, no es necesario especificar una cadena de conexión de almacenamiento. El selector de recursos selecciona automáticamente el identificador URI del recurso para la cuenta de almacenamiento. Si se usa Azure Resource Manager, es preciso suministrar explícitamente dicho identificador URI como una cadena.

La ventana de tiempo predeterminada es cinco minutos. El valor mínimo es 1 y el máximo 15. La ventana **Tamaño** tiene un intervalo de 10-500 MB.

![Período de tiempo para la captura][1]

> [!NOTE]
> Puede habilitar o deshabilitar el envío de archivos vacíos cuando no se producen eventos durante la ventana de Capture. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Captura de datos para Azure Data Lake Storage Gen2 

1. Siga los pasos del artículo [Crear una cuenta de almacenamiento](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) para crear una cuenta de Azure Storage. Establezca **Espacio de nombres jerárquico** en **Habilitado** en la pestaña **Opciones avanzadas** para convertirlo en una cuenta de Azure Data Lake Storage Gen2. La cuenta de Azure Storage debe encontrarse en la misma suscripción que el centro de eventos.
2. Al crear un centro de eventos, siga estos pasos: 

    1. Seleccione **Activar** para **Capture**. 
    2. Seleccione **Azure Storage** como proveedor de captura. La opción **Azure Data Lake Storage** que se ve para **Proveedor de captura** corresponde a la generación 1 de Azure Data Lake Storage. Para usar una generación 2 de Azure Data Lake Storage, seleccione **Azure Storage**.
    2. Seleccione el botón **Seleccionar contenedor**. 

        ![Habilitación de captura para Data Lake Storage Gen2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Seleccione la cuenta **Azure Data Lake Storage Gen2** en la lista. 

    ![Selección de Data Lake Storage Gen2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Seleccione el **contenedor** (sistema de archivos de Data Lake Storage Gen2).

    ![Selección del sistema de archivos del almacenamiento](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. En la página **Crear centro de eventos**, seleccione **Crear**. 

    ![Selección del botón Crear](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > El contenedor que se crea en una cuenta de Azure Data Lake Storage Gen2 con esta interfaz de usuario (IU) se muestra en **Sistemas de archivos** en **Explorador de Storage**. Del mismo modo, el sistema de archivos que se crea en una cuenta de Data Lake Storage Gen2 se muestra como un contenedor en esta interfaz de usuario. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Captura de datos para Azure Data Lake Storage Gen1 

Para capturar datos para Azure Data Lake Storage Gen1, cree una cuenta de Data Lake Storage Gen1 y un centro de eventos:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Creación de una cuenta de Azure Data Lake Storage Gen1 y carpetas

1. Cree una cuenta de Data Lake Storage con las instrucciones de [Introducción a Azure Data Lake Storage Gen1 con Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md).
2. Siga las instrucciones de la sección [Asignar permisos a Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) para crear una carpeta dentro de la cuenta de Data Lake Storage Gen1 en la que quiere capturar los datos de Event Hubs y asigne permisos a Event Hubs para que pueda escribir datos en la cuenta de Data Lake Storage Gen1.  


### <a name="create-an-event-hub"></a>Creación de un centro de eventos

1. El centro de eventos debe estar en la misma suscripción de Azure que la cuenta de Azure Data Lake Storage Gen1 que ha creado. Para crear el centro de eventos, haga clic en el botón **Activado** situado bajo **Capture** en la página del portal **Crear centro de eventos**. 
2. En la página del portal **Crear centro de eventos**, seleccione **Azure Data Lake Store** en el cuadro **Capture Provider** (Proveedor de Capture).
3. En **Seleccionar Store**, junto a la lista desplegable **Data Lake Store**, especifique la cuenta de Data Lake Storage Gen1 creada anteriormente y, en el campo **Ruta de acceso de Data Lake**, escriba la ruta de acceso a la carpeta de datos creada.

    ![Selección de la cuenta de Data Lake Storage][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Adición o configuración de Capture en un centro de eventos existente

Se puede configurar Capture en los centros de eventos existentes que se encuentran en los espacios de nombres de Event Hubs. Para habilitar Capture en un centro de eventos existente o para cambiar la configuración de Capture, haga clic en el espacio de nombres para cargar la pantalla de la introducción y, después, haga clic en el centro de eventos para el que desea habilitar o cambiar la configuración de Capture. Por último, haga clic en la opción **Capture** del lado izquierdo de la página abierta y después edite la configuración, tal como se muestra en las ilustraciones siguientes:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Configuración de Azure Blob Storage][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Configuración de Azure Data Lake Storage Gen2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Configurar Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre Event Hubs Capture en el artículo [Introducción a Event Hubs Capture][capture-overview].
- La Event Hubs Capture también se puede configurar a través de las plantillas de Azure Resource Manager. Para más información, consulte [Habilitar Capture mediante la plantilla de Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Aprenda a crear una suscripción de Azure Event Grid con un espacio de nombres de Event Hubs como origen](store-captured-data-data-warehouse.md)
- [Introducción al uso de Azure Portal por parte de Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
