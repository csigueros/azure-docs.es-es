---
title: Puntos de conexión de seguridad de Microsoft Azure IoT Device Provisioning Service
description: Conceptos sobre cómo controlar el acceso de las aplicaciones back-end a IoT Device Provisioning Service (DPS). Incluye información sobre los tokens de seguridad.
author: anastasia-ms
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: v-stharr
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: cf2934c57441176034d28a7b60e33c639977e62d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779604"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Control de acceso al servicio Azure IoT Hub Device Provisioning

En este artículo se describen las opciones disponibles para proteger IoT Device Provisioning Service. El servicio de aprovisionamiento usa *autenticación* y *permisos* para conceder acceso a cada punto de conexión. Los permisos permiten al proceso de autenticación limitar el acceso a una instancia de servicio en función de la funcionalidad.

En este artículo se trata lo siguiente:

* El proceso de autenticación y los tokens que usa el servicio de aprovisionamiento para comprobar los permisos en las [API REST del servicio y del dispositivo](/rest/api/iot-dps/).

* Los distintos permisos que puede conceder a una aplicación back-end para acceder a la API de servicios.

## <a name="authentication"></a>Authentication

La API de dispositivos admite la autenticación de dispositivos basada en certificados X.509 y basada en claves.  

La API de servicios admite la autenticación basada en claves para aplicaciones back-end.  

Al usar la autenticación basada en claves, Device Provisioning Service usa tokens de seguridad para autenticar servicios para evitar el envío de claves a través de la red. Además, los tokens de seguridad están limitados en cuanto al ámbito y el período de validez. Los SDK de Azure IoT Device Provisioning generan automáticamente tokens sin necesidad de ninguna configuración especial.  

En algunos casos, es posible que tenga que usar las API REST de servicios de HTTP Device Provisioning directamente, sin usar los SDK. En las secciones siguientes se describe cómo autenticarse directamente en las API REST.

## <a name="device-api-authentication"></a>Autenticación de API de dispositivos

Los dispositivos usan la [API de dispositivos](/rest/api/iot-dps/device/runtime-registration) para dar fe del servicio Device Provisioning y recibir una conexión de IoT Hub.

>[!NOTE]
>Para recibir una conexión autenticada, los dispositivos deben registrarse primero en Device Provisioning Service a través de una inscripción. Use la API de servicios para registrar un dispositivo mediante programación a través de una inscripción.

Un dispositivo debe autenticarse en la API de dispositivos como parte del proceso de aprovisionamiento. El método que usa un dispositivo para autenticarse se define al configurar un grupo de inscripciones o una inscripción individual. Sea cual sea el método de autenticación, el dispositivo debe emitir una solicitud HTTPS PUT a la siguiente dirección URL para aprovisionarse a sí mismo.

```http
    https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01
```
Si usa la autenticación basada en claves, se pasa un token de seguridad en el encabezado de solicitud de  **autorización**  HTTP en el formato siguiente:

```bash
    SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI} 
```

### <a name="security-token-structure-for-key-based-authentication"></a>Estructura del token de seguridad para la autenticación basada en claves

El token de seguridad se pasa en el encabezado de solicitud de  **autorización**  HTTP en el formato siguiente:

```bash
    SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI} 
```

Los valores esperados son:

| Value  | Descripción |
|:-------|:------------|
| `{signature}`  | Cadena de firma HMAC-SHA256 del formulario:  `{URL-encoded-resourceURI} + "\n" + expiry`.  **Importante**: La clave se descodifica en Base64 y se utiliza para realizar el cálculo de HMAC-SHA256. |
| `{expiry}`  | Cadenas UTF8 para el número de segundos transcurridos desde el tiempo 00:00:00 UTC el 1 de enero de 1970.  |
| `{URL-encoded-resourceURI}`   |Codificación URL en minúsculas de `{ID_Scope}/registrations/{registration_id}` |
| `{policyName}`  | Para la API de dispositivos, esta directiva siempre es "registration". |

