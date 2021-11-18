---
title: Adición de una audiencia de versión preliminar para una oferta de máquina virtual en Azure Marketplace
description: Incorpore una audiencia de versión preliminar para una oferta de máquina virtual en Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.reviewer: amhindma
ms.date: 11/11/2021
ms.openlocfilehash: acbef7c640222656cfe737194b1cc8b43b3d6d98
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370448"
---
# <a name="add-a-preview-audience-for-a-virtual-machine-offer"></a>Incorporación de una audiencia de versión preliminar para una oferta de máquina virtual

En la página **Público preliminar** (selecciónela en el menú de navegación de la izquierda del Centro de partners), seleccione un *Público preliminar* limitado para validar su oferta antes de publicarla y ponerla a disposición de un público más amplio en el marketplace comercial. Al publicar la oferta, los vínculos de versión preliminar estarán disponibles para los miembros del público preliminar que haya especificado. Solo el público privado que configure podrá acceder a los vínculos de versión preliminar y comprobar los detalles de la versión preliminar de la oferta antes de cerrar sesión para **Publicarla**.

Los vínculos de versión preliminar no están disponibles para los planes ocultos. En el caso de los planes ocultos, el público preliminar puede probar la oferta a través del símbolo del sistema.

Los **identificadores de suscripción de Azure**, identifican su público preliminar, junto con una **descripción** opcional de cada uno. Los clientes no pueden ver ninguno de estos campos. Puede encontrar el identificador de suscripción de Azure en la página **[Suscripciones](https://go.microsoft.com/fwlink/?LinkId=2122490)** de Azure Portal.

Agregue al menos un identificador de suscripción de Azure, ya sea individualmente o cargando un archivo CSV. Si la oferta ya está publicada, todavía puede modificar el público preliminar para probar los cambios o las actualizaciones de la oferta.

> [!IMPORTANT]
> Los cambios realizados en el público privado de la oferta deben guardarse y solo tendrán efecto después de volver a publicar la oferta.

> [!NOTE]
> Un público preliminar no es el mismo que un público privado. Un público preliminar es una lista de identificadores de suscripción que pueden probar y validar la oferta. Esto incluye los planes privados, antes de que estén disponibles para los usuarios. Por el contrario, al hacer que una oferta sea privada, debe especificar un público privado para restringir la visibilidad de la oferta a los clientes de su elección. Una audiencia privada (definida en la página **Precios y disponibilidad** de cada uno de los planes) es una lista de identificadores de suscripción o identificadores de inquilino que tendrán acceso a un plan determinado una vez que la oferta esté activa.

Después de configurar el público preliminar, seleccione **Guardar borrador** antes de pasar a la pestaña siguiente del menú de navegación de la izquierda, **Información general del plan**.

## <a name="next-steps"></a>Pasos siguientes

- [Creación de un plan en Azure Stack Hub](azure-vm-create-plans.md)
