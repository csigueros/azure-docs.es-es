---
title: Habilitación de una conexión de explorador en máquinas virtuales de Azure DevTest Labs
description: Integre Azure Bastion con DevTest Labs para permitir el acceso a máquinas virtuales (VM) de laboratorio a través de un explorador.
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 876a72548c70f3e7717c75973edee802e584fca2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442977"
---
# <a name="enable-browser-connection-to-devtest-labs-vms"></a>Habilitación de la conexión de explorador a máquinas virtuales de DevTest Labs

Azure DevTest Labs se integra con [Azure Bastion](../bastion/index.yml) para permitir la conexión con máquinas virtuales de laboratorio a través de un explorador. Como propietario del laboratorio, puede habilitar el acceso de explorador a todas las máquinas virtuales de laboratorio a través de Azure Bastion.

Azure Bastion proporciona conectividad segura e ininterrumpida de Protocolo de escritorio remoto (RDP) y Secure Shell (SSH) a través de la Seguridad de la capa de transporte (TLS), directamente desde Azure Portal. No necesita ningún otro cliente, agente o software para conectarse a las máquinas virtuales de laboratorio a través del explorador. Las máquinas virtuales no necesitan direcciones IP públicas.

En este artículo se tratan dos maneras diferentes de habilitar la conectividad de explorador de Azure Bastion para las máquinas virtuales de DevTest Labs:

- Puede crear una nueva red virtual habilitada para Azure Bastion para el laboratorio y sus máquinas virtuales.
- Puede implementar Azure Bastion en la red virtual de laboratorio existente.

## <a name="prerequisites"></a>Prerrequisitos

