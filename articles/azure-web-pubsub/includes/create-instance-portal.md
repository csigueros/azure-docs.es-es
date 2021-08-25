---
title: Creación de una instancia de Azure Web PubSub con Azure Portal
description: Archivo de inclusión sobre la creación de una instancia de Azure Web PubSub con el portal
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 03/11/2021
ms.openlocfilehash: 38571df633c50cf309f15bd98218e77ab75c610e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734167"
---
## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com/](https://portal.azure.com/) con su cuenta de Azure.

## <a name="create-an-azure-web-pubsub-service-instance"></a>Creación de una instancia de servicio de Azure Web PubSub

La aplicación se conectará a una instancia de servicio de Web PubSub en Azure.

1. Seleccione el botón Nuevo de la esquina superior izquierda en Azure Portal. En la pantalla "Nuevo", escriba *Web PubSub* en el cuadro de búsqueda y presione Entrar. (También puede buscar Azure Web PubSub en la categoría `Web`).

    :::image type="content" source="../media/create-instance-portal/search-web-pubsub-in-portal.png" alt-text="Captura de pantalla de la búsqueda de Azure Web PubSub en el portal.":::

2. Seleccione **Web PubSub** en los resultados de la búsqueda y, después, haga clic en **Crear**.

3. Escriba la siguiente configuración.

    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nombre del recurso** | Nombre único globalmente | Nombre único global que identifica la nueva instancia del servicio Web PubSub. Los caracteres válidos son `a-z`, `0-9` y `-`. |
    | **Suscripción** | Su suscripción | Suscripción de Azure en la que se va a crear esta instancia del servicio Web PubSub. |
    | **[Grupo de recursos](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nombre del grupo de recursos nuevo en el que se va a crear la instancia del servicio Web PubSub. |
    | **Ubicación** | Oeste de EE. UU. | Seleccione una [región](https://azure.microsoft.com/regions/) cerca de usted. |
    | **Plan de tarifa** | Gratuito | Pruebe el servicio Azure Web PubSub de forma gratuita. Más información sobre los [planes de tarifa del servicio Azure Web PubSub](https://azure.microsoft.com/pricing/details/web-pubsub/). |
    | **Recuento de unidades** |  - | El recuento de unidades especifica cuántas conexiones puede aceptar la instancia del servicio Web PubSub. Cada unidad admite 1000 conexiones simultáneas como máximo. Solo es configurable en el nivel Estándar. |

    :::image type="content" source="../media/howto-develop-create-instance/create-web-pubsub-instance-in-portal.png" alt-text="Captura de pantalla de la creación de la instancia de Azure Web PubSub en el portal.":::

4. Seleccione **Crear** para empezar a implementar la instancia del servicio Web PubSub.
