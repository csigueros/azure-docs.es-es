---
title: Restricciones de los URI de redirección (URL de respuesta) | Azure AD
titleSuffix: Microsoft identity platform
description: Descripción de las restricciones y limitaciones del formato del identificador URI de redirección (dirección URL de respuesta) aplicado por la Plataforma de identidad de Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/23/2021
ms.topic: conceptual
ms.subservice: develop
ms.custom: contperf-fy21q4-portal, aaddev
ms.service: active-directory
ms.reviewer: marsma, lenalepa, manrath
ms.openlocfilehash: b9484973e724246db76ccc927437fccf2c4c7be1
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112966471"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>Restricciones y limitaciones del identificador URI de redirección (dirección URL de respuesta)

Un identificador URI de redirección, o dirección URL de respuesta, es la ubicación a la que el servidor de autorización envía al usuario una vez que se haya autorizado correctamente a la aplicación y se haya concedido un código de autorización o un token de acceso. El servidor de autorización envía el código o el token al identificador URI de redirección, por lo que es importante que registre la ubicación correcta como parte del proceso de registro de la aplicación.

 Las siguientes restricciones se aplican a los URI de redireccionamiento:

* El identificador URI de redirección debe comenzar con el esquema `https`. Hay algunas [excepciones](#localhost-exceptions) para los URI de redirección de localhost.

* El identificador URI de redirección distingue entre mayúsculas y minúsculas. Sus mayúsculas o minúsculas deben coincidir con las de la ruta de acceso de la dirección URL de la aplicación en ejecución. Por ejemplo, si la aplicación incluye como parte de su ruta de acceso `.../abc/response-oidc`, no especifique `.../ABC/response-oidc` en el identificador URI de redirección. Dado que el explorador web tiene en cuenta las mayúsculas y minúsculas de la ruta de acceso, se pueden excluir las cookies asociadas con `.../abc/response-oidc` si se redirigen a la dirección URL `.../ABC/response-oidc` con mayúsculas y minúsculas no coincidentes.

* Un URI de redirección sin un segmento de ruta de acceso anexa una barra diagonal al final del URI de la respuesta. Por ejemplo, los URI https://contoso.com y http://localhost:7071 devuelven https://contoso.com/ y http://localhost:7071/ respectivamente. Esto ocurre únicamente cuando el modo de respuesta es query o fragment.

* Los URI de redirección que contienen el segmento de ruta de acceso no anexan una barra diagonal al final. En el ejemplo https://contoso.com/abc, se usará https://contoso.com/abc/response-oidc tal cual en la respuesta.

## <a name="maximum-number-of-redirect-uris"></a>Número máximo de URI de redireccionamiento

En esta tabla se muestra el número máximo de identificadores URI de redirección que puede agregar a un registro de aplicación en la Plataforma de identidad de Microsoft.

| Cuentas en las que se inicia sesión | Número máximo de URI de redireccionamiento | Descripción |
|--------------------------|---------------------------------|-------------|
| Cuentas profesionales o educativas de Microsoft en un inquilino de Azure Active Directory (Azure AD) de una organización | 256 | El campo `signInAudience` del manifiesto de aplicación está establecido en *AzureADMyOrg* o *AzureADMultipleOrgs*. |
| Cuentas personales de Microsoft y profesionales o educativas | 100 | El campo `signInAudience` del manifiesto de aplicación está establecido en *AzureADandPersonalMicrosoftAccount*. |

## <a name="maximum-uri-length"></a>Longitud máxima de URI

Puede usar un máximo de 256 caracteres para cada identificador URI de redirección que agregue a un registro de aplicación.

## <a name="supported-schemes"></a>Esquemas admitidos

En la actualidad, el modelo de aplicación de Azure Active Directory (Azure AD) admite esquemas HTTP y HTTPS para las aplicaciones que inician sesión en cuentas profesionales o educativas de Microsoft en el inquilino de Azure AD de cualquier organización. Estos tipos de cuenta se especifican mediante los valores `AzureADMyOrg` y `AzureADMultipleOrgs` en el campo `signInAudience` del manifiesto de aplicación. En el caso de las aplicaciones que inician sesión en cuentas personales de Microsoft (MSA) *y* en cuentas profesionales y educativas (es decir, que el elemento `signInAudience` está establecido en `AzureADandPersonalMicrosoftAccount`), solo se permite el esquema HTTPS.

Para agregar identificadores URI de redirección con un esquema HTTP a los registros de aplicaciones que la inician sesión de cuentas profesionales o educativas, use el editor de manifiestos de aplicación en [Registros de aplicaciones](https://go.microsoft.com/fwlink/?linkid=2083908) en Azure Portal. Sin embargo, aunque es posible establecer un identificador URI de redirección basado en HTTP mediante el editor de manifiestos, *se recomienda* usar el esquema HTTPS para los identificadores URI de redirección.

## <a name="localhost-exceptions"></a>Excepciones de localhost

Según las [secciones 8.3](https://tools.ietf.org/html/rfc8252#section-8.3) y [7.3 de RFC 8252](https://tools.ietf.org/html/rfc8252#section-7.3), los URI de redireccionamiento de "bucle invertido" o "localhost" incluyen dos consideraciones especiales:

1. Los esquemas URI `http` son aceptables porque la redirección nunca sale del dispositivo. Por tanto, ambos URI son aceptables:
    - `http://localhost/myApp`
    - `https://localhost/myApp`
1. Debido a los intervalos de puertos efímeros que suelen necesitar las aplicaciones nativas, el componente de puerto (por ejemplo, `:5001` o `:443`) se omite con el fin de buscar coincidencias con un identificador URI de redirección. Como resultado, todos estos URI se consideran equivalentes:
    - `http://localhost/MyApp`
    - `http://localhost:1234/MyApp`
    - `http://localhost:5000/MyApp`
    - `http://localhost:8080/MyApp`

Desde el punto de vista del desarrollo, esto significa algunas cosas:

* No registre varios identificadores URI de redirección en los que solo el puerto es distinto. El servidor de inicio de sesión seleccionará uno arbitrariamente y usará el comportamiento asociado a ese identificador URI de redirección (por ejemplo, si es una redirección de tipo `web`, `native` o `spa`).

    Esto es especialmente importante si desea utilizar flujos de autenticación diferentes en el mismo registro de aplicación, por ejemplo, la concesión de código de autorización y el flujo implícito. Para asociar el comportamiento de respuesta correcto con cada identificador URI de redirección, el servidor de inicio de sesión debe ser capaz de distinguir entre los identificadores URI de redirección y no puede hacerlo cuando solo difiere el puerto.
* Para registrar varios URI de redirección en localhost a fin de probar diferentes flujos durante el desarrollo, puede diferenciarlos mediante el componente *ruta de acceso* del URI. Por ejemplo, `http://localhost/MyWebApp` no coincide con `http://localhost/MyNativeApp`.
* La dirección de bucle invertido IPv6 (`[::1]`) no se admite actualmente.

#### <a name="prefer-127001-over-localhost"></a>Es preferible 127.0.0.1 frente a localhost

Para evitar la interrupción de la aplicación a causa de firewalls mal configurados o interfaces de red cuyo nombre ha cambiado, use la dirección de bucle invertido del literal de IP `127.0.0.1` en el URI de redirección en lugar de `localhost`. Por ejemplo, `https://127.0.0.1`.

Sin embargo, no puede usar el cuadro de texto **URI de redirección** en Azure Portal para agregar un URI de redirección basado en bucles que use el esquema `http`:

:::image type="content" source="media/reply-url/portal-01-no-http-loopback-redirect-uri.png" alt-text="Cuadro de diálogo de error en Azure Portal que muestra el URI de redirección del bucle invertido basado en HTTP no permitido":::

Para agregar un URI de redirección que usa el esquema `http` con la dirección de bucle invertido `127.0.0.1`, actualmente debe modificar el atributo [replyUrlsWithType en el manifiesto de la aplicación](reference-app-manifest.md#replyurlswithtype-attribute).

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>Restricciones en los caracteres comodín en los identificadores URI de redirección

Los identificadores URI con caracteres comodín como `https://*.contoso.com` pueden parecer prácticos, pero se deben evitar debido a las implicaciones de seguridad. Según la especificación de OAuth 2.0 ([sección 3.1.2 de RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), un URI de punto de conexión de redireccionamiento debe ser un URI absoluto.

Los identificadores URI con caracteres comodín no se admiten actualmente en los registros de aplicación configurados para iniciar sesión en cuentas personales de Microsoft y cuentas profesionales o educativas. Sin embargo, se permiten los identificadores URI con caracteres comodín para las aplicaciones que están configuradas para iniciar sesión solo en cuentas profesionales o educativas del inquilino de Azure AD de la organización.

Para agregar identificadores URI de redirección con caracteres comodín a los registros de aplicaciones que inician la sesión de cuentas profesionales o educativas, use el editor de manifiestos de aplicación en **Registros de aplicaciones** en Azure Portal. Aunque es posible establecer un URI de redirección con un carácter comodín usando el editor de manifiestos, se recomienda *encarecidamente* adherirse a la sección 3.1.2 de la especificación RFC 6749. y usar únicamente URI absolutos.

Si el escenario requiere más identificadores URI de redirección que el límite máximo permitido, en lugar de agregar un identificador URI de redirección con caracteres comodín, considere la posibilidad de utilizar el siguiente enfoque de parámetro de estado.

#### <a name="use-a-state-parameter"></a>Uso de un parámetro de estado

Si tiene varios subdominios y el escenario requiere, tras su correcta autenticación, redirigir a los usuarios a la misma página en la que comenzaron, puede ser útil usar un parámetro de estado.

Según este enfoque:

1. Cree un URI de redireccionamiento "compartido" por cada aplicación para procesar los tokens de seguridad que reciba del punto de conexión de autorización.
1. La aplicación puede enviar parámetros específicos de la aplicación (por ejemplo, la dirección URL del subdominio donde se originó el usuario o la información de marca) en el parámetro de estado. Cuando use un parámetro de estado, protéjase contra las infracciones de CSRF (falsificación de solicitud entre sitios), tal como se especifica en la [sección 10.12 de RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12).
1. Los parámetros específicos de la aplicación incluirán toda la información necesaria para que la aplicación represente la experiencia correcta para el usuario, es decir, para que construya el estado de la aplicación adecuado. El punto de conexión de autorización de Azure AD elimina el código HTML del parámetro de estado, por lo que debe asegurarse de no pasar contenido HTML en este parámetro.
1. Cuando Azure AD envía una respuesta al URI de redireccionamiento "compartido", enviará el parámetro de estado de vuelta a la aplicación.
1. Después, la aplicación puede usar el valor del parámetro de estado para determinar a qué dirección URL debe enviar al usuario. Asegúrese de validar la protección contra CSRF.

> [!WARNING]
> Este enfoque permite que un cliente en peligro modifique los parámetros adicionales que se envían en el parámetro de estado, con lo que se redirige al usuario a una dirección URL diferente, que es la [amenaza de redirector abierto](https://tools.ietf.org/html/rfc6819#section-4.2.4) que se describe en RFC 6819. Por lo tanto, el cliente debe proteger estos parámetros, para lo que debe cifrar el estado o comprobarlo por otros medios, por ejemplo, mediante la validación del nombre de dominio en el identificador URI de redirección con el token.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre el registro de aplicación en [Manifiesto de aplicación](reference-app-manifest.md).
