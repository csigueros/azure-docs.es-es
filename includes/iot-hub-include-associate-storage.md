---
title: Archivo de inclusión
description: archivo de inclusión
services: iot-hub
author: JimacoMS3
ms.service: iot-hub
ms.topic: include
ms.date: 01/22/2019
ms.author: v-jbrannian
ms.custom: include file
ms.openlocfilehash: de266fe50b163820b3dd50545546d099431daefa
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114729934"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Asociación de una cuenta de Azure Storage a IoT Hub

Para cargar archivos desde un dispositivo, debe tener una cuenta de Azure Storage y un contenedor de Azure Blob Storage asociado al centro de IoT. Una vez que asocie la cuenta de almacenamiento y el contenedor con el centro de IoT, este puede proporcionar los elementos de un URI de SAS cuando lo solicite un dispositivo. A continuación, el dispositivo puede usar estos elementos para construir el URI de SAS que usa para autenticarse con Azure Storage y cargar archivos en el contenedor de blobs.

Para asociar una cuenta de Azure Storage con el centro de IoT:

1. En **Mensajería**, seleccione **Carga de archivos** en el panel izquierdo del centro de IoT.

    :::image type="content" source="./media/iot-hub-include-associate-storage/select-storage.png" alt-text="Seleccione la configuración de carga de archivos en el portal.":::

1. En el panel **Carga de archivos**, seleccione **Contenedor de Azure Storage**. En este artículo, se recomienda que la cuenta de almacenamiento e IoT Hub se encuentren en la misma región. 
    * Si ya tiene una cuenta de almacenamiento que desea usar, selecciónela de la lista. 

    * Para crear una nueva cuenta de almacenamiento, seleccione **+ Cuenta de almacenamiento**. Proporcione un nombre a la cuenta de almacenamiento y asegúrese de que **Ubicación** esté establecida en la misma región que el centro de IoT y, a continuación, seleccione **Aceptar**. La nueva cuenta se crea en el mismo grupo de recursos que el centro de IoT. Cuando se complete la implementación, seleccione la cuenta de almacenamiento de la lista. 

    Después de seleccionar la cuenta de almacenamiento, se abre el panel **Contenedores**. 

1. En el panel **Contenedores**, seleccione el contenedor de blobs.
    * Si ya tiene un contenedor de blobs que desea usar, selecciónelo en la lista y haga clic en **Seleccionar**. 
    
    * Para crear un contenedor de blobs, seleccione **+ Contenedor**. Proporcione un nombre para el nuevo contenedor. Para los fines de este artículo, puede dejar todos los demás campos en su valor predeterminado. Seleccione **Crear**. Cuando finalice la implementación, seleccione el contenedor de la lista y haga clic en **Seleccionar**.

1. De nuevo en el panel **Carga de archivos**, asegúrese de que las notificaciones de archivos estén establecidas en **Activadas**. Puede dejar el resto de la configuración en sus valores predeterminados. Seleccione **Guardar** y espere a que se complete la configuración antes de pasar a la siguiente sección. 

    :::image type="content" source="./media/iot-hub-include-associate-storage/file-upload-settings-small.png" alt-text="Confirme la configuración de la carga de archivos en el portal.":::

Para obtener instrucciones detalladas sobre cómo crear una cuenta de Azure Storage, consulte [Crear una cuenta de almacenamiento](../articles/storage/common/storage-account-create.md). Para obtener instrucciones detalladas sobre cómo asociar una cuenta de almacenamiento y un contenedor de blobs a un centro de IoT, consulte [Configuración de cargas de archivos mediante Azure Portal](../articles/iot-hub/iot-hub-configure-file-upload.md).
