---
title: Opciones de la línea de comandos del Explorador de Azure Storage | Microsoft Docs
description: Documentación de las opciones de línea de comandos de inicio del Explorador de Azure Storage
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 69b5eb294bf33a56e89052752cb8473189f34eac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626653"
---
# <a name="azure-storage-explorer-command-line-options"></a>Opciones de la línea de comandos del Explorador de Azure Storage

El Explorador de Microsoft Azure Storage tiene un conjunto de opciones de línea de comandos que se pueden agregar al iniciar la aplicación. La mayoría de las opciones de línea de comandos son para depurar o solucionar problemas.

## <a name="command-line-options"></a>Opciones de línea de comandos

Opción  | Descripción
:------- | :-----------
`--debug`/`--prod`  | Inicia la aplicación en modo de depuración o de producción. En el modo de depuración, los datos adjuntos locales se almacenarán en el almacenamiento local de la aplicación y no se cifrarán. Las propiedades ocultas se mostrarán en el panel Propiedades de los nodos de recursos seleccionados. El nivel de detalle del registro se establecerá para imprimir los mensajes de depuración que revelan la lógica de instalación interna del Explorador de Storage. El valor predeterminado es `--prod`.
`--lang`  | Inicia la aplicación con un idioma determinado. Por ejemplo, `--lang="zh-Hans"`.
`--disable-gpu` | Inicia la aplicación sin aceleración de GPU.
`--auto-open-dev-tools` | Permite que la aplicación abra la ventana herramientas de desarrollo en cuanto se muestre la ventana del explorador. Esta opción resulta útil si quiere alcanzar un punto de interrupción en alguna línea del código de inicio de la ventana del explorador.
`--verbosity` | Establece el nivel de detalle del registro del Explorador de Storage. Entre los niveles de detalle se incluyen`debug`, `verbose`, `info`, `warn`, `error` y `silent`. Por ejemplo, `--verbosity=verbose`. Cuando se ejecuta en modo de producción, el nivel de detalle predeterminado es `info`. Cuando se ejecuta en modo de depuración, el nivel de detalle del registro siempre será `debug`.
`--log-dir` | Establece el directorio en el que se guardarán los archivos de registro. Por ejemplo, `--log-dir=path_to_a_directory`.

Ejemplo de inicio del Explorador de Storage con opciones de línea de comandos personalizadas.

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> Estas opciones de la línea de comandos pueden cambiar en nuevas versiones del Explorador de Storage.

## <a name="when-to-use-command-line-options"></a>Cuándo usar las opciones de la línea de comandos

Algunas opciones de la línea de comandos se pueden usar para personalizar el Explorador de Storage. Es el caso de esas opciones que tienen una configuración de usuario correspondiente, como `--lang`. Se recomienda usar la configuración de usuario en lugar de usar la opción de línea de comandos.

Las demás opciones de la línea de comandos pueden resultar útiles para la depuración y solución de problemas. Si surge un problema en el Explorador de Storage, la reproducción del problema en el modo de depuración puede ayudarnos a obtener información más detallada para investigar.
