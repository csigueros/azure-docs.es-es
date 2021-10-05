---
title: Códigos de error comunes para Azure Key Vault | Microsoft Docs
description: Códigos de error comunes para Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: bd66619c65dd39e32a11b81096d4b763430fc16c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553686"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Códigos de error comunes para Azure Key Vault

Una operación en un almacén de claves de Azure puede devolver los códigos de error que se muestran en la tabla siguiente.

| Código de error | Mensaje de usuario |
|--|--|
| VaultAlreadyExists |  Se ha producido un error al intentar crear un nuevo almacén de claves con el nombre especificado porque ese nombre ya está en uso. Si ha eliminado recientemente un almacén de claves con este nombre, es posible que todavía se encuentre en estado de eliminación temporal. Puede comprobar [aquí](./key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault) si existe en el estado de eliminación temporal. |
| VaultNameNotValid |  El nombre del almacén debe ser una cadena de entre 3 y 24 caracteres y solo puede contener números (0-9), letras (a-z, A-Z) y guiones (-). |
| AccessDenied |  Es posible que falten permisos en la directiva de acceso para realizar esa operación. |
| ForbiddenByFirewall |  La dirección de cliente no está autorizada y el autor de llamada no es un servicio de confianza. |
| ConflictError |  Está solicitando varias operaciones en el mismo elemento.  |
| RegionNotSupported |  No se admite la región de Azure especificada para este recurso. |
| SkuNotSupported |  No se admite el tipo de SKU especificado para este recurso. |
| ResourceNotFound |  No se encuentra el recurso de Azure especificado. |
| ResourceGroupNotFound | No se encuentra el grupo de recursos de Azure especificado. |
| CertificateExpired |  Compruebe la fecha de expiración y el período de validez del certificado. |


## <a name="next-steps"></a>Pasos siguientes

- Consulte la [guía del desarrollador de Azure Key Vault](developers-guide.md).
- Más información acerca de la [Autenticación en Key Vault](authentication.md).
