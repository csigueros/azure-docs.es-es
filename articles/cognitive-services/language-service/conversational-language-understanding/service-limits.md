---
title: Límites del reconocimiento del lenguaje conversacional
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre los límites de datos, regiones y rendimiento del reconocimiento del lenguaje conversacional
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: ec9a32c4f73e174798a3422a1c152f3972bd076f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091509"
---
# <a name="service-limits-for-conversational-language-understanding"></a>Límites de servicio para el reconocimiento del lenguaje conversacional

Obtenga información sobre los límites de datos, regiones y rendimiento del servicio Conversational Language Understanding

## <a name="region-limits"></a>Límites de región

- Conversational Language Understanding solo está disponible en dos regiones: **Oeste de EE. UU. 2** y **Oeste de Europa**. 
    - Los proyectos de flujo de trabajo de orquestación habilitarán los **proyectos de conversación**, **QnA Maker** y conexiones de **LUIS** en Oeste de Europa.
    - Los proyectos de flujo de trabajo de orquestación habilitarán los **proyectos de conversación** y **QnA Maker** en Oeste de EE. UU. 2. No hay ninguna región Oeste de EE. UU. 2 de creación para LUIS. 
- La única SKU disponible para acceder a CLU es el recurso de **Language** con la SKU **S**.

## <a name="data-limits"></a>Límites de datos

Se observan los límites siguientes para el servicio Conversational Language Understanding.

|Elemento|Límite|
| --- | --- |
|Grabaciones de voz|15 000 por proyecto*|
|Intenciones|500 por proyecto|
|Entidades|100 por proyecto|
|longitud de la expresión|500 caracteres|
|Longitud del nombre de la intención y la entidad|50 caracteres|
|Modelos|10 por proyecto|
|Proyectos|500 por recurso|
|Sinónimos|20 000 por componente de lista|

\**Solo incluye expresiones agregadas por el usuario. Los datos extraídos para los proyectos de flujo de trabajo de orquestación no cuentan para el total.*


## <a name="throughput-limits"></a>Límites de rendimiento

|Elemento | Límite |
--- | --- 
|Llamadas de creación| 60 solicitudes por minuto|
|Llamadas de predicción| 60 solicitudes por minuto|

## <a name="quota-limits"></a>Límites de cuota

| Elemento | Límite |
--- | --- 
|Llamadas de creación| Sin límite|
|Llamadas de predicción| 100 000 al mes|

## <a name="next-steps"></a>Pasos siguientes

[Introducción al reconocimiento del lenguaje conversacional](overview.md)
