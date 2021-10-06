---
title: Conexión a una máquina virtual Linux mediante SSH
titleSuffix: Azure Bastion
description: Aprenda a usar Azure Bastion para conectarse a una máquina virtual Linux mediante SSH.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: 96edce1434665a5b49c35f8cc305ab4d2c417f8d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594579"
---
# <a name="create-an-ssh-connection-to-a-linux-vm-using-azure-bastion"></a>Creación de una conexión SSH a una máquina virtual Linux mediante Azure Bastion

En este artículo se muestra cómo crear de forma segura y sin problemas una conexión SSH a las máquinas virtuales Linux ubicadas en una red virtual de Azure directamente desde Azure Portal. Cuando se usa Azure Bastion, las máquinas virtuales no necesitan un cliente, un agente o software adicional. También se puede conectar a una máquina virtual Linux mediante RDP. Para obtener información, vea [Creación de una conexión RDP a una máquina virtual Linux](bastion-connect-vm-rdp-linux.md).

Azure Bastion proporciona conectividad segura a todas las máquinas virtuales de la red virtual en la que se aprovisiona. El uso de Azure Bastion protege las máquinas virtuales frente a la exposición de los puertos de RDP/SSH al mundo exterior, al tiempo que ofrece acceso seguro mediante RDP/SSH. Para obtener más información, vea [¿Qué es Azure Bastion?](bastion-overview.md)

Al conectarse a una máquina virtual Linux mediante SSH, puede usar el nombre de usuario y la contraseña, y las claves SSH para la autenticación. Puede conectarse a la máquina virtual con claves SSH mediante una de estas opciones:

* Una clave privada que se escribe manualmente.
* Un archivo que contiene la información de la clave privada.

La clave privada SSH debe tener un formato que empieza con `"-----BEGIN RSA PRIVATE KEY-----"` y finaliza con `"-----END RSA PRIVATE KEY-----"`.

## <a name="prerequisites"></a>Prerrequisitos

Asegúrese de haber configurado un host de Azure Bastion para la red virtual donde reside la máquina virtual. Para más información, consulte [Create an Azure Bastion host](./tutorial-create-host-portal.md) (Creación de un host de Azure Bastion). Cuando el servicio Bastion se aprovisiona e implementa en la red virtual, puede usarlo para conectarse a cualquier máquina virtual de esta red virtual. 

### <a name="required-roles"></a>Roles necesarios

Para crear una conexión, se requieren los siguientes roles:

* Rol de lector en la máquina virtual
* Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual
* Rol de lector en el recurso de Azure Bastion

### <a name="ports"></a>Puertos

Para conectarse a la máquina virtual Linux mediante SSH, debe tener abiertos los siguientes puertos en la máquina virtual:

* Puerto de entrada: SSH (22), ***o bien***
* Puerto de entrada: valor personalizado (tendrá que especificar este puerto personalizado al conectarse a la máquina virtual por medio de Azure Bastion)

   > [!NOTE]
   > Si quiere especificar un valor de puerto personalizado, Azure Bastion se debe configurar mediante la SKU Estándar. La SKU Básica no permite especificar puertos personalizados. La SKU estándar se encuentra actualmente en versión preliminar.
   >

## <a name="connect-using-username-and-password"></a><a name="username"></a>Conexión: mediante un nombre de usuario y una contraseña

1. Abra [Azure Portal](https://portal.azure.com). Vaya a la máquina virtual a la que quiere conectarse y, a continuación, haga clic en **Conectar** y seleccione **Bastion** en la lista desplegable.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Captura de pantalla que muestra la información general de una máquina virtual en Azure Portal con la opción Conectar seleccionada." lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::

1. Después de seleccionar Bastion, haga clic en **Usar Bastion**. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](./quickstart-host-portal.md) (Configuración de Bastion).
1. En la página **Conectar mediante Azure Bastion**, especifique los valores pertinentes en **Nombre de usuario** y **Contraseña**.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/password.png" alt-text="Captura de pantalla en la que se muestra la autenticación de contraseña.":::
1. Seleccione **Conectar** para conectarse a la máquina virtual.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Conexión: con una clave privada escrita

