---
title: Archivo de inclusión
description: archivo de inclusión
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 2c33ad42eeb710edbffdf6448fc138eb6d6aae86
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123078515"
---
## <a name="prepare-for-akv-integration"></a>Preparación para la integración de AKV
Para usar la Integración de Azure Key Vault para configurar la máquina virtual de SQL Server, hay varios requisitos previos: 

1. [Instalar Azure Powershell](#install)
2. [Crear un directorio de Azure Active Directory](#register)
3. [Creación de un Almacén de claves](#createkeyvault)

Las secciones siguientes describen estos requisitos previos y la información que necesita recopilar para ejecutar más adelante los cmdlets de PowerShell.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a> Instalar Azure PowerShell
Asegúrese de que ha instalado el módulo de Azure PowerShell más reciente. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a> Registrar una aplicación en Azure Active Directory

En primer lugar, tiene que tener un [directorio de Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) en la suscripción. Entre otras muchas ventajas, esto le permite conceder permiso para Almacén de claves a determinados usuarios y aplicaciones.

A continuación, registre una aplicación con AAD. Esto le dará una cuenta de entidad de servicio que tenga acceso a su almacén de claves, algo que la máquina virtual va a necesitar. En el artículo sobre Azure Key Vault, puede encontrar estos pasos en la sección [Registro de una aplicación con Azure Active Directory](../articles/key-vault/general/manage-with-cli2.md#registering-an-application-with-azure-active-directory), o bien puede ver los pasos con capturas de pantalla en la **sección en la que se indica cómo obtener una identidad para la aplicación** de [esta entrada de blog](/archive/blogs/kv/azure-key-vault-step-by-step). Antes de completar estos pasos, durante este registro tiene que recopilar la siguiente información que necesitará más adelante cuando habilite la integración de Azure Key Vault en la máquina virtual de SQL.

* Después de agregar la aplicación, busque el **identificador de la aplicación** (también conocido como ClientID o AppID de AAD) en la hoja **Aplicación registrada**.
    El identificador de la aplicación se asigna posteriormente al parámetro **$spName** (nombre de entidad de seguridad de servicio) en el script de PowerShell para habilitar la integración de Azure Key Vault.

   ![Identificador de aplicación](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Durante estos pasos al crear la clave, copie el secreto de la clave tal y como se muestra en la siguiente captura de pantalla. Este secreto de clave se asigna posteriormente al parámetro **$spSecret** (secreto de entidad de servicio) en el script de PowerShell.

   ![Secreto de AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* El identificador de la aplicación y el secreto también se utilizarán para crear una credencial en SQL Server.

* Debe autorizar este nuevo identificador de aplicación (o identificador de cliente) para tener los siguientes permisos de acceso: **get**, **wrapKey** y **unwrapKey**. Esto se hace con el cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Para obtener más información, consulte la [información general de Azure Key Vault](../articles/key-vault/general/overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a> Crear un almacén de claves
Para poder usar Azure Key Vault para guardar las claves que se utilizarán para el cifrado en la máquina virtual, tiene que tener acceso a un almacén de claves. Si no ha configurado ya su almacén de claves, cree uno siguiendo los pasos que se mencionan en el artículo [Introducción a Azure Key Vault](../articles/key-vault/general/overview.md). Antes de completar estos pasos, durante esta configuración tiene que recopilar cierta información que necesitará más adelante cuando habilite la integración de Azure Key Vault en la máquina virtual de SQL.

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'
```

Cuando llegue al paso Creación de un Almacén de claves, tenga en cuenta la propiedad **vaultUri** devuelta, que es la dirección URL del Almacén de claves. En el ejemplo que se proporciona en ese paso, que se muestra a continuación, el nombre del almacén de claves es ContosoKeyVault, por lo que su dirección URL sería `https://contosokeyvault.vault.azure.net/`.

La URL de Key Vault se asigna posteriormente al parámetro **$akvURL** en el script de PowerShell para habilitar la integración de Azure Key Vault.

Una vez creado el almacén de claves, se debe agregar una clave para dicho almacén; esta clave se utilizará más adelante cuando se cree una clave asimétrica en SQL Server.
