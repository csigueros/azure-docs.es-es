---
title: 'Uso de las API estándar de DICOMweb &trade; con cURL: API de Azure Healthcare'
description: En este tutorial, aprenderá a usar las API estándar de DICOMweb con cURL.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: aersoy
ms.openlocfilehash: afa4b294b71a3cde198001d204d4670cf8ca1ec1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787786"
---
# <a name="using-dicomwebtrade-standard-apis-with-curl"></a>Uso de LAS API estándar &trade; dicomweb con cURL

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este tutorial se usa cURL para demostrar el trabajo con el servicio DICOM.

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

>[!NOTE]
>Cada uno de estos archivos representa una instancia única y forma parte del mismo estudio. Además, el cuadrado verde y el triángulo rojo forman parte de la misma serie, mientras que el círculo azul está en una serie independiente.

## <a name="prerequisites"></a>Requisitos previos

Para usar las API de DICOMWeb Standard, debe tener implementada una &trade; instancia del servicio DICOM. Si aún no ha implementado una instancia del servicio DICOM, consulte [Deploy DICOM service using the Azure Portal](deploy-dicom-services-in-azure.md).

Una vez que haya implementado una instancia del servicio DICOM, recupere la dirección URL de app service:

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com/).
2. Busque **Recursos recientes y** seleccione la instancia del servicio DICOM.
3. Copie la **dirección URL del** servicio DICOM. 
4. Si aún no ha obtenido un token, consulte Obtención del token de acceso para [el servicio DICOM mediante CLI de Azure](dicom-get-access-token-azure-cli.md). 

Para este código, accederemos a un servicio de Azure de versión preliminar pública. Es importante que no cargue ninguna información de mantenimiento privado (PHI).


## <a name="working-with-the-dicom-service"></a>Trabajar con el servicio DICOM
 
El estándar DICOMweb &trade; hace un uso pesado de las solicitudes HTTP `multipart/related` combinadas con encabezados de aceptación específicos de DICOM. A los desarrolladores familiarizados con otras API basadas en REST les resulta difícil trabajar con DICOMweb &trade; Standard. Sin embargo, una vez que lo tenga en funcionamiento, es fácil de usar. Solo se necesita un poco de familiaridad para empezar.

Los comandos cURL contienen al menos una y, a veces, dos variables que se deben reemplazar. Para simplificar la ejecución de los comandos, busque y reemplace las siguientes variables reemplazándolos por sus valores específicos:

* {Dirección URL del servicio} Esta es la dirección URL para acceder al servicio DICOM que aprovisionó en el Azure Portal, por ejemplo, ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` . Asegúrese de especificar la versión como parte de la dirección URL al realizar solicitudes. Para obtener más información, consulte [Control de versiones de API para el servicio DICOM](api-versioning-dicom-service.md).
* {path-to-dicoms}: la ruta de acceso al directorio que contiene el archivo red-triangle.dcm, como `C:/dicom-server/docs/dcms`
    * Asegúrese de usar barras diagonales como separadores y finalice el directorio _sin_ una barra diagonal final.


## <a name="uploading-dicom-instances-stow"></a>Carga de instancias DICOM (STOW)

### <a name="store-instances-using-multipartrelated"></a>Store-instances-using-multipart/related

Esta solicitud pretende demostrar cómo cargar archivos DICOM mediante varias partes o relacionadas. 

>[!NOTE]
>El servicio DICOM es más sensible que el estándar DICOM. Sin embargo, en el ejemplo siguiente se muestra una solicitud POST que cumple estrechamente con el estándar.

_Detalles:_

* Camino:.. /studies
* Método: POST
* Encabezados:
    * Accept: application/dicom+json
    * Content-Type: multipart/related; type="application/dicom"
    * Autorización: Portador {valor de token}
* Cuerpo:
    * Content-Type: application/dicom para cada archivo cargado, separado por un valor de límite

Algunos lenguajes de programación y herramientas se comportan de manera diferente. Por ejemplo, algunos requieren que defina su propio límite. Para ello, es posible que tenga que usar un encabezado Content-Type ligeramente modificado. Los siguientes se han usado correctamente.
* Content-Type: multipart/related; type="application/dicom"; boundary=ABCD1234
* Content-Type: multipart/related; boundary=ABCD1234
* Content-Type: multipart/related

```
curl --location --request POST "{Service URL}/v{version}/studies"
--header "Accept: application/dicom+json"
--header "Content-Type: multipart/related; type=\"application/dicom\""
--header "Authorization: Bearer {token value}"
--form "file1=@{path-to-dicoms}/red-triangle.dcm;type=application/dicom"
--trace-ascii "trace.txt"
```

### <a name="store-instances-for-a-specific-study"></a>Almacenamiento de instancias para un estudio específico

Esta solicitud muestra cómo cargar archivos DICOM mediante varias partes o relacionadas con un estudio designado.

_Detalles:_
* Camino:.. /studies/{study}
* Método: POST
* Encabezados:
    * Accept: application/dicom+json
    * Content-Type: multipart/related; type="application/dicom"
    * Autorización: Portador {valor de token}
* Cuerpo:
    * Content-Type: application/dicom para cada archivo cargado, separado por un valor de límite

Algunos lenguajes de programación y herramientas se comportan de manera diferente. Por ejemplo, algunos requieren que defina su propio límite. Para ello, es posible que tenga que usar un encabezado Content-Type ligeramente modificado. Los siguientes se han usado correctamente.

 * Content-Type: multipart/related; type="application/dicom"; boundary=ABCD1234
 * Content-Type: multipart/related; boundary=ABCD1234
 * Content-Type: multipart/related

```
curl --request POST "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: application/dicom+json"
--header "Content-Type: multipart/related; type=\"application/dicom\"
--header "Authorization: Bearer {token value}"
--form "file1=@{path-to-dicoms}/blue-circle.dcm;type=application/dicom"
```

### <a name="store-single-instance"></a>Store-single-instance

> [!NOTE]
> Se trata de una API no estándar que permite cargar un único archivo DICOM sin necesidad de configurar POST para varias partes o relacionadas. Aunque cURL controla bien las varias partes o relacionadas, esta API permite que herramientas como Postman carguen archivos en el servicio DICOM.

El método siguiente es necesario para cargar un único archivo DICOM.

_Detalles:_
* Camino:.. /studies
* Método: POST
* Encabezados:
   * Accept: application/dicom+json
   * Content-Type: application/dicom
   * Autorización: Portador {valor de token}
* Cuerpo:
    * Contiene un único archivo DICOM como bytes binarios.

```
curl --location --request POST "{Service URL}/v{version}/studies"
--header "Accept: application/dicom+json"
--header "Content-Type: application/dicom"
--header "Authorization: Bearer {token value}"
--data-binary "@{path-to-dicoms}/green-square.dcm"
```

## <a name="retrieving-dicom-wado"></a>Recuperación de DICOM (WADO)

### <a name="retrieve-all-instances-within-a-study"></a>Recuperación de todas las instancias de un estudio

Esta solicitud recupera todas las instancias de un solo estudio y las devuelve como una colección de bytes relacionados o de varias partes.

_Detalles:_
* Camino:.. /studies/{study}
* Método: GET
* Encabezados:
   * Accept: multipart/related; type="application/dicom"; transfer-syntax=*
   * Autorización: Portador {valor de token}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: multipart/related; type=\"application/dicom\"; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

Este comando cURL mostrará los bytes descargados en el archivo de salida (suppressWarnings.txt), pero no son archivos DICOM directos, solo una representación de texto de la descarga de varias partes o relacionadas.

### <a name="retrieve-metadata-of-all-instances-in-study"></a>Recuperación de metadatos de todas las instancias en estudio

Esta solicitud recupera los metadatos de todas las instancias de un solo estudio.

_Detalles:_
* Camino:.. /studies/{study}/metadata
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador {valor de token}

Este comando cURL mostrará los bytes descargados en el archivo de salida (suppressWarnings.txt), pero no son archivos DICOM directos, solo una representación de texto de la descarga de varias partes o relacionadas.

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="retrieve-all-instances-within-a-series"></a>Recuperación de todas las instancias de una serie

Esta solicitud recupera todas las instancias de una sola serie y las devuelve como una colección de bytes relacionados o de varias partes.

_Detalles:_
* Camino:.. /studies/{study}/series/{series}
* Método: GET
* Encabezados:
   * Accept: multipart/related; type="application/dicom"; transfer-syntax=*
   * Autorización: Portador {valor de token}

Este comando cURL mostrará los bytes descargados en el archivo de salida (suppressWarnings.txt), pero no es el archivo DICOM, solo una representación de texto de la descarga de varias partes o relacionada.

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: multipart/related; type=\"application/dicom\"; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

### <a name="retrieve-metadata-of-all-instances-within-a-series"></a>Recuperación de metadatos de todas las instancias de una serie

Esta solicitud recupera los metadatos de todas las instancias de un solo estudio.

_Detalles:_
* Camino:.. /studies/{study}/series/{series}/metadata
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador {valor de token}

```
curl --request GET "{Service URL}/v{version}/studies1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```
 
### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>Recuperación de una sola instancia dentro de una serie de un estudio

Esta solicitud recupera una instancia única y la devuelve como una secuencia de bytes con formato DICOM.

_Detalles:_
* Camino:.. /studies/{study}/series{series}/instances/{instance}
* Método: GET
* Encabezados:
   * Accept: application/dicom; transfer-syntax=*
   * Autorización: Portador {valor de token}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>Recuperación de metadatos de una sola instancia dentro de una serie de un estudio

Esta solicitud recupera los metadatos de una sola instancia dentro de un único estudio y serie.

_Detalles:_
* Camino:.. /studies/{study}/series/{series}/instances/{instance}/metadata
* Método: GET
* Encabezados:
  * Accept: application/dicom+json
  * Autorización: Portador {valor de token}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>Recuperación de uno o varios fotogramas de una sola instancia

Esta solicitud recupera uno o varios fotogramas de una sola instancia y los devuelve como una colección de bytes relacionados o de varias partes. Se pueden recuperar varios fotogramas pasando una lista separada por comas de números de fotogramas.  Todas las instancias dicom con imágenes tienen como mínimo un fotograma, que a menudo es solo la imagen asociada a la propia instancia.

_Detalles:_
* Camino:.. /studies/{study}/series{series}/instances/{instance}/frames/1,2,3
* Método: GET
* Encabezados:
   * Accept: multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1 (valor predeterminado) o
   * Accept: multipart/related; type="application/octet-stream"; transfer-syntax=* o
   * Accept: multipart/related; type="application/octet-stream";
   * Autorización: Portador {valor de token}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395/frames/1"
--header "Accept: multipart/related; type=\"application/octet-stream\"; transfer-syntax=1.2.840.10008.1.2.1"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

## <a name="query-dicom-qido"></a>Consultar DICOM (QIDO)

En los ejemplos siguientes, buscaremos elementos con sus identificadores únicos. También puede buscar otros atributos, como `PatientName` .

### <a name="search-for-studies"></a>Búsqueda de estudios

Esta solicitud permite buscar uno o varios estudios por atributos DICOM.

Para obtener más información sobre los atributos de DICOM admitidos, vea la [instrucción de conformidad de DICOM](dicom-services-conformance-statement.md).

_Detalles:_
* Camino:.. /studies? StudyInstanceUID={study}
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador {valor de token}

