---
title: Habilitación de dominios personalizados de Azure AD B2C
titleSuffix: Azure AD B2C
description: Aprenda a habilitar dominios personalizados en las direcciones URL de redireccionamiento para Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/16/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b64806b3683db8f6cd3ec665b462f4f6f26397eb
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770157"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Habilitación de dominios personalizados para Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

En este artículo se describe cómo habilitar dominios personalizados en las direcciones URL de redireccionamiento de Azure Active Directory B2C (Azure AD B2C). El uso de un dominio personalizado con la aplicación proporciona una experiencia de usuario más fluida. Desde la perspectiva del usuario, permanecen en el dominio durante el proceso de inicio de sesión en lugar de redirigirse al dominio predeterminado de Azure AD B2C *&lt;nombre-de-inquilino&gt;.b2clogin.com*.

![En la captura de pantalla se muestra una experiencia de usuario de dominio personalizado de Azure AD B2C.](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Información general sobre dominios personalizados

Puede habilitar dominios personalizados para Azure AD B2C mediante [Azure Front Door](https://azure.microsoft.com/services/frontdoor/). Azure Front Door es un punto de entrada global que usa la red perimetral global de Microsoft para crear aplicaciones web rápidas, seguras y muy escalables. Puede representar el contenido de Azure AD B2C detrás de Azure Front Door y, después, configurar una opción en Azure Front Door para ofrecer el contenido a través de un dominio personalizado en la dirección URL de la aplicación.

En el diagrama siguiente se muestra la integración de Azure Front Door:

1. Desde una aplicación, un usuario selecciona el botón de inicio de sesión, que le lleva a la página de inicio de sesión de Azure AD B2C. Esta página especifica un nombre de dominio personalizado.
1. El explorador web resuelve el nombre de dominio personalizado en la dirección IP de Azure Front Door. Durante la resolución DNS, un registro de nombre canónico (CNAME) con un nombre de dominio personalizado apunta al host de front-end predeterminado de Front Door (por ejemplo, `contoso.azurefd.net`). 
1. El tráfico dirigido al dominio personalizado (por ejemplo, `login.contoso.com`) se enruta al host de front-end predeterminado de Front Door especificado (`contoso.azurefd.net`).
1. Azure Front Door invoca el contenido de Azure AD B2C mediante el dominio predeterminado de Azure AD B2C `<tenant-name>.b2clogin.com`. La solicitud al punto de conexión de Azure AD B2C contiene el nombre de dominio personalizado original.
1. Azure AD B2C responde a la solicitud mostrando el contenido pertinente y el dominio personalizado original.

![En el diagrama se muestra el flujo de red de dominio personalizado.](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> La conexión desde el explorador a Azure Front Door siempre debe utilizar IPv4 en lugar de IPv6.

Al utilizar dominios personalizados, tenga en cuenta lo siguiente:

- Puede configurar varios dominios personalizados. Para conocer el número máximo de dominios personalizados admitidos, consulte [Restricciones y límites del servicio Azure AD](../active-directory/enterprise-users/directory-service-limits-restrictions.md) para Azure AD B2C y [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) para Azure Front Door.
- Azure Front Door es un servicio de Azure independiente, por lo que se aplicarán cargos adicionales. Para más información, consulte [Precios de Azure Front Door](https://azure.microsoft.com/pricing/details/frontdoor).
- Para utilizar el [firewall de aplicaciones web](../web-application-firewall/afds/afds-overview.md) en Azure Front Door, debe confirmar que la configuración y las reglas del firewall funcionen correctamente con los flujos de usuario de Azure AD B2C.
- Después de configurar dominios personalizados, los usuarios seguirán teniendo acceso al nombre de dominio predeterminado de Azure AD B2C *&lt;nombre-de-inquilino&gt;.b2clogin.com* (a menos que esté utilizando una directiva personalizada y [bloquee el acceso](#block-access-to-the-default-domain-name)).
- Si tiene varias aplicaciones, migre todas al dominio personalizado porque el explorador almacena la sesión de Azure AD B2C con el nombre de dominio que se está utilizando actualmente.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant"></a>Paso 1. Adición de un nombre de dominio personalizado a su inquilino de Azure AD B2C

Cada inquilino nuevo de Azure AD B2C incluye un nombre de dominio inicial, &lt;nombrededominio&gt;.onmicrosoft.com. No se puede cambiar ni eliminar el nombre de dominio inicial, pero puede agregar un dominio personalizado. 

Siga estos pasos para agregar un dominio personalizado a su inquilino de Azure AD B2C:

1. [Agregue su nombre de dominio personalizado a Azure AD](../active-directory/fundamentals/add-custom-domain.md#add-your-custom-domain-name-to-azure-ad).

    > [!IMPORTANT]
    > En estos pasos, asegúrese de iniciar sesión en el inquilino de **Azure AD B2C** y seleccione el servicio **Azure Active Directory**.

1. [Agregue la información de DNS al registrador de dominios](../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar). Después de agregar su nombre de dominio personalizado a Azure AD, cree un registro `TXT` o `MX` de DNS para su dominio. La creación de este registro de DNS para el dominio comprueba la propiedad del nombre de dominio.

    En los siguientes ejemplos, se muestran los registros TXT para *login.contoso.com* y *account.contoso.com*:

    |Nombre (nombre de host)  |Tipo  |Datos  |
    |---------|---------|---------|
    |login   | TXT  | MS=ms12345678  |
    |account | TXT  | MS=ms87654321  |
    
    El registro TXT debe estar asociado al subdominio o nombre de host del dominio. Por ejemplo, la parte de *inicio de sesión* del dominio *contoso.com*. Si el nombre de host está vacío o `@`, Azure AD no podrá comprobar el dominio personalizado que haya agregado. En los ejemplos siguientes, ambos registros están configurados incorrectamente.
    
    |Nombre (nombre de host)  |Tipo  |Datos  |
    |---------|---------|---------|
    | | TXT  | MS=ms12345678  |
    | @ | TXT  | MS=ms12345678  | 
    
    > [!TIP]
    > Puede administrar el dominio personalizado con cualquier servicio DNS disponible públicamente, como GoDaddy. Si no tiene un servidor DNS, puede usar la [zona DNS de Azure](../dns/dns-getstarted-portal.md) o los [dominios de App Service](../app-service/manage-custom-dns-buy-domain.md).

1. [Compruebe el nombre de su dominio personalizado](../active-directory/fundamentals/add-custom-domain.md#verify-your-custom-domain-name). Compruebe cada subdominio o nombre de host que vaya a utilizar. Por ejemplo, para poder iniciar sesión con *login.contoso.com* y *account.contoso.com*, debe comprobar ambos subdominios y no el dominio de nivel superior *contoso.com*. 

    Una vez comprobado el dominio, **elimine** el registro TXT de DNS creado.

    
## <a name="step-2-create-a-new-azure-front-door-instance"></a>Paso 2. Creación de una nueva instancia de Azure Front Door

Siga estos pasos para crear una instancia de Front Door para el inquilino de Azure AD B2C. Para obtener más información, consulte [Creación de una instancia de Front Door para su aplicación](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application).
  

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione **Directorio + suscripción** y elija el directorio que contiene la suscripción de Azure que quiera usar para Azure Front Door. *No* debe ser el directorio que contiene el inquilino de Azure AD B2C.
1. En la página principal o en el menú de Azure Portal, seleccione **Crear un recurso**. Seleccione **Redes** > **Ver todas** > **Front Door**.
1. En la pestaña **Conceptos básicos** de la página **Crear una instancia de Front Door**, escriba o seleccione la siguiente información y, a continuación, seleccione **Siguiente: Configuration** (Siguiente: Configuración).

    | Configuración | Value |
    | --- | --- |
    | **Suscripción** | Seleccione la suscripción a Azure. |    
    | **Grupo de recursos** | Seleccione un grupo de recursos existente o seleccione **Crear** para crear uno.|
    | **Ubicación del grupo de recursos** | seleccione la ubicación del grupo de recursos. Por ejemplo, **Centro de EE. UU**. |

### <a name="21-add-frontend-host"></a>2.1 Adición de un host de front-end

El host de front-end es el nombre de dominio que usa la aplicación. Cuando se crea una instancia de Front Door, el host de front-end predeterminado es un subdominio de `azurefd.net`.

Azure Front Door permite asociar un dominio personalizado al host de front-end. Con esta opción, asociará la interfaz de usuario de Azure AD B2C a un dominio personalizado en la dirección URL en lugar de un nombre de dominio propiedad de Front Door. Por ejemplo, `https://login.contoso.com`.

Para agregar un host de front-end, siga estos pasos:

1. En **Front-ends/dominios**, seleccione **+** para abrir **Agregar un host de front-end**.
1. En **Nombre de host**, escriba un nombre de host único global. El nombre de host no es el dominio personalizado. En este ejemplo se usa *contoso-frontend*. Seleccione **Agregar**.

    ![En la captura de pantalla se muestra cómo agregar un host de front-end.](./media/custom-domain/add-frontend-host-azure-front-door.png)

### <a name="22-add-backend-and-backend-pool"></a>2.2 Adición de un grupo de back-end y un back-end

Un back-end hace referencia a su [nombre de inquilino de Azure AD B2C](tenant-management.md#get-your-tenant-name), `tenant-name.b2clogin.com`. Para agregar un grupo de back-end, siga estos pasos:

1. Todavía en **Crear una instancia de Front Door**, en **Grupos de back-end**, seleccione **+** para abrir **Agregar un grupo de back-end**.

1. Escriba un **nombre**. Por ejemplo, *myBackendPool*. Seleccione **Agregar un back-end**.
    
    En la captura de pantalla siguiente, se muestra cómo crear un grupo de back-end:
    
    ![En la captura de pantalla se muestra cómo agregar un grupo de back-end.](./media/custom-domain/front-door-add-backend-pool.png)

1. En la hoja **Agregar un back-end**, seleccione la información siguiente y, después, **Agregar**.

    | Parámetro | Value |
    | --- | --- |
    | **Tipo de host de back-end**| Seleccione **Host personalizado**.| 
    | **Nombre de host de back-end**| Seleccione el nombre de su [Azure AD B2C](tenant-management.md#get-your-tenant-name), `<tenant-name>.b2clogin.com`. Por ejemplo, contoso.b2clogin.com.|
    | **Encabezado host de back-end**.| Seleccione el mismo valor que ha seleccionado para **Nombre de host de back-end**.|
    
    **Deje todos los demás campos con el valor predeterminado.*
    
    En la captura de pantalla siguiente, se muestra cómo crear un back-end de host personalizado asociado a un inquilino de Azure AD B2C:
    
    ![En la captura de pantalla se muestra cómo agregar un back-end de host personalizado.](./media/custom-domain/add-a-backend.png)

1. Para completar la configuración del grupo de back-end, en la hoja **Agregar un grupo de back-end**, seleccione **Agregar**.

1. Después de agregar el **back-end** al **grupo de back-end**, deshabilite los **sondeos de estado**.

    ![En la captura de pantalla se muestra cómo agregar un grupo de back-end y deshabilitar los sondeos de estado.](./media/custom-domain/add-a-backend-pool.png)

### <a name="23-add-a-routing-rule"></a>2.3 Adición de una regla de enrutamiento

Por último, agregue una regla de enrutamiento. Una regla de enrutamiento asigna el host de front-end al grupo de back-end. La regla reenvía una solicitud para el [host de front-end](#21-add-frontend-host) al [back-end](#22-add-backend-and-backend-pool) de Azure AD B2C. Para agregar una regla de enrutamiento, siga estos pasos:

1. En **Agregar una regla**, en **Nombre**, escriba *LocationRule*. Acepte todos los valores predeterminados y, a continuación, seleccione **Agregar** para agregar la regla de enrutamiento.
1. Seleccione **Revisar y crear** y, a continuación, **Crear**.
    
     ![En la captura de pantalla se muestra cómo crear una instancia de Azure Front Door.](./media/custom-domain/configuration-azure-front-door.png)


## <a name="step-3-set-up-your-custom-domain-on-azure-front-door"></a>Paso 3. Configuración del dominio personalizado en Azure Front Door

En este paso, agregará el dominio personalizado que registró en el [paso 1](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant) a Front Door. 

### <a name="31-create-a-cname-dns-record"></a>3.1 Creación de un registro de DNS de CNAME

Para poder usar un dominio personalizado con Front Door, antes hay que crear un registro de nombre canónico (CNAME) con su proveedor de dominios para señalar a su host de front-end predeterminado de Front Door (como por ejemplo, contoso.azurefd.net).

Un registro CNAME es un tipo de registro de DNS que asigna un nombre de dominio de origen a un nombre de dominio de destino (alias). En el caso de Azure Front Door, el nombre del dominio de origen es su nombre de dominio personalizado, mientras que el nombre del dominio de destino es el nombre de host predeterminado de Front Door que configuró en el [paso 2.1](#21-add-frontend-host). 

Una vez que Front Door compruebe el registro CNAME que ha creado, el tráfico dirigido al dominio personalizado de origen (como login.contoso.com) se enrutará al host del front-end predeterminado de Front Door del destino especificado, como `contoso.azurefd.net`. Para obtener más información, consulte [Adición de un dominio personalizado a una instancia de Front Door](../frontdoor/front-door-custom-domain.md). 

Para crear un registro CNAME para un dominio personalizado:

1. Inicie sesión en el sitio web del proveedor de dominios de su dominio personalizado.

1. Para encontrar la página de administración de los registros DNS, consulte la documentación del proveedor o busque las áreas del sitio web con la etiqueta **Nombre de dominio**, **DNS** o **Name Server Management** (Administración del servidor de nombres). 

1. Cree una entrada de registro CNAME para el dominio personalizado y rellene los campos como se muestra en la tabla siguiente (los nombres de campo pueden variar):

    | Source          | Tipo  | Destination           |
    |-----------------|-------|-----------------------|
    | `<login.contoso.com>` | CNAME | `contoso.azurefd.net` |

   - Origen: escriba el nombre de dominio personalizado (por ejemplo, login.contoso.com).

   - Escriba:  Escriba *CNAME*.

   - Destino: escriba el host de front-end de Front Door que ha creado en el [paso 2.1](#21-add-frontend-host). Debe tener el siguiente formato: _&lt;nombre de host&gt;_ .azurefd.net. Por ejemplo, `contoso.azurefd.net`.

1. Guarde los cambios.

### <a name="32-associate-the-custom-domain-with-your-front-door"></a>3.2 Asociación del dominio personalizado a Front Door

Una vez que haya registrado un dominio personalizado, puede agregarlo a Front Door.
    
1. En la página **Diseñador de Front Door**, en **Front-ends/dominios**, seleccione **+** para agregar un dominio personalizado.

    ![En la captura de pantalla se muestra cómo agregar un dominio personalizado.](./media/custom-domain/azure-front-door-add-custom-domain.png) 
    
1. En **Host de front-end**, el host de front-end que se usa como dominio de destino de su registro CNAME se rellena previamente y se deriva de Front Door: *&lt;nombre de host predeterminado&gt;* .azurefd.NET. No se puede modificar.

1. Para **Nombre de host personalizado**, escriba el dominio personalizado, incluido el subdominio, que se usará como el dominio de origen del registro CNAME. Por ejemplo, login.contoso.com.

    ![En la captura de pantalla se muestra cómo comprobar un dominio personalizado.](./media/custom-domain/azure-front-door-add-custom-domain-verification.png)

    Azure comprueba que el registro CNAME existe para el nombre de dominio personalizado que ha especificado. Si el registro CNAME es correcto, el dominio personalizado se valida.

    
1. Una vez comprobado el nombre de dominio personalizado, en **Custom domain name HTTPS** (Nombre de dominio personalizado HTTPS), seleccione **Habilitado**. 
    
    ![En la captura de pantalla se muestra cómo habilitar HTTPS mediante un certificado de Azure Front Door.](./media/custom-domain/azure-front-door-add-custom-domain-https-settings.png)

1. En **Tipo de administración de certificados**, seleccione [Administración de Front Door](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door) o [Usar mi propio certificado](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate). Si elige la opción *Front Door administrado*, espere hasta que el certificado esté completamente aprovisionado.

1. Seleccione **Agregar**.

### <a name="33-update-the-routing-rule"></a>3.3 Adición de la regla de enrutamiento

1. En **Reglas de enrutamiento**, seleccione la regla de enrutamiento que creó en el [paso 2.3](#23-add-a-routing-rule).

    ![En la captura de pantalla se muestra cómo seleccionar una regla de enrutamiento.](./media/custom-domain/select-routing-rule.png)

1. En **Front-ends/dominios**, seleccione su nombre de dominio personalizado.
    
    ![En la captura de pantalla se muestra cómo actualizar la regla de enrutamiento de Azure Front Door.](./media/custom-domain/update-routing-rule.png)

1. Seleccione **Actualizar**.
1. En la ventana principal, seleccione **Guardar**.

## <a name="step-4-configure-cors"></a>Paso 4. Configuración de CORS

Si [personaliza la interfaz de usuario de Azure AD B2C](customize-ui-with-html.md) con una plantilla HTML, debe [configurar CORS](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) con su dominio personalizado.

Para configurar Azure Blob Storage para el uso compartido de recursos entre orígenes (CORS), realice los siguientes pasos:

1. En [Azure Portal](https://portal.azure.com), vaya a la cuenta de almacenamiento.
1. En el menú, seleccione **CORS**.
1. En **Orígenes permitidos**, escriba `https://your-domain-name`. Reemplace `your-domain-name` por el nombre de dominio. Por ejemplo, `https://login.contoso.com`. Al escribir su nombre de inquilino, use solo minúsculas.
1. En **Métodos permitidos**, seleccione `GET` y `OPTIONS`.
1. En **Encabezados permitidos**, escriba un asterisco (*).
1. En **Encabezados expuestos**, escriba un asterisco (*).
1. Para **Antigüedad máxima**, introduzca 200.
1. Seleccione **Guardar**.

## <a name="test-your-custom-domain"></a>Prueba de un dominio personalizado

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el filtro **Directorio y suscripción** en el menú superior y, luego, elija el directorio que contiene el inquilino de Azure AD B2C.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. En **Directivas**, seleccione **Flujos de usuario (directivas)** .
1. Seleccione un flujo de usuario y luego **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación web denominada *webapp1* que registró anteriormente. La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Copie la dirección URL de **Ejecutar punto de conexión de flujo de usuario**.

    ![En la captura de pantalla se muestra cómo copiar el URI de la solicitud de autorización.](./media/custom-domain/user-flow-run-now.png)

1. Para simular un inicio de sesión con el dominio personalizado, abra un explorador web y use la dirección URL que copió. Reemplace el dominio de Azure AD B2C ( _&lt;nombre-de-inquilino&gt;_ .b2clogin.com) por el dominio personalizado.   

    Por ejemplo, en lugar de:

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    utilice:

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```

1. Compruebe que Azure AD B2C se haya cargado correctamente. Después, inicie sesión con una cuenta local.
1. Repita la prueba con el resto de las directivas.

## <a name="configure-your-identity-provider"></a>Configuración del proveedor de identidades

Cuando un usuario elige iniciar sesión con un proveedor de identidades sociales, Azure AD B2C inicia una solicitud de autorización y lleva al usuario al proveedor de identidades seleccionado para completar el proceso de inicio de sesión. La solicitud de autorización especifica `redirect_uri` con el nombre de dominio predeterminado de Azure AD B2C: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

Si ha configurado la directiva para permitir el inicio de sesión con un proveedor de identidades externo, actualice los URI de redirección de OAuth con el dominio personalizado. La mayoría de los proveedores de identidades permiten registrar varios URI de redirección. Se recomienda agregar los URI de redirección en lugar de reemplazarlos para poder probar la directiva personalizada sin que afecte a las aplicaciones que utilizan el nombre de dominio predeterminado de Azure AD B2C. 

En el siguiente URI de redirección:

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- Reemplace **&lt;nombre-de-dominio-personalizado&gt;** por el nombre de dominio personalizado.
- Reemplace **&lt;nombre-de-inquilino&gt;** con el nombre del inquilino o el identificador del inquilino.

En el ejemplo siguiente se muestra un URI de redirección de OAuth válido:

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

Si decide usar el [identificador de inquilino](#optional-use-tenant-id), un URI de redirección de OAuth válido tendría un aspecto similar al siguiente:

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

Los metadatos de los [proveedores de identidades de SAML](saml-identity-provider-technical-profile.md) tendrían el siguiente aspecto:

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="configure-your-application"></a>Configuración de la aplicación 

Después de configurar y probar el dominio personalizado, puede actualizar las aplicaciones para cargar la dirección URL que especifica su dominio personalizado como nombre de host en lugar del dominio de Azure AD B2C. 

La integración del dominio personalizado se aplica a los puntos de conexión de autenticación que usan directivas de Azure AD B2C (flujos de usuario o directivas personalizadas) para autenticar a los usuarios. Estos puntos de conexión pueden parecerse a lo siguiente:

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Sustituya:
- **dominio-personalizado** por el nombre de dominio personalizado.
- **nombre-de-inquilino** por el nombre o el identificador de inquilino.
- **nombre-de-directiva** por el nombre de la directiva. [Obtenga más información sobre las directivas de Azure AD B2C](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


Los metadatos del [proveedor de servicios de SAML](./saml-service-provider.md) pueden tener un aspecto similar al siguiente: 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>(Opcional) Uso del identificador de inquilino

Puede reemplazar el nombre del inquilino de B2C en la dirección URL por el GUID del identificador de inquilino para quitar todas las referencias a "b2c" en la dirección URL. Puede encontrar el GUID del identificador de inquilino en la página Información general de B2C en Azure Portal.
Por ejemplo, cambie `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` a `https://account.contosobank.co.uk/<tenant ID GUID>/`.

Si decide utilizar el identificador de inquilino en lugar del nombre de inquilino, asegúrese de actualizar los **URI de redirección de OAuth** del proveedor de identidades en consecuencia. Para más información, consulte [Configuración del proveedor de identidades](#configure-your-identity-provider).

### <a name="token-issuance"></a>Emisión de token

La notificación del nombre del emisor del token (ISS) cambia en función del dominio personalizado que se utiliza. Por ejemplo:

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Bloquear el acceso al nombre de dominio predeterminado

Después de agregar el dominio personalizado y configurar la aplicación, los usuarios podrán seguir accediendo al dominio &lt;nombre-de-inquilino&gt;.b2clogin.com. Para impedir el acceso, puede configurar la directiva para comprobar la solicitud de autorización "nombre de host" en una lista de dominios permitidos. El nombre de host es el nombre de dominio que aparece en la dirección URL. El nombre de host está disponible mediante [solucionadores de notificaciones](claim-resolver-overview.md) `{Context:HostName}`. A continuación, puede presentar un mensaje de error personalizado. 

1. Obtenga el ejemplo de una directiva de acceso condicional que comprueba el nombre de host de [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name).
1. En cada archivo, reemplace la cadena `yourtenant` por el nombre del inquilino de Azure AD B2C. Por ejemplo, si el nombre del inquilino de B2C es *contosob2c*, todas las instancias de `yourtenant.onmicrosoft.com` se convierten en `contosob2c.onmicrosoft.com`.
1. Cargue los archivos de directivas en el orden siguiente: `B2C_1A_TrustFrameworkExtensions_HostName.xml` y luego `B2C_1A_signup_signin_HostName.xml`.

::: zone-end

## <a name="troubleshooting"></a>Solución de problemas

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C devuelve un error de página no encontrada

- **Síntoma**: después de configurar un dominio personalizado, cuando intenta iniciar sesión con el dominio personalizado, recibe un mensaje de error HTTP 404.
- **Causas posibles**: este problema puede estar relacionado con la configuración de DNS o la configuración de back-end de Azure Front Door. 
- **Solución:**  
    1. Asegúrese de que el dominio personalizado esté [registrado y comprobado correctamente](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant) en el inquilino de Azure AD B2C.
    1. Asegúrese de que el [dominio personalizado](../frontdoor/front-door-custom-domain.md) esté correctamente configurado. El registro `CNAME` del dominio personalizado debe apuntar al host de front-end predeterminado de Azure Front Door (por ejemplo, contoso.azurefd.net).
    1. Asegúrese de que la [configuración del grupo de back-end de Azure Front Door](#22-add-backend-and-backend-pool) apunte al inquilino donde haya configurado el nombre de dominio personalizado y donde se almacenen el flujo de usuario o las directivas personalizadas.


### <a name="azure-ad-b2c-returns-the-resource-you-are-looking-for-has-been-removed-had-its-name-changed-or-is-temporarily-unavailable"></a>Azure AD B2C indica que el recurso que está buscando se ha quitado, se le ha cambiado el nombre o no está disponible temporalmente.

- **Síntoma**: después de configurar un dominio personalizado, cuando intenta iniciar sesión con el dominio personalizado, aparece un mensaje de error en el que se le indica que *el recurso que busca se ha quitado, se le ha cambiado el nombre o no está disponible temporalmente*.
- **Causas posibles**: este problema podría estar relacionado con la comprobación del dominio personalizado de Azure AD. 
- **Resolución**: asegúrese de que el dominio personalizado esté [registrado y **comprobado correctamente**](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant) en el inquilino de Azure AD B2C.

### <a name="identify-provider-returns-an-error"></a>El proveedor de identidades devuelve un error

- **Síntoma**: después de configurar un dominio personalizado, podrá iniciar sesión con las cuentas locales. Sin embargo, cuando inicia sesión con credenciales de [proveedores de identidades sociales o empresariales](add-identity-provider.md) externos, los proveedores de identidades presentan un mensaje de error.
- **Causas posibles**: cuando Azure AD B2C toma el usuario para iniciar sesión con un proveedor de identidades federado, especifica el URI de redirección. El URI de redirección es el punto de conexión en el que el proveedor de identidades devuelve el token. El URI de redirección es el mismo dominio que utiliza la aplicación con la solicitud de autorización. Si el URI de redirección no está aún registrado en el proveedor de identidades, es posible que no confíe en el nuevo URI de redirección, lo que genera un mensaje de error. 
- **Solución**: siga los pasos descritos en [Configuración del proveedor de identidades](#configure-your-identity-provider) para agregar el nuevo URI de redirección. 

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>¿Puedo usar la configuración avanzada de Azure Front Door, como las *reglas de firewall de aplicaciones web*? 
  
Aunque las opciones de configuración avanzada de Azure Front Door no se admiten oficialmente, puede utilizarlas bajo su responsabilidad. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>Cuando utilizo Ejecutar ahora para intentar ejecutar mi directiva, ¿por qué no veo el dominio personalizado?

Copie la dirección URL, cambie el nombre de dominio manualmente y péguelo de nuevo en el explorador.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>¿Qué dirección IP se presenta para Azure AD B2C? ¿La dirección IP del usuario o la dirección IP de Azure Front Door?

Azure Front Door pasa la dirección IP original del usuario. Esta es la dirección IP que verá en los informes de auditoría o en la directiva personalizada.

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>¿Puedo utilizar un firewall de aplicaciones web (WAF) de terceros con B2C?

Para usar su propio firewall de aplicaciones web delante de Azure Front Door, debe configurar y asegurarse de que todo funciona correctamente con los flujos de usuario de Azure AD B2C o con las directivas personalizadas.  

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las [solicitudes de autorización de OAuth](protocols-overview.md).
