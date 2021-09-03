---
title: Alta disponibilidad y recuperación ante desastres
description: Aprenda a diseñar la aplicación de Batch para una interrupción regional.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: eb3d0cb218e607e38b7478b7cc7956963e450f46
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730197"
---
# <a name="design-your-batch-application-for-high-availability"></a>Diseño de la aplicación de Batch para una alta disponibilidad

Azure Batch está disponible en todas las regiones de Azure, pero cuando se crea una cuenta de Batch, se debe asociar con una región específica. A continuación, todas las operaciones para la cuenta de Batch se aplican a esa región. Por ejemplo, se crean los grupos y las máquinas virtuales (VM) asociadas en la misma región que la cuenta de Batch.

Al diseñar una aplicación que usa Batch, debe considerar la posibilidad de que Batch no esté disponible en una región. Es posible que encuentre una situación poco frecuente en la que haya un problema con la región como un todo, con el servicio completo de Batch en la región o con su cuenta de Batch específica.

Si la aplicación o solución que usa Batch siempre debe estar disponible, entonces se debe diseñar para conmutarse por error a otra región o para que la carga de trabajo siempre se divida en dos o más regiones. Ambos enfoques requieren al menos dos cuentas de Batch y que cada cuenta se encuentre en una región distinta.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Varias cuentas de Batch en varias regiones

Usar varias cuentas de Batch en regiones distintas permite que su aplicación siga ejecutándose si una cuenta de Batch en una región deja de estar disponible. Si su aplicación debe tener una alta disponibilidad, tener varias cuentas es particularmente importante.

En algunos casos, se pueden diseñar aplicaciones para que use intencionadamente dos o más regiones. Por ejemplo, cuando necesite una cantidad considerable de capacidad, puede que necesite usar varias regiones para controlar una aplicación a gran escala o satisfacer las necesidades de crecimiento futuro. Estas aplicaciones también requerirán varias cuentas de Batch (una por región usada).

## <a name="design-considerations-for-providing-failover"></a>Consideraciones de diseño para proporcionar la conmutación por error

Al proporcionar la capacidad de conmutación por error a una región alternativa, se deben incluir todos los componentes de la solución. No basta con tener simplemente una segunda cuenta de Batch. Por ejemplo, en la mayoría de las aplicaciones de Batch, se requiere de una cuenta de almacenamiento de Azure y dicha cuenta debe pertenecer a la misma región que la cuenta de Batch para que el rendimiento sea aceptable.

Tenga en cuenta lo siguiente a la hora de diseñar una solución con conmutación por error:

- Cree previamente todos los servicios necesarios en cada región, como la cuenta de Batch y la de almacenamiento. No se suele aplicar ningún cargo por tener cuentas creadas y los cargos se generan solo cuando se usa la cuenta o se almacenan datos.
- Asegúrese de establecer las [cuotas](batch-quota-limit.md) adecuadas en todas las suscripciones de antemano, de forma que pueda asignar el número necesario de núcleos mediante la cuenta de Batch.
- Use scripts o plantillas para automatizar la implementación de la aplicación en una región.
- Mantenga actualizados los archivos binarios de aplicación y los datos de referencia en todas las regiones. Mantenerlos actualizados garantizará que la región se pueda conectar rápidamente sin necesidad de esperar a que los archivos se carguen y se implementen. Por ejemplo, si una aplicación personalizada que se va a instalar en los nodos de grupo se almacena mediante paquetes de aplicación de Batch y se hace referencia a ella mediante los mismos, cuando se genere una nueva versión de dicha aplicación, la configuración de grupo deberá cargarla a cada cuenta de Batch y hacer referencia a ella (o la nueva versión tendrá que establecerse como versión predeterminada).
- En la aplicación que realice llamadas a Batch, al almacenamiento y a cualquier otro servicio, cambie los clientes o la carga con facilidad a regiones distintas.
- Considere la posibilidad de cambiar con frecuencia a una región alternativa como parte del funcionamiento normal. Por ejemplo, si tiene dos implementaciones en regiones distintas, cambie a la región alternativa cada mes.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información acerca de cómo crear cuentas de Batch con [Azure Portal](batch-account-create-portal.md), la [CLI de Azure](./scripts/batch-cli-sample-create-account.md), [Powershell](batch-powershell-cmdlets-get-started.md) o [Batch Management API](batch-management-dotnet.md).
- Obtenga información sobre las [cuotas predeterminadas asociadas a una cuenta de Batch](batch-quota-limit.md) y cómo pueden aumentarse estas.
