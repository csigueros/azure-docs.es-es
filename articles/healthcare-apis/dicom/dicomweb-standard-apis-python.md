---
title: 'Uso de las API estándar de DICOMweb con Python: API de Azure Healthcare'
description: En este tutorial se describe cómo usar las API estándar de DICOMweb con Python.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: aersoy
ms.openlocfilehash: c474409b2dfdbca0a921690b1871c7268bde08d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785521"
---
# <a name="using-dicomwebtrade-standard-apis-with-python"></a>Uso de LAS API de DICOMWeb &trade; Standard con Python

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este tutorial se usa Python para demostrar cómo trabajar con el servicio DICOM.

En el tutorial, usaremos los siguientes archivos [DICOM .dcm de ejemplo.](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)

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

Para usar las API de DICOMWeb Standard, debe tener implementada una instancia &trade; del servicio DICOM. Si aún no ha implementado el servicio DICOM, consulte [Deploy DICOM service using the Azure Portal](deploy-dicom-services-in-azure.md).

Después de implementar una instancia del servicio DICOM, recupere la dirección URL de su instancia de App Service:

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com/).
1. Busque **Recursos recientes y** seleccione la instancia del servicio DICOM.
1. Copie la **dirección URL del** servicio de DICOM. 
2. Si aún no ha obtenido un token, consulte Obtención del token de acceso para el servicio [DICOM mediante CLI de Azure](dicom-get-access-token-azure-cli.md). 

Para este código, accederemos a un servicio de Azure de versión preliminar pública. Es importante que no cargue ninguna información de salud privada (PHI).

## <a name="working-with-the-dicom-service"></a>Trabajo con el servicio DICOM

El estándar DICOMweb hace un uso pesado de las solicitudes &trade; `multipart/related` HTTP combinadas con encabezados de aceptación específicos de DICOM. A los desarrolladores familiarizados con otras API basadas en REST les resulta difícil trabajar con el estándar DICOMweb. &trade; Sin embargo, una vez que lo tenga en funcionamiento, es fácil de usar. Solo es necesario estar un poco familiarizado para empezar.

### <a name="import-the-appropriate-python-libraries"></a>Importación de las bibliotecas de Python adecuadas

En primer lugar, importe las bibliotecas de Python necesarias.

Hemos elegido implementar este ejemplo mediante la biblioteca `requests` sincrónica. Para obtener compatibilidad asincrónica, considere la posibilidad `httpx` de usar u otra biblioteca asincrónica. Además, vamos a importar dos funciones de soporte técnico de para `urllib3` admitir el trabajo con `multipart/related` solicitudes.

Además, vamos a importar para iniciar `DefaultAzureCredential` sesión en Azure y obtener un token.

```python
import requests
import pydicom
from pathlib import Path
from urllib3.filepost import encode_multipart_formdata, choose_boundary
from azure.identity import DefaultAzureCredential
```

### <a name="configure-user-defined-variables-to-be-used-throughout"></a>Configuración de variables definidas por el usuario que se usarán en todo el proceso

Reemplace todos los valores de variable encapsulados en { } por sus propios valores. Además, valide que las variables construidas sean correctas.  Por ejemplo, se construye mediante la dirección URL del servicio y, a continuación, se anexa con `base_url` la versión de la API REST que se usa. La dirección URL del servicio DICOM será: ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` . Puede usar Azure Portal para ir al servicio DICOM y obtener la dirección URL del servicio. También puede visitar la documentación del [servicio Control de versiones de API para DICOM](api-versioning-dicom-service.md) para obtener más información sobre el control de versiones. Si usa una dirección URL personalizada, deberá invalidar ese valor por el suyo propio.

```python
dicom_service_name = "{server-name}"
path_to_dicoms_dir = "{path to the folder that includes green-square.dcm and other dcm files}"

base_url = f"{Service URL}/v{version}"

