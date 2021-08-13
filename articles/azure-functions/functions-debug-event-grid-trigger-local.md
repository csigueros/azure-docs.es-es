---
title: Depuración local de funciones de Azure desencadenadas por Event Grid
description: Obtenga información sobre cómo depurar localmente instancias de Azure Functions desencadenadas por un evento de Event Grid.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 240b721d71ef53ad6f43f076f1d5db0e29b556a4
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970926"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Depuración local de funciones de Azure desencadenadas por Event Grid

En este artículo se muestra cómo depurar una función local que controla un evento de Azure Event Grid generado por una cuenta de almacenamiento. 

## <a name="prerequisites"></a>Requisitos previos

- Cree o use una aplicación de función existente.
- Cree o use una cuenta de almacenamiento existente. La suscripción de notificación de Event Grid se puede establecer en cuentas de Azure Storage para `BlobStorage`, `StorageV2` o [Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).
- Descargue [ngrok](https://ngrok.com/) para permitir que Azure llame a su función local.

## <a name="create-a-new-function"></a>Creación de una función

Abra la aplicación de función en Visual Studio, haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones y, a continuación, haga clic en **Agregar > Nueva función de Azure**.

En la ventana *Nueva función de Azure*, seleccione **Desencadenador de la cuadrícula de eventos** y haga clic en **Aceptar**.

![Crear una nueva función](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Una vez creada la función, abra el archivo de código y copie la dirección URL comentada en la parte superior del archivo. Esta ubicación se utiliza al configurar el desencadenador de Event Grid.

![Ubicación de copia](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

A continuación, establezca un punto de interrupción en la línea que comienza por `log.LogInformation`.

![Establecimiento de punto de interrupción](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


A continuación, **presione F5** para iniciar una sesión de depuración.

[!INCLUDE [functions-event-grid-local-dev](../../includes/functions-event-grid-local-dev.md)]

## <a name="debug-the-function"></a>Depurar la función

Una vez que Event Grid reconoce que se ha cargado un nuevo archivo en el contenedor de almacenamiento, se alcanza el punto de interrupción en la función local.

![Iniciar ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Para limpiar los recursos creados en este artículo, elimine el contenedor **test** de su cuenta de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

- [Automatización del cambio de tamaño de las imágenes cargadas mediante Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Desencadenador de Event Grid para Azure Functions](./functions-bindings-event-grid.md)