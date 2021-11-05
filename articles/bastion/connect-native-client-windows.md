---
title: Conexión a una máquina virtual mediante el cliente Windows nativo y Azure Bastion
titleSuffix: Azure Bastion
description: Aprenda a conectarse a una máquina virtual desde un equipo Windows mediante Bastion y el cliente Windows nativo.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 11/01/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2e6b0caa3c5540a082bf20e3ed2a11389a4188e4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093765"
---
# <a name="connect-to-a-vm-using-bastion-and-the-native-client-on-your-windows-computer"></a>Conexión a una máquina virtual mediante Bastion y el cliente nativo en el equipo Windows

Azure Bastion ahora ofrece compatibilidad para conectarse a máquinas virtuales de destino en Azure mediante un cliente nativo del equipo local Windows. Esta característica le permite conectarse a las máquinas virtuales de destino a través de Bastion mediante la CLI de Azure y expande las opciones de inicio de sesión para incluir el par de claves SSH local, el nombre de usuario y la contraseña locales y Azure Active Directory (Azure AD). Este artículo le ayuda a configurar Bastion con los valores necesarios y, a continuación, a conectarse a una máquina virtual en la red virtual. Para obtener más información, vea [¿Qué es Azure Bastion?](bastion-overview.md)

> [!NOTE]
> Esta configuración requiere la SKU estándar para Azure Bastion.
>

Actualmente, esta característica tiene las siguientes limitaciones:

* El inicio de sesión en la máquina virtual de destino mediante un puerto o protocolo personalizados aún no está disponible con la compatibilidad de cliente nativo. Si quiere usar un puerto o protocolo personalizados para iniciar sesión en la máquina virtual de destino a través de Bastion, use la experiencia de Azure Portal.

* El inicio de sesión con una clave privada SSH almacenada en Azure Key Vault no se admite con esta característica. Descargue la clave privada en un archivo de la máquina local antes de iniciar sesión en la máquina virtual Linux con un par de claves SSH.

## <a name="prerequisites"></a><a name="prereq"></a>Requisitos previos

Antes de empezar, compruebe que se cumplen los criterios siguientes:

* Está instalada la versión más reciente de los comandos de la CLI. Para más información acerca de la instalación de los comandos de la CLI, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli) y [Introducción a la CLI de Azure](/cli/azure/get-started-with-azure-cli).
* Una red virtual de Azure.
* Una máquina virtual en la red virtual.

## <a name="configure-bastion"></a>Configuración de Bastion

Siga las instrucciones que pertenecen a su entorno.

### <a name="to-modify-an-existing-bastion-host"></a>Para modificar un host bastión existente

Si ya ha configurado Bastion para la red virtual, modifique la siguiente configuración:

1. Navegue hasta la página **Configuración** del recurso de Bastion. Verifique que la SKU sea **Estándar**. Si no lo es, cámbiela a **Estándar** en la lista desplegable.
1. Active la casilla **Native Client Support** (Compatibilidad con cliente nativo) y aplique los cambios.

    :::image type="content" source="./media/connect-native-client-windows/update-host.png" alt-text="Configuración para actualizar un host existente con el cuadro de compatibilidad de cliente nativo seleccionado." lightbox="./media/connect-native-client-windows/update-host-expand.png":::
