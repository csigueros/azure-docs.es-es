---
title: Búsqueda y eliminación de NIC de Azure no asociadas
description: Cómo buscar y eliminar NIC de Azure no asociadas a máquinas virtuales con la CLI de Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 35c2bb34b3fa19a8bc127f45fe00223686f820b6
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691901"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Cómo buscar y eliminar tarjetas de interfaz de red (NIC) no asociadas en máquinas virtuales de Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Conjuntos de escalado flexibles 

Cuando se elimina una máquina virtual (VM) en Azure, las tarjetas de interfaz de red (NIC) no se eliminan de forma predeterminada. Si crea y elimina varias máquinas virtuales, las NIC no usadas siguen utilizando las concesiones de dirección IP internas. Como resultado, cuando cree otras NIC de máquina virtual, es posible que no puedan obtener una concesión de IP en el espacio de direcciones de la subred. En este artículo se muestra cómo buscar y eliminar NIC no asociadas.

## <a name="find-and-delete-unattached-nics"></a>Búsqueda y eliminación de NIC no conectadas

La propiedad *virtualMachine* de una NIC almacena el identificador y el grupo de recursos de la máquina virtual a la que está asociada la NIC. El script siguiente recorre en bucle todas las NIC de una suscripción y comprueba si la propiedad *virtualMachine* es null. Si esta propiedad es null, la NIC no está asociada a una máquina virtual.

Para ver todas las NIC no asociadas, se recomienda ejecutar primero el script con la variable *deleteUnattachedNics* establecida en *0*. Para eliminar todas las NIC no asociadas después de revisar la salida de la lista, ejecute el script con *deleteUnattachedNics* establecido en *1*.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo crear y administrar redes virtuales en Azure, consulte [Creación y administración de redes de máquina virtual](tutorial-virtual-network.md).