1. Abra [Azure Portal](https://portal.azure.com). Vaya a la máquina virtual a la que quiere conectarse y, a continuación, haga clic en **Conectar** y seleccione **Bastion** en la lista desplegable.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Captura de pantalla de la información general de una máquina virtual en Azure Portal con la opción Conectar seleccionada." lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::
1. Después de seleccionar Bastion, haga clic en **Usar Bastion**. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](./quickstart-host-portal.md) (Configuración de Bastion).
1. En la página **Conectar mediante Azure Bastion**, especifique los valores pertinentes en **Nombre de usuario** y **Clave privada SSH**.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/ssh-private-key.png" alt-text="Captura de pantalla de la autenticación de clave privada SSH.":::
1. Escriba la clave privada en el área de texto **Clave privada SSH** (o péguela directamente).
1. Seleccione **Conectar** para conectarse a la máquina virtual.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Conexión: con un archivo de clave privada

1. Abra [Azure Portal](https://portal.azure.com). Vaya a la máquina virtual a la que quiere conectarse y, a continuación, haga clic en **Conectar** y seleccione **Bastion** en la lista desplegable.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Captura de pantalla en la que se describe la información general de una máquina virtual en Azure Portal con la opción Conectar seleccionada." lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::
1. Después de seleccionar Bastion, haga clic en **Usar Bastion**. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](./quickstart-host-portal.md) (Configuración de Bastion).
1. En la página **Conectar mediante Azure Bastion**, especifique los valores pertinentes en **Nombre de usuario** y **Clave privada SSH del archivo local**.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/private-key-file.png" alt-text="Captura de pantalla en la que se muestra el archivo de clave privada SSH.":::

1. Busque el archivo y, luego, seleccione **Abrir**.
1. Seleccione **Conectar** para conectarse a la máquina virtual. Al hacer clic en Connect (Conectar), la conexión SSH con esta máquina virtual se abrirá directamente en Azure Portal. Esta conexión se realiza a través de HTML5 mediante el puerto 443 en el servicio Bastion a través de la dirección IP privada de la máquina virtual.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Conexión: Uso de una clave privada almacenada en Azure Key Vault

1. Abra [Azure Portal](https://portal.azure.com). Vaya a la máquina virtual a la que quiere conectarse y, a continuación, haga clic en **Conectar** y seleccione **Bastion** en la lista desplegable.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Captura de pantalla en la que se muestra la información general de una máquina virtual en Azure Portal con la opción Conectar seleccionada" lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::
1. Después de seleccionar Bastion, haga clic en **Usar Bastion**. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](./quickstart-host-portal.md) (Configuración de Bastion).
1. En la página **Conectar mediante Azure Bastion**, especifique el valor pertinente en **Nombre de usuario** y seleccione **Clave privada SSH de Azure Key Vault**.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/ssh-key-vault.png" alt-text="Captura de pantalla en la que se muestra la clave privada SSH de Azure Key Vault.":::
1. Seleccione la lista desplegable **Azure Key Vault** y elija el recurso en el que ha almacenado la clave privada SSH. 

   * Si no ha configurado un recurso de Azure Key Vault, consulte [Creación de un almacén de claves](../key-vault/secrets/quick-create-powershell.md) y almacene la clave privada SSH como el valor de un nuevo secreto de Key Vault.

   * Asegúrese de que tiene los permisos de acceso **Enumerar** y **Obtener** para los secretos almacenados en el recurso de Key Vault. Para asignar y modificar directivas de acceso para el recurso de Key Vault, consulte [Asignación de una directiva de acceso de Key Vault](../key-vault/general/assign-access-policy-portal.md).

     > [!NOTE]
     > Almacene la clave privada SSH como un secreto en Azure Key Vault mediante la experiencia de **PowerShell** o de la **CLI de Azure**. El almacenamiento de la clave privada mediante la experiencia del portal de Azure Key Vault interferirá con el formato y provocará un inicio de sesión incorrecto. Si ha guardado la clave privada como un secreto mediante la experiencia del portal y ya no tiene acceso al archivo de clave privada original, consulte [Actualización de la clave SSH](../virtual-machines/extensions/vmaccess.md#update-ssh-key) para actualizar el acceso a la máquina virtual de destino con un nuevo par de claves SSH.
     >

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/private-key-stored.png" alt-text="Captura de pantalla en la que se muestra Azure Key Vault." lightbox="./media/bastion-connect-vm-ssh-linux/private-key-stored.png":::

1. Seleccione la lista desplegable **Azure Key Vault Secret** (Secreto de Azure Key Vault) y elija el secreto de Key Vault que contiene el valor de la clave privada SSH.
1. Seleccione **Conectar** para conectarse a la máquina virtual. Al hacer clic en **Conectar**, la conexión SSH con esta máquina virtual se abrirá directamente en Azure Portal. Esta conexión se realiza a través de HTML5 mediante el puerto 443 en el servicio Bastion a través de la dirección IP privada de la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure Bastion, consulte las [preguntas frecuentes sobre Bastion](bastion-faq.md).