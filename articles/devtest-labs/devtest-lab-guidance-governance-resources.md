---
title: Gobernanza de la infraestructura de Azure DevTest Labs
description: Este artículo aborda la alineación y la administración de recursos de Azure DevTest Labs dentro de su organización.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: b279047b46e7e9abbc3198a62dc2b86d2c68d424
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652384"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Gobernanza de la infraestructura de Azure DevTest Labs: recursos
Este artículo aborda la alineación y la administración de recursos de DevTest Labs dentro de su organización. 

## <a name="align-within-an-azure-subscription"></a>Alineación dentro de una suscripción de Azure 

### <a name="question"></a>Pregunta
¿Cómo se pueden alinear recursos de DevTest Labs dentro de una suscripción de Azure?

### <a name="answer"></a>Respuesta
Antes de que una organización empiece a usar Azure para desarrollo de aplicaciones generales, los planificadores de TI deben revisar cómo introducir la funcionalidad como parte de su cartera de servicios global. Las áreas de revisión deben resolver las siguientes cuestiones:

- ¿Cómo medir el costo asociado con el ciclo de vida de desarrollo de las aplicaciones?
- ¿Cómo alinea la organización la oferta de servicio propuesta con la directiva de seguridad corporativa? 
- ¿La segmentación es necesaria para separar los entornos de producción y desarrollo? 
- ¿Qué controles se introducen para facilitar la administración, la estabilidad y el crecimiento a largo plazo?

