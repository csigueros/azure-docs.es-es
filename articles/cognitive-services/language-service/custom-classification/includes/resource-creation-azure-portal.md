---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 93ee37d6ef607a65f5b8be68d71c46fc258387f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091037"
---
### <a name="create-a-new-resource-from-the-azure-portal"></a>Creación de un nuevo recurso en Azure Portal

Vaya a [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) y cree un recurso de idioma de Azure. Si se le pide que seleccione características adicionales, seleccione **Omitir este paso**. Al crear el recurso, asegúrese de que tiene los parámetros siguientes.  

|Requisito  |Valor obligatorio  |
|---------|---------|
|Location | Oeste de EE. UU. 2 u Oeste de Europa         |
|Plan de tarifa     | Plan de tarifa estándar (**S**)        |

> [!IMPORTANT]
> En la sección **Custom Named Entity Recognition (NER) & Custom Classification (Preview)** (Reconocimiento de entidades con nombre [NER] personalizado y clasificación personalizada [versión preliminar]), asegúrese de seleccionar una cuenta de almacenamiento existente o cree una nueva. Se requiere una cuenta de almacenamiento para usar la clasificación de texto personalizado. Aunque puede especificar una cuenta de almacenamiento más adelante, ahora es más fácil hacerlo. 
