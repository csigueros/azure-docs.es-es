---
title: Kit de procesamiento por lotes para contenedores de voz
titleSuffix: Azure Cognitive Services
description: Use el kit de procesamiento por lotes para escalar las solicitudes del contenedor de voz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: 0ecdb23eddcb47f8a50c2d10cab601787354a81b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745651"
---
# <a name="batch-processing-kit-for-speech-containers"></a>Kit de procesamiento por lotes para contenedores de voz

Use el kit de procesamiento por lotes para complementar y escalar horizontalmente las cargas de trabajo en los contenedores de voz. Disponible como contenedor, esta utilidad de código abierto ayuda a facilitar la transcripción por lotes de grandes cantidades de archivos de audio, en cualquier número de puntos de conexión de contenedor de voz locales y basados en la nube. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="Un diagrama que muestra un ejemplo del flujo de trabajo del kit por lote para contenedor.":::

El kit por lote para contenedor está disponible de forma gratuita en [GitHub](https://github.com/microsoft/batch-processing-kit) y [Docker Hub](https://hub.docker.com/r/batchkit/speech-batch-kit/tags). Solo se le [facturarán](speech-container-howto.md#billing) los contenedores de voz que utilice.

| Característica  | Descripción  |
|---------|---------|
| Distribución de archivos de audio por lotes     | Envíe automáticamente un gran número de archivos a los puntos de conexión de contenedor de voz locales o basados en la nube. Los archivos pueden estar en cualquier volumen compatible con POSIX, incluidos los sistemas de archivos de red.       |
| Integración de SDK de voz | Pase las marcas comunes al SDK de voz, incluidos los siguientes supuestos, diarización, lenguaje, enmascaramiento de blasfemias.  |
|Modos de ejecución     | Ejecute el lote del cliente una vez, continuamente en segundo plano, o cree puntos de conexión HTTP para los archivos de audio.         |
| Tolerancia a errores | Reintentar y continuar automáticamente la transcripción sin perder el progreso y diferenciar entre los errores que se pueden y no se pueden repetir. |
| Detección de disponibilidad de punto de conexión | Si un punto de conexión deja de estar disponible, el cliente del lote continuará la transcripción, con otros puntos de conexión del contenedor. Después de volver a estar disponible, el cliente comenzará a usar el punto de conexión automáticamente.   |
| Intercambio frecuente del punto de conexión | Agregar, quitar o editar los puntos de conexión del contenedor de voz durante el tiempo de ejecución sin interrumpir el progreso del lote. Las actualizaciones son inmediatas. |
| Registro en tiempo real | Registro en tiempo real de los intentos de solicitud, las marcas de tiempo y los motivos de error, con archivos de registro del SDK de voz para cada archivo de audio. |

## <a name="get-the-container-image-with-docker-pull"></a>Obtención de la imagen del contenedor con `docker pull`

Use el comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para descargar el contenedor del kit de lote más reciente.

[!INCLUDE [pull-image-include](../../../includes/pull-image-include.md)]

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>Configuración del punto de conexión

El lote del cliente toma un archivo de configuración YAML que especifica los puntos de conexión del contenedor local. El ejemplo siguiente se puede escribir en `/mnt/my_nfs/config.yaml`, que se usa en los ejemplos siguientes. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

Este ejemplo de YAML especifica tres contenedores de voz en tres hosts. El primer host está especificado por una dirección IPv4, el segundo se ejecuta en la misma VM que el lote del cliente y el tercer contenedor está especificado por el nombre de host DNS de otra VM. El valor `concurrency` especifica el número máximo de transcripciones de archivos simultáneas que se pueden ejecutar en el mismo contenedor. El valor `rtf` (factor en tiempo real) es opcional y se puede usar para optimizar el rendimiento.
 
El lote del cliente puede detectar dinámicamente si un punto de conexión deja de estar disponible (por ejemplo, debido a un reinicio del contenedor o un problema de red) y cuando vuelve a estar disponible. Las solicitudes de transcripción no se enviarán a los contenedores que no estén disponibles y el cliente seguirá usando otros contenedores disponibles. Puede agregar, quitar o editar los puntos de conexión en cualquier momento sin interrumpir el progreso del lote.


## <a name="run-the-batch-processing-container"></a>Ejecutar el contenedor de procesamiento por lotes
  
> [!NOTE] 
> * Este ejemplo utiliza el mismo directorio (`/my_nfs`) para el archivo de configuración y los directorios de entradas, salidas y registros. Puede usar directorios hospedados o NFS montados para estas carpetas.
> * Al ejecutar el cliente con `–h` se listarán los parámetros de la línea de comandos disponibles, y sus valores predeterminados. 
> * El contenedor de procesamiento por lotes solo se admite en Linux.

Utilice el comando `run` de Docker para iniciar el contenedor. Se iniciará un shell interactivo dentro del contenedor.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

Para ejecutar el lote cliente:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

Para ejecutar el lote cliente y el contenedor en un solo comando:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


El cliente comenzará a ejecutarse. Si ya se ha transformado un archivo de audio en una ejecución anterior, el cliente omitirá automáticamente el archivo. Los archivos se envían con un reintento automático si se producen errores transitorios y puede diferenciar entre los errores en los que desea que el cliente vuelva a intentarlo. En un error de transcripción, el cliente seguirá la transcripción y puede volver a intentarlo sin perder el progreso.  

## <a name="run-modes"></a>Modos de ejecución 

El kit de procesamiento por lotes ofrece tres modos, mediante el parámetro `--run-mode`.

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

El modo `ONESHOT` transcribe un solo lote de archivos de audio (de un directorio de entrada y una lista de archivos opcional) a una carpeta de salida.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="Un diagrama que muestra los archivos de procesamiento del contenedor del kit por lotes en modo OneShot.":::

1. Defina los puntos de conexión del contenedor de voz que el cliente de lote usará en el archivo de `config.yaml`. 
2. Coloque los archivos de audio para la transcripción en un directorio de entrada.  
3. Invoque el contenedor en el directorio, que iniciará el procesamiento de los archivos. Si el archivo de audio ya ha sido transcrito en una ejecución anterior con el mismo directorio de salida (mismo nombre de archivo y suma de comprobación), el cliente omitirá el archivo. 
4. Los archivos se envían a los puntos de conexión del contenedor del paso 1.
5. Los registros y la salida del contenedor de voz se devuelven al directorio de salida especificado. 

#### <a name="daemon"></a>[Demonio](#tab/daemon)

> [!TIP]
> Si se agregan varios archivos al directorio de entrada al mismo tiempo, puede mejorar el rendimiento si los agrega en un intervalo normal.

El modo `DAEMON` transcribe los archivos existentes en una carpeta determinada, y transcribe continuamente nuevos archivos de audio a medida que se agregan.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="Un diagrama que muestra los archivos de procesamiento del contenedor del kit por lotes en modo demonio.":::

1. Defina los puntos de conexión del contenedor de voz que el cliente de lote usará en el archivo de `config.yaml`. 
2. Invoque el contenedor en un directorio de entrada. El cliente de lote comenzará a supervisar el directorio para los archivos entrantes. 
3. Configure la entrega continua de archivos de audio en el directorio de entrada. Si el archivo de audio ya ha sido transcrito en una ejecución anterior con el mismo directorio de salida (mismo nombre de archivo y suma de comprobación), el cliente omitirá el archivo. 
4. Una vez que se detecta una escritura de archivo o una señal POSIX, el contenedor se desencadena para responder.
5. Los archivos se envían a los puntos de conexión del contenedor del paso 1.
6. Los registros y la salida del contenedor de voz se devuelven al directorio de salida especificado. 

#### <a name="rest"></a>[REST](#tab/rest)

El modo `REST` es un modo de servidor API que proporciona un conjunto básico de puntos de conexión HTTP para el envío por lotes de archivos de audio, la comprobación del estado y el sondeo prolongado. También habilita el consumo programático mediante una extensión de módulo de python, o la importación como un submódulo.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="Un diagrama que muestra los archivos de procesamiento del contenedor del kit por lotes en modo REST.":::

1. Defina los puntos de conexión del contenedor de voz que el cliente de lote usará en el archivo de `config.yaml`. 
2. Envíe una solicitud HTTP a uno de los puntos de conexión del servidor de la API. 
        
    |Punto de conexión  |Descripción  |
    |---------|---------|
    |`/submit`     | Punto de conexión para crear nuevas solicitudes por lotes.        |
    |`/status`     | Punto de conexión para comprobar el estado de una solicitud por lotes. La conexión permanecerá abierta hasta que finalice el lote.       |
    |`/watch`     | Punto de conexión para usar el sondeo de HTTP prolongado hasta que finalice el lote.        |

3. Los archivos de audio se cargan desde el directorio de entrada. Si el archivo de audio ya ha sido transcrito en una ejecución anterior con el mismo directorio de salida (mismo nombre de archivo y suma de comprobación), el cliente omitirá el archivo. 
4. Si se envía una solicitud al punto de conexión `/submit`, los archivos se envían a los puntos de conexión del contenedor del paso 1.
5. Los registros y la salida del contenedor de voz se devuelven al directorio de salida especificado. 

---

## <a name="logging"></a>Registro

> [!NOTE]
> El cliente de lote puede sobrescribir el archivo *run.log* periódicamente si es demasiado grande.

El cliente crea un archivo *run.log* en el directorio especificado por el argumento `-log_folder` en el comando `run` de Docker. Los registros se capturan en el nivel de depuración de forma predeterminada. Los mismos registros se envían al `stdout/stderr` y se filtran según los argumentos `-file_log_level` o `console_log_level`. Este registro solo es necesario para la depuración o si necesita enviar un seguimiento para la compatibilidad. La carpeta de registro también contiene los registros del SDK de voz para cada archivo de audio.

El directorio de salida especificado por `-output_folder` contendrá un archivo *run_summary.json* , que se reescribe periódicamente cada 30 segundos o cada vez que se terminan nuevas transcripciones. Puede usar este archivo para comprobar el progreso a medida que el lote continúa. También contendrá las estadísticas de ejecución finales y el estado final de todos los archivos cuando se complete el lote. El lote se completa cuando el proceso tiene una salida limpia. 

## <a name="next-steps"></a>Pasos siguientes

* [Instalación y ejecución de contenedores](speech-container-howto.md)
