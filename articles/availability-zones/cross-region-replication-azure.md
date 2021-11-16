---
title: Replicación entre regiones en Azure
description: Obtenga información sobre la replicación entre regiones en Azure.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: rarco
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: aa074d5f745f9a427fe3f4c197d0cd09de660a84
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325607"
---
# <a name="cross-region-replication-in-azure-business-continuity-and-disaster-recovery"></a>Replicación entre regiones en Azure: continuidad empresarial y recuperación ante desastres

Muchas organizaciones requieren la alta disponibilidad proporcionada por zonas de disponibilidad que también se admiten con la protección frente a fenómenos a gran escala y desastres regionales. Como se describe en la [información general](overview.md) sobre la resistencia para regiones y zonas de disponibilidad, las regiones de Azure están diseñadas para ofrecer protección frente a desastres locales con zonas de disponibilidad. Pero también pueden proporcionar protección frente a desastres de zonas geográficas grandes o regionales con recuperación ante desastres mediante el uso de otra región que usa la *replicación entre regiones*.

## <a name="cross-region-replication"></a>Replicación entre regiones

Para garantizar la prestación del servicio a los clientes en todo el mundo, Azure mantiene varias zonas geográficas. Estas demarcaciones discretas definen un límite de recuperación ante desastres y residencia de datos en una o varias regiones de Azure. 

La replicación entre regiones es uno de los varios pilares importantes de la estrategia de continuidad empresarial y recuperación ante desastres de Azure. La replicación entre regiones se basa en la replicación sincrónica de las aplicaciones y los datos existentes mediante el uso de zonas de disponibilidad dentro de la región principal de Azure para una alta disponibilidad. La replicación entre regiones replica de forma asincrónica las mismas aplicaciones y datos en otras regiones de Azure para la protección mediante la recuperación ante desastres. 

![Imagen que representa la alta disponibilidad con la replicación asincrónica de las aplicaciones y los datos en otras regiones de Azure para la protección mediante la recuperación ante desastres.](./media/cross-region-replication.png)

Algunos servicios de Azure utilizan la replicación entre regiones para garantizar la continuidad empresarial y protegerse frente a la pérdida de datos. Azure ofrece varias [soluciones de almacenamiento](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) que utilizan la replicación entre regiones para garantizar la disponibilidad de los datos. Por ejemplo, el [almacenamiento con redundancia geográfica de Azure](../storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) replica los datos en una región secundaria automáticamente. Este enfoque garantiza la durabilidad de los datos aunque no se pueda recuperar la región primaria.

No todos los servicios de Azure replican automáticamente los datos ni se replican automáticamente desde una región con errores para la replicación cruzada en otra región habilitada. En estos escenarios, el cliente debe configurar la replicación y la recuperación. Estos ejemplos son ilustraciones del *modelo de responsabilidad compartida*. Es un pilar fundamental de la estrategia de recuperación ante desastres. Para más información sobre el modelo de responsabilidad compartida y sobre la continuidad empresarial y la recuperación ante desastres en Azure, vea [Administración de la continuidad empresarial en Azure](business-continuity-management-program.md).

La responsabilidad compartida se convierte en la parte más importante de la toma de decisiones estratégicas en lo que respecta a la recuperación ante desastres. Azure no requiere el uso de la replicación entre regiones, por lo que puede usar servicios para generar resistencia sin la replicación cruzada en otra región habilitada. Sin embargo, se recomienda encarecidamente configurar los servicios esenciales entre regiones para beneficiarse del [aislamiento](../security/fundamentals/isolation-choices.md) y mejorar la [disponibilidad](overview.md). 

Para las aplicaciones que admiten varias regiones activas, se recomienda usar varias regiones habilitadas disponibles. Esta práctica garantiza una disponibilidad óptima para las aplicaciones y minimiza el tiempo de recuperación si un evento afecta a la disponibilidad. Siempre que sea posible, diseñe la aplicación para obtener la [resistencia máxima](/azure/architecture/framework/resiliency/overview) y facilitar la [recuperación ante desastres](/azure/architecture/framework/resiliency/backup-and-recovery).

## <a name="benefits-of-cross-region-replication"></a>Ventajas de la replicación entre regiones

La arquitectura de la replicación entre regiones para sus servicios y datos se puede decidir en función de cada servicio. Tendrá que adoptar un enfoque de análisis costo-beneficio basado en los requisitos estratégicos y empresariales de su organización. Las ventajas principales y amplias de la replicación entre regiones basada en el costo son complejas, extensas y necesitan elaboración. Entre las ventajas se incluye lo siguiente:

