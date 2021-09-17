---
title: 'Tutorial: Escalado de clústeres en una nube privada'
description: En este tutorial, usará Azure Portal para escalar una nube privada de la versión preliminar de Azure VMware Solution.
ms.topic: tutorial
ms.date: 08/03/2021
ms.openlocfilehash: 47bc7d12a025104df48d5a55af4abf342759696e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638770"
---
# <a name="tutorial-scale-clusters-in-a-private-cloud"></a>Tutorial: Escalado de clústeres en una nube privada

Para sacar el máximo partido de la experiencia de la nube privada de Azure VMware Solution, escale los clústeres y los hosts para que reflejen lo que necesita para las cargas de trabajo planeadas. Puede escalar los clústeres y los hosts en una nube privada, según sea necesario, para la carga de trabajo de la aplicación.  Las limitaciones de rendimiento y disponibilidad de los servicios específicos deberán abordarse caso por caso. 

[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

En este tutorial, usará Azure Portal para:

> [!div class="checklist"]
> * Agregar un clúster a una nube privada existente
> * Agregar nodos a un clúster existente

## <a name="prerequisites"></a>Prerrequisitos

Para completar este tutorial, necesitará una nube privada existente. Si no ha creado una nube privada, realice el tutorial para la [creación de una nube privada](tutorial-create-private-cloud.md) con el fin de crear una. 

## <a name="add-a-new-cluster"></a>Incorporación de un nuevo clúster

1. En la nube privada de Azure VMware Solution, en **Administrar**, seleccione **Clústeres** > **Agregar un clúster**.

   :::image type="content" source="media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Captura de pantalla que muestra cómo agregar un clúster a una nube privada de Azure VMware Solution." border="true":::

1. Use el control deslizante para seleccionar el número de hosts y, a continuación, seleccione **Guardar**.

   :::image type="content" source="media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Captura de pantalla que muestra cómo configurar un clúster nuevo." border="true":::

   Se inicia la implementación del nuevo clúster.

## <a name="scale-a-cluster"></a>Escalado de un clúster 

1. En la nube privada de Azure VMware Solution, en **Administrar**, seleccione **Clústeres**.

1. Seleccione el clúster que desea escalar, seleccione **Más** (...) y, por último, seleccione **Editar**.

   :::image type="content" source="media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Captura de pantalla que muestra dónde editar un clúster existente." border="true":::

1. Use el control deslizante para seleccionar el número de hosts y, a continuación, seleccione **Guardar**.

   Empiezan a agregarse hosts al clúster.

## <a name="next-steps"></a>Pasos siguientes

Si necesita otra nube privada de Azure VMware Solution, [créela](tutorial-create-private-cloud.md), con los mismos requisitos previos de red, clúster y límites de host.

<!-- LINKS - external-->

<!-- LINKS - internal -->
