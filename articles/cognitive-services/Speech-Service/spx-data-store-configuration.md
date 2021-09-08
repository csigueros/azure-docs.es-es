---
title: 'Opciones de configuración de la CLI de Voz: Servicio Voz'
titleSuffix: Azure Cognitive Services
description: Aprenda a crear y administrar archivos de configuración para su uso con la CLI de Voz de Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: bdae10e5e99216105035c45f9693acafb58b79a2
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537317"
---
# <a name="speech-cli-configuration-options"></a>Opciones de configuración de la CLI de Voz

El comportamiento de la CLI de Voz utiliza los valores de los archivos de configuración, a los se puede hacer referencia mediante un símbolo `@`. La CLI de Voz guarda los valores nuevos en un subdirectorio `./spx/data` nuevo que crea en el directorio de trabajo de la CLI de Voz. Al buscar un valor de configuración, la CLI de Voz busca en el directorio de trabajo actual, luego en el almacén datos de `./spx/data` y, después, en otros almacenes de datos, incluido uno de solo lectura final que se encuentra en el binario `spx`. 

En el inicio rápido de la CLI de Voz se usó el almacén de datos para guardar los valores `@key` y `@region`, por lo que no era necesario especificarlos con cada comando `spx`. Tenga en cuenta que puede usar los archivos de configuración para almacenar su propia configuración, o incluso para pasar direcciones URL o cualquier otro contenido dinámico que se haya generado en el runtime.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>Creación y administración de archivos de configuración en el almacén de datos

En esta sección se muestra cómo se usa un archivo de configuración en el almacén de datos local para almacenar y capturar la configuración de comandos mediante `spx config`, así como para almacenar la salida de la CLI de Voz mediante la opción `--output`.

En el ejemplo siguiente se borra el archivo de configuración `@my.defaults`, se agregan pares clave-valor para **clave** y **región** en el archivo, y se usa la configuración en una llamada a `spx recognize`.

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

En los archivos de configuración también se puede escribir contenido dinámico. Por ejemplo, el siguiente comando crea un modelo de voz personalizado y almacena la dirección URL del mismo en un archivo de configuración. El siguiente comando espera hasta que el modelo de la dirección URL esté listo para su uso antes de volver.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

En el ejemplo siguiente se escriben dos direcciones URL en el archivo de configuración `@my.datasets.txt`. En este escenario, existe la opción de que `--output` incluya la palabra clave **add** para crear un archivo de configuración o anexarlo al existente.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Para más información sobre los archivos del almacén de datos, incluido el uso de archivos de configuración predeterminados (`@spx.default`, `@default.config` y `@*.default.config` para la configuración predeterminada específica del comando), escriba este comando:

```console
spx help advanced setup
```

## <a name="next-steps"></a>Pasos siguientes 

* [Operaciones por lotes con la CLI de Voz](./spx-batch-operations.md)