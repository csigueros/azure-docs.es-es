---
title: Recomendaciones y requisitos del contenedor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2021
ms.author: aahi
ms.openlocfilehash: 81e86eda454bdbd262f6c38a4766ee2a899d225d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122336163"
---
> [!NOTE]
> Los requisitos y las recomendaciones se basan en pruebas comparativas con una única solicitud por segundo, con una imagen de 523 kB de una carta comercial escaneada que contiene 29 líneas y 803 caracteres en total. La configuración recomendada dio como resultado una respuesta aproximadamente dos veces más rápida en comparación con la configuración mínima.

En la tabla siguiente se describe la asignación mínima y recomendada de recursos para cada contenedor OCR de Read.

| Contenedor | Mínima | Recomendado |
|-----------|---------|-------------|
| Versión preliminar de Read 2.0 | 1 núcleo, 8 GB de memoria |    8 núcleos, 16 GB de memoria |
| Read 3.2 | 4 núcleos, 16 GB de memoria | 8 núcleos, 24 GB de memoria |

* Cada núcleo debe ser de 2,6 gigahercios (GHz) como mínimo.

El núcleo y la memoria se corresponden con los valores de `--cpus` y `--memory` que se usan como parte del comando `docker run`.
