---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: lajanuar
ms.openlocfilehash: 85f5a40ef569ff6ec462bfdeca5fa966b918473b
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326484"
---
* Para recuperar la dirección URL de la firma de acceso compartido para los datos de entrenamiento del modelo personalizado, vaya al recurso de almacenamiento en Azure Portal y seleccione la pestaña **Explorador de Storage**. Vaya al contenedor, haga clic con el botón derecho y seleccione **Obtener firma de acceso compartido**. Es importante obtener la firma de acceso compartido para el contenedor, no para la propia cuenta de almacenamiento. Asegúrese de que están seleccionados los permisos de **lectura**, **escritura**, **eliminación** y **enumeración** y haga clic en **Crear**. A continuación, copie el valor de la sección **URL** en una ubicación temporal. Debe tener el formato `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
