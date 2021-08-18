---
title: Adición de una audiencia de versión preliminar para una oferta de máquina virtual en Azure Marketplace
description: Incorpore una audiencia de versión preliminar para una oferta de máquina virtual en Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 8877862ba9f22fb41c31cc1167e836fd783abde0
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601137"
---
# <a name="add-a-preview-audience-for-a-virtual-machine-offer"></a>Incorporación de una audiencia de versión preliminar para una oferta de máquina virtual

En la página **Versión preliminar** (selecciónela en el menú de navegación de la izquierda del Centro de partners), seleccione una **audiencia de versión preliminar** limitada para validar la oferta antes de publicarla y ponerla a disposición de una audiencia más amplia del marketplace comercial.

> [!IMPORTANT]
> Después de revisar la oferta en el panel **Versión preliminar**, seleccione **Publicar** a fin de incluir la oferta para la audiencia pública en el marketplace comercial.

Los GUID de **identificador de suscripción de Azure**, junto con una **descripción** opcional de cada uno, identifican la audiencia de versión preliminar. Los clientes no pueden ver ninguno de estos campos. Puede encontrar el identificador de suscripción de Azure en la página **Suscripciones** de Azure Portal.

Agregue al menos un identificador de suscripción de Azure, ya sea de forma individual (hasta 10 identificadores) o mediante la carga de un archivo CSV (hasta 100 identificadores). Al agregar estos identificadores de suscripción, puede definir quién puede obtener una vista previa de la oferta antes de publicarla. Si la oferta ya está publicada, todavía puede definir una audiencia preliminar para probar los cambios o las actualizaciones de la oferta.

> [!NOTE]
> Un público preliminar no es el mismo que un público privado. A una audiencia preliminar se le permite acceder a la oferta *antes* de que se publique en Azure Marketplace. La audiencia privada podrá ver y validar todos los planes, incluidos aquellos que estén disponibles solo para una audiencia privada, después de que su oferta se publique por completo en Azure Marketplace. Una audiencia privada (definida en el panel **Precios y disponibilidad** del plan) tiene acceso exclusivo a un plan determinado.

Si hizo cambios, seleccione **Guardar borrador** antes de pasar a la pestaña siguiente del menú de navegación de la izquierda, **Información general del plan**.

## <a name="next-steps"></a>Pasos siguientes

- [Creación de un plan en Azure Stack Hub](azure-vm-create-plans.md)
