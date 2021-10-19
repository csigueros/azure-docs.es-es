---
title: 'Procedimientos recomendados para usar Key Vault: Azure Key Vault | Microsoft Docs'
description: Conozca procedimientos recomendados de Azure Key Vault, incluido el control de acceso, cuándo usar almacenes de claves independientes, copias de seguridad, registro y opciones de recuperación.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 616376d034fd32fae23d24a3a6e12f329604d376
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858969"
---
# <a name="best-practices-to-use-key-vault"></a>Procedimientos recomendados para utilizar Key Vault

## <a name="use-separate-key-vaults"></a>Uso de instancias de Key Vault independientes

Nuestra recomendación es usar un almacén por aplicación, entorno (desarrollo, preproducción y producción) y región. Esto ayuda a no compartir secretos entre los entornos y regiones y, también, reduce la amenaza en el caso de infracción.

### <a name="why-we-recommend-separate-key-vaults"></a>Motivos para recomendar almacenes de claves independientes

La instancia de Key Vault define el límite de seguridad de los secretos almacenados. La agrupación de secretos en el mismo almacén aumenta el *radio del impacto* de un evento de seguridad, ya que los ataques podrían tener acceso a información confidencial. Para reducir esta posibilidad, tenga en cuenta a qué información confidencial *debería* tener acceso una aplicación concreta y, después, separe los almacenes de claves en función de esta delineación. La separación de almacenes de claves por aplicación es el límite más común, pero el límite de seguridad puede ser más granular en el caso de aplicaciones grandes, por ejemplo, por grupo de servicios relacionados.

## <a name="control-access-to-your-vault"></a>Controlar el acceso al almacén

Azure Key Vault es un servicio en la nube que protege las claves de cifrado y los secretos, como certificados, cadenas de conexión y contraseñas. Dado que estos datos son confidenciales y críticos para la empresa, debe proteger el acceso a los almacenes de claves, de modo que solo se admita el acceso de las aplicaciones y los usuarios autorizados. En este [artículo](security-features.md) se proporciona información general sobre modelo de acceso de Key Vault. Se explican la autenticación y la autorización, y se describe cómo proteger el acceso a los almacenes de claves.

Estas son algunas sugerencias a la hora de controlar el acceso al almacén:
1. Bloquee el acceso a la suscripción, al grupo de recursos y a los distintos Key Vault (Azure RBAC)
2. Cree directivas de acceso para cada almacén.
3. Establezca la entidad de seguridad de acceso con privilegios mínimos para conceder el acceso.
4. Active el Firewall y los [puntos de conexión de servicio de red virtual](overview-vnet-service-endpoints.md).

## <a name="backup"></a>Copia de seguridad

Asegúrese de hacer copias de seguridad del almacén periódicamente, cuando actualice, elimine o cree objetos dentro de un almacén.

### <a name="azure-powershell-backup-commands"></a>Comandos de copia de seguridad de Azure PowerShell

* [Copia de seguridad de certificado](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Copia de seguridad de clave](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Copia de seguridad de secreto](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Comandos de copia de seguridad de la CLI de Azure

* [Copia de seguridad de certificado](/cli/azure/keyvault/certificate#az_keyvault_certificate_backup)
* [Copia de seguridad de clave](/cli/azure/keyvault/key#az_keyvault_key_backup)
* [Copia de seguridad de secreto](/cli/azure/keyvault/secret#az_keyvault_secret_backup)


## <a name="turn-on-logging"></a>Activar el registro

[Active el registro](logging.md) en el almacén. De igual modo, configure alertas.

## <a name="turn-on-recovery-options"></a>Activar las opciones de recuperación

1. Active la [eliminación temporal](soft-delete-overview.md).
2. Active la protección de purgas si quiere tener protección frente a posibles eliminaciones forzadas de secretos o del almacén, incluso con la eliminación temporal activada.

## <a name="learn-more"></a>Más información
- [Procedimientos recomendados para la administración de secretos en Key Vault](../secrets/secrets-best-practices.md)
