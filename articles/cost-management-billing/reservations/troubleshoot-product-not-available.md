---
title: Solución de problemas de tipo de reserva de Azure no disponible
description: Este artículo le ayuda a comprender y solucionar problemas de las instancias reservadas que aparecen como no disponibles en Azure Portal.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: primittal
ms.topic: troubleshooting
ms.date: 09/20/2021
ms.openlocfilehash: 51d30767c3e084ade041d22bac8a246aa5d1f502
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631141"
---
# <a name="troubleshoot-reservation-type-not-available"></a>Solución de problemas de tipo de reserva no disponible

Este artículo le ayuda a comprender y solucionar problemas de las instancias reservadas que aparecen como no disponibles en Azure Portal.

## <a name="symptoms"></a>Síntomas

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y vaya a **Reservas**.
2. Seleccione **+ Agregar** y, después, elija un servicio.
3. Seleccione la pestaña **Todos los productos**.
4. En la lista de productos, seleccione uno. Puede ver uno de los mensajes siguientes:
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="Ejemplo que muestra que el producto no está disponible para la suscripción o región seleccionadas" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="Ejemplo que muestra el mensaje de cuota de núcleos insuficientes" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>Causa

Algunas reservas no están disponibles para su compra porque:

- No todos los productos de instancia reservada están disponibles para su compra en cada región.
- La suscripción tiene una restricción de cuota.

### <a name="cause-1"></a>Causa 1

No todos los productos de instancia reservada están disponibles para su compra. Azure decide permitir o no permitir algunos productos en función de los costos asociados a proporcionar el descuento monetario a los clientes. En otros casos, Azure no ofrece algunos productos debido a las condiciones de capacidad en centros de datos específicos. Además, algunos grupos de desarrollo de productos de Azure ahora pueden permitir determinados productos porque quieren retirar un producto anterior.

En otros casos, Azure coloca restricciones de capacidad en varios productos en función de la demanda de esos recursos en algunas regiones. Por ejemplo, tal restricción podría realizarse cuando la demanda de un determinado tamaño de máquina virtual se agota en un centro de datos. En este ejemplo, Azure no puede garantizar la capacidad de los clientes que han comprado una reserva para ese tamaño en esa región. Por último, hay algunos productos que son únicos por varias razones. Estos productos solo se ponen a disposición de un pequeño conjunto seleccionado de clientes.

Puesto que algunas reservas no están disponibles para su compra, ¿por qué se muestran en Azure Portal? La respuesta se debe a que los usuarios crean solicitudes de soporte técnico que indican que no pueden encontrar los productos que desean. El equipo de desarrollo de reserva de Azure determinó que mostrando todos los productos con el mensaje `Product unavailable` da como resultado menos solicitudes de soporte técnico que no mostrándolos.

### <a name="cause-2"></a>Causa 2

La suscripción tiene una restricción de cuota. Las suscripciones tienen límites en el número de núcleos de CPU que pueden consumir. El límite se aplica a algunos productos de instancia reservada, especialmente a las máquinas virtuales. Azure quiere asegurarse de que las personas que compren una instancia reservada puedan usarla. Azure realiza una sencilla y rápida comprobación en Azure Portal para asegurarse de que dispone de suficientes núcleos libres en su suscripción para implementar la máquina virtual y obtener una ventaja en la compra de la reserva.

La comprobación para que pueda agregar un producto determinado al carro y comprar una reserva es sencilla. Azure evalúa el número total de núcleos de CPU disponibles para la suscripción y comprueba si el número es mayor que el número de núcleos del elemento seleccionado.

Azure no comprueba la cuota de las instancias reservadas del ámbito **Compartido** o de **Grupo de administración**. La ventaja de instancia reservada para el ámbito compartido se aplica a todas las suscripciones de la inscripción. La ventaja de instancia reservada para el ámbito del grupo de administración se aplica a todas las suscripciones que forman parte del grupo de administración y del ámbito de facturación. Azure no puede determinar si dispone de suficientes núcleos libres en todas las suscripciones para implementar el recurso. Sea cual sea la cuota, Azure siempre permite seleccionar un tamaño de máquina virtual cuando el ámbito seleccionado se establece en Compartido o Grupo de administración.

Además, Azure no realiza una comprobación de la cuota de las compras **recomendadas**. Las recomendaciones se basan en el uso activo. Azure supone que tiene suficientes núcleos para ejecutar un tamaño de máquina virtual específico porque ya ha generado el uso necesario para crear la recomendación.

## <a name="solution"></a>Solución

En función del mensaje de error que haya recibido, utilice una de las siguientes soluciones para el problema.

### <a name="solution-1"></a>Solución 1

Si ve el mensaje de _producto no disponible_, seleccione el vínculo **Contacto con soporte técnico** en el mensaje de error para solicitar agregar una excepción a la suscripción. Las excepciones no siempre se conceden.

### <a name="solution-2"></a>Solución 2

Si ve un mensaje que indica que la _cuota de núcleos es insuficiente_, puede cambiar el ámbito a **compartido**. Después de comprar la reserva, puede cambiar el ámbito de reserva de **compartido** a **único**.

O bien, seleccione el vínculo para **solicitar un aumento de la cuota** en el mensaje de error para solicitar una cuota de núcleos de CPU adicional para la suscripción.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre las opciones de ámbito de la reserva, consulte [Ámbito de las reservas](prepare-buy-reservation.md#scope-reservations).