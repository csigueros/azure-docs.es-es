---
title: Solución de problemas de recomendaciones de reservas de Azure
description: Este artículo le ayuda a comprender y solucionar problemas de las recomendaciones de reserva de Azure que se muestran en Azure Portal.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.openlocfilehash: 9ab8626a10e729e5bdf070f355fcc480d6dc4f16
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646822"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>Solución de problemas de recomendaciones de reservas de Azure

Este artículo le ayuda a comprender y solucionar problemas de las recomendaciones de reserva de Azure que se muestran en Azure Portal. Es posible que no vea recomendaciones ni recomendaciones que no coincidan con sus expectativas. Por ejemplo, puede que ya tenga una instancia reservada para una configuración de máquina virtual específica. Podría esperar ver una recomendación para una configuración de máquina virtual similar.

## <a name="symptoms"></a>Síntomas

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y vaya a **Reservas**.
2. Seleccione **+ Agregar** y, después, elija un servicio.
3. En la pestaña **Recomendado**, es posible que no vea recomendaciones ni recomendaciones que no cumplan sus expectativas.

## <a name="cause"></a>Causa

La lista de productos recomendados se genera por la cantidad de uso que tiene para el ámbito (compartido o único) en comparación con el costo de una reserva para el producto. Si el motor de recomendaciones detecta el ahorro, recomienda un producto para su adquisición. Sin embargo, cuando el motor no identifica ningún ahorro, no se recomienda ningún producto.

Al ejecutar un recurso con una configuración determinada, no hay ninguna garantía de que ahorre dinero mediante la compra de una reserva para esa configuración. Por ejemplo, podría tener un uso esporádico. Si es así, el costo total de la reserva podría no ahorrarle dinero a lo largo de la duración del período de reserva.

También es importante comprender cómo afecta la selección del ámbito a las recomendaciones. Cuando el ámbito se establece en **compartido**, las recomendaciones de la lista muestran las instancias reservadas donde Azure encuentra ahorros para toda la inscripción asociada a la suscripción de facturación. Cuando el ámbito se establece en **único**, las recomendaciones de la lista solo se aplican a los recursos que se ejecutan en la suscripción. Es posible que se recomienden algunos tamaños de máquina virtual para un ámbito, pero no para otro. Por ejemplo, podría haber agregado el uso de **Standard_B1ls** en la inscripción lo suficientemente alto como para justificar el costo de adquirir una reserva en el ámbito de inscripción. Sin embargo, una suscripción única en la inscripción podría no tener suficiente uso para justificar el costo de adquirir una reserva en el ámbito. Cambiar el ámbito entre **compartido** y **único** podría generar recomendaciones diferentes.

Azure puede recomendar la compra de una reserva para ciertos términos y no para otros, en función del ahorro de costos identificado. En concreto, los términos de tres años tienen más descuentos que los de un año. Es más probable que Azure encuentre un ahorro durante un término de tres años que para un término de un año.

Si desea comprender por qué Azure recomienda una cantidad y un tamaño de recurso específicos, seleccione **&lt;Cantidad&gt;. Consulte los detalles** para ver una visualización detallada que muestra un ahorro potencial con el tiempo.

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="Ejemplo que muestra la recomendación de reserva. Consulte el vínculo de detalles." lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>Solución

Puede comprar cualquier cantidad de reserva para un término que desee. La compra de una reserva con una cantidad y un término que difieran de la recomendación probablemente dará lugar a un ahorro y un uso óptimos.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre las recomendaciones de reserva, consulte [Determinación de qué reserva comprar](determine-reservation-purchase.md).
