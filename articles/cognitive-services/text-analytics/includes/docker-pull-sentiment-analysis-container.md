---
title: Docker pull para el contenedor de análisis de sentimiento
titleSuffix: Azure Cognitive Services
description: Comando pull de Docker para el contenedor de análisis de sentimiento
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/21/2021
ms.author: aahi
ms.openlocfilehash: f836a8313927dd773c432428cdf1b12c0cf00aef
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677561"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker pull para el contenedor de Análisis de sentimiento v3

El contenedor de Análisis de sentimiento v3 está disponible en varios idiomas. Para descargar el contenedor en inglés, use el siguiente comando. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Para descargar el contenedor para otro idioma, reemplace `en` por uno de los siguientes códigos de idioma. 

| Contenedor de Text Analytics | Código de lenguaje |
|--|--|
| Chino simplificado    |   `zh-hans`   |
| Chino (tradicional)   |   `zh-hant`   |
| Neerlandés                 |     `nl`      |
| Inglés               |     `en`      |
| Francés                |     `fr`      |
| Alemán                |     `de`      |
| Hindi                 |    `hi`       |
| Italiano               |     `it`      |
| Japonés              |     `ja`      |
| Coreano                |     `ko`      |
| Noruego (Bokmål)   |     `no`      |
| Portugués (Brasil)   |    `pt-BR`    |
| Portugués (Portugal) |    `pt-PT`    |
| Español               |     `es`      |
| Turco               |     `tr`      |

Para obtener una descripción completa de las etiquetas disponibles para los contenedores de Text Analytics, consulte [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).
