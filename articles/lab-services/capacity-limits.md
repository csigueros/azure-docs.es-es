---
title: Límites de capacidad en Azure Lab Services
description: Obtenga información sobre los límites de capacidad (relativos a las máquinas virtuales) en Azure Lab Services.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 344ca5950e38d3c4850accdbfb5062a011059919
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112294515"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Límites de capacidad en Azure Lab Services
Azure Lab Services tiene límites de capacidad predeterminados en las suscripciones de Azure para cumplir con las limitaciones de cuota de núcleos de Azure y mitigar posibles fraudes. Todas las suscripciones de Azure tendrán un límite de capacidad inicial, que puede variar en función del tipo de suscripción, el número de núcleos de proceso estándar y los núcleos de GPU disponibles dentro de Azure Lab Services. Este límite restringe el número de máquinas virtuales que se puede crear en los laboratorios antes de que haya que solicitar un aumento del límite.  

Si está cerca o ha alcanzado el límite de núcleos de máquina virtual de la suscripción, aparecerán mensajes de Azure Lab Services cuando intente realizar acciones con las que se crean más máquinas virtuales. Por ejemplo: 

- Creación de un laboratorio
- Publicar un laboratorio
- Ajustar la capacidad de laboratorio para agregar más máquinas virtuales a un laboratorio existente

Estas acciones pueden estar deshabilitadas también si ya ha alcanzado el límite de núcleos. 

![Límites de núcleo: mensaje de advertencia](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>Suscripciones con el límite predeterminado de cero núcleos
Algunos tipos de suscripción (bastante inusuales) que suelen usarse en los fraudes pueden tener un límite predeterminado de 0 núcleos estándar y 0 núcleos de GPU. Si usa uno de estos tipos de suscripción, el administrador que cree su cuenta de laboratorio deberá solicitar un aumento del límite para poder usar Azure Lab Services. 

El administrador puede seguir estos pasos para solicitar un aumento del límite:  

1.  [Crea una cuenta de laboratorio](tutorial-setup-lab-account.md) en su suscripción.
2.  En la página **Información general** de la cuenta de laboratorio, hace clic en el botón para **solicitar un aumento del límite** situado en la parte superior. 
3.  Realiza los pasos pertinentes del formulario para enviar una solicitud de soporte técnico para aumentar el límite.

## <a name="request-a-limit-increase"></a>Solicitud de un aumento del límite
Si alcanza el límite de núcleos, puede solicitar un aumento de límite para poder seguir usando Azure Lab Services. El proceso de solicitud es un punto de control para garantizar que su suscripción no está implicada en ningún caso de fraude o de implementación a gran escala involuntaria.

Los mensajes sobre el límite de núcleos de máquina virtual en el portal de Azure Lab Services incluyen un vínculo para solicitar un aumento del límite. El vínculo abre una nueva pestaña del explorador, donde puede tramitar una nueva solicitud de soporte técnico. La información relativa al tipo de problema, la suscripción y el tipo de cuota se rellenará automáticamente, como se muestra en la siguiente imagen: 

![Nueva solicitud de soporte](./media/capacity-limits/new-support-request.png)


Tras ello, se le pedirá que proporcione más información sobre el aumento del límite. En el campo **Descripción**, proporcione los siguientes detalles:

- Lo que intenta hacer (por ejemplo, crear un laboratorio para impartir una clase de informática, ejecutar un hackathon, etc.)
- Tamaño de la máquina virtual que usa en este laboratorio
- Número de máquinas virtuales necesarias

Una vez enviada su solicitud de soporte técnico, se revisará. Si es necesario, nos pondremos en contacto con usted para obtener más detalles. 

## <a name="next-steps"></a>Pasos siguientes
Consulte el artículo siguiente:
- [Guía del administrador: tamaño de la VM](administrator-guide.md#vm-sizing).
- [Preguntas más frecuentes](classroom-labs-faq.yml).