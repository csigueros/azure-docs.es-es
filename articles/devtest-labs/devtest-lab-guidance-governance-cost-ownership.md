---
title: Administración de costos y propiedad
description: En este artículo se proporciona información que ayudará a optimizar el costo y alinear la propiedad en su entorno.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: cc3b607155655935ee1e77d82ca60b7f92b70c2a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623615"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Gobernanza de la infraestructura de Azure DevTest Labs: administrar costo y propiedad
El costo y la propiedad son las principales preocupaciones cuando considere la posibilidad de crear los entornos de desarrollo y pruebas. En esta sección, encontrará información que le ayudará a optimizar el costo y alinear la propiedad en su entorno.

## <a name="optimize-for-cost"></a>Optimizar el costo

### <a name="question"></a>Pregunta
¿Cómo puedo optimizar el costo dentro de mi entorno de DevTest Labs?

### <a name="answer"></a>Respuesta
Hay una serie de características integradas de DevTest Labs que le ayudarán a optimizar el costo. Consulte los artículos sobre [administración de costos, umbrales](devtest-lab-configure-cost-management.md)[y directivas](devtest-lab-set-lab-policy.md) para limitar las actividades de los usuarios. 

Cuando utilice DevTest Labs para cargas de trabajo de desarrollo y pruebas, puede considerar la posibilidad de utilizar el [Beneficio de suscripción Desarrollo/pruebas de Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) como parte de su contrato Enterprise. Como alternativa, si es un cliente de pago por uso, es posible que le interese la [oferta de pago por uso de DevTest](https://azure.microsoft.com/offers/ms-azr-0023p/).

Este enfoque le proporciona numerosas ventajas:

- Tarifas especiales de Desarrollo/pruebas más económicas en instancias de máquinas virtuales con Windows, Cloud Services, HDInsight, App Service y Logic Apps
- Tarifas geniales del Contrato Enterprise (EA) en otros servicios de Azure
- Acceso a imágenes exclusivas de Desarrollo y pruebas de la galería, que incluye Windows 8.1 y Windows 10
 
Solo los suscriptores activos de Visual Studio (suscripciones estándar, suscripciones de nube anuales y mensuales) pueden usar los recursos de Azure que se ejecutan en una suscripción Desarrollo/pruebas - Enterprise. No obstante, los usuarios finales pueden acceder también a la aplicación para aportar comentarios o hacer pruebas de aceptación. El uso de recursos en esta suscripción se restringe al desarrollo y la realización de pruebas de aplicaciones, y no se ofrece ninguna garantía sobre el tiempo de actividad.

Si decide usar la oferta de DevTest, tenga en cuenta que este beneficio es únicamente para desarrollo y pruebas de sus aplicaciones. El uso en el marco de la suscripción no incluye un Acuerdo de Nivel de Servicio con respaldo financiero, salvo para la utilización de Azure DevOps y HockeyApp.

## <a name="define-role-based-access-across-your-organization"></a>Definición de acceso basado en rol en la organización
### <a name="question"></a>Pregunta
¿Cómo defino el control de acceso basado en rol de Azure para mis entornos de DevTest Labs a fin de asegurarme de que TI puede conservar el control mientras los desarrolladores o evaluadores hacen su trabajo? 

### <a name="answer"></a>Respuesta
Hay un amplio modelo, sin embargo, habrá detalles específicos que dependerán de su organización.

El departamento de TI central debe poseer solo lo que es necesario y permitir que los equipos del proyecto y la aplicación tengan el nivel necesario de control. Normalmente, esto significa que esa departamento de TI central posee la suscripción y las controla las funciones de TI básicas, como las configuraciones de redes. El conjunto de **propietarios** para una suscripción debe ser pequeño. Estos propietarios pueden designar a propietarios adicionales cuando sea necesario, o aplicar directivas de nivel de suscripción, por ejemplo “sin dirección IP pública”.

Puede haber un subconjunto de usuarios que requieren acceso a través de una suscripción, como la compatibilidad con Tier1 o Tier2. En este caso, se recomienda dar a dichos usuarios acceso de **colaborador** para que puedan administrar los recursos, pero no puedan proporcionar acceso de usuario o ajustar las directivas.

Los propietarios del recurso de DevTest Labs deben estar cerca del equipo de proyecto o aplicación. De esta forma entenderán sus requisitos en cuanto a las máquinas y el software necesario. En la mayoría de las organizaciones, el propietario de este recurso de DevTest Labs suele ser el responsable de desarrollo o del proyecto. Este propietario puede administrar los usuarios y las directivas en el entorno de laboratorio y puede administrar todas las máquinas virtuales en el entorno de DevTest Labs.

Los miembros del equipo de proyecto o aplicación se deben agregar al rol de usuarios de DevTest Labs. Estos usuarios pueden crear máquinas virtuales (en línea con las directivas de nivel de suscripción y de laboratorio). También pueden administrar sus propias máquinas virtuales. No pueden administrar las máquinas virtuales que pertenecen a otros usuarios.

Para obtener más información vea la documentación de [Scaffold empresarial de Azure: gobernanza de suscripción prescriptiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


## <a name="next-steps"></a>Pasos siguientes
Vea [Corporate policy and compliance](devtest-lab-guidance-governance-policy-compliance.md) (Cumplimiento y directiva corporativa).
