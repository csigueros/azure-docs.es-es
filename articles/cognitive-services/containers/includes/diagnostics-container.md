---
titleSuffix: Cognitive Services
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2021
ms.author: aahi
ms.openlocfilehash: 2d26c560cd05aad4962d318e8c2c798c576db811
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "131011663"
---
Si tiene problemas al ejecutar un contenedor de Cognitive Services, puede intentar usar el contenedor de diagnósticos de Microsoft. Use este contenedor para diagnosticar errores comunes en el entorno de implementación que podrían impedir que los contenedores de Cognitive Services funcionen según lo previsto.

Para obtener el contenedor, use el comando de Docker `pull` siguiente:

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/diagnostic
```

Luego ejecute el contenedor, reemplace `{ENDPOINT_URI}` por el punto de conexión y `{API_KEY}` por la clave del recurso:

```bash
docker run --rm mcr.microsoft.com/azure-cognitive-services/diagnostic \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

El contenedor prueba la conectividad de red con el punto de conexión de facturación.