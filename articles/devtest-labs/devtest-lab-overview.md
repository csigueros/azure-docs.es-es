---
title: ¿Qué es Azure DevTest Labs?
description: Aprenda cómo DevTest Labs puede facilitar la creación, la administración y la supervisión de máquinas virtuales de Azure
ms.topic: overview
ms.date: 10/20/2021
ms.openlocfilehash: f521b1fcd886d56387c9b6c44451d1858f95d62f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229064"
---
# <a name="what-is-azure-devtest-labs"></a>¿Qué es Azure DevTest Labs?
Azure DevTest Labs es un servicio que permite a los desarrolladores administrar automáticamente y de forma eficaz las máquinas virtuales (VM) y los recursos de plataforma como servicio (PaaS) sin esperar a las aprobaciones. DevTest Labs crea laboratorios que consisten en bases o plantillas de Azure Resource Manager preconfiguradas. Estos laboratorios disponen de todas las herramientas necesarias y el software que puede usar para crear entornos.

Con DevTest Labs, puede probar las versiones más recientes de las aplicaciones mediante las siguientes tareas:

- Cree rápidamente entornos de Windows y Linux con artefactos y plantillas reutilizables.
- Integre fácilmente la canalización de la implementación con DevTest Labs para crear entornos a petición.
- Escale verticalmente las pruebas de carga mediante la creación de varios agentes de prueba y entornos previamente preparados para llevar a cabo formación y demos.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="cost-control-and-governance"></a>Gobernanza y control de costos
DevTest Labs facilita el control de costos, lo que le permite realizar las siguientes tareas:

- [Establecer directivas en sus laboratorios](devtest-lab-set-lab-policy.md), como el número de máquinas virtuales por usuario o por laboratorio. 
- Crear [directivas para iniciar](devtest-lab-set-lab-policy.md) y apagar automáticamente las máquinas virtuales.
- Realizar un seguimiento de los costos en recursos de PaaS puestos en marcha dentro de los laboratorios para ceñirse a [su presupuesto](devtest-lab-configure-cost-management.md). Recibir notificaciones de costos elevados previstos para laboratorios de manera que pueda realizar las acciones necesarias.
- Continuar dentro del contexto de los laboratorios, por lo que no se pondrán en marcha recursos fuera de ellos.

## <a name="quickly-get-to-ready-to-test"></a>Obtenga acceso rápidamente al modo "Listo para probar"
DevTest Labs le permite crear entornos aprovisionados previamente para desarrollar y probar aplicaciones. Simplemente [notifique el entorno](devtest-lab-add-claimable-vm.md) de la última compilación en buen estado de la aplicación y podrá empezar a trabajar. También puede usar contenedores para agilizar y simplificar aún más la creación de entornos.

## <a name="create-once-use-everywhere"></a>Se crean una vez y se utilizan en todas partes
Capture y comparta [plantillas de entornos](devtest-lab-create-environment-from-arm.md) y [artefactos](add-artifact-repository.md) de PaaS dentro de su equipo u organización, todo con control de código fuente, para crear entornos de desarrollo y pruebas con facilidad.

## <a name="worry-free-self-service"></a>Autoservicio sin preocupaciones
DevTest Labs permite a sus desarrolladores y evaluadores crear de forma rápida y sencilla [máquinas virtuales de IaaS](devtest-lab-add-vm.md) y [recursos de PaaS](devtest-lab-create-environment-from-arm.md) mediante un conjunto de recursos preconfigurados.

## <a name="use-iaas-and-paas-resources"></a>Uso de recursos de IaaS y PaaS 
Ponga en marcha recursos, como clústeres de Azure Service Fabric o granjas de SharePoint, mediante plantillas de Resource Manager. Las plantillas vienen del [repositorio de entorno público](devtest-lab-configure-use-public-environments.md) o [conecte el laboratorio a su propio repositorio de Git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). También puede poner en marcha un grupo de recursos vacío (espacio aislado) mediante una plantilla de Resource Manager para explorar Azure en el contexto de un laboratorio.

## <a name="integrate-with-your-existing-toolchain"></a>Integración con la cadena de herramientas existente
Utilice complementos ya creados o la API para crear entornos de desarrollo y pruebas directamente desde la [herramienta de integración continua (CI)](devtest-lab-integrate-ci-cd.md), el entorno de desarrollo integrado (IDE) o la canalización de entrega de versiones que prefiera. También puede utilizar la completa herramienta de línea de comandos.

## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

- Para obtener más información sobre DevTest Labs, consulte [Conceptos de DevTest Labs](devtest-lab-concepts.md).
- Para ver un tutorial con instrucciones paso a paso, vea [Tutorial: configurar un laboratorio mediante Azure DevTest Labs](tutorial-create-custom-lab.md).