El siguiente fragmento de código de Python muestra una función denominada  `generate_sas_token`  que calcula el token de las entradas  `uri`, `key`, `policy_name` y `expiry` para una inscripción individual mediante un tipo de autenticación de clave simétrica.

```python

from base64 import b64encode, b64decode, encode 
from hashlib import sha256 
from time import time 
from urllib.parse import quote_plus, urlencode 
from hmac import HMAC 

 def generate_sas_token(uri, key, policy_name, expiry=3600): 
    ttl = time() + expiry 
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl)) 
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest()) 

    rawtoken = { 
        'sr' :  uri, 
        'sig': signature, 
        'se' : str(int(ttl)), 
        'skn' : policy_name 
    } 

    return 'SharedAccessSignature ' + urlencode(rawtoken) 

print(generate_sas_token("myIdScope/registrations/mydeviceregistrationid", "00mysymmetrickey", "registration"))

```

El resultado debería ser similar a la salida siguiente:

```bash

SharedAccessSignature sr=myIdScope%2Fregistrations%2Fmydeviceregistrationid&sig=SDpdbUNk%2F1DSjEpeb29BLVe6gRDZI7T41Y4BPsHHoUg%3D&se=1630175722&skn=registration 
```

En el ejemplo siguiente se muestra cómo se usa la firma de acceso compartido para autenticarse con la API de dispositivos.  

```python

curl -L -i -X PUT -H 'Content-Type: application/json' -H 'Content-Encoding:  utf-8' -H 'Authorization: [token]' -d '{"registrationId": "[registration_id]"}' https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01 

```

Si usa un grupo de inscripción basado en claves simétricas, primero debe generar una clave `device symmetric` con la clave del grupo de inscripción. Use la clave principal o secundaria del grupo de inscripción para calcular un HMAC-SHA256 del identificador de registro del dispositivo. A continuación, el resultado se convierte al formato Base64 para obtener la clave de dispositivo derivada. Para ver ejemplos de códigos, consulte [Aprovisionamiento de dispositivos mediante grupos de inscripción de clave simétrica](how-to-legacy-device-symm-key.md?tabs=linux%22%20%5Cl%20%22derive-a-device-key). Una vez derivada la clave simétrica del dispositivo, puede registrar el dispositivo mediante los ejemplos anteriores.

>[!WARNING]
>Para evitar incluir la clave maestra de grupo en el código del dispositivo, el proceso de derivación de la clave de dispositivo debe realizarse fuera del dispositivo.

### <a name="certificate-based-authentication"></a>Autenticación basada en certificados 

Si ha configurado un grupo de inscripciones o inscripción individual para la autenticación basada en certificados X.509, el dispositivo deberá usar su certificado X.509 emitido para dar fe de la API de dispositivos. Consulte los artículos siguientes sobre cómo configurar la inscripción y generar el certificado de dispositivo.

* Inicio rápido: [Aprovisionamiento de un dispositivo X.509 simulado en Azure IoT Hub mediante Python](quick-create-simulated-device-x509-python.md?tabs=linux)

* Inicio rápido: [Aprovisionamiento de un dispositivo X.509 simulado en Azure IoT Hub mediante Node.js](quick-create-simulated-device-x509-node.md)

* Inicio rápido: [Inscripción de dispositivos X.509 en Azure Device Provisioning Service mediante Python](quick-enroll-device-x509-python.md)

* Inicio rápido: [Inscripción de dispositivos X.509 en Azure Device Provisioning Service mediante Node.js](quick-enroll-device-x509-node.md)

Una vez configurada la inscripción y emitido el certificado de dispositivo, en el ejemplo siguiente se muestra cómo autenticarse en la API de dispositivos con el certificado X.509 del dispositivo.

```bash

curl -L -i -X PUT –cert ./[device_cert].pem –key ./[device_cert_private_key].pem -H 'Content-Type: application/json' -H 'Content-Encoding:  utf-8' -d '{"registrationId": "[registration_id]"}' https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01 

```

