---
title: 'Inspección de la calidad de los datos de Habla personalizada: Servicio de voz'
titleSuffix: Azure Cognitive Services
description: Habla personalizada proporciona herramientas que le permiten inspeccionar visualmente la calidad del reconocimiento de un modelo mediante la comparación de los datos de audio con el resultado de reconocimiento correspondiente. Puede reproducir el audio cargado y determinar si el resultado de reconocimiento proporcionada es correcto.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: pafarley
ms.openlocfilehash: 5f127a588b4b104c8c09771b6ede52f8049d2110
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543626"
---
# <a name="inspect-custom-speech-data"></a>Inspección de los datos de Habla personalizada

> [!NOTE]
> En esta página se supone que ha leído [Preparación de los datos de Habla personalizada](./how-to-custom-speech-test-and-train.md) y que ha cargado un conjunto de datos para su inspección.

Habla personalizada proporciona herramientas que le permiten inspeccionar visualmente la calidad del reconocimiento de un modelo mediante la comparación de los datos de audio con el resultado de reconocimiento correspondiente. En [Speech Studio](https://speech.microsoft.com/customspeech), puede reproducir el audio cargado y determinar si el resultado de reconocimiento proporcionado es correcto. Esta herramienta le ayuda a inspeccionar la calidad del modelo de conversión de voz a texto de línea de base de Microsoft, a inspeccionar un modelo personalizado entrenado o a comparar la transcripción con dos modelos.

En este documento, aprenderá a inspeccionar visualmente la calidad del modelo de conversión de voz en texto de línea de base de Microsoft o de los modelos personalizados que haya entrenado. También aprenderá a usar el editor de transcripciones en línea para crear y perfeccionar conjuntos de datos de audio con etiqueta.

## <a name="create-a-test"></a>Creación de una prueba

Para crear una prueba, siga estas instrucciones:

1. Inicie sesión en [Speech Studio](https://speech.microsoft.com/customspeech).
2. Vaya a **Speech-to-text > Custom Speech > [nombre del proyecto] > Pruebas**.
3. Haga clic en **Add test** (Agregar prueba).
4. Seleccione **Inspect quality (Audio-only data)** (Inspeccionar la calidad [solo datos de audio]). Asigne un nombre y una descripción a la prueba y seleccione el conjunto de datos de audio.
5. Puede seleccionar hasta dos modelos para probar.
6. Haga clic en **Crear**.

Una vez que se ha creado correctamente una prueba, puede ver la forma en que un modelo transcribe el conjunto de datos de audio especificado o comparar los resultados de dos modelos en paralelo.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Comparaciones de los modelos en paralelo

Cuando el estado de la prueba sea _Succeeded_ (Correcto), haga clic en el nombre del elemento de prueba para ver los detalles de la prueba. En esta página de detalles se enumeran todas los expresiones del conjunto de datos y se muestran los resultados de reconocimiento de los dos modelos que se comparan.

Con el fin de inspeccionar la comparación en paralelo, puede alternar los distintos tipos de error, como inserción, eliminación y sustitución. Al escuchar el audio y comparar los resultados del reconocimiento de cada columna (que muestra la transcripción con la etiqueta humana y los resultados de los dos modelos de conversión de voz a texto), puede decidir qué modelo satisface sus necesidades y dónde hacen falta mejoras.

Las pruebas de modelos en paralelo son útiles para validar qué modelo de reconocimiento de voz es el mejor para una aplicación. Si necesita una medida objetiva de precisión, que necesite la transcripción del audio, siga las instrucciones que encontrará en [Evaluación de la precisión](how-to-custom-speech-evaluate-data.md).

## <a name="online-transcription-editor"></a>Editor de transcripciones en línea

El editor de transcripciones en línea le permite trabajar fácilmente con transcripciones de audio de Habla personalizada. Los casos de uso principales del editor son los siguientes: 

* Solo tiene datos de audio, pero quiere crear conjuntos de datos precisos de audio y transcripción etiquetada por usuarios desde cero para usarlos en el entrenamiento del modelo.
* Ya tiene conjuntos de datos de audio y transcripción etiquetada por usuarios, pero hay errores o defectos en la transcripción. El editor permite modificar rápidamente las transcripciones para obtener la máxima precisión del entrenamiento.

El único requisito para usar el editor de transcripción es tener datos de audio cargados (ya sea de solo audio o de audio y transcripción).

### <a name="import-datasets-to-editor"></a>Importación de conjuntos de datos al editor

Para importar datos al editor, navegue primero a **Habla personalizada > [su proyecto] > Editor**.

![Pestaña Editor](media/custom-speech/custom-speech-editor-detail.png)

Luego siga estos pasos para importar datos.

1. Hacer clic en **Importar datos**
1. Crear conjuntos de datos y agregarles una descripción
1. Seleccionar conjuntos de datos. Se admiten varias selecciones, y solo se pueden seleccionar datos de audio, así como datos de audio y transcripción etiquetada por usuarios.
1. En el caso de los datos de solo audio, puede usar opcionalmente los modelos predeterminados para generar automáticamente la transcripción de máquina después de importar al editor.
1. Haga clic en **Importar**

Una vez que los datos se han importado correctamente, puede hacer clic en los conjuntos de datos e iniciar la edición.

> [!TIP]
> También puede importar conjuntos de datos directamente al editor seleccionando conjuntos de datos y haciendo clic en **Exportar a editor**

### <a name="edit-transcription-by-listening-to-audio"></a>Edición de la transcripción escuchando audio

Una vez que la carga de datos se haya realizado correctamente, haga clic en cada nombre de elemento para ver los detalles de los datos. También puede usar **Anterior** y **Siguiente** para desplazarse entre cada archivo.

La página de detalles muestra todos los segmentos de cada archivo de audio. Puede hacer clic en la expresión deseada. Para cada expresión, puede reproducir el audio y examinar las transcripciones, además de editar las transcripciones si encuentra errores de inserción, eliminación o sustitución. Para más información sobre los tipos de error, consulte los [procedimientos de evaluación de datos](how-to-custom-speech-evaluate-data.md).

![Página del editor](media/custom-speech/custom-speech-editor.png)

Una vez realizadas las modificaciones, haga clic en el botón **Guardar**.

### <a name="export-datasets-from-the-editor"></a>Exportación de conjuntos de datos desde el editor

Para volver a exportar los conjuntos de datos a la pestaña **Datos**, navegue a la página de detalles de datos y haga clic en el botón **Exportar** para exportar todos los archivos como un nuevo conjunto de datos. También puede filtrar los archivos por hora de última modificación, duraciones de audio, etc., para seleccionar parcialmente los archivos que quiera. 

![Exportar datos](media/custom-speech/custom-speech-editor-export.png)

Los archivos exportados a Datos se utilizarán como un conjunto de datos completamente nuevo y no afectarán a ninguna de las entidades de datos, entrenamiento y prueba existentes.

## <a name="next-steps"></a>Pasos siguientes

- [Evaluación de los datos](how-to-custom-speech-evaluate-data.md)
- [Entrenamiento del modelo](how-to-custom-speech-train-model.md)
- [Mejora del modelo](./how-to-custom-speech-evaluate-data.md)
- [Implementación del modelo](./how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Recursos adicionales

- [Preparación de los datos de prueba para Habla personalizada](./how-to-custom-speech-test-and-train.md)