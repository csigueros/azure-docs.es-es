---
title: Protección de las API utilizadas como conectores de API en los flujos de usuario de registro de autoservicio de Azure AD
description: Proteja las API RESTful personalizadas que se usan como conectores de API en los flujos de usuario de registro de autoservicio.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 07/16/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f9f580c903ccc806ab4cfe5229c4931a4810bf8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481884"
---
# <a name="secure-your-api-used-an-api-connector-in-azure-ad-external-identities-self-service-sign-up-user-flows"></a>Protección de la API utilizada como conector de API en los flujos de usuarios de registro de autoservicio de Azure AD External Identities

Al integrar una API REST en un flujo de usuario de registro de autoservicio de Azure AD External Identities, debe proteger el punto de conexión de la API REST con autenticación. La autenticación de la API REST garantiza que solo los servicios que tengan credenciales adecuadas, como Azure AD, puedan realizar llamadas al punto de conexión. En este artículo se examina cómo proteger la API de REST. 

## <a name="prerequisites"></a>Requisitos previos
Complete los pasos que se incluyen en la guía [Tutorial: Incorporación de un conector de API a un flujo de usuario de registro](self-service-sign-up-add-api-connector.md).

Puede proteger el punto de conexión de la API mediante la autenticación HTTP básica o la autenticación de certificados de cliente HTTPS. En cualquier caso, debe proporcionar las credenciales que usará Azure AD al llamar al punto de conexión de la API. A continuación, el punto de conexión de la API comprueba las credenciales y realiza decisiones de autorización.


## <a name="http-basic-authentication"></a>Autenticación HTTP básica

La autenticación HTTP básica se define en [RFC 2617](https://tools.ietf.org/html/rfc2617). La autenticación básica funciona de la manera siguiente: Azure AD envía una solicitud HTTP con las credenciales del cliente (`username` y `password`) en el encabezado `Authorization`. Las credenciales tienen el formato de cadena codificada en Base 64 `username:password`. A continuación, la API es responsable de comprobar estos valores para tomar otras decisiones de autorización.

Para configurar un conector de API con autenticación básica HTTP, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En **Servicios de Azure**, seleccione **Azure AD**.
3. Seleccione **Conectores de API** y, después, el **conector de API** que quiera configurar.
4. En **Tipo de autenticación**, seleccione **Básica**.
5. Rellene los campos **Nombre de usuario** y **Contraseña** con relación al punto de conexión de la API de REST.
    :::image type="content" source="media/secure-api-connector/api-connector-config.png" alt-text="Suministro de la configuración de autenticación básica de un conector de API.":::
6. Seleccione **Guardar**.

## <a name="https-client-certificate-authentication"></a>Autenticación con certificados de cliente HTTPS

La autenticación con certificados de cliente es una autenticación mutua basada en certificados en la que el cliente, Azure AD, proporciona su certificado de cliente al servidor para demostrar su identidad. Esto sucede como parte del protocolo de enlace SSL. La API es responsable de validar que los certificados pertenecen a un cliente válido, como Azure AD, y de tomar decisiones de autorización. El certificado de cliente es un certificado digital X.509. 

> [!IMPORTANT]
> En entornos de producción, el certificado debe estar firmado por una entidad de certificación.

### <a name="create-a-certificate"></a>Crear un certificado

#### <a name="option-1-use-azure-key-vault-recommended"></a>Opción 1: Usar Azure Key Vault (recomendado)

Para crear un certificado, puede usar [Azure Key Vault](../../key-vault/certificates/create-certificate.md), que tiene opciones para certificados autofirmados e integraciones con proveedores de emisores de certificados para certificados firmados. La configuración recomendada incluye:
- **Asunto**: `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **Tipo de contenido**: `PKCS #12`
- **Tipo de Acton de duración**: `Email all contacts at a given percentage lifetime` o `Email all contacts a given number of days before expiry`
- **Tipo de clave**: `RSA`
- **Tamaño de clave**: `2048`
- **Clave privada exportable**: `Yes` (para poder exportar el archivo `.pfx`)

Después, puede [exportar el certificado](../../key-vault/certificates/how-to-export-certificate.md).

#### <a name="option-2-prepare-a-self-sized-certificate-using-powershell-module"></a>Opción 2: Preparar un certificado autofirmado mediante el módulo de PowerShell

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="configure-your-api-connector"></a>Configuración de un conector de API

Para configurar un conector de API con autenticación de certificado de cliente, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En **Servicios de Azure**, seleccione **Azure AD**.
3. Seleccione **Conectores de API** y, después, el **conector de API** que quiera configurar.
4. En **Tipo de autenticación**, seleccione **Certificado**.
5. En el cuadro **Cargar certificado**, seleccione el archivo .pfx del certificado con una clave privada.
6. En el cuadro **Escribir contraseña**, escriba la contraseña del certificado.
  :::image type="content" source="media/secure-api-connector/api-connector-upload-cert.png" alt-text="Proporcionar la configuración de autenticación de certificados para un conector de API.":::
7. Seleccione **Guardar**.

### <a name="perform-authorization-decisions"></a>Toma de decisiones de autorización 
La API debe implementar la autorización basada en certificados de cliente enviados con el fin de proteger los puntos de conexión de la API. Para obtener Azure App Service y Azure Functions, consulte [Configuración de la autenticación mutua de TLS](../../app-service/app-service-web-configure-tls-mutual-auth.md) para obtener información sobre cómo habilitar y *validar el certificado desde el código de la API*.  También puede usar Azure API Management como una capa delante de cualquier servicio de API para [comprobar las propiedades del certificado de cliente](
../../api-management/api-management-howto-mutual-certificates-for-clients.md) con los valores deseados.

### <a name="renewing-certificates"></a>Renovación de certificados
Le recomendamos que establezca alertas de aviso para cuando expire el certificado. Deberá generar un certificado nuevo y repetir los pasos anteriores cuando los certificados utilizados estén a punto de expirar. Para "implementar" el uso de un nuevo certificado, el servicio de API puede seguir aceptando certificados antiguos y nuevos temporalmente mientras se implementa el nuevo certificado. 

Para cargar un nuevo certificado en un conector de API existente, seleccione el conector de API en **Conectores de API** y haga clic en **Cargar certificado nuevo**. Azure AD usará automáticamente el certificado cargado más recientemente que no haya expirado y cuya fecha de inicio haya pasado.

  :::image type="content" source="media/secure-api-connector/api-connector-renew-cert.png" alt-text="Suministro de un nuevo certificado a un conector de API cuando ya existe uno.":::

## <a name="api-key-authentication"></a>Autenticación mediante clave de API

Algunos servicios usan un mecanismo de "clave de API" para ofuscar el acceso a los puntos de conexión HTTP durante el desarrollo. Para ello, exigen que el autor de llamada incluya una clave única como un encabezado HTTP o un parámetro de consulta HTTP. Para lograrlo en [Azure Functions](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys), incluya `code` como parámetro de consulta en la **dirección URL del punto de conexión** del conector de la API. Por ejemplo, `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>. 

No se trata de un mecanismo que se debe usar por sí solo en el entorno producción. Por lo tanto, siempre se requiere la configuración de autenticación básica o de certificado. Si no quiere implementar ningún método de autenticación (opción no recomendada) con fines de desarrollo, puede elegir la autenticación básica en la configuración del conector de la API y usar valores temporales para `username` y `password` que la API pueda omitir mientras implementa la autorización adecuada.

## <a name="next-steps"></a>Pasos siguientes
- Comience con nuestros [ejemplos de inicio rápido](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).