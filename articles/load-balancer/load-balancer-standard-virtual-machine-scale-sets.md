---
title: Adición de reglas para Azure Standard Load Balancer y conjuntos de escalado de máquinas virtuales
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: Con esta ruta de aprendizaje, empiece a usar Azure Standard Load Balancer y conjuntos de escalado de máquinas virtuales.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 2ba2d00d1cbb025b665c2c9c6cb929f8b46a1f33
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723672"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>Adición de reglas para Azure Load Balancer con conjuntos de escalado de máquinas virtuales

Al trabajar con conjuntos de escalado de máquinas virtuales y Azure Load Balancer, debe tener en cuenta las pautas siguientes.

## <a name="port-forwarding-and-inbound-nat-rules"></a>Reglas de enrutamiento de puerto y reglas NAT de entrada

Después de crear el conjunto de escalado, no se puede modificar el puerto de back-end para una regla de equilibrio de carga que se usa en un sondeo de estado del equilibrador de carga. Para cambiar el puerto, quite el sondeo de estado; para ello, actualice el conjunto de escalado de máquinas virtuales y actualice el puerto. Luego vuelva a configurar el sondeo de estado.

Al usar el conjunto de escalado de máquinas virtuales en el grupo de back-end del equilibrador de carga, las reglas NAT de entrada predeterminadas se crean automáticamente.
  
## <a name="inbound-nat-pool"></a>Grupo NAT de entrada

Cada conjunto de escalado de máquinas virtuales debe tener al menos un grupo NAT de entrada. Un grupo NAT de entrada es una colección de reglas NAT de entrada. Un grupo NAT de entrada no admite varios conjuntos de escalado de máquinas virtuales.

## <a name="load-balancing-rules"></a>Reglas de equilibrio de carga

Al usar el conjunto de escalado de máquinas virtuales en el grupo de back-end del equilibrador de carga, la regla de equilibrio de carga predeterminada se crea automáticamente.

## <a name="virtual-machine-scale-set-instance-level-ips"></a>Direcciones IP de la instancia del conjunto de escalado de máquinas virtuales

Cuando los conjuntos de escalado de máquinas virtuales con [IP públicas por instancia](../virtual-network/ip-services/public-ip-address-prefix.md) se crean con un equilibrador de carga delante, la SKU de las IP de la instancia viene determinada por la SKU del equilibrador de carga (es decir, Básico o Estándar).  Tenga en cuenta que cuando se usa un equilibrador de carga estándar, las IP de instancia individuales son todas del tipo Estándar "sin zona" (aunque el front-end del equilibrador de carga podría ser zonal o con redundancia de zona).

## <a name="outbound-rules"></a>Reglas de salida

Para crear una regla de salida para un grupo de back-end al que ya se hace referencia mediante una regla de equilibrio de carga, seleccione **No** en **Crear reglas de salida implícitas** en Azure Portal cuando se cree la regla de equilibrio de carga de entrada.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Captura de pantalla que muestra la creación de reglas de equilibrio de carga." border="true":::

Use los siguientes métodos para implementar un conjunto de escalado de máquinas virtuales con una instancia existente de Load Balancer:

* [Configuración de un conjunto de escalado de máquinas virtuales con una instancia existente de Azure Load Balancer mediante Azure Portal](./configure-vm-scale-set-portal.md)
* [Configuración de un conjunto de escalado de máquinas virtuales con una instancia existente de Azure Load Balancer mediante Azure PowerShell](./configure-vm-scale-set-powershell.md)
* [Configuración de un conjunto de escalado de máquinas virtuales con una instancia existente de Azure Load Balancer mediante la CLI de Azure](./configure-vm-scale-set-cli.md)
* [Actualización o eliminación de una instancia de Azure Load Balancer existente usada por un conjunto de escalado de máquinas virtuales](./update-load-balancer-with-vm-scale-set.md)
