---
title: Arquitectura de las cuentas de Purview y procedimientos recomendados
description: En este artículo se proporcionan ejemplos de arquitecturas de las cuentas de Azure Purview y se explican los procedimientos recomendados.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: 5e6faa2eb9556207aae7583044755bc9d01b0502
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721848"
---
# <a name="azure-purview-accounts-architectures-and-best-practices"></a>Arquitecturas de las cuentas de Azure Purview y procedimientos recomendados  

Azure Purview es una solución de gobernanza de datos unificada. Puede implementar una cuenta de Azure Purview para administrar de forma centralizada la gobernanza de datos en todo el patrimonio de datos, desde entornos locales hasta en la nube. Para usar Azure Purview como solución de gobernanza de datos centralizada, debe implementar una o varias cuentas de Purview dentro de la suscripción de Azure. Se recomienda mantener el número de instancias de Purview como mínimo; sin embargo, en algunos casos se necesitan más instancias de Purview para cumplir los requisitos de cumplimiento y seguridad empresarial.

## <a name="intended-audience"></a>Destinatarios

- Equipo de arquitectura de datos
- Equipos de administración y gobernanza de datos
- Equipo de seguridad de datos

## <a name="single-purview-account"></a>Cuenta única de Azure Purview

Considere la posibilidad de implementar el número mínimo de cuentas de Purview para toda la organización. Este enfoque aprovecha al máximo los "efectos de red" en los que el valor de la plataforma aumenta exponencialmente como una función de los datos que residen en la misma plataforma. 

Use la [jerarquía de colecciones de Azure Purview](./concept-best-practices-collections.md) para crear la estructura de administración de datos de la organización dentro de una sola cuenta de Purview. En este escenario, se implementa una cuenta de Purview en una suscripción de Azure. Los orígenes de datos de una o varias suscripciones de Azure se pueden registrar y examinar dentro de Azure Purview. También puede registrar y examinar orígenes de datos desde entornos locales o de varias nubes.

:::image type="content" source="media/concept-best-practices/accounts-single-account.png" alt-text="Captura de pantalla que muestra la única cuenta de Azure Purview."lightbox="media/concept-best-practices/accounts-single-account.png":::

## <a name="multiple-purview-accounts"></a>Varias cuentas de Purview

Algunas organizaciones pueden requerir la configuración de varias cuentas de Azure Purview. Revise los siguientes escenarios como algunos ejemplos al definir la arquitectura de las cuentas de Azure Purview:  

### <a name="testing-new-features"></a>Prueba de las características nuevas 

Se recomienda crear una nueva instancia de la cuenta de Purview al probar configuraciones de examen o clasificaciones en entornos aislados. En algunos escenarios, hay una característica de "control de versiones" en algunas áreas de la plataforma, como el glosario, pero sería más fácil tener una instancia "descartable" de Purview para probar libremente la funcionalidad esperada y, a continuación, planear la implementación de la característica en la instancia de producción.  

Además, considere la posibilidad de usar una cuenta de Purview de prueba cuando no se pueda realizar una reversión. Por ejemplo, actualmente no se puede quitar un atributo de término de glosario de una instancia de Purview una vez que se agrega a la cuenta de Purview. En este caso, se recomienda usar primero una cuenta de Purview de prueba.
 
### <a name="isolating-productionand-non-production-environments"></a>Aislamiento de entornos de producción y de no producción 

Considere la posibilidad de implementar instancias independientes de cuentas de Purview para entornos de desarrollo, pruebas y producción, especialmente cuando tenga instancias independientes de datos para cada entorno.  

En este escenario, los orígenes de datos de producción y de no producción se pueden registrar y examinar dentro de sus instancias de Purview correspondientes.

Opcionalmente, puede registrar un origen de datos en más de una instancia de Purview, si es necesario.

:::image type="content" source="media/concept-best-practices/accounts-multiple-accounts.png" alt-text="Captura de pantalla que muestra varias cuentas de Azure Purview basadas en entornos."lightbox="media/concept-best-practices/accounts-multiple-accounts.png":::

### <a name="fulfilling-compliance-requirements"></a>Cumplimiento de los requisitos de cumplimiento  

Al examinar orígenes de datos en Azure Purview, la información relacionada con los metadatos se ingiere y almacena dentro del Mapa de datos de Azure Purview en la región de Azure donde se implementa la cuenta de Purview. Considere la posibilidad de implementar instancias independientes de Azure Purview si tiene requisitos normativos y de cumplimiento específicos que incluyen incluso tener metadatos en una ubicación geográfica específica.  

