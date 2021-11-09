---
title: Introducción a DevOps Starter para Azure | Microsoft Docs
description: Conozca el valor de DevOps Starter.
services: devops-project
documentationcenter: ''
author: georgewallace
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: gwallace
ms.openlocfilehash: 04fe33686d640ab84c0bdb532486a15b5582a612
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057145"
---
# <a name="overview-of-devops-starter"></a>Información general de DevOps Starter

 DevOps Starter facilita el uso inicial de Azure mediante acciones de GitHub o Azure DevOps. Le ayuda a iniciar su aplicación favorita en el servicio de Azure que elija en unos pocos pasos rápidos desde Azure Portal. 

 DevOps Starter configura todo lo necesario para desarrollar, implementar y supervisar la aplicación. El panel de DevOps Starter se puede usar para supervisar confirmaciones de código, compilaciones e implementaciones, y todo ello, desde una sola vista en Azure Portal.

## <a name="advantages-of-using-devops-starter"></a>Ventajas del uso de DevOps Starter

  DevOps Starter admite los dos proveedores siguientes de CI/CD para automatizar las implementaciones:
  * [Acciones de GitHub](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  DevOps Starter automatiza la configuración de una canalización completa de integración continua (CI) y entrega continua (CD) para una aplicación en Azure.  Puede empezar con el código existente o usar una de las aplicaciones de ejemplo proporcionadas. Luego puede implementar rápidamente dicha aplicación en varios servicios de Azure como Virtual Machines, App Service, Azure Kubernetes Services (AKS), Azure SQL Database y Azure Service Fabric.  

  DevOps Starter realiza todo el trabajo de la configuración inicial de una canalización de DevOps, lo que incluye configurar el repositorio de Git inicial y la canalización de CI/CD, crear un recurso de Application Insights para la supervisión y proporcionar una sola vista de toda la solución con la creación de un panel de DevOps Starter en Azure Portal.

Puede usar DevOps Starter para:

* Implementar rápidamente la aplicación en Azure
* Automatizar la instalación de un flujo de trabajo o una canalización de CI/CD
* Ver y comprender cómo se configura correctamente una canalización o un flujo de trabajo de CI/CD
* Personalizar aún más las canalizaciones de versión en función de sus escenarios concretos

## <a name="how-to-use-devops-starter"></a>¿Cómo se usa DevOps Starter?

  DevOps Starter está disponible en Azure Portal. Los recursos de DevOps Starter se crean como cualquier otro recurso de Azure desde el portal. DevOps Projects proporciona una experiencia de asistente paso a paso para las distintas opciones de configuración.  

Elija varias opciones de configuración como parte de la instalación inicial. Entre estas opciones se incluyen:

* Selección del proveedor de CI/CD favorito
* Uso de la aplicación de ejemplo proporcionada o de su propio código (solo para Azure DevOps)
* Selección del lenguaje de una aplicación
* Elección de un marco de aplicación en función del lenguaje
* Selección de un servicio de Azure (destino de implementación)
* Selección de la organización de GitHub o Azure DevOps
* Elección de una suscripción de Azure
* Elección de la ubicación de los servicios de Azure
* Elección entre varios planes de tarifa de servicios de Azure

Después de crear la instancia de DevOps Starter, también puede:

* Personalización del flujo de trabajo de GitHub o de la canalización de Azure DevOps
* Usar solicitudes de incorporación de cambios para administrar el flujo de código y mantener un alto nivel de calidad
* Probar y compilar cada confirmación antes de fusionar el código para elevar el listón de calidad

Después de usar DevOps Starter también puede eliminar todos los recursos desde un mismo lugar desde el panel de DevOps Starter en Azure Portal.

## <a name="devops-starter-and-github-integration"></a>Integración de DevOps Starter y GitHub

DevOps Starter ahora admite acciones de GitHub como proveedor de CI/CD. Automatiza todo el trabajo necesario en GitHub para configurar un flujo de trabajo de CI/CD mediante Acciones de GitHub. Crea un repositorio de GitHub en una organización existente de GitHub y, luego, confirma una aplicación de ejemplo en el nuevo repositorio de GitHub.  

La automatización también establece un desencadenador para el flujo de trabajo, de modo que cada nueva confirmación de código inicia un trabajo de compilación e implementación dentro del flujo de trabajo. La aplicación se implementa en el servicio de Azure de su elección. El flujo de trabajo de GitHub puede personalizarse para adaptarlo a otros escenarios. 

## <a name="devops-starter-and-azure-devops-integration"></a>Integración de DevOps Starter y Azure DevOps

DevOps Starter con Azure DevOps automatiza todo el trabajo que se necesita en Azure Pipelines para configurar una canalización de CI/CD. Crea un repositorio Git en una organización de Azure DevOps nueva o existente y, luego, confirma una aplicación de ejemplo o el código existente en un nuevo repositorio Git.  

La automatización también establece un desencadenador de integración continua para la compilación, con el fin de que cada confirmación de código inicie una compilación. DevOps Starter crea un desencadenador de implementación continua e implementa cada nueva compilación correcta en el servicio de Azure que elija.  

Las canalizaciones de compilación y versión se pueden personalizar para otros escenarios. Además, puede clonar las canalizaciones de compilación y de versión para usarlas en otros proyectos.

## <a name="getting-started-with-devops-starter"></a>Introducción a DevOps Starter

* [Introducción a DevOps Starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>Vídeos de DevOps Starter

* [Creación de CI/CD con Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)
