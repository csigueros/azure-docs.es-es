---
title: Ampliación del soporte técnico del ciclo de vida de Red Hat Enterprise Linux
description: Más información sobre cómo agregar el complemento de soporte técnico ampliado del ciclo de vida de Red Hat Enterprise
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.openlocfilehash: 82aa54f1b9aea331c7c08128487fbc9711cac2df
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696843"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Soporte técnico ampliado del ciclo de vida de Red Hat Enterprise Linux (RHEL)

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux 

En este artículo se proporciona información sobre el soporte técnico ampliado del ciclo de vida para las imágenes de Red Hat Enterprise:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Ciclo de vida de Red Hat Enterprise Linux 6
A partir del 30 de noviembre de 2020, finalizará la fase de mantenimiento de Red Hat Enterprise Linux 6. La fase de mantenimiento va seguida de la fase de duración ampliada. Como Red Hat Enterprise Linux 6 realiza la transición de las fases de mantenimiento o completa, se recomienda encarecidamente actualizar a Red Hat Enterprise Linux 7 u 8. Si los clientes deben permanecer en Red Hat Enterprise Linux 6, se recomienda agregar el complemento de soporte técnico ampliado del ciclo de vida (ELS) de Red Hat Enterprise Linux.

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Pasos para agregar el soporte técnico Extended Lifecycle en máquinas virtuales de pago por uso de marketplace
1. Rellene el [formulario de ELS disponible en este vínculo](https://aka.ms/els-form) con su información de contacto y la información de suscripción de las máquinas virtuales para las que quiere agregar el soporte técnico ELS. La información adicional sobre los precios del complemento también está disponible en el formulario.
1. El equipo de Azure Red Hat Enterprise Linux se pondrá en contacto con usted y le enviará una lista de máquinas virtuales para el complemento de soporte técnico ELS en un plazo de 1 a 2 días hábiles. Revise la lista y responda para aceptar los precios del complemento.
1. El equipo de Azure Red Hat Enterprise Linux compartirá los pasos para agregar el paquete de cliente de ELS a las máquinas virtuales. Siga los pasos que se incluirán en el correo electrónico para seguir recibiendo mantenimiento de software (correcciones de errores y seguridad) y soporte técnico para Red Hat Enterprise Linux 6.

> [!Note]
> No comparta los pasos necesarios para usar el complemento ELS de RHEL con ninguna persona ajena a su organización. Póngase en contacto con AzureRedHatELS@microsoft.com para obtener soporte técnico o para cualquier duda adicional.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Ejecuto Red Hat Enterprise Linux 6 y no puedo migrar a una versión posterior en este momento. ¿De qué opciones dispongo?
* Continúe con la ejecución de Red Hat Enterprise Linux 6 y compre los repositorios del complemento de soporte técnico ampliado del ciclo de vida (ELS) para seguir recibiendo soporte técnico y mantenimiento de software limitado (consulte el proceso de actualización y los detalles de precios a continuación).
* Migre a Red Hat Enterprise Linux 7 u 8 tan pronto como pueda.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>¿Cuál es el cargo adicional por el uso del complemento de soporte técnico ampliado del ciclo de vida (ELS) de Red Hat Enterprise Linux?
Los costos relacionados con el soporte técnico Extended Lifecycle se pueden encontrar con el [formulario de ELS](https://aka.ms/els-form).

#### <a name="ive-deployed-a-vm-by-using-custom-image-how-can-i-add-extended-lifecycle-support-to-this-vm"></a>He implementado una máquina virtual con una imagen personalizada. ¿Cómo puedo agregar soporte técnico ampliado del ciclo de vida a esta máquina virtual?
Debe ponerse en contacto con Red Hat y obtener soporte técnico directamente de ellos.

#### <a name="ive-deployed-a-vm-by-using-custom-image-can-i-convert-this-vm-to-a-payg-vm"></a>He implementado una máquina virtual con una imagen personalizada. ¿Puedo convertir esta máquina virtual en una máquina virtual de Pago por uso?
Lamentablemente, no. La conversión no se admite actualmente en Azure.


## <a name="next-steps"></a>Pasos siguientes

* Para ver la lista completa de imágenes de RHEL en Azure, consulte las [imágenes de Red Hat Enterprise Linux (RHEL) disponibles en Azure](./redhat-imagelist.md).
* Para más información sobre la infraestructura de actualización de Azure Red Hat, consulte [Infraestructura de actualización de Red Hat para máquinas virtuales de RHEL a petición en Azure](./redhat-rhui.md).
* Para más información, consulte [Imágenes Gold de tipo Traiga su propia suscripción (BYOS) de Red Hat Enterprise Linux en Azure](./byos.md).
* Puede encontrar información sobre las directivas de soporte técnico de Red Hat para todas las versiones de RHEL en [Ciclo de vida de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).