---
title: 'Operaciones por lotes de la CLI de Voz: Servicio Voz'
titleSuffix: Azure Cognitive Services
description: Aprenda a realizar la conversión de voz en texto por lotes (reconocimiento de voz), la conversión de texto a voz (síntesis de voz) con la CLI de Voz.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: lajanuar
ms.openlocfilehash: c3481edc4b66afc4556ae6d7e957f2dfee9b2e69
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122072338"
---
# <a name="speech-cli-batch-operations"></a>Operaciones por lotes de la CLI de Voz

Las tareas habituales al usar los servicios de voz de Azure son operaciones por lotes. En este artículo, aprenderá a realizar la conversión de voz en texto por lotes (reconocimiento de voz), la conversión de texto a voz (síntesis de voz) con la CLI de Voz. En concreto, obtendrá información sobre cómo:

* Ejecutar el reconocimiento de voz por lotes en un directorio de archivos de audio
* Ejecutar la síntesis de voz por lotes iterando por un archivo `.tsv`

## <a name="batch-speech-to-text-speech-recognition"></a>Conversión de voz en texto por lotes (reconocimiento de voz)

El servicio Voz se usa a menudo para reconocer la voz de los archivos de audio. En este ejemplo, aprenderá a iterar un directorio mediante el uso de la CLI de Voz para capturar la salida de reconocimiento de cada archivo `.wav`. La marca `--files` se usa para apuntar al directorio donde se almacenan los archivos de audio y el carácter comodín `*.wav` se usa para indicar a la CLI de Voz que ejecute el reconocimiento en cada archivo con la extensión `.wav`. La salida de cada archivo de reconocimiento se escribe como un valor separado por tabulaciones en `speech_output.tsv`.

> [!NOTE]
> El argumento `--threads` también se puede usar en la siguiente sección para los comandos `spx synthesize`, y los subprocesos que estén disponibles dependerán de la CPU y de su porcentaje de carga actual.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Este es un ejemplo de la estructura del archivo de salida.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>Conversión de texto a voz por lotes (síntesis de voz)

La forma más sencilla de ejecutar la conversión de texto a voz por lotes es crear un archivo `.tsv` (con valores separados por tabulaciones) y usar el comando `--foreach` de la CLI de Voz. Puede crear un archivo `.tsv` con el editor de texto que prefiera. En este ejemplo, se llamará `text_synthesis.tsv`:

>[!IMPORTANT]
> Al copiar el contenido de este archivo de texto, asegúrese de que el archivo tenga una **tabulación** y no espacios entre la ubicación del archivo y el texto. A veces, al copiar el contenido de este ejemplo, las tabulaciones se convierten en espacios, lo que provoca un error en el comando `spx` cuando se ejecuta.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

A continuación, ejecutará un comando para que apunte a `text_synthesis.tsv`, realizará la síntesis en cada campo `text` y escribirá el resultado en la ruta de acceso de `audio.output` correspondiente como un archivo `.wav`.

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Este comando equivale a ejecutar `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav` **para cada** registro en el archivo `.tsv`.

Tenga en cuenta lo siguiente:

* Los encabezados de columna, `audio.output` y `text`, se corresponden con los argumentos de la línea de comandos `--audio output` y `--text`, respectivamente. Los argumentos de la línea de comandos de varias partes, como `--audio output`, deben tener el formato del archivo sin espacios, guiones iniciales y puntos que separen las cadenas, por ejemplo `audio.output`. Cualquier otro argumento de la línea de comandos existente se puede agregar al archivo como columnas adicionales mediante este patrón.
* Cuando el archivo se formatea de esta manera, no es necesario pasar argumentos adicionales a `--foreach`.
* Asegúrese de separar cada valor del archivo `.tsv` con una **tabulación**.

Sin embargo, si tiene un archivo `.tsv` como en el ejemplo siguiente, con encabezados de columna que **no coinciden** con los argumentos de la línea de comandos:

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Estos nombres de campo se pueden invalidar con los argumentos correctos mediante la siguiente sintaxis en la llamada `--foreach`. Esta es la misma llamada que la anterior.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a la CLI de Voz](./spx-overview.md)
* [Inicio rápido de la CLI de Voz](./spx-basics.md)
