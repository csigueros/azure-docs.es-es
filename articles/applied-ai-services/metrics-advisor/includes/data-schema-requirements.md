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
ms.openlocfilehash: f00c25396405678312e4c18a345840d628c15848
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114340923"
---
Metrics Advisor es un servicio de detección, diagnóstico y análisis de anomalías de serie temporal. Como servicio basado en IA, utiliza los datos para entrenar el modelo usado. El servicio acepta tablas de datos agregados con las columnas siguientes:

* **Medida** (obligatorio): una medida es un término fundamental o específico de unidad y un valor cuantificable de la métrica. Significa que una o varias columnas contienen valores numéricos.
* **Timestamp** (Marca de tiempo) (opcional): cero o una columna del tipo `DateTime` o `String`. Cuando esta columna no está establecida, la marca de tiempo se establece como la hora de inicio de cada período de ingesta. Dé formato a la marca de tiempo como `yyyy-MM-ddTHH:mm:ssZ`. 
* **Dimensión** (opcional): una dimensión es uno o más valores de categorías. La combinación de esos valores identifica una serie temporal univariante determinada, por ejemplo: país, idioma, inquilino, etc. Las columnas de dimensión pueden ser de cualquier tipo de datos. Tenga cuidado al trabajar con grandes volúmenes de columnas y valores si desea evitar que se procese un número excesivo de dimensiones.

Este es un ejemplo de un esquema de métricas esperado: 

<!-- ![Screenshot of metrics schema example](../media/tutorial/metric-schema.png) -->

Agregue los datos con antelación para alinearlos con el esquema de métricas esperado si usa orígenes de datos como ADLS, Azure Blob, etc., que usan un archivo como entrada de métricas. Pero si usa orígenes de datos como Azure SQL Server, Azure Data Explorer u otros orígenes, que permiten la ejecución de una consulta para obtener datos de métricas de orígenes, puede usar funciones de agregación para agregar datos en el esquema esperado.
