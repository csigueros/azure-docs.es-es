---
title: Canalización de Azure Video Analyzer
description: Una canalización de Azure Video Analyzer que permite ingerir, procesar y publicar vídeos en la nube y el entorno perimetral de Azure Video Analyzer. Una canalización consta de nodos que están conectados para lograr el flujo de datos deseado.
ms.topic: conceptual
ms.date: 10/31/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 53ac9631155d35972546f937d872d1f48b476b52
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073910"
---
# <a name="pipeline"></a>Canalización

Las canalizaciones permiten ingerir, procesar y publicar vídeos en la nube y el entorno perimetral de Azure Video Analyzer. Con las topologías de canalización, se puede definir cómo se debe ingerir, procesar y publicar el vídeo mediante un conjunto de nodos configurables. Una vez definidas, se pueden crear instancias de topologías como canalizaciones individuales destinadas a cámaras o contenido de origen específicos, que se procesan de forma independiente. Las canalizaciones se pueden definir y sus instancias se pueden crear en el entorno perimetral para el procesamiento de vídeo local o en la nube. En los diagramas a continuación se proporcionan representaciones gráficas de estas canalizaciones.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline/pipeline-representation.svg" alt-text="Representación de una canalización":::

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline/cloud-pipeline.svg" alt-text="Representación de una canalización en la nube":::

## <a name="suggested-pre-reading"></a>Sugerencias antes de la lectura

* [Información general](overview.md)
* [Terminología](terminology.md)

## <a name="pipeline-topologies"></a>Topologías de canalización

