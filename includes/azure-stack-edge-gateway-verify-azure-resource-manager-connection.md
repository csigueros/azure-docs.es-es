---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: e0770eb27bfebc085d99af9fdc9749676b699b70
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113106593"
---
Asegúrese de que se pueden usar los pasos siguientes para acceder al dispositivo desde el cliente.

Compruebe que el cliente puede conectarse a Azure Resource Manager local. 

1. Llame a las API del dispositivo local para autenticarse:

    ### <a name="az"></a>[Az](#tab/az)

    ```powershell
    login-AzAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

    ### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Proporcione el nombre de usuario `EdgeArmUser` y la contraseña para conectarse mediante Azure Resource Manager. Si no recuerda la contraseña, consulte [Restablecimiento de la contraseña de Azure Resource Manager](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md) y use esta contraseña para iniciar la sesión.