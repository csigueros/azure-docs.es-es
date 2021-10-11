---
title: ¿Qué es Azure Active Directory B2C?
description: Obtenga información sobre cómo puede usar Azure Active Directory B2C para admitir identidades externas en las aplicaciones, como el inicio de sesión de redes sociales con Facebook, Google y otros proveedores de identidades.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 916c5191acb0f7245c929c6ef1db5fca4fc3841b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352470"
---
# <a name="what-is-azure-active-directory-b2c"></a>¿Qué es Azure Active Directory B2C?

Azure Active Directory B2C proporciona la identidad de negocio a cliente como servicio. Los clientes usan las identidades de la cuenta de redes sociales, corporativa o local preferidas para acceder al inicio de sesión único para sus aplicaciones y API.

![Infografía de proveedores de identidades de Azure AD B2C y aplicaciones de nivel inferior](./media/overview/azureadb2c-overview.png)

Azure AD B2C es una solución de administración de acceso de identidades de clientes (CIAM) capaz de admitir millones de usuarios y miles de millones de autenticaciones al día. Asimismo, se encarga del escalado y la seguridad de la plataforma de autenticación, de la supervisión y del control automático de amenazas, como la denegación del servicio, la difusión de contraseñas o los ataques por fuerza bruta.

Azure AD B2C es un servicio independiente de [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Se basa en la misma tecnología que Azure AD, pero la usa para un propósito diferente: permitir a las empresas crear aplicaciones orientadas al cliente y, a continuación, permitir que cualquiera se suscriba a esas aplicaciones sin restricciones en la cuenta de usuario.
   
## <a name="who-uses-azure-ad-b2c"></a>¿Quién usa Azure AD B2C?
Cualquier empresa o persona que quiera permitir que cualquier usuario se suscriba a sus aplicaciones web o móviles. 

## <a name="custom-branded-identity-solution"></a>Solución de identidad de marca personalizada

Azure AD B2C es una solución de autenticación de marca blanca. Puede personalizar toda la experiencia del usuario con su marca para que se fusione sin problemas con las aplicaciones web y móviles.

Personalice todas las páginas mostradas por Azure AD B2C cuando los usuarios se registren, inicien sesión y modifiquen su información de perfil. Personalice el HTML, CSS y JavaScript en los recorridos del usuario para que la experiencia de Azure AD B2C tenga la apariencia de una parte nativa de la aplicación.

![Páginas de registro e inicio de sesión personalizadas e imagen de fondo](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Acceso de inicio de sesión único con una identidad proporcionada por el usuario

Azure AD B2C utiliza protocolos de autenticación basados en estándares, como OpenID Connect, OAuth 2.0 y el Lenguaje de marcado de aserción de seguridad (SAML). Se integra con la mayoría de las aplicaciones modernas y el software comercial.

:::image type="content" source="./media/overview/scenario-singlesignon.png" alt-text="Diagrama de identidades de terceros que se federan en Azure AD B2C.":::

Al actuar como la entidad de autenticación central de las aplicaciones web, las aplicaciones móviles y las API, Azure AD B2C le permite crear una solución de inicio de sesión único (SSO) para todas ellas. Centralice la recopilación de información de perfiles de usuario y preferencias, y capture el análisis detallado sobre el comportamiento de inicio de sesión y la conversión de registros.

## <a name="integrate-with-external-user-stores"></a>Integración con almacenes de usuarios externos

Azure AD B2C proporciona un directorio que puede contener 100 atributos personalizados por usuario. Sin embargo, también puede integrarse con sistemas externos. Por ejemplo, use Azure AD B2C para la autenticación, pero delegue en una base de datos externa de administración de relaciones con el cliente (CRM) o de fidelización de clientes como el origen de veracidad para los datos de los clientes.

Otro escenario de almacenamiento de usuarios externo consiste en que Azure AD B2C se encargue de la autenticación de la aplicación, pero se integre con un sistema externo que almacena el perfil de usuario o los datos personales. Por ejemplo, para satisfacer los requisitos de residencia de datos, como las directivas de almacenamiento de datos regionales o locales. Sin embargo, el servicio Azure AD B2C está disponible en todo el mundo a través de la nube pública de Azure. 

:::image type="content" source="./media/overview/scenario-remoteprofile.png" alt-text="Diagrama lógico de Azure AD B2C en el que se comunica con un almacén de usuario externo.":::

Azure AD B2C puede facilitar la recopilación de la información del usuario durante el registro o la edición de perfiles y, a continuación, colocar los datos en el sistema externo mediante las API. Después, durante las autenticaciones futuras, Azure AD B2C puede recuperar los datos del sistema externo y, si es necesario, incluirlos como parte de la respuesta del token de autenticación que envía a la aplicación.

## <a name="progressive-profiling"></a>Generación de perfiles progresiva

Otra opción de recorrido del usuario incluye la generación de perfiles progresiva. La generación de perfiles progresiva permite a los clientes completar rápidamente la primera transacción mediante la recopilación de una cantidad mínima de información. Posteriormente, se recopilarán gradualmente más datos de perfil del cliente en los inicios de sesión futuros.

:::image type="content" source="./media/overview/scenario-progressive.png" alt-text="Representación visual de la generación de perfiles progresiva.":::

## <a name="third-party-identity-verification-and-proofing"></a>Comprobación y prueba de identidades de terceros

Use Azure AD B2C para facilitar la comprobación de identidades y su prueba mediante la recopilación de datos de usuario y, a continuación, pasarlos a un sistema de terceros para realizar la validación, la puntuación de confianza y la aprobación para la creación de cuentas de usuario.


:::image type="content" source="./media/overview/scenario-idproofing.png" alt-text="Diagrama que muestra el flujo de usuario para la prueba de identidad de terceros.":::

Estas son solo algunas de las cosas que puede hacer con Azure AD B2C como plataforma de identidad de negocio a cliente. Las siguientes secciones de esta información general le guiarán por una aplicación de demostración que usa Azure AD B2C. También va a pasar directamente a una [introducción general técnica más detallada de Azure AD B2C](technical-overview.md).

## <a name="example-woodgrove-groceries"></a>Ejemplo: WoodGrove Groceries

[WoodGrove Groceries][woodgrove] es una aplicación web activa creada por Microsoft para mostrar varias características de Azure AD B2C. En las siguientes secciones se van a revisar algunas de las opciones de autenticación proporcionadas por Azure AD B2C al sitio web de WoodGrove.

### <a name="business-overview"></a>Información general empresarial

WoodGrove es una tienda de comestibles en línea que vende comestibles tanto a clientes individuales como a clientes empresariales. Los clientes empresariales compran los comestibles en nombre de la empresa u organizaciones que administran.

### <a name="sign-in-options"></a>Opciones de inicio de sesión

WoodGrove Groceries ofrece varias opciones de inicio de sesión basadas en la relación que tienen sus clientes con la tienda:

* Los clientes **individuales** pueden registrarse o iniciar sesión con cuentas individuales, como un proveedor de identidades de redes sociales o una dirección de correo electrónico y una contraseña.
* Los clientes **empresariales** pueden registrarse o iniciar sesión con las credenciales de empresa.
* Los **asociados** y proveedores son usuarios que suministran a la tienda de comestibles productos para su venta. La identidad del asociado la proporciona [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md).

![Páginas de inicio de sesión individuales (B2C), empresariales (B2C) y asociados (B2B)](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Autenticación de clientes individuales

Cuando un cliente selecciona **Inicie sesión con su cuenta personal**, se le redirige a una página de inicio de sesión personalizada hospedada por Azure AD B2C. Puede ver en la siguiente imagen que hemos personalizado la interfaz de usuario para que se parezca al sitio web de WoodGrove Groceries. Los clientes de WoodGrove no deberían notar que la experiencia de autenticación está hospedada y protegida por Azure AD B2C.

![Página de inicio de sesión de WoodGrove personalizada hospedada por Azure AD B2C](./media/overview/sign-in.png)

WoodGrove permite a sus clientes registrarse e iniciar sesión con sus cuentas de Google, Facebook o Microsoft como proveedor de identidades. O bien, pueden registrarse con su dirección de correo electrónico y una contraseña para crear lo que se denomina una *cuenta local*.

Cuando un cliente selecciona **Regístrese con su cuenta personal** y, después, **Registrarse ahora**, se le presenta una página de registro personalizada.

![Página de registro de WoodGrove personalizada hospedada por Azure AD B2C](./media/overview/sign-up.png)

Después de escribir una dirección de correo electrónico y seleccionar **Enviar código de verificación**, Azure AD B2C le envía el código. Cuando escriba el código, seleccione **Verificar código** y, después, escriba la otra información en el formulario, que también debe aceptar los términos de servicio.

Al hacer clic en el botón **Crear**, Azure AD B2C redirige al usuario al sitio web de WoodGrove Groceries. Cuando se redirige, Azure AD B2C pasa un token de autenticación de OpenID Connect a la aplicación web de WoodGrove. El usuario ya ha iniciado sesión y está preparado, y su nombre para mostrar aparece en la esquina superior derecha para indicar que está conectado.

![Encabezado del sitio web de WoodGrove Groceries que muestra que el usuario ha iniciado sesión](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Autenticación de los clientes empresariales

Cuando un cliente selecciona una de las opciones en **Clientes empresariales**, el sitio web de WoodGrove Groceries invoca una *directiva de Azure AD B2C* diferente a la de los clientes individuales. Puede ver qué es una *directiva B2C* en la [información general técnica de Azure AD B2C](technical-overview.md).

Esta directiva presenta al usuario una opción para usar sus credenciales corporativas para el registro y el inicio de sesión. En el ejemplo de WoodGrove, se pide a los usuarios que inicien sesión con cualquier cuenta profesional o educativa. Esta directiva usa una [aplicación de Azure AD multiinquilino](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) y el punto de conexión de Azure AD `/common` para federar Azure AD B2C con cualquier cliente de Microsoft 365 del mundo.

### <a name="authenticate-partners"></a>Autenticación de asociados

El vínculo **Sign in with your supplier account** (Inicie sesión con su cuenta de proveedor) usa la funcionalidad de colaboración de Azure Active Directory B2B. Azure AD B2B es una familia de características de Azure Active Directory para administrar las identidades de los asociados. Esas identidades se pueden federar desde Azure Active Directory para el acceso a aplicaciones protegidas mediante Azure AD B2C.

Para más información sobre Azure AD B2B, consulte [¿Qué es el acceso de usuarios invitados en Azure Active Directory B2B?](../active-directory/external-identities/what-is-b2b.md).

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una idea de lo que es Azure AD B2C y algunos de los escenarios en los que puede ayudar, profundice un poco más en sus características y aspectos técnicos.

> [!div class="nextstepaction"]
> [Información general técnica de Azure AD B2C >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo