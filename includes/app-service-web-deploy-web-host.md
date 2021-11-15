---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 4108aca3e5f1d944005f02e56964687c75162b41
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570601"
---
### <a name="app-service-plan"></a>Plan de App Service

Crea el plan de servicio para hospedar la aplicación web. Debe proporcionar el nombre del plan a través del parámetro **hostingPlanName** . La ubicación del plan es la misma ubicación que se usa para el grupo de recursos. El tamaño de trabajo y el plan de tarifa se especifican en los parámetros **sku** y **workerSize**.

```config
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('hostingPlanName')]",
  "type": "Microsoft.Web/serverfarms",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[parameters('sku')]",
    "capacity": "[parameters('workerSize')]"
  },
  "properties": {
    "name": "[parameters('hostingPlanName')]"
  }
},
```
