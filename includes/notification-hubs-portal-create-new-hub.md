---
title: Archivo de inclusión
description: archivo de inclusión
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 09/07/2021
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 9d33df05e54d9cdeb97631ca479dd8ac9869343c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "126056470"
---
1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **Todos los servicios** en el menú de la izquierda y seleccione **Notification Hubs** en la sección **Móvil**. Seleccione el icono de estrella junto al nombre del servicio para agregarlo a la sección **FAVORITOS** en el menú de la izquierda. Cuando se agrega **Notification Hubs** a **FAVORITOS**, selecciónelo en el menú de la izquierda.

      ![Azure Portal: selección de Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. En la página **Notification Hubs**, seleccione **Crear** en la barra de herramientas.

      ![Notification Hubs: botón Agregar de la barra de herramientas](./media/notification-hubs-portal-create-new-hub/create-toolbar-button.png)

1. En la pestaña **Básico** de la página **Centro de notificaciones**, siga estos pasos:

    1. En **Suscripción**, seleccione el nombre de la suscripción de Azure que desea usar y, a continuación, seleccione un grupo de recursos existente o cree uno nuevo.  

    1. Escriba un nombre único para el nuevo espacio de nombres en **Detalles del espacio de nombres**. 
    
    1. Un espacio de nombres contiene uno o varios centros de notificaciones, por lo que debe escribir un nombre para el centro en **Detalles del centro de notificaciones**. O bien, seleccione un espacio de nombres existente en la lista desplegable.

    1. Seleccione un valor en el cuadro de lista desplegable **Ubicación**. Este valor especifica la ubicación en la que quiere crear el centro.

    1. Seleccione **Crear**.

        ![Azure Portal: establecimiento de las propiedades del centro de notificaciones](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Seleccione **Notificaciones** (el icono de campana) y, luego, **Ir al recurso**. También puede actualizar la lista en la página **Notification Hubs** y seleccionar su centro.

      ![Azure Portal: ir al recurso](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Seleccione **Directivas de acceso** en la lista. Observe que las dos cadenas de conexión están disponibles. Las necesitará más adelante para gestionar las notificaciones push.

      >[!IMPORTANT]
      >*No* use la directiva **DefaultFullSharedAccessSignature** en la aplicación. Está pensada para usarse solamente en el back-end.
      >

      ![Azure Portal: cadenas de conexión del centro de notificaciones](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