1. Instale los comandos de la CLI. Revise los [requisitos previos](#prereq) para ver los pasos para instalar la versión más reciente de los comandos de la CLI.

### <a name="to-configure-a-new-bastion-host"></a>Para configurar un nuevo host bastión

Si aún no tiene un host bastión configurado, consulte [Creación de un host bastión](tutorial-create-host-portal.md#createhost). Al configurar el host bastión, especifique las siguientes opciones:

1. En la pestaña **Aspectos básicos**, en **Detalles de la instancia -> Nivel**, seleccione **Estándar** para crear un host bastión mediante la SKU estándar.

   :::image type="content" source="./media/connect-native-client-windows/standard.png" alt-text="Configuración para un nuevo host bastión con la SKU estándar seleccionada." lightbox="./media/connect-native-client-windows/standard.png":::
1. En la pestaña **Avanzada**, active la casilla para **Native Client Support** (Compatibilidad con cliente nativo).

   :::image type="content" source="./media/connect-native-client-windows/new-host.png" alt-text="Configuración para un nuevo host bastión con el cuadro de compatibilidad con cliente nativo seleccionado." lightbox="./media/connect-native-client-windows/new-host-expand.png":::
1. Instale los comandos de la CLI. Revise los [requisitos previos](#prereq) para ver los pasos para instalar la versión más reciente de los comandos de la CLI.

## <a name="verify-roles-and-ports"></a>Comprobación de roles y puertos

Compruebe que los siguientes roles y puertos están configurados para conectarse.

### <a name="required-roles"></a>Roles necesarios

* Rol Lector en la máquina virtual.
* Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual.
* Rol Lector en el recurso de Azure Bastion.

### <a name="ports"></a>Puertos

Para conectarse a una Windows VM mediante la compatibilidad con cliente nativo, debe tener abiertos los siguientes puertos en la Windows VM:

* Puerto de entrada: RDP (3389)

Para conectarse a una Linux VM mediante la compatibilidad con cliente nativo, debe tener abiertos los siguientes puertos en la Linux VM:

* Puerto de entrada: SSH (22)

Si quiere usar un puerto personalizado para conectarse a la máquina virtual de destino, siga las instrucciones de Azure Portal en su lugar.

## <a name="connect-to-a-vm"></a><a name="connect"></a>Conexión a una máquina virtual

Esta sección le ayuda a conectarse a la máquina virtual. Siga los pasos correspondientes al tipo de máquina virtual a la que quiere conectarse.

### <a name="connect-to-a-linux-vm"></a>Conexión a una máquina virtual Linux

1. Inicie sesión en su cuenta de Azure y seleccione la suscripción que contiene el recurso de Bastion.

   ```azurecli-interactive
   az login
   az account list
   az account set --subscription "<subscription ID>"
   ```

1. Inicie sesión en la máquina virtual de Linux de destino mediante una de las siguientes opciones:

   * Si va a iniciar sesión en una máquina virtual conectada a Azure AD (AADJ), use el ejemplo siguiente. Para obtener más información sobre cómo usar Azure AD para iniciar sesión en las Linux VM de Azure, consulte [VM Linux de Azure y Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md).

     ```azurecli-interactive
     az network bastion ssh --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type  "AAD"
     ```

   * Si inicia sesión con un nombre de usuario y una contraseña locales, use el ejemplo siguiente:

      ```azurecli-interactive
      az network bastion ssh "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "password" --username "<Username>" --password "<Password>"
      ```

   * Si inicia sesión con un par de claves SSH, use el ejemplo siguiente.

      ```azurecli-interactive
      az network bastion ssh "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "ssh-key" --username "<Username>" --ssh-key "<Filepath>"
      ```

### <a name="connect-to-a-windows-vm"></a>Conexión a una máquina virtual Windows

1. Inicie sesión en su cuenta de Azure y seleccione la suscripción que contiene el recurso de Bastion.

   ```azurecli-interactive
   az login
   az account list
   az account set –subscription "<subscription ID>"

1. Log into your target Windows VM using one of the following options:

   * If you are logging into an Azure AD-joined (AADJ) VM, use the following command. To learn more about how to use Azure AD to log into your Azure Windows VMs, see [Azure Windows VMs and Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md).

      ```azurecli-interactive
      az network bastion rdp --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "AAD"
      ```

   * Si inicia sesión con un nombre de usuario y una contraseña locales:

      ```azurecli-interactive
      az network bastion rdp "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "password" --username "<Username>" --password "<Password>" 
      ```

1. Una vez que inicie sesión en la máquina virtual de destino, el cliente nativo del equipo local se abrirá con la sesión de máquina virtual (**mstc** para sesiones RDP y **az ssh** para sesiones SSH).

## <a name="next-steps"></a>Pasos siguientes

Lea el artículo sobre [preguntas frecuentes de Bastion](bastion-faq.md).
