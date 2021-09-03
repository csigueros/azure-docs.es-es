---
title: Uso de Azure Portal para configurar la carga de archivos | Microsoft Docs
description: Describe cómo usar Azure Portal para configurar el centro de IoT Hub con el fin de habilitar las cargas de archivo desde dispositivos conectados. Incluye información sobre cómo configurar la cuenta de Azure Storage.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: robinsh
ms.openlocfilehash: bf8bf9d1b472ff1986596f6cae7e1f7b415d3bc6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729838"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configuración de cargas de archivos de IoT Hub mediante Azure Portal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

En este artículo se muestra cómo configurar cargas de archivos en el centro de IoT mediante Azure Portal. 

Para usar la [funcionalidad de carga de archivos en IoT Hub](iot-hub-devguide-file-upload.md), primero debe asociar una cuenta de Azure Storage y un contenedor de blobs al centro de IoT. IoT Hub genera automáticamente identificadores URI de SAS con permisos de escritura en este contenedor de blobs para los dispositivos que se utilizarán cuando se carguen archivos. Además de la cuenta de almacenamiento y el contenedor de blobs, puede establecer el período de vida del URI de SAS y el tipo de autenticación que IoT Hub usa con Azure Storage. También puede configurar opciones para las notificaciones de carga de archivos opcionales que IoT Hub puede entregar a los servicios de back-end.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en tan solo unos minutos.

* Un centro de Azure IoT. Si actualmente no tiene ningún centro de IoT, consulte [Creación de una instancia de IoT Hub mediante Azure Portal](iot-hub-create-through-portal.md).

## <a name="configure-your-iot-hub"></a>Configuración del centro de IoT

1. En [Azure Portal](https://portal.azure.com), navegue a su centro de IoT y seleccione **Carga de archivos** para mostrar las propiedades de carga de archivos. A continuación, seleccione **Contenedor de Azure Storage** en **Configuración del contenedor de almacenamiento**.

    :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-settings.png" alt-text="Visualización de la configuración de carga de archivos de IoT Hub en el portal":::

1. Seleccione una cuenta de Azure Storage y un contenedor de blobs en su suscripción actual para asociarlos a su centro de IoT. Si es necesario, puede crear una cuenta de Azure Storage en el panel **Cuentas de almacenamiento** y un contenedor de blobs en el panel **Contenedores**. 

   :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-container-selection.png" alt-text="Visualización de contenedores de almacenamiento para la carga de archivos en el portal":::

1. Después de seleccionar una cuenta de Azure Storage y un contenedor de blobs, configure el resto de las propiedades de carga de archivos.    

    :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-selected-container.png" alt-text="Configuración de la carga de archivos de IoT Hub en el portal":::

    * **Receive notifications for uploaded files**(Recibir notificaciones para archivos cargados): habilite o deshabilite las notificaciones de carga de archivos mediante el botón de alternancia.

    * **SAS TTL**(TTL SAS): este valor es el periodo de vida de los URI de SAS que Azure IoT Hub devuelve al dispositivo. Se establece en una hora de forma predeterminada, pero se puede personalizar con otros valores mediante el control deslizante.

    * **File notification settings default TTL** (TTL predeterminado de configuración de notificación de archivos): período de vida de una notificación de carga de archivos antes de que expire. Se establece en un día de forma predeterminada, pero se puede personalizar con otros valores mediante el control deslizante.

    * **File notification maximum delivery count**(Número máximo de entregas de notificaciones de archivo): el número de veces que Azure IoT Hub tratará de entregar una notificación de carga de archivos. Se establece en 10 días de forma predeterminada, pero se puede personalizar con otros valores mediante el control deslizante.

    * **Tipo de autenticación**: de forma predeterminada, Azure IoT Hub usa la autenticación basada en claves para la conexión y autorización con Azure Storage. También puede configurar identidades administradas asignadas por el usuario o asignadas por el sistema para autenticar Azure IoT Hub con Azure Storage. Las identidades administradas proporcionan a los servicios de Azure una identidad administrada automáticamente en Azure AD de manera segura. Para obtener información sobre cómo configurar identidades administradas, consulte [Compatibilidad de IoT Hub para identidades administradas](./iot-hub-managed-identity.md). Después de configurar una o varias identidades administradas en su cuenta de Azure Storage e IoT Hub, puede seleccionar una para la autenticación con Azure Storage con los botones **Asignado por el sistema** o **Asignado por el usuario**.

        > [!NOTE]
        > La configuración del tipo de autenticación establece cómo se autentica el centro de IoT con la cuenta de Azure Storage. Los dispositivos siempre se autentican con Azure Storage mediante el URI de SAS que obtienen del centro de IoT. 

1. Haga clic en **Guardar** para guardar la configuración. Asegúrese de comprobar la confirmación de que finalización correcta. Algunas selecciones, como la de **Tipo de autenticación**, solo se validan después de guardar la configuración. 

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a la carga de archivos desde un dispositivo](iot-hub-devguide-file-upload.md)
* [Compatibilidad de IoT Hub con identidades administradas](./iot-hub-managed-identity.md)
* [Guías paso a paso de carga de archivos](./iot-hub-csharp-csharp-file-upload.md)
