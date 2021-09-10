---
title: Tutorial para configurar Azure Active Directory B2C con Nok Nok
titleSuffix: Azure AD B2C
description: Tutorial para configurar Nok Nok con Azure Active Directory B2C con el fin de habilitar la autenticación FIDO2 sin contraseña
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/04/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 586c8373e32f210d6f2f53c773fbe58ef5a2181b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780499"
---
# <a name="tutorial-configure-nok-nok-with-azure-active-directory-b2c-to-enable-passwordless-fido2-authentication"></a>Tutorial: Configuración de Nok Nok con Azure Active Directory B2C para habilitar la autenticación FIDO2 sin contraseña

En este tutorial de ejemplo aprenderá a integrar el conjunto de autenticación Nok Nok S3 en su inquilino de Azure Active Directory (AD) B2C. [Nok Nok](https://noknok.com/) habilita la autenticación multifactor certificada por FIDO, como FIDO UAF, FIDO U2F, WebAuthn y FIDO2, para aplicaciones móviles y web. Usando Nok Nok, los clientes pueden mejorar su posición de seguridad a la vez que equilibran la experiencia del usuario.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

- Suscripción a Azure. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).

- [Un inquilino de Azure AD B2C](tutorial-create-tenant.md) vinculado a la suscripción de Azure.

