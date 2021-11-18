---
title: 'Preparación de los datos para Habla personalizada: servicio de Voz'
titleSuffix: Azure Cognitive Services
description: Al probar la precisión del reconocimiento de voz de Microsoft o entrenar sus modelos personalizados, necesitará datos de texto y de audio. En esta página, veremos los tipos de datos, cómo se usan y administran.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: eur
ms.custom: ignite-fall-2021
ms.openlocfilehash: 14cf969fa5aba3f3fddd6fedc63c2ccf19d71e08
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133489"
---
# <a name="prepare-data-for-custom-speech"></a>Preparación de los datos para Habla personalizada

Al probar la precisión del reconocimiento de voz de Microsoft o entrenar sus modelos personalizados, necesitará datos de texto y de audio. En esta página, tratamos los tipos de datos que necesita un modelo de voz personalizado.

## <a name="data-diversity"></a>Diversidad de datos

El texto y el audio usados para probar y entrenar un modelo personalizado deben incluir ejemplos de un conjunto diverso de oradores y escenarios que es preciso que el modelo reconozca.
Tenga en cuenta estos factores al recopilar datos para las pruebas y el entrenamiento de modelos personalizados:

* Los datos de texto y de audio de voz deben cubrir los tipos de instrucciones verbales que los usuarios realizarán al interactuar con el modelo. Por ejemplo, un modelo que eleva y reduce la temperatura necesita entrenamiento en las instrucciones que las personas pueden dar para solicitar tales cambios.
* Los datos deben incluir todas las variantes de la voz que el modelo tendrá que reconocer. Muchos factores pueden variar la voz, entre los que se incluyen los acentos, los dialectos, la combinación de idiomas, la edad, el género, el tono de voz, el nivel de estrés y la hora del día.
* Debe incluir ejemplos de diferentes entornos (en interior, en exteriores, con ruido de carretera) en los que se utilizará el modelo.
* El audio se debe recoger mediante dispositivos de hardware que utilizará el sistema de producción. Si el modelo necesita identificar la voz grabada en dispositivos de grabación de calidad variable, los datos de audio que proporcione para entrenar el modelo también deben representar los distintos escenarios.
* Puede agregar más datos al modelo más adelante, pero procure que el conjunto de datos sea diverso y representativo de las necesidades del proyecto.
* La inclusión de datos que *no* estén dentro de las necesidades de reconocimiento del modelo personalizado puede perjudicar la calidad general del reconocimiento, por lo que se recomienda no incluir datos que el modelo no necesite transcribir.

Un modelo entrenado en un subconjunto de escenarios solo puede funcionar correctamente en esos escenarios. Por consiguiente, es conveniente que elija minuciosamente los datos que representan el ámbito completo de los escenarios que necesita que el modelo personalizado reconozca.

> [!TIP]
> Comience con pequeños conjuntos de datos de ejemplo que coincidan con el idioma y la acústica con los que se encontrará el modelo.
> Por ejemplo, grabe un pequeño pero representativo ejemplo de audio en el mismo hardware y en el mismo entorno acústico que el modelo encontrará en escenarios de producción.
> Los pequeños conjuntos de datos representativos pueden sacar a la luz problemas antes de que haya invertido en la recopilación de un conjunto de datos mucho mayor para el entrenamiento.
>
> Para empezar a trabajar rápidamente, considere la posibilidad de usar datos de ejemplo. Consulte este repositorio de GitHub para obtener <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">datos de Habla personalizada de ejemplo </a>

## <a name="data-types"></a>Tipos de datos

En esta tabla se enumeran los tipos de datos aceptados, cuándo se debe utilizar cada tipo de datos y la cantidad recomendada. No todos los tipos de datos son necesarios para crear un modelo. Los requisitos de datos variarán dependiendo de si crea una prueba o entrena un modelo.

