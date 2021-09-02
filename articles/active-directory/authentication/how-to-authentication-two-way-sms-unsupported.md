---
title: 'El SMS bidireccional ya no se admite: Azure Active Directory'
description: Explica cómo habilitar otro método para los usuarios que siguen usando el SMS bidireccional.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/19/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c92095449132b790c80cf6396a3608f802d7e9a7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458510"
---
# <a name="two-way-sms-unsupported"></a>Incompatibilidad del SMS bidireccional

El SMS bidireccional para el servidor de autenticación multifactor de Azure AD (MFA) se quedó en desuso originalmente en 2018 y dejó de tener soporte técnico el 24 de febrero de 2021, excepto para aquellas organizaciones que recibieron una ampliación del soporte técnico hasta el 2 de agosto de 2021. Los administradores deben habilitar otro método para los usuarios que siguen usando el SMS bidireccional.

Se enviaron notificaciones por correo electrónico y por Service Health en Azure Portal (notificaciones del portal) a los administradores afectados el 8 de diciembre de 2020 y el 28 de enero de 2021. Las alertas se dirigieron a los roles RBAC de propietario, copropietario, administrador y administrador de servicios vinculados a las suscripciones. Si ya ha completado los pasos siguientes, no es necesario realizar ninguna acción.

## <a name="required-actions"></a>Acciones necesarias

1. Habilite la aplicación móvil para los usuarios, si todavía no lo ha hecho. Para obtener más información, consulte [Habilitación de la autenticación de aplicación móvil con el Servidor Azure Multi-Factor Authentication](howto-mfaserver-deploy-mobileapp.md).
1. Indique a los usuarios finales que visiten el [portal de usuarios](howto-mfaserver-deploy-userportal.md) del servidor MFA para activar la aplicación móvil. La [aplicación Microsoft Authenticator](https://www.microsoft.com/en-us/account/authenticator) es la opción de verificación recomendada, ya que es más segura que la del SMS bidireccional. Para obtener más información, consulte [Ha llegado el momento de abandonar los transportes telefónicos para la autenticación](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752).
1. Cambie la configuración del usuario de mensaje de texto bidireccional a aplicación móvil como método predeterminado.

## <a name="faq"></a>Preguntas más frecuentes

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>¿Qué ocurre si no cambio el método predeterminado del SMS bidireccional a la aplicación móvil?
El SMS bidireccional dará error después del 24 de febrero de 2021. Los usuarios verán un error cuando intenten iniciar sesión y pasar la MFA.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>¿Cómo se cambia la configuración del usuario del mensaje de texto bidireccional a la aplicación móvil?

Para cambiar la configuración del usuario, siga estos pasos:

1. En el Servidor MFA, filtre la lista de usuarios por el mensaje de texto bidireccional.
1. Seleccione a todos los usuarios.
1. Abra el cuadro de diálogo Editar usuarios.
1. En los usuarios, cambie de Mensaje de texto a Aplicación móvil.

   ![Captura de pantalla de los usuarios finales](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>¿Los usuarios finales tienen que hacer algo? De ser así, ¿cómo lo hacen?
Sí. Los usuarios finales deben visitar el portal de usuario del Servidor MFA específico para activar la aplicación móvil, si aún no lo han hecho. Una vez realizado el paso 3, los usuarios que no hayan visitado el portal de usuario para configurar la aplicación móvil no podrán iniciar sesión hasta que lo visiten para volver a registrarse.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>¿Qué ocurre si los usuarios no pueden instalar la aplicación móvil? ¿Qué otras opciones tienen?
La alternativa al SMS bidireccional o a la aplicación móvil es una llamada telefónica. Sin embargo, la aplicación Microsoft Authenticator es el método de comprobación recomendado.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>¿El SMS unidireccional también quedará en desuso?
No, solo se dejará de usar el SMS bidireccional. En el caso del Servidor MFA, el SMS unidireccional funciona en un subconjunto de escenarios:

- Adaptador de AD FS
- Autenticación de IIS (requiere el portal de usuario y la configuración)
- RADIUS (requiere que los clientes RADIUS admitan el desafío de acceso y que se use el protocolo PAP)

Existen limitaciones a la hora de utilizar el SMS unidireccional que hacen que la aplicación móvil sea una mejor alternativa, ya que no requiere la solicitud del código de verificación.
Si aún desea usar el SMS unidireccional en algunos escenarios, puede dejarlo activado, pero en la sección **Configuración de la empresa**, en la pestaña **General**, cambie la opción **User Defaults Text Message** (Mensaje de texto predeterminado del usuario) a **Unidireccional** en lugar de **Bidireccional**. Por último, si usa la sincronización de directorios que tiene como valor predeterminado SMS, deberá cambiarla de Bidireccional a Unidireccional.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>¿Cómo puedo comprobar qué usuarios siguen usando el SMS bidireccional?
Para ver a estos usuarios, inicie el **Servidor MFA**, seleccione la sección **Usuarios**, haga clic en **Filtrar lista de usuarios** y filtre por **Mensaje de texto bidireccional**.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>¿Cómo se oculta el SMS bidireccional como opción en el portal de MFA para evitar que los usuarios lo seleccionen en el futuro?
En el portal del usuario del Servidor MFA, haga clic en **Configuración**; puede desactivar **Mensaje de texto** para que no esté disponible. Lo mismo sucede en la sección **AD FS** si usa AD FS para la inscripción de usuarios.

