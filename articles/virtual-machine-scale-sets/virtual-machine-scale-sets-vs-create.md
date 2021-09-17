---
title: Implementación de un conjunto de escalado de máquinas virtuales mediante Visual Studio
description: Implementación de un conjunto de escalado de máquinas virtuales mediante Visual Studio y una plantilla de Resource Manager
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 09/09/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f33cedfa9220510e23cfa5b1c72ac4182e9dd028
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692374"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Procedimiento para crear un conjunto de escalado de máquinas virtuales con Visual Studio

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado uniformes

En este artículo se muestra cómo implementar un conjunto de escalado de máquinas virtuales de Azure mediante la utilización de una implementación de grupo de recursos de Visual Studio.

Los [conjuntos de escalado de máquinas virtuales de Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) constituyen un recurso de Azure Compute para implementar y administrar un conjunto de máquinas virtuales similares con equilibrio de carga y escalado automático. Puede aprovisionar e implementar conjuntos de escalado de máquinas virtuales mediante [plantillas de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Las plantillas de Azure Resource Manager se pueden implementar mediante la CLI de Azure, PowerShell, REST y también directamente desde Visual Studio. Visual Studio proporciona un conjunto de plantillas de ejemplo que se pueden implementar como parte de un proyecto de implementación de grupo de recursos de Azure.

Las implementaciones de grupo de recursos de Azure son una forma de agrupar y publicar un conjunto de recursos de Azure relacionados en una única operación de implementación. Para más información, consulte [Creación e implementación de grupos de recursos de Azure mediante Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="prerequisites"></a>Requisitos previos

Para empezar a implementar conjuntos de escalado de máquinas virtuales en Visual Studio, necesita los siguientes requisitos previos:

* Visual Studio 2013 o posterior.
* Azure SDK 2.7, 2.8 o 2.9

>[!NOTE]
>En este artículo se usa Visual Studio 2019 con [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="create-a-project"></a>Creación de un proyecto <a name="creating-a-project"></a> 

1. Abra Visual Studio y seleccione **Crear un proyecto**.

1. En **Crear un proyecto**, elija **Grupo de recursos de Azure** para C# y, después, seleccione **Siguiente**.

1. En **Configure su nuevo proyecto**, escriba un nombre y seleccione **Crear**.

    ![Asignación de nombre y creación del proyecto](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. En la lista de plantillas, elija la plantilla **Windows Virtual Machine Scale Set** o **Linux Virtual Machine Scale Set**. Seleccione **Aceptar**.

   ![Selección de una plantilla de máquina virtual](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Después de crear el proyecto, el **Explorador de soluciones** contiene un script de implementación de PowerShell, una plantilla de Azure Resource Manager y un archivo de parámetros para el conjunto de escalado de máquinas virtuales.

## <a name="customize-your-project"></a>Personalización del proyecto

Ahora puede editar la plantilla para personalizarla según las necesidades de su aplicación. Puede agregar propiedades de extensión de máquina virtual o editar las reglas de equilibrio de carga. De forma predeterminada, las plantillas del conjunto de escalado de máquinas virtuales se configuran para implementar la extensión **AzureDiagnostics**, que facilita la operación de agregar reglas de escalado automático. También implementan un equilibrador de carga con una dirección IP pública, configurado con reglas NAT de entrada.

El equilibrador de carga permite conectarse a las instancias de máquina virtual con SSH (Linux) o RDP (Windows). El intervalo de puertos de front-end comienza en 50000. En el caso de Linux, si se conecta con SSH al puerto 50000, el equilibrio de carga le dirige al puerto 22 de la primera máquina virtual del conjunto de escalado. La conexión al puerto 50001 se enruta al puerto 22 de la segunda máquina virtual, y así sucesivamente.

 Una buena forma de editar las plantillas con Visual Studio es utilizar el **esquema JSON**. Puede organizar los parámetros, las variables y los recursos. Si comprende el esquema, Visual Studio puede señalar errores en la plantilla antes de implementarla.

![Explorador de JSON](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Implementación del proyecto

Implemente la plantilla de Azure Resource Manager para crear el recurso de conjunto de escalado de máquinas virtuales:

1. En el **Explorador de soluciones**, haga clic con el botón derecho y elija **Implementar** > **Nuevo**.

    ![Implementación del proyecto](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. En **Implementar en grupo de recursos**, decida qué suscripción va a use y seleccione un grupo de recursos. Si es necesario, puede crear un grupo de recursos.

1. A continuación, seleccione **Editar parámetros** para escribir los parámetros que se pasan a la plantilla.

   ![Introducción de la suscripción y el grupo de recursos](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Indique el nombre de usuario y la contraseña para el sistema operativo. Estos valores son necesarios para crear la implementación. Si no tiene instalado PowerShell Tools for Visual Studio, seleccione **Guardar contraseñas** para evitar un símbolo del sistema de PowerShell oculto o bien use la [compatibilidad con Key Vault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). Seleccione **Guardar** para continuar.

    ![Editar parámetros de implementación](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. En **Implementar en grupo de recursos**, seleccione **Implementar**. La acción ejecuta el script **Deploy-AzureResourceGroup.ps1**. En la ventana **Salida** se muestra el progreso de la implementación.

   ![La salida muestra los resultados](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Exploración del conjunto de escalado de máquinas virtuales <a name="exploring-your-virtual-machine-scale-set"></a>

Seleccione **Ver** > **Cloud Explorer** para ver el nuevo conjunto de escalado de máquinas virtuales. Use **Actualizar todo**, si es necesario.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Cloud Explorer** le permite administrar recursos de Azure en Visual Studio mientras desarrolla aplicaciones. También puede ver el conjunto de escalado de máquinas virtuales en [Azure Portal](https://portal.azure.com) y en el [Explorador de recursos de Azure](https://resources.azure.com/).

 El portal constituye la mejor manera de administrar la infraestructura de Azure con un explorador web. Azure Resource Explorer proporciona una forma sencilla de explorar y depurar los recursos de Azure. Azure Resource Explorer ofrece la vista de la instancia y también muestra los comandos de PowerShell para los recursos que está examinando.

## <a name="next-steps"></a>Pasos siguientes

Después de implementar satisfactoriamente los conjuntos de escalado de máquinas virtuales a través de Visual Studio, puede personalizar aún más el proyecto para satisfacer las necesidades de la aplicación. Por ejemplo, configure el escalado automático agregando un recurso **Insights**. Puede agregar infraestructura a la plantilla, como por ejemplo máquinas virtuales independientes, o implementar aplicaciones con la extensión de script personalizado. Se puede encontrar una buena serie de plantillas de ejemplo en el repositorio de GitHub de [plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates). Busque `vmss`.