| Tipo de datos | Se usa para pruebas | Cantidad recomendada | Se utiliza para el entrenamiento | Cantidad recomendada |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Sí<br>Se utiliza para la inspección visual | Más de cinco archivos de audio | No | N/D |
| [Transcripciones de audio con etiqueta humana](#audio--human-labeled-transcript-data-for-trainingtesting) | Sí<br>Se utiliza para evaluar la precisión | De 0,5 a 5 horas de audio | Sí | De 1 a 20 horas de audio |
| [Texto sin formato](#plain-text-data-for-training) | No | N/a | Sí | De 1 a 200 MB de texto relacionado |
| [Texto estructurado](#structured-text-data-for-training-public-preview) (versión preliminar pública) | No | N/a | Sí | Hasta 10 clases con un máximo de 4000 elementos y hasta 50 000 frases de entrenamiento |
| [Pronunciación](#pronunciation-data-for-training) | No | N/a | Sí | 1 KB - 1 MB de texto de pronunciación |

Los archivos deben agruparse por tipo en un conjunto de datos y cargarse como archivo zip. Cada conjunto de datos solo puede contener un tipo de datos.

> [!TIP]
> Al entrenar un nuevo modelo, comience con datos de texto sin formato o datos de texto estructurado. Estos datos mejorarán el reconocimiento de términos y frases especiales. El entrenamiento con texto es mucho más rápido que el entrenamiento con audio (minutos en comparación con días).

> [!NOTE]
> No todos los modelos base son compatibles con el entrenamiento con audio. Si un modelo base no es compatible, el Servicio de voz solo usará el texto de las transcripciones y omitirá el audio. Consulte la [compatibilidad con idiomas](language-support.md#speech-to-text) para obtener una lista de los modelos base que admiten el entrenamiento con datos de audio. Aunque un modelo base admita el entrenamiento con datos de audio, el servicio podría usar solo parte del audio. Aún así, usará todas las transcripciones.
>
> En los casos en los que cambia el modelo base utilizado para el entrenamiento y tiene audio en el conjunto de datos de entrenamiento, compruebe *siempre* si el nuevo modelo base seleccionado [admite el entrenamiento con datos de audio](language-support.md#speech-to-text). Si el modelo base usado anteriormente no admitía el entrenamiento con datos de audio, y el conjunto de datos de entrenamiento contiene audio, el tiempo de entrenamiento con el nuevo modelo base aumentará **drásticamente** y puede pasar de horas a días e incluso más. Esto es especialmente cierto si la suscripción del servicio Voz **no** está en una [región con hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para el entrenamiento.
>
> Si se encuentra con el problema que se describe en el párrafo anterior, puede reducir rápidamente el tiempo de entrenamiento reduciendo la cantidad de audio del conjunto de datos o eliminándolo por completo y dejando solo el texto. La ultima opción es muy recomendable si la suscripción del servicio Voz **no** está en una [región con hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para el entrenamiento.
>
> En regiones con hardware dedicado para el entrenamiento, el servicio Voz usará hasta 20 horas de audio para el entrenamiento. En otras regiones, solo usará hasta 8 horas.

> [!NOTE]
> El entrenamiento con texto estructurado solo se admite para estas configuraciones regionales: en-US, en-UK, en-IN, de-DE, fr-FR, fr-CA, es-ES, es-MX y debe usar el modelo base más reciente para ellas. 
>
> En el caso de las configuraciones regionales que no admiten el entrenamiento con texto estructurado, el servicio tomará las frases de entrenamiento que no hagan referencia a ninguna clase como parte del entrenamiento con datos de texto sin formato.

## <a name="upload-data"></a>Carga de datos

Para cargar los datos, vaya a [Speech Studio](https://aka.ms/speechstudio/customspeech). Después de crear un proyecto, navegue a la pestaña **Conjuntos de datos de Voz** y haga clic en **Cargar datos** para iniciar el asistente y crear el primer conjunto de datos. Seleccione un tipo de datos de voz para el conjunto de datos y cargue los datos.

> [!NOTE]
> Si el tamaño de archivo del conjunto de datos supera los 128 MB, solo puede cargarlo con la opción *Blob de Azure o ubicación compartida*. También puede usar la [API de REST de conversión de voz en texto v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) para cargar un conjunto de datos de [cualquier tamaño permitido](speech-services-quotas-and-limits.md#model-customization). Vea la [siguiente sección](#upload-data-using-speech-to-text-rest-api-v30) para obtener detalles.

En primer lugar, debe especificar si el conjunto de datos se va a usar para **entrenamiento** o para **pruebas**. Hay muchos tipos de datos que se pueden cargar y usar para **entrenamiento** o **pruebas**. Cada conjunto de datos que cargue debe tener el formato correcto antes de la carga y cumplir los requisitos del tipo de datos elegido. Los requisitos se muestran en las secciones siguientes.

Una vez cargado el conjunto de datos, tiene algunas opciones:

* Puede navegar a la pestaña **Entrenar modelos personalizados** para entrenar un modelo personalizado.
* Puede ir a la pestaña **Modelos de prueba** para inspeccionar visualmente la calidad con datos de solo audio o evaluar la precisión con datos de transcripción etiquetada por usuarios y audio.

### <a name="upload-data-using-speech-to-text-rest-api-v30"></a>Carga de datos mediante la API de REST de conversión de voz en texto v3.0

Puede usar la [API de REST de conversión de voz en texto v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) para automatizar las operaciones relacionadas con los modelos personalizados. En concreto, puede usarla para cargar un conjunto de datos. Esto resulta especialmente útil si el archivo del conjunto de datos supera los 128 MB, ya que los archivos de ese tamaño no se pueden cargar mediante la opción *Archivo local* de Speech Studio. (También puede usar la opción *Blob de Azure o ubicación compartida* de Speech Studio para el mismo propósito que se describe en la sección anterior).

Para crear y cargar un conjunto de datos, use la solicitud [Crear conjunto de datos](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateDataset).

**Conjuntos de datos creados mediante la API de REST y proyectos de Speech Studio**

Un conjunto de datos creado con la API de REST de conversión de voz en texto v3.0 *no* se va a conectar a ninguno de los proyectos de Speech Studio, a menos que se especifique un parámetro especial en el cuerpo de la solicitud (vea a continuación). *No* se necesita conexión con un proyecto de Speech Studio para las operaciones de personalización de modelos, siempre que se realicen mediante la API de REST.

Al iniciar sesión en Speech Studio, su interfaz de usuario le avisa si detecta cualquier objeto no conectado (como conjuntos de datos cargados mediante la API de REST sin ninguna referencia de proyecto) y le ofrece conectar esos objetos a un proyecto existente. 

Para conectar el nuevo conjunto de datos a un proyecto existente de Speech Studio durante su carga, use [Crear conjunto de datos](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateDataset) y rellene el cuerpo de la solicitud según el formato siguiente:
```json
{
  "kind": "Acoustic",
  "contentUrl": "https://contoso.com/mydatasetlocation",
  "locale": "en-US",
  "displayName": "My speech dataset name",
  "description": "My speech dataset description",
  "project": {
    "self": "https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/projects/c1c643ae-7da5-4e38-9853-e56e840efcb2"
  }
}
```

La dirección URL de proyecto necesaria para el elemento `project` se puede obtener con la solicitud [Get Projects](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetProjects).

## <a name="audio--human-labeled-transcript-data-for-trainingtesting"></a>Datos de transcripción de audio y con etiqueta humanos para pruebas y entrenamiento

Los datos de transcripción etiquetada por usuarios y audio se pueden usar con fines de entrenamiento y prueba. Para mejorar los aspectos acústicos, como acentos ligeros, estilos de habla, ruidos de fondo o para medir la precisión de la conversión de voz en texto de Microsoft al procesar los archivos de audio, debe proporcionar transcripciones etiquetada por usuarios (palabra por palabra) para poder comparar. Aunque la transcripción con etiqueta humana a menudo requiere mucho tiempo, es necesario evaluar la precisión y entrenar al modelo para los casos de uso. Tenga en cuenta que las mejoras en el reconocimiento solo serán tan buenas como los datos proporcionados. Por ese motivo, es importante que solo se carguen las transcripciones de alta calidad.

Los archivos de audio pueden tener silencio al principio y al final de la grabación. Si es posible, incluya al menos medio segundo de silencio antes y después de la voz en cada archivo de ejemplo. Aunque el audio con un volumen de grabación bajo o con ruido de fondo molesto no resulta útil, no debería perjudicar al modelo personalizado. Considere siempre la posibilidad de actualizar los micrófonos y el hardware de procesamiento de la señal antes de recopilar muestras de audio.

| Propiedad                 | Value                               |
|--------------------------|-------------------------------------|
| Formato de archivo              | RIFF (WAV)                          |
| Velocidad de muestreo              | 8000 o 16 000 Hz               |
| Canales                 | 1 (mono)                            |
| Longitud máxima por audio | 2 horas (pruebas)/60 s (entrenamiento) |
| Formato de ejemplo            | PCM, 16 bits                         |
| Formato de archivo           | .zip                                |
| Tamaño máximo de archivo zip         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Al cargar los datos del entrenamiento y de las pruebas, el archivo ZIP no puede superar los 2 GB. Solo puede realizar pruebas desde un *único* conjunto de datos; asegúrese de mantenerlo dentro del tamaño de archivo adecuado. Además, cada archivo de entrenamiento no puede superar los 60 segundos, ya que, si lo hace, se producirá un error.

Para abordar problemas como la eliminación o sustitución de palabras, se necesita una cantidad significativa de datos para mejorar el reconocimiento. Por lo general, se recomienda proporcionar transcripciones palabra por palabra para aproximadamente de 1 a 20 horas de audio. Sin embargo, solo 30 minutos pueden ayudar a mejorar los resultados del reconocimiento. Las transcripciones para todos los archivos WAV deben incluirse en un único archivo de texto sin formato. Cada línea del archivo de transcripción debe contener el nombre de uno de los archivos de audio, seguido de la transcripción correspondiente. El nombre de archivo y la transcripción deben estar separados por un carácter de tabulación (\t).

Por ejemplo:

<!-- The following example contains tabs. Don't accidentally convert these into spaces. -->

```input
speech01.wav    speech recognition is awesome
speech02.wav    the quick brown fox jumped all over the place
speech03.wav    the lazy dog was not amused
```

> [!IMPORTANT]
> La transcripción debería estar codificada como una marca BOM UTF-8.

Las transcripciones son texto normalizado para que el sistema pueda procesarlas. Aunque hay algunas normalizaciones importantes que debe hacer el usuario antes de cargar los datos en Speech Studio. Para conocer el idioma que debe usar al preparar las transcripciones, consulte [How to create a human-labeled transcription](how-to-custom-speech-human-labeled-transcriptions.md) (Creación de una transcripción con etiqueta humana).

Cuando haya reunido los archivos de audio y las transcripciones correspondientes, debe empaquetarlos como un solo archivo .zip antes de cargarlos en <a href="https://speech.microsoft.com/customspeech" target="_blank">Speech Studio </a>. A continuación se muestra un conjunto de datos de ejemplo con tres archivos de audio y un archivo de transcripción con etiqueta humana:

> [!div class="mx-imgBorder"]
> ![Selección del audio desde el portal de Voz](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

Consulte [Configuración de la cuenta de Azure](custom-speech-overview.md#set-up-your-azure-account) para obtener una lista de las regiones recomendadas para las suscripciones del servicio de Voz. La configuración de las suscripciones de Voz en una de estas regiones reducirá el tiempo necesario para entrenar el modelo. En estas regiones, el entrenamiento puede procesar aproximadamente 10 horas de audio al día en comparación con solo 1 hora al día en otras regiones. Si el entrenamiento del modelo no se puede completar en una semana, el modelo se marcará como erróneo.

No todos los modelos base son compatibles con el entrenamiento con datos de audio. Si el modelo base no lo admite, el servicio omitirá el audio y simplemente entrenará con el texto de las transcripciones. En este caso, el entrenamiento será el mismo que el del entrenamiento con texto relacionado. Consulte la [compatibilidad con idiomas](language-support.md#speech-to-text) para obtener una lista de los modelos base que admiten el entrenamiento con datos de audio.

## <a name="plain-text-data-for-training"></a>Datos de texto sin formato para entrenamiento

Puede usar las oraciones relacionadas con el dominio para mejorar la precisión al reconocer nombres de productos o jerga específica del sector. Proporcione oraciones en un único archivo de texto. Para mejorar la precisión, use datos de texto que estén más cerca de las expresiones orales esperadas. 

El entrenamiento con texto sin formato suele completarse en unos minutos.

Para crear un modelo personalizado con frases, tendrá que proporcionar una lista de expresiones de ejemplo. Las expresiones _no_ tienen que ser frases completas o gramaticalmente correctas, pero deben reflejar con precisión la entrada oral que se espera en producción. Si quiere que ciertos términos tengan mayor peso, puede agregar varias frases que incluyan estos términos específicos.

Como guía general, la adaptación de modelos es más eficaz cuando el texto de entrenamiento es lo más parecido posible al texto real esperado en producción. La jerga y las frases específicas del dominio que va a mejorar deben incluirse en el texto de entrenamiento. Cuando sea posible, intente que una frase o una palabra clave se controle en una línea independiente. Para las palabras clave y frases que son importantes para usted (por ejemplo, nombres de producto), puede copiarlas varias veces. Pero tenga en cuenta que no debe copiar demasiado; podría afectar a la tasa de reconocimiento general.

Utilice esta tabla para asegurarse de que el archivo de datos relacionado con las expresiones esté formateado correctamente:

| Propiedad | Value |
|----------|-------|
| Codificación de texto | BOM UTF-8 |
| Número de expresiones por línea | 1 |
| Tamaño de archivo máximo | 200 MB |

Además, querrá tener en cuenta las siguientes restricciones:

* Evite repetir caracteres, palabras o grupos de palabras más de tres veces. Por ejemplo: "a", "sí sí sí sí" o "eso es todo eso es todo eso es todo eso es todo". El servicio Voz podría quitar líneas con demasiadas repeticiones.
* No utilice caracteres especiales ni caracteres UTF-8 por encima de `U+00A1`.
* Se rechazarán los identificadores URI.
* Para algunos idiomas (por ejemplo, japonés o coreano), importar grandes cantidades de datos de texto puede conllevar mucho tiempo o agotar el tiempo de espera. Considere la posibilidad de dividir los datos cargados en archivos de texto de hasta 20 000 líneas cada uno.

## <a name="structured-text-data-for-training-public-preview"></a>Datos de texto estructurado para entrenamiento (versión preliminar pública)

A menudo, las expresiones esperadas siguen un patrón determinado. Un patrón común es que las expresiones solo difieren en palabras o frases de una lista. Algunos ejemplos de esto podrían ser "Tengo una pregunta sobre `product`", donde `product` es una lista de posibles productos. O bien, "Hacer que ese `object` sea `color`", donde `object` es una lista de formas geométricas y `color` es una lista de colores. Para simplificar la creación de datos de entrenamiento y habilitar un mejor modelado dentro del modelo de lenguaje personalizado, puede usar un texto estructurado en formato Markdown para definir listas de elementos y, después, hacer referencia a ellos dentro de las expresiones de entrenamiento. Además, el formato Markdown también admite la especificación de la pronunciación fonética de las palabras. El archivo Markdown debe tener la extensión `.md`. La sintaxis del formato Markdown es la misma que la de los modelos de Language Understanding, en particular, las entidades de lista y las expresiones de ejemplo. Para más información sobre la sintaxis completa del formato Markdown, consulte <a href="/azure/bot-service/file-format/bot-builder-lu-file-format" target="_blank">Formato de archivo .lu</a>.

Este es un ejemplo del formato Markdown:

```markdown
// This is a comment

// Here are three separate lists of items that can be referenced in an example sentence. You can have up to 10 of these
@ list food =
- pizza
- burger
- ice cream
- soda

@ list pet =
- cat
- dog

@ list sports =
- soccer
- tennis
- cricket
- basketball
- baseball
- football

// This is a list of phonetic pronunciations. 
// This adjusts the pronunciation of every instance of these word in both a list or example training sentences 
@ speech:phoneticlexicon
- cat/k ae t
- cat/f i l ai n

// Here are example training sentences. They are grouped into two sections to help organize the example training sentences.
// You can refer to one of the lists we declared above by using {@listname} and you can refer to multiple lists in the same training sentence
// A training sentence does not have to refer to a list.
# SomeTrainingSentence
- you can include sentences without a class reference
- what {@pet} do you have
- I like eating {@food} and playing {@sports}
- my {@pet} likes {@food}

# SomeMoreSentence
- you can include more sentences without a class reference
- or more sentences that have a class reference like {@pet} 
```

Al igual que el texto sin formato, el entrenamiento con texto estructurado normalmente tarda unos minutos. Además, las oraciones y listas de ejemplo deben reflejar el tipo de entrada hablada que se espera en producción.
Para las entradas de pronunciación, vea la descripción del [ Conjunto de segmentos acústicos universal](phone-sets.md).

En la tabla siguiente se especifican los límites y otras propiedades para el formato Markdown:

| Propiedad | Value |
|----------|-------|
| Codificación de texto | BOM UTF-8 |
| Tamaño de archivo máximo | 200 MB |
| Número máximo de oraciones de ejemplo | 50.000 |
| Número máximo de clases de lista | 10 |
| Número máximo de elementos de una clase de lista | 4\.000 |
| Número máximo de entradas speech:phoneticlexicon | 15000 |
| Número máximo de pronunciaciones por palabra | 2 |


## <a name="pronunciation-data-for-training"></a>Datos de pronunciación para entrenamiento

Si hay términos poco comunes sin pronunciaciones estándar que los usuarios van a encontrar o utilizar, puede proporcionar un archivo de pronunciación personalizado para mejorar el reconocimiento. Para obtener una lista de idiomas que admiten la pronunciación personalizada, vea **Pronunciación** en la columna **Personalizaciones** de la [tabla de conversión de voz en texto](language-support.md#speech-to-text).

> [!IMPORTANT]
> No se recomienda utilizar archivos de pronunciación personalizados para modificar la pronunciación de palabras comunes.

> [!NOTE]
> No se puede combinar este tipo de archivo de pronunciación con datos de entrenamiento de texto estructurado. Para los datos de texto estructurado, use la funcionalidad de pronunciación fonética que se incluye en el formato Markdown de texto estructurado.

Proporcione las pronunciaciones en un único archivo de texto. Esto incluye ejemplos de una expresión hablada y una pronunciación personalizada para cada uno:

| Formulario reconocido o mostrado | Formato hablado |
|--------------|--------------------------|
| 3CPO | CI-TRI-PI-OU |
| CNTK | CI EN TI KEI |
| IEEE | AI TRIPLE I |

La forma hablada es la secuencia fonética deletreada. Puede estar compuesta de letras, palabras, sílabas o una combinación de las tres.

Utilice la tabla siguiente para asegurarse de que el formato del archivo de datos relacionado con las pronunciaciones sea correcto. Los archivos de pronunciación son pequeños y no deberían superar unos pocos KB.

| Propiedad | Value |
|----------|-------|
| Codificación de texto | BOM UTF-8 (también se admite ANSI con el inglés) |
| Número de pronunciaciones por línea | 1 |
| Tamaño de archivo máximo | 1 MB (1 KB por cada nivel gratis) |

## <a name="audio-data-for-testing"></a>Datos de audio para pruebas

Los datos de audio son óptimos para probar la precisión del modelo de línea de base de Microsoft de voz a texto o de un modelo personalizado. Tenga en cuenta que los datos de audio se utilizan para inspeccionar la precisión de la voz con respecto al rendimiento de un modelo específico. Si desea cuantificar la precisión de un modelo, utilice las [transcripciones de audio y con etiqueta humana](#audio--human-labeled-transcript-data-for-trainingtesting).

Habla personalizada requiere archivos de audio con estas propiedades:

| Propiedad                 | Value                 |
|--------------------------|-----------------------|
| Formato de archivo              | RIFF (WAV)            |
| Velocidad de muestreo              | 8000 o 16 000 Hz |
| Canales                 | 1 (mono)              |
| Longitud máxima por audio | 2 horas               |
| Formato de ejemplo            | PCM, 16 bits           |
| Formato de archivo           | .zip                  |
| Tamaño de archivo máximo     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Al cargar los datos del entrenamiento y de las pruebas, el archivo ZIP no puede superar los 2 GB. Si requiere más datos para el entrenamiento, divídalos en varios archivos ZIP y cárguelos por separado. Más adelante, puede optar por entrenar en *varios* conjuntos de datos. Aunque solo puede realizar pruebas desde un *único* conjunto de datos.

Use <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX </a> para comprobar las propiedades de audio o convertir el audio existente en los formatos adecuados. A continuación se muestran algunos comandos SoX de ejemplo:

| Actividad | Comando de SoX |
|---------|-------------|
| Compruebe el formato de archivo de audio. | `sox --i <filename>` |
| Convierta el archivo de audio en un canal único, de 16 bits y 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="next-steps"></a>Pasos siguientes

* [Inspección de los datos](how-to-custom-speech-inspect-data.md)
* [Evaluación de los datos](how-to-custom-speech-evaluate-data.md)
* [Entrenamiento de modelos personalizados](how-to-custom-speech-train-model.md)
* [Implementación de un modelo](./how-to-custom-speech-train-model.md)
