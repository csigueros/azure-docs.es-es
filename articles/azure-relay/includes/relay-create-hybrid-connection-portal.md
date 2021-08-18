---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 0db7058ba6491aae86bbaf45257a7aed9041bc35
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "114666039"
---
Asegúrese de haber [creado un espacio de nombres de Relay][namespace-how-to].

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el menú izquierdo, seleccione **Todos los recursos**.
3. Seleccione el espacio de nombres en el que quiere crear la conexión híbrida. En este caso, es **mynewns**.  
4. En **Relay namespace** (Espacio de nombres de Relay), seleccione **Conexiones híbridas**.

    ![Create a hybrid connection](./media/relay-create-hybrid-connection-portal/create-hc-1.png)

5. En la ventana de información general del espacio de nombres, haga clic en **+ Hybrid Connection** (+ Conexión híbrida).
   
    ![Seleccione la conexión híbrida](./media/relay-create-hybrid-connection-portal/create-hc-2.png)
6. En **Crear conexión híbrida**, escriba un valor para el nombre de la conexión híbrida. Deje los demás valores predeterminados.
   
    ![Seleccionar Nuevo](./media/relay-create-hybrid-connection-portal/create-hc-3.png)
7. Seleccione **Crear**.

[namespace-how-to]: ../relay-create-namespace-portal.md 