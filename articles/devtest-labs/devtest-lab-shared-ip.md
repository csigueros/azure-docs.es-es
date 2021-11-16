---
title: Descripción de las direcciones IP compartidas
description: Obtenga información sobre cómo Azure DevTest Labs usa direcciones IP compartidas para minimizar las direcciones IP públicas necesarias para acceder a las máquinas virtuales de su laboratorio.
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 06aac18fb7016a7eb5bee938a4d9988719f86a2f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056949"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Direcciones IP compartidas en Azure DevTest Labs

Las máquinas virtuales de Azure DevTest Labs pueden compartir una dirección IP pública para minimizar el número de direcciones IP públicas que necesita para acceder a las máquinas virtuales de laboratorio.  En este artículo se describe cómo funcionan las direcciones IP compartidas y cómo configurarlas.

## <a name="shared-ip-settings"></a>Configuración de direcciones IP compartidas

Puede crear un laboratorio de DevTest Labs en una red virtual que tenga una o varias subredes. La subred predeterminada tiene la opción **Habilitar IP pública compartida** establecida en **Sí**.  Esta configuración crea una dirección IP pública para toda la subred. Las máquinas virtuales de esta subred usan de forma predeterminada la dirección IP compartida.

Para obtener más información sobre cómo configurar redes virtuales y subredes, vea [Configuración de una red virtual en Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Captura de pantalla que muestra la configuración de la dirección IP compartida en la página Subred de laboratorio.](media/devtest-lab-shared-ip/lab-subnet.png)

Para los laboratorios existentes, puede activar o establecer esta opción si selecciona **Configuración y directivas** en el panel de navegación izquierdo del laboratorio y, a continuación, selecciona **Redes virtuales** en **Recursos externos**. Seleccione una red virtual de la lista para ver la configuración de IP compartida de sus subredes.

Para cambiar la configuración, seleccione una subred de la lista y cambie **Habilitar IP pública compartida** a **Sí** o **No**.

Al crear una máquina virtual, puede acceder a esta configuración en la página **Configuración avanzada** junto a **dirección IP**.

![Captura de pantalla que muestra la configuración de IP compartida en Configuración avanzada al crear una máquina virtual.](media/devtest-lab-shared-ip/new-vm.png)

- **Compartidas:** todas las máquinas virtuales que cree como **Compartidas** se van al mismo grupo de recursos. El grupo de recursos tiene una dirección IP asignada que usan todas las máquinas virtuales del grupo de recursos.
- **Públicas:** cada máquina virtual pública tiene su propia dirección IP y grupo de recursos.
- **Privadas:** todas las máquinas virtuales que se crean usan una dirección IP privada. No se puede conectar a estas máquinas virtuales desde Internet mediante el protocolo Escritorio remoto (RDP).

Cuando agrega una máquina virtual con dirección IP compartida a una subred, DevTest Labs agrega automáticamente la máquina virtual a un equilibrador de carga y asigna a la máquina virtual un número de puerto TCP en la dirección IP pública. El número de puerto se reenvía al puerto de Secure Shell (SSH) en la máquina virtual.

## <a name="use-a-shared-ip"></a>Uso de una dirección IP compartida

- **Usuarios de Windows:** seleccione el botón **Conectar** en la página de **información general** de la máquina virtual para descargar un archivo RDP preconfigurado y acceder a la máquina virtual.

- **Usuarios de Linux:** Secure Shell (SSH) permite conectarse a la máquina virtual con la dirección IP o el nombre de dominio completo, seguido de dos puntos y del número de puerto. Por ejemplo, en la captura de pantalla siguiente se muestra una dirección de conexión SSH de `contosolab21000000000000.westus3.cloudapp.azure.com:65013`.

  ![Captura de pantalla que muestra las opciones de conexión RDP y SSH en la página de información general de una máquina virtual.](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>Pasos siguientes

- [Definición de directivas de laboratorio en Azure DevTest Labs](devtest-lab-set-lab-policy.md)
- [Configuración de una red virtual en Azure DevTest Labs](devtest-lab-configure-vnet.md)
