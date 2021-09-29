---
title: Solución de problemas de utilización de reservas de Azure
description: Este artículo le ayuda a comprender las reservas de Azure que no muestran ningún uso o un uso del 0 % en Azure Portal, y a solucionar sus problemas. También se explica el uso que no coincide.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.openlocfilehash: 3bade2b547db3b76bde02731807d4e6ef3539711
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649613"
---
# <a name="troubleshoot-reservation-utilization"></a>Solución de problemas de utilización de reservas

Este artículo le ayuda a comprender las reservas de Azure que no muestran ningún uso o un uso del 0 % en Azure Portal, y a solucionar sus problemas. También se explica el uso que no coincide.

## <a name="symptoms"></a>Síntomas

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a **Reservas**.
1. En la lista de reservas, busque la cantidad de uso de una reserva en la columna **Uso (%)** . Podría ser 0 %.
1. Seleccione la reserva.
1. En la página de información general de la reserva, el porcentaje usado en el gráfico podría no coincidir con el valor que se muestra en la lista de reservas.

## <a name="cause"></a>Causa

La columna **Uso (%)** de Azure Portal muestra el valor del día actual. El valor se calcula a medida que los datos de uso llegan desde donde se ejecutan los recursos. Azure emplea el uso para calcular el porcentaje de uso.

Algunos recursos informan del uso más lentamente que otros. Además, algunos tipos de productos, como las bases de datos SQL, tardan en notificar los datos de uso.

La latencia puede hacer que el cálculo de uso muestre valores más bajos que el uso real. La diferencia se percibe en el límite del día. En tales casos, si Azure no obtiene datos de uso de cuatro a ocho horas, calcula un valor de 0 %. El valor 0 % se muestra porque no han llegado datos de uso y parece que la reserva no está aplicando ventajas para ninguno de los recursos.

A medida que llegan los datos de uso, el valor cambia hacia el porcentaje correcto. Cuando se recopilan todos los datos de uso, se determina el valor correcto y se muestra con precisión en el gráfico.

## <a name="solution"></a>Solución

Si observa que los valores de uso no coinciden con sus expectativas, revise el gráfico para obtener un mejor panorama del uso real. Cualquier valor en puntos con una antigüedad superior a dos días debe ser preciso. Los promedios a largo plazo de 7 a 30 días deben ser precisos.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo administrar las reservas, consulte [Administración de reservas para recursos de Azure](manage-reserved-vm-instance.md).