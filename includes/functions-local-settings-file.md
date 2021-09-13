---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/15/2021
ms.author: glenga
ms.openlocfilehash: 33a4b0f7d25162cf258e9ef6ad4ee438d6b76c8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741379"
---
## <a name="local-settings"></a>Configuración local

Cuando se ejecuta en una aplicación de funciones de Azure, la configuración requerida por las funciones se [almacena de forma segura en la configuración de la aplicación](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#settings). Durante el desarrollo local, esta configuración se agrega en su lugar al objeto `Values` del archivo local.settings.json. El archivo local.settings.json almacena también la configuración que usan las herramientas locales de desarrollo. 

Dado que local.settings.json puede contener secretos, como cadenas de conexión, nunca debe almacenarlo en un repositorio remoto. Para más información sobre la configuración local, consulte [Archivo de configuración local](../articles/azure-functions/functions-develop-local.md#local-settings-file).