```
curl --request GET "{Service URL}/v{version}/studies?StudyInstanceUID=1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-series"></a>Búsqueda de series

Esta solicitud permite buscar una o varias series por atributos DICOM.

Para obtener más información sobre los atributos de DICOM admitidos, vea la [instrucción de conformidad de DICOM](dicom-services-conformance-statement.md).

_Detalles:_
* Camino:.. /series? SeriesInstanceUID={series}
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador {valor de token}

```
curl --request GET "{Service URL}/v{version}/series?SeriesInstanceUID=1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-series-within-a-study"></a>Búsqueda de series dentro de un estudio

Esta solicitud permite buscar una o varias series dentro de un solo estudio mediante atributos DICOM.

Para obtener más información sobre los atributos de DICOM admitidos, vea la [instrucción de conformidad de DICOM](dicom-services-conformance-statement.md).

_Detalles:_
* Camino:.. /studies/{study}/series? SeriesInstanceUID={series}
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador {valor de token}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series?SeriesInstanceUID=1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances"></a>Búsqueda de instancias

Esta solicitud permite buscar una o varias instancias por atributos DICOM.

Para obtener más información sobre los atributos de DICOM admitidos, vea la [instrucción de conformidad de DICOM](dicom-services-conformance-statement.md).

_Detalles:_
* Camino:.. /instances? SOPInstanceUID={instance}
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador {valor de token}

```
curl --request GET "{Service URL}/v{version}/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances-within-a-study"></a>Búsqueda de instancias dentro de un estudio

Esta solicitud permite buscar una o varias instancias dentro de un único estudio mediante atributos DICOM.

Para obtener más información sobre los atributos de DICOM admitidos, vea la [instrucción de conformidad de DICOM](dicom-services-conformance-statement.md).

_Detalles:_
* Camino:.. /studies/{study}/instances? SOPInstanceUID={instance}
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador {valor de token} 

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances-within-a-study-and-series"></a>Búsqueda de instancias dentro de un estudio y una serie

Esta solicitud permite buscar una o varias instancias dentro de un único estudio y una sola serie por atributos DICOM.

Para obtener más información sobre los atributos de DICOM admitidos, consulte la [instrucción de conformidad de DICOM.](dicom-services-conformance-statement.md)

_Detalles:_
* Camino:.. /studies/{study}/series/{series}/instances? SOPInstanceUID={instance}
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador {valor de token}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```


## <a name="delete-dicom"></a>Eliminación de DICOM 

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>Eliminación de una instancia específica dentro de un estudio y una serie

Esta solicitud elimina una sola instancia dentro de una sola serie y estudio.

La eliminación no forma parte del estándar DICOM, pero se ha agregado para mayor comodidad.

_Detalles:_
* Camino:.. /studies/{study}/series/{series}/instances/{instance}
* Método: DELETE
* Encabezados:
   * Autorización: Portador {valor de token}  

```
curl --request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Authorization: Bearer {token value}"
```

### <a name="delete-a-specific-series-within-a-study"></a>Eliminación de una serie específica dentro de un estudio

Esta solicitud elimina una sola serie (y todas las instancias secundarias) dentro de un solo estudio.

La eliminación no forma parte del estándar DICOM, pero se ha agregado para mayor comodidad.

_Detalles:_
* Camino:.. /studies/{study}/series/{series}
* Método: DELETE
* Encabezados:
   * Autorización: Portador {valor de token}

```
curl --request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Authorization: Bearer {token value}"
```

### <a name="delete-a-specific-study"></a>Eliminación de un estudio específico

Esta solicitud elimina un único estudio (y todas las series e instancias secundarias).

La eliminación no forma parte del estándar DICOM, pero se ha agregado para mayor comodidad.

_Detalles:_
* Camino:.. /studies/{study}
* Método: DELETE
* Encabezados:
   * Autorización: Portador {valor de token}

```
curl--request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498
--header "Authorization: Bearer {token value}"
```

### <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre el servicio DICOM, consulte

>[!div class="nextstepaction"]
>[Información general del servicio DICOM](dicom-services-overview.md)
