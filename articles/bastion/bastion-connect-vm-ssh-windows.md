---
title: Conexión mediante SSH a una máquina virtual Windows
titleSuffix: Azure Bastion
description: Aprenda a usar Azure Bastion para conectarse a una máquina virtual Windows mediante SSH.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: ca2425aad93d5878060c723d4c43f41765483a65
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699482"
---
# <a name="create-an-ssh-connection-to-a-windows-vm-using-azure-bastion-preview"></a>Creación de una conexión SSH a una máquina virtual Windows mediante Azure Bastion (versión preliminar)

En este artículo se muestra cómo crear de forma segura y sin problemas una conexión SSH a las máquinas virtuales Windows ubicadas en una red virtual de Azure directamente desde Azure Portal. Cuando se usa Azure Bastion, las máquinas virtuales no necesitan un cliente, un agente o software adicional. También puede conectarse a una máquina virtual Windows mediante RDP. Si desea información, consulte [Creación de una conexión RDP a una máquina virtual Windows](bastion-connect-vm-rdp-windows.md).

Azure Bastion proporciona conectividad segura a todas las máquinas virtuales de la red virtual en la que se aprovisiona. El uso de Azure Bastion protege las máquinas virtuales frente a la exposición de los puertos de RDP/SSH al mundo exterior, al tiempo que ofrece acceso seguro mediante RDP/SSH. Para obtener más información, vea [¿Qué es Azure Bastion?](bastion-overview.md)

> [!NOTE]
> Si desea crear una conexión SSH a una máquina virtual Windows, se debe configurar Azure Bastion mediante la SKU estándar. La SKU estándar se encuentra actualmente en versión preliminar.
>

Al conectarse a una máquina virtual Windows mediante SSH, puede usar el nombre de usuario y la contraseña, y las claves SSH para la autenticación. Puede conectarse a la máquina virtual con claves SSH mediante una de estas opciones:

* Una clave privada que se escribe manualmente.
* Un archivo que contiene la información de la clave privada.

La clave privada SSH debe tener un formato que empieza con `"-----BEGIN RSA PRIVATE KEY-----"` y finaliza con `"-----END RSA PRIVATE KEY-----"`.

## <a name="prerequisites"></a>Prerrequisitos

Asegúrese de haber configurado un host de Azure Bastion para la red virtual donde reside la máquina virtual. Para más información, consulte [Create an Azure Bastion host](tutorial-create-host-portal.md) (Creación de un host de Azure Bastion). Cuando el servicio Bastion se aprovisiona e implementa en la red virtual, puede usarlo para conectarse a cualquier máquina virtual de esta red virtual. 

Para conectarse mediante SSH a una máquina virtual Windows, también debe asegurarse de que:
* La máquina virtual Windows ejecuta Windows Server 2019 o posterior.
* Tiene instalado el servidor OpenSSH y ejecutándose en la máquina virtual Windows. Para información sobre cómo hacerlo, consulte el artículo sobre la [instalación de OpenSSH](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse).
* Azure Bastion está configurado para utilizar la SKU estándar.

### <a name="required-roles"></a>Roles necesarios

Para crear una conexión, se requieren los siguientes roles:

* Rol de lector en la máquina virtual
* Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual
* Rol de lector en el recurso de Azure Bastion

### <a name="ports"></a>Puertos

Para conectarse a la máquina virtual Windows mediante SSH, debe tener abiertos estos puertos en la máquina virtual:

* Puerto de entrada: SSH (22) *o*
* Puerto de entrada: valor personalizado (tendrá que especificar este puerto personalizado al conectarse a la máquina virtual por medio de Azure Bastion)

### <a name="supported-configurations"></a>Configuraciones admitidas

Actualmente, Azure Bastion solo admite la conexión a máquinas virtuales Windows mediante SSH a través de **OpenSSH**.

## <a name="connect-using-username-and-password"></a><a name="username"></a>Conexión: mediante un nombre de usuario y una contraseña

1. Abra [Azure Portal](https://portal.azure.com). Vaya a la máquina virtual a la que quiere conectarse y, a continuación, haga clic en **Conectar** y seleccione **Bastion** en la lista desplegable.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="Captura de pantalla de la información general de una máquina virtual en Azure Portal con la opción Conectar seleccionada." lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::

1. Después de seleccionar Bastion, seleccione **Usar Bastion**. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](./quickstart-host-portal.md) (Configuración de Bastion).
1. En la página **Conectar mediante Azure Bastion,** despliegue la sección **Configuración de la conexión** y seleccione **SSH**. Si tiene previsto usar un puerto de entrada distinto del puerto SSH estándar (22), escriba el **Puerto**.

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings.png" alt-text="Captura de pantalla en la que se muestra la configuración de la conexión." lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings.png":::

1. Escriba su **nombre de usuario** y **contraseña**, y haga clic en **Conectar** para conectarse a la VM.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/authentication.png" alt-text="Captura de pantalla de la autenticación de contraseña." lightbox="./media/bastion-connect-vm-ssh-windows/authentication.png":::

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Conexión: con una clave privada escrita

1. Abra [Azure Portal](https://portal.azure.com). Vaya a la máquina virtual a la que quiere conectarse y, a continuación, haga clic en **Conectar** y seleccione **Bastion** en la lista desplegable.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="Captura de pantalla en la que se muestra la información general de una máquina virtual en Azure Portal con la opción Conectar seleccionada." lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::
1. Después de seleccionar Bastion, haga clic en **Usar Bastion**. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](./quickstart-host-portal.md) (Configuración de Bastion).
1. En la página **Conectar mediante Azure Bastion,** despliegue la sección **Configuración de la conexión** y seleccione **SSH**. Si tiene previsto usar un puerto de entrada distinto del puerto SSH estándar (22), escriba el **Puerto**.

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings-manual.png" alt-text="Captura de pantalla de la configuración de la conexión." lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings-manual.png":::

