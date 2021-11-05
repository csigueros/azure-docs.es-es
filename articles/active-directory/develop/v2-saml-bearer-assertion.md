---
title: Plataforma de identidad de Microsoft y flujo de aserción de portador SAML | Azure
description: Obtenga información acerca de cómo capturar datos de Microsoft Graph sin pedir al usuario las credenciales mediante el flujo de aserción de portador SAML.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2021
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 1641d3390f2377065d21d8648f0c14256a0c9955
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091753"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Plataforma de identidad de Microsoft y flujo de aserción de portador SAML de OAuth 2.0
El flujo de aserción de portador SAML de OAuth 2.0 le permite solicitar un token de acceso de OAuth mediante una aserción SAML cuando un cliente tiene que usar una relación de confianza existente. La firma aplicada a la aserción SAML proporciona la autenticación de la aplicación autorizada. Una aserción SAML es un token de seguridad XML emitido por un proveedor de identidades y consumido por un proveedor de servicios. El proveedor de servicios se basa en su contenido para identificar el asunto de la aserción con fines relacionados con la seguridad.

La aserción SAML se envía al punto de conexión del token de OAuth.  El punto de conexión procesa la aserción y emite un token de acceso basado en la aprobación anterior de la aplicación. No es necesario que el cliente tenga o almacene un token de actualización, ni que el secreto de cliente deba pasarse al punto de conexión del token.

El flujo de aserción de portador SAML es útil cuando se recuperan datos de Microsoft Graph API (que solo admiten permisos delegados) sin pedir las credenciales al usuario. En este escenario, la concesión de credenciales de cliente, que se prefiere para los procesos en segundo plano, no funciona.

En el caso de las aplicaciones que realizan un inicio de sesión interactivo basado en el explorador para obtener una aserción SAML y agregar acceso a una API protegida por OAuth (como Microsoft Graph), puede realizar una solicitud OAuth para obtener un token de acceso para la API. Cuando el explorador se redirige a Azure Active Directory (Azure AD) para autenticar al usuario, el explorador seleccionará la sesión desde el inicio de sesión de SAML y el usuario no tendrá escribir sus credenciales.

También se admite el flujo de aserción de portador SAML de OAuth t para los usuarios que se autentican con proveedores de identidades como Servicios de federación de Active Directory (AD FS) (ADFS) federados para Azure AD.  La aserción SAML obtenida de ADFS puede usarse en un flujo de OAuth para autenticar al usuario.

![Flujo de OAuth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Gráfico de llamadas con aserción de portador SAML
Ahora, vamos a comprender cómo podemos capturar realmente aserciones SAML mediante programación. El enfoque mediante programación se prueba con ADFS. Sin embargo, el enfoque funciona con cualquier proveedor de identidades que admita la devolución de aserción de SAML mediante programación. El proceso básico es: obtener una aserción SAML, obtener un token de acceso y acceder a Microsoft Graph.

### <a name="prerequisites"></a>Prerrequisitos

Establezca una relación de confianza entre el servidor o entorno de autorización (Microsoft 365) y el proveedor de identidades o el emisor de la aserción de portador SAML 2.0. Para configurar ADFS para el inicio de sesión único y como proveedor de identidades, consulte [Configuración de AD FS y habilitación del inicio de sesión único para Office 365](/archive/blogs/canitpro/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365).

Registre la aplicación en el [portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Inicie sesión en la [página de registro de aplicaciones del portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (tenga en cuenta usamos los puntos de conexión v2.0 para Graph API y, por tanto, es necesario registrar la aplicación en Azure Portal. De lo contrario, podríamos haber usado los registros en Azure AD). 
1. Seleccione **Nuevo registro**.
1. Cuando aparece la página **Registrar una aplicación**, escriba la información de registro de la aplicación: 
    1. **Nombre**: escriba un nombre significativo para la aplicación, que se mostrará a los usuarios de la aplicación.
    1. **Tipos de cuentas compatibles**: seleccione qué cuentas desea que admita la aplicación.
    1. **URI de redirección (opcional)** : Seleccione el tipo de aplicación que se va a crear, Web o Cliente público (móvil y escritorio) y, luego, escriba el URI de redireccionamiento (o la dirección URL de respuesta) para la aplicación.
    1. Cuando termine, seleccione **Registrar**.
1. Tome nota del id. de aplicación (cliente).
1. En el panel izquierdo, seleccione **Certificados y secretos**. Haga clic en **Nuevo secreto de cliente** en la sección **Secretos de cliente**. Copie el nuevo secreto de cliente; no podrá recuperarlo cuando salga de la página.
1. En el panel izquierdo, seleccione **Permisos de API** y, luego, **Agregar un permiso**. Seleccione **Microsoft Graph**, luego, **permisos delegados** y, luego, seleccione **Tasks.read**, ya que tenemos previsto usar la Graph API de Outlook. 

Instale [Postman](https://www.getpostman.com/), una herramienta necesaria para probar las solicitudes de ejemplo.  Posteriormente, puede convertir las solicitudes en código.

### <a name="get-the-saml-assertion-from-adfs"></a>Obtención de la aserción SAML desde ADFS
Cree una solicitud POST al punto de conexión de ADFS mediante un sobre SOAP para capturar la aserción SAML:

![Obtención de la aserción SAML](./media/v2-saml-bearer-assertion/2.png)

Valores de encabezado:

![Valores de encabezado](./media/v2-saml-bearer-assertion/3.png)

Cuerpo de la solicitud de ADFS:

![Cuerpo de la solicitud de ADFS](./media/v2-saml-bearer-assertion/4.png)

Una vez que la solicitud se publique correctamente, debe recibir una aserción SAML de ADFS. Solo se requieren los datos de la etiqueta **SAML:Assertion**; conviértalos a la codificación en base 64 para usarlos en solicitudes posteriores.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Obtención del token de OAuth2 mediante la aserción SAML 

Capturar un token de OAuth2 mediante la respuesta de aserción de ADFS.

1. Cree una solicitud POST como se muestra a continuación con los valores de encabezado:

    ![Solicitud POST](./media/v2-saml-bearer-assertion/5.png)
1. En el cuerpo de la solicitud, reemplace **client_id**, **client_secret** y **assertion** (la aserción SAML codificada en base 64 que obtuvo en el paso anterior):

    ![Cuerpo de la solicitud](./media/v2-saml-bearer-assertion/6.png)
1. Tras la solicitud correcta, recibirá un token de acceso del directorio activo de Azure.

### <a name="get-the-data-with-the-oauth2-token"></a>Obtener los datos con el token de OAuth2

Después de recibir el token de acceso, llame a las Graph API (tareas de Outlook en este ejemplo). 

1. Cree una solicitud GET con el token de acceso capturado en el paso anterior:

    ![Solicitud GET](./media/v2-saml-bearer-assertion/7.png)

1. Tras una solicitud correcta, recibirá una respuesta JSON.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el registro de aplicaciones y el flujo de autenticación, consulte:

- [Registro de una aplicación con la plataforma de identidad de Microsoft](quickstart-register-app.md)
- [Flujos de autenticación y escenarios de aplicaciones](authentication-flows-app-scenarios.md)
