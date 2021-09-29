---
title: Uso del URI de Azure Data Lake Storage Gen2
description: Conozca la sintaxis del URI del identificador de esquema de ABFS, que representa el controlador de Azure Blob File System (controlador del sistema de archivos de Hadoop para Azure Data Lake Storage Gen2).
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: af31e49228f655d5b16cc20fdc07cd9a7da597d6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620224"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Uso del URI de Azure Data Lake Storage Gen2

El controlador [Hadoop Filesystem](https://www.aosabook.org/en/hdfs.html), que es compatible con Azure Data Lake Storage Gen2, se conoce por su identificador de esquema `abfs` (sistema de archivos de blob de Azure). El controlador ABFS es coherente con otros controladores del sistema de archivos de Hadoop y emplea un formato URI para dirigir archivos y directorios dentro de una cuenta compatible con Data Lake Storage Gen2.

## <a name="uri-syntax"></a>Sintaxis de URI

La sintaxis del URI de Data Lake Storage Gen2 depende de si la cuenta de almacenamiento está configurada para tener Data Lake Storage Gen2 como sistema de archivos predeterminado.

Si la cuenta compatible con Data Lake Storage Gen2 que quiere dirigir **no está** configurada como el sistema de archivos predeterminado durante la creación de la cuenta, la sintaxis abreviada del URI es la siguiente:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identificador de esquema**: el protocolo `abfs` se usa como el identificador de esquema. Si agrega una "s" al final (abfs<b><i>s</i></b>), el controlador cliente de Hadoop ABFS <i>siempre</i> usará Seguridad de la capa de transporte (TLS) independientemente del método de autenticación elegido. Si elige OAuth como método de autenticación, el controlador cliente siempre usará TLS aunque especifique "abfs" en lugar de "abfss", porque OAuth solo se basa en la capa TLS. Por último, si decide usar el método anterior de clave de cuenta de almacenamiento, el controlador cliente interpretará "abfs" para indicar que no desea usar TLS.

2. **Sistema de archivos**: ubicación principal que contiene los archivos y las carpetas. Es la misma que la de los contenedores del servicio de Azure Storage Blob.

3. **Nombre de cuenta**: nombre dado a la cuenta de almacenamiento durante la creación.

4. **Rutas de acceso**: representación de barra diagonal delimitada (`/`) de la estructura del directorio.

5. **Nombre de archivo**: nombre del archivo individual. Este parámetro es opcional si va a dirigirse a un directorio.

Sin embargo, si la cuenta que quiere dirigir está configurada como el sistema de archivos predeterminado durante la creación de la cuenta, la sintaxis abreviada del URI es la siguiente:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Ruta de acceso**: representación de barra diagonal delimitada (`/`) de la estructura del directorio.

2. **Nombre de archivo**: nombre del archivo individual.

## <a name="next-steps"></a>Pasos siguientes

- [Uso de Data Lake Storage Gen2 con clústeres de Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
