---
title: Archivo de inclusión
description: archivo de inclusión
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ef0ad097f0dff99de71096cb24eba511a21bf06a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122263058"
---
## <a name="protect-your-access-keys"></a>Protección de las claves de acceso

Las claves de acceso de la cuenta de almacenamiento son similares a una contraseña raíz de la cuenta de almacenamiento. Siempre debe proteger las claves de acceso. Use Azure Key Vault para administrar y rotar las claves de forma segura. Evite distribuirlas a otros usuarios, codificarlas de forma rígida o guardarlas en un archivo de texto sin formato al que puedan acceder otros usuarios. Rote sus claves si cree que se han puesto en peligro.

> [!NOTE]
> Microsoft recomienda el uso de Azure Active Directory (Azure AD) para autorizar solicitudes de datos para blobs y colas, si es posible, en lugar de la clave compartida. Azure AD proporciona una mayor seguridad y facilidad de uso a través de la clave compartida. Para obtener más información sobre la autorización de acceso a datos con Azure AD, consulte [Autorización del acceso a los blobs y las colas de Azure con Azure Active Directory](../articles/storage/blobs/authorize-access-azure-active-directory.md).