Si su organización tiene datos en varias zonas geográficas y debe mantener los metadatos en la misma región que los datos reales, debe implementar varias instancias de Purview, una para cada geografía. En este caso, los orígenes de datos de cada región se deben registrar y examinar en la cuenta de Purview que se corresponda con la región del origen de datos o la geografía.

:::image type="content" source="media/concept-best-practices/accounts-multiple-regions.png" alt-text="Captura de pantalla que muestra varias cuentas de Azure Purview basadas en requisitos de cumplimiento."lightbox="media/concept-best-practices/accounts-multiple-regions.png":::

### <a name="having-data-sources-distributed-across-multiple-tenants"></a>Disposición de orígenes de datos distribuidos entre varios inquilinos  

Actualmente, Purview no admite servicios multiinquilino. Si tiene orígenes de datos de Azure distribuidos entre varias suscripciones de Azure en diferentes inquilinos de Azure Active Directory, se recomienda implementar cuentas de Azure Purview independientes en cada inquilino. 

Se aplica una excepción a los orígenes de datos basados en máquinas virtuales e inquilinos de Power BI. Para obtener más información sobre cómo examinar y registrar una cuenta entre inquilinos de Power BI en una sola cuenta de Purview, consulte [Registro y examen de una cuenta entre inquilinos Power BI](./register-scan-power-bi-tenant.md). 

:::image type="content" source="media/concept-best-practices/accounts-multiple-tenants.png" alt-text="Captura de pantalla que muestra varias cuentas de Azure Purview basadas en requisitos de servicios multiinquilino."lightbox="media/concept-best-practices/accounts-multiple-tenants.png"::: 

### <a name="billing-model"></a>Modelo de facturación 

Revise el [modelo de precios de Azure Purview](https://azure.microsoft.com/pricing/details/azure-purview) al definir el modelo de presupuestos y diseñar la arquitectura de Azure Purview para su organización. Se genera una facturación para una sola cuenta de Purview en la suscripción donde se implementa la cuenta de Purview. Este modelo también se aplica a otros costos de Purview, como el examen y la clasificación de metadatos dentro del Mapa de datos de Purview.

Algunas organizaciones tienen muchas unidades de negocio (BU) que operan de forma individual y, en algunos casos, no comparten la facturación entre sí. En esos casos, la organización acaba creando una instancia de Purview para cada BU. Este modelo no es el idóneo, pero puede ser necesario, especialmente porque las unidades de negocio no suelen estar dispuestas a compartir la facturación de Azure. 

Para obtener más información sobre el modelo de costos de informática en la nube en los modelos de contracargo y visualización de costos, consulte [¿Qué es la contabilidad en la nube?](/azure/cloud-adoption-framework/strategy/cloud-accounting).  

## <a name="additional-considerations-and-recommendations"></a>Consideraciones y recomendaciones adicionales 

- Mantenga bajo el número de cuentas de Purview para simplificar la sobrecarga administrativa. Si planea compilar varias cuentas de Purview, puede que necesite crear y administrar exámenes adicionales, el modelo de control de acceso, las credenciales y los entornos de ejecución en las cuentas de Purview. Además, es posible que tenga que administrar las clasificaciones y los términos del glosario para cada cuenta de Purview.

- Revise los requisitos financieros y de presupuesto. Si es posible, use el modelo de contracargo o de visualización de costos al usar los servicios de Azure y divida el costo de Azure Purview entre la organización para mantener el número mínimo de cuentas de Purview. 

- Use [colecciones de Azure Purview](concept-best-practices-collections.md) para definir el control de acceso a metadatos dentro del Mapa de datos de Azure Purview para los usuarios empresariales, los equipos de gobernanza y administración de datos de su organización. Para más información, consulte [Control de acceso en Azure Purview](./catalog-permissions.md).

- Revise los [límites de Azure Purview](./how-to-manage-quotas.md#azure-purview-limits) antes de implementar las nuevas cuentas de Purview. Actualmente, el límite predeterminado de cuentas de Purview por región, por inquilino (todas las suscripciones combinadas) es 3. Es posible que tenga que ponerse en contacto con el soporte técnico de Microsoft para aumentar este límite en su suscripción o inquilino antes de implementar instancias adicionales de Azure Purview.  

- Revise los [requisitos previos de Azure Purview](./create-catalog-portal.md#prerequisites) antes de implementar nuevas cuentas de Purview en su entorno.
  
## <a name="next-steps"></a>Pasos siguientes
-  [Creación de una cuenta de Purview](./create-catalog-portal.md)
