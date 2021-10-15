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
ms.openlocfilehash: 4c758dfdc40e23197128bc08cccc5748f08f7ed4
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534880"
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

## <a name="other-common-scenarios"></a>Otros escenarios comunes
- Es posible que haya dejado de ejecutar el recurso A y haya empezado a ejecutar el recurso B, que no es aplicable a la reserva para la que ha adquirido. Para solucionar este problema, es posible que tenga que intercambiar la reserva para que coincida con el recurso correcto. 
- Es posible que haya movido un recurso de una suscripción o grupo de recursos a otro, mientras que el ámbito de la reserva es diferente del lugar al que se está moviendo el recurso. Para resolver este caso, es posible que tenga que cambiar el ámbito de la reserva.
- Es posible que haya adquirido otra reserva que también aplicó una ventaja al mismo ámbito y, como resultado, menos de una instancia reservada existente aplicó una ventaja. Para resolver esto, es posible que deba cambiar o reembolsar una de las reservas.
- Es posible que haya dejado de ejecutar un recurso determinado y, como resultado, dejó de emitir el uso y la ventaja dejó de aplicarse. Para solucionar este problema, es posible que tenga que intercambiar la reserva para que coincida con el recurso correcto. 
- Es posible que haya cambiado el ámbito de la reserva y que esto haya hecho que dejara de aplicar una ventaja a los recursos. Para corregirlo, es posible que deba cambiar el ámbito de la reserva de nuevo para asegurarse de que los recursos se implementan en el mismo ámbito.
- La suscripción a la que se ha limitado el ámbito de la reserva se eliminó o se cambió de sitio, por lo que la ventaja no se aplica a los recursos. Para resolver esto, es posible que tenga que cambiar el ámbito de la reserva.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo administrar las reservas, consulte [Administración de reservas para recursos de Azure](manage-reserved-vm-instance.md).
