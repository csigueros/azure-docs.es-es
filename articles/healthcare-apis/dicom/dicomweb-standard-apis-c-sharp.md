---
title: 'Uso de las API estándar de DICOMweb &trade; con C#: API de Azure Healthcare'
description: En este tutorial, aprenderá a usar las API estándar de DICOMweb con C#.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 08/03/2021
ms.author: aersoy
ms.openlocfilehash: 2594ff27dd7e4bae5c5463c32a5d4ee3d481cfe8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787646"
---
# <a name="using-dicomwebtrade-standard-apis-with-c"></a>Uso de las API estándar de DICOMweb &trade; con C #

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este tutorial se usa C# para demostrar cómo trabajar con el servicio DICOM.

En este tutorial, usaremos los siguientes archivos [DICOM .dcm de ejemplo.](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)

* blue-circle.dcm
* dicom-metadata.csv
* green-square.dcm
* red-triangle.dcm

El nombre de archivo, studyUID, seriesUID e instanceUID de los archivos DICOM de ejemplo es el siguiente:

| Archivo | StudyUID | SeriesUID | InstanceUID |
| --- | --- | --- | ---|
|green-square.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.12714725698140337137334606354172323212|
|red-triangle.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395|
|blue-circle.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.77033797676425927098669402985243398207|1.2.826.0.1.3680043.8.498.13273713909719068980354078852867170114|

> [!NOTE]
> Cada uno de estos archivos representa una instancia única y forma parte del mismo estudio. Además, el cuadrado verde y el triángulo rojo forman parte de la misma serie, mientras que el círculo azul está en una serie independiente.

## <a name="prerequisites"></a>Requisitos previos

Para usar las API estándar de DICOMweb, debe tener implementada una instancia &trade; del servicio DICOM. Si aún no ha implementado una instancia del servicio DICOM, consulte [Deploy DICOM service using the Azure Portal](deploy-dicom-services-in-azure.md).

Después de implementar una instancia del servicio DICOM, recupere la dirección URL de su instancia de App Service:

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com/).
1. Busque **Recursos recientes y** seleccione la instancia del servicio DICOM.
1. Copie la **dirección URL del** servicio de DICOM. Asegúrese de especificar la versión como parte de la dirección URL al realizar solicitudes. Para obtener más información, consulte [Control de versiones de API para el servicio DICOM](api-versioning-dicom-service.md).

En la aplicación, instale los siguientes NuGet paquetes:

*  [Cliente DICOM](https://microsofthealthoss.visualstudio.com/FhirServer/_packaging?_a=package&feed=Public&package=Microsoft.Health.Dicom.Client&protocolType=NuGet)

*  [fo-dicom](https://www.nuget.org/packages/fo-dicom/)

## <a name="create-a-dicomwebclient"></a>Creación de dicomWebClient

Después de implementar el servicio DICOM, creará un DicomWebClient. Ejecute el siguiente fragmento de código para crear DicomWebClient, que usaremos para el resto de este tutorial. Asegúrese de que tiene instalados NuGet paquetes como se mencionó anteriormente. Si aún no ha obtenido un token, consulte Obtención del token de acceso para el [servicio DICOM mediante CLI de Azure](dicom-get-access-token-azure-cli.md).

```c#
string webServerUrl ="{Your DicomWeb Server URL}"
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri(webServerUrl);
IDicomWebClient client = new DicomWebClient(httpClient);
client.HttpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", “{Your token value}”); 
```
Con DicomWebClient, ahora podemos realizar las operaciones Store, Retrieve, Search y Delete.

## <a name="store-dicom-instances-stow"></a>Almacenar instancias DICOM (STOW)

Con dicomWebClient que hemos creado, ahora podemos almacenar archivos DICOM.

### <a name="store-single-instance"></a>Almacenamiento de una sola instancia

Almacenar una sola instancia muestra cómo cargar un único archivo DICOM.

_Detalles:_

* POST /studies

```c#
DicomFile dicomFile = await DicomFile.OpenAsync(@"{Path To blue-circle.dcm}");
DicomWebResponse response = await client.StoreAsync(new[] { dicomFile });
```

### <a name="store-instances-for-a-specific-study"></a>Almacenamiento de instancias para un estudio específico

Las instancias de almacenamiento de un estudio específico muestran cómo cargar un archivo DICOM en un estudio especificado.

_Detalles:_

* POST /studies/{study}

```c#
DicomFile dicomFile = await DicomFile.OpenAsync(@"{Path To red-triangle.dcm}");
DicomWebResponse response = await client.StoreAsync(new[] { dicomFile }, "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420");
```

Antes de pasar a la siguiente parte del tutorial, cargue el `green-square.dcm` archivo mediante cualquiera de los métodos anteriores.

## <a name="retrieving-dicom-instances-wado"></a>Recuperación de instancias DICOM (WADO)

Los fragmentos de código siguientes mostrarán cómo realizar cada una de las consultas de recuperación mediante dicomWebClient creado anteriormente.

Las siguientes variables se usarán en el resto de los ejemplos:

```c#
string studyInstanceUid = "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"; //StudyInstanceUID for all 3 examples
string seriesInstanceUid = "1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"; //SeriesInstanceUID for green-square and red-triangle
string sopInstanceUid = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"; //SOPInstanceUID for red-triangle
```

### <a name="retrieve-all-instances-within-a-study"></a>Recuperación de todas las instancias de un estudio

Recuperar todas las instancias de un estudio recupera todas las instancias de un solo estudio.

_Detalles:_

* GET /studies/{study}

```c#
DicomWebResponse response = await client.RetrieveStudyAsync(studyInstanceUid);
```

Los tres archivos dcm que hemos cargado anteriormente forman parte del mismo estudio, por lo que la respuesta debe devolver las tres instancias. Compruebe que la respuesta tiene un código de estado correcto y que se devuelven las tres instancias.

### <a name="use-the-retrieved-instances"></a>Uso de las instancias recuperadas

El siguiente fragmento de código muestra cómo acceder a las instancias que se recuperan. También se muestra cómo acceder a algunos de los campos de las instancias y cómo guardarlos como un archivo dcm.

```c#
DicomWebAsyncEnumerableResponse<DicomFile> response = await client.RetrieveStudyAsync(studyInstanceUid);
await foreach (DicomFile file in response)
{
    string patientName = file.Dataset.GetString(DicomTag.PatientName);
    string studyId = file.Dataset.GetString(DicomTag.StudyID);
    string seriesNumber = file.Dataset.GetString(DicomTag.SeriesNumber);
    string instanceNumber = file.Dataset.GetString(DicomTag.InstanceNumber);

    file.Save($"<path_to_save>\\{patientName}{studyId}{seriesNumber}{instanceNumber}.dcm");
}
```

### <a name="retrieve-metadata-of-all-instances-in-study"></a>Recuperación de metadatos de todas las instancias en estudio

Esta respuesta recupera los metadatos de todas las instancias de un solo estudio.

_Detalles:_

* GET /studies/{study}/metadata

```c#
DicomWebResponse response = await client.RetrieveStudyMetadataAsync(studyInstanceUid);
```

Los tres archivos dcm que hemos cargado anteriormente forman parte del mismo estudio, por lo que la respuesta debe devolver los metadatos de las tres instancias. Compruebe que la respuesta tiene un código de estado correcto y que se devuelven todos los metadatos.

### <a name="retrieve-all-instances-within-a-series"></a>Recuperación de todas las instancias de una serie

Esta respuesta recupera todas las instancias de una sola serie.

_Detalles:_

* GET /studies/{study}/series/{series}


```c#
DicomWebResponse response = await client.RetrieveSeriesAsync(studyInstanceUid, seriesInstanceUid);
```

Esta serie tiene dos instancias (triángulo verde cuadrado y rojo), por lo que la respuesta debe devolver ambas instancias. Compruebe que la respuesta tiene un código de estado correcto y que se devuelven ambas instancias.

### <a name="retrieve-metadata-of-all-instances-within-a-series"></a>Recuperación de metadatos de todas las instancias de una serie

Esta respuesta recupera los metadatos de todas las instancias de un solo estudio.

_Detalles:_

* GET /studies/{study}/series/{series}/metadata

```c#
DicomWebResponse response = await client.RetrieveSeriesMetadataAsync(studyInstanceUid, seriesInstanceUid);
```

Esta serie tiene dos instancias (triángulo verde cuadrado y rojo), por lo que la respuesta debe devolver metadatos para ambas instancias. Compruebe que la respuesta tiene un código de estado correcto y que se devuelven ambas instancias de los metadatos.

### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>Recuperación de una sola instancia dentro de una serie de un estudio

Esta solicitud recupera una instancia única.

_Detalles:_

* GET /studies/{study}/series{series}/instances/{instance}

```c#
DicomWebResponse response = await client.RetrieveInstanceAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid);
```

Esta respuesta solo debe devolver el triángulo rojo de la instancia. Compruebe que la respuesta tiene un código de estado correcto y que se devuelve la instancia.

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>Recuperación de metadatos de una sola instancia dentro de una serie de un estudio

Esta solicitud recupera los metadatos de una sola instancia dentro de un único estudio y serie.

_Detalles:_

* GET /studies/{study}/series/{series}/instances/{instance}/metadata

```c#
DicomWebResponse response = await client.RetrieveInstanceMetadataAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid);
```

Esta respuesta solo debe devolver los metadatos del triángulo rojo de la instancia. Compruebe que la respuesta tiene un código de estado correcto y que se devuelven los metadatos.

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>Recuperación de uno o varios fotogramas de una sola instancia

Esta solicitud recupera uno o varios fotogramas de una sola instancia.

_Detalles:_

* GET /studies/{study}/series/{series}/instances/{instance}/frames/{frames}

```c#
DicomWebResponse response = await client.RetrieveFramesAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid, frames: new[] { 1 });

```

Esta respuesta debe devolver el único fotograma del triángulo rojo. Compruebe que la respuesta tiene un código de estado correcto y que se devuelve el marco.

## <a name="query-dicom-qido"></a>Consultar DICOM (QIDO)

> [!NOTE]
> Consulte la instrucción [de conformidad de DICOM para](dicom-services-conformance-statement.md#supported-search-parameters) ver los atributos de DICOM admitidos.

### <a name="search-for-studies"></a>Búsqueda de estudios

Esta solicitud busca uno o varios estudios por atributos DICOM.

_Detalles:_

* GET /studies? StudyInstanceUID={study}

```c#
string query = $"/studies?StudyInstanceUID={studyInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Valida que la respuesta incluye un estudio y que el código de respuesta es correcto.

### <a name="search-for-series"></a>Búsqueda de series

Esta solicitud busca una o varias series por atributos DICOM.

_Detalles:_

* GET /series? SeriesInstanceUID={series}

```c#
string query = $"/series?SeriesInstanceUID={seriesInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Valida que la respuesta incluye una serie y que el código de respuesta es Correcto.

### <a name="search-for-series-within-a-study"></a>Búsqueda de series dentro de un estudio

Esta solicitud busca una o varias series dentro de un único estudio por atributos DICOM.

_Detalles:_

* GET /studies/{study}/series? SeriesInstanceUID={series}

```c#
string query = $"/studies/{studyInstanceUid}/series?SeriesInstanceUID={seriesInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Valida que la respuesta incluye una serie y que el código de respuesta es Correcto.

### <a name="search-for-instances"></a>Búsqueda de instancias

Esta solicitud busca una o varias instancias por atributos DICOM.

_Detalles:_

* GET /instances? SOPInstanceUID={instance}

```c#
string query = $"/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Valida que la respuesta incluye una instancia y que el código de respuesta es Correcto.

### <a name="search-for-instances-within-a-study"></a>Búsqueda de instancias dentro de un estudio

Esta solicitud busca una o varias instancias dentro de un único estudio por atributos DICOM.

_Detalles:_

* GET /studies/{study}/instances? SOPInstanceUID={instance}

```c#
string query = $"/studies/{studyInstanceUid}/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Valida que la respuesta incluye una instancia y que el código de respuesta es Correcto.

### <a name="search-for-instances-within-a-study-and-series"></a>Búsqueda de instancias dentro de un estudio y una serie

Esta solicitud busca una o varias instancias dentro de un único estudio y una sola serie por atributos DICOM.

_Detalles:_

* GET /studies/{study}/series/{series}instances? SOPInstanceUID={instance}

```c#
string query = $"/studies/{studyInstanceUid}/series/{seriesInstanceUid}/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Valida que la respuesta incluye una instancia y que el código de respuesta es Correcto.

## <a name="delete-dicom"></a>Eliminación de DICOM

> [!NOTE]
> La eliminación no forma parte del estándar DICOM, pero se ha agregado para mayor comodidad.

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>Eliminación de una instancia específica dentro de un estudio y una serie

Esta solicitud elimina una sola instancia dentro de una sola serie y estudio.

_Detalles:_

* DELETE /studies/{study}/series/{series}/instances/{instance}

```c#
string sopInstanceUidRed = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395";
DicomWebResponse response = await client.DeleteInstanceAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUidRed);
```

Este repositorio elimina la instancia de triángulo rojo del servidor. Si se realiza correctamente, el código de estado de respuesta no contiene contenido.

### <a name="delete-a-specific-series-within-a-study"></a>Eliminación de una serie específica dentro de un estudio

Esta solicitud elimina una sola serie (y todas las instancias secundarias) dentro de un solo estudio.

_Detalles:_

* DELETE /studies/{study}/series/{series}

```c#
DicomWebResponse response = await client.DeleteSeriesAsync(studyInstanceUid, seriesInstanceUid);
```

Esta respuesta elimina la instancia de green-square (es el único elemento que queda en la serie) del servidor. Si se realiza correctamente, el código de estado de respuesta no contendrá contenido.

### <a name="delete-a-specific-study"></a>Eliminación de un estudio específico

Esta solicitud elimina un único estudio (y todas las series e instancias secundarias).

_Detalles:_

* DELETE /studies/{study}

```c#
DicomWebResponse response = await client.DeleteStudyAsync(studyInstanceUid);
```

Esta respuesta elimina la instancia de círculo azul (es el único elemento que queda en la serie) del servidor. Si se realiza correctamente, el código de estado de respuesta no contiene contenido.

### <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre el servicio DICOM, consulte

>[!div class="nextstepaction"]
>[Información general del servicio DICOM](dicom-services-overview.md)
