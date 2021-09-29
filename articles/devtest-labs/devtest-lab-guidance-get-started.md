---
title: Escenarios conocidos de uso de Azure DevTest Labs
description: Este artículo proporciona los escenarios principales de uso de Azure DevTest Labs y dos rutas generales para empezar a usar el servicio en su organización.
ms.topic: conceptual
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: fc4c7ca1cdbc8e503ad6c0d94fcec2d9697baae4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657488"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Escenarios conocidos de uso de Azure DevTest Labs
Según las necesidades de la empresa, DevTest Labs puede configurarse para satisfacer requisitos diferentes.  En este artículo se tratan los escenarios más habituales. Cada escenario aborda las ventajas del uso de DevTest Labs y los recursos para implementar estos escenarios.  

- Equipos de escritorio del desarrollador
- Entornos de prueba
- Sesiones de aprendizaje, laboratorios prácticos y hackathons
- Investigaciones en espacio aislado
- Laboratorios educativos

## <a name="developer-desktops"></a>Equipos de escritorio del desarrollador
Los desarrolladores a menudo tienen requisitos diferentes para las máquinas de desarrollo de proyectos diferentes. Con DevTest Labs, los desarrolladores pueden tener acceso a máquinas virtuales a petición configuradas para que se adapten a sus escenarios más comunes. DevTest Labs ofrece las siguientes ventajas:

- Las organizaciones pueden proporcionar máquinas comunes de desarrollo para garantizar la coherencia entre los equipos.
- Los desarrolladores pueden aprovisionar rápidamente las máquinas de desarrollo a petición o [solicitar una máquina existente preconfigurada](devtest-lab-add-claimable-vm.md).
- Los desarrolladores pueden aprovisionar recursos mediante autoservicio sin necesidad de tener permisos a nivel de suscripción.
- El departamento de TI o los administradores pueden [definir previamente la topología de red](devtest-lab-configure-vnet.md), y los desarrolladores pueden usarla directamente de una forma fácil e intuitiva sin requerir ningún acceso especial.
- Los desarrolladores pueden [personalizar](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) fácilmente las máquinas de desarrollo, según proceda.
- Los administradores pueden controlar los costos garantizando que:
    - Los desarrolladores [no pueden obtener más máquinas virtuales](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) de las que necesitan para el desarrollo.
    - [Las máquinas virtuales se apagan](devtest-lab-set-lab-policy.md#set-auto-shutdown) cuando no están en uso.
    - [Se admite solo un subconjunto de tamaños de instancia de máquina virtual](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) para cargas de trabajo específicas.
    - [Se administran los objetivos de costos y notificaciones](devtest-lab-configure-cost-management.md) para cada laboratorio.

Para obtener más información, consulte [Uso de Azure DevTest Labs para desarrolladores](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Entornos de prueba
Crear y administrar entornos de prueba en una empresa puede requerir un esfuerzo considerable. Con DevTest Labs, los entornos de prueba se pueden crear, actualizar o duplicar con facilidad. Permite que los equipos tengan acceso a un entorno completamente configurado cuando sea necesario. En este escenario, DevTest Labs ofrece las siguientes ventajas:

- Las organizaciones pueden proporcionar entornos comunes de pruebas para garantizar la coherencia entre los equipos.
- Los evaluadores pueden probar la última versión de la aplicación aprovisionando rápidamente entornos de Windows y Linux mediante plantillas reutilizables.
- Los administradores pueden conectar el laboratorio a Azure DevOps para habilitar escenarios de DevOps.
- Los propietarios de los laboratorios pueden controlar los costos garantizando que:
    - [Las máquinas virtuales se apagan](devtest-lab-set-lab-policy.md#set-auto-shutdown) cuando no están en uso.
    - [Se admite solo un subconjunto de tamaños de instancia de máquina virtual](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) para cargas de trabajo específicas.
    - [Se administran los objetivos de costos y notificaciones](devtest-lab-configure-cost-management.md) para cada laboratorio.

Para obtener más información, consulte [Usar Azure DevTest Labs para entornos de prueba de máquina virtual y PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Investigaciones en espacio aislado
Los desarrolladores a menudo investigan diferentes tecnologías o diseño de infraestructura. De forma predeterminada, todos los entornos creados con DevTest Labs se crean en su propio grupo de recursos. El usuario de DevTest Labs obtiene acceso de solo lectura a esos recursos. Sin embargo, para los desarrolladores que necesitan más control, se puede actualizar un valor en todo el laboratorio para dar [derechos de colaborador](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) al usuario de DevTest Labs que lo origina para todos los entornos que creen.  Con DevTest Labs, los desarrolladores pueden recibir permisos de colaborador automáticamente para los entornos que crean en el laboratorio.  Este escenario permite a los desarrolladores agregar o cambiar los recursos de Azure en función de sus necesidades para sus entornos de desarrollo o prueba. La página de [coste por recurso](devtest-lab-configure-cost-management.md#view-cost-by-resource) permite a los propietarios de laboratorios realizar un seguimiento del costo de cada entorno que se usa para las investigaciones.

Para obtener más información, consulte [Set access rights to an environment resource group](https://aka.ms/dtl-sandbox) (Establecimiento de derechos de acceso para un grupo de recursos de entorno).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Aprendizaje, laboratorios prácticos y hackathons 
Un laboratorio de Azure DevTest Labs actúa como un magnífico contenedor para actividades transitorias como talleres, laboratorios prácticos, cursos o hackathons.  El servicio permite crear un laboratorio en el que puede proporcionar plantillas personalizadas que cada aprendiz puede usar para crear entornos idénticos y aislados con fines de entrenamiento. En este escenario, DevTest Labs ofrece las siguientes ventajas:

- Las [directivas](devtest-lab-set-lab-policy.md) garantizan que los aprendices solo reciben el número de recursos, como máquinas virtuales, que necesitan.
- Las máquinas configuradas y creadas previamente se [solicitan](devtest-lab-add-claimable-vm.md) con una acción única del aprendiz.
- Los laboratorios se comparten con los aprendices accediendo a la [dirección URL para el laboratorio](/azure/devtest-labs/devtest-lab-faq#how-do-i-share-a-direct-link-to-my-lab).
- Las [fechas de caducidad](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) en máquinas virtuales aseguran que las máquinas se eliminan después de que ya no sean necesarias.
- Es fácil [eliminar un laboratorio](devtest-lab-delete-lab-vm.md#delete-a-lab) y todos los [recursos relacionados](/azure/devtest-labs/devtest-lab-faq#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) cuando el aprendizaje ha finalizado.

Para obtener más información, consulte [Uso de Azure DevTest Labs para entrenamiento](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Prueba de concepto frente a implementación a escala
Una vez que decida explorar DevTest Labs, hay dos enfoques generales para seguir: la prueba de concepto y la implementación a escala.  

Una **implementación a escala** conlleva semanas o meses de revisión y planeamiento con la intención de implementar DevTest Labs en toda la empresa con cientos o miles de desarrolladores.

Una implementación de **prueba de concepto** se centra en un esfuerzo concentrado de un único equipo para establecer el valor de la organización. Aunque puede ser tentador pensar en una implementación con escala, el enfoque tiende a recaer con más frecuencia en la opción de la prueba de concepto. Por lo tanto, se recomienda que empiece poco a poco, que aprenda con el primer equipo, que repita el mismo enfoque con dos o tres equipos más y que luego planee una implementación a escala basándose en los conocimientos adquiridos. Para una correcta prueba de concepto, se recomienda que elija uno o dos equipos e identifique sus escenarios (entornos de pruebas frente a entornos de desarrollo), documente sus casos de uso actuales e implemente DevTest Labs.

## <a name="next-steps"></a>Pasos siguientes
Lea los siguientes artículos:

- [Conceptos de DevTest Labs](devtest-lab-concepts.md)
- [Preguntas frecuentes sobre DevTest Labs](devtest-lab-faq.yml)