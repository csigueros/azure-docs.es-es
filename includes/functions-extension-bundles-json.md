---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 23e75e2cf2e097e08c0b6bf3c4b0b8c9842a9184
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609766"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

Las siguientes propiedades están disponibles en `extensionBundle`:

| Propiedad | Descripción |
| -------- | ----------- |
| id | Espacio de nombres de las agrupaciones de extensiones de Microsoft Azure Functions. |
| version | Versión de la agrupación que va a instalar. Functions Runtime siempre elige la máxima versión permitida definida por el rango o intervalo de versiones. El valor de versión anterior permite todas las versiones del lote desde la 2.0.0 en adelante, pero sin incluir la 3.0.0. Para más información, consulte la [notación de intervalo para especificar intervalos de versiones](/nuget/reference/package-versioning#version-ranges). |