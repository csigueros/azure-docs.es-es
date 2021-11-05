---
title: Administración de la confiabilidad y el rendimiento con suscripciones de desarrollo y pruebas de Azure
description: Cree confiabilidad en las aplicaciones con suscripciones de desarrollo y pruebas.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: fe34eb3b5fff32ab59c72c90041b095a82806e9b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093752"
---
# <a name="reliability-management"></a>Administración de confiabilidad  

Aunque los servicios de producción no estarán en una suscripción de desarrollo y pruebas, puede usar otras fases de la suscripción de desarrollo y pruebas de Azure para garantizar la confiabilidad en producción.  

Al usar las suscripciones de desarrollo y pruebas de su organización, tiene que decidir cómo va a:  

- datos de control  
- Control de seguridad y acceso  
- Administrar el tiempo de actividad de ese sistema de producción  

Normalmente, hay distintas fases de implementación que se pasan antes de la producción: compartido, control de calidad, integración, almacenamiento provisional y conmutación por error. Dependiendo de cómo defina su empresa estas fases, puede cambiar el uso de una suscripción de desarrollo/pruebas de la organización.  

Si ejecuta servicios críticos, como las aplicaciones orientadas al cliente, no use una suscripción de desarrollo y pruebas. Las suscripciones de desarrollo y pruebas no tienen un Acuerdo de Nivel de Servicio con respaldo financiero. Estas suscripciones son para pruebas y desarrollo de producción previa.  

## <a name="site-reliability-engineering-sre"></a>Ingeniería de confiabilidad de sitios (SRE)  

Para obtener más información sobre la administración y la ingeniería de confiabilidad, considere la administración de la confiabilidad de sitios, una materia de ingeniería dedicada a ayudar a las organizaciones a lograr de forma sostenible la confiabilidad adecuada en sus sistemas, servicios y productos.  

En el campo, la diferencia entre SRE y DevOps aún está en discusión. Entre las diferencias que se han acordado ampliamente se incluyen:  

- SRE es una materia de ingeniería centrada en la confiabilidad. DevOps es un movimiento cultural que surgió de la necesidad de dividir los silos asociados a las organizaciones de desarrollo y operaciones.  
- SRE puede ser el nombre de un rol como en "Soy un ingeniero de confiabilidad de sitios (SRE)". DevOps no.  
- SRE tiende a ser prescriptiva. DevOps intencionadamente no. La adopción casi universal de la integración continua y la entrega continua, y los principios de Agile son los DevOps más cercanos.  

Si desea obtener más información sobre la práctica de SRE, consulte estos vínculos:  

- [SRE en contexto](/learn/modules/intro-to-site-reliability-engineering/3-sre-in-context.md)  
- [Principios y prácticas clave de SRE: ciclos virtuosos](/learn/modules/intro-to-site-reliability-engineering/4-key-principles-1-virtuous-cycles.md)  
- [Principios y prácticas clave de SRE: el lado humano de SRE](/learn/modules/intro-to-site-reliability-engineering/5-key-principles-2-human-side-of-sre.md)  
- [Introducción a SRE](/learn/modules/intro-to-site-reliability-engineering/6-getting-started.md)  

## <a name="service-level-agreements"></a>Acuerdos de Nivel de Servicio  

El Desarrollo/pruebas - Enterprise es exclusivamente para el desarrollo y las pruebas de las aplicaciones. El uso de la suscripción no tiene un Acuerdo de Nivel de Servicio con respaldo financiero.  

## <a name="learn-to-use-different-types-of-devtest-subscriptions"></a>Aprenda a usar diferentes tipos de suscripciones de desarrollo y pruebas  

Tanto si necesita [créditos mensuales de Azure para suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), [suscripciones de Desarrollo/pruebas - Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) o [una suscripción de Desarrollo/pruebas - Pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/) (PAYG), puede encontrar fácilmente ofertas que funcionen para usuarios individuales o un equipo.  

## <a name="managing-individual-credit-subscriptions"></a>Administración de suscripciones de crédito individuales  

Los crédito de Azure de Visual Studio son una ventaja individual para el desarrollo y pruebas individuales y el desarrollo de bucles internos. No se pueden agrupar créditos entre desarrolladores. Las suscripciones de crédito siguen siendo suscripciones de Azure, pero una oferta específica de Azure. Administre las suscripciones de crédito de la misma manera que administra otras suscripciones de Azure para que pueda trabajar en grupos y equipos. Puede quitar los límites de gasto individuales con una tarjeta de crédito o si su suscripción de desarrollo/pruebas empresarial va al método de adquisición elegido por la empresa.  

Las actividades de bucle interno para desarrolladores a menudo usan créditos, pero luego cambian a suscripciones de desarrollo/pruebas de Azure empresariales u organizaciones, incluido el pago por uso. De este modo, a medida que sigue los procesos DevOps, puede realizar un bucle interno con su suscripción de crédito individual. En el bucle externo del DevOps, los destinos que no son de producción están en desarrollo/pruebas empresariales: prod va a prod.  

Administre las suscripciones de crédito, las suscripciones de desarrollo/pruebas empresariales y las suscripciones de pago por uso y segmente a los desarrolladores mediante [grupos de administración](../../governance/management-groups/how-to/protect-resource-hierarchy.md), cada uno debe tener una jerarquía única.  

## <a name="using-your-organization-azure-devtest-offers"></a>Uso de ofertas de desarrollo y pruebas de Azure para su organización  

Si necesita una suscripción a Desarrollo/pruebas de Azure para organización, tiene dos ofertas entre las que elegir.  

- [Desarrollo y pruebas de pago por uso (PAYG) (0023P)](https://azure.microsoft.com/offers/ms-azr-0023p/-)  
- [Desarrollo/pruebas - Enterprise (0148P)](https://azure.microsoft.com/offers/ms-azr-0148p/)  

Cada uno incluye su propio conjunto de descuentos y requieren una Visual Studio Subscription.  

Cada oferta de suscripción le permite poner en marcha su equipo con entornos de desarrollo y pruebas en la nube mediante máquinas virtuales preconfiguradas. Cree varias suscripciones de Azure y adminístrelas desde una cuenta. Puede mantener entornos aislados y una factura independiente para diferentes proyectos o equipos.  

Las suscripciones de Desarrollo/pruebas - Enterprise requieren un Contrato Enterprise (EA). Las suscripciones de Desarrollo/pruebas - Pago por uso no requieren un Contrato Enterprise, pero se pueden usar con una cuenta de acuerdo empresarial.  

## <a name="why-would-i-use-payg-offers-vs-enterprise-devtest-offers"></a>¿Por qué usar las ofertas de Pago por uso frente a Desarrollo/pruebas - Enterprise?  

Una oferta de Desarrollo/pruebas de Pago por uso podría ser la opción adecuada para usarla como suscriptor de Visual Studio. A diferencia de las suscripciones de crédito para uso individual, las ofertas de Pago por uso son excelentes para el desarrollo en equipo y permiten tener varios usuarios dentro de una suscripción. Una oferta de Desarrollo/pruebas de Pago por uso podría ser adecuada para usted si:  

- No tiene un acuerdo empresarial. En este caso, solo puede crear una cuenta de pago por uso con una licencia de Visual Studio.  
- Está creando un acuerdo empresarial, pero debe configurar una suscripción que no use el acuerdo de su organización. Puede tener un proyecto único que requiera su propia suscripción o crear un entorno aislado facturado por separado para proyectos o equipos.  
- Prefiere mantener las identidades aisladas. Es posible que necesite que determinadas identidades permanezcan separadas de otras para proteger el acceso a datos, recursos y aplicaciones.  
