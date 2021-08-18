---
title: Protección de API mediante la autenticación de certificados de cliente en API Management
titleSuffix: Azure API Management
description: Aprenda a proteger el acceso a las API mediante certificados de cliente. Se pueden usar expresiones de directiva para validar los certificados entrantes.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2021
ms.author: apimpm
ms.openlocfilehash: 6faa78dc75b67afd4c310a94ce4a81129e09cb26
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742752"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Protección de API mediante la autenticación de certificados de cliente en API Management

API Management proporciona la capacidad de proteger el acceso a las API (es decir, de cliente a API Management) mediante certificados de cliente. Puede validar los certificados presentados por el cliente que se conecta y comprobar las propiedades del certificado con los valores deseados mediante expresiones de directiva.

Para información acerca de cómo proteger el acceso al servicio de back-end de una API mediante certificados de cliente (por ejemplo, de API Management para back-end), consulte [Cómo asegurar servicios back-end con la autenticación de certificados de cliente en Azure API Management](./api-management-howto-mutual-certificates.md)

> [!IMPORTANT]
> Para recibir y comprobar los certificados de cliente a través de HTTP/2 en los niveles Desarrollador, Básico, Estándar o Premium, debe activar la opción "Negociación del certificado de cliente" en la hoja "Dominios personalizados", tal como se muestra a continuación.

![Negociación del certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Para recibir y comprobar los certificados de cliente en el nivel Consumo, debe activar la opción "Solicitar certificado de cliente" en la hoja "Dominios personalizados", tal como se muestra a continuación.

![Solicitar certificado de cliente](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="policy-to-validate-client-certificates"></a>Directiva para validar certificados de cliente

Use la directiva [validate-client-certificate](api-management-access-restriction-policies.md#validate-client-certificate) para validar uno o varios atributos de un certificado de cliente que se usan para acceder a las API hospedadas en la instancia de API Management.

Configure la directiva para validar uno o varios atributos, incluidos el emisor de certificado, el asunto, la huella digital, si el certificado se valida con la lista de revocación en línea y otros.

Para obtener más información, consulte [Directivas de restricción de acceso de API Management](api-management-access-restriction-policies.md).

## <a name="certificate-validation-with-context-variables"></a>Validación de certificados con variables de contexto

También puede crear expresiones de directiva con la [variable `context`](api-management-policy-expressions.md#ContextVariables) para comprobar los certificados de cliente. En los ejemplos de las secciones siguientes se muestran expresiones que usan la propiedad `context.Request.Certificate` y otras propiedades `context`.

> [!IMPORTANT]
> A partir de mayo de 2021, la propiedad `context.Request.Certificate` solo solicita el certificado cuando [`hostnameConfiguration`](/rest/api/apimanagement/2020-12-01/api-management-service/create-or-update#hostnameconfiguration) de la instancia de API Management establece la propiedad `negotiateClientCertificate` en True. De manera predeterminada, `negotiateClientCertificate` se establece en False.

### <a name="checking-the-issuer-and-subject"></a>Comprobación del emisor y el asunto

Es posible configurar las directivas siguientes para comprobar el emisor y el firmante de un certificado de cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para deshabilitar la comprobación de la lista de revocación de certificados, use `context.Request.Certificate.VerifyNoRevocation()` en lugar de `context.Request.Certificate.Verify()`.
> Si el certificado de cliente es un certificado autofirmado, los certificados CA raíz (o intermedios) deben [cargarse](api-management-howto-ca-certificates.md) en API Management para que `context.Request.Certificate.Verify()` y `context.Request.Certificate.VerifyNoRevocation()` funcionen.

### <a name="checking-the-thumbprint"></a>Comprobación de la huella digital

Es posible configurar las directivas siguientes para comprobar la huella digital de un certificado de cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para deshabilitar la comprobación de la lista de revocación de certificados, use `context.Request.Certificate.VerifyNoRevocation()` en lugar de `context.Request.Certificate.Verify()`.
> Si el certificado de cliente es un certificado autofirmado, los certificados CA raíz (o intermedios) deben [cargarse](api-management-howto-ca-certificates.md) en API Management para que `context.Request.Certificate.Verify()` y `context.Request.Certificate.VerifyNoRevocation()` funcionen.

### <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Comprobación de una huella digital en relación con certificados cargados en API Management

En el ejemplo siguiente se muestra cómo comprobar la huella digital de un certificado de cliente en relación con los certificados cargados en API Management:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Para deshabilitar la comprobación de la lista de revocación de certificados, use `context.Request.Certificate.VerifyNoRevocation()` en lugar de `context.Request.Certificate.Verify()`.
> Si el certificado de cliente es un certificado autofirmado, los certificados CA raíz (o intermedios) deben [cargarse](api-management-howto-ca-certificates.md) en API Management para que `context.Request.Certificate.Verify()` y `context.Request.Certificate.VerifyNoRevocation()` funcionen.

> [!TIP]
> El problema de interbloqueo de certificados de cliente descrito en este [artículo](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) puede manifestarse de varias maneras, por ejemplo, las solicitudes se inmovilizan, las solicitudes resultan en un código de estado `403 Forbidden` después de agotar el tiempo, `context.Request.Certificate` es `null`. Este problema afecta normalmente a las solicitudes `POST` y `PUT` con la longitud del contenido de aproximadamente 60 KB o más.
> Para evitar que este problema se produzca, active "Negociar certificado de cliente" para los nombres de host deseados en la hoja "Dominios personalizados", tal como se muestra en la primera imagen de este documento. Esta característica no está disponible en el nivel Consumo.

## <a name="next-steps"></a>Pasos siguientes

-   [Cómo asegurar servicios back-end con la autenticación de certificados de cliente](./api-management-howto-mutual-certificates.md)
-   [¿Cómo se cargan certificados?](./api-management-howto-mutual-certificates.md)