study_uid = "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"; #StudyInstanceUID for all 3 examples
series_uid = "1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"; #SeriesInstanceUID for green-square and red-triangle
instance_uid = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"; #SOPInstanceUID for red-triangle
```

### <a name="authenticate-to-azure-and-get-a-token"></a>Autenticación en Azure y obtener un token

`DefaultAzureCredential` nos permite obtener varias maneras de obtener tokens para iniciar sesión en el servicio. Usaremos para `AzureCliCredential` obtener un token para iniciar sesión en el servicio. Hay otros proveedores de credenciales como `ManagedIdentityCredential` y que también son `EnvironmentCredential` posibles de usar. Para usar AzureCliCredential, debe haber iniciado sesión en Azure desde la CLI antes de ejecutar este código. (Consulte [Get access token for the DICOM service using CLI de Azure](dicom-get-access-token-azure-cli.md) (Obtener token de acceso para el servicio DICOM mediante CLI de Azure para obtener más información). Como alternativa, puede copiar y pegar el token recuperado al iniciar sesión desde la CLI.

> [!NOTE]
> `DefaultAzureCredential` devuelve varios objetos Credential diferentes. Hacemos referencia a `AzureCliCredential` como el quinto elemento de la colección devuelta. Esto puede no ser coherente. Si es así, descomprima la `print(credential.credential)` línea. Esto enumerará todos los elementos. Busque el índice correcto y recuerde que Python usa la indexación de base cero.

> [!NOTE]
> Si no ha iniciado sesión en Azure mediante la CLI, se producirá un error. Debe haber iniciado sesión en Azure desde la CLI para que esto funcione. 

```python
from azure.identity import DefaultAzureCredential
credential = DefaultAzureCredential()

#print(credential.credentials) # this can be used to find the index of the AzureCliCredential
token = credential.credentials[4].get_token('https://dicom.healthcareapis.azure.com')
bearer_token = f'Bearer {token.token}'
```

### <a name="create-supporting-methods-to-support-multipartrelated"></a>Creación de métodos auxiliares para admitir `multipart\related`

Las bibliotecas (y la mayoría de las bibliotecas de Python) no funcionan con de una `Requests` `multipart\related` manera que admita DICOMweb &trade; . Debido a estas bibliotecas, debemos agregar algunos métodos para admitir el trabajo con archivos DICOM.

`encode_multipart_related` toma un conjunto de campos (en el caso de DICOM, estas bibliotecas suelen ser archivos de la parte 10) y un límite opcional definido por el usuario. Devuelve el cuerpo completo, junto con el content_type, que se puede usar.

```python
def encode_multipart_related(fields, boundary=None):
    if boundary is None:
        boundary = choose_boundary()

    body, _ = encode_multipart_formdata(fields, boundary)
    content_type = str('multipart/related; boundary=%s' % boundary)

    return body, content_type
```

### <a name="create-a-requests-session"></a>Creación de una `requests` sesión

Crea una `requests` sesión, denominada `client` , que se usará para comunicarse con el servicio DICOM.


```python
client = requests.session()
```

### <a name="verify-authentication-is-configured-correctly"></a>Comprobación de que la autenticación está configurada correctamente

Llame al punto de conexión de la API changefeed, que devolverá un valor 200 si la autenticación se realiza correctamente.

```python
headers = {"Authorization":bearer_token}
url= f'{base_url}/changefeed'

response = client.get(url,headers=headers)
if (response.status_code != 200):
    print('Error! Likely not authenticated!')
```

## <a name="uploading-dicom-instances-stow"></a>Carga de instancias DICOM (STOW)

En los ejemplos siguientes se resaltan los archivos DICOM persistentes.

### <a name="store-instances-using-multipartrelated"></a>Almacenamiento de instancias mediante `multipart/related`

En este ejemplo se muestra cómo cargar un único archivo DICOM y se usa un poco de Python para cargar previamente el archivo DICOM (como bytes) en la memoria. Al pasar una matriz de archivos al parámetro fields de , se pueden cargar `encode_multipart_related` varios archivos en un único POST. A veces se usa para cargar varias instancias dentro de una serie o estudio completos.

_Detalles:_

* Camino:.. /studies
* Método: POST
* Encabezados:
    * Accept: application/dicom+json
    * Content-Type: multipart/related; type="application/dicom"
    * Autorización: Portador $token"

* Cuerpo:
    * Content-Type: application/dicom para cada archivo cargado, separado por un valor de límite

Algunos lenguajes de programación y herramientas se comportan de forma diferente. Por ejemplo, algunos de ellos requieren que defina su propio límite. Para ello, es posible que tenga que usar un encabezado Content-Type ligeramente modificado. Los siguientes se han usado correctamente.
* Content-Type: multipart/related; type="application/dicom"; boundary=ABCD1234
* Content-Type: multipart/related; boundary=ABCD1234
* Content-Type: multipart/related

```python
#upload blue-circle.dcm
filepath = Path(path_to_dicoms_dir).joinpath('blue-circle.dcm')

