---
title: Protección del back-end de API en API Management mediante OAuth 2.0 y Azure Active Directory
titleSuffix: Azure API Management
description: Aprenda a proteger el acceso de un usuario a un back-end de API web en Azure API Management y el portal de desarrollador con la autorización de usuario de OAuth 2.0 y Azure Active Directory.
services: api-management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 09/17/2021
ms.author: danlep
ms.custom: contperf-fy21q1
ms.openlocfilehash: 6a6a83ef0e810880ff14ddc8125235229db75787
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676188"
---
# <a name="protect-a-web-api-backend-in-azure-api-management-using-oauth-20-authorization-with-azure-active-directory"></a>Protección de un back-end de API web en Azure API Management mediante la autorización de OAuth 2.0 con Azure Active Directory 

En este artículo se muestra cómo configurar la instancia de [Azure API Management](api-management-key-concepts.md) para proteger una API mediante el [protocolo OAuth 2.0 con Azure Active Directory (Azure AD)](../active-directory/develop/active-directory-v2-protocols.md). 

> [!NOTE]
> Esta característica está disponible en los niveles **Desarrollador**, **Básico**, **Estándar** y **Premium** de API Management.  
> 
> Puede seguir cada paso siguiente en el nivel **Consumo**, excepto para llamar a la API desde el portal para desarrolladores.

## <a name="prerequisites"></a>Requisitos previos

Para seguir los pasos de este artículo, debe tener:

- Una instancia de API Management
- Una API publicada mediante la instancia API Management
- Un inquilino de Azure AD

## <a name="overview"></a>Información general

:::image type="content" source="media/api-management-howto-protect-backend-with-aad/overview-graphic-2021.png" alt-text="Gráfico de información general para conceptualizar visualmente el flujo siguiente.":::

1. Registre una aplicación (aplicación back-end) en Azure AD que represente la API.

1. Registre otra aplicación (aplicación cliente) en Azure AD que represente una aplicación de cliente que deba llamar a la API.

1. En Azure AD, conceda permisos para que la aplicación cliente llame a la aplicación back-end.

1. Configure la consola del desarrollador en el portal para desarrolladores y así llamar a la API con la autorización de usuario de OAuth 2.0.

1. Agregue la directiva **validate-jwt** para validar el token de OAuth para todas las solicitudes entrantes.

## <a name="1-register-an-application-in-azure-ad-to-represent-the-api"></a>1. Registro de una aplicación en Azure AD que represente la API

Mediante Azure Portal, proteja una API con Azure AD; para ello, registre una aplicación que represente la API en Azure AD. 

Para obtener detalles sobre el registro de aplicaciones, vea [Inicio rápido: Configuración de una aplicación para exponer las API web](../active-directory/develop/quickstart-configure-app-expose-web-apis.md).