- **Secuencia de recuperación de regiones**: si se produce una interrupción en todas las zonas geográficas, se da prioridad a la recuperación de una región de cada conjunto de regiones habilitado. Se garantiza que las aplicaciones que se implementan en conjuntos de regiones habilitados tengan una de las regiones prioritarias para la recuperación. Si una aplicación se implementa entre regiones que no están habilitadas para la replicación entre regiones, la recuperación se puede retrasar.
- **Actualización secuencial**: las actualizaciones planeadas del sistema de Azure para las regiones habilitadas se escalonan cronológicamente para minimizar el tiempo de inactividad, el impacto de los errores y los errores lógicos en el extraño caso de que se produzca una actualización defectuosa.
- **Aislamiento físico**: Azure se esfuerza por garantizar una distancia mínima de 300 millas (483 kilómetros) entre los centros de datos de las regiones habilitadas, aunque no es posible en todas las zonas geográficas. La separación de los centros de datos reduce la probabilidad de que un desastre natural, un conflicto civil, las interrupciones de energía o las interrupciones de la red física puedan afectar a varias regiones. El aislamiento está sujeto a las restricciones de una zona geográfica, como la dimensión, la disponibilidad de la infraestructura de red o eléctrica y la normativa.
- **Residencia de datos**: una región reside dentro de la misma zona geográfica que su conjunto habilitado (a excepción del Sur de Brasil y Singapur) con el objeto de cumplir los requisitos de residencia de datos para fines de jurisdicción de impuestos y aplicación de la ley. 

## <a name="azure-cross-region-replication-pairings-for-all-geographies"></a>Emparejamientos de la replicación entre regiones de Azure para todas las zonas geográficas

Las regiones se emparejan para la replicación entre regiones en función de la proximidad y otros factores.

**Pares regionales de Azure**

| Geography | Par regional A | Par regional B |
| --- | --- | --- |
| Asia Pacífico |Asia Oriental (Hong Kong) | Sudeste Asiático (Singapur) |
| Australia |Este de Australia |Sudeste de Australia |
| Australia |Centro de Australia |Centro de Australia 2\* |
| Brasil |Sur de Brasil |Centro-sur de EE. UU. |
| Brasil |Sudeste de Brasil\* |Sur de Brasil |
| Canadá |Centro de Canadá |Este de Canadá |
| China |Norte de China |Este de China|
| China |Norte de China 2 |Este de China 2|
| Europa |Norte de Europa (Irlanda) |Oeste de Europa (Países Bajos) |
| Francia |Centro de Francia|Sur de Francia\*|
| Alemania |Centro-oeste de Alemania |Norte de Alemania\* |
| India |Centro de la India |Sur de la India |
| India |Oeste de la India |Sur de la India |
| Japón |Japón Oriental |Japón Occidental |
| Corea |Centro de Corea del Sur |Corea del Sur |
| Norteamérica |Este de EE. UU. |Oeste de EE. UU. |
| Norteamérica |Este de EE. UU. 2 |Centro de EE. UU. |
| Norteamérica |Centro-Norte de EE. UU |Centro-sur de EE. UU. |
| Norteamérica |Oeste de EE. UU. 2 |Centro-Oeste de EE. UU. |
| Norteamérica |Oeste de EE. UU. 3 |Este de EE. UU. |
| Noruega | Este de Noruega | Oeste de Noruega\* |
| Sudáfrica | Norte de Sudáfrica |Oeste de Sudáfrica\* |
| Suiza | Norte de Suiza |Oeste de Suiza\* |
| Reino Unido |Oeste de Reino Unido |Sur de Reino Unido |
| Emiratos Árabes Unidos | Norte de Emiratos Árabes Unidos | Centro de Emiratos Árabes Unidos\* |
| Departamento de Defensa de Estados Unidos |US DoD (este)\* |US DoD (centro)\* |
| US Gov |US Gov: Arizona\* |US Gov Texas\* |
| US Gov |US Gov Iowa\* |US Gov - Virginia\* |
| US Gov |US Gov - Virginia\* |US Gov Texas\* |

(\*) Ciertas regiones tienen acceso restringido para admitir escenarios de clientes específicos, como la recuperación ante desastres en el país. Estas regiones solo están disponibles cuando se solicitan mediante [la creación de una nueva solicitud de soporte técnico en Azure Portal](https://portal.azure.com/#blade/Microsoft\_Azure\_Support/HelpAndSupportBlade/newsupportrequest).

> [!IMPORTANT]
> - Oeste de la India se empareja en una sola dirección. La región secundaria del India occidental es Sur de la India, pero la región secundaria esta última es Centro de la India.
> - Sur de Brasil es un caso único porque se empareja con una región fuera de su ubicación geográfica. La región secundaria de Sur de Brasil es Centro-sur de EE. UU. La región secundaria de Centro-sur de EE. UU. no es Sur de Brasil.

## <a name="next-steps"></a>Pasos siguientes

- [Regiones que admiten Availability Zones en Azure](az-region.md)
- [Plantillas de inicio rápido](https://aka.ms/azqs)