- Tener o [crear un laboratorio](tutorial-create-custom-lab.md#create-a-lab) en DevTest Labs.
- Para utilizar el acceso de explorador de Azure Bastion, los usuarios del laboratorio deben tener el rol **Lector** en el host de Azure Bastion y en la red virtual del laboratorio que tiene configurado Azure Bastion.

## <a name="option-1-connect-a-lab-to-an-azure-bastion-enabled-virtual-network"></a>Opción 1: Conectar un laboratorio a una red virtual habilitada para Azure Bastion

En primer lugar, cree una nueva red virtual con una subred de Azure Bastion y otra subred en ella. Una subred de Azure Bastion no permite crear recursos que no sean de Azure Bastion en ella, por lo que se necesita la otra subred para crear máquinas virtuales de laboratorio.

1. En Azure Portal, busque y seleccione **Redes virtuales**.
1. Seleccione **+ Crear** en la parte superior de la página **Redes virtuales**.
1. En la pantalla **Crear red virtual**, escriba un **Nombre** para la nueva red virtual y seleccione los mismos valores para **Suscripción**, **Grupo de recursos** y **Región** que los de su laboratorio.
1. Seleccione **Siguiente: Direcciones IP**.
1. En la pestaña **Direcciones IP**, ya hay una subred, **default**. Haga clic en **Agregar subred**.
1. En el panel **Agregar subred**, escriba *AzureBastionSubnet* en **Nombre**.
1. En **Intervalo de direcciones de subred**, escriba un intervalo de direcciones que se encuentre dentro del espacio de direcciones de la red virtual, pero que no se superponga con la subred predeterminada. Si es necesario, puede agregar nuevos espacios de direcciones en los campos en blanco de la página **Crear red virtual**.
1. Seleccione **Agregar**.

   ![Captura de pantalla que muestra la creación de la subred AzureBastionSubnet.](media/enable-browser-connection-lab-virtual-machines/create-subnet.png)

1. Seleccione **Revisar y crear** y, una vez superada la validación, seleccione **Crear**.
1. Una vez creada la nueva red virtual, vaya a su página, seleccione **Subredes** en el panel de navegación izquierdo y confirme que hay dos subredes: **default** y **AzureBastionSubnet**.

   ![Captura de pantalla que muestra dos subredes en la red virtual de Azure Bastion.](media/enable-browser-connection-lab-virtual-machines/second-subnet.png)

A continuación, conecte el laboratorio a la nueva red virtual:

1. En la página **Información general** del laboratorio, seleccione **Configuración y directivas** en el panel de navegación izquierdo.
1. En la página **Configuración y directivas**, seleccione **Redes virtuales** en **Recursos externos** en el panel de navegación izquierdo.
1. En la página **Configuración y directivas | Redes virtuales**, seleccione **Agregar** en la parte superior.
1. En la página **Red virtual**, elija **Seleccionar red virtual**.
1. En la página **Elegir red virtual**, seleccione la red virtual habilitada para Azure Bastion que acaba de crear.
1. Seleccione **Guardar** en la parte superior de la página **Red virtual**.
1. En la página **Configuración y directivas | Redes virtuales**, quite cualquier red virtual anterior del laboratorio. Seleccione **...** junto a esa red virtual, seleccione **Eliminar** y, a continuación, seleccione **Sí**. 

   ![Captura de pantalla que muestra la eliminación de la red virtual de laboratorio anterior.](media/enable-browser-connection-lab-virtual-machines/add-virtual-network.png)

Habilite la creación de máquinas virtuales en la subred que no es de Azure Bastion:

1. En la página **Configuración y directivas | Redes virtuales**, seleccione la red virtual habilitada para Azure Bastion.
1. En la página **Red virtual**, asegúrese de que aparecen las subredes **AzureBastionSubnet** y **default**. Si no ve ambas subredes, cierre la página y vuelva a abrirla.
1. Seleccione la subred **default**.
1. En la pantalla **Subred de laboratorio**, en **Usar en la creación de máquinas virtuales**, seleccione **Sí** y luego seleccione **Guardar**. Ahora puede crear máquinas virtuales en la subred predeterminada de la red virtual de laboratorio.

   ![Captura de pantalla que muestra cómo habilitar la creación de VM en la subred predeterminada.](./media/enable-browser-connection-lab-virtual-machines/enable-vm-creation-subnet.png)

## <a name="option-2-deploy-azure-bastion-in-a-labs-existing-virtual-network"></a>Opción 2: Implementación de Azure Bastion en la red virtual existente de un laboratorio

En primer lugar, cree una nueva subred de Azure Bastion en la red virtual existente de su laboratorio:

1. En Azure Portal, busque y seleccione **Redes virtuales**.
1. Seleccione la red virtual de su laboratorio en la lista de la página **Redes virtuales**.
1. En la página de redes virtuales, seleccione **Subredes** en el panel de navegación izquierdo.
1. En la página **Subredes**, seleccione **+Subred** en el menú superior.
1. En la pantalla **Agregar subred**, escriba *AzureBastionSubnet* en **Nombre**.
1. En **Intervalo de direcciones de subred**, escriba un intervalo de direcciones que se encuentre dentro del espacio de direcciones de la red virtual, pero que no se superponga con la subred de laboratorio existente.
   >[!TIP]
   >Es posible que tenga que cancelar este cuadro de diálogo; seleccione **Espacio de direcciones** en el panel de navegación izquierdo de la red virtual y cree un nuevo espacio de direcciones para la subred.
1. Seleccione **Guardar**.

   ![Captura de pantalla que muestra cómo agregar una subred a la red virtual existente.](./media/enable-browser-connection-lab-virtual-machines/add-subnet.png)

A continuación, implemente el host de Azure Bastion en la nueva subred de Azure Bastion:

1. En Azure Portal, busque y seleccione **Bastiones**.
1. En la página **Bastiones** seleccione **+ Crear** en la parte superior.
1. En la página **Crear una instancia de Bastion**, escriba un **Nombre** y seleccione los mismos valores para **Suscripción**, **Grupo de recursos** y **Región** que los del laboratorio.
1. En **Redes virtuales**, seleccione la red virtual del laboratorio en la lista desplegable y asegúrese de que **AzureBastionSubnet** está seleccionado en **Subred**.
1. Seleccione **Revisar y crear** y, una vez superada la validación, seleccione **Crear**.

   ![Captura de pantalla que muestra la implementación de Azure Bastion en la red virtual existente.](./media/enable-browser-connection-lab-virtual-machines/create-bastion.png)

## <a name="connect-to-lab-vms-through-azure-bastion"></a>Conexión a las máquinas virtuales del laboratorio a través de Azure Bastion

Una vez que implemente Azure Bastion en la red virtual del laboratorio, habilite las conexiones de explorador para el laboratorio:

1. En la página del laboratorio **Información general**, seleccione **Configuración y directivas** y, a continuación, seleccione **Conexión del explorador** en **Configuración**.
1. En la página **Conexión del explorador**, seleccione **Activado**.
1. En la parte superior de la página, seleccione **Guardar**.

   ![Captura de pantalla que muestra cómo habilitar la conexión del explorador.](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

Para conectarse a una máquina virtual de laboratorio mediante Azure Bastion:

1. Seleccione una máquina virtual de laboratorio en **Mis máquinas virtuales**, en la página **Información general** del laboratorio.
1. En la parte superior de la página de la máquina virtual, seleccione **Conexión del explorador**.
1. En el panel **Conexión del explorador**, escriba el nombre de usuario y la contraseña de la máquina virtual y seleccione **Conectar**.

## <a name="next-steps"></a>Pasos siguientes
- [¿Qué es Azure Bastion?](../bastion/bastion-overview.md)
- [Agregar una máquina virtual al laboratorio](devtest-lab-add-vm.md)
