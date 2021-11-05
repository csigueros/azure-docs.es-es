---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: feabf1cfde0facc49694554094064ee7b54e816b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852350"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[3.3.0, 4.0.0)"
    }
}
```

Las siguientes propiedades están disponibles en `extensionBundle`:

| Propiedad | Descripción |
| -------- | ----------- |
| id | Espacio de nombres de las agrupaciones de extensiones de Microsoft Azure Functions. |
| version | Versión de la agrupación que va a instalar. Functions Runtime siempre elige la máxima versión permitida definida por el rango o intervalo de versiones. El valor de versión anterior permite todas las versiones del lote desde la 2.0.0 en adelante, pero sin incluir la 3.0.0. Para más información, consulte la [notación de intervalo para especificar intervalos de versiones](/nuget/reference/package-versioning#version-ranges). |