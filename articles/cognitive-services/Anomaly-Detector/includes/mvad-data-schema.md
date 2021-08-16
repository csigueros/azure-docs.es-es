---
title: Esquema de datos MVAD
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: quying
manager: tonyxin
ms.service: cognitive-services
ms.topic: include
ms.date: 7/1/2021
ms.author: yingqunpku
ms.openlocfilehash: 3be9d5d50d34eee35de38b7b28dc1d42217ace24
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292633"
---
### <a name="input-data-schema"></a>Esquema de datos de entrada

MVAD detecta anomalías en un grupo de métricas; cada métrica se denomina **variable** o serie temporal.

* Puede descargar el archivo de datos de ejemplo de Microsoft para comprobar el esquema aceptado desde: [https://aka.ms/AnomalyDetector/MVADSampleData](https://aka.ms/AnomalyDetector/MVADSampleData)
* Cada variable debe tener dos, y solo dos, campos, `timestamp` y `value`, que deben almacenarse en un archivo de valores separados por comas (CSV).
* Los nombres de columna del archivo CSV deben ser exactamente `timestamp` y `value`, y distinguir mayúsculas de minúsculas.
* Los valores de `timestamp` deben cumplir la norma ISO 8601; los de `value` pueden ser números enteros o decimales con cualquier número de posiciones decimales.
    Un buen ejemplo del contenido de un archivo CSV:

    |timestamp | value|
    |-------|-------|
    |2019-04-01T00:00:00Z| 5|
    |2019-04-01T00:01:00Z| 3.6|
    |2019-04-01T00:02:00Z| 4|
    |...| ...|

    > [!NOTE]
    > Si las marcas de tiempo tienen horas, minutos o segundos, asegúrese de que se redondeen correctamente antes de llamar a las API.
    >
    > Por ejemplo, si se da por hecho que la frecuencia de los datos es un punto de datos cada 30 segundos, pero observa marcas de tiempo como "12:00:01" y "12:00:28", es señal segura de que debe procesar previamente las marcas de tiempo a nuevos valores como "12:00:00" y "12:00:30".
    >
    > Para obtener más información, vea la sección ["Redondeo de marcas de tiempo"](../concepts/best-practices-multivariate.md#timestamp-round-up) del documento de procedimientos recomendados.
* El nombre del archivo csv se va a usar como nombre de variable y debe ser único. Por ejemplo, "temperature.csv" y "humidity.csv".
* Las variables para el entrenamiento y las variables para la inferencia deben ser coherentes. Por ejemplo, si usa `series_1`, `series_2`, `series_3`, `series_4` y `series_5` para el entrenamiento, debe proporcionar exactamente las mismas variables para la inferencia.
* Los archivos CSV deben comprimirse en un archivo ZIP y cargarse en un contenedor de blobs de Azure. El archivo ZIP puede tener el nombre que desee.

#### <a name="folder-structure"></a>Estructura de carpetas

Un error común en la preparación de datos son las carpetas adicionales del archivo ZIP. Por ejemplo, imagine que el nombre del archivo ZIP es `series.zip`. Después de descomprimir los archivos en una nueva carpeta `./series`, la ruta de acceso **correcta** a los archivos CSV es `./series/series_1.csv`, y una ruta de acceso **incorrecta** podría ser `./series/foo/bar/series_1.csv`.

Ejemplo correcto del árbol de directorios después de descomprimir el archivo ZIP en Windows

```bash
.
└── series
    ├── series_1.csv
    ├── series_2.csv
    ├── series_3.csv
    ├── series_4.csv
    └── series_5.csv
```

Ejemplo incorrecto del árbol de directorios después de descomprimir el archivo ZIP en Windows

```bash
.
└── series
    └── series
        ├── series_1.csv
        ├── series_2.csv
        ├── series_3.csv
        ├── series_4.csv
        └── series_5.csv
```