---
title: Configuración de precios para la facturación mensual en Azure Marketplace
description: Aprenda a configurar los precios de la máquina virtual.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshah
ms.author: iqshah
ms.date: 09/28/2021
ms.openlocfilehash: 6c08319037a06ff7bb1051fd41b40dc20ec37fb6
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219383"
---
# <a name="configure-prices-for-usage-based-monthly-billing"></a>Configuración de precios para la facturación mensual basada en uso

El plan de facturación mensual basado en el uso cobrará al cliente por su uso por hora y se facturará mensualmente. Este es nuestro plan de "Pago por uso", donde los clientes solo se facturan por las horas que han usado.
Al seleccionar este plan, elija una de las siguientes opciones de precios:

- **Gratis**: la oferta de máquina virtual es gratuita.
- **Tarifa plana (recomendada)** : la oferta de máquina virtual es el mismo precio, independientemente del hardware en el que se ejecute.
- **Por núcleo**: los precios de la oferta de máquina virtual son por recuento de núcleos de CPU (nos da el precio de un núcleo de CPU e incrementaremos los precios en función del tamaño del hardware).
- **Por tamaño de núcleo**: asigne precios en función del número de núcleos de CPU en el hardware en el que se implementa.
- **Por tamaño de mercado y núcleo**: asigne precios en función del número de núcleos de CPU en el hardware en el que se implementa y también para todos los mercados (la conversión de moneda la realiza el anunciante, esta opción es más fácil si usa la característica de precios de importación).

Algunos aspectos que se deben tener en cuenta al seleccionar una opción de precios:

- En las cuatro primeras opciones, Microsoft realiza la conversión de moneda.
- Microsoft sugiere el uso de precios de tarifa plana para soluciones de software.
- Los precios son fijos, por lo que una vez publicados no se pueden ajustar. Sin embargo, si desea reducir los precios de las ofertas de máquina virtual, puede abrir una [vale de soporte técnico](/azure/marketplace/support).

## <a name="new-offering-pricing"></a>Nuevos precios de ofertas

Microsoft Azure agrega periódicamente una nueva infraestructura de máquina virtual. En ocasiones, agregamos una máquina que tiene un recuento de CPU que no se ofrecía antes. Microsoft determina el precio del nuevo tamaño de núcleo en función de los precios anteriores y los agrega como precios sugeridos.

Los publicadores reciben un correo electrónico cuando se establece el precio de los nuevos tamaños de núcleo y tendrán algún tiempo para revisar y realizar ajustes según sea necesario. Una vez que se supera la fecha límite, Microsoft publica los precios de los tamaños de núcleo recién agregados.

Si el anunciante eligió Gratis, Tarifa plana o Por tamaño de núcleo, el anunciante ya ha proporcionado los detalles necesarios sobre cómo establecer los precios de la oferta para nuevos tamaños de núcleo y no es necesario realizar ninguna otra acción. Sin embargo, si el anunciante seleccionó previamente Por tamaño de núcleo o Por tamaño de mercado y núcleo, tendría que ponerse en contacto con Microsoft con su información de precios actualizada.

## <a name="next-steps"></a>Pasos siguientes

- Si tiene alguna pregunta, abra un vale con el [soporte técnico](/azure/marketplace/support).