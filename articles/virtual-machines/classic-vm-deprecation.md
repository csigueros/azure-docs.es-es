---
title: Vamos a retirar las máquinas virtuales (clásicas) de Azure el 1 de marzo de 2023
description: En este artículo se proporciona información general de alto nivel sobre la retirada de máquinas virtuales creadas con el modelo de implementación clásica.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: eee11a2f50dbc0d3121d3ba603e85d3700e333bb
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294013"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migre los recursos de IaaS a Azure Resource Manager antes del 1 de marzo de 2023. 

En 2014, lanzamos la infraestructura como servicio (IaaS) en [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Desde entonces, hemos mejorado las funcionalidades. Dado que Azure Resource Manager tiene ahora funcionalidades completas de IaaS y otras mejoras, hemos puesto en desuso las máquinas virtuales (VM) de IaaS mediante [Azure Service Manager](/azure/virtual-machines/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) el 28 de febrero de 2020. Esta funcionalidad se retirará por completo el 1 de marzo de 2023. 

En la actualidad, aproximadamente el 90 por ciento de las máquinas virtuales de IaaS usan Azure Resource Manager. Si usa recursos de IaaS a través de ASM, empiece a planear la migración ahora. Complétela antes del 1 de marzo de 2023 para aprovechar [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Las máquinas virtuales creadas con el modelo de implementación clásica seguirán la [directiva de ciclo de vida moderno ](https://support.microsoft.com/help/30881/modern-lifecycle-policy) para su retirada.

## <a name="how-does-this-affect-me"></a>¿Cómo me afecta esto? 

- A partir del 28 de febrero de 2020, los clientes que no hayan usado máquinas virtuales de IaaS a través de ASM en el mes de febrero de 2020 ya no podrán crear máquinas virtuales (clásicas). 
- El 1 de marzo de 2023, los clientes ya no podrán iniciar máquinas virtuales de IaaS con ASM. Todo lo que todavía esté en ejecución o asignado se detendrá y desasignará. 
- El 1 de marzo de 2023, se informará a las suscripciones que no se migran a Azure Resource Manager acerca de nuestras escalas de tiempo para eliminar las máquinas virtuales (clásicas) restantes.  

Esta retirada *no* afecta a los siguientes servicios y funcionalidades de Azure: 
- [Azure Cloud Services (clásico)](../cloud-services/cloud-services-choose-me.md)
- Las máquinas virtuales (clásicas) *no* usan las cuentas de almacenamiento. 
- Las máquinas virtuales (clásicas) *no* usan las redes virtuales. 
- Otros recursos clásicos

## <a name="what-resources-are-available-for-this-migration"></a>¿Qué recursos están disponibles para esta migración?

- [Microsoft Q&A](/answers/topics/azure-virtual-machines-migration.html): Soporte técnico de Microsoft y de la comunidad para la migración

- [Soporte técnico para la migración de Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): equipo de soporte técnico dedicado para brindar asistencia técnica durante la migración. Los clientes sin soporte técnico pueden usar la [funcionalidad de soporte técnico gratuita](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%0A%20%20%20%20%22pesId%22%3A%20%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0A%20%20%20%20%22supportTopicId%22%3A%20%22794bb734-af1b-e2d5-a757-dac7438009ab%22%2C%0A%20%20%20%20%22contextInfo%22%3A%20%22Migrate%20IAAS%20resources%20from%20Classic%20%28ASM%29%20to%20Azure%20Resource%20Manager%20%28ARM%29%22%2C%0A%20%20%20%20%22caller%22%3A%20%22NoSupportPlanASM2ARM%22%2C%0A%20%20%20%20%22severity%22%3A%20%222%22%0A%7D) que se proporciona específicamente para esta migración. 

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack): Fast Track puede ayudar a los clientes que reúnan los requisitos a planear y ejecutar esta migración. [Solicite su inclusión](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0) en el programa de migración de DC.  

- Si su empresa u organización se ha asociado con Microsoft o trabaja con un representante de Microsoft (como arquitecto de soluciones en la nube [CSA] o administradores técnicos de cuentas [TAM]), trabaje en conjunto con ellos para obtener recursos adicionales para la migración.

## <a name="what-actions-should-i-take"></a>¿Qué medidas debo tomar? 

Empiece ya a planear la migración a Azure Resource Manager. 

1. Haga una lista de todas las máquinas virtuales afectadas: 

   - Las máquinas virtuales de tipo **máquinas virtuales (clásicas)** en el [panel Máquinas virtuales de Azure Portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) son todas las máquinas virtuales afectadas dentro de la suscripción. 
   - También puede realizar consultas en Azure Resource Graph mediante el [portal](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) o [PowerShell](../governance/resource-graph/concepts/work-with-data.md) para ver la lista de todas las máquinas virtuales (clásicas) marcadas y su información relacionada para las suscripciones seleccionadas. 
   - El 8 de febrero y el 2 de septiembre de 2020 enviamos correos electrónicos con el asunto "Empezar a planear la migración de máquinas virtuales de IaaS a Azure Resource Manager" a los propietarios de la suscripción. El correo electrónico proporciona una lista de todas las suscripciones y máquinas virtuales (clásicas) que contiene. Úselas para crear esta lista. 

1. [Más información](./migration-classic-resource-manager-overview.md) sobre cómo migrar las máquinas virtuales (clásicas) [Linux](./migration-classic-resource-manager-plan.md) y [Windows](./migration-classic-resource-manager-plan.md) a Azure Resource Manager. Para más información, consulte [Preguntas más frecuentes sobre la migración del método clásico al de Azure Resource Manager](./migration-classic-resource-manager-faq.yml).

1. Se recomienda iniciar el planeamiento con la [herramienta de migración de compatibilidad de la plataforma](./migration-classic-resource-manager-overview.md) para migrar las máquinas virtuales existentes en tres sencillos pasos: validar, preparar y confirmar. La herramienta está diseñada para migrar las máquinas virtuales sin apenas tiempo de inactividad o ninguno en absoluto. 

   - El primer paso, validación, no afecta de ningún modo a la implementación existente y proporciona una lista de todos los escenarios de migración no admitidos. 
   - Examine la [lista de soluciones alternativas](./migration-classic-resource-manager-overview.md#unsupported-features-and-configurations) para corregir su implementación y prepararla para la migración. 
   - Lo normal es que después de corregir todos los errores de validación no se encuentre con ningún problema durante los pasos de preparación y confirmación. Después de que la confirmación se realice correctamente, la implementación se migra en vivo a Azure Resource Manager y, a partir de entonces, se puede administrar con las nuevas API que Azure Resource Manager expone. 

   Si la herramienta de migración no es adecuada para su migración, puede explorar [otras ofertas de proceso](/azure/architecture/guide/technology-choices/compute-decision-tree) para la migración. Dado que hay muchas ofertas de proceso de Azure, y son muy diferentes entre sí, no podemos proporcionar un método de migración admitido por la plataforma para estas ofertas.  

1. Para consultar preguntas técnicas y problemas, y obtener ayuda para agregar suscripciones a la lista de permitidos, [póngase en contacto con el equipo de soporte técnico](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

1. Complete la migración lo antes posible para evitar el impacto en el negocio y aproveche el rendimiento mejorado, la seguridad y las características nuevas de Azure Resource Manager.