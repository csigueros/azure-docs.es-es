---
title: Exportación de una parte de un vídeo grabado de Azure Video Analyzer a un archivo MP4
description: En este tutorial, aprenderá a exportar una parte de un vídeo grabado de Video Analyzer en formato MP4 que se almacena como un vídeo de Video Analyzer que se puede descargar y usar fuera del ecosistema de cuentas de Video Analyzer.
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1da3d7b0a201f50ae2b3ed7e429cfb7673010cb6
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552931"
---
# <a name="tutorial-export-portion-of-recorded-video-as-an-mp4-file"></a>Tutorial: Exportación de una parte de un vídeo grabado como un archivo MP4

[!INCLUDE [header](includes/cloud-env.md)]

En este tutorial, aprenderá a exportar una parte de un vídeo que se ha grabado en la cuenta de Azure Video Analyzer.  Esta parte exportada del vídeo se guarda como un archivo MP4 que se puede descargar y usar fuera de la cuenta de Video Analyzer.

## <a name="suggested-pre-reading"></a>Sugerencias antes de la lectura  

Consulte estos artículos antes de empezar:

* [Introducción a Azure Video Analyzer](../overview.md)
* [Terminología de Azure Video Analyzer](../terminology.md)
* [Conceptos sobre la canalización de Video Analyzer](../pipeline.md)
* [Grabación de vídeo basada en eventos](../event-based-video-recording-concept.md)

## <a name="prerequisites"></a>Requisitos previos

Los requisitos previos de este tutorial son los siguientes:

* Una cuenta de Azure que incluya una suscripción activa. [Cree una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) gratuita, en caso de que aún no lo haya hecho.
* [Cuenta de Video Analyzer](../create-video-analyzer-account.md).
* Ha completado [Inicio rápido: Detección de movimiento y grabación de vídeo en Video Analyzer](../edge/detect-motion-record-video-clips-cloud.md) o cualquier canalización de Video Analyzer que grabe en un receptor de vídeo.

## <a name="overview"></a>Información general

Video Analyzer puede grabar vídeos desde un origen RTSP y en la cuenta de Video Analyzer estos vídeos se graban en un archivo segmentado.  El formato de archivo segmentado permite una duración ilimitada de la grabación de vídeo; sin embargo, en algunos casos es necesario guardar una parte del vídeo como MP4 para que se pueda archivar, descargar o reproducir individualmente fuera del ecosistema de Video Analyzer.

En este tutorial, aprenderá a:

* Acerca de las topologías de canalización por lotes y los trabajos de canalización por lotes
* Crear una topología por lotes.
* Crear un trabajo de canalización por lotes a partir de un archivo de vídeo de Video Analyzer que creará un archivo MP4 que contiene un valor de tiempo especificado (hasta 24 horas).

## <a name="pipeline-topology-of-batch-kind"></a>Topología de canalización del tipo **por lotes**

