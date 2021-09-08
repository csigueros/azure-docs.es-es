---
title: 'Instrucciones para las transcripciones con etiqueta humana: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Para mejorar la precisión del reconocimiento de voz, en especial cuando hay eliminaciones o sustituciones incorrectas de palabras, puede usar las transcripciones con etiqueta humana junto con los datos de audio. Las transcripciones con etiqueta humana son transcripciones textuales, palabra por palabra, de un archivo de audio.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: pafarley
ms.openlocfilehash: 562c603fbf549af3877a6aa408456845ad9c886e
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538820"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Creación de transcripciones con etiqueta humana

Si quiere mejorar la precisión del reconocimiento, en especial cuando se producen problemas por eliminaciones o sustituciones incorrectas de palabras, le interesa usar las transcripciones con etiqueta humana junto con los datos de audio. ¿Qué son las transcripciones con etiqueta humana? Fácil; son transcripciones textuales, palabra por palabra, de un archivo de audio.

Se necesita una muestra grande de datos de transcripciones para mejorar el reconocimiento; se recomienda proporcionar entre 1 y 20 horas de datos de transcripción. El servicio Voz usará hasta 20 horas de audio para el entrenamiento. En esta página revisaremos las instrucciones diseñadas para ayudarle a crear transcripciones de alta calidad. Esta guía se divide por configuración regional, con secciones para inglés de Estados Unidos, chino mandarín y alemán.

