---
title: Comprobación del estado de la venta conjunta de una oferta del marketplace comercial | Azure Marketplace
description: Obtenga información sobre cómo comprobar el estado de la venta conjunta de una oferta en el marketplace comercial de Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 07/13/2021
ms.openlocfilehash: 3d2048f643da5f9cdd77b95f2a992efef74a77e9
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114388085"
---
# <a name="verify-co-sell-status-of-a-commercial-marketplace-offer"></a>Comprobación del estado de la venta conjunta de una oferta del marketplace comercial

Puede comprobar el estado de la venta conjunta de una oferta en la página **Información general de la oferta** de una oferta del programa de marketplace comercial del Centro de partners.

## <a name="verify-co-sell-status"></a>Comprobación del estado de la venta conjunta

1. Inicie sesión en el [Centro de partners](https://partner.microsoft.com/dashboard/home).
1. En el menú de navegación izquierdo, seleccione **Marketplace comercial** > **Información general**.
1. En la columna **Alias de la oferta**, seleccione la oferta que desee. El estado de la venta conjunta se muestra en la sección de programas de marketplace de la página.

    [![Muestra el estado de la venta conjunta en los programas de marketplace en la página de información general del Centro de partners.](./media/co-sell/co-sell-status.png)](./media//co-sell/co-sell-status.png#lightbox)

En la tabla siguiente se muestran todos los posibles estados de la venta conjunta. Para obtener información sobre los requisitos de cada estado de venta conjunta, consulte [Requisitos de venta conjunta](co-sell-requirements.md).

| Status | Comentario |
| ------------ | ------------- |
| No listo para la venta conjunta | No se cumplen los [requisitos mínimos para el estado Listo para la venta conjunta](co-sell-requirements.md#requirements-for-co-sell-ready-status). |
| Listo para la venta conjunta | Se cumplen todos los [requisitos para el estado Listo para la venta conjunta](co-sell-requirements.md#requirements-for-co-sell-ready-status). |
| Incentivo de venta conjunta de Azure IP | Se cumplen los requisitos de venta conjunta, además de [estos requisitos adicionales](co-sell-requirements.md#requirements-for-azure-ip-co-sell-incentive-status). |
| Incentivo de venta conjunta de Business Applications | Este estado se aplica a las ofertas de Dynamics 365 y Power Apps del [Programa Connect para ISV](business-applications-isv-program.md) e indica que se cumplen todos los [requisitos de este estado](co-sell-requirements.md#requirements-for-business-applications-co-sell-incentive-status). |
|||

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre los requisitos de venta conjunta, consulte [Requisitos de venta conjunta](co-sell-requirements.md).
- Para configurar una oferta para la venta conjunta, consulte [Configuración de la venta conjunta para una oferta del marketplace comercial](co-sell-configure.md).