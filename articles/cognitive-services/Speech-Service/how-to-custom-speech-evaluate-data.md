---
title: 'Evaluación y mejorar de la precisión de Habla personalizada: Servicio de voz'
titleSuffix: Azure Cognitive Services
description: En este documento aprenderá a medir cuantitativamente y a mejorar la calidad del modelo de conversión de voz en texto o su modelo personalizado. Para probar la voz se requieren datos de transcripción de con la etiqueta audio + humano, y se deben proporcionar entre 30 minutos y 5 horas de audio representativo.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: lajanuar
ms.openlocfilehash: 03fb3e5b5363d57aac7a0e9636dae1dd0fb95141
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122067507"
---
# <a name="evaluate-and-improve-custom-speech-accuracy"></a>Evaluación y mejora de la precisión de Habla personalizada

En este artículo, aprenderá a medir cuantitativamente y a mejorar la precisión de los modelos de conversión de voz en texto de Microsoft o sus propios modelos personalizados. Para probar la voz se requieren datos de transcripción de con la etiqueta audio + humano, y se deben proporcionar entre 30 minutos y 5 horas de audio representativo.

## <a name="evaluate-custom-speech-accuracy"></a>Evaluación de la precisión de Habla personalizada

Es el estándar del sector para medir la precisión [del modelo](https://en.wikipedia.org/wiki/Word_error_rate) (WER). WER cuenta el número de palabras incorrectas identificadas durante el reconocimiento y luego las divide entre el número total de palabras proporcionadas en la transcripción con la etiqueta humana (se muestra a continuación como N). Por último, ese número se multiplica por 100 % para calcular la tasa WER.

![Fórmula de WER](./media/custom-speech/custom-speech-wer-formula.png)

Las palabras identificadas incorrectamente pertenecen a tres categorías:

* Inserción (I): palabras que se agregan incorrectamente en la transcripción de hipótesis.
* Eliminación (D): palabras que no se detectan en la transcripción de hipótesis.
* Sustitución (S): palabras que se sustituyeron entre la referencia y la hipótesis.

Este es un ejemplo:

![Ejemplo de palabras identificadas incorrectamente](./media/custom-speech/custom-speech-dis-words.png)

Si quiere replicar las medidas WER de manera local, puede usar sclite desde [SCTK](https://github.com/usnistgov/SCTK).

## <a name="resolve-errors-and-improve-wer"></a>Resolución de errores y mejora de WER

Puede usar WER a partir de los resultados de reconocimiento automático para evaluar la calidad del modelo que usa con su aplicación, herramienta o producto. Una tasa WER de entre un 5 y un 10 % se considera buena calidad y está listo para usarse. Una tasa WER de 20 % es aceptable, pero quizás considere la posibilidad de entrenamiento adicional. Una tasa WER de 30 % o más señala una calidad deficiente y la necesidad de personalización y entrenamiento.

El modo en que se distribuyen los errores es importante. Si se encuentran muchos errores de eliminación, la causa suele ser la intensidad débil de señal de audio. Para resolver este problema, debe recopilar los datos de audio más cerca de la fuente. Los errores de inserción significan que el audio se grabó en un entorno ruidoso y pueden producirse interferencias, lo que ocasiona problemas de reconocimiento. Los errores de sustitución se encuentran a menudo cuando se ha proporcionado una muestra insuficiente de términos específicos del dominio como transcripciones con la etiqueta humano o texto relacionado.

Al analizar archivos individuales, puede determinar qué tipo de errores existen y qué errores son específicos de un determinado archivo. Comprender los problemas en el nivel de archivo le ayudará a identificar las mejoras.

## <a name="create-a-test"></a>Creación de una prueba

Si quiere probar la calidad del modelo de línea de base de texto a voz de Microsoft o un modelo personalizado que haya entrenado, puede comparar dos modelos en paralelo para evaluar la precisión. La comparación incluye los resultados de reconocimiento y WER. Normalmente, un modelo personalizado se compara con el modelo de línea de base de Microsoft.

Para evaluar los modelos en paralelo:

1. Inicie sesión en el [portal de Habla personalizada](https://speech.microsoft.com/customspeech).
2. Vaya a **Speech-to-text > Custom Speech > [nombre del proyecto] > Pruebas**.
3. Haga clic en **Add test** (Agregar prueba).
4. Seleccione **Evaluate accuracy** (Evaluar precisión). Proporcione a la prueba un nombre y una descripción, y seleccione el conjunto de datos de transcripción con la etiqueta audio + humano.
5. Puede seleccionar hasta dos modelos para probar.
6. Haga clic en **Crear**.

Después de que la prueba se ha creado correctamente, puede comparar los resultados en paralelo.

### <a name="side-by-side-comparison"></a>Comparación en paralelo

Una vez finalizada la prueba, que viene indicado por el cambio de estado a *Succeeded* (Correcto), encontrará un número de WER para ambos modelos incluidos en la prueba. Haga clic en el nombre de la prueba para ver la página de detalles de las pruebas. En esta página se muestran todas las expresiones del conjunto de datos y se indican los resultados del reconocimiento de los dos modelos al lado de la transcripción del conjunto de datos enviado. Con el fin de inspeccionar la comparación en paralelo, puede alternar los distintos tipos de error, como inserción, eliminación y sustitución. Al escuchar el audio y comparar los resultados del reconocimiento de cada columna, que muestra la transcripción con la etiqueta humano y los resultados de los dos modelos de voz a texto, puede decidir qué modelo satisface sus necesidades y dónde hacen falta entrenamiento y mejoras adicionales.

## <a name="improve-custom-speech-accuracy"></a>Mejora de la precisión de Habla personalizada

Los escenarios de reconocimiento de voz varían en función de la calidad y del idioma (vocabulario y estilo del habla) del audio. En la siguiente tabla se examinan cuatro escenarios habituales:

| Escenario | Calidad de audio | Vocabulario | Estilo del habla |
|----------|---------------|------------|----------------|
| Centro de llamadas | Baja, 8 kHz, podrían ser dos seres humanos en un canal de audio, se podría comprimir. | Limitado, único para dominio y productos. | Conversacional, estructura flexible. |
| Asistente por voz (como Cortana o una ventana de autoservicio) | Alta, 16 kHz. | Entidad intensa (títulos de canciones, productos y ubicaciones). | Palabras y frases claramente indicadas. |
| Dictado (mensaje instantáneo, notas, búsqueda) | Alta, 16 kHz. | Varía | Toma de notas. |
| Subtítulos de vídeos | Variada, incluido el uso variado del micrófono, con música. | Variado (reuniones, voz recitada, letras de canciones). | Lectura, preparada o estructura flexible. |

Los distintos escenarios generan resultados de distinta calidad. En la tabla siguiente se examina cómo se evalúa el contenido de estos cuatro escenarios en [Word Error Rate (WER)](how-to-custom-speech-evaluate-data.md). En la tabla se muestran los tipos de error más comunes en cada escenario.

| Escenario | Calidad del reconocimiento de voz | Errores de inserción | Errores de eliminación | Errores de sustitución |
|----------|----------------------------|------------------|-----------------|---------------------|
| Centro de llamadas | Medio (WER inferior al 30 %) | Pocos, excepto cuando otras personas hablan en segundo plano. | Pueden ser muchos. En los centros de llamadas puede haber mucho ruido y el hecho de que haya muchas personas hablando a la vez puede confundir al modelo. | Mediana. Los nombres de los productos y personas pueden provocar estos errores. |
| Asistente por voz | Alta (el WER puede ser inferior al 10 %) | Bajo | Bajo | Medio, debido títulos de canciones, nombres de producto o ubicaciones. |
| Dictado | Alta (el WER puede ser inferior al 10 %) | Bajo | Bajo | Alto |
| Subtítulos de vídeos | Depende del tipo de vídeo (el WER puede ser inferior al 50 %) | Bajo | Puede ser un número elevado debido a la música, a ruidos y a la calidad del micrófono. | La jerga puede generar estos errores. |

La determinación de los componentes del WER (número de errores de inserción, eliminación y sustitución) ayuda a determinar qué tipo de datos se van a agregar para mejorar el modelo. Use el [portal de Habla personalizada](https://speech.microsoft.com/customspeech) para ver la calidad de un modelo de referencia. El portal notifica las tasas de error de inserción, sustitución y eliminación que se combinan en la tasa de calidad del WER.

## <a name="improve-model-recognition"></a>Mejora del reconocimiento de modelos

Para reducir los errores de reconocimiento, agregue datos de entrenamiento en el [portal de Habla personalizada](https://speech.microsoft.com/customspeech). 

Agregue materiales de origen periódicamente para realizar un mantenimiento del modelo personalizado. El modelo personalizado necesita entrenamiento adicional para mantenerse al tanto de los cambios que se producen en sus entidades. Por ejemplo, es posible que necesite actualizar los nombres de producto, los nombres de canciones o las nuevas ubicaciones del servicio.

En las secciones siguientes se describe de qué forma puede reducir el número de errores cada uno de los tipo de datos de entrenamiento adicionales.

### <a name="add-related-text-sentences"></a>Adición de oraciones de texto relacionadas

Cuando entrene un nuevo modelo personalizado, comience por agregar el texto relacionado para mejorar el reconocimiento de las palabras y frases específicas del dominio. Las oraciones de texto relacionadas pueden reducir principalmente los errores de sustitución debidos al reconocimiento erróneo de palabras comunes y palabras específicas del dominio, ya que las muestra en contexto. Las palabras específicas del dominio pueden ser palabras poco frecuentes o compuestas, pero su pronunciación debe ser fácil de reconocer.

> [!NOTE]
> Evite las oraciones de texto relacionadas que tengan ruido, como caracteres o palabras irreconocibles.

### <a name="add-audio-with-human-labeled-transcripts"></a>Adición de audio con transcripciones etiquetadas por humanos

El audio con transcripciones etiquetadas por humanos ofrece las máximas mejoras en cuando a la precisión si el audio procede del caso de uso de destino. Los ejemplos deben cubrir todo el ámbito de la voz. Por ejemplo, el centro de llamadas de una tienda recibirá sobre todo llamadas relacionadas con trajes de baño o gafas de sol en los meses de verano. Asegúrese de que el ejemplo incluya todo el ámbito de voz que desea detectar.

Tenga en cuenta estos detalles:

* El entrenamiento con audio aportará más ventajas si el audio también es difícil de entender para las personas. En la mayoría de los casos, debe iniciar el entrenamiento simplemente con texto relacionado.
* Si utiliza alguno de los idiomas más usados, como el inglés de Estados Unidos, existe la posibilidad de que no sea necesario entrenar con datos de audio. En el caso de estos idiomas, los modelos base ofrecen ya resultados de reconocimiento muy buenos en la mayoría de los escenarios. Probablemente será suficiente con entrenar con texto relacionado.
* Habla personalizada solo puede capturar el contexto de las palabras para reducir los errores de sustitución, no los de inserción ni los de eliminación.
* Evite ejemplos que incluyan errores de transcripción, pero incluya audio con distintas calidades.
* Evite oraciones no relacionadas con el dominio del problema. Las oraciones no relacionadas pueden dañar el modelo.
* Cuando la calidad de las transcripciones varía, puede duplicar las frases cuya calidad sea excepcionalmente buena (como las transcripciones excelentes que incluyan frases clave) para aumentar su ponderación.
* El servicio de voz usará automáticamente las transcripciones para mejorar el reconocimiento de las palabras y frases específicas del dominio, como si se hubieran agregado como texto relacionado.
* Una operación de entrenamiento puede tardar varios días en completarse. Para mejorar la velocidad de entrenamiento, asegúrese de crear la suscripción al servicio Voz en una [región con hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para el entrenamiento.

> [!NOTE]
> No todos los modelos base son compatibles con el entrenamiento con audio. Si un modelo base no es compatible, el Servicio de voz solo usará el texto de las transcripciones y omitirá el audio. Consulte la [compatibilidad con idiomas](language-support.md#speech-to-text) para obtener una lista de los modelos base que admiten el entrenamiento con datos de audio. Aunque un modelo base admita el entrenamiento con datos de audio, el servicio podría usar solo parte del audio. Aún así, usará todas las transcripciones.

> [!NOTE]
> En los casos en los que cambia el modelo base utilizado para el entrenamiento y tiene audio en el conjunto de datos de entrenamiento, compruebe *siempre* si el nuevo modelo base seleccionado [admite el entrenamiento con datos de audio](language-support.md#speech-to-text). Si el modelo base usado anteriormente no admitía el entrenamiento con datos de audio, y el conjunto de datos de entrenamiento contiene audio, el tiempo de entrenamiento con el nuevo modelo base aumentará **drásticamente** y puede pasar de horas a días e incluso más. Esto es especialmente cierto si la suscripción del servicio Voz **no** está en una [región con hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para el entrenamiento.
>
> Si se encuentra con el problema que se describe en el párrafo anterior, puede reducir rápidamente el tiempo de entrenamiento reduciendo la cantidad de audio del conjunto de datos o eliminándolo por completo y dejando solo el texto. La ultima opción es muy recomendable si la suscripción del servicio Voz **no** está en una [región con hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para el entrenamiento.

### <a name="add-new-words-with-pronunciation"></a>Adición de nuevas palabras con su pronunciación

Es posible que las palabras compuestas o cuyo grado de especialización sea elevado tengan pronunciaciones únicas. Estas palabras se pueden reconocer si cada una de ellas se puede desglosar en palabras más pequeñas para pronunciarla. Por ejemplo, para reconocer **Xbox**, pronúnciela como **X box**. Este enfoque no aumentará la precisión general, pero puede aumentar el reconocimiento de estas palabras clave.

> [!NOTE]
> Actualmente, esta técnica solo está disponible para algunos idiomas. Para más información, vea la personalización de la pronunciación en [la tabla de conversión de voz a texto](language-support.md).

## <a name="sources-by-scenario"></a>Orígenes por escenario

En la tabla siguiente se muestran escenarios de reconocimiento de voz y se enumeran los materiales de origen que se deben tener en cuenta en las tres categorías de contenido de entrenamiento mencionadas anteriormente.

| Escenario | Oraciones de texto relacionadas | Transcripciones de audio con etiqueta humana | Nuevas palabras con su pronunciación |
|----------|------------------------|------------------------------|------------------------------|
| Centro de llamadas             | Documentos de marketing, sitios web, reseñas de productos relacionados con la actividad del centro de llamadas. | Llamadas al centro de llamadas transcritas por humanos. | Términos que tienen pronunciación ambigua (vea el ejemplo anterior de Xbox). |
| Asistente por voz         | Enumerar oraciones que usan todas las combinaciones de comandos y entidades. | Grabar voces que dicen comandos a un dispositivo y transcribirlas en texto. | Nombres (películas, canciones, productos) que tienen pronunciaciones únicas. |
| Dictado               | Entrada escrita, como mensajes instantáneos o correos electrónicos. | Similar a la anterior. | Similar a la anterior. |
| Subtítulos de vídeos | Guiones de programas de TV, películas, contenido de marketing y resúmenes en vídeo. | Transcripciones exactas de vídeos. | Similar a la anterior. |

## <a name="next-steps"></a>Pasos siguientes

* [Entrenamiento e implementación de un modelo](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Recursos adicionales

* [Preparación y prueba de los datos](./how-to-custom-speech-test-and-train.md)
* [Inspección de los datos](how-to-custom-speech-inspect-data.md)