1. En [Azure Portal](https://portal.azure.com), busque y seleccione **Registros de aplicaciones**.

1. Seleccione **Nuevo registro**. 

1. Cuando aparece la página **Registrar una aplicación**, escriba la información de registro de la aplicación:

   - En la sección **Nombre**, escriba un nombre significativo para la aplicación, que se mostrará a los usuarios de la aplicación, por ejemplo, *backend-app*. 
   - En la sección **Tipos de cuenta admitidos**, seleccione una opción que se ajuste a su escenario. 

1. Deje vacía la sección [**URI de redireccionamiento**](../active-directory/develop/reply-url.md).

1. Seleccione **Registrar** para crear la aplicación. 

1. En la página **Información general** de la aplicación, busque el valor de **Id. de aplicación (cliente)** y regístrelo para usarlo más tarde.

1. En la sección **Administrar** del menú lateral, seleccione **Exponer una API** y configure el **URI del id. de aplicación** con el valor predeterminado. Anote este valor para más adelante.

1. Seleccione el botón **Agregar un ámbito** para mostrar la página **Agregar un ámbito**:
    1. Ingrese un **nombre de ámbito**, un **nombre para mostrar del consentimiento del administrador** y la **descripción del consentimiento del administrador**.
    1. Asegúrese de seleccionar el estado del ámbito **Habilitado**.

1. Seleccione el botón **Agregar ámbito** para crear el ámbito. 

1. Repita los pasos 8 y 9 para agregar todos los ámbitos que admita la API.

1. Cuando se creen los ámbitos, anótelos para usarlos en un paso posterior. 

## <a name="2-register-another-application-in-azure-ad-to-represent-a-client-application"></a>2. Registro de otra aplicación en Azure AD que represente una aplicación cliente

Registre todas las aplicaciones cliente que llamen a la API como aplicación en Azure AD. En este ejemplo, la aplicación cliente es la **consola del desarrollador** en el portal para desarrolladores de API Management. 

Para registrar otra aplicación en Azure AD que represente la consola del desarrollador:

1. En [Azure Portal](https://portal.azure.com), busque y seleccione **Registros de aplicaciones**.

1. Seleccione **Nuevo registro**.

1. Cuando aparece la página **Registrar una aplicación**, escriba la información de registro de la aplicación:

   - En la sección **Nombre**, escriba un nombre significativo para la aplicación, que se mostrará a los usuarios de la aplicación, por ejemplo, *client-app*. 
   - En la sección **Tipos de cuenta admitidos**, seleccione **Cuentas en cualquier directorio organizativo (cualquier directorio de Azure AD: multiinquilino)** . 

1. En la sección **URI de redirección**, seleccione `Web` y deje el campo de dirección URL vacío por ahora.

1. Seleccione **Registrar** para crear la aplicación. 

1. En la página **Información general** de la aplicación, busque el valor de **Id. de aplicación (cliente)** y regístrelo para usarlo más tarde.

1. Cree un secreto de cliente para esta aplicación que se usará en un paso posterior.

   1. En la sección **Administrar** del menú lateral, seleccione **Certificados y secretos**.
   1. En **Secretos de cliente**, seleccione **Nuevo secreto de cliente**.
   1. En **Agregar un secreto de cliente**, escriba la **Descripción** y elija cuándo debe caducar la clave.
   1. Seleccione **Agregar**.

Cuando se cree el secreto, anote el valor de clave para usarlo en un paso posterior. 

## <a name="3-grant-permissions-in-azure-ad"></a>3. Concesión de permisos en Azure AD

Ahora que hemos registrado dos aplicaciones que representan la API y la consola del desarrollador, conceda permisos para que la aplicación cliente pueda llamar a la aplicación back-end.  

1. En [Azure Portal](https://portal.azure.com), busque y seleccione **Registros de aplicaciones**.

1. Seleccione la aplicación cliente. Después, en la lista de páginas de la aplicación, seleccione **Permisos de API**.

1. Seleccione **Agregar un permiso**.

1. En **Seleccionar una API**, seleccione **Mis API** y busque y seleccione la aplicación de back-end.

1. Seleccione **Permisos delegados** y, a continuación, seleccione los permisos apropiados para la aplicación de back-end.

1. Seleccione **Agregar permisos**.

Opcionalmente:
1. Vaya a la página de **permisos de API** de la aplicación cliente.

1. Seleccione **Conceder el consentimiento del administrador para \<your-tenant-name>** y así conceder el consentimiento en nombre de todos los usuarios de este directorio. 

## <a name="4-enable-oauth-20-user-authorization-in-the-developer-console"></a>4. Habilitación de la autorización de usuario OAuth 2.0 en la consola del desarrollador

En este punto ya se han creado nuestras aplicaciones en Azure AD y se han concedido los permisos pertinentes para que la aplicación cliente pueda llamar a la aplicación back-end. 

En este ejemplo, habilitará la autorización de usuario de OAuth 2.0 en la consola del desarrollador (la aplicación cliente).

1. En Azure Portal, busque la dirección **URL del punto de conexión de autorización** y la **URL del punto de conexión del token** y guárdelas para más adelante. 
    1. Abra la página **Registros de aplicaciones**. 
    1. Seleccione **Puntos de conexión**.
    1. Copie el **Punto de conexión de autorización de OAuth 2.0** y el **punto de conexión del token de OAuth 2.0**. 

1. Vaya a la instancia de API Management.

1. En la sección **Portal para desarrolladores** del menú lateral, seleccione **OAuth 2.0 y OpenID Connect**. 

1. En la pestaña **OAuth 2.0**, seleccione **Agregar**.

1. Proporcione un **Nombre para mostrar** y una **Descripción**.

1. Como **URL de la página de registro de cliente**, escriba un valor de marcador de posición como `http://localhost`. 
    * La **dirección URL de la página de registro del cliente** señala a una página en la que los usuarios pueden crear y configurar sus propias cuentas compatibles con los proveedores de OAuth 2.0. 
    * Usaremos un marcador de posición, ya que, en este ejemplo, los usuarios no crean ni configuran sus propias cuentas.

1. En **Tipos de concesión de autorización**, seleccione **Código de autorización**.

1. Especifique la **dirección URL del punto de conexión de autorización** y la **URL del punto de conexión de token** que guardó con anterioridad: 
    1. Copie el **punto de conexión de autorización de OAuth 2.0** y péguelo en el cuadro de texto **URL del punto de conexión de autorización**. 
    1. Seleccione **POST** en Método de solicitud de autorización.
    1. Escriba el **punto de conexión de token de OAuth 2.0** y péguelo en el cuadro de texto **URL de punto de conexión del token**. 
        * Si usa puntos de conexión **v1**:
          * Agregue un parámetro de cuerpo denominado **Recurso**.
          * Como valor del parámetro, escriba el **id. de aplicación** correspondiente a la aplicación de back-end.
        * Si usa puntos de conexión **v2**:
          * Use el ámbito de la aplicación de back-end que creó en el campo **Ámbito predeterminado**.
          * Establezca el valor de la propiedad [`accessTokenAcceptedVersion`](../active-directory/develop/reference-app-manifest.md#accesstokenacceptedversion-attribute) en `2` en el [manifiesto de aplicación](../active-directory/develop/reference-app-manifest.md).
          

   >[!IMPORTANT]
   > Si bien puede utilizar puntos de conexión **v1** o **v2**, le recomendamos que utilice los puntos de conexión v2. 

1. A continuación, especifique las credenciales de aplicación del cliente:
    * Como **Id. de cliente**, use el **id. de aplicación** de la aplicación cliente.
    * Como **secreto de cliente**, use la clave que creó para la aplicación cliente. 

1. Anote el **URI de redireccionamiento** correspondiente al tipo de concesión del código de autorización.

1. Seleccione **Crear**.

1. Vuelva al registro de la aplicación cliente. 
 
1. En **Administrar**, seleccione **Autenticación**.

1. En **Configuraciones de plataforma**:
    * Haga clic en **Agregar una plataforma**.
    * Seleccione el tipo como **Web**. 
    * Pegue el URI de redireccionamiento que guardó anteriormente en la sección para **URI de redireccionamiento**.
    * Haga clic en el botón **Configurar** para guardar.

   Ahora que la consola del desarrollador puede obtener tokens de acceso de Azure AD a través del servidor de autorización de OAuth 2.0, habilite la autorización de usuario de OAuth 2.0 para la API. Esto permite que la consola del desarrollador sepa que debe obtener un token de acceso en nombre del usuario antes de realizar llamadas a la API.

15. Vaya a la instancia de API Management y a **API**.

1. Seleccione la API que desea proteger. Por ejemplo, `Echo API`.

1. Vaya a **Configuración**.

1. En **Seguridad**:
    1. Elija **OAuth 2.0**.
    1. Seleccione el servidor OAuth 2.0 que configuró anteriormente. 

1. Seleccione **Guardar**.

## <a name="5-successfully-call-the-api-from-the-developer-portal"></a>5. Llamada correcta a la API desde el portal para desarrolladores

> [!NOTE]
> Esta sección no es aplicable al nivel de **consumo**, ya que no es compatible con el portal para desarrolladores.

Ahora que la autorización de usuario OAuth 2.0 está habilitada en la API, la consola del desarrollador obtendrá un token de acceso en nombre del usuario antes de llamar a la API.

1. Vaya a cualquier operación de la API en el portal para desarrolladores. 
1. Seleccione **Probar** para acceder a la Consola para desarrolladores.

1. Tenga en cuenta el nuevo elemento de la sección **Autorización** correspondiente al servidor de autorización que acaba de agregar.

1. Seleccione el **Código de autorización** de la lista desplegable de autorización. 
1. Tendrá que iniciar sesión en el inquilino de Azure AD. 
    * Si ya ha iniciado sesión con la cuenta, es posible que no se le indique nada.

1. Al iniciar sesión correctamente, un encabezado `Authorization` se agregará a la solicitud con un token de acceso de Azure AD. El siguiente es un token de ejemplo (con codificación Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

1. Seleccione **Enviar** para llamar a la API correctamente.

## <a name="6-configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>6. Configurar una directiva de validación de JWT para autorizar previamente las solicitudes

Por ahora ha realizado lo siguiente:
* Ha intentado realizar una llamada desde la consola del desarrollador.
* Ha iniciado sesión en el inquilino de Azure AD. 
* La consola del desarrollador obtiene un token de acceso en su nombre e incluye el token en la solicitud realizada a la API.

Sin embargo, ¿qué ocurre si alguien llama a la API sin un token o con uno no válido? Por ejemplo, si llama a la API sin el encabezado `Authorization`, la llamada seguirá pasando, ya que API Management no valida el token de acceso. Simplemente pasa el encabezado `Authorization` a la API de back-end.

Use la directiva de [validación de JWT](./api-management-access-restriction-policies.md#ValidateJWT) para autorizar previamente las solicitudes en API Management validando los tokens de acceso de cada solicitud entrante. Si una solicitud no tiene un token válido, API Management la bloquea. 

Cuando se agrega la siguiente directiva de ejemplo a la sección de directiva `<inbound>`, se comprueba el valor de la notificación de audiencia en un token de acceso obtenido de Azure AD y se devuelve un mensaje de error si el token no es válido. 


```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/v2.0/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>insert aud claim value expected in the token</value>
        </claim>
    </required-claims>
</validate-jwt>
```

> [!NOTE]
> La dirección URL `openid-config` anterior corresponde al punto de conexión v2. En cuanto al punto de conexión v1 `openid-config`, use `https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration`.

> [!TIP] 
> Busque el valor **{aad-tenant}** como id. de inquilino de Azure AD en Azure Portal, ya sea en:
> * La página de información general del recurso de Azure AD.
> * La página **Administrar> Propiedades** del recurso de Azure AD.

Para información sobre cómo configurar directivas, consulte el artículo sobre [edición o establecimiento de directivas](./set-edit-policies.md).

## <a name="build-an-application-to-call-the-api"></a>Compilación de una aplicación para llamar a la API

En esta guía, se utiliza la consola del desarrollador en API Management como la aplicación de cliente de ejemplo para llamar a `Echo API` protegido por OAuth 2.0. Para obtener más información sobre cómo compilar una aplicación e implementar OAuth 2.0, consulte [Ejemplos de código de Azure AD](../active-directory/develop/sample-v2-code.md).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre [Azure AD Y OAuth2.0](../active-directory/develop/authentication-vs-authorization.md).
- Consulte más [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre la administración de API.
- Para conocer otras formas de proteger el servicio back-end, consulte [Autenticación de certificado mutua](./api-management-howto-mutual-certificates.md).
- [Creación de una instancia del servicio de API Management](./get-started-create-service-instance.md).
- [Administración de su primera API en Administración de API de Azure](./import-and-publish.md).
