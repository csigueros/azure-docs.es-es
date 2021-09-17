---
title: Configuración de implementaciones controladas para máquinas virtuales Linux en Azure
description: Aprenda a configurar una canalización de implementación continua (CD). Esta canalización actualiza un grupo de máquinas virtuales Linux en Azure con la estrategia de implementación controlada.
author: moala
tags: azure-devops-pipelines
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: f9ab4712e6419303f010d7cf1a417a8a907f9a05
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690953"
---
# <a name="configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Configuración de la estrategia de implementación controlada para máquinas virtuales Linux en Azure

**Se aplica a:** :heavy_check_mark: máquinas virtuales Linux 

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infraestructura como servicio (IaaS): configuración de CI/CD

Azure Pipelines ofrece un conjunto completo de herramientas de automatización de CI/CD para las implementaciones en máquinas virtuales. En Azure Portal, se puede configurar directamente una canalización de entrega continua para una máquina virtual de Azure.

En este artículo, se muestra cómo configurar una canalización de CI/CD que utiliza la estrategia controlada para la implementación de varias máquinas. Azure Portal también admite otras estrategias, como [gradual](./tutorial-devops-azure-pipelines-classic.md) y [azul-verde](./tutorial-azure-devops-blue-green-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>Configuración de CI/CD en máquinas virtuales

Las máquinas virtuales se pueden agregar como destinos a un [grupo de implementación](/azure/devops/pipelines/release/deployment-groups). Posteriormente, se pueden establecer como destino para actualizaciones de varias máquinas. Después de efectuar la implementación en las máquinas, vea el **historial de implementación** para un grupo de implementación. Esta vista le permite hacer un seguimiento de la máquina virtual hasta la canalización y, posteriormente, hasta la confirmación.

### <a name="canary-deployments"></a>Implementaciones controladas

Las implementaciones controladas reducen el riesgo, ya que implementan los cambios gradualmente en un pequeño subconjunto de usuarios. A medida que va ganando confianza con la nueva versión, puede usarla en más servidores de la infraestructura y enrutar más usuarios a ella.

Puede configurar implementaciones controladas en sus "máquinas virtuales" con la opción de entrega continua de Azure Portal. A continuación, se incluyen los pasos detallados:

1. Inicie sesión en Azure Portal y desplácese hasta una máquina virtual.
1. En el panel de la izquierda de la configuración de la máquina virtual, seleccione **Entrega continua**. A continuación, seleccione **Configurar**.

   ![Panel de entrega continua con el botón Configurar](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. En el panel de configuración, haga clic en **Organización de Azure DevOps** para seleccionar una cuenta existente o crear una nueva. Después, seleccione el proyecto en el que desea configurar la canalización.  

   ![El panel de entrega continua](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Un grupo de implementación es un conjunto lógico de máquinas de destino de implementación que representan los entornos físicos. Por ejemplo, Dev, Test, UAT y Production. Puede crear un grupo de implementación o seleccionar uno existente.
1. Seleccione la canalización de compilación que publica el paquete que se va a implementar en la máquina virtual. Tenga en cuenta que el paquete publicado debe tener un script de implementación llamado deploy.ps1 o deploy.sh en la carpeta deployscripts de la carpeta raíz del paquete. La canalización ejecuta este script de implementación.
1. En **Deployment strategy** (Estrategia de implementación), seleccione **Canary** (Valor controlado).
1. Agregue una etiqueta "controlada" a las máquinas virtuales que van a formar parte de las implementaciones controladas. Agregue una etiqueta "prod" a las máquinas virtuales que formen parte de las implementaciones realizadas después de que la implementación controlada se realice correctamente. Las etiquetas le ayudan a dirigirse solo a las máquinas virtuales que tienen un rol específico.

   ![Panel de entrega continua, con la opción Valor controlada elegida como estrategia de implementación](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Seleccione **Aceptar** para configurar la canalización de entrega continua que va a implementar en la máquina virtual.

   ![La canalización controlada](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. Se muestran los detalles de la implementación de la máquina virtual. Puede seleccionar el vínculo para ir a la canalización de versión en Azure DevOps. En la canalización de versión, seleccione **Editar** para ver la configuración de canalización. La canalización tiene estas tres fases:

   1. Esta fase es una fase de grupo de implementación. Las aplicaciones se implementan en las máquinas virtuales etiquetadas como "controladas".
   1. En esta fase, la canalización se detiene y espera una intervención manual para reanudar la ejecución.
   1. De nuevo, esta es una fase de grupo de implementación. La actualización se implementa ahora en las máquinas virtuales etiquetadas como "prod".

      ![El panel Grupo de implementación de la tarea Implementación controlada](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. Antes de reanudar la ejecución de la canalización, asegúrese de que al menos una máquina virtual esté etiquetada como "prod". En la tercera fase de la canalización, las aplicaciones se implementarán solo en las máquinas virtuales que tengan la etiqueta "prod".

1. La tarea Execute Deploy Script (Ejecutar script de implementación) ejecuta de forma predeterminada el script de implementación deploy.ps1 or deploy.sh. El script se encuentra en la carpeta deployscripts de la carpeta raíz del paquete publicado. Asegúrese de que la canalización de compilación seleccionada publica la implementación en la carpeta raíz del paquete.

   ![Panel Artefactos, que muestra el script deploy.sh en la carpeta deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Otras estrategias de implementación
- [Configuración de la estrategia de implementación gradual](./tutorial-devops-azure-pipelines-classic.md)
- [Configuración de la estrategia de implementación azul-verde](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Se puede comenzar a utilizar Azure fácilmente. En Azure DevOps Projects, puede empezar a ejecutar su aplicación en cualquier servicio de Azure con solo tres pasos. Para ello, seleccione:

- Un idioma de la aplicación
- Un entorno de ejecución
- Un servicio de Azure

[Más información](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Recursos adicionales

- [Implementación de una aplicación de ASP.NET en máquinas virtuales de Azure mediante Azure DevOps Starter](../../devops-project/azure-devops-project-vms.md)
- [Implementación continua de una aplicación en un conjunto de escalado de máquinas virtuales de Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)