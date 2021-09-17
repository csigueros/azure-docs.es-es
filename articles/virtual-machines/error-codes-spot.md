---
title: Códigos de error para instancias de máquinas virtuales de acceso puntual de Azure y conjuntos de escalado
description: Obtenga información sobre los códigos de error que podría ver al usar instancias de máquinas virtuales de acceso puntual de Azure y conjuntos de escalado.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: f035d7813dfcf84d21e5913fa4626188e125db9a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691090"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Mensajes de error para máquinas virtuales de acceso puntual de Azure y conjuntos de escalado

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Estos son algunos de los códigos de error que podría recibir al usar máquinas virtuales de acceso puntual de Azure y conjuntos de escalado.


| Clave | Message | Descripción |
|-----|---------|-------------|
| SkuNotAvailable | El nivel solicitado para el recurso "\<resource\>" no está disponible actualmente en la ubicación "\<location\>" para la suscripción "\<subscriptionID\>". Pruebe otro nivel o realice la implementación en una ubicación diferente. | No hay suficiente capacidad en las máquinas virtuales de acceso puntual de Azure para crear la instancia de VM o de conjunto de escalado. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  La directiva de expulsión solo se puede establecer en las máquinas virtuales de Azure Spot. | Esta VM no es una máquina virtual de acceso puntual de Azure, por lo que no se puede establecer la directiva de expulsión. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  La máquina virtual de Azure Spot no es compatible con el conjunto de disponibilidad. | Tiene la opción de usar una máquina virtual de acceso puntual de Azure o usar una VM en un conjunto de disponibilidad, no ambas. |
| AzureSpotFeatureNotEnabledForSubscription  |  Suscripción no habilitada con la característica Máquina virtual de acceso puntual de Azure. | Use una suscripción que admita máquinas virtuales de acceso puntual de Azure. |
| VMPriorityCannotBeApplied  |  El valor de prioridad especificado "{0}" no se puede aplicar a la máquina virtual "{1}", ya que no se especificó ninguna prioridad al crearla. | Especifique la prioridad al crear la máquina virtual. |
| SpotPriceGreaterThanProvidedMaxPrice  |  No se pudo realizar la operación "{0}" porque el precio máximo proporcionado ("{1} USD") es menor que el precio actual de acceso puntual ("{2} USD") para el tamaño de máquina virtual de acceso puntual de Azure "{3}". | Seleccione un precio máximo mayor. Para más información, consulte la información de precios para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) o [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Valor de precio máximo no válido. Los únicos valores admitidos de precio máximo son -1 o un valor decimal mayor que cero. El valor de precio máximo -1 indica que la máquina virtual de acceso puntual de Azure no se expulsará por razones de precio. | Escriba un precio máximo válido. Para más información, consulte la información de precios para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) o [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | No se permite cambiar el precio máximo con la máquina virtual "{0}" asignada. Desasígnela e inténtelo de nuevo. | Detenga la máquina virtual o cancele su asignación para cambiar el precio máximo. |
| MaxPriceChangeNotAllowed | No se permite el cambio de precio máximo. | No se puede cambiar el precio máximo para esta máquina virtual. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  La máquina virtual de acceso puntual de Azure no es compatible con esta versión de API. | La versión de API debe ser 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  La máquina virtual de acceso puntual de Azure no es compatible para este tamaño de VM: {0}. | Seleccione otro. Para obtener más información, consulte [Máquinas virtuales de acceso puntual de Azure](./spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  El precio máximo solo es compatible con las máquinas virtuales de Azure Spot. | Para obtener más información, consulte [Máquinas virtuales de acceso puntual de Azure](./spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  La solicitud Mover recursos contiene una máquina virtual de acceso puntual de Azure. No compatible. Consulte los detalles del error para conocer los identificadores de máquina virtual. | No puede mover máquinas virtuales de acceso puntual de Azure. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  La solicitud Mover recursos contiene un conjunto de escalado de máquinas virtuales de Azure Spot. No compatible. Consulte los detalles del error para conocer los identificadores de conjunto de escalado de máquinas virtuales. | No puede mover instancias de conjuntos de escalado de máquinas virtuales de acceso puntual de Azure. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Los conjuntos de escalado de máquinas virtuales con el modo de orquestación de VM no admiten máquinas virtuales de acceso puntual de Azure. | Para permitir el uso de instancias de máquinas virtuales de acceso puntual de Azure, establezca el modo de orquestación en el conjunto de escalado de máquinas virtuales. |


**Pasos siguientes** Para más información, consulte [Máquinas virtuales de Spot](./spot-vms.md).