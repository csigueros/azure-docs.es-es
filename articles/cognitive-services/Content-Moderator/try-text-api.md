---
title: 'Moderación del texto mediante Text Moderation API: Content Moderator'
titleSuffix: Azure Cognitive Services
description: Pruebe la moderación de texto mediante Text Moderation API en la consola en línea.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/27/2021
ms.openlocfilehash: 965d8f420f25dead5709c333ffaaa93cf64ead13
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454720"
---
# <a name="moderate-text-from-the-api-console"></a>Moderación de texto desde la consola de API

Use la [API de moderación de texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) de Azure Content Moderator para examinar el texto, detectar las blasfemias y compararlo con listas personalizadas y compartidas.

## <a name="get-your-api-key"></a>Obtener la clave de API

Antes de probar la API en la consola en línea, necesita la clave de suscripción. Se encuentra en la pestaña **Configuración**, en el cuadro **Ocp-Apim-Subscription-Key**. Para más información, consulte [Información general](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navegar hasta la referencia de API

Vaya a la [referencia de Text Moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Se abre la página **Text - Screen** (Texto: filtrar).

## <a name="open-the-api-console"></a>Abrir la consola de API

En **Open API testing console** (Abrir consola de pruebas de API), seleccione la región que mejor describa su ubicación. 

  ![Selección de la región en la página Text - Screen (Texto: filtrar)](images/test-drive-region.png)

  Se abre la consola de API de **Text - Screen** (Texto: filtrar).

## <a name="select-the-inputs"></a>Seleccionar las entradas

### <a name="parameters"></a>Parámetros

Seleccione los parámetros de consulta que quiere usar en la pantalla de texto. En este ejemplo, utilice el valor predeterminado de **language** (idioma). También puede dejarlo en blanco, ya que la operación detectará automáticamente el idioma probable como parte de su ejecución.

> [!NOTE]
> Para el parámetro **language** (idioma), asigne `eng` o déjelo en blanco para ver la respuesta **classification** (clasificación) asistida por máquina. **Esta característica solo admite inglés**.
>
> Para la detección de **palabras soeces**, use el [código ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) de los idiomas admitidos que se enumeran en este artículo, o deje el campo en blanco.

Para **autocorrect** (autocorrección), **PII** (DCP) y **classify (preview)** [clasificar (versión preliminar)], seleccione **true**. Deje el campo **ListId** vacío.

  ![Parámetros de la consulta de la consola Text - Screen (Texto: filtrar)](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tipo de contenido

Para **Content-Type**, seleccione el tipo de contenido que quiera filtrar. Para este ejemplo, use el tipo predeterminado **text/plain** (texto/sin formato). En el cuadro **Ocp-Apim-Subscription-Key**, especifique la clave de suscripción.

### <a name="sample-text-to-scan"></a>Texto de ejemplo que se va a examinar

En el cuadro **Request body** (Cuerpo de la solicitud), escriba algo de texto. En el ejemplo siguiente se muestra un error intencionado en el texto.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Análisis de la respuesta

La respuesta siguiente muestra las distintas conclusiones desde la API. Contiene posibles palabras soeces, datos personales, clasificación (versión preliminar) y la versión corregida automáticamente.

> [!NOTE]
> La característica "Classification" (Clasificación) asistida por máquina está en versión preliminar y solo admite inglés.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
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
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

Para una explicación detallada de todas las secciones de la respuesta JSON, consulte la guía conceptual de [moderación de texto](text-moderation-api.md).

## <a name="next-steps"></a>Pasos siguientes

Use la API REST del código o siga el [inicio rápido del SDK de .NET](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) para realizar la integración con la aplicación.