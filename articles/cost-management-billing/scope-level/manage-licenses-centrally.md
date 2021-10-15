---
title: Forma en que Azure aplica las licencias de SQL asignadas al uso por hora
description: En este artículo se proporciona una explicación detallada sobre cómo Azure aplica las licencias de SQL asignadas al uso por hora con la Ventaja híbrida de Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: bc08d9465830043071fd78a6e9735f0443be42ec
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547287"
---
# <a name="how-azure-applies-assigned-sql-licenses-to-hourly-usage"></a>Forma en que Azure aplica las licencias de SQL asignadas al uso por hora

En este artículo se proporciona una explicación detallada sobre cómo funciona la Ventaja híbrida de Azure en un nivel de ámbito para ayudarle a administrar las licencias de SQL Server. El proceso comienza con un administrador que asigna licencias a la suscripción o a un ámbito de cuenta de facturación.

Cada recurso informa de su uso una vez por hora con el precio completo o los medidores de pago por uso adecuados. Internamente en Azure, el motor de aplicación de uso evalúa las NCL disponibles y las aplica durante esa hora. Para una hora determinada de consumo de recursos de núcleo virtual, los medidores de pago por uso se cambian al medidor de la Ventaja híbrida de Azure correspondiente con un precio de cero (0 USD) si hay suficientes NCL sin usar en el ámbito seleccionado.

## <a name="license-discount"></a>Descuento por licencias 

En el diagrama siguiente se muestra el proceso de descuento cuando hay suficientes NCL sin utilizar para descontar todo el consumo de núcleos virtuales por todos los recursos de SQL durante la hora.

Los precios que se muestran en la siguiente imagen son solo a modo de ejemplo.

:::image type="content" source="./media/manage-licenses-centrally/fully-discounted-consumption.svg" alt-text="Diagrama que muestra el consumo total de núcleos virtuales con descuento" border="false" lightbox="./media/manage-licenses-centrally/fully-discounted-consumption.svg":::.


Cuando el consumo de núcleos virtuales por parte de los recursos de SQL en el ámbito supera el número de NCL sin utilizar, el consumo excesivo de núcleos virtuales se factura mediante el medidor de pago por uso adecuado. En el diagrama siguiente se muestra el proceso de descuento cuando el consumo de núcleo virtual supera el número de NCL sin utilizar.

Los precios que se muestran en la siguiente imagen son solo a modo de ejemplo.

:::image type="content" source="./media/manage-licenses-centrally/partially-discounted-consumption.svg" alt-text="Diagrama en el que se muestra parcialmente el consumo con descuento" border="false" lightbox="./media/manage-licenses-centrally/partially-discounted-consumption.svg":::.

Los recursos de Azure SQL cubiertos por las licencias Core asignadas pueden variar de una hora a otra. La varianza depende de qué recursos se ejecutan y en qué orden el sistema automatizado procesa su uso. Sin embargo, el sistema garantiza el uso máximo de las licencias de SQL asignadas en el ámbito seleccionado. Puede supervisar el uso mediante Azure Cost Management. Para más información, vea [Seguimiento del uso de licencias asignadas](create-sql-license-assignments.md#track-assigned-license-use).

En el diagrama siguiente se muestra cómo se aplican las licencias asignadas de SQL Server a lo largo del tiempo para obtener el máximo descuento de la Ventaja híbrida de Azure.

:::image type="content" source="./media/manage-licenses-centrally/ncl-utilization-over-time.png" alt-text="Diagrama en el que se muestra el uso de las NCL con el paso del tiempo" border="false" lightbox="./media/manage-licenses-centrally/ncl-utilization-over-time.png":::.

## <a name="next-steps"></a>Pasos siguientes

- Vea [Preguntas frecuentes sobre la administración del nivel de ámbito de la Ventaja híbrida de Azure](faq-azure-hybrid-benefit-scope.yml).
- Obtenga información sobre cómo realizar la [transición desde la experiencia de la Ventaja híbrida de Azure existente](transition-existing.md).