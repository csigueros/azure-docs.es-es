---
title: Aptitud cognitiva División de texto
titleSuffix: Azure Cognitive Search
description: Divida texto en fragmentos o páginas de texto en función de la longitud de una canalización de enriquecimiento con IA de Azure Cognitive Search.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: e5b907b89491721d2529f2caa303fc9e77d47169
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862580"
---
# <a name="text-split-cognitive-skill"></a>Aptitud cognitiva División de texto

La aptitud **División de texto** divide el texto en fragmentos de texto. Puede especificar si desea dividir el texto en oraciones o en páginas de una longitud determinada. Esta aptitud es especialmente útil si hay requisitos de longitud de texto máxima en otras aptitudes de bajada. 

> [!NOTE]
> Esta capacidad no está enlazada a Cognitive Services. No es facturable y no tiene ningún requisito de clave de Cognitive Services.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas.

| Nombre de parámetro     | Descripción |
|--------------------|-------------|
| `textSplitMode`    | `pages` o `sentences` | 
| `maximumPageLength` | Solo se aplica si `textSplitMode` está establecido en `pages`. Esto hace referencia a la longitud máxima de página en caracteres medida mediante `String.Length`. El valor mínimo es 300, el máximo es 100 000 y el valor predeterminado es 10 000.  El algoritmo hará todo lo posible para dividir el texto en los límites de oraciones, por lo que el tamaño de cada fragmento puede ser ligeramente menor que `maximumPageLength`. | 
| `defaultLanguageCode` | (Opcional) Uno de los siguientes códigos de idioma: `am, bs, cs, da, de, en, es, et, fr, he, hi, hr, hu, fi, id, is, it, ja, ko, lv, no, nl, pl, pt-PT, pt-BR, ru, sk, sl, sr, sv, tr, ur, zh-Hans`. El valor predeterminado es inglés (en). Aspectos que se deben tener en cuenta:<ul><li>Proporcionar un código de idioma es útil para evitar cortar una palabra por la mitad para idiomas sin espacios como el chino, japonés y coreano.</li><li>Si no conoce el idioma (es decir, debe dividir el texto de la entrada en [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), el valor predeterminado de inglés (en) debe ser suficiente. </li></ul>  |


## <a name="skill-inputs"></a>Entradas de la aptitud

| Nombre de parámetro       | Descripción      |
|----------------------|------------------|
| `text`    | Texto que se dividirá en subcadenas. |
| `languageCode`    | (Opcional) Código de idioma para el documento. Si no conoce el idioma (es decir, debe dividir el texto de la entrada en [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), es seguro quitar esta entrada. Si el idioma no está en la lista admitida para el parámetro `defaultLanguageCode` anterior, se emitirá una advertencia y el texto no se dividirá.  |

## <a name="skill-outputs"></a>Salidas de la aptitud 

| Nombre de parámetro     | Descripción |
|--------------------|-------------|
| `textItems`   | Una matriz de subcadenas que se han extraído. |


##  <a name="sample-definition"></a>Definición de ejemplo

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
        }
    ]
}
```

##  <a name="sample-input"></a>Entrada de ejemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Salida de ejemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Casos de error
Si no se admite un idioma, se genera una advertencia.

## <a name="see-also"></a>Consulte también

+ [Aptitudes integradas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