La **primera práctica recomendada** consiste en revisar la taxonomía de Azure de las organizaciones donde se describen las divisiones entre las suscripciones de desarrollo y producción. En el diagrama siguiente, la taxonomía sugerida permite una separación lógica de los entornos de desarrollo y pruebas y producción. Con este enfoque, una organización puede introducir códigos de facturación para realizar un seguimiento de los costos asociados con cada entorno por separado. Para más información, vea [Gobernanza de suscripción prescriptiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Además, puede usar [etiquetas de Azure](../azure-resource-manager/management/tag-resources.md) para organizar recursos para fines de facturación y seguimiento.

La **segunda práctica recomendada** consiste en habilitar la suscripción a DevTest dentro del portal de Azure Enterprise. Permite que una organización ejecute sistemas operativos cliente que no están disponibles normalmente en una suscripción de Azure Enterprise. A continuación, use software empresarial donde solo paga por el proceso sin tener que preocuparse por las licencias. Esto garantiza que la facturación por servicios designados, incluidas las imágenes de la galería en IaaS, como Microsoft SQL Server, se base solo en el consumo. Puede encontrar detalles sobre la suscripción de Azure DevTest [aquí](https://azure.microsoft.com/offers/ms-azr-0148p/) para los clientes con Contrato Enterprise (EA) y [aquí](https://azure.microsoft.com/offers/ms-azr-0023p/) para los clientes de pago por uso.

![Alineación de los recursos con las suscripciones](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Este modelo proporciona a una organización la flexibilidad para implementar Azure DevTest Labs a escala. Una organización puede admitir cientos de laboratorios para diversas unidades de negocio con la ejecución en paralelo de 100 a 1000 máquinas virtuales. Promociona la noción de una solución de laboratorio empresarial centralizada que puede compartir los mismos principios de administración de configuración y controles de seguridad.

Este modelo también garantiza que la organización no agote sus límites de recursos asociados con su suscripción de Azure. Para obtener información detallada sobre suscripciones y límites de servicio, vea [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-resource-manager/management/azure-subscription-service-limits.md). El proceso de aprovisionamiento de DevTest Labs puede consumir una gran cantidad de grupos de recursos. Puede solicitar un aumento de los límites con una solicitud de soporte técnico en la suscripción de Azure DevTest. El aumento del uso de la suscripción de desarrollo no afecta a los recursos incluidos en la suscripción de producción. Para más información sobre el escalado de DevTest Labs, vea [Escalado de cuotas y límites en DevTest Labs](devtest-lab-scale-lab.md).

Un límite a nivel de suscripción común que debe tenerse en cuenta es cómo se realizan las asignaciones de intervalos IP de red para que admitan suscripciones de desarrollo y producción. Deben tenerse en cuenta estas asignaciones para el crecimiento a lo largo del tiempo (suponiendo la conectividad local u otra topología de red que requiere la empresa para administrar su pila de red en lugar de usar como opción predeterminada la implementación de Azure). La práctica recomendada es tener algunas redes virtuales que tengan un prefijo de dirección IP grande asignado y dividido con muchas subredes grandes, en lugar de disponer de varias redes virtuales con subredes pequeñas. Por ejemplo, con 10 suscripciones, puede definir 10 redes virtuales (una para cada suscripción). Todos los laboratorios que no requieran el aislamiento pueden compartir la misma subred en la red virtual de la suscripción.

## <a name="maintain-naming-conventions"></a>Mantenimiento de las convenciones de nomenclatura

### <a name="question"></a>Pregunta
¿Cómo se puede mantener una convención de nomenclatura en un entorno de DevTest Labs?

### <a name="answer"></a>Respuesta
Es posible que desee ampliar las convenciones de nomenclatura empresariales actuales para las operaciones de Azure y que sean coherentes en todo el entorno de DevTest Labs.

Al implementar DevTest Labs, se recomienda que tenga directivas específicas de partida. Implemente estas directivas mediante plantillas JSON y un script central para garantizar la coherencia. Las directivas de nomenclatura se pueden implementar mediante directivas de Azure aplicadas a nivel de suscripción. Para consultar ejemplos de JSON para Azure Policy, vea [Ejemplos de Azure Policy](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Número de usuarios por laboratorio y laboratorios por organización

### <a name="question"></a>Pregunta 
¿Cómo se puede determinar la proporción de usuarios por laboratorio y el número total de laboratorios necesarios en toda la organización?

### <a name="answer"></a>Respuesta
Se recomienda que las unidades de negocio y los grupos de desarrollo que están asociados con el mismo proyecto de desarrollo estén asociados con el mismo laboratorio. Esto permite aplicar a ambos grupos los mismos tipos de directivas, imágenes y directivas de cierre. 

También debe tener en cuenta los límites geográficos. Por ejemplo, los desarrolladores del Nordeste de Estados Unidos (EE. UU.) pueden usar un laboratorio aprovisionado en el Este de EE. UU. 2. Además, los desarrolladores de Dallas (Texas) y Denver (Colorado) pueden ser remitidos a usar un recurso en Centro y Sur de EE. UU. Si existe algún trabajo de colaboración con un tercero externo, se les podría asignar a un laboratorio que no utilizan los desarrolladores internos. 

También puede usar un laboratorio para un proyecto específico en los proyectos de Azure DevOps. A continuación, aplique seguridad en un grupo específico de Azure Active Directory, lo que permite acceder a ambos conjuntos de recursos. La red virtual asignada al laboratorio puede ser otro límite para consolidar a los usuarios.

## <a name="deletion-of-resources"></a>Eliminación de recursos

### <a name="question"></a>Pregunta
¿Cómo se puede evitar la eliminación de recursos de un laboratorio?

### <a name="answer"></a>Respuesta
Se recomienda establecer los permisos adecuados a nivel de laboratorio para que solo los usuarios autorizados puedan eliminar los recursos o cambiar las directivas del laboratorio. Los desarrolladores deben incorporarse al grupo de **usuarios de DevTest Labs**. El jefe de desarrollo o el responsable de la infraestructura debe ser el **propietario de DevTest Labs**. Se recomienda que haya solo dos propietarios del laboratorio. Esta directiva se extiende al repositorio de código para evitar daños. Los usuarios del laboratorio tienen derechos para usar los recursos, pero no pueden actualizar las directivas del laboratorio. Consulte el artículo siguiente en el que se enumeran los roles y derechos que cada grupo integrado tiene dentro de un laboratorio: [Adición de propietarios y usuarios en Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Mover el laboratorio a otro grupo de recursos 

### <a name="question"></a>Pregunta
¿Se permite mover un laboratorio a otro grupo de recursos?

### <a name="answer"></a>Respuesta
Sí. Vaya a la página del grupo de recursos desde la página principal del laboratorio. A continuación, seleccione **Mover** en la barra de herramientas y seleccione el laboratorio que desea mover a otro grupo de recursos. Cuando se crea un laboratorio, se crea automáticamente un grupo de recursos. Sin embargo, desea mover el laboratorio a otro grupo de recursos que sigue las convenciones de nomenclatura de la empresa. 

## <a name="next-steps"></a>Pasos siguientes
Vea [Administración de costos y propiedad](devtest-lab-guidance-governance-cost-ownership.md).
