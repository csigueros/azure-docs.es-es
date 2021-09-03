---
title: Autenticación de clientes de publicación de Azure Event Grid mediante claves de acceso o firmas de acceso compartido
description: En este artículo se describe cómo autenticar clientes de publicación de Azure Event Grid mediante claves de acceso y firmas de acceso compartido.
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 5a271372afcc36df97d0a7afa37ee0b065a5a5e6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780396"
---
# <a name="authenticate-azure-event-grid-publishing-clients-using-access-keys-or-shared-access-signatures"></a>Autenticación de clientes de publicación de Azure Event Grid mediante claves de acceso o firmas de acceso compartido
En este artículo se proporciona información sobre la autenticación de clientes que publican eventos en temas, dominios, espacios de nombres de los asociados de Azure Event Grid mediante **clave de acceso** o token de **Firma de acceso compartido (SAS)** . 

> [!IMPORTANT]
> Autenticar y autorizar a usuarios o aplicaciones con identidades de Azure AD proporciona una seguridad y facilidad de uso superiores en lo que respecta a la autenticación basada en claves y en firmas de acceso compartido (SAS). Con Azure AD, no es preciso almacenar los secretos que se utilizan para la autenticación en el código y arriesgarse a posibles vulnerabilidades en la seguridad. Se recomienda encarecidamente usar Azure AD con las aplicaciones de publicación de eventos de Azure Event Grid. Para más información, consulte el artículo sobre la [autenticación de clientes de publicación mediante Azure Active Directory](authenticate-with-active-directory.md).

## <a name="authenticate-using-access-key"></a>Autenticación mediante una clave de acceso
La autenticación de clave es la forma más sencilla de autenticación. Puede pasar la clave de acceso como un encabezado HTTP o un parámetro de consulta de dirección URL. 

### <a name="access-key-in-an-http-header"></a>Clave de acceso en un encabezado HTTP
Pase la clave de acceso como un valor para el encabezado HTTP: `aeg-sas-key`.

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>Clave de acceso como parámetro de consulta
También puede especificar `aeg-sas-key` como parámetro de consulta. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Para obtener instrucciones sobre cómo obtener las claves de acceso para un tema o un dominio, consulte [Obtener claves de acceso](get-access-keys.md).

## <a name="authenticate-using-sas"></a>Autenticación mediante SAS 
Los tokens de SAS para Event Grid incluyen el recurso, un tiempo de expiración y una firma. El formato del token de SAS es: `r={resource}&e={expiration}&s={signature}`.

El recurso es la ruta de acceso del tema de Event Grid al que envía los eventos. Por ejemplo, una ruta de acceso de recurso válida es: `https://<yourtopic>.<region>.eventgrid.azure.net/api/events`. Para ver todas las versiones de API compatibles, vea [Tipos de recursos de Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). 

En primer lugar, genere un token de SAS mediante programación y, a continuación, use el encabezado de `aeg-sas-token` o el encabezado de `Authorization SharedAccessSignature` para autenticarse con Event Grid. 

### <a name="generate-sas-token-programmatically"></a>Generar tokens de SAS mediante programación
En el ejemplo siguiente se crea un token de SAS para su uso con Event Grid:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>Uso del encabezado de token AEG-SAS
Este es un ejemplo de cómo pasar el token de SAS como un valor para el encabezado `aeg-sas-token`. 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Uso del encabezado de autorización
Este es un ejemplo de cómo pasar el token de SAS como un valor para el encabezado `Authorization`. 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>Pasos siguientes
Consulte [Autenticación de entrega de eventos](security-authentication.md) para obtener información sobre la autenticación con controladores de eventos para entregar eventos. 