- Obtenga un [inquilino de prueba](https://noknok.com/products/strong-authentication-service/) gratuito de Nok Nok.

## <a name="scenario-description"></a>Descripción del escenario

Si desea habilitar la autenticación FIDO sin contraseña para sus usuarios, habilite Nok Nok como proveedor de identidades en su inquilino de Azure AD B2C. La integración de Nok Nok incluye los siguientes componentes:

- **Azure AD B2C:** servidor de autorización responsable de comprobar las credenciales del usuario.

- **Aplicaciones web y móviles:** aplicaciones web o móviles que usted decide proteger con Nok Nok y Azure AD B2C.

- **SDK de la aplicación Nok Nok o aplicación Nok Nok Passport:** aplicaciones que se usan para autenticar aplicaciones compatibles con Azure AD B2C. Estas aplicaciones están disponibles en el [App Store de Apple](https://apps.apple.com/us/app/nok-nok-passport/id1050437340) y en [Google Play Store](https://play.google.com/store/apps/details?id=com.noknok.android.passport2&hl=en&gl=US).

En el siguiente diagrama de arquitectura se muestra la implementación. Nok Nok actúa como proveedor de identidades para Azure AD B2C usando Open ID Connect (OIDC) para habilitar la autenticación sin contraseña.

![Imagen en la que se muestra el diagrama de arquitectura de Nok Nok y Azure AD B2C](./media/partner-nok-nok/nok-nok-architecture-diagram.png)

| Paso | Descripción |
|:------|:-----------|
| 1. | El usuario llega a una página de inicio de sesión. En ella inicia sesión o se registra y escribe su nombre de usuario. |
| 2. | Azure AD B2C redirige al usuario al proveedor de autenticación OIDC de Nok Nok. |
| 3a. | Para las autenticaciones basadas en dispositivos móviles, Nok Nok muestra un código QR o envía una solicitud de notificación push al dispositivo móvil del usuario final. |
| 3b. | En el caso del inicio de sesión basado en PC o equipos de escritorio, Nok Nok redirige al usuario final a la página de inicio de sesión de la aplicación web, para iniciar una solicitud de autenticación sin contraseña. |
|4a. | El usuario digitaliza el código QR mostrado en su smartphone usando el SDK de la aplicación Nok Nok o la aplicación Nok Nok Passport.|
| 4b. | El usuario proporciona el nombre de usuario como entrada en la página de inicio de sesión de la aplicación web y selecciona "Siguiente". |
| 5a. | Se solicita al usuario que se autentique en su smartphone. <BR> El usuario realiza la autenticación sin contraseña usando su método preferido, como biometría, el PIN del dispositivo o cualquier autenticador de itinerancia.|
| 5b. | Se solicita al usuario que se autentique en la aplicación web. <BR> El usuario realiza la autenticación sin contraseña usando su método preferido, como biometría, el PIN del dispositivo o cualquier autenticador de itinerancia. |
| 6. | El servidor Nok Nok valida la aserción FIDO y, tras la validación, envía la respuesta de autenticación OIDC a Azure AD B2C.|
| 7. | En función de la respuesta, se concede o se deniega el acceso al usuario. |

## <a name="onboard-with-nok-nok"></a>Incorporación con Nok Nok

Rellene el [formulario en la nube de Nok Nok](https://noknok.com/contact/) crear su propio inquilino de la plataforma. Una vez que envíe el formulario, recibirá un correo electrónico en el que se explica cómo acceder al inquilino. El correo electrónico también incluirá acceso a las guías de Nok Nok. Siga las instrucciones que se proporcionan en la guía de integración de Nok Nok para completar la configuración de OIDC de su inquilino en la nube de Nok Nok.

## <a name="integrate-with-azure-ad-b2c"></a>Integración con Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Incorporación de un nuevo proveedor de identidades

Para agregar un nuevo proveedor de identidades, siga estos pasos:

1. Inicie sesión en **[Azure Portal](https://portal.azure.com/#home)** como administrador global del inquilino de Azure AD B2C.

2. Asegúrese de usar el directorio que contiene el inquilino de Azure AD B2C. Para ello, seleccione el filtro **Directorio y suscripción** en el menú superior y luego el directorio que contiene el inquilino.

3. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, busque y seleccione **Azure AD B2C**.

4. Vaya a **Panel** > **Azure Active Directory B2C** >  **Proveedores de identidad**.

5. Seleccione **Proveedores de identidades**.

6. Seleccione **Agregar**.

### <a name="configure-an-identity-provider"></a>Configuración de un proveedor de identidades 

Para configurar un proveedor de identidades, siga estos pasos:

1. Seleccione **Tipo de proveedor de identidades** > **OpenID Connect (versión preliminar)** .
2. Rellene el formulario para configurar el proveedor de identidades:

   |Propiedad | Valor |
   |:-----| :-----------|
   | Nombre   | Proveedor de autenticación Nok Nok |
   | URL de metadatos | Inserte el URI de la aplicación de autenticación Nok Nok hospedada, seguido de la ruta de acceso específica, por ejemplo, "https://demo.noknok.com/mytenant/oidc/.well-known/openid-configuration". |
   | Secreto del cliente | Use el secreto de cliente proporcionado por la plataforma Nok Nok.|
   | Id. de cliente | Use el Id. de cliente proporcionado por la plataforma Nok Nok.|
   | Ámbito | Correo electrónico del perfil de OpenID |
   | Tipo de respuesta | código |
   | Modo de respuesta | form_post|

3. Seleccione **Aceptar**.

4. Seleccione **Asignar las notificaciones de este proveedor de identidades**.

5. Rellene el formulario para asignar el proveedor de identidades:

   |Propiedad | Value |
   |:-----| :-----------|
   | UserID    | Desde la suscripción. |
   | Nombre para mostrar | Desde la suscripción. |
   | Modo de respuesta | Desde la suscripción. |

6. Seleccione **Guardar** para completar la configuración del nuevo proveedor de identidades OIDC.

### <a name="create-a-user-flow-policy"></a>Creación de una directiva de flujo de usuario

Nok Nok debería aparecer como nuevo proveedor de identidades OIDC entre sus proveedores de identidades de B2C.

1. En el inquilino de Azure AD B2C, en **Directivas**, seleccione **Flujos de usuario**.

2. Seleccione **Nuevo** flujo de usuario.

3. Seleccione **Registrarse e iniciar sesión**, seleccione una **versión** y, a continuación, seleccione **Crear**.

4. Escriba un **nombre** para la directiva.

5. En la sección "Proveedores de identidades", seleccione el proveedor de identidades Nok Nok recién creado.

6. Configure los parámetros del flujo de usuario. Inserte un nombre y seleccione el proveedor de identidades que creó. También puede agregar una dirección de correo electrónico. En este caso, Azure no redirigirá el procedimiento de inicio de sesión directamente a Nok Nok, sino que mostrará una pantalla en la que el usuario podrá elegir la opción que desee usar.

7. Deje el campo **Autenticación multifactor** con el valor que tiene.

8. Seleccione **Aplicar directivas de acceso condicional**.

9. En **atributos de usuario y notificaciones de token**, seleccione **Dirección de correo electrónico** en la opción Recopilar atributo. Puede agregar todos los atributos que Azure AD puede recopilar sobre el usuario junto con las notificaciones que Azure AD B2C puede devolver a la aplicación cliente.

10. Seleccione **Crear**.

11. Tras un proceso de creación correcto, seleccione el nuevo **Flujo de usuario**.

12. En el panel izquierdo, seleccione **Notificaciones de aplicación**. En opciones, marque la casilla **correo electrónico** y seleccione **Guardar**.

## <a name="test-the-user-flow"></a>Prueba del flujo de usuario

1. Abra el inquilino de Azure AD B2C y, en Directivas, seleccione Identity Experience Framework.

2. Seleccione el valor de SignUpSignIn que creó anteriormente.

3. Seleccione Ejecutar flujo de usuario y elija la configuración:

   a. Aplicación: seleccione la aplicación registrada (el ejemplo es JWT).

   b. URL de respuesta: seleccione la dirección URL de redireccionamiento.

   c. Seleccione Ejecutar flujo de usuario.

4. Recorra el flujo de registro y cree una cuenta.

5. Se llamará a Nok Nok durante el flujo, una vez creado el atributo de usuario. Si el flujo está incompleto, compruebe que el usuario no esté guardado en el directorio.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los artículos siguientes:

- [Directivas personalizadas de Azure AD B2C](./custom-policy-overview.md)

- [Introducción a las directivas personalizadas en Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
