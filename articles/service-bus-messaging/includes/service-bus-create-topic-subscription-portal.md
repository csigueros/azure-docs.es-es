---
title: Archivo de inclusión
description: archivo de inclusión
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/11/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 491abcae4bf73d6b2dcd8172fcb1b69cb7b60d32
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807524"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Creación de un tema mediante Azure Portal
1. En la página **Espacio de nombres de Service Bus**, seleccione **Temas** en el menú izquierdo.
2. En la barra de herramientas, seleccione **+ Tema**. 
4. Escriba un **nombre** para el tema. Deje las restantes opciones con sus valores predeterminados.
5. Seleccione **Crear**.

    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/create-topic.png" alt-text="Imagen que muestra la página Crear un tema.":::

## <a name="create-a-subscription-to-the-topic"></a>Creación de una suscripción al tema
1. Seleccione el **tema** que creó en la sección anterior. 
    
    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/select-topic.png" alt-text="Imagen que muestra la selección del tema de la lista de temas.":::
2. En la página **Tema de Service Bus**, seleccione **Suscripción** en la barra de herramientas. 

    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png" alt-text="Imagen que muestra el botón Agregar suscripción.":::    
3. En la página **Crear suscripción**, siga estos pasos:
    1. Escriba **S1** como **nombre** de la suscripción.
    1. Escriba **3** en **Max delivery count** (Número máximo de entregas).
    1. Luego, seleccione **Create** (Crear) para guardar la suscripción. 

        :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png" alt-text="Imagen que muestra la página Crear suscripción.":::
