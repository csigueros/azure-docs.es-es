---
title: Configuración de la creación de reflejo del puerto para Azure VMware Solution
description: Aprenda a configurar la creación de reflejo del puerto para supervisar el tráfico de red que supone el reenvío de una copia de cada paquete de un puerto de conmutación de red a otro.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 07/16/2021
ms.openlocfilehash: 3f1b205cc75911132abeed83d1e52e3566919551
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322736"
---
# <a name="configure-port-mirroring-in-the-azure-portal"></a>Configuración de la creación de reflejo del puerto en Azure Portal

Después de implementar Azure VMware Solution, puede el reflejo del puerto en Azure Portal. Esta funcionalidad coloca un analizador de protocolos en el puerto que recibe los datos reflejados. Se analiza el tráfico desde un origen, una máquina virtual (VM) o un grupo de máquinas virtuales y, luego, se envía a un destino definido. 

En esta guía paso a paso, configurará la creación de reflejo del puerto para supervisar el tráfico de red que supone el reenvío de una copia de cada paquete de un puerto de conmutación de red a otro. 

## <a name="prerequisites"></a>Requisitos previos

Una nube privada de Azure VMware Solution con acceso a las interfaces de vCenter y NSX-T Manager. Para obtener más información, consulte el tutorial de [configuración de redes](tutorial-configure-networking.md).

## <a name="create-the-vms-or-vm-groups"></a>Creación de las máquinas virtuales o de los grupos de máquinas virtuales

Creará las máquinas virtuales o los grupos de máquinas virtuales de origen y destino. El grupo de origen tiene una sola máquina virtual o varias máquinas virtuales donde se refleja el tráfico.

1. En la nube privada de Azure VMware Solution, en **Workload Networking** (Red de la carga de trabajo), seleccione **Port mirroring** > **VM groups** > **Add** (Creación de reflejo del puerto > Grupos de máquinas virtuales > Agregar).

   :::image type="content" source="media/networking/add-port-mirroring-vm-groups.png" alt-text="Captura de pantalla que muestra cómo crear un grupo de máquinas virtuales para la creación de reflejo del puerto.":::

1. Proporcione un nombre para el nuevo grupo de máquinas virtuales, seleccione las máquinas virtuales de la lista y, luego, haga clic en **Aceptar**.

1. Repita estos pasos para crear el grupo de máquinas virtuales de destino.

   >[!NOTE]
   >Antes de crear un perfil de creación de reflejo del puerto, asegúrese de que ha creado los grupos de máquinas virtuales de origen y destino.

## <a name="create-a-port-mirroring-profile"></a>Creación de un perfil de creación de reflejo del puerto

Creará un perfil de creación de reflejo del puerto que defina la dirección del tráfico de los grupos de máquinas virtuales de origen y destino.

1. Seleccione **Creación de reflejo del puerto** > **Creación de reflejo del puerto** > **Agregar** y, luego, proporcione:

   :::image type="content" source="media/networking/add-port-mirroring-profile.png" alt-text="Captura de pantalla que muestra la información necesaria para el perfil de creación de reflejo del puerto.":::

   - **Port mirroring name** (Nombre de la creación de reflejo del puerto): nombre descriptivo del perfil.

   - **Direction** (Dirección): seleccione entrada, salida o bidireccional.

   - **Source** (Origen): seleccione el grupo de máquinas virtuales de origen.

   - **Destination** (Destino): seleccione el grupo de máquinas virtuales de destino.

   - **Description** (Descripción): escriba una descripción para la creación de reflejo del puerto.

1. Seleccione **OK** (Aceptar) para completar el perfil. 

   El perfil y los grupos de máquinas virtuales están visibles en la consola de Azure VMware Solution.
