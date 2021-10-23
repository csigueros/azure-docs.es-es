---
title: Regiones y zonas de disponibilidad de Azure
description: Obtenga información sobre las regiones y las zonas de disponibilidad y su funcionamiento para lograr una auténtica resistencia.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: bc31ebc507eed47caab3118aa32332cb26005f7a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003351"
---
# <a name="regions-and-availability-zones"></a>Regiones y zonas de disponibilidad

Las regiones y zonas de disponibilidad de Azure están diseñadas para ayudarle a lograr resistencia y confiabilidad en las cargas de trabajo críticas para la empresa. Azure mantiene varias zonas geográficas. Estas demarcaciones discretas definen los límites de recuperación ante desastres y residencia de datos en una o varias regiones de Azure. El mantenimiento de muchas regiones garantiza que se admita a clientes de todo el mundo. 

## <a name="regions"></a>Regions

Cada región de Azure incluye centros de datos implementados en un perímetro definido por la latencia. Estos se conectan a través de una red dedicada, regional y de baja latencia. Gracias a ellos, se garantiza que los servicios de Azure de una región ofrezcan el mejor rendimiento y seguridad posibles.

## <a name="availability-zones"></a>Zonas de disponibilidad

Las *zonas de disponibilidad* de Azure son ubicaciones separadas físicamente dentro de cada región de Azure y toleran los errores locales. Estos errores pueden abarcar desde errores de software y hardware hasta eventos como terremotos, inundaciones e incendios. La tolerancia a los errores se logra gracias a la redundancia y el aislamiento lógico de los servicios de Azure. Para garantizar la resistencia, se configuran un mínimo de tres zonas de disponibilidad independientes en todas las regiones habilitadas. 

Estas zonas de disponibilidad de Azure se conectan mediante una red de alto rendimiento que cuenta con una latencia de ida y vuelta inferior a 2 milisegundos. Asimismo, le ayudan a sincronizar los datos y a mantenerlos accesibles cuando las cosas van mal. Cada zona consta de uno o varios centros de datos equipados con una infraestructura de alimentación, refrigeración y redes independientes. Las zonas de disponibilidad están diseñadas de manera que, si se ve afectada una zona, los servicios regionales, la capacidad y la alta disponibilidad serán compatibles con las dos zonas restantes.

![Imagen que muestra ubicaciones de zonas de disponibilidad físicamente separadas dentro de una región de Azure.](media/availability-zones.png)

Las ubicaciones del centro de datos se seleccionan mediante criterios rigurosos de evaluación de riesgos de vulnerabilidades. Este proceso identifica todos los riesgos significativos específicos del centro de datos y tiene en cuenta los riesgos compartidos entre las zonas de disponibilidad.

Gracias a las zonas de disponibilidad, puede diseñar y usar aplicaciones y bases de datos que cambian automáticamente entre zonas sin interrupción. Asimismo, las zonas de disponibilidad de Azure son de alta disponibilidad, tolerantes a errores y más escalables que las infraestructuras tradicionales de uno o varios centros de datos.

Puede compilar una alta disponibilidad en la arquitectura de la aplicación mediante el uso de servicios de Azure que ofrezcan resistencia. Ubique los recursos de proceso, almacenamiento, redes y datos en una zona de disponibilidad y replique esta disposición en otras zonas de disponibilidad.

Los *servicios habilitados para zonas de disponibilidad* de Azure están diseñados para proporcionar el nivel adecuado de resistencia y flexibilidad. Se pueden configurar de dos maneras. Pueden tener *redundancia de zona*, con una replicación automática entre zonas o ser *zonales*, con instancias ancladas a una zona específica. También puede combinar ambos enfoques.

Algunas organizaciones requieren una alta disponibilidad de zonas de disponibilidad y protección frente a desastres regionales y a gran escala. Por ello, las regiones de Azure están diseñadas para ofrecer protección contra desastres localizados con zonas de disponibilidad, así como protección frente a desastres geográficos regionales o a gran escala con recuperación ante desastres, mediante el uso de otra región. Para obtener más información sobre la continuidad empresarial, la recuperación ante desastres y la replicación entre regiones, consulte [Replicación entre regiones en Azure](../best-practices-availability-paired-regions.md).

![Imagen que muestra las zonas de disponibilidad que protegen contra desastres localizados y desastres regionales o geográficos a gran escala, mediante el uso de otra región.](media/availability-zones-region-geography.png)

## <a name="azure-regions-with-availability-zones"></a>Regiones de Azure con zonas de disponibilidad
Azure proporciona la superficie global más amplia de cualquier proveedor de nube y está abriendo rápidamente nuevas regiones y zonas de disponibilidad.

| América | Europa | África | Asia Pacífico |
|--------------------|----------------------|---------------------|----------------|
| Sur de Brasil | Centro de Francia | Norte de Sudáfrica | Este de Australia |
| Centro de Canadá | Centro-oeste de Alemania | | Centro de la India\* |
| Centro de EE. UU. | Norte de Europa | | Japón Oriental |
| Este de EE. UU. | Este de Noruega | | Centro de Corea del Sur |
| Este de EE. UU. 2 | Sur de Reino Unido 2 | | Sudeste de Asia |
| Centro-sur de EE. UU. | Oeste de Europa |  | Este de Asia* |
| US Gov - Virginia | Suecia* | | |
| Oeste de EE. UU. 2 | | | |
| Oeste de EE. UU. 3 | | | |

\* Para obtener más información sobre la compatibilidad de las zonas de disponibilidad y los servicios disponibles en estas regiones, póngase en contacto con el representante de ventas o de clientes de Microsoft. Para las próximas regiones que van a admitir las zonas de disponibilidad, consulte [Zonas geográficas de Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Pasos siguientes

- [Compromiso de Microsoft para expandir las zonas de disponibilidad de Azure a más regiones](https://azure.microsoft.com/blog/our-commitment-to-expand-azure-availability-zones-to-more-regions/)
- [Servicios de Azure compatibles con las zonas de disponibilidad](az-region.md)
- [Servicios de Azure](region-types-service-categories-azure.md)
