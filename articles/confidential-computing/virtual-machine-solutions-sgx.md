---
title: Soluciones de computación confidencial de Azure en máquinas virtuales
description: Obtenga información sobre las soluciones de computación confidencial de Azure en máquinas virtuales.
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1805b153d33915c2ec252c0e4a932eceb710b59f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331906"
---
# <a name="solutions-on-azure-for-intel-sgx"></a>Soluciones en Azure para Intel SGX

En este artículo se trata información sobre la implementación de máquinas virtuales Intel SGX.

### <a name="current-available-sizes-and-regions"></a>Tamaños y regiones disponibles actualmente

Para obtener una lista de todos los tamaños de máquina virtual de Intel SGX en las regiones disponibles y en las zonas de disponibilidad, ejecute el siguiente comando en la [CLI de Azure](/cli/azure/install-azure-cli-windows):

```azurecli-interactive
az vm list-skus `
    --size Standard_DC `
    --all `
    --output table
```

### <a name="dedicated-host-requirements"></a>Requisitos de host dedicados

La implementación de una máquina virtual **Standard_DC8_v2**, **Standard_DC48s_v3**, **Standard_DC48ds_v3** ocupará el host completo y no se compartirá con otros inquilinos o suscripciones. Esta familia de SKU de máquina virtual proporciona el aislamiento que puede necesitar para cumplir los requisitos de cumplimiento normativo y seguridad que normalmente se cumplen con un servicio de host dedicado. Al elegir estos tamaños, el servidor host físico asignará todos los recursos de hardware disponibles, incluida la memoria EPC, solo a la máquina virtual. Esta implementación no es la misma que el servicio [Azure Dedicated Host](../virtual-machines/dedicated-hosts.md) que proporcionan otras familias de máquinas virtuales de Azure.


## <a name="deployment-considerations"></a>Consideraciones de la implementación

Siga un tutorial de inicio rápido para implementar una máquina virtual de la serie DCsv2 en menos de 10 minutos. 

- **Suscripción de Azure**: para implementar una instancia de máquina virtual de computación confidencial, considere la posibilidad de usar una suscripción de pago por uso u otra opción de compra. Si usa una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), no tendrá cuota para la cantidad adecuada de núcleos de proceso de Azure.

- **Precios y disponibilidad regional**: encuentre los precios de las máquinas virtuales DCsv2, DCsv3 y DCdsv3 en la [página de precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Para ver la disponibilidad en las regiones de Azure, consulte [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) .

- **Cuota de núcleos**: quizás tenga que aumentar la cuota de núcleos de su suscripción de Azure partiendo del valor predeterminado. La suscripción también podría limitar el número de núcleos que se pueden implementar en ciertas familias de tamaño de máquina virtual, como la serie DCsv2. Para solicitar un aumento de cuota, [abra una solicitud de soporte técnico al cliente en línea](../azure-portal/supportability/per-vm-quota-requests.md) sin cargo alguno. Tenga en cuenta que los límites predeterminados pueden variar según la categoría de suscripción.

  > [!NOTE]
  > Si tiene necesidades de capacidad a gran escala, póngase en contacto con el soporte técnico de Azure. Las cuotas de Azure son límites de crédito, no garantías de capacidad. Independientemente de la cuota, solamente se le cobrarán los núcleos que use.
  
- **Cambio de tamaño**: debido a su hardware especializado, solo se puede cambiar el tamaño de las instancias dentro de la misma familia de tamaño. Por ejemplo, una máquina virtual de la serie DCsv2 solo se puede cambiar de un tamaño de serie DCsv2 a otro. 

- **Imagen**: para proporcionar compatibilidad con Intel Software Guard Extension (Intel SGX) en instancias de proceso confidenciales, todas las implementaciones deben ejecutarse en imágenes de generación 2. La computación confidencial de Azure admite cargas de trabajo que se ejecutan en Ubuntu 20.04 Gen 2, Ubuntu 18.04 Gen 2 y Windows Server 2019 Gen 2. Lea acerca de la [compatibilidad con las máquinas virtuales de generación 2 en Azure](../virtual-machines/generation-2.md) para obtener más información sobre los escenarios admitidos y no admitidos. 

- **Almacenamiento**: la serie DCsv2 admite SSD estándar y SSD prémium, a excepción de DC8_v2. Las series DCsv3 y DCdsv3 admiten SSD estándar, SSD prémium y Disco Ultra.

- **Cifrado de disco**: las instancias de proceso confidenciales no admiten el cifrado de disco en este momento. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Consideraciones sobre la alta disponibilidad y la recuperación ante desastres

Si usa máquinas virtuales en Azure, es responsable de implementar una solución de recuperación ante desastres y alta disponibilidad para evitar tiempo de inactividad. 

La computación confidencial de Azure no admite la redundancia de zona a través de Availability Zones en este momento. Para obtener la máxima disponibilidad y redundancia para la computación confidencial, use [conjuntos de disponibilidad](../virtual-machines/availability-set-overview.md). Debido a las restricciones de hardware, los conjuntos de disponibilidad para las instancias de computación confidencial solo pueden tener un máximo de 10 dominios de actualización. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Implementación con plantilla de Azure Resource Manager (ARM)

Azure Resource Manager es el servicio de implementación y administración para Azure. Proporciona una capa de administración que le permite crear, actualizar y eliminar recursos de su suscripción de Azure. Puede usar las características de administración, como el control de acceso, la auditoría y las etiquetas, para proteger y organizar los recursos después de la implementación.

Para información sobre las plantillas de Resource Manager, consulte [Información general de Template Deployment](../azure-resource-manager/templates/overview.md).

Para realizar la implementación mediante plantillas de ARM, use el [recurso de máquina virtual](../virtual-machines/windows/template-description.md). Asegúrese de que especifica las propiedades correctas para **vmSize** y para **imageReference**.

### <a name="vm-size"></a>Tamaño de VM

Especifique uno de los siguientes tamaños en la plantilla de ARM del recurso de máquina virtual. Esta cadena se coloca como **vmSize** en las **propiedades**.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2",
        "Standard_DC1s_v3",
        "Standard_DC2s_v3",
        "Standard_DC4s_v3",
        "Standard_DC8s_v3",
        "Standard_DC16s_v3",
        "Standard_DC24s_v3",
        "Standard_DC32s_v3",
        "Standard_DC48s_v3",
        "Standard_DC1ds_v3",
        "Standard_DC2ds_v3",
        "Standard_DC4ds_v3",
        "Standard_DC8ds_v3",
        "Standard_DC16ds_v3",
        "Standard_DC24ds_v3",
        "Standard_DC32ds_v3",
        "Standard_DC48ds_v3",
      ],
```

### <a name="gen2-os-image"></a>Imagen de sistema operativo Gen2

En **Propiedades**, también tendrá que hacer referencia a una imagen en **storageProfile**. Use *solo una* de las siguientes imágenes para su valor de **imageReference**.

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "20_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "20_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Pasos siguientes 

En este artículo, ha aprendido sobre las calificaciones y configuraciones necesarias al crear una máquina virtual Intel SGX.

> [!div class="nextstepaction"]
> [Creación de una máquina virtual Intel SGX mediante Azure Marketplace](quick-create-marketplace.md)
