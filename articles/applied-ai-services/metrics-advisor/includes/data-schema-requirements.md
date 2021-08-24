---
title: Requisitos del esquema de datos
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 32fc672bf41c35881baf082b5694d1800084889d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745950"
---
Azure Metrics Advisor es un servicio de detección, diagnóstico y análisis de anomalías de serie temporal. Como servicio basado en IA, utiliza los datos para entrenar el modelo usado. El servicio acepta tablas de datos agregados con las columnas siguientes:

* **Medida** (obligatorio): una medida es un término fundamental o específico de unidad y un valor cuantificable de la métrica. Significa que una o varias columnas contienen valores numéricos.
* **Timestamp** (Marca de tiempo) (opcional): cero o una columna del tipo `DateTime` o `String`. Cuando esta columna no está establecida, la marca de tiempo se establece como la hora de inicio de cada período de ingesta. Formatee la marca de tiempo de la siguiente manera: `yyyy-MM-ddTHH:mm:ssZ`. 
* **Dimensión** (opcional): una dimensión es uno o más valores de categorías. La combinación de esos valores identifica una serie temporal univariante determinada, por ejemplo: país, idioma e inquilino. Las columnas de dimensión pueden ser de cualquier tipo de datos. Tenga cuidado al trabajar con grandes volúmenes de columnas y valores si desea evitar que se procese un número excesivo de dimensiones.

Si usa orígenes de datos como Azure Data Lake Storage o Azure Blob Storage, puede agregar los datos para alinearlos con el esquema de métricas previsible. Esto se debe a que estos orígenes de datos usan un archivo como entrada de métricas.

Si usa orígenes de datos como Azure SQL o Azure Data Explorer, puede usar funciones de agregación para agregar datos al esquema previsible. Esto se debe a que estos orígenes de datos admiten la ejecución de una consulta para obtener datos de métricas de los orígenes.