## <a name="service-api-authentication"></a>Autenticación de API de servicios

La [API de servicios](/rest/api/iot-dps/service/device-registration-state) se usa para recuperar el estado de registro y quitar los registros de dispositivos. Las aplicaciones de back-end también usan el servicio para administrar mediante programación [grupos individuales](/rest/api/iot-dps/service/individual-enrollment) y [grupos de inscripciones](/rest/api/iot-dps/service/enrollment-group). La API de servicios admite la autenticación basada en claves para aplicaciones back-end.  

Debe tener los permisos adecuados para acceder a cualquiera de los puntos de conexión de la API de servicios. Por ejemplo, una aplicación back-end tiene que incluir un token que contenga las credenciales de seguridad junto con cada mensaje que se envía al servicio.

Azure IoT Hub Device Provisioning Service concede acceso a los puntos de conexión mediante la verificación de un token con las directivas de acceso compartido. Las credenciales de seguridad, como las claves simétricas, nunca se envían en la conexión.

### <a name="access-control-and-permissions"></a>Permisos y control del acceso

Puede conceder los [permisos](#device-provisioning-service-permissions) de las maneras siguientes:

* **Directivas de autorización de acceso compartido**. Las directivas de acceso compartido pueden conceder cualquier combinación de los [permisos](#device-provisioning-service-permissions). Las directivas se pueden definir en [Azure Portal][lnk-management-portal] o mediante programación, para lo que se usan las [API de REST del servicio Device Provisioning][lnk-resource-provider-apis]. Un servicio de aprovisionamiento recién creado tiene la siguiente directiva predeterminada:

* **provisioningserviceowner**: directiva con todos los permisos. Para más detalles, vea [Permisos](#device-provisioning-service-permissions).

> [!NOTE]
> El proveedor de recursos del servicio de aprovisionamiento de dispositivos se protege mediante la suscripción de Azure, igual que todos los proveedores en [Azure Resource Manager][lnk-azure-resource-manager].

Para más información sobre cómo crear y utilizar tokens de seguridad, vea la sección siguiente.

HTTP es el único protocolo compatible e implementa la autenticación mediante la inclusión de un token válido en el encabezado de solicitud **Authorization**.

#### <a name="example"></a>Ejemplo

```bash
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Los [SDK del servicio Azure IoT Device Provisioning][lnk-sdks] generan tokens automáticamente cuando se conectan al servicio.

### <a name="security-tokens"></a>Tokens de seguridad

El servicio de aprovisionamiento de dispositivos usa tokens de seguridad para autenticar servicios para evitar el envío de claves a través de la red. Además, los tokens de seguridad están limitados en cuanto al ámbito y el período de validez. Los [SDK del servicio Azure IoT Device Provisioning][lnk-sdks] generan automáticamente tokens sin necesidad de ninguna configuración especial. Algunos escenarios, requieren que el usuario genere y utilice directamente los tokens de seguridad. Estos escenarios incluyen el uso directo de la superficie HTTP.

### <a name="security-token-structure"></a>Estructura del token de seguridad

Utilice tokens de seguridad para conceder acceso por tiempo limitado a los servicios en la funcionalidad específica del servicio de aprovisionamiento de dispositivos IoT. Para obtener autorización para conectarse al servicio de aprovisionamiento, los servicios deben enviar tokens de seguridad firmados con un acceso compartido o una clave simétrica.

Un token firmado con una clave de acceso compartido concede acceso a toda la funcionalidad asociada con los permisos de la directiva de acceso compartido. 

El token de seguridad tiene el formato siguiente:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Estos son los valores esperados:

| Value | Descripción |
| --- | --- |
| {signature} |Una cadena de firma HMAC-SHA256 con el formato: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: La clave se descodifica en Base64 y se utiliza para realizar el cálculo de HMAC-SHA256.|
| {expiry} |Cadenas UTF8 para el número de segundos transcurridos desde el tiempo 00:00:00 UTC el 1 de enero de 1970. |
| {URL-encoded-resourceURI} | Codificación de dirección URL en minúsculas del identificador URI del recurso en minúsculas. Prefijo del identificador URI (por segmento) de los puntos de conexión a los que se puede acceder con este token, que comienza por un nombre de host del servicio de aprovisionamiento de dispositivos IoT (sin protocolo). Por ejemplo, `mydps.azure-devices-provisioning.net`. |
| {policyName} |El nombre de la directiva de acceso compartido a la que hace referencia este token. |

>[!NOTE]
>el prefijo URI se calcula por segmento y no por carácter. Por ejemplo `/a/b` es un prefijo de `/a/b/c` pero `/a/bc`.

El siguiente fragmento de Node.js muestra una función denominada **generateSasToken** que calcula el token de las entradas `resourceUri, signingKey, policyName, expiresInMins`. Las secciones siguientes detallan cómo inicializar las entradas diferentes para los distintos casos de uso de token.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Como comparación, el código de Python equivalente para generar un token de seguridad es:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Puesto que el período de validez del token se valida en equipos que disponen del servicio de aprovisionamiento de dispositivos IoT, el desfase del reloj del equipo que genera el token debe ser mínimo.

### <a name="use-security-tokens-from-service-components"></a>Uso de tokens de seguridad de los componentes de servicio

Los componentes de servicio solo pueden generar tokens de seguridad mediante directivas de acceso compartido que conceden los permisos apropiados, tal y como se explicó anteriormente.

Estas son las funciones de servicio expuestas en los puntos de conexión:

| Punto de conexión | Funcionalidad |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Ofrece operaciones de inscripción de dispositivos con el servicio de aprovisionamiento de dispositivos. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Proporciona operaciones para administrar grupos de inscripción de dispositivos. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Proporciona operaciones para recuperar y administrar el estado de los registros de dispositivos. |


Por ejemplo, un servicio generado con el uso de la directiva de acceso compartido creada previamente denominada `enrollmentread` crearía un token con los parámetros siguientes:

* URI de recurso: `{mydps}.azure-devices-provisioning.net`,
* clave de firma: una de las claves de la directiva `enrollmentread` ,
* nombre de la directiva: `enrollmentread`,
* cualquier fecha de expiración.backn

![Creación de una directiva de acceso compartido para la instancia de servicio de aprovisionamiento de dispositivos en el portal][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

El resultado, que concedería acceso para leer todos los registros de inscripción, sería:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Temas de referencia:

Los siguientes temas de referencia proporcionan más información sobre el control de acceso al servicio de aprovisionamiento de dispositivos IoT.

### <a name="device-provisioning-service-permissions"></a>Permisos para el servicio de aprovisionamiento de dispositivos

En la tabla siguiente se enumeran los permisos que se pueden utilizar para controlar el acceso al servicio de aprovisionamiento de dispositivos IoT.

| Permiso | Notas |
| --- | --- |
| **ServiceConfig** |Concede acceso para cambiar las configuraciones del servicio. <br/>Este permiso se usa en servicios de back-end en la nube. |
| **EnrollmentRead** |Concede acceso de lectura de los grupos de inscripción y las inscripciones de dispositivos. <br/>Este permiso se usa en servicios de back-end en la nube. |
| **EnrollmentWrite** |Concede acceso de escritura de los grupos de inscripción y las inscripciones de dispositivos. <br/>Este permiso se usa en servicios de back-end en la nube. |
| **RegistrationStatusRead** |Concede acceso de lectura del estado de registro de los dispositivos. <br/>Este permiso se usa en servicios de back-end en la nube. |
| **RegistrationStatusWrite**  |Concede acceso de eliminación del estado de registro de los dispositivos. <br/>Este permiso se usa en servicios de back-end en la nube. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/