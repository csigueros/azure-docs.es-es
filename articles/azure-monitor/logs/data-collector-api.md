---
title: HTTP Data Collector API de Azure Monitor | Microsoft Docs
description: Puede usar HTTP Data Collector API de Azure Monitor para agregar datos POST JSON en un área de trabajo de Log Analytics desde cualquier cliente que pueda llamar a la API REST. En este artículo se describe cómo utilizar la API y se incluyen ejemplos de cómo publicar datos mediante diferentes lenguajes de programación.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/14/2020
ms.openlocfilehash: 9c6be388d02ebec99a3ec9ad105cff68c6e3f1bd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735828"
---
# <a name="send-log-data-to-azure-monitor-by-using-the-http-data-collector-api-preview"></a>Envío de datos de registro a Azure Monitor con HTTP Data Collector API (versión preliminar)

En este artículo se muestra cómo utilizar HTTP Data Collector API para enviar datos de registro a Azure Monitor desde un cliente de API REST.  Describe cómo dar formato a los datos recopilados por el script o la aplicación, incluirlos en una solicitud y obtener de Azure Monitor la autorización de dicha solicitud. Proporcionamos ejemplos de Azure PowerShell, C# y Python.

> [!NOTE]
> HTTP Data Collector API de Azure Monitor se encuentra en versión preliminar pública.

## <a name="concepts"></a>Conceptos
Puede usar HTTP Data Collector API para enviar datos a un área de trabajo de Log Analytics en Azure Monitor desde cualquier cliente que pueda llamar a una API REST.  El cliente podría ser un runbook en Azure Automation que recopila datos de administración de Azure u otra nube, o podría ser un sistema de administración alternativo que usa Azure Monitor para consolidar y analizar los datos de registro.

Todos los datos del área de trabajo de Log Analytics se almacenan como un registro con un tipo de registro concreto.  Debe formatear los datos para enviarlos a la HTTP Data Collector API como varios registros en notación de objetos JavaScript (JSON).  Al enviar los datos, se crea un registro individual en el repositorio para cada registro en la carga de solicitud.

![Captura de pantalla que ilustra la introducción al recopilador de datos HTTP.](media/data-collector-api/overview.png)

## <a name="create-a-request"></a>Creación de una solicitud
Para usar HTTP Data Collector API, cree una solicitud POST que incluya los datos que se van a enviar en JSON. Las siguientes tres tablas enumeran los atributos que son necesarios para cada solicitud. Se describirá cada atributo con más detalle más adelante en el artículo.

### <a name="request-uri"></a>URI de solicitud
| Atributo | Propiedad |
|:--- |:--- |
| Método |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tipo de contenido |application/json |
| | |

### <a name="request-uri-parameters"></a>Parámetros de URI de solicitud
| Parámetro | Descripción |
|:--- |:--- |
| CustomerID |El identificador único del área de trabajo de Log Analytics. |
| Resource |Nombre de recurso de la API: /api/logs. |
| Versión de API |Versión de la API que se usará con esta solicitud. Actualmente, la versión es 2016-04-01. |
| | |

