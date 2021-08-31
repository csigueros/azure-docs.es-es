---
title: 'Creación de un proyecto de Voz personalizada: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Cuando esté listo para cargar los datos, vaya al portal de voz personalizada. Cree o seleccione un proyecto de voz personalizada. El proyecto debe compartir el idioma o configuración regional y las propiedades de género correctos con los datos que pretende usar para el entrenamiento de voz.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: lajanuar
ms.openlocfilehash: a380c66159304e8e5fabbc73c8a2c663f7d23c27
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122066469"
---
# <a name="create-and-use-your-voice-model"></a>Creación y uso de un modelo de voz

En [Preparación de los datos de entrenamiento](how-to-custom-voice-prepare-data.md), ha aprendido sobre los diferentes tipos de datos que puede usar para entrenar una voz neuronal personalizada y los distintos requisitos de formato. Una vez preparados los datos y la instrucción verbal del talento de voz, puede empezar a cargarlos en [Speech Studio](https://aka.ms/custom-voice-portal). En este artículo aprenderá a entrenar una instancia de Voz neuronal personalizada mediante el portal de Speech Studio. Consulte los [idiomas admitidos](language-support.md#customization) para la voz neuronal personalizada.

## <a name="prerequisites"></a>Prerrequisitos

* Haber completado [Introducción a Voz neuronal personalizada](how-to-custom-voice.md)
* Haber completado [Preparación de los datos de entrenamiento](how-to-custom-voice-prepare-data.md)

## <a name="set-up-voice-talent"></a>Configuración del talento de voz

Un talento de voz es un hablante individual o de destino cuyas voces se graban y se usan para crear modelos de voz neuronal. Antes de crear una voz, defina el rol de voz y seleccione un talento de voz adecuado. Para más información sobre cómo grabar ejemplos de voz, consulte [el tutorial](record-custom-voice-samples.md).

Para entrenar una voz neuronal, debe crear un perfil de talento de voz con un archivo de audio grabado por él en el que consienta el uso de sus datos de voz para entrenar un modelo de voz personalizado. Cuando prepare el script de grabación, asegúrese de incluir la frase siguiente:

**"I [state your first and last name] am aware that recordings of my voice will be used by [state the name of the company] to create and use a synthetic version of my voice." ("Yo [indique su nombre y apellido] acepto que [indique el nombre de empresa] use grabaciones de mi voz para crear y usar una versión sintética de la voz").**
Esta frase se usa para comprobar si los datos de entrenamiento coinciden con el audio de la instrucción de consentimiento. > Consulte aquí más información sobre la [comprobación del talento de voz](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

> [!NOTE]
> Voz neuronal personalizada está disponible con acceso limitado. Asegúrese de comprender los [requisitos de IA responsable](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) y, después, [solicite el acceso](https://aka.ms/customneural). 

En los pasos siguientes se supone que ha preparado los archivos de consentimiento verbal del talento de voz.  Vaya a [Speech Studio](https://aka.ms/custom-voice-portal) para seleccionar un proyecto de voz neuronal personalizado y siga los pasos a continuación para crear un perfil de talento de voz.

1. Vaya a **Texto a voz** > **Voz personalizada** > **seleccione un proyecto** > **Set up voice talent** (Configurar talento de voz).

2. Seleccione **Add voice talent** (Agregar talento de voz).

3. A continuación, para definir las características de voz, seleccione el **escenario de destino** que se va a usar. Describa los valores de **Características de voz** correspondientes.

> [!NOTE]
> Los escenarios que proporcione deben ser coherentes con lo que haya solicitado en el formulario.

4. A continuación, vaya a **Cargar la instrucción del talento de voz**, siga las instrucciones para cargar la declaración del talento de voz que ha preparado de antemano.

> [!NOTE]
> Asegúrese de que la instrucción verbal se registra en la misma configuración que los datos de entrenamiento, incluido el entorno de grabación y el estilo de habla.

5. Por último, vaya a **Review and create** (Revisar y enviar) para revisar la configuración y seleccione **Submit** (Enviar).

## <a name="upload-your-data"></a>Creación del código

Cuando esté listo para cargar los datos, vaya a la pestaña **Prepare training data** (Preparar datos de entrenamiento) para agregar su primer conjunto de entrenamiento y cargar datos.  Un conjunto de entrenamiento es un conjunto de expresiones de audio y los scripts de asignación que se usan para entrenar un modelo de voz. Puede usar un conjunto de entrenamiento para organizar los datos de entrenamiento. Se comprueba si los datos están preparados en cada conjunto de entrenamiento. Puede importar varios datos a un conjunto de entrenamiento.

Puede hacer lo siguiente para crear y revisar los datos de entrenamiento.

1. En la pestaña **Prepare training data** (Preparar datos de entrenamiento), seleccione **Add training set** (Agregar conjunto de entrenamiento) para escribir **Nombre** y **Descripción** > **Crear** para agregar un nuevo conjunto de entrenamiento.

   Una vez creado el conjunto de entrenamiento correctamente, podrá empezar a cargar los datos. 

2. Para cargar datos, seleccione **Upload data** > **Choose data type** > **Upload data** (Cargar datos > Elegir tipo de datos > Cargar datos) y **Specify the target training set** (Especificar el conjunto de entrenamiento objetivo) > Escriba los valores de **Nombre** y **Descripción** de los datos > revise la configuración y seleccione **Submit** (Enviar).

> [!NOTE]
>- Los nombres de audio duplicados se quitarán del entrenamiento. Asegúrese de que los datos que seleccione no contengan los mismos nombres de audio en el archivo ZIP o en varios archivos ZIP. Si los ID de expresión (ya sea en archivos de audio o de script) están duplicados, se rechazarán.
>- Si ha creado archivos datos en la versión anterior de Speech Studio, debe especificar un conjunto de entrenamiento de antemano para usarlos. De lo contrario, se anexará un signo de exclamación al nombre de los datos y no se podrá usar.

Cada conjunto que cargue debe cumplir los requisitos del tipo de datos elegido. Es importante dar el formato correcto a los datos antes de cargarlos, lo que garantiza que el servicio Voz neuronal personalizada los procesará con precisión. Vaya a [Prepare training data](how-to-custom-voice-prepare-data.md) (Preparar datos de entrenamiento) y asegúrese de que los datos tienen el formato correcto.

> [!NOTE]
> - En cambio, los usuarios con una suscripción estándar (S0) pueden cargar cinco archivos de datos a la vez. Si alcanza el límite, espere hasta que al menos uno de los archivos de datos finalice la importación. A continuación, inténtelo de nuevo.
> - El número máximo de archivos de datos que se pueden importar por suscripción es de 10 archivos ZIP para usuarios de una suscripción gratuita (F0) y 500 para usuarios de la suscripción estándar (S0).

Los archivos de datos se validan automáticamente una vez presionado el botón **Submit** (Enviar). La validación de datos incluye una serie de comprobaciones en los archivos de audio para comprobar su formato de archivo, el tamaño y la frecuencia de muestreo. Corrija los errores si los hay y vuelva a realizarla. 

Una vez cargados los datos, puede comprobar los detalles en la vista de detalles del conjunto de entrenamiento. En pestaña **Overview** (Información general) puede comprobar la puntuación de las pronunciaciones y el nivel de ruido de los datos. La puntuación de las pronunciaciones abarca un rango del 0 al 100. Una puntuación por debajo de 70 normalmente indica un error en el discurso o que el guion no coincide. Un acento marcado puede reducir la puntuación de las pronunciaciones, y afectar a la voz digital que se ha creado.

Una relación de la señal y el ruido (SNR) más alta indica un ruido más bajo en el audio. Por lo general, el audio puede alcanzar una SNR de más de 50 puntos si se graba en estudios profesionales. Un audio que tenga un valor de SNR por debajo de 20 puntos puede provocar un ruido obvio en la voz generada.

Puede volver a grabar cualquier expresión que tenga una puntuación baja debido a la pronunciación o a la pobre relación entre el ruido y la señal. Si no es posible volver a realizar la grabación, considere la exclusión de esas expresiones de los datos.

En **Detalles de los datos** puede comprobar los detalles de los datos del conjunto de entrenamiento. Si hay presentes algunos problemas típicos en los datos puede seguir las instrucciones del mensaje que se muestra para corregirlos antes del entrenamiento.

Los problemas se dividen en tres tipos. Consulte las tres tablas siguientes para comprobar los tipos de errores respectivos.

El primer tipo de error que se enumera en la tabla siguiente debe corregirse manualmente; de lo contrario, los datos con estos errores se excluirán durante el entrenamiento.

| Category | Nombre | Descripción |
| --------- | ----------- | --------------------------- |
| Script | Invalid separator (Separador no válido)| Debe separar el id. de expresión y el contenido del script con un carácter de tabulación.|
| Script | Invalid script ID (Id. de script no válido)| El id. de la línea del script debe ser numérico.|
| Script | Script duplicado|Cada línea del contenido del script debe ser única. La línea está duplicada con 1{}.|
| Script | El script es demasiado largo| El script debe tener menos de 1000 caracteres.|
| Script | Sin audio correspondiente| El id. de cada expresión (cada línea del archivo del script) debe coincidir con el id. de audio.|
| Script | No valid script (Sin script válido)| No se encontró ningún script válido en este conjunto de datos. Corrija las líneas del script que aparecen en la lista detallada de problemas.|
| Audio | Sin script correspondiente| Ningún archivo de audio coincide con el id. del script. El nombre de los archivos wav debe coincidir con los de los archivos del script.|
| Audio | Invalid audio format (Formato de audio no válido)| El formato de audio de los archivos .wav no es válido. Compruebe el formato de archivo wav mediante una herramienta de audio como [SoX](http://sox.sourceforge.net/).|
| Audio | Low sampling rate (Velocidad de muestreo baja)| La frecuencia de muestreo de los archivos .wav no puede ser inferior a 16 KHz.|
| Audio | El audio es demasiado largo| La duración del audio es mayor que 30 segundos. Divida el audio largo en varios archivos. Se recomienda que las expresiones duren menos de 15 segundos.|
| Audio | No valid audio (No hay audio válido)| No se encontró ningún audio válido en este conjunto de datos. Compruebe los datos de audio y cárquelos de nuevo.|

El segundo tipo de error que se enumera en la tabla siguiente se corregirá automáticamente, pero se recomienda comprobar los datos reparados.

| Category | Nombre | Descripción |
| --------- | ----------- | --------------------------- |
| Audio | Audio estéreo corregido automáticamente | Use mono en las grabaciones de ejemplo de audio. Los canales de audio estéreo se combinan automáticamente en un canal mono, lo que puede provocar la pérdida de contenido.  Descargue el conjunto de datos normalizado y revíselo.|
| Volumen | Corrección automática del pico de volumen |El pico de volumen no está dentro del intervalo de -3 dB (70 % del volumen máximo) a -6 dB (50 %). Controle el pico de volumen durante la grabación de ejemplo o la preparación de datos. Este audio se escala linealmente para ajustarse al intervalo máximo automáticamente (-4 dB o 65 %). Descargue el conjunto de datos normalizado y revíselo.|
|Error de coincidencia | Silencio corregido automáticamente| Se detecta que el silencio al principio es superior a 200 ms y se ha recortado a 200 ms automáticamente. Descargue el conjunto de datos normalizado y revíselo. |
| Error de coincidencia |Silencio corregido automáticamente | Se detecta que el silencio al final es superior a 200 ms y se ha recortado a 200 ms automáticamente. Descargue el conjunto de datos normalizado y revíselo. |
| Error de coincidencia |Silencio corregido automáticamente |Se detecta que el silencio al principio es inferior a 100 ms y se ha ampliado a 100 ms automáticamente. Descargue el conjunto de datos normalizado y revíselo. |
| Error de coincidencia |Silencio corregido automáticamente | Se detecta que el silencio al final es inferior a 100 ms y se ha ampliado a 100 ms automáticamente. Descargue el conjunto de datos normalizado y revíselo.|

Si el tercer tipo de error que se enumera en la tabla siguiente no se corrige, aunque los datos con estos errores no se excluirán durante el entrenamiento, afectará a la calidad del entrenamiento. Para un entrenamiento de mayor calidad, se recomienda corregir manualmente estos errores. 

| Category | Nombre | Descripción |
| --------- | ----------- | --------------------------- |
| Script | Texto no normalizado|Este script contiene dígitos entre el 0 y el 9. Escríbalos como palabras normalizadas y que coincidan con el audio. Por ejemplo: para "123", use "ciento veintitrés".|
| Script | Texto no normalizado|Este script contiene símbolos {}. Normalice los símbolos para que coincidan con el audio. Por ejemplo: para "50 %", use "cincuenta por ciento".|
| Script | No hay suficientes expresiones de pregunta| Al menos el 10 % de las expresiones totales deben ser frases de pregunta. Esto ayuda al modelo de voz a expresar correctamente un tono de interrogación.|
| Script |No hay suficientes expresiones de exclamación| Al menos el 10 % de las expresiones totales deben ser frases de exclamación. Esto ayuda al modelo de voz a expresar correctamente un tono de exclamación.|
| Audio| Low sampling rate for neural voice (Velocidad de muestreo baja para la voz neuronal) | Se recomienda que la frecuencia de muestreo de los archivos .wav sea de 24 KHz o superior para crear voces neuronales. Se subirá automáticamente a 24 KHz si es inferior.|
| Volumen |Overall volume too low (Volumen general demasiado bajo)|El volumen no debe ser inferior a -18 dB (10 % del volumen máximo). Controle el volumen medio dentro del intervalo adecuado durante la grabación de ejemplo o la preparación de los datos.|
| Volumen | Desbordamiento de volumen| El volumen desbordante se detecta en {}s. Ajuste el equipo de grabación para evitar el desbordamiento de volumen en su valor máximo.|
| Volumen | Problema de silencio al principio | Los primeros 100 ms de silencio tienen ruido. Reduzca el nivel de ruido de la grabación y deje los primeros 100 ms del principio en modo silencioso.|
| Volumen| Problema de silencio al final| Los últimos 100 ms de silencio tienen ruido.  Reduzca el nivel de ruido de la grabación y deje los últimos 100 ms del final en modo silencioso.|
| Error de coincidencia | Palabras con puntuación baja|Revise el contenido del script y del audio para asegurarse de que coinciden y controle el ruido. Reduzca la longitud del silencio largo o divida el audio en varias expresiones si es demasiado largo.|
| Error de coincidencia | Problema de silencio al principio |Antes de la primera palabra se oía audio adicional. Revise el script y el contenido de audio para asegurarse de que coinciden, controlan el nivel de ruido del suelo y hacen que los primeros 100 ms no se usen.|
| Error de coincidencia | Problema de silencio al final| Después de la última palabra se oía audio adicional. Revise el script y el contenido de audio para asegurarse de que coinciden, controlan el nivel de ruido del suelo y hacen que los últimos 100 ms no se usen.|
| Error de coincidencia | Low signal-noise ratio (Relación entre señal y ruido de baja calidad) | La relación entre señal y ruido del audio es menor de 20 dB. Se recomiendan al menos 35 dB.|
| Error de coincidencia | No hay puntuación disponible |No se pudo reconocer el contenido de voz en este audio. Compruebe el contenido del audio y del script para asegurarse de que el audio es válido y coincide con el script.|

## <a name="train-your-custom-neural-voice-model"></a>Entrenamiento del modelo de voz neuronal personalizada

Una vez validados los archivos de datos, podrá usarlos para crear su modelo de voz neuronal personalizada.

1. En la pestaña **Train model** (Entrenar modelo), haga clic en **Train model** (Entrenar modelo) para crear un modelo de voz con los datos que ha cargado.

2. Seleccione el método de entrenamiento neuronal para el modelo y el idioma de destino.

De forma predeterminada, el modelo de voz se entrena en el mismo idioma de los datos de entrenamiento. También puede seleccionar crear un idioma secundario (versión preliminar) para el modelo de voz.  Compruebe los idiomas admitidos para la voz neuronal personalizada y la característica multilingüe: [idioma para la personalización](language-support.md#customization).

3. A continuación, elija los datos que desea usar para el entrenamiento y especifique un archivo del hablante.

>[!NOTE]
>- Debe seleccionar al menos 300 expresiones para crear una voz neuronal personalizada.
>- Para entrenar una voz neuronal, debe especificar un perfil de talento de voz con el archivo de consentimiento de audio del talento de voz al aceptar que se usen sus datos de voz para entrenar un modelo de voz personalizada. Voz neuronal personalizada está disponible con acceso limitado. Asegúrese de comprender los [requisitos de IA responsable](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) y [aplicar el acceso aquí](https://aka.ms/customneural).
>- En esta página también puede cargar su script para realizar pruebas. El script de prueba debe ser un archivo txt de menos de 1 MB. Entre los formatos de codificación compatibles se incluyen ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE o UTF-16-BE. Cada párrafo de la expresión dará como resultado un audio independiente. Si desea combinar todas las frases en un solo audio, reúnalas todas en un párrafo.

4. A continuación, escriba los valores de **Nombre** y **Descripción** que le ayuden a identificar este modelo.

Elija un nombre con cuidado. El nombre que escriba aquí será el nombre que use para especificar la voz en su solicitud de síntesis de voz como parte de la entrada de SSML. Solo se permiten letras, números y algunos signos de puntuación, como -, _ y (","). Use nombres diferentes para los modelos de voz neuronal diferentes.

Un uso habitual del campo **Descripción** es registrar los nombres de los datos que se usaron para crear el modelo.

5. Revise la configuración y seleccione **Submit** (Enviar) para empezar a entrenar el modelo.

> [!NOTE]
> Los nombres de audio duplicados se quitarán del entrenamiento. Asegúrese de que los datos que seleccione no contengan los mismos nombres de audio en varios archivos ZIP.

En la tabla **Entrenar modelo** se muestra una nueva entrada que corresponde a este modelo recién creado. En la tabla también se muestra el estado: Procesando, Correcto, Error.

El estado que se muestra refleja el proceso de convertir los datos en un modelo de voz, como se muestra aquí.

| State | Significado |
| ----- | ------- |
| Processing | Se está creando el modelo de voz. |
| Correcto | El modelo de voz se ha creado y se puede implementar. |
| Con error | El modelo de voz ha dado error en el entrenamiento por muchas razones, por ejemplo, problemas desapercibidos con los datos o problemas de red. |

La duración del entrenamiento varía en función de la cantidad de datos que se están entrenando. En entrenar una voz neuronal personalizada se tarda una media de 40 horas de proceso. 

> [!NOTE]
> El entrenamiento de voces neuronales personalizadas no es gratuito. Consulte aquí los [precios](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). En cambio, los usuarios que tengan una suscripción estándar (S0) pueden entrenar tres voces simultáneamente. Si alcanza el límite, espere hasta que al menos una de las fuentes de voz finalice el aprendizaje e inténtelo de nuevo. 

6. Cuando termine de entrenar el modelo correctamente, puede revisar los detalles de este.

Cada entrenamiento generará 100 audios de muestra automáticamente para ayudarle a probar el modelo. Una vez que el modelo de voz se haya creado correctamente, podrá probarlo antes de implementarlo para su uso.

La calidad de la voz depende de muchos factores, como el tamaño de los datos de entrenamiento, la calidad de la grabación, la precisión del archivo de transcripción, el grado en que la voz grabada en los datos de entrenamiento coincide con la personalidad de la voz diseñada para el caso de uso previsto, etc. [Consulte aquí más información sobre las funcionalidades y los límites de nuestra tecnología y el procedimiento recomendado para mejorar la calidad del modelo](/legal/cognitive-services/speech-service/custom-neural-voice/characteristics-and-limitations-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

## <a name="create-and-use-a-custom-neural-voice-endpoint"></a>Creación y uso de un punto de conexión de voz neuronal personalizada

Una vez que haya creado y probado con éxito su modelo de voz, impleméntelo en un punto de conexión personalizado de Text to Speech. A continuación, use ese punto de conexión en lugar del punto de conexión habitual al realizar solicitudes de Text to Speech a través de la API de REST. Recuerde que solo se puede llamar al punto de conexión personalizado mediante la suscripción que utilizó para implementar la fuente.

Para crear un punto de conexión de voz neuronal personalizado, haga lo siguiente.

1. En la pestaña **Deploy model** (Implementar modelo), seleccione **Deploy model** (Implementar modelo). 
2. A continuación, escriba un valor para **Nombre** y **Descripción** para el punto de conexión personalizado.
3. A continuación, seleccione el modelo de voz que le gustaría asociar a este punto de conexión. 
4. Por último, haga clic en **Deploy** (Implementar) para crear el punto de conexión.

Después de haber hecho clic en el botón **Implementar**, en la tabla de puntos de conexión verá una entrada para el nuevo punto de conexión. El proceso para crear instancias del nuevo punto de conexión puede llevar unos minutos. Cuando el estado de la implementación muestra el valor **Completado**, quiere decir que el punto de conexión está listo para su uso.

Puede **suspender** y **reanudar** el punto de conexión si no lo usa continuamente. Cuando se reactiva un punto de conexión tras la suspensión, la dirección URL del punto de conexión se mantiene igual, por lo que no es necesario cambiar el código de las aplicaciones. 

También puede actualizar el punto de conexión a un nuevo modelo. Para cambiar el modelo, asegúrese de que el nuevo tiene el mismo nombre que el que desea actualizar. 

> [!NOTE]
>- En cambio, los usuarios que tengan una suscripción estándar (S0) pueden crear hasta 50 puntos de conexión, cada uno de ellos con su propia voz neuronal personalizada.
>- Para usar su voz neuronal personalizada, debe especificar el nombre del modelo de voz, utilizar el URI personalizado directamente en una solicitud HTTP y emplear la misma suscripción para pasar por la autenticación del servicio TTS.

Una vez implementado el punto de conexión, su nombre aparece como un vínculo. Haga clic en el vínculo para mostrar información específica del punto de conexión, como la clave o la dirección URL, y código de ejemplo.

El punto de conexión personalizado es técnicamente idéntico al punto de conexión estándar que se usa en las solicitudes de texto a voz.  Para más información, consulte el [SDK de Voz](./get-started-text-to-speech.md) o la [API REST](rest-text-to-speech.md).

También proporcionamos una herramienta en línea, [Creación de contenido de audio](https://speech.microsoft.com/audiocontentcreation), que permite ajustar la salida de audio mediante una interfaz de usuario sencilla.

## <a name="next-steps"></a>Pasos siguientes

- [Grabación de ejemplos de voz](record-custom-voice-samples.md)
- [Referencia de Text-to-Speech API](rest-text-to-speech.md)
- [Long Audio API](long-audio-api.md)
