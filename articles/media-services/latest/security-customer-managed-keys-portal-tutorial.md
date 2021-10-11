---
title: Uso de claves administradas por el cliente o del portal BYOK
description: En este tutorial, usará Azure Portal para habilitar claves administradas por el cliente o Bring Your Own Key (BYOK) con una cuenta de almacenamiento de Azure Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 1a5b4c8e387085b8fee6c6e82ae765a186f43e87
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356684"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>Tutorial: Uso de Azure Portal para emplear claves administradas por el cliente o BYOK con Media Services

Con la API 2020-05-01 o una versión anterior, puede usar una clave RSA administrada por el cliente con una cuenta de Azure Media Services que tenga una identidad administrada por el sistema. Este tutorial indica los pasos en Azure Portal.

Los servicios utilizados son:

- Azure Storage
- Azure Key Vault
- Azure Media Services

En este tutorial, aprenderá a usar Azure Portal para las siguientes acciones:

> [!div class="checklist"]
> - Cree un grupo de recursos.
> - Crear una cuenta de almacenamiento con una identidad administrada por el sistema.
> - Crear una cuenta de Media Services con una identidad administrada por el sistema.
> - Crear un almacén de claves para almacenar una clave RSA administrada por el cliente.

## <a name="prerequisites"></a>Requisitos previos

Suscripción a Azure.

Si no tiene ninguna suscripción a Azure, [cree una cuenta de evaluación gratuita](https://azure.microsoft.com/free/).

## <a name="system-managed-keys"></a>Claves administradas por el sistema

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> En los siguientes pasos de creación de la cuenta de almacenamiento, seleccionará la opción de clave administrada por el sistema en la configuración avanzada.

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> En los siguientes pasos de cifrado de almacenamiento, seleccionará la **opción de clave administrada por el cliente**.

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>Cambio de la clave

Media Services detecta automáticamente cuándo se cambia la clave. OPCIONAL: Para probar este proceso, cree otra versión de clave para la misma clave. Media Services debe detectar que se ha cambiado la clave.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando los recursos que ha creado y *no desea que se le siga facturando*, elimínelos.

## <a name="next-steps"></a>Pasos siguientes

Avance al siguiente artículo para obtener información sobre:
> [!div class="nextstepaction"]
> [Tutorial: Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: REST](stream-files-tutorial-with-rest.md)
