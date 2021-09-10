---
title: Creación de máquinas virtuales en un conjunto de escalado flexible con Azure Portal
description: Información sobre cómo crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible a través de Azure Portal.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: cef23894759b0b2dca7098ef1c430548d1720406
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868539"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Versión preliminar: creación de máquinas virtuales en un conjunto de escalado flexible con Azure Portal

**Se aplica a:** :heavy_check_mark: Conjuntos de escalado flexibles

En este artículo se describe el uso de Azure Portal para crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible. Para obtener más información sobre los conjuntos de escalado flexibles, vea [Modo de orquestación flexible para conjuntos de escalado de máquinas virtuales](flexible-virtual-machine-scale-sets.md). 


> [!IMPORTANT]
> En el modo de orquestación flexible, los conjuntos de escalado de máquinas virtuales se encuentran actualmente en versión preliminar pública. No es necesario ningún procedimiento de participación para usar la funcionalidad de versión preliminar pública que se describe a continuación.
> Esta versión preliminar se ofrece sin contrato de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


> [!CAUTION]
> El modo de orquestación se define al crear el conjunto de escalado y no se puede cambiar ni actualizar más adelante.


## <a name="register-for-flexible-orchestration-mode"></a>Registro para el modo de orquestación flexible

Para poder implementar conjuntos de escalado de máquinas virtuales en el modo de orquestación flexible, antes es preciso registrar la suscripción en la característica en vista previa (GB). El registro de la característica puede tardar hasta 15 minutos.

Durante el modo de orquestación flexible de versión preliminar de conjuntos de escalado, use la *característica en vista previa* de Azure Portal cuyo vínculo encontrará en los siguientes pasos. 

1. Inicie sesión en Azure Portal en https://preview.portal.azure.com.
1. Vaya a **Suscripciones**.
1. Vaya a la página de detalles de la suscripción en la que quiere crear un conjunto de escalado en modo de orquestación flexible y seleccione el nombre de dicha suscripción.
1. En el menú bajo **Configuración**, seleccione **Características en vista previa**.
1. Seleccione las cuatro características de orquestador que pueden habilitarse: *VMOrchestratorSingleFD*, *VMOrchestratorMultiFD*, *VMScaleSetFlexPreview* y *SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview*.
1. Seleccione **Registrar**.

Una vez que se hayan registrado las características para su suscripción, complete el proceso de participación mediante la propagación del cambio en el proveedor de recursos de Compute. 

1. En el menú bajo **Configuración**, seleccione **Proveedores de recursos**.
1. Seleccione `Microsoft.compute`.
1. Seleccione **Volver a registrar**.


## <a name="get-started-with-flexible-orchestration-mode"></a>Introducción al modo de orquestación flexible

### <a name="create-a-virtual-machine-scale-set-in-flexible-orchestration-mode-through-the-azure-portal"></a>Cree un conjunto de escalado de máquinas virtuales en modo de orquestación Flexible mediante Azure Portal.

Durante el modo de orquestación flexible de versión preliminar de conjuntos de escalado, use la *característica en vista previa* de Azure Portal cuyo vínculo encontrará en los siguientes pasos. 

1. Inicie sesión en Azure Portal en https://preview.portal.azure.com.
1. En la barra de búsqueda, busque y seleccione **Conjuntos de escalado de máquinas virtuales**.
1. Seleccione **Crear** en la página **Conjuntos de escalado de máquinas virtuales**.
1. En la página **Crear un conjunto de escalado de máquinas virtuales**, vea la sección **Orquestación**.
1. En **Orchestration mode** (Modo de orquestación), seleccione la opción **Flexible**.
1. Indique un valor en **Número de dominios de error**.
1. Termine de crear el conjunto de escalado. Para más información sobre cómo crear un conjunto de escalado, consulte [Creación de un conjunto de escalado en Azure Portal](../virtual-machine-scale-sets/quick-create-portal.md#create-virtual-machine-scale-set).


### <a name="next-add-a-virtual-machine-to-the-scale-set-in-flexible-orchestration-mode"></a>A continuación, agregue una máquina virtual al conjunto de escalado en el modo de orquestación flexible.

1. En la barra de búsqueda, busque y seleccione **Máquinas virtuales**.
1. En la página **Máquinas virtuales**, seleccione **Agregar**.
1. En la pestaña **Aspectos básicos**, vea la sección **Detalles de instancia**.
1. Agregue la máquina virtual al conjunto de escalado en el modo de orquestación flexible, para lo que debe seleccionar el conjunto de escalado en las **opciones de disponibilidad**. Puede agregar la máquina virtual a un conjunto de escalado en la misma región, zona y grupo de recursos.
1. Vaya a la pestaña **Redes** y defina explícitamente la conectividad saliente.

    > [!IMPORTANT]
    > La conectividad saliente definida explícitamente es necesaria en los conjuntos de escalado de máquinas virtuales con orquestación flexible. Consulte la [Conectividad de red saliente explícita](flexible-virtual-machine-scale-sets.md#explicit-network-outbound-connectivity-required) para obtener más información.

1. Termine de crear la máquina virtual.


## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Más información sobre cómo crear un conjunto de escalado flexible con la CLI de Azure](flexible-virtual-machine-scale-sets-cli.md)