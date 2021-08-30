---
title: Introducción a la traducción de documentos
description: Creación de un servicio de traducción de documentos con los lenguajes de programación y las plataformas de C#, Go, Java, Node.js o Python
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 08/09/2021
ms.openlocfilehash: 82070e6b10a1b0bffddb511545f54d369f6f99b8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745580"
---
# <a name="get-started-with-document-translation"></a>Introducción a la traducción de documentos

 En este artículo, aprenderá a usar la traducción de documentos con métodos de la API REST HTTP. Traducción de documentos es una característica basada en la nube del servicio [Azure Translator](../translator-overview.md).  La API de traducción de documentos permite la traducción de documentos completos al tiempo que conserva la estructura del documento de origen y el formato del texto.

## <a name="prerequisites"></a>Requisitos previos

> [!NOTE]
>
> 1. Por lo general, cuando se crea un recurso de Cognitive Services en Azure Portal, tiene la opción de crear una clave de suscripción de varios servicios o una clave de suscripción de un solo servicio. Sin embargo, la traducción de documentos se admite actualmente solo en el recurso de Translator (servicio único) y **no** se incluye en el recurso de Cognitive Services (multiservicio).
> 2. La traducción de documentos **solo** está disponible en el plan de servicio Estándar S1 (pago por uso) o en el plan de descuento por volumen D3. _Consulte_ [los precios de Cognitive Services precios: Translator](https://azure.microsoft.com/pricing/details/cognitive-services/translator/).
>

Para empezar, necesitará lo siguiente:

* Una [**cuenta de Azure**](https://azure.microsoft.com/free/cognitive-services/) activa.  En caso de no tener ninguna, puede crear una [**cuenta gratuita**](https://azure.microsoft.com/free/).

* Un [**recurso Translator de servicio único**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (**no** un recurso multiservicio de Cognitive Services).

* Una [**cuenta de Azure Blob Storage**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Creará contenedores para almacenar y organizar los datos de los blobs en la cuenta de almacenamiento.

## <a name="custom-domain-name-and-subscription-key"></a>Nombre de dominio y clave de suscripción personalizados

> [!IMPORTANT]
>
> * **Todas las solicitudes de API al servicio de traducción de documentos requieren un punto de conexión de dominio personalizado**.
> * No utilizará el punto de conexión que se encuentra en la página _Claves y punto de conexión_ del recurso en Azure Portal, ni el punto de conexión global de Translator (`api.cognitive.microsofttranslator.com`) para realizar solicitudes HTTP de traducción de documentos.

### <a name="what-is-the-custom-domain-endpoint"></a>¿Qué es el punto de conexión de dominio personalizado?

El punto de conexión de dominio personalizado es una dirección URL con el nombre del recurso, el nombre de host y los subdirectorios de Translator:

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0
```

### <a name="find-your-custom-domain-name"></a>Búsqueda del nombre de dominio personalizado

El parámetro **NAME-OF-YOUR-RESOURCE** (también llamado *nombre de dominio personalizado*) es el valor que especificó en el campo **Nombre** al crear el recurso de Translator.

:::image type="content" source="../media/instance-details.png" alt-text="Imagen de Azure Portal de la creación de un recurso, con los detalles de la instancia y el campo Nombre.":::

### <a name="get-your-subscription-key"></a>Obtener tu clave de suscripción

Las solicitudes al servicio Translator requieren una clave de solo lectura para autenticar el acceso.

1. Si ha creado un recurso nuevo, seleccione **Ir al recurso** una vez se haya implementado. Si tiene un recurso de traducción de documentos existente, vaya directamente a la página del recurso.
1. En el raíl izquierdo, en *Administración de recursos*, seleccione **Claves y punto de conexión**.
1. Copie y pegue la clave de suscripción en una ubicación adecuada, como el *Bloc de notas de Microsoft*.
1. Lo pegará en el código siguiente para autenticar la solicitud en el servicio de traducción de documentos.

:::image type="content" source="../media/translator-keys.png" alt-text="Imagen del campo para obtener la clave de suscripción en Azure Portal.":::

## <a name="create-azure-blob-storage-containers"></a>Creación de contenedores de Azure Blob Storage

Tendrá que [**crear contenedores**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) en la [**cuenta de Azure Blob Storage**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) para los archivos de origen y destino.

* **Contenedor de origen**. En este contenedor se cargan los archivos para su traducción (obligatorio).
* **Contenedor de destino**. En este contenedor se almacenarán los archivos traducidos (obligatorio).

> [!NOTE]
> La traducción de documentos admite glosarios en forma de blobs en contenedores de destino (no contenedores de glosario independientes). Si quiere incluir un glosario personalizado, agréguelo al contenedor de destino e incluya ` glossaryUrl` con la solicitud.  Si el par de idiomas de traducción no existe en el glosario, no se aplicará. *Consulte* [Traducción de documentos mediante glosarios personalizados](#translate-documents-using-a-custom-glossary)

### <a name="create-sas-access-tokens-for-document-translation"></a>**Creación de tokens de acceso de SAS para la traducción de documentos**

Los elementos `sourceUrl`, `targetUrl` y el elemento opcional `glossaryUrl` deben incluir un token de firma de acceso compartido (SAS), que se anexa como una cadena de consulta. El token se puede asignar al contenedor o a blobs específicos. *Consulte* [**Creación de tokens de SAS para el proceso de traducción de documentos**](create-sas-tokens.md).

* El contenedor o el blob de **origen** deben tener designado acceso de **lectura** y de **lista**.
* El contenedor o el blob de **destino** deben tener designado acceso de **escritura** y de **lista**.
* El blob de **glosario** debe tener acceso designado de **lectura** y **enumeración**.

> [!TIP]
>
> * Si va a traducir **varios** archivos (blobs) en una operación, **delegue el acceso de SAS en el nivel de contenedor**.
> * Si va a traducir un **único** archivo (blob) en una operación, **delegue el acceso de SAS en el nivel de blob**.
>

## <a name="document-translation-http-requests"></a>Traducción de documentos: solicitudes HTTP

Una solicitud de traducción de documentos por lotes se envía al punto de conexión del servicio Translator mediante una solicitud POST. Si se realiza correctamente, el método POST devuelve un código de respuesta `202 Accepted` y el servicio crea la solicitud por lotes.

### <a name="http-headers"></a>Encabezados HTTP

En cada solicitud de API de traducción de documentos se incluyen los siguientes encabezados:

|Encabezado HTTP|Descripción|
|---|--|
|Ocp-Apim-Subscription-Key|**Requerido**: el valor es la clave de suscripción de Azure para el recurso de Translator o Cognitive Services.|
|Content-Type|**Requerido**: especifica el tipo de contenido de la carga. Los valores aceptados son application/json y charset=UTF-8.|
|Content-Length|**Requerido**: longitud del cuerpo de la solicitud.|

### <a name="post-request-body-properties"></a>Propiedades del cuerpo de la solicitud POST

* La dirección URL de la solicitud POST es POST `https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches`.
* El cuerpo de la solicitud POST es un objeto JSON llamado `inputs`.
* El objeto `inputs` contiene las direcciones de contenedor `sourceURL` y `targetURL` de los pares de idioma de origen y destino.
* Los campos `prefix` y `suffix` (opcional) se usan para filtrar documentos en el contenedor, incluidas las carpetas.
* Cuando se traduce el documento, se aplica un valor para el campo `glossaries` (opcional).
* El valor de `targetUrl` para cada idioma de destino debe ser único.

>[!NOTE]
> Si ya existe un archivo con el mismo nombre en el destino, se sobrescribirá.

<!-- markdownlint-disable MD024 -->
### <a name="translate-all-documents-in-a-container"></a>Traducción de todos los documentos de un contenedor

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

### <a name="translate-a-specific-document-in-a-container"></a>Traducción de un documento específico de un contenedor

* Asegúrese de que ha especificado "storageType": "File".
* Asegúrese de haber creado la URL de origen y el token de SAS para el blob/documento específico (no para el contenedor).
* Asegúrese de haber especificado el nombre del archivo de destino como parte de la dirección URL de destino, aunque el token de SAS sigue siendo para el contenedor.
* La solicitud de ejemplo siguiente muestra un único documento que se traduce en dos idiomas de destino.

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "es"
                },
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="translate-documents-using-a-custom-glossary"></a>Traducción de documentos mediante glosarios personalizados

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://myblob.blob.core.windows.net/source",
                "filter": {
                    "prefix": "myfolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://myblob.blob.core.windows.net/target",
                    "language": "es",
                    "glossaries": [
                        {
                            "glossaryUrl": "https:// myblob.blob.core.windows.net/glossary/en-es.xlf",
                            "format": "xliff"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## <a name="use-code-to-submit-document-translation-requests"></a>Uso de código para enviar solicitudes de traducción de documentos

### <a name="set-up-your-coding-platform"></a>Configuración de la plataforma de codificación

### <a name="c"></a>[C#](#tab/csharp)

* Cree un nuevo proyecto.
* Reemplace Program.cs por el código de C# que se muestra a continuación.
* Establezca los valores del punto de conexión, la clave de suscripción y la dirección URL del contenedor en Program.cs.
* Para procesar datos JSON, agregue el [paquete Newtonsoft.Json mediante la CLI de .NET](https://www.nuget.org/packages/Newtonsoft.Json/).
* Ejecute el programa desde el directorio del proyecto.

### <a name="nodejs"></a>[Node.js](#tab/javascript)

* Cree un proyecto de Node.js.
* Instale la biblioteca Axios con `npm i axios`.
* Copie el código siguiente y péguelo en el proyecto.
* Establezca los valores del punto de conexión, la clave de suscripción y la dirección URL del contenedor.
* Ejecutar el programa

### <a name="python"></a>[Python](#tab/python)

* Cree un nuevo proyecto.
* Copie y pegue el código de uno de los ejemplos en el proyecto.
* Establezca los valores del punto de conexión, la clave de suscripción y la dirección URL del contenedor.
* Ejecute el programa. Por ejemplo: `python translate.py`.

### <a name="java"></a>[Java](#tab/java)

* Cree un directorio de trabajo para el proyecto. Por ejemplo:

```powershell
mkdir sample-project
```

* En el directorio del proyecto, cree la siguiente estructura de subdirectorios:

  src</br>
&emsp; └ main</br>
&emsp;&emsp;&emsp;└ java

```powershell
mkdir -p src/main/java/
```

**NOTA**: Los archivos de código fuente de Java (por ejemplo, _sample.java_) se encuentran en src/main/**java**.

* En el directorio raíz (por ejemplo, *sample-project*), inicialice el proyecto con Gradle:

```powershell
gradle init --type basic
```

* Cuando se le solicite que elija un **DSL**, seleccione **Kotlin**.

* Actualice el archivo `build.gradle.kts`. Tenga en cuenta que deberá actualizar `mainClassName` en función del ejemplo:

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* Cree un archivo de Java en el directorio **java** y copie y pegue el código del ejemplo proporcionado. No olvide agregar la clave de suscripción y el punto de conexión.

* **Compile y ejecute el ejemplo desde el directorio raíz**:

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Go](#tab/go)

* Cree un nuevo proyecto de Go.
* Agregue el código que se proporciona a continuación.
* Establezca los valores del punto de conexión, la clave de suscripción y la dirección URL del contenedor.
* Guarde el archivo con la extensión ".go".
* Abra un símbolo del sistema en un equipo que tenga instalado Go.
* Compile el archivo. Por ejemplo: "go build example-code.go".
* Ejecute el archivo, por ejemplo: "example-code".

 ---

> [!IMPORTANT]
>
> Para los ejemplos de código siguientes, deberá codificar de forma rígida la clave y el punto de conexión donde se indique. Recuerde quitar la clave del código cuando haya terminado y no publicarla públicamente.  Consulte [Seguridad de Azure Cognitive Services](../../cognitive-services-security.md?tabs=command-line%2ccsharp) para saber cómo almacenar y acceder a las credenciales de forma segura.
>
> Es posible que tenga que actualizar los campos siguientes, en función de la operación:
>>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id` (identificador del trabajo)
>>

#### <a name="locating--the-id-value"></a>Búsqueda del valor de `id`

* Encontrará el valor de `id` del trabajo en el valor de la dirección URL `Operation-Location` del encabezado de respuesta del método POST. El último parámetro de la dirección URL es el valor de **`id`** del trabajo de la operación:

|**Encabezado de respuesta**|**Dirección URL del resultado**|
|-----------------------|----------------|
Operation-Location   | https://<<span>NOMBRE-DEL-RECURSO>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/9dce0aa9-78dc-41ba-8cae-2e2f3c2ff8ec</span>

* También puede usar una solicitud **Get Jobs** para recuperar el valor de `id` de un trabajo de traducción de documentos.

>

 ## <a name="translate-documents"></a>Traducción de documentos

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;


    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");

        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {

                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;

                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:");
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="get-file-formats"></a>Obtención de los formatos de archivo

Recupera una lista de los formatos de archivo admitidos. Si se realiza correctamente, este método devuelve un código de respuesta `200 OK`.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="get-job-status"></a>Obtención de estado del trabajo

Obtiene el estado actual de un único trabajo y un resumen de todos los trabajos de una solicitud de traducción de documentos. Si se realiza correctamente, este método devuelve un código de respuesta `200 OK`.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="get-document-status"></a>Obtención del estado del documento

### <a name="brief-overview"></a>Información general breve

Recupera el estado de un documento específico de una solicitud de traducción de documentos. Si se realiza correctamente, este método devuelve un código de respuesta `200 OK`.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="delete-job"></a>Eliminación de un trabajo

### <a name="brief-overview"></a>Información general breve

Cancela el procesamiento actual o el trabajo en cola. Solo se cancelarán los documentos para los que no se haya iniciado la traducción.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>Límites del contenido

En la tabla siguiente se enumeran los límites de los datos que se envían a la traducción de documentos.

|Atributo | Límite|
|---|---|
|Tamaño del documento| ≤40 MB |
|Número total de archivos.|≤1000 |
|Tamaño total del contenido de un lote | ≤250 MB|
|Número de idiomas de destino en un lote| ≤10 |
|Tamaño del archivo de la memoria de traducción| ≤10 MB|

La traducción de documentos no se puede usar para traducir documentos protegidos, como aquellos con una contraseña cifrada o con acceso restringido para copiar contenido.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="common-http-status-codes"></a>Códigos de estado HTTP comunes

| Código de estado HTTP | Descripción | Posible motivo |
|------------------|-------------|-----------------|
| 200 | Aceptar | La solicitud fue correcta. |
| 400 | Bad Request | Falta un parámetro requerido, está vacío o es nulo. O bien, el valor pasado a un parámetro obligatorio u opcional no es válido. Un problema común es que el encabezado sea demasiado largo. |
| 401 | No autorizado | La solicitud no está autenticada. Asegúrese de que la clave de suscripción o el token sean válidos y de la región correcta. Al administrar la suscripción en Azure Portal, asegúrese de usar el recurso de servicio único **Translator**, y _no_ el recurso multiservicios de **Cognitive Services**.
| 429 | Demasiadas solicitudes | Ha superado la cuota o la tasa de solicitudes permitidas para su suscripción. |
| 502 | Puerta de enlace incorrecta    | Problema de red o de servidor. Podría indicar también encabezados no válidos. |

## <a name="learn-more"></a>Más información

* [Referencia de la API de Translator v3](../reference/v3-0-reference.md)
* [Compatibilidad con idiomas](../language-support.md)
* [Suscripciones en Azure API Management](../../../api-management/api-management-subscriptions.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [¿Qué es Custom Translator?](../custom-translator/overview.md)
>
>
