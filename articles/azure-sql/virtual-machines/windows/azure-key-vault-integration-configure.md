---
title: Integración de Key Vault con SQL Server en máquinas virtuales Windows en Azure (Resource manager) | Microsoft Docs
description: Aprenda a automatizar la configuración de cifrado de SQL Server para su uso con Azure Key Vault. En este tema se explica cómo usar la integración de Azure Key Vault con máquinas virtuales de SQL creadas con Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 76363f171ba08c28e7460a988ff72afbf546716b
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434878"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Configuración de la integración de Azure Key Vault para SQL Server en máquinas virtuales de Azure (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server tiene varias características de cifrado, como el [cifrado de datos transparente (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption), el [cifrado de nivel de columna (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql) y el [cifrado de copia de seguridad](/sql/relational-databases/backup-restore/backup-encryption). Estas formas de cifrado requieren administrar y almacenar las claves criptográficas que se usan para el cifrado. El servicio Azure Key Vault está diseñado para mejorar la seguridad y la administración de estas claves en una ubicación segura y con gran disponibilidad. El [conector de SQL Server](https://www.microsoft.com/download/details.aspx?id=45344) permite que SQL Server use estas claves de Azure Key Vault.

Si ejecuta SQL Server de forma local, hay una serie de pasos que puede seguir para [acceder a Azure Key Vault desde la instancia de SQL Server local](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server). Pero para SQL Server en las máquinas virtuales de Azure, puede ahorrar tiempo usando la característica *Integración de Azure Key Vault*.

Cuando se habilita esta característica, automáticamente se instala el conector de SQL Server, se configura el proveedor EKM para obtener acceso a Azure Key Vault y se crea la credencial para que pueda tener acceso a su almacén. Si examinamos los pasos descritos en la documentación local que se mencionó anteriormente, puede ver que esta característica automatiza los pasos 2 y 3. Lo único que aún tiene que hacer manualmente es crear el Almacén de claves y las claves. Desde allí, se automatiza toda la configuración de la máquina virtual de SQL Server. Cuando esta característica haya completado el programa de instalación, puede ejecutar instrucciones de Transact-SQL (T-SQL) para empezar a cifrar sus bases de datos o copias de seguridad como lo haría normalmente.

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > La versión 1.0.4.0 del proveedor de Administración extensible de claves (EKM) se instala en la VM con SQL Server a través de la [extensión de infraestructura como servicio (IaaS) de SQL](./sql-server-iaas-agent-extension-automate-management.md). Al actualizar la extensión de IaaS de SQL, no se actualizará la versión del proveedor. Considere la posibilidad de actualizar manualmente la versión del proveedor EKM si es necesario (por ejemplo, al migrar a una instancia administrada de SQL).


## <a name="enabling-and-configuring-key-vault-integration"></a>Habilitación y configuración de la integración de Key Vault
Puede habilitar la integración de Key Vault durante el aprovisionamiento o configurarla para las máquinas virtuales existentes.

### <a name="new-vms"></a>Nuevas máquinas virtuales
Si está aprovisionando una nueva máquina virtual de SQL con Resource Manager, Azure Portal permite habilitar la integración de Azure Key Vault. La característica Azure Key Vault está disponible solo para las ediciones Enterprise, Developer y Evaluation de SQL Server.

![Integración de Azure Key Vault en SQL](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

Para ver un tutorial detallado del aprovisionamiento, consulte [Aprovisionamiento de una máquina virtual de SQL en Azure Portal](create-sql-vm-portal.md).

### <a name="existing-vms"></a>Máquinas virtuales existentes

En el caso de las máquinas virtuales de SQL existentes, abra su [recurso de máquinas virtuales de SQL](manage-sql-vm-portal.md#access-the-resource) y seleccione **Seguridad** en **Configuración**. Seleccione **Habilitar** para habilitar la integración de Azure Key Vault. 

![Integración de Key Vault con SQL para máquinas virtuales existentes](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

Cuando termine, seleccione el botón **Aplicar** en la parte inferior de la página **Seguridad** para guardar los cambios.

> [!NOTE]
> El nombre de la credencial que se ha creado aquí se asignará más tarde a un inicio de sesión de SQL. Esto permite que el inicio de sesión de SQL tenga acceso al almacén de claves. 


> [!NOTE]
> También puede usar una plantilla para configurar la integración de Key Vault. Para más información, consulte la [plantilla de inicio rápido de Azure para la integración de Azure Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-existing-keyvault-update).


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]