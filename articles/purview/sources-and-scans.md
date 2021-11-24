---
title: Tipos de archivo y orígenes de datos admitidos
description: En este artículo se proporcionan detalles conceptuales sobre los orígenes de datos y los tipos de archivo admitidos en Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 09/27/2021
ms.custom: references_regions
ms.openlocfilehash: b29896a9ca047b05c3313b716b2a4df1b0e72f6c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517185"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Tipos de archivo y orígenes de datos admitidos en Azure Purview

En este artículo se describen los orígenes de datos, los tipos de archivo y los conceptos de examen admitidos en Purview.

## <a name="supported-data-sources"></a>Orígenes de datos admitidos

Purview admite todos los orígenes de datos enumerados [aquí](purview-connector-overview.md).

## <a name="file-types-supported-for-scanning"></a>Tipos de archivo admitidos para examen

Los siguientes tipos de archivo se admiten para los exámenes y para la extracción y clasificación de esquemas, si procede:

- Formatos de archivo estructurados admitidos por extensión: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML, GZIP
 > [!Note]
 > * El analizador de Purview solo admite la extracción de esquemas de los tipos de archivo estructurados indicados arriba.
 > * En los tipos de archivo AVRO, ORC y PARQUET, el analizador de Purview no permite la extracción de esquemas de archivos que contienen tipos de datos complejos (por ejemplo, MAP, LIST, STRUCT). 
 > * El analizador de Purview admite el examen de tipos de archivo PARQUET comprimidos con Snappy para la extracción y clasificación de esquemas. 
 > * En el caso de los tipos de archivo GZIP, el archivo GZIP debe asignarse a un único archivo csv dentro de él. 
 > Los archivos Gzip están sujetos a reglas de clasificación personalizadas y del sistema. Actualmente no se admite el examen de un archivo Gzip asignado a varios archivos o cualquier otro tipo de archivo que no sea CSV. 
- Formatos de archivo de documento admitidos por extensión: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- Purview también admite extensiones de archivo personalizadas y analizadores personalizados.

## <a name="sampling-within-a-file"></a>Muestreo en un archivo

En la terminología de Purview:
- Examen L1: extrae información básica y metadatos como el nombre de archivo, el tamaño y el nombre completo
- Examen L2: extrae el esquema de los tipos de archivo estructurados y las tablas de base de datos
- Examen L3: extrae el esquema cuando proceda y somete el archivo muestreado a reglas de clasificación personalizadas y del sistema.

En todos los formatos de archivo estructurados, el examen de Purview muestrea los archivos de la siguiente manera:

- En el caso de los tipos de archivo estructurados, muestra 128 filas de cada columna o el primer MB, lo que sea menor.
- En el caso de los formatos de archivo de documento, muestra los primeros 20 MB de cada archivo.
    - Si un archivo de documento es mayor de 20 MB, no estará sujeto a un examen profundo (sujeto a clasificación). En tal caso, Purview captura solo metadatos básicos como el nombre de archivo y el nombre completo.
- Para **orígenes de datos tabulares (SQL, CosmosDB)** , muestra las primeras 128 filas. 

## <a name="resource-set-file-sampling"></a>Muestreo de archivos del conjunto de recursos

En Purview, una carpeta o grupo de archivos de partición se detecta como un *conjunto de recursos* si coincide con una directiva de conjuntos de recursos del sistema o con una directiva de conjuntos de recursos definida por el cliente. Si se detecta un conjunto de recursos, Purview muestreará cada carpeta que contenga. Consulte [aquí](concept-resource-sets.md) más información sobre los conjuntos de recursos.

Muestreo de archivos para conjuntos de recursos por tipos de archivo:

- **Archivos delimitados (CSV, PSV, SSV, TSV)** : se muestrea uno de cada 100 archivos (examen L3) de una carpeta o grupo de archivos de partición que se considere un grupo de recursos.
- **Tipos de archivo de Data Lake (Parquet, Avro, Orc)** : se muestrea uno de cada 18446744073709551615 archivos (longitud máxima) (examen L3) de una carpeta o grupo de archivos de partición que se considere *grupo de recursos*.
- **Otros tipos de archivo estructurados (JSON, XML, TXT)** : se muestrea uno de cada 100 archivos (examen L3) de una carpeta o grupo de archivos de partición que se considere un grupo de recursos.
- **Objetos SQL y entidades de CosmosDB**: cada archivo se somete a un examen L3.
- **Tipos de archivo de documento**: cada archivo se somete a un examen L3. Los patrones de conjuntos de recursos no se aplican a estos tipos de archivo.

## <a name="classification"></a>clasificación

Las 206 reglas de clasificación del sistema se aplican a los formatos de archivo estructurados. Solo las reglas de clasificación de MCE se aplican a los tipos de archivo de documento (no los patrones de expresiones regulares nativos del examen de datos, detección basada en filtros de Bloom). Para más información sobre las clasificaciones admitidas, consulte [Clasificaciones admitidas en Azure Purview](supported-classifications.md).

## <a name="next-steps"></a>Pasos siguientes

- [Exámenes e ingesta en Purview](concept-scans-and-ingestion.md)
- [Administración de orígenes de datos en Azure Purview](manage-data-sources.md)
