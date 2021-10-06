---
title: Archivo de inclusión
description: archivo de inclusión
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 3a5413591ba637364bbb42a7e7aedf77b40fb898
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128565698"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Creación de un tema mediante Azure Portal
1. En la página **Espacio de nombres de Service Bus**, seleccione **Temas** en el menú izquierdo.
2. En la barra de herramientas, seleccione **+ Tema**. 
4. Escriba un **nombre** para el tema. Deje las restantes opciones con sus valores predeterminados.
5. Seleccione **Crear**.

    ![Crear tema](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Crear suscripciones en el tema
1. Seleccione el **tema** que creó en la sección anterior. 
    
    ![Selección de tema](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. En la página **Tema de Service Bus**, seleccione **Suscripciones** en el menú izquierdo y, a continuación, **+ Suscripción** en la barra de herramientas. 
    
    ![Botón Agregar suscripción](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. En la página **Crear suscripción**, siga estos pasos:
    1. Escriba **S1** como **nombre** de la suscripción.
    1. Escriba **3** en **Max delivery count** (Número máximo de entregas).
    1. Luego, seleccione **Create** (Crear) para guardar la suscripción. 

        ![Página Crear suscripción](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Repita dos veces el paso anterior para crear suscripciones denominadas **S2** y **S3**.