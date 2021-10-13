---
title: Solicitud de la clave de autorización para ExpressRoute
description: Pasos para solicitar una clave de autorización para ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 1ab25c1c22dd2697b9101d76602e81d22adc5b37
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638490"
---
<!-- used in tutorial-expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. En Azure Portal, vaya a la nube privada de Azure VMware Solution. Seleccione **Administrar** > **Conectividad** > **ExpressRoute** y seleccione **+ Solicitar una clave de autorización**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Captura de pantalla que muestra cómo solicitar una clave de autorización de ExpressRoute." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Proporciónele un nombre y luego seleccione **Crear**.

   La clave puede tardar unos 30 segundos en crearse. Una vez creada, la nueva clave aparece en la lista de claves de autorización para la nube privada.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Captura de pantalla que muestra la clave de autorización de ExpressRoute Global Reach." lightbox="../media/expressroute-global-reach/show-global-reach-auth-key.png":::
  
1. Copie la clave de autorización y el identificador de ExpressRoute. Necesitará la información para finalizar el emparejamiento. La clave de autorización desaparece tras un tiempo, por lo que debe copiarla en cuanto aparezca.