1. Escriba el **nombre de usuario** y la **Clave privada SSH**. Escriba la clave privada en el área de texto **Clave privada SSH** (o péguela directamente).

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/authentication-manual.png" alt-text="Captura de pantalla de la autenticación de clave SSH." lightbox="./media/bastion-connect-vm-ssh-windows/authentication-manual.png":::

1. Seleccione **Conectar** para conectarse a la máquina virtual.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Conexión: con un archivo de clave privada

1. Abra [Azure Portal](https://portal.azure.com). Vaya a la máquina virtual a la que quiere conectarse y, a continuación, haga clic en **Conectar** y seleccione **Bastion** en la lista desplegable.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="Captura de pantalla en la que se describe la información general de una máquina virtual en Azure Portal con la opción Conectar seleccionada" lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::
1. Después de seleccionar Bastion, haga clic en **Usar Bastion**. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](./quickstart-host-portal.md) (Configuración de Bastion).
1. En la página **Conectar mediante Azure Bastion,** despliegue la sección **Configuración de la conexión** y seleccione **SSH**. Si tiene previsto usar un puerto de entrada distinto del puerto SSH estándar (22), escriba el **Puerto**.

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings-file.png" alt-text="Captura de pantalla que muestra la configuración de la conexión." lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings-file.png":::

1. Escriba el **nombre de usuario** y **Clave privada SSH del archivo local**. Busque el archivo y, luego, seleccione **Abrir**.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/authentication-file.png" alt-text="Captura de pantalla que muestra el archivo de clave SSH." lightbox="./media/bastion-connect-vm-ssh-windows/authentication-file.png":::

1. Seleccione **Conectar** para conectarse a la máquina virtual. Al hacer clic en Connect (Conectar), la conexión SSH con esta máquina virtual se abrirá directamente en Azure Portal. Esta conexión se realiza a través de HTML5 mediante el puerto 443 en el servicio Bastion a través de la dirección IP privada de la máquina virtual.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Conexión: Uso de una clave privada almacenada en Azure Key Vault

1. Abra [Azure Portal](https://portal.azure.com). Vaya a la máquina virtual a la que quiere conectarse y, a continuación, haga clic en **Conectar** y seleccione **Bastion** en la lista desplegable.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="Captura de pantalla de la información general de una máquina virtual en Azure Portal con la opción Conectar seleccionada." lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::
1. Después de seleccionar Bastion, haga clic en **Usar Bastion**. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](./quickstart-host-portal.md) (Configuración de Bastion).
1. En la página **Conectar mediante Azure Bastion,** despliegue la sección **Configuración de la conexión** y seleccione **SSH**. Si tiene previsto usar un puerto de entrada distinto del puerto SSH estándar (22), escriba el **Puerto**.

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings-akv.png" alt-text="Captura de pantalla en la que se muestra la configuración de la conexión." lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings-akv.png":::

1. Escriba el **nombre de usuario** y seleccione **Clave privada SSH de Azure Key Vault**.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/ssh-key-vault.png" alt-text="Captura de pantalla en la que se muestra la clave privada SSH de Azure Key Vault.":::
1. Seleccione la lista desplegable **Azure Key Vault** y elija el recurso en el que ha almacenado la clave privada SSH. 

   * Si no ha configurado un recurso de Azure Key Vault, consulte [Creación de un almacén de claves](../key-vault/secrets/quick-create-powershell.md) y almacene la clave privada SSH como el valor de un nuevo secreto de Key Vault.
   * Asegúrese de que tiene los permisos de acceso **Enumerar** y **Obtener** para los secretos almacenados en el recurso de Key Vault. Para asignar y modificar directivas de acceso para el recurso de Key Vault, consulte [Asignación de una directiva de acceso de Key Vault](../key-vault/general/assign-access-policy-portal.md).

      >[!NOTE]
      >Almacene la clave privada SSH como un secreto en Azure Key Vault mediante la experiencia de **PowerShell** o de la **CLI de Azure**. El almacenamiento de la clave privada mediante la experiencia del portal de Azure Key Vault interferirá con el formato y provocará un inicio de sesión incorrecto. Si ha guardado la clave privada como un secreto mediante la experiencia del portal y ya no tiene acceso al archivo de clave privada original, consulte [Actualización de la clave SSH](../virtual-machines/extensions/vmaccess.md#update-ssh-key) para actualizar el acceso a la máquina virtual de destino con un nuevo par de claves SSH.
      >

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/private-key-stored.png" alt-text="Captura de pantalla en la que se muestra Azure Key Vault." lightbox="./media/bastion-connect-vm-ssh-windows/private-key-stored.png":::

1. Seleccione la lista desplegable **Azure Key Vault Secret** (Secreto de Azure Key Vault) y elija el secreto de Key Vault que contiene el valor de la clave privada SSH.
1. Seleccione **Conectar** para conectarse a la máquina virtual. Al hacer clic en **Conectar**, la conexión SSH con esta máquina virtual se abrirá directamente en Azure Portal. Esta conexión se realiza a través de HTML5 mediante el puerto 443 en el servicio Bastion a través de la dirección IP privada de la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure Bastion, consulte las [preguntas frecuentes sobre Bastion](bastion-faq.md).