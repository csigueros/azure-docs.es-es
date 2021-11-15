---
title: Aplicación de licencias de SQL asignadas centralmente al uso por hora en Azure
description: En este artículo se proporciona una explicación detallada sobre cómo Azure aplica licencias de SQL asignadas centralmente al uso por hora con Ventaja híbrida de Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 53f8538e6e34c91023bc55e7d47e5f28e9fb3e21
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130243854"
---
# <a name="how-azure-applies-centrally-assigned-sql-licenses-to-hourly-usage"></a>Aplicación de licencias de SQL asignadas centralmente al uso por hora en Azure

En este artículo se proporcionan detalles sobre cómo funciona la administración centralizada de Ventaja híbrida de Azure para SQL Server a nivel de ámbito. El proceso comienza con un administrador que asigna licencias a la suscripción o a un ámbito de cuenta de facturación.

Cada recurso informa de su uso una vez por hora con el precio completo o los medidores de pago por uso adecuados. Internamente en Azure, el motor de aplicación de uso evalúa las NCL disponibles y las aplica durante esa hora. Para una hora determinada de consumo de recursos de núcleo virtual, los medidores de pago por uso se cambian al medidor de la Ventaja híbrida de Azure correspondiente con un precio de cero (0 USD) si hay suficientes NCL sin usar en el ámbito seleccionado.

## <a name="license-discount"></a>Descuento por licencias 

En el diagrama siguiente se muestra el proceso de descuento cuando hay suficientes NCL sin utilizar para descontar todo el consumo de núcleos virtuales por todos los recursos de SQL durante la hora.

Los precios que se muestran en la siguiente imagen son solo a modo de ejemplo.

:::image type="content" source="./media/manage-licenses-centrally/fully-discounted-consumption.svg" alt-text="Diagrama que muestra el consumo total de núcleos virtuales con descuento" border="false" lightbox="./media/manage-licenses-centrally/fully-discounted-consumption.svg":::.


Cuando el consumo de núcleos virtuales por parte de los recursos de SQL en el ámbito supera el número de NCL sin utilizar, el consumo excesivo de núcleos virtuales se factura mediante el medidor de pago por uso adecuado. En el diagrama siguiente se muestra el proceso de descuento cuando el consumo de núcleo virtual supera el número de NCL sin utilizar.

Los precios que se muestran en la siguiente imagen son solo a modo de ejemplo.

:::image type="content" source="./media/manage-licenses-centrally/partially-discounted-consumption.svg" alt-text="Diagrama en el que se muestra parcialmente el consumo con descuento" border="false" lightbox="./media/manage-licenses-centrally/partially-discounted-consumption.svg":::.

Los recursos de Azure SQL cubiertos por las licencias Core asignadas pueden variar de una hora a otra. La varianza depende de qué recursos se ejecutan y en qué orden el sistema automatizado procesa su uso. Sin embargo, el sistema garantiza el uso máximo de las licencias de SQL asignadas en el ámbito seleccionado. Puede supervisar el uso mediante Cost Management. Para más información, vea [Seguimiento del uso de licencias asignadas](create-sql-license-assignments.md#track-assigned-license-use).

En el diagrama siguiente se muestra cómo se aplican las licencias asignadas de SQL Server a lo largo del tiempo para obtener el máximo descuento de la Ventaja híbrida de Azure.

:::image type="content" source="./media/manage-licenses-centrally/ncl-utilization-over-time.png" alt-text="Diagrama en el que se muestra el uso de las NCL con el paso del tiempo" border="false" lightbox="./media/manage-licenses-centrally/ncl-utilization-over-time.png":::.

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Preguntas frecuentes sobre la administración centralizada de Ventaja híbrida de Azure](faq-azure-hybrid-benefit-scope.yml).
- Obtenga información sobre cómo realizar la [transición desde la experiencia de la Ventaja híbrida de Azure existente](transition-existing.md).