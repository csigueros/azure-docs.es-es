---
title: Conceptos de acceso de autoservicio de Azure Purview
description: Entienda en qué consisten el acceso de autoservicio y la detección de datos de Azure Purview y explore cómo pueden aprovecharlos los usuarios.
author: bjspeaks
ms.author: blessonj
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b4b90a7b2bab6801754121d56fc5b0fd7eca8c61
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092940"
---
# <a name="azure-purview-self-service-data-discovery-and-access-preview"></a>Detección y el acceso de datos de autoservicio de Azure Purview (versión preliminar)

Este artículo le ayuda a comprender la administración del acceso de autoservicio de Azure Purview.

> [!IMPORTANT]
> La detección y el acceso de datos de autoservicio de Azure Purview están actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="overview"></a>Información general

La administración del acceso de autoservicio de Azure Purview permite al consumidor de datos solicitar acceso a los datos al examinar o buscar datos. Actualmente, se admite el acceso de datos de autoservicio a cuentas de almacenamiento, contenedores, carpetas y archivos.

Un **administrador de colecciones** deberá asignar un flujo de trabajo de autoservicio a una colección. Una colección es una agrupación lógica de orígenes de datos que se registran en Azure Purview. **Solo los orígenes de datos registrados** para la administración de directivas permitirán a los consumidores de datos solicitar acceso a los datos.

## <a name="terminology"></a>Terminología

* El **consumidor de datos** es cualquier persona que use los datos. Por ejemplo, un analista de datos que accede a los datos de marketing para la segmentación de clientes. En este documento se usarán indistintamente los términos consumidor de datos y solicitante de datos.

* Una **colección** es una agrupación lógica de orígenes de datos que se registran en Azure Purview.

* El **flujo de trabajo de autoservicio** es el proceso que se invoca cuando un consumidor de datos solicita acceso a los datos.

* El **aprobador** es el grupo de seguridad o los usuarios de AAD que pueden aprobar solicitudes de acceso de autoservicio.

## <a name="how-to-use-azure-purview-self-service-access-management"></a>Uso de la administración del acceso de autoservicio de Azure Purview

Azure Purview permite a las organizaciones catalogar metadatos de todos los recursos de datos registrados. Permite a los consumidores de datos buscar o examinar el recurso de datos necesario.  

Con la administración del acceso de autoservicio, los consumidores de datos no solo pueden encontrar recursos de datos, sino también solicitar acceso a los recursos de datos que se encuentran dentro de los orígenes de datos registrados para la integración de directivas. Cuando el consumidor de datos solicita acceso a un recurso de datos, se desencadena el flujo de trabajo de acceso de autoservicio asociado.

Hay una plantilla de flujo de trabajo de autoservicio predeterminada disponible con la cuenta de Azure Purview.
La plantilla predeterminada se puede modificar para agregar más aprobadores o para establecer la dirección de correo electrónico del aprobador.

El rol de conservador de datos podrá personalizar la plantilla de flujo de trabajo de autoservicio predeterminada o usar el flujo de trabajo predeterminado sin personalización. El conservador de datos asigna los aprobadores de la solicitud de acceso. Para ello, puede usar un grupo de distribución de correo electrónico o la dirección de correo electrónico de un aprobador.

Cada vez que un consumidor de datos solicita acceso a un conjunto de datos, se envía una notificación al aprobador del flujo de trabajo. El aprobador podrá ver la solicitud y aprobarla desde el portal de Purview o desde la notificación por correo electrónico. Cuando se aprueba la solicitud, se genera automáticamente una directiva y se aplica al origen de datos correspondiente.

Se notifica al solicitante o al consumidor de datos que la solicitud se ha aprobado.

Si se rechaza la solicitud de acceso a datos de un consumidor de datos, no se genera una directiva y se notifica al consumidor de datos que se ha rechazado la solicitud.

## <a name="next-steps"></a>Pasos siguientes

Si quiere obtener una versión preliminar de estas características en su entorno, siga el vínculo que aparece a continuación.

-  [Inscripción para la versión preliminar de autoservicio de Azure Purview](https://aka.ms/opt-in-data-use-policy)
