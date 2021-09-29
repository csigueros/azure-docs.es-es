---
title: Descripción de las direcciones IP compartidas
description: Obtenga información sobre cómo Azure DevTest Labs usa direcciones IP compartidas para minimizar las direcciones IP públicas necesarias para acceder a las máquinas virtuales de su laboratorio.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: e3d5afd69b898a4f17440a81fc41a065c1c79a3e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659785"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Direcciones IP compartidas en Azure DevTest Labs

Las máquinas virtuales de laboratorio de Azure DevTest Labs comparten la misma dirección para minimizar el número de direcciones IP públicas necesarias para acceder a estas máquinas virtuales.  En este artículo se describe cómo funcionan las direcciones IP compartidas y sus opciones de configuración relacionadas.

## <a name="shared-ip-setting"></a>Configuración de IP compartidas

Cuando se crea un laboratorio, lo hace en una subred de una red virtual.  De forma predeterminada, esta subred se crea con la opción **Habilitar IP pública compartida** establecida en *Sí*.  Esta configuración crea una dirección IP pública para toda la subred.  Para obtener más información sobre cómo configurar redes virtuales y subredes, vea [Configuración de una red virtual en Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nueva subred de laboratorio](media/devtest-lab-shared-ip/lab-subnet.png)

Para habilitar esta opción en laboratorios existentes, seleccione **Configuration and policies (Directivas y configuración) > Redes virtuales**. Después, seleccione una red virtual de la lista y elija **HABILITAR IP PÚBLICA COMPARTIDA** para la subred seleccionada. También puede deshabilitar esta opción en cualquier laboratorio si no quiere compartir una dirección IP pública en todas las máquinas virtuales del laboratorio.

Las máquinas virtuales creadas en este laboratorio usarán una dirección IP compartida de forma predeterminada.  Al crear la máquina virtual, este valor se puede encontrar en la página **Configuración avanzada**, en **Configuración de dirección IP**.

![Nueva máquina virtual](media/devtest-lab-shared-ip/new-vm.png)

- **Compartido:** todas las máquinas virtuales creadas como **Compartido** se colocan en un solo grupo de recursos. Se asigna una sola dirección IP al grupo de recursos y todas las máquinas virtuales usarán esa dirección IP.
- **Público:** todas las máquinas virtuales que se crean tienen su propia dirección IP y se crean en su propio grupo de recursos.
- **Privado:** todas las máquinas virtuales que se crean usan una dirección IP privada. No es posible conectarse a estas máquinas virtuales directamente desde Internet con Escritorio remoto.

Siempre que se agrega una máquina virtual con la dirección IP compartida a la subred, DevTest Labs agrega automáticamente la máquina virtual a un equilibrador de carga y le asigna un número de puerto TCP en la dirección IP pública, que se reenvía al puerto RDP de la máquina virtual.  

## <a name="using-the-shared-ip"></a>Uso de la dirección IP compartida

- **Usuarios de Linux:** use SSH para conectarse a la máquina virtual con la dirección IP o el nombre de dominio completo, seguido de dos puntos y del puerto. Por ejemplo, en la imagen siguiente, la dirección RDP para conectarse a la máquina virtual es `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`.

  ![Ejemplo de máquina virtual](media/devtest-lab-shared-ip/vm-info.png)

- **Usuarios de Windows:** en Azure Portal, seleccione el botón **Conectar** para descargar un archivo RDP preconfigurado y acceder a la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

* [Definición de directivas de laboratorio en Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configuración de una red virtual en Azure DevTest Labs](devtest-lab-configure-vnet.md)
