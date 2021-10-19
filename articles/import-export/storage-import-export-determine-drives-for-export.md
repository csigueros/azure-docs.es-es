---
title: Compruebe el número de unidades necesarias para una exportación con Azure Import/Export | Microsoft Docs
description: Descubra cuántas unidades necesita para una exportación mediante el servicio Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: eda9ddd4fa6fca9e4d46c5b86a7f3ab8b7c8cb03
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716318"
---
# <a name="check-number-of-drives-needed-for-an-export-with-azure-importexport"></a>Compruebe el número de unidades necesarias para una exportación con Azure Import/Export 

En este artículo se describe cómo determinar cuántas unidades físicas se deben proporcionar para el pedido de exportación de Azure Import/Export. Usará la herramienta Azure Import/Export. En el artículo se explica cómo instalar y usar la herramienta.

La herramienta Azure Import/Export versión 1 enumera todos los blobs que se van a exportar y calcula cómo empaquetarlos en unidades del tamaño especificado, teniendo en cuenta cualquier sobrecarga necesaria; después calcula el número de unidades necesarias para albergar los blobs y la información de uso de la unidad.

Puede usar SSD de 2,5", HDD de 2,5" o HDD de 3,5" con el pedido de exportación. Para obtener más información, consulte [Discos admitidos](storage-import-export-requirements.md#supported-disks).


## <a name="prerequisite"></a>Requisito previo

* Tendrá que usar la herramienta en un sistema de Windows que ejecute una [versión admitida del sistema operativo](storage-import-export-requirements.md#supported-operating-systems).

## <a name="determine-how-many-drives-you-need"></a>Determine cuántas unidades necesita

Para averiguar cuántos discos físicos necesita para el pedido de exportación, siga estos pasos:

1. Descargue la versión actual de la herramienta Azure Import/Export versión 1 (WAImportExportV1.zip), para blobs, en el sistema de Windows.
  1. [Descargue WAImportExport versión 1](https://www.microsoft.com/download/details.aspx?id=42659). La versión actual es 1.5.0.300.
  1. Descomprima en la carpeta predeterminada `waimportexportv1`. Por ejemplo, `C:\WaImportExportV1`.

2. Abra una ventana de PowerShell o de línea de comandos con privilegios administrativos. Para cambiar el directorio a la carpeta descomprimida, ejecute el siguiente comando:

   `cd C:\WaImportExportV1`

3. Para comprobar el número de discos necesarios para los blobs seleccionados, ejecute el siguiente comando:

   `WAImportExport.exe PreviewExport /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Los parámetros se describen en la tabla siguiente:

    |Parámetro de línea de comandos|Descripción|
    |--------------------------|-----------------|
    |**/logdir:**|Opcional. Directorio de registro. Los archivos de registro detallados se escriben en este directorio. Si no se especifica, se usa el directorio actual como directorio de registro.|
    |**/ExportBlobListFile:**|Necesario. Ruta de acceso al archivo XML que contiene una lista de rutas de acceso de blob o prefijos de ruta de acceso para los blobs que se van a exportar. El formato de archivo usado en el elemento `BlobListBlobPath` de la operación [Put Job](/rest/api/storageimportexport/jobs) de la API de REST del servicio Import/Export.|
    |**/DriveSize:**|Necesario. El tamaño de las unidades que se van a usar para un trabajo de exportación, *por ejemplo*, 500 GB o 1,5 TB.|

    Vea el [Ejemplo del comando PreviewExport](#example-of-previewexport-command).

4. Compruebe que puede leer o escribir en las unidades de disco que se van a enviar para el trabajo de exportación.

## <a name="example-of-previewexport-command"></a>Ejemplo del comando PreviewExport

En el siguiente ejemplo se muestra el comando `PreviewExport`:

```powershell
    WAImportExport.exe PreviewExport /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

El archivo de lista de blobs de exportación puede contener nombres de blob y prefijos de blob, como se muestra aquí:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

### <a name="examples-of-valid-blob-paths"></a>Ejemplos de rutas de acceso del blob válidas

En la siguiente tabla se muestran ejemplos de rutas de acceso del blob y prefijos de ruta de acceso de blob válidos para usar con las etiquetas &lt;BlobPath&gt; y &lt;BlobPathPrefix&gt; en el archivo de lista de blobs.

* Una *ruta de acceso* selecciona y filtra un único blob o archivo.
* Un *prefijo* selecciona y filtra varios blobs o archivos.

   | Selector | Prefijo o ruta de acceso de blob | Descripción |
   | --- | --- | --- |
   | Empieza por |/ |Exporta todos los blobs de la cuenta de almacenamiento. |
   | Empieza por |/$root/ |Exporta todos los blobs del contenedor raíz. |
   | Empieza por |/book |Exporta todos los blobs de cualquier contenedor que empiecen por el prefijo **book** |
   | Empieza por |/music/ |Exporta todos los blobs del contenedor **music** |
   | Empieza por |/music/love |Exporta todos los blobs del contenedor **music** que empiecen por el prefijo **love**. |
   | Igual a |$root/logo.bmp |Exporta el blob **logo.bmp** del contenedor raíz. |
   | Igual a |videos/story.mp4 |Exporta el blob **story.mp4** del contenedor **videos** |


### <a name="sample-output"></a>Salida de ejemplo

Este es un ejemplo de la salida, con los registros informativos omitidos:

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="next-steps"></a>Pasos siguientes

- [Exportación de blobs desde Azure Blob Storage](storage-import-export-data-from-blobs.md)