Una topología de canalización del tipo por lotes le permite describir cómo se debe procesar el vídeo grabado para su exportación en función de sus necesidades personalizadas a través de tres nodos interconectados.  Una topología de canalización del tipo por lotes es la base que se usa para los trabajos de canalización.  Un trabajo de canalización es la instancia individual de una topología de canalización del tipo por lotes.  El trabajo de canalización importa el vídeo grabado de Video Analyzer y lo guarda en la cuenta de almacenamiento de Video Analyzer, como un archivo MP4 descargable.  La topología de canalización del tipo por lotes usa un [nodo de origen de vídeo](../pipeline.md#video-source) que se conecta a un [nodo de procesador del codificador](../pipeline.md#encoder-processor) y, a continuación, se conecta a un [nodo receptor de vídeo](../pipeline.md#video-sink).

> [!NOTE]
> Para más información sobre orígenes, procesadores y receptores, consulte [Orígenes, procesadores y receptores](../pipeline.md#sources-processors-and-sinks). Para más información sobre los trabajos de canalización, consulte [trabajos de canalización](../pipeline.md#batch-pipeline).

## <a name="create-a-pipeline-job-from-videos"></a>Creación de un trabajo de canalización (desde Vídeos)

1. En Azure Portal vaya a su cuenta de Video Analyzer.
1. Seleccione **Vídeos** en la sección `Video Analyzer` y, a continuación, seleccione la secuencia de vídeo que se debe utilizar para exportar una parte del vídeo.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/export-portion-of-video-as-mp4/video-analyzer-video.png" alt-text="Imagen de la sección del menú Video Analyzer de Azure Video Analyzer que resalta la selección Vídeos.":::
1. En la hoja Reproductor de widget de vídeo, haga clic en **Crear trabajo** en la parte superior.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/export-portion-of-video-as-mp4/create-job.png" alt-text="Imagen de la hoja del widget de vídeo de Azure Video Analyzer que resalta la selección de Crear un trabajo.":::
1. En el menú emergente `Create Job`, seleccione:

   1. Seleccione **Crear desde la muestra** para `Batch topology`.
   1. Seleccione la topología de ejemplo de **exportación de vídeo** en la lista desplegable `Batch topology name`.
   1. Escriba un nombre en `Batch topology name` para guardar la topología.

        > [!NOTE]
        > La topología de ejemplo se guardará con el nombre especificado anteriormente.  Estará disponible para volverla a usar la próxima vez que se vaya a exportar un vídeo.
1. En la sección `Name your job` escriba un **nombre** y una **descripción** del trabajo en los campos correspondientes (la descripción es opcional).
1. En la sección `Define parameters`:
    1. Escriba el nombre del vídeo grabado de Video Analyzer en el campo `sourceVideoName`.
    1. En el campo `videoSourceTimeSequenceParameter`, seleccione las fechas de inicio y finalización haciendo clic en el icono de calendario y seleccionando las fechas de cada valor.  En los campos de tiempo, escriba las horas de inicio y finalización del trabajo de canalización que se usará para crear el clip de vídeo.
        > [!NOTE]
        > El valor de tiempo de un vídeo grabado determinado se muestra en la parte superior derecha del reproductor de widget de vídeo.  Este valor de tiempo se muestra en la imagen siguiente con un cuadro rojo alrededor.  El icono de calendario también se muestra en la imagen siguiente con un cuadro verde alrededor.

    1. Escriba un nombre para el archivo MP4 exportado en el campo `exportedVideoName`.
  
      > [!div class="mx-imgBorder"]
      > :::image type="content" source="./media/export-portion-of-video-as-mp4/video-widget-job-creation.png" alt-text="Imagen de la hoja emergente de trabajos de canalización y widget del reproductor de vídeo de Azure Video Analyzer que resalta la marca de tiempo del vídeo en un cuadro rojo y un cuadro verde alrededor del icono de calendario.":::
1. Haga clic en **Crear** en la parte inferior de la hoja emergente `Create a job`.  
    
    Para supervisar el trabajo de canalización, vaya a `Batch Jobs tab`.
1. En la sección `Video Analyzer`, seleccione **Lote**.
1. Haga clic en la pestaña `Jobs` de la parte superior de la hoja Lote.
  
    El trabajo por lotes entrará en un estado de procesamiento y, una vez completado correctamente, cambiará el estado a `Completed`.  Para ver el archivo de vídeo MP4 asociado:
1. Haga clic en **Vídeos** en la sección `Video Analyzer`.
1. Haga clic en el nombre del vídeo que coincide con el nombre de los trabajos por lotes usado anteriormente en el paso 5.  

El reproductor del widget de vídeo debe empezar a reproducir el archivo MP4.  Para descargar el archivo MP4, haga clic en `Download video` en la parte superior de la hoja.  Se abrirá el archivo MP4 en una nueva pestaña del explorador. Haga clic con el botón derecho en el vídeo y haga clic en **Guardar como**.

## <a name="cancel-a-pipeline-job"></a>Cancelación de un trabajo de canalización

Una vez que un trabajo de canalización ha entrado en el estado de procesamiento, se puede cancelar el trabajo de canalización.  Para cancelar un trabajo de canalización:

1. Vaya a la cuenta de Video Analyzer y seleccione **Lote** en la sección **Video Analyzer**.
1. En la hoja Lote, seleccione la pestaña **Trabajos** en la parte superior.
1. En la pestaña Trabajos encontrará una lista de trabajos que se encuentran en estados diferentes. Busque el trabajo que desea cancelar en el estado de procesamiento, seleccione **Cancelar** en el lado derecho de la pestaña Trabajos de canalización por lotes y, a continuación, haga clic en **Sí**.

    > [!NOTE]
    > No se puede cancelar un trabajo de canalización con errores.

## <a name="delete-a-pipeline-job"></a>Eliminación de un trabajo de canalización

Una vez que un trabajo de canalización ha entrado en el estado completado o con error, se puede eliminar el trabajo de canalización.  Para eliminar un trabajo de canalización:

1. Vaya a la cuenta de Video Analyzer y seleccione **Lote** en la sección **Video Analyzer**.
1. En la hoja Lote, seleccione la pestaña **Trabajos** en la parte superior.
1. En la pestaña Trabajos encontrará una lista de trabajos que se encuentran en estados diferentes.  Busque el trabajo que desea eliminar (en el estado cancelado, completado o con error) y seleccione **Eliminar** a la derecha de los trabajos de canalización y haga clic en **Eliminar**.

## <a name="delete-a-pipeline-topology-of-batch-kind"></a>Eliminación de una topología de canalización del tipo por lotes

Para eliminar una topología de canalización del tipo por lotes, se deben eliminar todos los trabajos de canalización asociados a la topología de canalización.  Para eliminar una topología de canalización del tipo por lotes:

1. Vaya a la cuenta de Video Analyzer.
2. Haga clic en **Lote** en la sección `Video Analyzer`.
3. En la pestaña topologías, busque la topología de canalización del tipo por lotes que desea eliminar y haga clic en " **...** " en el lado derecho de la topología de canalización del tipo por lotes.
4. Haga clic en **Eliminar topología**.

    > [!NOTE]
    > Se deben eliminar todos los trabajos de canalización de una topología de canalización del tipo por lotes para poder eliminar la topología.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere probar otros artículos de inicio rápidos o tutoriales, conserve los recursos que creó. En caso contrario, vaya a Azure Portal, luego a los grupos de recursos, seleccione el grupo de recursos en que ejecutó este inicio rápido y elimine todos los recursos.

## <a name="next-steps"></a>Pasos siguientes

* [Conecte cámaras directamente a la nube](./connect-cameras-to-cloud.md) para capturar y grabar vídeo, mediante [canalizaciones en la nube](../pipeline.md).
* [Conecte cámaras al servicio Video Analyzer a través del módulo perimetral de Video Analyzer que actúa como puerta de enlace transparente para paquetes de vídeo a través del protocolo RTSP](./use-remote-device-adapter.md).
