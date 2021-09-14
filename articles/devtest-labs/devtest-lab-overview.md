---
title: Acerca de Azure DevTest Labs | Microsoft Docs
description: Aprenda cómo DevTest Labs puede facilitar la creación, la administración y la supervisión de máquinas virtuales de Azure
ms.topic: article
ms.date: 08/20/2021
ms.openlocfilehash: 5ef1aabb861b90679fbb36e46b837d6567f122d4
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123468049"
---
# <a name="about-azure-devtest-labs"></a>Acerca de Azure DevTest Labs
Azure DevTest Labs permite a los desarrolladores de los equipos administrar automáticamente y de forma eficaz las máquinas virtuales (VM) y los recursos PaaS sin esperar a las aprobaciones.

DevTest Labs crea laboratorios que consisten en bases o plantillas de Azure Resource Manager preconfiguradas. Estas disponen de todas las herramientas necesarias y el software que puede usar para crear entornos. Puede crear entornos en cuestión de minutos en lugar de horas o días.

Con DevTest Labs, puede probar las versiones más recientes de las aplicaciones mediante las siguientes tareas:

- Aprovisione rápidamente entornos de Windows y Linux con artefactos y plantillas reutilizables.
- Integre fácilmente la canalización de la implementación con DevTest Labs para aprovisionar entornos a petición.
- Escale verticalmente las pruebas de carga mediante el aprovisionamiento de varios agentes de prueba y cree entornos previamente aprovisionados con fines de capacitación y demostración.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Funcionalidades
DevTest Labs proporciona las siguientes capacidades a desarrolladores que trabajan con máquinas virtuales:

- Creación de máquinas virtuales rápidamente mediante cinco pasos sencillos.
- Selección de una lista mantenida de bases de máquina virtual que están configuradas, aprobadas y autorizadas por el responsable de equipo o el departamento de TI central.
- Creación de máquinas virtuales desde imágenes personalizadas creadas previamente que disponen del software y de herramientas ya instalados. 
- Creación de máquinas virtuales a partir de fórmulas que son básicamente imágenes personalizadas combinadas con las compilaciones más recientes del software que se instalan cuando se crean las máquinas virtuales. 
- Instalación de artefactos que son extensiones implementadas en máquinas virtuales después de que se realice el aprovisionamiento.
- Establecimiento de programaciones de inicio y apagado automáticos en las máquinas virtuales.
- Notificación de una máquina virtual creada previamente mediante un proceso de creación.

DevTest Labs proporciona las siguientes capacidades a desarrolladores que trabajan con entornos de PaaS:

- Uso de Resource Manager para crear rápidamente entornos de PaaS mediante tres pasos sencillos.
- Selección de una lista mantenida de plantilla de Resource Manager, que están configuradas y autorizadas por el responsable de equipo o el departamento de TI central.
- Puesta en marcha de un grupo de recursos vacío (sandbox) mediante una plantilla de Resource Manager para explorar Azure en el contexto de un laboratorio.

DevTest Labs también permite al departamento de TI central controlar los residuos, optimizar los costos de los recursos y ceñirse a los presupuestos realizando las tareas siguientes:  

- Establecimiento de programaciones de inicio y apagado automáticos en las máquinas virtuales.
- Establecimiento de directivas sobre el número de máquinas virtuales que los usuarios pueden crear.
- Establecimiento de directivas sobre imágenes de la galería y tamaños de máquina virtual que los usuarios pueden elegir.
- Seguimiento de costos y configuración de objetivos en los laboratorios.
- Notificación de costos elevados previstos para laboratorios de manera que pueda realizar las acciones necesarias.

DevTest Labs proporciona las siguientes ventajas en la creación, configuración y administración de entornos en la nube.

## <a name="cost-control-and-governance"></a>Gobernanza y control de costos
DevTest Labs facilita el control de costos, lo que le permite realizar las siguientes tareas:

- [Establecer directivas en sus laboratorios](devtest-lab-set-lab-policy.md), como el número de máquinas virtuales por usuario o por laboratorio. 
- Crear [directivas para iniciar](devtest-lab-set-lab-policy.md) y apagar automáticamente las máquinas virtuales.
- Realizar un seguimiento de los costos en recursos de PaaS puestos en marcha dentro de los laboratorios para ceñirse a [su presupuesto](devtest-lab-configure-cost-management.md).
- Continuar dentro del contexto de los laboratorios, por lo que no se pondrán en marcha recursos fuera de ellos.

## <a name="quickly-get-to-ready-to-test"></a>Obtenga acceso rápidamente al modo "Listo para probar"
DevTest Labs le permite crear entornos aprovisionados previamente equipados con todo lo que necesita su equipo para desarrollar y probar aplicaciones. Simplemente [notifique los entornos](devtest-lab-add-claimable-vm.md) en los que se instaló la última compilación en buen estado de la aplicación y podrá empezar a trabajar. También puede usar contenedores para agilizar y simplificar aún más la creación de entornos.

## <a name="create-once-use-everywhere"></a>Se crean una vez y se utilizan en todas partes
Capture y comparta [plantillas de entornos](devtest-lab-create-environment-from-arm.md) y [artefactos](add-artifact-repository.md) de PaaS dentro de su equipo u organización, todo con control de código fuente, para crear entornos de desarrollo y pruebas con facilidad.

## <a name="worry-free-self-service"></a>Autoservicio sin preocupaciones
DevTest Labs permite a sus desarrolladores y evaluadores crear de forma rápida y sencilla [máquinas virtuales de IaaS](devtest-lab-add-vm.md) y [recursos de PaaS](devtest-lab-create-environment-from-arm.md) mediante un conjunto de recursos preconfigurados.

## <a name="use-iaas-and-paas-resources"></a>Uso de recursos de IaaS y PaaS 
Los desarrolladores pueden poner en marcha también recursos de PaaS, como clústeres de Azure Service Fabric, la característica Web Apps de Azure App Service y granjas de SharePoint mediante plantillas de Resource Manager. Para comenzar a trabajar con PaaS en laboratorios, utilice las plantillas del [repositorio de entorno público](devtest-lab-configure-use-public-environments.md) o [conecte el laboratorio a su propio repositorio de Git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). También puede realizar un seguimiento de estos recursos para ceñirse al presupuesto.

## <a name="integrate-with-your-existing-toolchain"></a>Integración con la cadena de herramientas existente
Utilice complementos ya creados o la API para aprovisionar entornos de desarrollo y pruebas directamente desde la [herramienta de integración continua (CI)](devtest-lab-integrate-ci-cd.md), el entorno de desarrollo integrado (IDE) o la canalización de entrega de versiones que prefiera. También puede utilizar la completa herramienta de línea de comandos.

## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

- Para obtener más información sobre DevTest Labs, consulte [Conceptos de DevTest Labs](devtest-lab-concepts.md).
- Para ver un tutorial con instrucciones paso a paso, vea [Tutorial: configurar un laboratorio mediante Azure DevTest Labs](tutorial-create-custom-lab.md).
