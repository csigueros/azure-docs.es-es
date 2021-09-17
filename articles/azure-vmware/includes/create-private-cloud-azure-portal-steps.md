---
title: Creación de una nube privada de Azure VMware Solution
description: Pasos para crear una nube privada de Azure VMware Solution mediante Azure Portal.
ms.topic: include
ms.date: 08/05/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: beae1ba1fe5cb37f9c96d411ca9ee0e8cddfe1d3
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122262723"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Puede crear una nube privada de Azure VMware Solution mediante Azure Portal o con la CLI de Azure.


### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **Crear un nuevo recurso**. 

1. En el cuadro de texto **Buscar en Marketplace**, escriba `Azure VMware Solution` y selecciónelo en los resultados. 

1. En la ventana **Azure VMware Solution**, seleccione **Crear**.

1. En la pestaña **Aspectos básicos**, introduzca los valores de los campos y, luego, seleccione **Revisar y crear**. 

   >[!TIP]
   >La información se recopiló durante la [fase de planeamiento](../plan-private-cloud-deployment.md) de esta inicio rápido.

   | Campo   | Value  |
   | ---| --- |
   | **Suscripción** | Seleccione la suscripción que va a usar para la implementación. Todos los recursos de una suscripción a Azure se facturan juntos.|
   | **Grupos de recursos** | Seleccione el grupo de recursos de la nube privada. Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Como alternativa, puede crear un nuevo grupo de recursos para su nube privada. |
   | **Nombre del recurso** | Facilite el nombre de la nube privada de Azure VMware Solution. |
   | **Ubicación** | Seleccione una ubicación, como **Este de EE. UU.** Esta es la *región* que definió en la fase de planeamiento. |
   | **Tamaño del host** | Seleccione **AV36**. |
   | **Número de hosts** | Número de hosts asignados para el clúster de nube privada. El valor predeterminado es 3 y puede aumentarlo o reducirlo después de la implementación.  |
   | **Bloque de direcciones para la nube privada** | Proporcione un bloque de direcciones IP para la nube privada.  El CIDR representa la red de administración de la nube privada y se usará para los servicios de administración de clústeres, como vCenter Server y NSX-T Manager. Use el espacio de direcciones /22, por ejemplo, 10.175.0.0/22.  La dirección debe ser única y no superponerse con otras instancias de Azure Virtual Network, así como tampoco con redes locales. |
   

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Captura de pantalla que muestra la pestaña Aspectos básicos en la ventana Crear una nube privada." border="true":::

1. Verifique la información especificada y, si es correcta, seleccione **Crear**.  

   > [!NOTE]
   > Este paso tarda aproximadamente entre 3 y 4 horas. La adición de un host individual a un clúster existente o al mismo clúster tarda entre 30 y 45 minutos.

1. Comprobación de que la implementación se ha realizado correctamente. Navegue hasta el grupo de recursos que creó y seleccione la nube privada.  Una vez que se haya completado la implementación, se mostrará el estado **Correcto**. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Captura de pantalla que muestra que la implementación se completó correctamente." border="true":::


### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
En lugar de usar Azure Portal para crear una nube privada de Azure VMware Solution, puede usar la CLI de Azure con Azure Cloud Shell. Para conocer la lista de los comandos que puede usar con Azure VMware Solution, consulte [Comandos de Azure VMware](/cli/azure/ext/vmware/vmware).

Para empezar a usar la CLI de Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]


1. Para crear un grupo de recursos, use el comando ["az group create"](/cli/azure/group). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

   ```azurecli-interactive
   
   az group create --name myResourceGroup --location eastus
   ```

2. Proporcione un nombre para el grupo de recursos y la nube privada, una ubicación y el tamaño del clúster.

   | Propiedad  | Descripción  |
   | --------- | ------------ |
   | **-g** (nombre del grupo de recursos)     | Nombre del grupo de recursos para los recursos de nube privada.        |
   | **-n** (nombre de nube privada)     | Nombre de la nube privada de Azure VMware Solution.        |
   | **--location**     | Región usada para la nube privada.         |
   | **--cluster-size**     | El tamaño del clúster. El valor mínimo es 3.         |
   | **--network-block**     | Bloque de red de ñas direcciones IP de CIDR que se usará para la nube privada. El bloque de direcciones no debe superponerse a los bloques de direcciones que se hayan usado en otras redes virtuales ubicadas en las redes locales y de la suscripción.        |
   | **--sku** | Valor de la SKU: AV36 |

   ```azurecli-interactive 
   az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
   ```
