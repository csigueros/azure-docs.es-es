---
title: 'Moderación de texto: Content Moderator'
titleSuffix: Azure Cognitive Services
description: Use la moderación de texto para detectar posible texto no deseado, datos personales y una lista de términos personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: pafarley
ms.openlocfilehash: e001e268a5e805fbab7cc84e7248750bd58e911e
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822865"
---
# <a name="learn-text-moderation-concepts"></a>Información sobre los conceptos de moderación de texto

Use los modelos de moderación de texto de Content Moderator para analizar el contenido de texto.

Puede bloquear, aprobar o revisar el contenido en función de las directivas y los umbrales (consulte [Revisiones, flujos de trabajo y trabajos](./review-api.md) para aprender a configurar revisiones humanas). Use los modelos de moderación de texto para aumentar la moderación humana de los entornos donde asociados, empleados y consumidores generan contenido de texto. Puede tratarse de salas de chat, paneles de discusión, bots de chat, catálogos de comercio electrónico y documentos.

La respuesta del servicio incluye la siguiente información:

- Palabras soeces: coincidencias basadas en términos con la lista integrada de términos soeces en varios idiomas
- Clasificación: clasificación asistida por máquina en tres categorías
- Datos personales
- Texto corregido automáticamente
- Texto original
- Idioma

## <a name="profanity"></a>Palabras soeces

Si la API detecta un término soez en cualquiera de los [idiomas admitidos](./language-support.md), estos términos se incluyen en la respuesta. La respuesta contiene también su ubicación (`Index`) en el texto original. El `ListId` en el siguiente ejemplo de JSON hace referencia a los términos encontrados en las [listas de términos personalizadas](try-terms-list-api.md), si hubiera alguna.

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }
```

> [!NOTE]
> Para el parámetro **language** (idioma), asigne `eng` o déjelo en blanco para ver la respuesta **classification** (clasificación) asistida por máquina. **Esta característica solo admite inglés**.
>
> Para la detección de **palabras soeces**, use el [código ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) de los idiomas admitidos que se enumeran en este artículo, o deje el campo en blanco.

## <a name="classification"></a>clasificación

La **característica de clasificación de texto** asistida por máquina de Content Moderator admite **únicamente el idioma inglés** y ayuda a detectar contenido potencialmente no deseado. El contenido marcado puede considerarse como inapropiado según el contexto. Transmite la probabilidad de cada categoría y puede recomendar una revisión humana. La característica utiliza un modelo entrenado para identificar posible lenguaje ofensivo, despectivo o discriminatorio. Esto incluye jerga, palabras abreviadas, palabras ofensivas y palabras mal escritas intencionadamente. 

El siguiente extracto de JSON muestra una salida de ejemplo:

```json
"Classification": {
    "ReviewRecommended": true,
    "Category1": {
        "Score": 1.5113095059859916E-06
    },
    "Category2": {
        "Score": 0.12747249007225037
    },
    "Category3": {
        "Score": 0.98799997568130493
    }
}
```

### <a name="explanation"></a>Explicación

- `Category1` se refiere a la posible presencia de lenguaje que se puede considerar sexualmente explícito o para adultos en ciertas situaciones.
- `Category2` se refiere a la posible presencia de lenguaje que se puede considerar sexualmente insinuante o para adultos en ciertas situaciones.
- `Category3` se refiere a la posible presencia de lenguaje que se puede considerar ofensivo en ciertas situaciones.
- `Score` se encuentra entre 0 y 1. Cuanto más alta sea la puntuación, mayor será la predicción del modelo sobre la aplicabilidad de la categoría. Esta característica usa un modelo estadístico, en lugar de resultados codificados manualmente. Se recomienda realizar pruebas con contenido propio para determinar cómo se alinea cada categoría en función de sus propios requisitos.
- `ReviewRecommended` es true o false según los umbrales de puntuación internos. Los clientes deben evaluar si quieren usar este valor o decidir si desean umbrales personalizados basados en sus directivas de contenido.

## <a name="personal-data"></a>Datos personales

La característica de datos personales detecta la posible presencia de esta información:

- Dirección de correo electrónico
- Dirección de correo postal en EE. UU.
- Dirección IP
- Número de teléfono en EE. UU.

El siguiente es un ejemplo de respuesta:

```json
"pii":{
  "email":[
      {
        "detected":"abcdef@abcd.com",
        "sub_type":"Regular",
        "text":"abcdef@abcd.com",
        "index":32
      }
  ],
  "ssn":[

  ],
  "ipa":[
      {
        "sub_type":"IPV4",
        "text":"255.255.255.255",
        "index":72
      }
  ],
  "phone":[
      {
        "country_code":"US",
        "text":"6657789887",
        "index":56
      }
  ],
  "address":[
      {
        "text":"1 Microsoft Way, Redmond, WA 98052",
        "index":89
      }
  ]
}
```

## <a name="auto-correction"></a>Corrección automática

Imagine que el texto de entrada es (los valores "qu!ck", "f0x" e "lzay" son intencionados):

> The qu!ck brown f0x jumps over the lzay dog.

Si solicita corrección automática, la respuesta contiene la versión corregida del texto:

> The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Creación y administración de las listas personalizadas de términos

Si bien la lista global y predeterminada de términos funciona muy bien para la mayoría de los casos, puede que desee analizar términos específicos para sus necesidades empresariales. Por ejemplo, quizá desee filtrar nombres de marcas de la competencia presentes en las publicaciones de los usuarios.

> [!NOTE]
> Hay un límite máximo de **5 listas de términos** y cada lista **no debe superar los 10 000 términos**.
>

En el ejemplo siguiente se muestra el identificador de la lista de coincidencias:

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }
```

Content Moderator proporciona una [API de lista de términos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) con operaciones para administrar listas de términos personalizadas. Comience con la [consola de API de listas de términos](try-terms-list-api.md) y use los ejemplos de código de la API REST. Consulte también el [inicio rápido de .NET de listas de términos](term-lists-quickstart-dotnet.md) si está familiarizado con C# y Visual Studio.

## <a name="next-steps"></a>Pasos siguientes

Pruebe las API con la [consola de API de moderación de texto](try-text-api.md). Consulte también [Revisiones, flujos de trabajo y trabajos](./review-api.md) para aprender a configurar revisiones humanas.
