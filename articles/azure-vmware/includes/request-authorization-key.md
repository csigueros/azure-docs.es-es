---
title: Solicitud de la clave de autorización para ExpressRoute
description: Pasos para solicitar una clave de autorización para ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 5f4c436a8559a4b579a3a0c9bbfbe79bcb50764e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729046"
---
<!-- used in tutorial-expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. En Azure Portal, vaya a la nube privada de Azure VMware Solution. Seleccione **Administrar** > **Conectividad** > **ExpressRoute** y seleccione **+ Solicitar una clave de autorización**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Captura de pantalla que muestra cómo solicitar una clave de autorización de ExpressRoute." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Proporciónele un nombre y luego seleccione **Crear**.

   La clave puede tardar unos 30 segundos en crearse. Una vez creada, la nueva clave aparece en la lista de claves de autorización para la nube privada.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Captura de pantalla que muestra la clave de autorización de ExpressRoute Global Reach." lightbox="../media/expressroute-global-reach/show-global-reach-auth-key.png":::
  
1. Copie la clave de autorización y el identificador de ExpressRoute. Necesitará la información para finalizar el emparejamiento. La clave de autorización desaparece tras un tiempo, por lo que debe copiarla en cuanto aparezca.