> [!NOTE]
> No todos los modelos base admiten la personalización con archivos de audio. Si un modelo base no lo admite, el entrenamiento solo usará el texto de las transcripciones de la misma manera en que se usa el texto relacionado. Consulte la [compatibilidad con idiomas](language-support.md#speech-to-text) para obtener una lista de los modelos base que admiten el entrenamiento con datos de audio.

> [!NOTE]
> En los casos en los que cambia el modelo base utilizado para el entrenamiento y tiene audio en el conjunto de datos de entrenamiento, compruebe *siempre* si el nuevo modelo base seleccionado [admite el entrenamiento con datos de audio](language-support.md#speech-to-text). Si el modelo base usado anteriormente no admitía el entrenamiento con datos de audio, y el conjunto de datos de entrenamiento contiene audio, el tiempo de entrenamiento con el nuevo modelo base aumentará **drásticamente** y puede pasar de horas a días e incluso más. Esto es especialmente cierto si la suscripción del servicio Voz **no** está en una [región con hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para el entrenamiento.
>
> Si se encuentra con el problema que se describe en el párrafo anterior, puede reducir rápidamente el tiempo de entrenamiento reduciendo la cantidad de audio del conjunto de datos o eliminándolo por completo y dejando solo el texto. La ultima opción es muy recomendable si la suscripción del servicio Voz **no** está en una [región con hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para el entrenamiento.

## <a name="us-english-en-us"></a>Inglés de Estados Unidos (en-US)

Las transcripciones con etiqueta humana para audio inglés deben proporcionarse como texto sin formato, utilizando solo caracteres ASCII. Debe evitarse el uso de caracteres de puntuación Latino-1 o Unicode. A menudo estos caracteres se agregan involuntariamente al copiar texto de una aplicación de procesamiento de texto o al extraer datos de páginas web. Si aparecen estos caracteres, asegúrese de que se actualizan con la sustitución por el carácter ASCII correspondiente.

Estos son algunos ejemplos:

| Caracteres para evitar | Sustitución | Notas |
| ------------------- | ------------ | ----- |
| “Hello world” | "Hello world" | Las comillas de apertura y cierre se han sustituido por los caracteres ASCII adecuados. |
| John’s day | John's day | El apóstrofo se ha sustituido por el carácter ASCII adecuado. |
| it was good—no, it was great! | it was good--no, it was great! | El guion largo se ha sustituido por dos guiones. |

### <a name="text-normalization-for-us-english"></a>Normalización de texto para inglés de Estados Unidos

La normalización de texto es la transformación de palabras en un formato coherente que se utiliza al entrenar un modelo. Algunas reglas de normalización se aplican al texto automáticamente; sin embargo, se recomienda seguir estas instrucciones a la hora de preparar los datos de transcripción con etiqueta humana:

- Escriba las abreviaturas con todas las palabras.
- Escriba las cadenas numéricas no estándar con todas las palabras (por ejemplo, términos contables).
- Los caracteres que no son alfabéticos o los caracteres alfanuméricos combinados deben transcribirse tal como se pronuncian.
- Las abreviaturas que se pronuncian como palabras no deben editarse (por ejemplo, "radar", "laser", "RAM" o "NATO").
- Escriba las abreviaturas que se pronuncian como letras independientes con cada letra separada por un espacio.
- Si usa audio, transcriba los números como palabras que coincidan con el audio (por ejemplo, "101" podría pronunciarse como "uno cero uno" o "ciento uno").
- Evite repetir caracteres, palabras o grupos de palabras más de tres veces, como "sí sí sí sí". El servicio Voz podría quitar las líneas con estas repeticiones.

Estos son algunos ejemplos de normalización que debe realizar en la transcripción:

| Texto original               | Texto después de la normalización              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce Banner            | Doctor Bruce Banner                   |
| James Bond, 007             | James Bond double oh seven           |
| Ke$ha                       | Kesha                                 |
| How long is the 2x4         | How long is the two by four           |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| My blood type is O+         | My blood type is O positive           |
| Water is H20                | Water is H 2 O                        |
| Play OU812 by Van Halen     | Play O U 8 1 2 by Van Halen           |
| UTF-8 with BOM              | U T F 8 with BOM                      |

Las siguientes reglas de normalización se aplican automáticamente a las transcripciones:

- Se utilizan letras minúsculas.
- Se quitan todos los signos de puntuación, excepto los apóstrofos dentro de las palabras.
- Se expanden los números de palabras o formas habladas, como los importes en dólares.

Estos son algunos ejemplos de normalización que se realiza de modo automático en la transcripción:

| Texto original                          | Texto después de la normalización          |
| -------------------------------------- | --------------------------------- |
| "Holy cow!" said Batman.               | holy cow said batman              |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em!                            | go get em                         |
| I'm double-jointed                     | i’m double jointed                |
| 104 Elm Street                         | one oh four Elm street            |
| Tune to 102.7                          | tune to one oh two point seven    |
| Pi is about 3.14                       | pi is about three point one four  |
| It costs \$3.14                        | it costs three fourteen           |

## <a name="mandarin-chinese-zh-cn"></a>Chino mandarín (zh-CN)

Las transcripciones con etiqueta humana de audio en chino mandarín deben codificarse como UTF-8 con un marcador de orden de bytes. Evite el uso de caracteres de puntuación de ancho medio. Estos caracteres se pueden incluir de forma involuntaria al preparar los datos en un programa de procesamiento de texto o al extraerlos de páginas web. Si aparecen estos caracteres, asegúrese de que se actualizan con la sustitución por el ancho completo correspondiente.

Estos son algunos ejemplos:

| Caracteres para evitar | Sustitución   | Notas |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | Las comillas de apertura y cierre se han sustituido por los caracteres adecuados. |
| 需要什么帮助? | 需要什么帮助？| El signo de interrogación se ha sustituido por el carácter adecuado. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalización de texto para chino mandarín

La normalización de texto es la transformación de palabras en un formato coherente que se utiliza al entrenar un modelo. Algunas reglas de normalización se aplican al texto automáticamente; sin embargo, se recomienda seguir estas instrucciones a la hora de preparar los datos de transcripción con etiqueta humana:

- Escriba las abreviaturas con todas las palabras.
- Escriba cadenas numéricas en forma hablada.

Estos son algunos ejemplos de normalización que debe realizar en la transcripción:

| Texto original | Texto después de la normalización |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

Las siguientes reglas de normalización se aplican automáticamente a las transcripciones:

- Se quitan todos los signos de puntuación.
- Los números se expanden a la forma hablada.
- Las letras de ancho completo se convierten en letras de ancho medio.
- Uso de letras mayúsculas para todas las palabras en inglés

Estos son algunos ejemplos de normalización que se realiza de modo automático en la transcripción:

| Texto original | Texto después de la normalización |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| ￥ 3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Alemán (de-DE) y otros idiomas

Las transcripciones con etiqueta humana de audio en alemán (y otros idiomas distintos del inglés o el chino mandarín) deben codificarse como UTF-8 con un marcador de orden de bytes. Debe proporcionarse una transcripción con etiqueta humanos para cada archivo de audio.

### <a name="text-normalization-for-german"></a>Normalización de texto para alemán

La normalización de texto es la transformación de palabras en un formato coherente que se utiliza al entrenar un modelo. Algunas reglas de normalización se aplican al texto automáticamente; sin embargo, se recomienda seguir estas instrucciones a la hora de preparar los datos de transcripción con etiqueta humana:

- Los puntos decimales se escriben como "," y no como ".".
- Los separadores de hora se escriben como ":"y no como "." (por ejemplo: 12:00 Uhr).
- Las abreviaturas, como "ca." no se reemplazan. Se recomienda usar la forma hablada completa.
- Se quitan los cuatro operadores matemáticos principales: +, -, \*, /. Se recomienda sustituirlos por su forma escrita: "plus", "minus", "mal" y "geteilt".
- Se quitan los operadores de comparación (=, <, y >). Se recomienda sustituirlos con "gleich", "kleiner als," y "grösser als".
- Las fracciones, como 3/4, se usan con la forma escrita (por ejemplo, "drei viertel" en lugar de 3/4).
- El símbolo "€" se reemplaza por su forma escriba "Euro".

Estos son algunos ejemplos de normalización que debe realizar en la transcripción:

| Texto original    | Texto después de la normalización del usuario | Texto después de la normalización del sistema       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

Las siguientes reglas de normalización se aplican automáticamente a las transcripciones:

- Se usan letras en minúsculas para todo el texto.
- Se quita toda la puntuación, incluidos varios tipos de comillas ("prueba", 'prueba', "prueba y «prueba» son correctos).
- Se descartan todas las filas que contengan un carácter especial de este conjunto: ¢ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬.
- Se expanden los números a la forma hablada, incluidos importes en dólares o euros.
- Se acepta la diéresis solo a, o y u. Otras se reemplazará por "th" o se descartarán.

Estos son algunos ejemplos de normalización que se realiza de modo automático en la transcripción:

| Texto original    | Texto después de la normalización |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

### <a name="text-normalization-for-japanese"></a>Normalización de texto para japonés

En japonés (ja-JP), hay una longitud máxima de 90 caracteres por cada frase. Las líneas con frases más largas se descartan. Para agregar texto más largo, inserte un punto en el medio.

## <a name="next-steps"></a>Pasos siguientes

- [Preparación y prueba de los datos](./how-to-custom-speech-test-and-train.md)
- [Inspección de los datos](how-to-custom-speech-inspect-data.md)
- [Evaluación de los datos](how-to-custom-speech-evaluate-data.md)
- [Entrenamiento del modelo](how-to-custom-speech-train-model.md)
- [Implementación del modelo](./how-to-custom-speech-train-model.md)