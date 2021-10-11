---
title: 'Acerca de las claves de cuentas de almacenamiento administradas de Azure Key Vault: Azure Key Vault'
description: Claves de cuentas de almacenamiento administradas de Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 10/01/2021
ms.author: mbaldwin
ms.openlocfilehash: e2b72dd6960232dd3b9afd05ff19edccaeface12
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390662"
---
# <a name="about-azure-key-vault-managed-storage-account-keys"></a>Acerca de las claves de cuentas de almacenamiento administradas de Azure Key Vault

> [!IMPORTANT]
> Es recomendable usar la integración de Azure Storage con Azure Active Directory (Azure AD), el servicio de administración de acceso y de identidades basado en la nube de Microsoft. La integración de Azure AD está disponible para los [Blobs y las colas de Azure](../../storage/blobs/authorize-access-azure-active-directory.md) y proporciona acceso basado en tokens de OAuth2 a Azure Storage (al igual que Azure Key Vault). Azure AD le permite autenticar la aplicación cliente mediante una identidad de aplicación o usuario, en lugar de las credenciales de cuenta de almacenamiento. Puede usar una [identidad administrada de Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) cuando realice la ejecución en Azure. Las identidades administradas eliminan la necesidad de autenticación del cliente y de almacenar las credenciales en la aplicación o con ella. Use la solución siguiente solo cuando no sea posible la autenticación de Azure AD.

Una cuenta de Azure Storage usa credenciales que constan de un nombre de cuenta y una clave. La clave se genera automáticamente y actúa como una contraseña y no como una clave criptográfica. Key Vault, para administrar las claves de la cuenta de almacenamiento, las regenera periódicamente en una cuenta de almacenamiento y proporciona tokens de firma de acceso compartido para el acceso delegado a los recursos de la cuenta de almacenamiento.

Puede usar la característica de clave de cuenta de almacenamiento administrada de Key Vault para mostrar (sincronizar) las claves con una cuenta de almacenamiento de Azure y volver a generar (girar) las claves periódicamente. Puede administrar claves para las cuentas de almacenamiento y las cuentas de almacenamiento clásicas.

## <a name="azure-storage-account-key-management"></a>Administración de claves de la cuenta de almacenamiento de Azure

Key Vault puede administrar las claves de una [cuenta de almacenamiento Azure](../../storage/common/storage-account-overview.md):

- Internamente, Key Vault puede enumerar (sincronizar) las claves con una cuenta de almacenamiento de Azure. 
- Key Vault vuelve a generar (rotar) las claves periódicamente.
- Los valores de clave nunca se devuelven como respuesta al autor de la llamada.
- Key Vault administra las claves de las cuentas de almacenamiento y de las cuentas de almacenamiento clásicas.

Para más información, consulte:
- [Claves de acceso de cuenta de almacenamiento](../../storage/common/storage-account-keys-manage.md)
- [Administración de claves de cuenta de almacenamiento en Azure Key Vault](../secrets/overview-storage-keys.md)


## <a name="storage-account-access-control"></a>Control de acceso a la cuenta de almacenamiento

Los siguientes permisos pueden usarse al autorizar a una entidad de seguridad de aplicación o usuario para realizar operaciones en una cuenta de almacenamiento administrada:  

- Permisos para la cuenta de almacenamiento administrada y operaciones de definiciones de SAS
  - *get*: obtener información sobre una cuenta de almacenamiento 
  - *list*: enumerar las cuentas de almacenamiento administradas por Key Vault
  - *update*: crear una cuenta de almacenamiento
  - *delete*: Eliminar una cuenta de almacenamiento  
  - *recover*: recuperar una cuenta de almacenamiento eliminada
  - *backup*: copia de seguridad de una cuenta de almacenamiento
  - *restore*: restaurar una copia de seguridad de una cuenta de almacenamiento en un almacén de claves
  - *set*: crear o actualizar una cuenta de almacenamiento
  - *regeneratekey*: volver a generar un valor de clave específico para una cuenta de almacenamiento
  - *getsas*: obtener información sobre una definición de SAS para una cuenta de almacenamiento
  - *listsas*: enumerar las definiciones de SAS de almacenamiento para una cuenta de almacenamiento
  - *deletesas*: eliminar una definición de SAS de una cuenta de almacenamiento
  - *setsas*: crear o actualizar definiciones o atributos de SAS nuevos para una cuenta de almacenamiento

- Permisos para las operaciones con privilegios
  - *purge*: purgar (eliminar permanentemente) una cuenta de almacenamiento administrada

Para más información, vea las [operaciones para cuentas de almacenamiento en la referencia de la API REST de Key Vault](/rest/api/keyvault). Para obtener información sobre cómo establecer permisos, vea [Almacenes: crear o actualizar](/rest/api/keyvault/vaults/createorupdate) y [Almacenes: actualizar directiva de acceso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Pasos siguientes

- [Administración de claves de cuenta de almacenamiento con Key Vault y la CLI de Azure](overview-storage-keys.md)
- [Acerca de Key Vault](../general/overview.md)
- [Información acerca de claves, secretos y certificados](../general/about-keys-secrets-certificates.md)
- [Procedimientos recomendados para la administración de secretos en Key Vault](secrets-best-practices.md)
- [Guía del desarrollador de Key Vault](../general/developers-guide.md)
