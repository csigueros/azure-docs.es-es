---
title: Archivo de inclusión
description: archivo de inclusión
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/19/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0914cf9515930e23e4134181ffe8332e36eacffe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612961"
---
Azure Blob Storage es la solución de almacenamiento de objetos de Microsoft para la nube. El almacenamiento de blobs está optimizado para almacenar grandes cantidades de datos no estructurados. Los datos no estructurados son datos que no se ciñen a ningún un modelo de datos o definición concretos, como texto o datos binarios.

## <a name="about-blob-storage"></a>Acerca de Blob Storage

Blob Storage está diseñado para:

* Visualización de imágenes o documentos directamente en un explorador.
* Almacenamiento de archivos para acceso distribuido.
* Streaming de audio y vídeo.
* Escribir en archivos de registro.
* Almacenamiento de datos para copia de seguridad y restauración, recuperación ante desastres y archivado.
* Almacenamiento de datos para el análisis en local o en un servicio hospedado de Azure.

Los usuarios o las aplicaciones cliente pueden acceder a objetos en Blob Storage a través de HTTP/HTTPS, desde cualquier lugar del mundo. Se puede acceder a los objetos de Blob Storage mediante la [API REST de Azure Storage](/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](/powershell/module/az.storage), la [CLI de Azure](/cli/azure/storage) o una biblioteca de cliente de Azure Storage. Hay bibliotecas de cliente disponibles para distintos lenguajes, entre los que se incluyen:

* [.NET](/dotnet/api/overview/azure/storage)
* [Java](/java/api/overview/azure/storage)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](../articles/storage/blobs/storage-quickstart-blobs-python.md)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Acerca de Azure Data Lake Storage Gen2

Blob Storage es compatible con Azure Data Lake Storage Gen2, solución de análisis de macrodatos empresarial de Microsoft para la nube. Azure Data Lake Storage Gen2 no solo ofrece un sistema de archivos jerárquico, sino también las ventajas de Blob Storage, entre las que se incluyen:

* Almacenamiento en niveles de bajo costo
* Alta disponibilidad
* Coherencia fuerte
* Funcionalidades de recuperación ante desastres

Para más información acerca de Data Lake Storage Gen2, consulte [Introduction to Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) (Introducción a Azure Data Lake Storage Gen2).