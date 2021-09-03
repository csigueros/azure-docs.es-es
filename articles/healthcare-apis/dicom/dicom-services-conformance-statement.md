---
title: Declaración de conformidad de DICOM para Azure Healthcare APIs
description: En este documento se proporcionan detalles sobre la Declaración de conformidad de DICOM para Azure Healthcare APIs.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: 8a43c48108c5714a0c11b08cf28b0fade1729596
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780650"
---
# <a name="dicom-conformance-statement"></a>Declaración de conformidad de DICOM

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

La **API de Azure para el servicio DICOM** admite un subconjunto del estándar DICOMweb&trade;. Esta compatibilidad incluye:

* [Almacenamiento (STOW-RS)](#store-stow-rs)
* [Recuperación (WADO-RS)](#retrieve-wado-rs)
* [Búsqueda (QIDO-RS)](#search-qido-rs)

Además, se admiten las API no estándar siguientes:

- [Eliminar](#delete)

El servicio también usa el control de versiones de API REST. Para información sobre cómo especificar la versión al hacer solicitudes, consulte la [documentación sobre el control de versiones de API para el servicio DICOM](api-versioning-dicom-service.md).

## <a name="store-stow-rs"></a>Almacenamiento (STOW-RS)

Esta transacción usa el método POST para almacenar representaciones de estudios, series e instancias que están contenidos en la carga de la solicitud.

| Método | Ruta de acceso               | Descripción |
| :----- | :----------------- | :---------- |
| POST   | ../studies         | Almacena instancias. |
| POST   | ../studies/{estudio} | Almacena instancias para un estudio específico. |

El parámetro `study` corresponde al atributo StudyInstanceUID de DICOM. Si se especifica, cualquier instancia que no pertenezca al estudio proporcionado se rechazará con un código de advertencia `43265`.

Se admiten los encabezados `Accept` siguientes para la respuesta:

* `application/dicom+json`

Se admiten los encabezados `Content-Type` siguientes:

* `multipart/related; type="application/dicom"`
* `application/dicom`

> [!NOTE]
> El servidor **no** fuerza ni reemplaza los atributos que entran en conflicto con los datos existentes. Todos los datos se almacenarán tal como se proporcionaron.

Es necesario que los elementos DICOM siguientes estén presentes en cada archivo DICOM que se intente almacenar:

* StudyInstanceUID
* SeriesInstanceUID
* SOPInstanceUID
* SOPClassUID
* PatientID

> [!NOTE]
> Todos los identificadores deben tener entre 1 y 64 caracteres y solo pueden contener caracteres alfanuméricos o estos caracteres especiales: '.', '-'.

Cada archivo almacenado debe tener una combinación única de StudyInstanceUID, SeriesInstanceUID y SopInstanceUID. Se devolverá el código de advertencia `45070` si ya existe un archivo con los mismos identificadores.

Límite de tamaño de archivo DICOM: de manera predeterminada, hay un límite de tamaño de 2 GB para un archivo DICOM.

### <a name="store-response-status-codes"></a>Códigos de estado de respuesta de almacenamiento

| Código                         | Descripción |
| :--------------------------- | :---------- |
| 200 (OK)                     | Se almacenaron todas las instancias de SOP de la solicitud. |
| 202 (Accepted)               | Se almacenaron algunas instancias de la solicitud, pero otras generaron error. |
| 204 (No Content)             | No se proporcionó contenido en la solicitud de transacción de almacenamiento. |
| 400 (Solicitud incorrecta)            | El formato de la solicitud no era correcto. Por ejemplo, el identificador de instancia de estudio proporcionado no se ajustaba al formato UID esperado. |
| 401 (No autorizado)           | El cliente no está autenticado. |
| 403 (Prohibido)              | El usuario no está autorizado. |
| 406 (No aceptable)         | No se admite el encabezado `Accept` especificado. |
| 409 (Conflicto)               | No se almacenó ninguna de las instancias de la solicitud de transacción de almacenamiento. |
| 415 (Tipo de medio no compatible) | No se admite el `Content-Type` proporcionado. |
| 503 (Servicio no disponible)    | El servicio no está disponible o está ocupado. Vuelva a intentarlo más tarde. |

### <a name="store-response-payload"></a>Carga de la respuesta de almacenamiento

La carga de la respuesta rellenará el conjunto de datos DICOM con los elementos siguientes:

| Etiqueta          | Nombre                  | Descripción |
| :----------- | :-------------------- | :---------- |
| (0008, 1190) | RetrieveURL           | La dirección URL de recuperación del estudio si se proporcionó StudyInstanceUID en la solicitud de almacenamiento y, al menos, una instancia se almacenó correctamente. |
| (0008, 1198) | FailedSOPSequence     | Secuencia de las instancias que no se pudieron almacenar. |
| (0008, 1199) | ReferencedSOPSequence | Secuencia de las instancias almacenadas. |

Cada conjunto de datos de `FailedSOPSequence` tendrá estos elementos (si se pudiera leer el archivo DICOM que se intenta almacenar):

| Etiqueta          | Nombre                     | Descripción |
| :----------- | :----------------------- | :---------- |
| (0008, 1150) | ReferencedSOPClassUID    | Identificador único de la clase de SOP de la instancia que no se pudo almacenar. |
| (0008, 1150) | ReferencedSOPInstanceUID | Identificador único de la instancia de SOP de la instancia que no se pudo almacenar. |
| (0008, 1197) | FailureReason            | Código del motivo por el cual no se pudo almacenar esta instancia. |

Cada conjunto de datos de `ReferencedSOPSequence` tendrá estos elementos:

| Etiqueta          | Nombre                     | Descripción |
| :----------- | :----------------------- | :---------- |
| (0008, 1150) | ReferencedSOPClassUID    | Identificador único de la clase de SOP de la instancia que no se pudo almacenar. |
| (0008, 1150) | ReferencedSOPInstanceUID | Identificador único de la instancia de SOP de la instancia que no se pudo almacenar. |
| (0008, 1190) | RetrieveURL              | Dirección URL de recuperación de esta instancia en el servidor DICOM. |

A continuación se muestra una respuesta de ejemplo con el encabezado `Accept` `application/dicom+json`:

```json
{
  "00081190":
  {
    "vr":"UR",
    "Value":["http://localhost/studies/d09e8215-e1e1-4c7a-8496-b4f6641ed232"]
  },
  "00081198":
  {
    "vr":"SQ",
    "Value":
    [{
      "00081150":
      {
        "vr":"UI","Value":["cd70f89a-05bc-4dab-b6b8-1f3d2fcafeec"]
      },
      "00081155":
      {
        "vr":"UI",
        "Value":["22c35d16-11ce-43fa-8f86-90ceed6cf4e7"]
      },
      "00081197":
      {
        "vr":"US",
        "Value":[43265]
      }
    }]
  },
  "00081199":
  {
    "vr":"SQ",
    "Value":
    [{
      "00081150":
      {
        "vr":"UI",
        "Value":["d246deb5-18c8-4336-a591-aeb6f8596664"]
      },
      "00081155":
      {
        "vr":"UI",
        "Value":["4a858cbb-a71f-4c01-b9b5-85f88b031365"]
      },
      "00081190":
      {
        "vr":"UR",
        "Value":["http://localhost/studies/d09e8215-e1e1-4c7a-8496-b4f6641ed232/series/8c4915f5-cc54-4e50-aa1f-9b06f6e58485/instances/4a858cbb-a71f-4c01-b9b5-85f88b031365"]
      }
    }]
  }
}
```

### <a name="store-failure-reason-codes"></a>Códigos de motivo de error de almacenamiento

| Código  | Descripción |
| :---- | :---------- |
| 272   | La transacción de almacenamiento no almacenó la instancia debido a un error general en el procesamiento de la operación. |
| 43264 | No se pudo validar la instancia de DICOM. |
| 43265 | El valor StudyInstanceUID de la instancia proporcionada no coincidió con el valor StudyInstanceUID especificado en la solicitud de almacenamiento. |
| 45070 | Ya se almacenó una instancia de DICOM con los mismos valores StudyInstanceUID, SeriesInstanceUID y SopInstanceUID. Si desea actualizar el contenido, primero elimine esta instancia. |
| 45071 | Otro proceso está creando una instancia de DICOM, o bien se produjo un error en la creación y el proceso de limpieza todavía no se ejecutó. Primero elimine esta instancia antes de volver a intentar la creación. |

## <a name="retrieve-wado-rs"></a>Recuperación (WADO-RS)

Esta transacción de recuperación ofrece compatibilidad para recuperar estudios, series, instancias y fotogramas almacenados por referencia.

| Método | Ruta de acceso                                                                    | Descripción |
| :----- | :---------------------------------------------------------------------- | :---------- |
| GET    | ../studies/{estudio}                                                      | Recupera todas las instancias de un estudio. |
| GET    | ../studies/{estudio}/metadata                                             | Recupera los metadatos de todas las instancias de un estudio. |
| GET    | ../studies/{estudio}/series/{serie}                                      | Recupera todas las instancias de una serie. |
| GET    | ../studies/{estudio}/series/{serie}/metadata                             | Recupera los metadatos de todas las instancias de una serie. |
| GET    | ../studies/{estudio}/series/{serie}/instances/{instancia}                 | Recupera una instancia única. |
| GET    | ../studies/{estudio}/series/{serie}/instances/{instancia}/metadata        | Recupera los metadatos de una sola instancia. |
| GET    | ../studies/{estudio}/series/{serie}/instances/{instancia}/frames/{fotogramas} | Recupera uno o varios fotogramas de una sola instancia. Si desea especificar más de un fotograma, use una coma para separada cada fotograma que se va a devolver. Por ejemplo, /studies/1/series/2/instance/3/frames/4,5,6 |

### <a name="retrieve-instances-within-study-or-series"></a>Recuperación de instancias de un estudio o una serie

Se admiten los encabezados `Accept` siguientes para recuperar instancias de un estudio o una serie:


* `multipart/related; type="application/dicom"; transfer-syntax=*`
* `multipart/related; type="application/dicom";` (cuando no se especifica la sintaxis de transferencia, 1.2.840.10008.1.2.1 se utiliza como valor predeterminado)
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-an-instance"></a>Recuperación de una instancia

Se admiten los encabezados `Accept` siguientes para recuperar una instancia específica:

* `application/dicom; transfer-syntax=*`
* `multipart/related; type="application/dicom"; transfer-syntax=*`
* `application/dicom;` (cuando no se especifica la sintaxis de transferencia, 1.2.840.10008.1.2.1 se utiliza como valor predeterminado)
* `multipart/related; type="application/dicom"` (cuando no se especifica la sintaxis de transferencia, 1.2.840.10008.1.2.1 se utiliza como valor predeterminado)
* `application/dicom; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.1`
* `application/dicom; transfer-syntax=1.2.840.10008.1.2.4.90`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-frames"></a>Recuperación de fotogramas

Se admiten los encabezados `Accept` siguientes para recuperar fotogramas:

* `multipart/related; type="application/octet-stream"; transfer-syntax=*`
* `multipart/related; type="application/octet-stream";` (cuando no se especifica la sintaxis de transferencia, 1.2.840.10008.1.2.1 se utiliza como valor predeterminado)
* `multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="image/jp2";` (cuando no se especifica la sintaxis de transferencia, 1.2.840.10008.1.2.4.90 se utiliza como valor predeterminado)
* `multipart/related; type="image/jp2";transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-transfer-syntax"></a>Recuperación de la sintaxis de transferencia

Cuando la sintaxis de transferencia solicitada es diferente del archivo original, el archivo original se transcodifica a la sintaxis de transferencia solicitada. El archivo original debe tener uno de los formatos siguientes para que la transcodificación se pueda realizar correctamente. De lo contrario, la transcodificación puede generar un error:

* 1.2.840.10008.1.2 (Little Endian Implicit)
* 1.2.840.10008.1.2.1 (Little Endian Explicit)
* 1.2.840.10008.1.2.2 (Explicit VR Big Endian)
* 1.2.840.10008.1.2.4.50 (JPEG Baseline Process 1)
* 1.2.840.10008.1.2.4.57 (JPEG Lossless)
* 1.2.840.10008.1.2.4.70 (JPEG Lossless Selection Value 1)
* 1.2.840.10008.1.2.4.90 (JPEG 2000 Lossless Only)
* 1.2.840.10008.1.2.4.91 (JPEG 2000)
* 1.2.840.10008.1.2.5 (RLE Lossless)

Un valor `transfer-syntax` no compatible generará `406 Not Acceptable`.

### <a name="retrieve-metadata-for-study-series-or-instance"></a>Recuperación de metadatos (para estudio, serie o instancia)

Se admiten los encabezados `Accept` para la recuperación de metadatos para un estudio, una serie o una instancia:

* `application/dicom+json`

La recuperación de metadatos no devolverá atributos con las representaciones de valor siguientes:

| Nombre de VR | Descripción            |
| :------ | :--------------------- |
| OB      | Other Byte             |
| OD      | Other Double           |
| OF      | Other Float            |
| OL      | Other Long             |
| OV      | Other 64-Bit Very Long |
| OW      | Other Word             |
| UN      | Unknown                |

### <a name="retrieve-metadata-cache-validation-for-study-series-or-instance"></a>Recuperación de validación de caché de metadatos para (estudio, serie o instancia)

La validación de caché se admite mediante el mecanismo `ETag`. En la respuesta a una solicitud de metadatos, se devuelve ETag como uno de los encabezados. Esta ETag se puede almacenar en caché y agregar como encabezado `If-None-Match` en las solicitudes posteriores para los mismos metadatos. Si los datos existen, hay dos tipos de respuestas posibles:

* No se han modificado los datos desde la última solicitud. La respuesta HTTP 304 (No modificado) se enviará sin cuerpo de respuesta.
* Los datos han cambiado desde la última solicitud. La respuesta HTTP 200 (Correcto) se enviará con la ETag actualizada. Los datos necesarios también se devolverán como parte del cuerpo.

### <a name="retrieve-response-status-codes"></a>Códigos de estado de respuesta de recuperación

| Código                         | Descripción |
| :--------------------------- | :---------- |
| 200 (OK)                     | Se recuperaron todos los datos solicitados. |
| 304 (No modificado)           | Los datos solicitados no se han modificado desde la última solicitud. En tal caso, el contenido no se agrega al cuerpo de la respuesta. Para más información, consulte la sección anterior **Recuperación de validación de caché de metadatos para (estudio, serie o instancia)** . |
| 400 (Solicitud incorrecta)            | El formato de la solicitud no era correcto. Por ejemplo, el identificador de la instancia de estudio que se proporcionó no se ajustaba al formato UID esperado o no se admite la codificación de sintaxis de transferencia solicitada. |
| 401 (No autorizado)           | El cliente no está autenticado. |
| 403 (Prohibido)              | El usuario no está autorizado. |
| 404 (No encontrado)              | No se pudo encontrar el recurso DICOM especificado. |
| 406 (No aceptable)         | No se admite el encabezado `Accept` especificado. |
| 503 (Servicio no disponible)    | El servicio no está disponible o está ocupado. Vuelva a intentarlo más tarde. |

## <a name="search-qido-rs"></a>Búsqueda (QIDO-RS)

La consulta basada en identificador para los objetos DICOM (QIDO) permite buscar estudios, series e instancias por atributos.

| Método | Ruta de acceso                                            | Descripción                       |
| :----- | :---------------------------------------------- | :-------------------------------- |
| *Búsqueda de estudios*                                                                         |
| GET    | ../studies?...                                  | Búsqueda de estudios                |
| *Búsqueda de series*                                                                          |
| GET    | ../series?...                                   | Búsqueda de series                 |
| GET    |../studies/{estudio}/series?...                    | Búsqueda de series en un estudio      |
| *Búsqueda de instancias*                                                                       |
| GET    |../instances?...                                 | Búsqueda de instancias              |
| GET    |../studies/{estudio}/instances?...                 | Búsqueda de instancias en un estudio   |
| GET    |../studies/{estudio}/series/{serie}/instances?... | Búsqueda de instancias en una serie  |

Se admiten los encabezados `Accept` siguientes para realizar la búsqueda:

- `application/dicom+json`

### <a name="supported-search-parameters"></a>Parámetros de búsqueda admitidos

Se admiten los parámetros siguientes para cada consulta:

| Clave              | Valor de soporte              | Número permitido | Descripción |
| :--------------- | :---------------------------- | :------------ | :---------- |
| `{attributeID}=` | {valor}                       | 0…N         | Busque la coincidencia de atributo y valor en la consulta. |
| `includefield=`  | `{attributeID}`<br/>`all`   | 0…N         | Atributos adicionales que se devolverán en la respuesta. Se admiten las etiquetas públicas y las privadas.<br/>Cuando se proporciona `all`. Consulte [Respuesta de la búsqueda](#search-response) para más información sobre los atributos que se van a devolver para cada tipo de consulta.<br/>Si se proporciona una combinación de {attributeID} y "all", el servidor usará "all" de manera predeterminada. |
| `limit=`         | {valor}                       | 0..1          | Valor entero para limitar el número de valores que se devuelven en la respuesta.<br/>El valor puede estar en el intervalo 1 >= x <= 200. El valor predeterminado es 100. |
| `offset=`        | {valor}                       | 0..1          | Omita los resultados de {value}.<br/>Si se proporciona un desplazamiento mayor que el número de resultados de la consulta de búsqueda, se devolverá una respuesta 204 (sin contenido). |
| `fuzzymatching=` | `true` \| `false`             | 0..1          | Si el valor es "true", se aplica la coincidencia aproximada verdadera al atributo PatientName. La coincidencia de palabra de prefijo se realizará con cualquier parte del nombre en el valor PatientName. Por ejemplo, si el valor PatientName es "John^Doe", entonces coincidirán "joh", "do", "jo do", "Doe" y "John Doe". Sin embargo, "ohn" no será una coincidencia. |

#### <a name="searchable-attributes"></a>Atributos que se pueden buscar

Es posible buscar los atributos y tipos de búsqueda siguientes.

| Palabra clave de atributo | Estudio | Serie | Instancia |
| :---------------- | :---: | :----: | :------: |
| StudyInstanceUID | X | X | X |
| PatientName | X | X | X |
| PatientID | X | X | X |
| AccessionNumber | X | X | X |
| ReferringPhysicianName | X | X | X |
| StudyDate | X | X | X |
| StudyDescription | X | X | X |
| SeriesInstanceUID |  | X | X |
| Modality |  | X | X |
| PerformedProcedureStepStartDate |  | X | X |
| SOPInstanceUID |  |  | X |

#### <a name="search-matching"></a>Coincidencias de búsqueda

Se admiten estos tipos de coincidencia.

| Tipo de búsqueda | Atributo admitido | Ejemplo |
| :---------- | :------------------ | :------ |
| Consulta por rango | StudyDate | {attributeID}={value1}-{value2}. Para los valores de fecha y hora, se admite un intervalo inclusivo en la etiqueta. Se asignará a `attributeID >= {value1} AND attributeID <= {value2}`. |
| Coincidencia exacta | Todos los atributos admitidos | {attributeID}={value1} |
| Coincidencia aproximada | PatientName | Coincide con cualquier componente del nombre del paciente que comienza con el valor en cuestión. |

#### <a name="attribute-id"></a>Identificador de atributo

Las etiquetas se pueden codificar de muchas maneras para el parámetro de consulta. Implementamos parcialmente el estándar tal como se define en [PS3.18 6.7.1.1.1](http://dicom.nema.org/medical/dicom/2019a/output/chtml/part18/sect_6.7.html#sect_6.7.1.1.1). Se admiten estas codificaciones para una etiqueta:

| Valor            | Ejemplo          |
| :--------------- | :--------------- |
| {group}{element} | 0020000D         |
| {dicomKeyword}   | StudyInstanceUID |

Consulta de ejemplo de búsqueda de instancias: **../instances?Modality=CT&00280011=512&includefield=00280010&limit=5&offset=0**

### <a name="search-response"></a>Respuesta de la búsqueda

La respuesta será una matriz de conjuntos de datos de DICOM. En función del recurso, los atributos siguientes se devuelven de manera *predeterminada*:

#### <a name="default-study-tags"></a>Etiquetas de estudio predeterminadas

| Etiqueta          | Nombre del atributo |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0020) | StudyDate |
| (0008, 0030) | StudyTime |
| (0008, 0050) | AccessionNumber |
| (0008, 0056) | InstanceAvailability |
| (0009, 0090) | ReferringPhysicianName |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0010, 0010) | PatientName |
| (0010, 0020) | PatientID |
| (0010, 0030) | PatientBirthDate |
| (0010, 0040) | PatientSex |
| (0020, 0010) | StudyID |
| (0020, 000D) | StudyInstanceUID |

#### <a name="default-series-tags"></a>Etiquetas de serie predeterminadas

| Etiqueta          | Nombre del atributo |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0060) | Modality |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0008, 103E) | SeriesDescription |
| (0020, 000E) | SeriesInstanceUID |
| (0040, 0244) | PerformedProcedureStepStartDate |
| (0040, 0245) | PerformedProcedureStepStartTime |
| (0040, 0275) | RequestAttributesSequence |

#### <a name="default-instance-tags"></a>Etiquetas de instancia predeterminadas

| Etiqueta          | Nombre del atributo |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0016) | SOPClassUID |
| (0008, 0018) | SOPInstanceUID |
| (0008, 0056) | InstanceAvailability |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0020, 0013) | NúmeroInstancia |
| (0028, 0010) | Filas |
| (0028, 0011) | Columnas |
| (0028, 0100) | BitsAllocated |
| (0028, 0008) | NumberOfFrames |

Si includefield=all, los atributos siguientes se incluyen junto con los atributos predeterminados. Junto con los atributos predeterminados, esta es la lista completa de atributos admitidos en cada nivel de recurso.

#### <a name="other-study-tags"></a>Otras etiquetas de estudio

| Etiqueta          | Nombre del atributo |
| :----------- | :------------- |
| (0008, 1030) | StudyDescription |
| (0008, 0063) | AnatomicRegionsInStudyCodeSequence |
| (0008, 1032) | ProcedureCodeSequence |
| (0008, 1060) | NameOfPhysiciansReadingStudy |
| (0008, 1080) | AdmittingDiagnosesDescription |
| (0008, 1110) | ReferencedStudySequence |
| (0010, 1010) | PatientAge |
| (0010, 1020) | PatientSize |
| (0010, 1030) | PatientWeight |
| (0010, 2180) | Occupation |
| (0010, 21B0) | AdditionalPatientHistory |

#### <a name="other-series-tags"></a>Otras etiquetas de serie

| Etiqueta          | Nombre del atributo |
| :----------- | :------------- |
| (0020, 0011) | SeriesNumber |
| (0020, 0060) | Laterality |
| (0008, 0021) | SeriesDate |
| (0008, 0031) | SeriesTime |

Se devuelven los atributos siguientes:

* Todos los parámetros de consulta y las UID de la dirección URL del recurso coincidentes.
* Atributos IncludeField admitidos en ese nivel de recurso. 
* Si el recurso de destino es Todas las series, también se devuelven los atributos en el nivel de estudio.
* Si el recurso de destino es Todas las instancias, también se devuelven los atributos en el nivel de estudio y serie.
* Si el recurso de destino es Instancias del estudio, también se devuelven los atributos en el nivel de serie.

### <a name="search-response-codes"></a>Códigos de respuesta de búsqueda

La API de consulta devuelve uno de los códigos de estado siguientes en la respuesta:

| Código                      | Descripción |
| :------------------------ | :---------- |
| 200 (OK)                  | La carga de respuesta contiene todos los recursos correspondientes. |
| 204 (No Content)          | La búsqueda se completó correctamente, pero no devolvió ningún resultado. |
| 400 (Solicitud incorrecta)         | El servidor no pudo realizar la consulta porque el componente de la consulta no era válido. El cuerpo de la respuesta contiene detalles del error. |
| 401 (No autorizado)        | El cliente no está autenticado. |
| 403 (Prohibido)           | El usuario no está autorizado. |
| 503 (Servicio no disponible) | El servicio no está disponible o está ocupado. Vuelva a intentarlo más tarde. |

### <a name="extra-notes"></a>Notas adicionales

* No se admiten las consultas con `TimezoneOffsetFromUTC` (`00080201`).
* La API de consulta no devolverá 413 (entidad de solicitud demasiado grande). Si el límite de respuesta de consulta solicitado está fuera del intervalo aceptable, se devolverá una solicitud no correcta. Se resolverá todo lo que se solicite dentro del intervalo aceptable.
* Cuando el recurso de destino es estudio o serie, existe la posibilidad de metadatos incoherentes en el nivel de estudio o serie en varias instancias. Por ejemplo, dos instancias podrían tener un valor patientName distinto. En este caso, la versión más reciente ganará y solo puede buscar en los datos más recientes.
* Los resultados paginados están optimizados para devolver primero la instancia coincidente *más reciente*. Esto puede dar lugar a registros duplicados en páginas posteriores si se agregaron datos más recientes que coincidan con la consulta.
* La coincidencia distingue mayúsculas de minúsculas y acentos en los tipos de VR PN.
* La coincidencia no distingue mayúsculas de minúsculas ni acentos en otros tipos de VR.

## <a name="delete"></a>Eliminar

Esta transacción no forma parte del estándar oficial de DICOMweb&trade;. Usa el método DELETE para quitar representaciones de estudios, series e instancias del almacén.

| Método | Ruta de acceso                                                    | Descripción |
| :----- | :------------------------------------------------------ | :---------- |
| Delete | ../studies/{estudio}                                      | Elimina todas las instancias de un estudio específico. |
| Delete | ../studies/{estudio}/series/{serie}                      | Elimina todas las instancias de una serie específica de un estudio. |
| Delete | ../studies/{estudio}/series/{serie}/instances/{instancia} | Elimine una instancia específica de una serie. |

Los parámetros `study`, `series` y `instance` corresponden a los atributos StudyInstanceUID, SeriesInstanceUID y SopInstanceUID de DICOM, respectivamente.

No hay restricciones en el encabezado `Accept`, el encabezado `Content-Type` ni el contenido del cuerpo de la solicitud.

> [!NOTE]
> Después de una transacción de eliminación, las instancias eliminadas no se podrán recuperar.

### <a name="response-status-codes"></a>Códigos de estado de respuesta

| Código                         | Descripción |
| :--------------------------- | :---------- |
| 204 (No Content)             | Cuando se eliminaron todas las instancias de SOP. |
| 400 (Solicitud incorrecta)            | El formato de la solicitud no era correcto. |
| 401 (No autorizado)           | El cliente no está autenticado. |
| 403 (Prohibido)              | El usuario no está autorizado. |
| 404 (No encontrado)              | Cuando no se encontró la serie especificada en un estudio o no se encontró la instancia especificada dentro de la serie. |
| 503 (Servicio no disponible)    | El servicio no está disponible o está ocupado. Vuelva a intentarlo más tarde. |

### <a name="delete-response-payload"></a>Carga de la respuesta de eliminación

El cuerpo de la respuesta estará vacío. El código de estado es la única información útil que se devuelve.

### <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre el servicio DICOM, consulte 

>[!div class="nextstepaction"]
>[Información general del servicio DICOM](dicom-services-overview.md)
