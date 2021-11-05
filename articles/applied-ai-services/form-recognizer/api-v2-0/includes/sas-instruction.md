---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.openlocfilehash: e05f24947281b30deb3e87db601b964dae180cde
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253347"
---
Para recuperar la dirección URL de la firma de acceso compartido para los datos de entrenamiento del modelo personalizado, vaya al recurso de almacenamiento en Azure Portal y seleccione la pestaña **Explorador de Storage**. Vaya al contenedor, haga clic con el botón derecho y seleccione **Obtener firma de acceso compartido**. Es importante obtener la firma de acceso compartido para el contenedor, no para la propia cuenta de almacenamiento. Asegúrese de que estén marcados los permisos de **lectura**, **escritura**, **eliminación** y **enumeración**, y seleccione **Crear**. A continuación, copie el valor de la sección **URL** en una ubicación temporal. Debe tener el formato `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
