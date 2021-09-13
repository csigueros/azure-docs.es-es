---
title: Configuración de un flujo de registro e inicio de sesión
titleSuffix: Azure AD B2C
description: Aprenda a configurar un flujo de registro e inicio de sesión en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: edaecb8d3969d251f8ff7c4e6525912a0c199089
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322378"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Configuración de un flujo de registro e inicio de sesión en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>Flujo de registro e inicio de sesión

La directiva de registro e inicio de sesión permite a los usuarios: 

* Regístrese con una cuenta local
* Iniciar sesión con una cuenta local
* Registrarse o iniciar sesión con una cuenta de redes sociales
* Restablecimiento de contraseña

![Flujo de edición de perfiles](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>Prerrequisitos

Si todavía no lo ha hecho, [registre una aplicación web en Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Creación de un flujo de usuario de registro e inicio de sesión

Este flujo de usuario de registro y de inicio de sesión controla las experiencias de registro y de inicio de sesión del cliente con una sola configuración. A los usuarios se les lleva por el proceso correcto según el contexto.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el icono **Directorio y suscripción** en la barra de herramientas del portal y, luego, elija el directorio que contiene el inquilino de Azure AD B2C.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. En **Directivas**, seleccione **Flujos de usuario** y **Nuevo flujo de usuario**.

    ![Página Flujos de usuario del portal con el botón Nuevo flujo de usuario resaltado](./media/add-sign-up-and-sign-in-policy/sign-up-sign-in-user-flow.png)

1. En la página **Crear un flujo de usuario**, seleccione el flujo de usuario **Registrarse e iniciar sesión**.

    ![Página Selección de un flujo de usuario con las opciones Registrarse e Iniciar sesión resaltadas](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. En **Seleccione una versión**, elija **Recomendada** y, luego, seleccione **Crear**. [Más información](user-flow-versions.md) sobre las versiones del flujo de usuario.

    ![Página Creación de flujo de usuario en Azure Portal con las propiedades resaltadas](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. Escriba un **nombre** para el flujo de usuario. Por ejemplo, *signupsignin1*.
1. En **Proveedores de identidades**, seleccione al menos un proveedor de identidades:

   * En **Cuentas locales**, seleccione una de las siguientes: **Registro por correo electrónico**, **Registro de id. de usuario**, **Phone signup** (Registro telefónico), **Phone/Email signup** (Registro telefónico o por correo electrónico) o **Ninguno**. [Más información](sign-in-options.md).
   * En **Proveedores de identidades sociales**, seleccione cualquiera de los proveedores de identidades sociales o empresariales externos que haya configurado. [Más información](add-identity-provider.md).
1. En **Autenticación multifactor**, si quiere exigir a los usuarios que comprueben su identidad con un segundo método de autenticación, elija el tipo de método y cuándo aplicar la autenticación multifactor (MFA). [Más información](multi-factor-authentication.md).
1. En **Acceso condicional**, si ha configurado directivas de acceso condicional para el inquilino de Azure AD B2C y quiere habilitarlas para este flujo de usuario, active la casilla **Aplicar directivas de acceso condicional**. No es necesario especificar un nombre de directiva. [Más información](conditional-access-user-flow.md?pivots=b2c-user-flow).
1. En **Atributos de usuario y notificaciones de token**, elija los atributos que quiere recopilar del usuario durante el registro y las notificaciones que quiere que se devuelvan en el token. Para obtener la lista completa de valores, seleccione **Mostrar más**, elija los valores y, después, seleccione **Aceptar**.

   > [!NOTE]
   > También puede [crear atributos personalizados](user-flow-custom-attributes.md?pivots=b2c-user-flow) para usarlos en el inquilino de Azure AD B2C.

    ![Página de selección de atributos y notificaciones con tres notificaciones seleccionadas](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. Seleccione **Crear** para agregar el flujo de usuario. El prefijo *B2C_1* se anexa automáticamente al nombre.
1. Siga los pasos para [controlar el flujo de la opción "¿Olvidó su contraseña?"](add-password-reset-policy.md?pivots=b2c-user-flow.md#self-service-password-reset-recommended). de la directiva de registro o de inicio de sesión.

### <a name="test-the-user-flow"></a>Prueba del flujo de usuario

1. Seleccione el flujo de usuario que ha creado para abrir su página de información general y, luego, **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación web denominada *webapp1* que registró anteriormente. La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Haga clic en **Ejecutar flujo de usuario** y, a continuación, seleccione **Registrarse ahora**.

    ![Página Ejecutar flujo de usuario del portal con el botón Ejecutar flujo de usuario resaltado](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.png)

1. Escriba una dirección de correo electrónico válida, haga clic en **Enviar código de verificación**, escriba el código de verificación que reciba y seleccione **Comprobar código**.
1. Escriba la contraseña nueva y confírmela.
1. Seleccione su país y región, escriba el nombre que desee que aparezca, escriba un código postal y haga clic en **Crear**. El token se devuelve al `https://jwt.ms` y debe mostrarse.
1. Ahora puede ejecutar el flujo de usuario nuevo y debe ser capaz de iniciar sesión con la cuenta que ha creado. El token devuelto incluye las notificaciones que seleccionó para país/región, nombre y código postal.

> [!NOTE]
> Actualmente, la experiencia "Ejecutar flujo de usuario" no es compatible con el tipo de dirección URL de respuesta de SPA que usa el flujo de código de autorización. Para usar la experiencia "Ejecutar flujo de usuario" con estos tipos de aplicaciones, registre una dirección URL de respuesta de tipo "Web" y habilite el flujo implícito tal como se describe [aquí](tutorial-register-spa.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>Creación de una directiva de registro o de inicio de sesión

Las directivas personalizadas son un conjunto de archivos XML que se cargan en el inquilino de Azure AD B2C para definir recorridos de usuario. Proporcionamos paquetes de inicio con varias directivas predefinidas, entre las que se incluyen: registro e inicio de sesión, restablecimiento de contraseña y directiva de edición de perfiles. Para obtener información, consulte [Introducción a las directivas personalizadas en Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

* [Iniciar sesión con un proveedor de identidades de redes sociales](add-identity-provider.md)
* Configure un [flujo de restablecimiento de contraseña](add-password-reset-policy.md).