Una topología de canalización le permite describir cómo se debe procesar y analizar el vídeo en directo o los [vídeos grabados](terminology.md#recording) según sus requisitos personalizados en un conjunto de nodos interconectados. Sirve como plantilla o plano técnico para el flujo de trabajo de vídeo en directo. **Video Analyzer admite dos tipos de topologías: en directo y por lotes. Las topologías en directo, como su nombre sugiere, se usan con vídeo en directo de las cámaras. Las topologías por lotes se usan para procesar vídeos grabados.**

Una **canalización** admite distintos tipos de nodos:

* Los **nodos de origen** permiten capturar los datos en la canalización. Los datos se refieren a audio, vídeo o metadatos.
* Los **nodos de procesador** permiten el procesamiento de elementos multimedia dentro de la canalización.
* Los **nodos receptores** permiten entregar los resultados a servicios y aplicaciones fuera de la canalización.

Puede crear topologías diferentes para distintos escenarios al seleccionar qué nodos forman parte de la topología y cómo están conectados, a la vez que puede usar parámetros como marcadores de posición para los valores. Una canalización es una instancia independiente de una topología de canalización específica. Los elementos multimedia en realidad se procesan en una canalización. Las canalizaciones se pueden asociar con cámaras individuales o vídeos grabados mediante parámetros definidos por el usuario que se declaran en la topología de canalización. **Las instancias de las topologías en directo se denominan canalizaciones en directo, mientras que las instancias de una topología por lotes se conocen como trabajos de canalización.**

Por ejemplo, si quiere grabar vídeos de varias cámaras IP, puede definir una topología de canalización que conste de un nodo de origen RTSP y un nodo receptor de vídeo. El nodo de origen RTSP puede tener como parámetros una dirección URL de RTSP, un nombre de usuario y una contraseña. El nodo receptor de vídeo puede tener el nombre del vídeo como parámetro. Los valores para estos parámetros se pueden especificar al crear varias canalizaciones a partir de la misma topología: una canalización por cámara.

Las topologías por lotes solo se admiten en el servicio Video Analyzer (y no en el módulo perimetral de Video Analyzer). Las canalizaciones en vivo se admiten en ambos.

## <a name="pipeline-states"></a>Estados de canalización

Empiece con la creación de la topología de canalización. Una vez definida la topología, puede especificar valores para los parámetros a fin de crear canalizaciones.

### <a name="live-pipeline"></a>Canalización en directo

En el diagrama a continuación se representa el ciclo de vida de una canalización.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline/pipeline-activation.svg" alt-text="Ciclo de vida de una canalización en directo":::

Una vez que haya creado correctamente una canalización, esta se encontrará en estado "Inactivo". Tras la activación, una canalización entra brevemente en el estado "Activando" antes de pasar al estado "Activo".
Los datos (vídeo en directo) comienzan a fluir a través de la canalización cuando esta se encuentra en el estado "Activo". Tras desactivarse, una canalización activa entra en el estado "Desactivando" y, a continuación, en "Inactivo". Solo se pueden eliminar las canalizaciones inactivas.

Las canalizaciones en directo están diseñadas para permanecer activas una vez activadas y seguir procesando el vídeo en directo desde el origen (cámara). Se requiere un comando de desactivación explícito para detener el procesamiento.
Una canalización puede estar activa incluso si no fluyen datos a través de ella (por ejemplo, el origen del vídeo de entrada se queda sin conexión). Se facturará a la suscripción a Azure cuando la canalización esté en estado activo.

### <a name="batch-pipeline"></a>Canalización por lotes

En el diagrama a continuación se representa el ciclo de vida de un trabajo de canalización.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline/batch-pipeline-lifecycle.svg" alt-text="Diagrama de un ciclo de vida de canalización por lotes.":::

Tras la creación correcta de un trabajo de canalización, pasa al estado "Procesando". Si el trabajo se completa correctamente, pasa al estado "Completado". De lo contrario, si se produce un error, cambia al estado "Error". Como alternativa, mientras el trabajo de canalización está en el estado "Procesando", se puede emitir una solicitud de cancelación. Si esa solicitud se completa correctamente, el trabajo pasará al estado "Cancelado". La suscripción a Azure solo se facturará si el trabajo de canalización se completa correctamente.

Se pueden crear varias canalizaciones a partir de una sola topología si se suministran valores diferentes para los parámetros de la topología. Por ejemplo, puede enviar trabajos de canalización con la misma topología para diferentes grabaciones de vídeo. Una topología se puede eliminar cuando se han eliminado todas las canalizaciones.

## <a name="sources-processors-and-sinks"></a>Orígenes, procesadores y receptores

Video Analyzer permite definir topologías de canalización con los nodos que se indican a continuación.

> [!NOTE]
> No todos los nodos están disponibles tanto en el servicio como en el módulo perimetral de Video Analyzer. Consulte [Reglas sobre el uso de los nodos](pipeline.md#rules-on-the-use-of-nodes).

### <a name="sources"></a>Orígenes

#### <a name="rtsp-source"></a>Origen RTSP

Un nodo de origen RTSP permite capturar elementos multimedia desde una cámara compatible con RTSP. Puede obtener más detalles [aquí](quotas-limitations.md#supported-cameras). El nodo de origen RTSP requiere que especifique una dirección URL de RTSP, junto con las credenciales para habilitar una conexión autenticada.

#### <a name="iot-hub-message-source"></a>Origen de mensajes de IoT Hub

Al igual que otros [módulos de IoT Edge](../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-device), el módulo Azure Video Analyzer puede recibir mensajes a través del [centro de IoT Edge](../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub). Se pueden enviar mensajes desde otros módulos o aplicaciones que se ejecutan en el dispositivo perimetral o desde la nube. Estos mensajes se pueden entregar (enrutar) a una [entrada con nombre](../../iot-edge/module-composition.md?view=iotedge-2020-11&preserve-view=true#sink) en el módulo de Video Analyzer. Un nodo de origen de mensajes de IoT Hub permite ingerir estos mensajes en una canalización. A continuación, los mensajes se pueden usar en una canalización para activar una puerta de señales (consulte la sección de [puertas de señales](#signal-gate-processor) a continuación).

Por ejemplo, podría tener un módulo de IoT Edge que genere un mensaje cuando se abre una puerta. El mensaje de ese módulo se puede enrutar al centro de IoT Edge, desde donde se puede enrutar al origen de los mensajes del centro de IoT de una canalización. Dentro de la canalización, el mensaje se puede pasar del origen de los mensajes del centro de IoT a un procesador de la puerta de señales, que puede activar la grabación del vídeo desde un origen RTSP en un archivo.

#### <a name="video-source"></a>Origen de vídeo

Permite usar un contenido de vídeo grabado de Video Analyzer como origen. El nodo requiere que especifique el nombre del [recurso de vídeo](terminology.md#video), así como la hora de inicio y finalización de las partes del vídeo grabado que se van a procesar.

### <a name="processors"></a>Procesadores

#### <a name="motion-detection-processor"></a>Procesador de detección de movimiento

El nodo del procesador de detección de movimiento permite detectar movimiento en vídeos en directo. Examina los fotogramas de vídeo entrantes y determina si hay movimiento en el vídeo. Si se detecta movimiento, pasa el fotograma de vídeo al siguiente nodo en la canalización y emite un evento. El nodo del procesador de detección de movimiento (junto con otros nodos) se puede usar para desencadenar la grabación del vídeo entrante cuando se detecta movimiento.

#### <a name="http-extension-processor"></a>Procesador de extensiones HTTP

El nodo del procesador de extensiones HTTP permite ampliar la canalización al módulo de IoT Edge. Este nodo recibe fotogramas de vídeo descodificados como entrada y los retransmite a un punto de conexión REST HTTP expuesto por el módulo, donde puede analizar el fotograma con un modelo de inteligencia artificial y devolver los resultados de la inferencia. Además, el nodo tiene un formateador de imagen integrado para escalar y codificar los fotogramas de vídeo antes de retransmitirlos al punto de conexión HTTP. El escalador tiene opciones para conservar, rellenar o ajustar la relación de aspecto de la imagen. El codificador de imágenes admite los formatos JPEG, PNG, BMP y RAW. Obtenga más información sobre el [procesador aquí](pipeline-extension.md#http-extension-processor).

#### <a name="grpc-extension-processor"></a>Procesador de extensiones gRPC

Este nodo procesador de extensiones gRPC toma los fotogramas de vídeo descodificados como entrada y los retransmite a un punto de conexión [gRPC](terminology.md#grpc) expuesto por el módulo. El nodo admite la transferencia de datos mediante [memoria compartida](https://en.wikipedia.org/wiki/Shared_memory) o la incrustación directa del fotograma en el cuerpo de los mensajes gRPC. Al igual que el proceso de la extensión HTTP, este nodo también tiene un formateador de imagen integrado para escalar y codificar los fotogramas de vídeo antes de retransmitirlos al punto de conexión gRPC. Obtenga más información sobre el [procesador aquí](pipeline-extension.md#grpc-extension-processor).

#### <a name="cognitive-services-extension-processor"></a>Procesador de extensiones de Cognitive Services

El procesador de extensiones de Cognitive Services le permite ampliar la canalización al módulo de [análisis espacial](https://azure.microsoft.com/services/cognitive-services/computer-vision/) de IoT Edge. Este nodo recibe fotogramas de vídeo descodificados como entrada y los retransmite a un punto de conexión [gRPC](pipeline-extension.md#grpc-extension-processor) expuesto, donde puede analizar el fotograma con las aptitudes de análisis espacial y devolver los resultados de la inferencia. Obtenga más información sobre el [procesador aquí](pipeline-extension.md#cognitive-services-extension-processor).

#### <a name="signal-gate-processor"></a>Procesador de la puerta de señales

El nodo del procesador de la puerta de señales permite reenviar de manera condicional elementos multimedia de un nodo a otro. El nodo de procesador de puerta de señal debe ir seguido inmediatamente de un receptor de vídeo o un receptor de archivos. Un ejemplo de caso de uso consiste en insertar un nodo del procesador de la puerta de señales entre el nodo de origen RTSP y el nodo receptor del vídeo y, después, usar la salida de un nodo del procesador de detección de movimiento para desencadenar la puerta. Con este tipo de canalización, solo se graba el vídeo cuando se detecta movimiento. También puede usar la salida del nodo de extensiones HTTP o gRPC para desencadenar la puerta, en lugar del nodo del procesador de detección de movimiento, lo que permite la grabación de vídeo cuando se detecta algo interesante.

#### <a name="object-tracker-processor"></a>Procesador de seguimiento de objetos

El nodo del procesador de seguimiento de objetos permite realizar un seguimiento de los objetos detectados en un nodo ascendente del procesador de extensiones HTTP o gRPC. Este nodo resulta útil cuando necesita detectar objetos en cada fotograma, pero el dispositivo perimetral no tiene la capacidad de proceso necesaria para poder aplicar el modelo de inteligencia artificial en todos los fotogramas. Si solo puede ejecutar el modelo de Computer Vision cada diez fotogramas, el seguimiento de objetos puede tomar los resultados de uno de estos fotogramas y, a continuación, usar técnicas de [flujo óptico](https://en.wikipedia.org/wiki/Optical_flow) para generar resultados para el segundo, tercer y hasta noveno fotograma, hasta que el modelo se aplique de nuevo en el fotograma siguiente. Hay un equilibrio entre la capacidad de proceso y la precisión al usar este nodo. Cuanto más cercanos sean los fotogramas en los que se aplica el modelo de inteligencia artificial, mayor será la precisión. Sin embargo, esto significa aplicar el modelo de inteligencia artificial con más frecuencia, lo que se traduce en una mayor capacidad de proceso. Un uso común del nodo del procesador de seguimiento de objetos es detectar cuándo un objeto cruza una línea.

#### <a name="line-crossing-processor"></a>Procesador de cruce de líneas

El nodo del procesador de cruce de líneas permite detectar en qué momento un objeto cruza una línea definida por el usuario. Además, mantiene también un recuento del número de objetos que cruzan la línea (desde el momento en que se activa una canalización). Este nodo debe usarse en el nivel inferior de un nodo del procesador de seguimiento de objetos.

#### <a name="encoder-processor"></a>Procesador de codificador

El nodo del procesador del codificador permite al usuario especificar propiedades de codificación al convertir el vídeo grabado al formato deseado para el procesamiento de bajada. Por ejemplo, es posible que sea necesario cambiar el tamaño del vídeo grabado desde una cámara configurada para una [resolución 4K](https://en.wikipedia.org/wiki/4K_resolution) a la resolución [Full HD (1920 x 1080)](https://en.wikipedia.org/wiki/1080p) antes de exportarlo a un archivo.

### <a name="sinks"></a>Receptores

#### <a name="video-sink"></a>Receptor de vídeo

Un nodo receptor de vídeo permite guardar el vídeo y los metadatos asociados en el recurso en la nube de Video Analyzer. El vídeo se puede grabar de forma continua o dispersa (según los eventos). El nodo receptor de vídeo puede almacenar el vídeo en la caché del dispositivo perimetral si se pierde la conectividad a la nube y reanudar la carga cuando se restaure dicha conectividad. Puede consultar el artículo sobre la [grabación continua de vídeo](continuous-video-recording.md) para obtener más información sobre cómo se pueden configurar las propiedades de este nodo.

Por motivos de seguridad, una instancia del **módulo perimetral** de Video Analyzer determinada solo puede grabar contenido en nuevas entradas de vídeo o en entradas de vídeo grabadas previamente por el mismo módulo. Cualquier intento de grabar contenido en un vídeo existente que no haya creado la misma instancia del módulo perimetral devolverá un error en el registro.

#### <a name="file-sink"></a>Receptor de archivos

El nodo receptor de archivos permite escribir vídeos en una ubicación del sistema de archivos local del dispositivo perimetral. Solo puede haber un nodo receptor de archivos en una canalización y debe encontrarse en un nivel inferior a un nodo del procesador de la puerta de señales. Esto limita la duración de los archivos de salida a los valores especificados en las propiedades del nodo del procesador de la puerta de señales. Para asegurarse de que el dispositivo perimetral no se queda sin espacio en disco, también puede establecer el tamaño máximo que el módulo perimetral de Video Analyzer puede usar para almacenar datos en caché.

Si la caché se llena, el módulo perimetral de Video Analyzer comenzará a eliminar los datos más antiguos y los reemplazará por los nuevos.

#### <a name="iot-hub-message-sink"></a>Receptor de mensajes de IoT Hub

Un nodo receptor de mensajes de IoT Hub permite publicar eventos en el centro de IoT Edge. El centro de IoT Edge puede configurarse para enrutar los datos a otros módulos o aplicaciones del dispositivo perimetral o a IoT Hub en la nube (según las rutas especificadas en el manifiesto de implementación). El nodo receptor de mensajes de IoT Hub puede aceptar eventos de nodos de procesadores de nivel superior, como un nodo del procesador de detección de movimiento o de un servicio de inferencia externo, a través de un nodo de procesador de extensiones HTTP.

## <a name="rules-on-the-use-of-nodes"></a>Reglas sobre el uso de los nodos

En la tabla a continuación se describen las reglas actuales sobre qué nodos se permiten en topologías en directo y por lotes, así como en el servicio y el módulo perimetral de Video Analyzer.

| nombre del nodo                              | Tipo de topología  |  Implementación       |
| -------------------------------------- | ---------------|-------------------|
| Origen RTSP                            | Directo           |  Entorno perimetral y nube   |
| Origen de mensajes del centro de IoT                 | Directo           |  Edge             |
| Origen de vídeo                           | Batch          |  Nube            |
| Procesador de detección de movimiento             | Directo           |  Edge             |
| Procesador de extensiones HTTP               | Directo           |  Edge             |
| Procesador de extensiones gRPC               | Directo           |  Edge             |
| Procesador de extensiones de Cognitive Services | Directo           |  Edge             |
| Procesador de la puerta de señales                  | Directo           |  Edge             |
| Procesador de seguimiento de objetos               | Directo           |  Edge             |
| Procesador de cruce de líneas                | Directo           |  Edge             |
| Procesador de codificador                      | Batch          |  Nube            |
| Receptor de vídeo                             | En directo y por lotes |  Entorno perimetral y nube   |
| Receptor de archivos                              | Directo           |  Edge             |
| Receptor de mensajes del centro de IoT                   | Directo           |  Edge             |

Consulte las [limitaciones de las canalizaciones](quotas-limitations.md#limitations-on-pipeline-topologies) para obtener más reglas sobre cómo se pueden usar diferentes nodos dentro de una topología.

## <a name="scenarios"></a>Escenarios

Al combinar los orígenes, procesadores y receptores definidos anteriormente, puede crear canalizaciones para diversos escenarios que implican el análisis de vídeos en directo. Los escenarios de ejemplo son:

* [Grabación continua de vídeo](continuous-video-recording.md) 
* [Grabación de vídeo basada en eventos](event-based-video-recording-concept.md) 
* [Análisis de vídeos sin grabación](analyze-live-video-without-recording.md) 

## <a name="next-steps"></a>Pasos siguientes

Para ver cómo puede ejecutar la detección de movimiento en una fuente de vídeo en directo, consulte [Inicio rápido: Introducción a Azure Video Analyzer](get-started-detect-motion-emit-events.md).
