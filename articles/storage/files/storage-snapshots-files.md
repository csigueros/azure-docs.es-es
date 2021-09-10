---
title: Introducción a las instantáneas de recurso compartido de Azure Files | Microsoft Docs
description: Una instantánea de recurso compartido es una versión de solo lectura de un recurso compartido de Azure Files que se usa en un momento dado como método para realizar una copia de seguridad del recurso compartido.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ba229ed81cd5ece4a6013654a11c21740c00c844
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254072"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Información general de las instantáneas de recurso compartido de Azure Files
Azure Files proporciona la funcionalidad de tomar instantáneas de recurso compartido de recursos compartidos de archivos. Las instantáneas de recursos compartidos capturan el estado del recurso compartido en ese momento dado. En este artículo se describen las funcionalidades que proporcionan las instantáneas de recurso compartido y cómo se puede sacar provecho de ellas en el caso de uso personalizado.

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## <a name="when-to-use-share-snapshots"></a>Cuándo usar instantáneas de recursos compartidos

### <a name="protection-against-application-error-and-data-corruption"></a>Error de protección frente a la aplicación y datos dañados

Las aplicaciones que usan recursos compartidos de archivos realizan operaciones tales como escritura, lectura, almacenamiento, transmisión o procesamiento. Si se desconfigura una aplicación o se produce un error accidental, es posible que se produzca una sobrescritura involuntaria o daños en algunos bloques. Para evitar estas circunstancias, puede tomar una instantánea del recurso compartido antes de implementar un código de aplicación nuevo. Si se produce un error o hay algún problema con la aplicación al llevar a cabo la nueva implementación, puede volver a la versión anterior de los datos de ese recurso compartido de archivos. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Protección frente a eliminaciones accidentales y cambios no intencionados

Imagine que está trabajando en un archivo de texto en un recurso compartido de archivos. Una vez que se cierra el archivo de texto pierde la capacidad de deshacer los cambios. En estos casos, necesitará recuperar una versión anterior del archivo. Gracias a las instantáneas de recursos compartidos, podrá recuperar versiones anteriores del archivo si se cambia el nombre o se elimina accidentalmente.

### <a name="general-backup-purposes"></a>Propósitos generales de copia de seguridad

Después de crear un recurso compartido de archivos, puede crear periódicamente una instantánea de recurso compartido del recurso compartido de archivos para usarla como una copia de seguridad de los datos. Si toma instantáneas de recurso compartido de forma periódica, podrá tener versiones de datos previas y usarlas si son necesarias en posibles auditorías o en una recuperación ante desastres. Se recomienda usar la [copia de seguridad de recursos compartidos de archivos de Azure](../../backup/azure-file-share-backup-overview.md) como una solución de copia de seguridad para tomar y administrar instantáneas. También puede tomar y administrar las instantáneas usted mismo mediante la CLI o PowerShell.

## <a name="capabilities"></a>Capacidades

Una instantánea de recurso compartido es una copia de solo lectura de un momento dado de sus datos. Puede crear, eliminar y administrar instantáneas mediante la API de REST. Asimismo, tiene disponibles estas mismas funcionalidades en la biblioteca cliente, la CLI de Azure y Azure Portal. 

