---
title: Archivo de inclusión
description: archivo de inclusión
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413090"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Creación de una cuenta de almacenamiento para el host de procesador de eventos
El host de procesador de eventos es un agente inteligente que simplifica la recepción de eventos desde Event Hubs mediante la administración de puntos de control persistentes y recepciones paralelas. Para los puntos de comprobación, el host de procesador de eventos requiere una cuenta de almacenamiento. El ejemplo siguiente muestra cómo crear una cuenta de almacenamiento y cómo obtener sus claves para el acceso:

1. En el menú de Azure Portal, seleccione **Crear un recurso**.

    ![Crear un elemento del menú de recursos, Microsoft Azure Portal](./media/event-hubs-create-storage/create-resource.png)

2. Seleccione **Almacenamiento** > **Cuenta de almacenamiento**.
   
    ![Creación de una cuenta de almacenamiento, Microsoft Azure Portal](./media/event-hubs-create-storage/select-storage-account.png)

3. En la página **Crear cuenta de almacenamiento**, realice los pasos siguientes: 

   1. Escriba el **nombre de la cuenta de almacenamiento**.
   2. Elija una **suscripción** de Azure que contenga el centro de eventos.
   3. Elija o cree el **grupo de recursos** que tenga el centro de eventos.
   4. Seleccione la **ubicación** en la que va a crear el recurso. 
   5. Seleccione **Revisar + crear**.
   
        ![Revisar y crear, Crear cuenta de almacenamiento, Microsoft Azure Portal](./media/event-hubs-create-storage/review-create.png)

4. En la página **Revisar + crear**, revise los valores y seleccione **Crear**. 

    ![Revisión de la configuración de la cuenta de almacenamiento y creación, Microsoft Azure Portal](./media/event-hubs-create-storage/create-storage-account.png)
5. Después de ver el mensaje **Correcto** en las notificaciones, seleccione **Ir al recurso** para abrir la página de la cuenta de almacenamiento. Como alternativa, puede expandir **Detalles de la implementación** y, después, seleccionar el nuevo recurso en la lista de recursos.  

    ![Ir al recurso, implementación de la cuenta de almacenamiento, Microsoft Azure Portal](./media/event-hubs-create-storage/go-to-resource.png) 
6. Seleccione **Contenedores**.

    ![Seleccionar el servicio de contenedor de blobs, cuentas de almacenamiento, Microsoft Azure Portal](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Seleccione **+ Contenedor** en la parte superior, escriba un **nombre** para el contenedor y seleccione **Aceptar**. 

    ![Crear un contenedor de blobs, cuentas de almacenamiento, Microsoft Azure Portal](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Elija **Claves de acceso** en el menú de la página **Cuenta de almacenamiento** y copie el valor de **key1**.

    Guarde los valores siguientes en el Bloc de notas o en cualquier otra ubicación temporal.
    - Nombre de la cuenta de almacenamiento
    - Clave de acceso para la cuenta de almacenamiento
    - Nombre del contenedor
