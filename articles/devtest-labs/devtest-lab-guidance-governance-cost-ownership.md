---
title: Administración de costos y propiedad
description: En este artículo se proporciona información que ayudará a optimizar el costo y alinear la propiedad en su entorno.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: a62ba95203ffea3162f7e79360bd36eb0f8f9733
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397610"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Gobernanza de la infraestructura de Azure DevTest Labs: administrar costo y propiedad
El costo y la propiedad son las principales preocupaciones cuando considere la posibilidad de crear los entornos de desarrollo y pruebas. En esta sección, encontrará información que le ayudará a optimizar el costo y alinear la propiedad en su entorno.

## <a name="optimize-for-cost"></a>Optimizar el costo

### <a name="question"></a>Pregunta
¿Cómo puedo optimizar el costo dentro de mi entorno de DevTest Labs?

### <a name="answer"></a>Respuesta
Ha varias características integradas de DevTest Labs que ayudan a optimizar el costo. Consulte los artículos sobre [administración de costos, umbrales](devtest-lab-configure-cost-management.md)[y directivas](devtest-lab-set-lab-policy.md) para limitar las actividades de los usuarios. 

Si usa DevTest Labs con cargas de trabajo de desarrollo y pruebas, puede considerar la posibilidad de usar la [ventaja de la suscripción Desarrollo/pruebas - Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/), que forma parte del Contrato Enterprise. O bien, si es cliente de Pago por uso, es posible que le interese la [oferta de Pago por uso de Desarrollo/pruebas](https://azure.microsoft.com/offers/ms-azr-0023p/).

Este enfoque proporciona varias ventajas:

- Tarifas especiales de Desarrollo/pruebas más económicas en instancias de máquinas virtuales con Windows, Cloud Services, HDInsight, App Service y Logic Apps
- Tarifas geniales del Contrato Enterprise (EA) en otros servicios de Azure
- Acceso a imágenes exclusivas de Desarrollo y pruebas de la galería, que incluye Windows 8.1 y Windows 10
 
Solo los suscriptores activos de Visual Studio (suscripciones estándar, suscripciones de nube anuales y mensuales) pueden usar los recursos de Azure que se ejecutan en una suscripción Desarrollo/pruebas - Enterprise. Pero los usuarios finales pueden acceder a la aplicación para aportar comentarios o hacer pruebas de aceptación. Puede usar recursos de esta suscripción solo para desarrollar y probar aplicaciones. No hay ninguna garantía de tiempo de actividad.

Si decide usar la oferta de Desarrollo/pruebas, emplee esta ventaja únicamente para el desarrollo y las pruebas de las aplicaciones. El uso conforme a la suscripción no conlleva ningún Acuerdo de Nivel de Servicio con respaldo financiero, salvo para el uso de Azure DevOps y HockeyApp.

## <a name="define-role-based-access-across-your-organization"></a>Definición de acceso basado en rol en la organización
### <a name="question"></a>Pregunta
¿Cómo defino el control de acceso basado en rol de Azure para mis entornos de DevTest Labs a fin de asegurarme de que TI puede conservar el control mientras los desarrolladores o evaluadores hacen su trabajo? 

### <a name="answer"></a>Respuesta
Hay un amplio patrón, pero el detalle depende de la organización.

El departamento de TI central debe poseer solo lo que sea necesario y permitir que los equipos de proyectos y aplicaciones tengan el nivel necesario de control. Normalmente, esto significa que esa departamento de TI central posee la suscripción y las controla las funciones de TI básicas, como las configuraciones de redes. El conjunto de **propietarios** para una suscripción debe ser pequeño. Estos propietarios pueden designar a otros propietarios cuando sea necesario, o aplicar directivas de nivel de suscripción, por ejemplo, “sin dirección IP pública”.

Puede haber un subconjunto de usuarios que requieren acceso a través de una suscripción, como la compatibilidad con Tier1 o Tier2. En este caso, se recomienda dar a dichos usuarios acceso de **colaborador** para que puedan administrar los recursos, pero no puedan proporcionar acceso de usuario o ajustar las directivas.

Los propietarios de recursos de DevTest Labs deben estar cercanos al equipo del proyecto o la aplicación. Estos propietarios comprenden los requisitos de máquina y software. En la mayoría de las organizaciones, el propietario del recurso de DevTest Labs es el responsable de desarrollo o del proyecto. Este propietario puede administrar usuarios y directivas en el entorno de laboratorio, además de todas las máquinas virtuales del entorno de DevTest Labs.

Agregue miembros del equipo de proyecto y aplicación al rol Usuarios de DevTest Labs. Estos usuarios pueden crear máquinas virtuales de conformidad con las directivas de nivel de suscripción y de laboratorio. Los usuarios también pueden administrar sus propias máquinas virtuales, pero no las que pertenecen a otros usuarios.

Para obtener más información, vea [Scaffolding empresarial de Azure: gobernanza de suscripción prescriptiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


## <a name="next-steps"></a>Pasos siguientes
Vea [Corporate policy and compliance](devtest-lab-guidance-governance-policy-compliance.md) (Cumplimiento y directiva corporativa).