Puede ver las instantáneas de un recurso compartido con la API de REST y SMB. Igualmente, puede recuperar la lista de versiones del directorio o archivo y también puede montar una versión específica directamente como unidad (disponible solo en Windows: consulte los [Límites](#limits)). 

Una vez se crea la instantánea de recurso compartido, puede leerla, copiarla o eliminarla, pero no modificarla. Recuerde que no puede copiar una instantánea de recurso compartido completa en otra cuenta de almacenamiento. Si quiere copiarla, deberá hacerlo archivo por archivo, mediante AzCopy u otros mecanismos de copia.

En el nivel del recurso compartido de archivos se proporciona la funcionalidad de la instantánea de recurso compartido. En cambio, en el nivel de archivos individual se proporciona la opción de recuperación que le permitirá restaurar archivos individuales. Puede restaurar un recurso compartido de archivos completo mediante el SMB, la API de REST, el portal, la biblioteca cliente o con las herramientas de PowerShell o CLI.

Una instantánea de recurso compartido de un recurso compartido de archivos es idéntica a su recurso compartido de archivos base. La única diferencia es que se anexa un valor **DateTime** al URI del recurso compartido para indicar el momento en que se tomó la instantánea de recurso compartido. Por ejemplo, si el identificador URI de un recurso compartido de archivos es http:\//storagesample.core.file.windows.net/myshare, el identificador URI de la instantánea de recurso compartido es similar a:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Las instantáneas de recurso compartido se conservan hasta que se eliminan explícitamente. Una instantánea de recurso compartido no puede durar más que su recurso compartido de archivos base. Puede enumerar las instantáneas asociadas al recurso compartido de archivos base para llevar a cabo un seguimiento de las instantáneas actuales. 

Cuando crea una instantánea de recurso compartido de un recurso compartido de archivos, los archivos que se encuentren en las propiedades del sistema del recurso compartido se copian en la instantánea de recurso compartido con los mismos valores. Los metadatos del recurso compartido de archivos y los archivos base también se copian en la instantánea de recurso compartido, a menos que especifique los metadatos independientes de la instantánea de recurso compartido al crearla.

No puede eliminar un recurso compartido que disponga de instantáneas de recurso compartido sin eliminar todas sus instantáneas de recurso compartido primero.

## <a name="space-usage"></a>Uso del espacio

Las instantáneas de recurso compartido son de naturaleza incremental. Solo se guardan los datos que hayan cambiado después de realizar la instantánea de recurso compartido más reciente. Esto minimiza el tiempo necesario para crear la instantánea de recurso compartido y ahorra en costos de almacenamiento. Cualquier operación de escritura en el objeto o propiedad o cualquier operación de actualización de metadatos se agrega al "contenido cambiado" y se guarda en la instantánea de recurso compartido. 

Para ahorrar espacio, puede eliminar la instantánea de recurso compartido durante el periodo en el que la renovación se encuentra en su punto álgido.

Incluso si las instantáneas de recurso compartido se guardan de forma incremental, solamente deberá guardar la instantánea de recurso compartido más reciente para poder restaurar el recurso compartido. Cuando elimine una instantánea de recurso compartido, solo se quitan los datos exclusivos para esa instantánea de recurso compartido. Las instantáneas activas contienen toda la información necesaria para examinar y restaurar sus datos (desde el momento en el que se realizó la instantánea de recurso compartido) en la ubicación original o en una alternativa. Puede restaurarlas en el nivel de elemento.

Las instantáneas no cuentan respecto al límite de tamaño del recurso compartido. No hay ninguna restricción en la cantidad de espacio que ocupan las instantáneas de recurso compartido. Los límites de cuenta de almacenamiento se siguen aplicando.

## <a name="limits"></a>Límites

En cambio, el número máximo de instantáneas de recurso compartido que permite Azure Files actualmente es de 200. Una vez se llegue a las 200 instantáneas de recurso compartido, las instantáneas más antiguas se eliminarán para poder crear otras nuevas. 

No hay ningún límite en las llamadas simultáneas dedicadas a crear instantáneas de recurso compartido. Asimismo, tampoco hay ningún límite en la cantidad de espacio que las instantáneas de recurso compartido de un recurso compartido de archivos determinado pueden consumir. 

En la actualidad, no es posible montar instantáneas de recurso compartido en Linux. Esto se debe a que el cliente de SMB de Linux, a diferencia del de Windows, no es compatible con el montaje de instantáneas.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Volver a copiar datos en un recurso compartido desde una instantánea de recurso compartido

Las operaciones de copia que implican archivos e instantáneas de recurso compartido siguen estas reglas:

Puede copiar archivos individuales en una instantánea de recurso compartido de archivos por encima de su recurso compartido base o en cualquier otra ubicación. Puede restaurar una versión anterior de un archivo o restaurar un recurso compartido de archivos completo copiando archivo por archivo desde la instantánea de recurso compartido. La instantánea de recurso compartido no se promociona al recurso compartido base. 

La instantánea de recurso compartido se mantiene intacta después de la operación de copia, pero el recurso compartido de archivos base se sobrescribe con una copia de los datos que estaban disponibles en la instantánea de recurso compartido. Todos los archivos restaurados se tienen en cuenta como "contenido cambiado".

Puede copiar un archivo en una instantánea de recurso compartido en un destino diferente con un nombre distinto. El archivo de destino resultante es un archivo en el que se puede escribir y no una instantánea de recurso compartido. En este caso, el recurso compartido de archivos de base permanecerá intacto.

Cuando un archivo de destino se sobrescribe con una copia, las instantáneas de recurso compartido asociadas al archivo de destino original no se modifican.

## <a name="general-best-practices"></a>Procedimientos recomendados generales

Se recomienda usar la [copia de seguridad de recursos compartidos de archivos de Azure](../../backup/azure-file-share-backup-overview.md) como una solución para tomar instantáneas automáticamente, así como para administrarlas. Cuando ejecute una infraestructura en Azure, automatice las copias de seguridad para recuperar los datos cuando sea posible. Las acciones automatizadas son más fiables que los procesos manuales, lo que le ayuda a mejorar la recuperabilidad y la protección de datos. Puede usar la copia de seguridad de recursos compartidos de archivos de Azure, la API de REST, el SDK de cliente o el scripting de automatización.

Antes de implementar el programador de la instantánea de recurso compartido, tenga en cuenta la frecuencia de la instantánea de recurso compartido y la configuración de retención para evitar gastos innecesarios.

Las instantáneas de recurso compartido solo proporcionan protección a nivel de archivo. Recuerde que las instantáneas de recurso compartido no previenen eliminaciones que se hayan producido por errores involuntarios en un recurso compartido de archivos o en una cuenta de almacenamiento. Para impedir la eliminación accidental de una cuenta de almacenamiento, puede [habilitar la eliminación temporal](storage-files-prevent-file-share-deletion.md) o bloquear la cuenta de almacenamiento o el grupo de recursos.

## <a name="next-steps"></a>Pasos siguientes
- Trabajo con instantáneas de recurso compartido en:
    - [Copia de seguridad de un recurso compartido de archivos de Azure](../../backup/azure-file-share-backup-overview.md)
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Preguntas más frecuentes sobre instantáneas de recurso compartido](storage-files-faq.md#share-snapshots)