# Read through file and load bytes into memory 
with open(filepath,'rb') as reader:
    rawfile = reader.read()
files = {'file': ('dicomfile', rawfile, 'application/dicom')}

#encode as multipart_related
body, content_type = encode_multipart_related(fields = files)

headers = {'Accept':'application/dicom+json', "Content-Type":content_type, "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
```

### <a name="store-instances-for-a-specific-study"></a>Almacenamiento de instancias para un estudio específico

En este ejemplo se muestra cómo cargar varios archivos DICOM en el estudio especificado. Usa un poco de Python para cargar previamente el archivo DICOM (como bytes) en la memoria.  

Al pasar una matriz de archivos al parámetro fields de , se pueden cargar varios `encode_multipart_related` archivos en un único POST. A veces se usa para cargar una serie completa o un estudio. 

_Detalles:_
* Camino:.. /studies/{study}
* Método: POST
* Encabezados:
    * Accept: application/dicom+json
    * Content-Type: multipart/related; type="application/dicom"
    * Autorización: Portador $token"
* Cuerpo:
    * Content-Type: application/dicom para cada archivo cargado, separado por un valor de límite


```python

filepath_red = Path(path_to_dicoms_dir).joinpath('red-triangle.dcm')
filepath_green = Path(path_to_dicoms_dir).joinpath('green-square.dcm')

# Open up and read through file and load bytes into memory 
with open(filepath_red,'rb') as reader:
    rawfile_red = reader.read()
with open(filepath_green,'rb') as reader:
    rawfile_green = reader.read()  
       
files = {'file_red': ('dicomfile', rawfile_red, 'application/dicom'),
         'file_green': ('dicomfile', rawfile_green, 'application/dicom')}

#encode as multipart_related
body, content_type = encode_multipart_related(fields = files)

headers = {'Accept':'application/dicom+json', "Content-Type":content_type, "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
```
### <a name="store-single-instance-non-standard"></a>Almacenamiento de una instancia única (no estándar)

En el ejemplo de código siguiente se muestra cómo cargar un único archivo DICOM. Es un punto de conexión de API no estándar que simplifica la carga de un único archivo como bytes binarios enviados en el cuerpo de una solicitud.

_Detalles:_
* Camino:.. /studies
* Método: POST
* Encabezados:
   *  Accept: application/dicom+json
   *  Content-Type: application/dicom
   *  Autorización: Portador $token"
* Cuerpo:
    * Contiene un único archivo DICOM como bytes binarios.

```python
#upload blue-circle.dcm
filepath = Path(path_to_dicoms_dir).joinpath('blue-circle.dcm')

# Open up and read through file and load bytes into memory 
with open(filepath,'rb') as reader:
    body = reader.read()

headers = {'Accept':'application/dicom+json', 'Content-Type':'application/dicom', "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
response  # response should be a 409 Conflict if the file was already uploaded in the above request
```

## <a name="retrieve-dicom-instances-wado"></a>Recuperación de instancias dicom (WADO)

En los ejemplos siguientes se resalta la recuperación de instancias DICOM.

### <a name="retrieve-all-instances-within-a-study"></a>Recuperación de todas las instancias de un estudio

En este ejemplo se recuperan todas las instancias de un solo estudio.

_Detalles:_
* Camino:.. /studies/{study}
* Método: GET
* Encabezados:
   * Accept: multipart/related; type="application/dicom"; transfer-syntax=*
   * Autorización: Portador $token"

Los tres archivos dcm cargados anteriormente forman parte del mismo estudio, por lo que la respuesta debe devolver las tres instancias. Compruebe que la respuesta tiene un código de estado De acuerdo y que se devuelven las tres instancias.

```python
url = f'{base_url}/studies/{study_uid}'
headers = {'Accept':'multipart/related; type="application/dicom"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="use-the-retrieved-instances"></a>Uso de las instancias recuperadas

Las instancias se recuperan como bytes binarios. Puede recorrer en bucle los elementos devueltos y convertir los bytes en un archivo de tipo que puede leer `pydicom` .


```python
import requests_toolbelt as tb
from io import BytesIO

mpd = tb.MultipartDecoder.from_response(response)
for part in mpd.parts:
    # Note that the headers are returned as binary!
    print(part.headers[b'content-type'])
    
    # You can convert the binary body (of each part) into a pydicom DataSet
    #   And get direct access to the various underlying fields
    dcm = pydicom.dcmread(BytesIO(part.content))
    print(dcm.PatientName)
    print(dcm.SOPInstanceUID)
```

### <a name="retrieve-metadata-of-all-instances-in-study"></a>Recuperación de metadatos de todas las instancias en estudio

Esta solicitud recupera los metadatos de todas las instancias de un solo estudio.

_Detalles:_
* Camino:.. /studies/{study}/metadata
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador $token"

Los tres archivos cargados anteriormente forman parte del mismo estudio, por lo que la respuesta debe devolver los `.dcm` metadatos de las tres instancias. Compruebe que la respuesta tiene un código de estado ok y que se devuelven todos los metadatos.

```python
url = f'{base_url}/studies/{study_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-all-instances-within-a-series"></a>Recuperación de todas las instancias de una serie

Esta solicitud recupera todas las instancias de una sola serie.

_Detalles:_
* Camino:.. /studies/{study}/series/{series}
* Método: GET
* Encabezados:
   * Accept: multipart/related; type="application/dicom"; transfer-syntax=*
   * Autorización: Portador $token"

Esta serie tiene dos instancias (cuadrado verde y triángulo rojo), por lo que la respuesta debe devolver ambas instancias. Compruebe que la respuesta tiene un código de estado correcto y que se devuelven ambas instancias.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}'
headers = {'Accept':'multipart/related; type="application/dicom"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-metadata-of-all-instances-in-series"></a>Recuperación de metadatos de todas las instancias de la serie

Esta solicitud recupera los metadatos de todas las instancias de una sola serie.

_Detalles:_
* Camino:.. /studies/{study}/series/{series}/metadata
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador $token"

Esta serie tiene dos instancias (cuadrado verde y triángulo rojo), por lo que la respuesta debe devolverse para ambas instancias. Compruebe que la respuesta tiene un código de estado ok y que se devuelven los metadatos de ambas instancias.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>Recuperación de una sola instancia dentro de una serie de un estudio

Esta solicitud recupera una instancia única.

_Detalles:_
* Camino:.. /studies/{study}/series{series}/instances/{instance}
* Método: GET
* Encabezados:
   * Accept: application/dicom; transfer-syntax=*
   * Autorización: Portador $token"

Este ejemplo de código solo debe devolver el triángulo rojo de la instancia. Compruebe que la respuesta tiene un código de estado ok y que se devuelve la instancia.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}'
headers = {'Accept':'application/dicom; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>Recuperación de metadatos de una sola instancia dentro de una serie de un estudio

Esta solicitud recupera los metadatos de una sola instancia dentro de un único estudio y serie.

_Detalles:_
* Camino:.. /studies/{study}/series/{series}/instances/{instance}/metadata
* Método: GET
* Encabezados:
  * Accept: application/dicom+json
  * Autorización: Portador $token"

Este ejemplo de código solo debe devolver los metadatos de la instancia de triángulo rojo. Compruebe que la respuesta tiene un código de estado ok y que se devuelven los metadatos.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>Recuperación de uno o varios fotogramas de una sola instancia

Esta solicitud recupera uno o varios fotogramas de una sola instancia.

_Detalles:_
* Camino:.. /studies/{study}/series{series}/instances/{instance}/frames/1,2,3
* Método: GET
* Encabezados:
   * Autorización: Portador $token"
   * `Accept: multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1` (Valor predeterminado) o
   * `Accept: multipart/related; type="application/octet-stream"; transfer-syntax=*` o
   * `Accept: multipart/related; type="application/octet-stream";`

Este ejemplo de código debe devolver el único marco del triángulo rojo. Valide que la respuesta tiene un código de estado De acuerdo y que se devuelve el marco.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}/frames/1'
headers = {'Accept':'multipart/related; type="application/octet-stream"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

## <a name="query-dicom-qido"></a>Consultar DICOM (QIDO)

En los ejemplos siguientes, buscamos elementos con sus identificadores únicos. También puede buscar otros atributos, como PatientName.

Consulte el documento De la instrucción [de conformidad de DICOM](dicom-services-conformance-statement.md#supported-search-parameters) para ver los atributos de DICOM admitidos.

### <a name="search-for-studies"></a>Búsqueda de estudios

Esta solicitud busca uno o varios estudios por atributos DICOM.

_Detalles:_
* Camino:.. /studies? StudyInstanceUID={study}
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador $token"

Compruebe que la respuesta incluye un estudio y que el código de respuesta es correcto.

```python
url = f'{base_url}/studies'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'StudyInstanceUID':study_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-series"></a>Búsqueda de series

Esta solicitud busca una o varias series por atributos DICOM.

_Detalles:_
* Camino:.. /series? SeriesInstanceUID={series}
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador $token"

Compruebe que la respuesta incluye una serie y que el código de respuesta es Correcto.

```python
url = f'{base_url}/series'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SeriesInstanceUID':series_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-series-within-a-study"></a>Búsqueda de series dentro de un estudio

Esta solicitud busca una o varias series dentro de un solo estudio por atributos DICOM.

_Detalles:_
* Camino:.. /studies/{study}/series? SeriesInstanceUID={series}
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador $token"

Compruebe que la respuesta incluye una serie y que el código de respuesta es Correcto.

```python
url = f'{base_url}/studies/{study_uid}/series'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SeriesInstanceUID':series_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances"></a>Búsqueda de instancias

Esta solicitud busca una o varias instancias por atributos DICOM.

_Detalles:_
* Camino:.. /instances? SOPInstanceUID={instance}
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador $token"

Compruebe que la respuesta incluye una instancia y que el código de respuesta es Correcto.

```python
url = f'{base_url}/instances'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances-within-a-study"></a>Búsqueda de instancias dentro de un estudio

Esta solicitud busca una o varias instancias dentro de un único estudio por atributos DICOM.

_Detalles:_
* Camino:.. /studies/{study}/instances? SOPInstanceUID={instance}
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador $token"

Compruebe que la respuesta incluye una instancia y que el código de respuesta es Correcto.

```python
url = f'{base_url}/studies/{study_uid}/instances'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances-within-a-study-and-series"></a>Búsqueda de instancias dentro de un estudio y una serie

Esta solicitud busca una o varias instancias dentro de un único estudio y una sola serie por atributos DICOM.

_Detalles:_
* Camino:.. /studies/{study}/series/{series}/instances? SOPInstanceUID={instance}
* Método: GET
* Encabezados:
   * Accept: application/dicom+json
   * Autorización: Portador $token"

Compruebe que la respuesta incluye una instancia y que el código de respuesta es Correcto.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances'
headers = {'Accept':'application/dicom+json'}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

## <a name="delete-dicom"></a>Eliminación de DICOM

> [!NOTE]
> La eliminación no forma parte del estándar DICOM, pero se ha agregado por comodidad.

Se devuelve un código de respuesta 204 cuando la eliminación se realiza correctamente. Se devuelve un código de respuesta 404 si los elementos nunca han existido o ya se han eliminado.

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>Eliminación de una instancia específica dentro de un estudio y una serie

Esta solicitud elimina una instancia única dentro de un solo estudio y una sola serie.

_Detalles:_
* Camino:.. /studies/{study}/series/{series}/instances/{instance}
* Método: DELETE
* Encabezados:
   * Autorización: Portador $token

Esta solicitud elimina la instancia de triángulo rojo del servidor. Si se realiza correctamente, el código de estado de respuesta no contiene contenido.

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="delete-a-specific-series-within-a-study"></a>Eliminación de una serie específica dentro de un estudio

Esta solicitud elimina una sola serie (y todas las instancias secundarias) dentro de un solo estudio.

_Detalles:_
* Camino:.. /studies/{study}/series/{series}
* Método: DELETE
* Encabezados:
   * Autorización: Portador $token

En este ejemplo de código se elimina la instancia de cuadrado verde (es el único elemento que queda en la serie) del servidor. Si se realiza correctamente, el código de estado de respuesta no se contenta.

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}/series/{series_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="delete-a-specific-study"></a>Eliminación de un estudio específico

Esta solicitud elimina un solo estudio (y todas las series e instancias secundarias).

_Detalles:_
* Camino:.. /studies/{study}
* Método: DELETE
* Encabezados:
   * Autorización: Portador $token

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre el servicio DICOM, consulte 

>[!div class="nextstepaction"]
>[Información general del servicio DICOM](dicom-services-overview.md)