### <a name="request-headers"></a>Encabezados de solicitud
| Encabezado | Descripción |
|:--- |:--- |
| Authorization |Firma de la autorización. Más adelante en este artículo, encontrará información acerca de cómo crear un encabezado HMAC-SHA256. |
| Log-Type |Especifica el tipo de registro de los datos que se envían. Solo puede contener letras, números y el carácter de subrayado (_) y no puede superar los 100 caracteres. |
| x-ms-date |Fecha en que se procesó la solicitud, en formato RFC 7234. |
| x-ms-AzureResourceId | Identificador del recurso de Azure con el que se deben asociar los datos. Rellena la propiedad [_ResourceId](./log-standard-columns.md#_resourceid) y permite que los datos se incluyan en las consultas de [contexto del recurso](./design-logs-deployment.md#access-mode). Si no se especifica este campo, los datos no se incluirán en las consultas de contexto del recurso. |
| time-generated-field | Nombre de un campo en los datos que contiene la marca de tiempo del elemento de datos. Si especifica un campo, su contenido se usa para **TimeGenerated**. Si no se especifica este campo, el valor predeterminado de **TimeGenerated** es la hora de ingesta del mensaje. El contenido del campo de mensaje debe seguir el formato ISO 8601 AAAA-MM-DDThh:mm:ssZ. |
| | |

## <a name="authorization"></a>Authorization
Cualquier solicitud a HTTP Data Collector API de Azure Monitor debe incluir un encabezado de autorización. Para autenticar una solicitud, firme la solicitud con la clave principal o secundaria del área de trabajo que realiza la solicitud. Después, pase esa firma como parte de la solicitud.   

Este es el formato del encabezado de autorización:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* es el identificador único del área de trabajo de Log Analytics. *Signature* es un [Código de autenticación de mensajes basado en hash (HMAC)](/dotnet/api/system.security.cryptography.hmacsha256) que se construye a partir de la solicitud y después se procesa mediante el [algoritmo SHA256](/dotnet/api/system.security.cryptography.sha256). Luego se codifica con la codificación Base64.

Use este formato para codificar la cadena de firma **SharedKey**:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
                  Content-Type + "\n" +
                  "x-ms-date:" + x-ms-date + "\n" +
                  "/api/logs";
```

Este es un ejemplo de una cadena de firma:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Cuando tenga la cadena de firma, codifíquela usando un algoritmo HMAC-SHA256 en la cadena codificada mediante UTF-8 y, después, codifique el resultado como Base64. Use este formato:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Los ejemplos de las secciones siguientes tienen código de ejemplo que le ayudarán a crear un encabezado de autorización.

## <a name="request-body"></a>Cuerpo de la solicitud
El cuerpo del mensaje debe estar en formato JSON. Debe incluir uno o varios registros con los pares de nombre y valor de propiedad en el siguiente formato. El nombre de la propiedad solo puede contener letras, números y el carácter de subrayado (-).

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Puede procesar por lotes varios registros en una sola solicitud con el formato siguiente. Todos los registros deben ser del mismo tipo de registro.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Propiedades y tipo de registro
Cuando se envían datos a través de HTTP Data Collector API de Azure Monitor, se define un tipo de registro personalizado. Actualmente, no se pueden escribir datos en tipos de registros existentes creados por otros tipos de datos y soluciones. Azure Monitor lee los datos entrantes y después crea las propiedades que coinciden con los tipos de datos de los valores que especifique.

Cada solicitud a Data Collector API debe incluir un encabezado **Log-Type** con el nombre del tipo de registro. El sufijo **_CL** se anexa automáticamente al nombre que especifique para distinguirlo de otros tipos de registros como un registro personalizado. Por ejemplo, si escribe el nombre **MyNewRecordType**, Azure Monitor crea un registro con el tipo de **MyNewRecordType_CL**. Esto ayuda a garantizar que no haya conflictos entre los nombres de tipo creados por el usuario y los incluidos en las soluciones de Microsoft actuales o futuras.

Para identificar el tipo de datos de una propiedad, Azure Monitor agrega un sufijo al nombre de la propiedad. Si una propiedad contiene un valor NULL, la propiedad no se incluye en ese registro. Esta tabla enumera el tipo de datos de la propiedad y el sufijo correspondiente:

| Tipo de datos de la propiedad | Sufijo |
|:--- |:--- |
| String |_s |
| Boolean |_b |
| Double |_d |
| Fecha/hora |_t |
| GUID (almacenado como una cadena) |_g |
| | |

> [!NOTE]
> A los valores de cadena que parecen ser GUID se les asigna el sufijo _g y se les aplica formato de GUID, aunque el valor entrante no incluya guiones. Por ejemplo, "8145d822-13a7-44ad-859c-36f31a84f6dd" y "8145d82213a744ad859c36f31a84f6dd" se almacenan como "8145d822-13a7-44ad-859c-36f31a84f6dd". Las únicas diferencias entre esta y otra cadena es la _g en el nombre y la inserción de guiones si no se proporcionan en la entrada. 

El tipo de datos que utiliza Azure Monitor para cada propiedad depende de si ya existe el tipo de registro para el nuevo registro.

* Si el tipo de registro no existe, Azure Monitor crea uno nuevo mediante la inferencia de tipos JSON para determinar el tipo de datos de cada propiedad del nuevo registro.
* Si el tipo de registro existe, Azure Monitor intenta crear un nuevo registro en función de las propiedades existentes. Si el tipo de datos de una propiedad en el nuevo registro no coincide y no se puede convertir al tipo existente, o si el registro incluye una propiedad que no existe, Azure Monitor crea una nueva propiedad que tiene el sufijo pertinente.

Por ejemplo, la entrada de envío siguiente crearía un registro con tres propiedades, **number_d**, **boolean_b** y **string_s**:

![Captura de pantalla del registro de ejemplo 1.](media/data-collector-api/record-01.png)

Si enviara la entrada siguiente, con todos los valores con formato de cadena, las propiedades no cambiarían. Puede convertir los valores en tipos de datos existentes.

![Captura de pantalla del registro de ejemplo 2.](media/data-collector-api/record-02.png)

No obstante, si después se realiza el siguiente envío, Azure Monitor creará las nuevas propiedades **boolean_d** y **string_d**. Estos valores no se pueden convertir.

![Captura de pantalla del registro de ejemplo 3.](media/data-collector-api/record-03.png)

Si después envía la entrada siguiente, antes de que se cree el tipo de registro, Azure Monitor creará un registro con tres propiedades: **number_s**, **boolean_s** y **string_s**. En esta entrada, se da un formato de cadena a cada uno de los valores iniciales:

![Captura de pantalla del registro de ejemplo 4.](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Propiedades reservadas
Las propiedades siguientes están reservadas y no deben usarse en un tipo de registro personalizado. Recibirá un error si la carga útil incluye alguno de estos nombres de propiedad:

- tenant

## <a name="data-limits"></a>Límites de datos
Los datos publicados en API de recopilación de datos de Azure Monitor están sujetos a ciertas restricciones:

* Máximo de 30 MB por publicación en Data Collector API de Azure Monitor. Se trata de un límite de tamaño para una sola publicación. Si los datos de una única publicación superan los 30 MB, debe dividir los datos en fragmentos más pequeños y enviarlos al mismo tiempo.
* Límite de 32 KB para los valores de campo. Si el valor del campo es mayor que 32 kB, se truncarán los datos.
* Se recomienda un máximo de 50 campos para un tipo determinado. Se trata de un límite práctico desde una perspectiva de la experiencia de búsqueda y la facilidad de uso.  
* Las tablas de las áreas de trabajo de Log Analytics solo admiten hasta 500 columnas (que se denominan "campos" en este artículo). 
* Máximo de 50 caracteres para los nombres de columna.

## <a name="return-codes"></a>Códigos de retorno
El código de estado HTTP 200 significa que se ha recibido la solicitud para su procesamiento. Esto indica que el trabajo se ha completado correctamente.

El conjunto completo de códigos de estado que el servicio puede devolver se muestra en la tabla siguiente:

| Código | Status | Código de error | Descripción |
|:--- |:--- |:--- |:--- |
| 200 |Aceptar | |La solicitud se aceptó correctamente. |
| 400 |Solicitud incorrecta |InactiveCustomer |El área de trabajo se cerró. |
| 400 |Solicitud incorrecta |InvalidApiVersion |El servicio no reconoció la versión de API especificada. |
| 400 |Solicitud incorrecta |InvalidCustomerId |El id. de área de trabajo especificado no es válido. |
| 400 |Solicitud incorrecta |InvalidDataFormat |Se envió un archivo JSON no válido. El cuerpo de la respuesta puede contener más información acerca de cómo resolver el error. |
| 400 |Solicitud incorrecta |InvalidLogType |El tipo de registro especificado contenía caracteres especiales o valores numéricos. |
| 400 |Solicitud incorrecta |MissingApiVersion |No se especificó la versión de API. |
| 400 |Solicitud incorrecta |MissingContentType |No se especificó el tipo de contenido. |
| 400 |Solicitud incorrecta |MissingLogType |No se especificó el tipo de registro de valor requerido. |
| 400 |Solicitud incorrecta |UnsupportedContentType |El tipo de contenido no se estableció en **application/json**. |
| 403 |Prohibido |InvalidAuthorization |El servicio no pudo autenticar la solicitud. Compruebe que el identificador del área de trabajo y la clave de conexión sean válidas. |
| 404 |No encontrado | | La dirección URL proporcionada no es correcta, o el tamaño de la solicitud es excesivo. |
| 429 |Demasiadas solicitudes | | El servicio está experimentando un gran volumen de datos de su cuenta. Vuelva a intentar realizar la solicitud más tarde. |
| 500 |Internal Server Error |UnspecifiedError |El servicio detectó un error interno. Vuelva a intentar realizar la solicitud. |
| 503 |Servicio no disponible |ServiceUnavailable |El servicio actualmente no está disponible para recibir solicitudes. Vuelva a intentar realizar la solicitud. |
| | |

## <a name="query-data"></a>Consultar datos
Para consultar los datos enviados por HTTP Data Collector API de Azure Monitor, busque los registros cuyo **Type** sea igual al valor de **LogType** que especificó, con el sufijo **_CL**. Por ejemplo, si utilizó **MyCustomLog**, se devolverán todos los registros con `MyCustomLog_CL`.

## <a name="sample-requests"></a>Solicitudes de ejemplo
En las secciones siguientes, encontrará ejemplos que demuestran cómo enviar datos a HTTP Data Collector API de Azure Monitor mediante diferentes lenguajes de programación.

Para cada ejemplo, establezca las variables para el encabezado de autorización haciendo lo siguiente:

1. En Azure Portal, seleccione el área de trabajo de Log Analytics.
2. Seleccione **Administración de agentes**.
2. A la derecha de **Id. de área de trabajo**, seleccione el icono de **Copiar** y pegue el identificador como valor de la variable **Id. de cliente**.
3. A la derecha de **Clave principal**, seleccione el icono de **Copiar** y pegue el identificador como valor de la variable **Clave compartida**.

También puede cambiar las variables del tipo de registro y de los datos JSON.

### <a name="powershell-sample"></a>Ejemplo de PowerShell
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Ejemplo de C#
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Ejemplo de Python 2
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

### <a name="python-3-sample"></a>Ejemplo de Python 3
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash, encoding="utf-8")  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest()).decode()
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print('Accepted')
    else:
        print("Response code: {}".format(response.status_code))

post_data(customer_id, shared_key, body, log_type)
```


### <a name="java-sample"></a>Ejemplo de Java

```java

import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.MediaType;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.text.SimpleDateFormat;
import java.util.Base64;
import java.util.Calendar;
import java.util.TimeZone;

import static org.springframework.http.HttpHeaders.CONTENT_TYPE;

public class ApiExample {

  private static final String workspaceId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
  private static final String sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";
  private static final String logName = "DemoExample";
  /*
  You can use an optional field to specify the timestamp from the data. If the time field is not specified,
  Azure Monitor assumes the time is the message ingestion time
   */
  private static final String timestamp = "";
  private static final String json = "{\"name\": \"test\",\n" + "  \"id\": 1\n" + "}";
  private static final String RFC_1123_DATE = "EEE, dd MMM yyyy HH:mm:ss z";

  public static void main(String[] args) throws IOException, NoSuchAlgorithmException, InvalidKeyException {
    String dateString = getServerTime();
    String httpMethod = "POST";
    String contentType = "application/json";
    String xmsDate = "x-ms-date:" + dateString;
    String resource = "/api/logs";
    String stringToHash = String
        .join("\n", httpMethod, String.valueOf(json.getBytes(StandardCharsets.UTF_8).length), contentType,
            xmsDate , resource);
    String hashedString = getHMAC256(stringToHash, sharedKey);
    String signature = "SharedKey " + workspaceId + ":" + hashedString;

    postData(signature, dateString, json);
  }

  private static String getServerTime() {
    Calendar calendar = Calendar.getInstance();
    SimpleDateFormat dateFormat = new SimpleDateFormat(RFC_1123_DATE, Locale.US);
    dateFormat.setTimeZone(TimeZone.getTimeZone("GMT"));
    return dateFormat.format(calendar.getTime());
  }

  private static void postData(String signature, String dateString, String json) throws IOException {
    String url = "https://" + workspaceId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
    HttpPost httpPost = new HttpPost(url);
    httpPost.setHeader("Authorization", signature);
    httpPost.setHeader(CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE);
    httpPost.setHeader("Log-Type", logName);
    httpPost.setHeader("x-ms-date", dateString);
    httpPost.setHeader("time-generated-field", timestamp);
    httpPost.setEntity(new StringEntity(json));
    try(CloseableHttpClient httpClient = HttpClients.createDefault()){
      HttpResponse response = httpClient.execute(httpPost);
      int statusCode = response.getStatusLine().getStatusCode();
      System.out.println("Status code: " + statusCode);
    }
  }

  private static String getHMAC256(String input, String key) throws InvalidKeyException, NoSuchAlgorithmException {
    String hash;
    Mac sha256HMAC = Mac.getInstance("HmacSHA256");
    Base64.Decoder decoder = Base64.getDecoder();
    SecretKeySpec secretKey = new SecretKeySpec(decoder.decode(key.getBytes(StandardCharsets.UTF_8)), "HmacSHA256");
    sha256HMAC.init(secretKey);
    Base64.Encoder encoder = Base64.getEncoder();
    hash = new String(encoder.encode(sha256HMAC.doFinal(input.getBytes(StandardCharsets.UTF_8))));
    return hash;
  }

}


```


## <a name="alternatives-and-considerations"></a>Alternativas y consideraciones

Aunque Data Collector API debe cubrir la mayor parte de sus necesidades para recopilar datos de formato libre en los registros de Azure, es posible que necesite un enfoque alternativo para solucionar algunas de las limitaciones de la API. Las opciones, incluidas las consideraciones principales, se enumeran en la tabla siguiente:

| Alternativa | Descripción | Idónea para |
|---|---|---|
| [Eventos personalizados](../app/api-custom-events-metrics.md?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): Ingesta basada en SDK nativa de Application Insights | Application Insights, que se instrumenta normalmente a través de un SDK dentro de la aplicación, ofrece la capacidad de enviar datos personalizados a través de eventos personalizados. | <ul><li> Datos que se generan dentro de la aplicación, pero que no captura el SDK a través de uno de los tipos de datos predeterminados (solicitudes, dependencias, excepciones, etc.).</li><li> Datos que la mayoría de las veces están correlacionados con otros datos de aplicación en Application Insights. </li></ul> |
| API del recopilador de datos en registros de Azure Monitor | La API del recopilador de datos en registros de Azure Monitor es una forma completamente libre para la ingesta de datos. Todos los datos con formato de objeto JSON se pueden enviar aquí. Una vez enviados, se procesarán y estarán disponibles en los registros de Monitor, para correlacionarse con otros datos de dichos registros u otros datos de Application Insights. <br/><br/> Es bastante fácil cargar los datos como archivos en un blob de Azure Blob Storage, desde donde se procesarán y cargarán estos archivos en Log Analytics. Para obtener una implementación de ejemplo, consulte [Creación de una canalización de datos con Data Collector API](./create-pipeline-datacollector-api.md). | <ul><li> Datos que no necesariamente se generaron dentro de una aplicación instrumentada en Application Insights.<br>Algunos ejemplos son tablas de hechos y de búsqueda, datos de referencia, estadísticas agregadas previamente, etc. </li><li> Datos a los que se hará referencia en otros datos de Azure Monitor (Application Insights, otros tipos de datos de registros de Monitor, Security Center, Container Insights y máquinas virtuales, etc.) </li></ul> |
| [Azure Data Explorer](/azure/data-explorer/ingest-data-overview) | Azure Data Explorer, ahora disponible para el público general, es la plataforma de datos que se utiliza en Analytics de Application Insights y los registros de Azure Monitor. Al usar la plataforma de datos sin procesar, dispone de flexibilidad completa (pero requiere la sobrecarga de administración) en el clúster (control de acceso basado en rol [RBAC] de Kubernetes, tasa de retención, esquema, etc). Azure Data Explorer proporciona muchas [opciones de ingesta](/azure/data-explorer/ingest-data-overview#ingestion-methods), incluidos los archivos [CSV, TSV y JSON](/azure/kusto/management/mappings). | <ul><li> Datos que no se correlacionarán con ningún otro dato en Application Insights o los registros de Monitor. </li><li> Datos que requieren funcionalidades avanzadas de ingesta o procesamiento, que no están disponibles actualmente en los registros de Azure Monitor. </li></ul> |


## <a name="next-steps"></a>Pasos siguientes
- Use [Log Search API](./log-query-overview.md) para recuperar datos desde el área de trabajo de Log Analytics.

- Para obtener más información sobre la [creación de una canalización de datos con Data Collector API](create-pipeline-datacollector-api.md), use un flujo de trabajo de Logic Apps para Azure Monitor.
