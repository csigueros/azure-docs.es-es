---
title: 'Configuración de la aplicación Microsoft Authenticator como método de comprobación: Azure AD'
description: Cómo configurar su página de información de seguridad (versión preliminar) para comprobar su identidad mediante la aplicación Microsoft Authenticator como su método de comprobación.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 06/10/2021
ms.author: curtand
ms.openlocfilehash: 4e03fe3ca7a66dc361988151b558fef0c4b4f390
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111982581"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Configuración de la aplicación Microsoft Authenticator como método de comprobación

Puede seguir estos pasos para agregar sus métodos de restablecimiento de contraseña y verificación en dos pasos. Una vez que haya configurado esto por primera vez, puede volver a la página **Información de seguridad** para agregar, actualizar o eliminar su información de seguridad.

Si se le pide que configure esto justo después de haber iniciado sesión en su cuenta profesional o educativa, consulte los pasos detallados en el artículo de [configuración de su información de seguridad desde el símbolo del sistema de página de inicio de sesión](security-info-setup-signin.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Si no ve la opción de la aplicación autenticadora, es posible que su organización no le permita usar esta opción para la comprobación. En este caso, tendrá que elegir otro método o ponerse en contacto con el departamento de soporte técnico de su organización para que le proporcione más ayuda.

## <a name="security-vs-password-reset-verification"></a>Comprobación de seguridad frente a comprobación de restablecimiento de contraseña

Los métodos de la información de seguridad se usan tanto para la comprobación de seguridad de dos factores como para al restablecimiento de la contraseña. Sin embargo, no todos los métodos pueden usarse para ambos.

| Método | Se usa para |
| ------ | -------- |
| Aplicación de autenticación | Autenticación mediante la comprobación de dos factores y restablecimiento de la contraseña. |
| Mensajes de texto | Autenticación mediante la comprobación de dos factores y restablecimiento de la contraseña. |
| Llamadas de teléfono | Autenticación mediante la comprobación de dos factores y restablecimiento de la contraseña. |
| Clave de seguridad | Autenticación mediante la comprobación de dos factores y restablecimiento de la contraseña. |
| Cuenta de correo electrónico | Solo autenticación mediante restablecimiento de contraseña. Deberá elegir otro método para la comprobación mediante dos factores. |
| Preguntas de seguridad | Solo autenticación mediante restablecimiento de contraseña. Deberá elegir otro método para la comprobación mediante dos factores. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Configuración de la aplicación Microsoft Authenticator desde la página Información de seguridad

Dependiendo de la configuración de su organización, es posible que pueda usar una aplicación autenticadora como uno de sus métodos de información de seguridad. Si no necesita usar la aplicación Microsoft Authenticator, puede elegir una aplicación diferente durante el proceso de configuración. Sin embargo, en este artículo se usa la aplicación Microsoft Authenticator.

> [!IMPORTANT]
> Si ha configurado la aplicación Microsoft Authenticator en cinco dispositivos distintos o si ha usado cinco tokens de hardware, no podrá configurar un sexto dispositivo y es posible que vea el mensaje de error siguiente:
> 
> **No se puede configurar Microsoft Authenticator porque ya tiene cinco aplicaciones de autenticación o cinco tokens de hardware. Póngase en contacto con su administrador para eliminar una de las aplicaciones de autenticación o los tokens de hardware.**

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Pasos para configurar la aplicación Microsoft Authenticator

1. Inicie sesión en su cuenta profesional o educativa y vaya a la página https://myaccount.microsoft.com/.

    ![Página Mi Perfil, que muestra los vínculos de Información de seguridad resaltados](media/security-info/securityinfo-myprofile-page.png)

2. Seleccione **información de seguridad** en el menú de la izquierda o use el vínculo del panel **Información de seguridad**. Si ya se ha registrado, se le solicitará que utilice la comprobación en dos pasos. A continuación, seleccione **Agregar método** en el panel de **Información de seguridad**.

    ![Página Información de seguridad con la opción Agregar método resaltada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. En la página **Agregar un método**, seleccione **Aplicación autenticadora** en la lista desplegable y, a continuación, seleccione **Agregar**.

    ![Cuadro de diálogo Agregar método, con la opción Aplicación autenticadora seleccionada](media/security-info/securityinfo-myprofile-addauthapp.png)

4. En la página **Empiece obteniendo la aplicación**, seleccione **Descargar ahora** para descargar e instalar la aplicación Microsoft Authenticator en un dispositivo móvil y, después, seleccione **Siguiente**.

    Para obtener más información sobre cómo descargar e instalar la aplicación, consulte [Download and install the Microsoft Authenticator app](user-help-auth-app-download-install.md) (Descargar e instalar la aplicación Microsoft Authenticator).

    ![Página Start by getting the app (Empiece obteniendo la aplicación)](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Si desea usar una aplicación de autenticación que no sea Microsoft Authenticator, seleccione el vínculo **Quiero usar otra aplicación de autenticación**.
   >
   > Si su organización le permite elegir un método que no sea la aplicación de autenticación, puede seleccionar el vínculo **I want to set up a different method** (Quiero configurar otro método).

5. Permanezca en la página **Configuración de la cuenta** mientras configura la aplicación Microsoft Authenticator en el dispositivo móvil.

    ![Configuración manual de la aplicación autenticadora](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Abra la aplicación Microsoft Authenticator, seleccione que permita las notificaciones (si se le solicita), **Agregar cuenta** en el icono **Customize and control** (Personalizar y controlar) de la esquina superior derecha y, después, seleccione **Cuenta profesional o educativa**.

    >[!Note]
    >Si es la primera vez que configura la aplicación Microsoft Authenticator, es posible que reciba un mensaje en el que se le pregunta si quiere permitir que la aplicación acceda a la cámara (iOS) o permitir que la aplicación tome fotografías y grabe vídeo (Android). Debe seleccionar **Permitir** para que la aplicación de autenticación pueda acceder a la cámara y tomar una fotografía del código QR en el paso siguiente. Aunque no otorgue permiso a la cámara, podrá configurar la aplicación de autenticación, pero tendrá que agregar la información de código manualmente. Para más información sobre cómo agregar manualmente código, consulte [Agregar manualmente una cuenta a la aplicación](user-help-auth-app-add-account-manual.md).

7. Vuelva a la página **Configuración de la cuenta** en el equipo y seleccione **Siguiente**.

    Aparece la página **Digitalización del código QR** .

    ![Examinar el código QA con la aplicación Authenticator](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Lea el código que se proporciona, para lo que debe usar el lector de códigos QR de la aplicación Microsoft Authenticator, que apareció en el dispositivo móvil tras crear la cuenta profesional o educativa en el paso 6.

    La aplicación de autenticación debería agregar correctamente la cuenta profesional o educativa sin solicitarle más información. No obstante, si el lector de códigos QR no lee este, puede seleccionar el vínculo **Can't scan the QR code** (No se puede leer el código QR) y especificar manualmente la dirección URL y el código en la aplicación Microsoft Authenticator. Para obtener más información sobre cómo agregar manualmente un código, consulte [Agregar manualmente una cuenta a la aplicación](user-help-auth-app-add-account-manual.md).

9. Seleccione **Siguiente** en la página **Digitalización del código QR** del equipo.

    Se envía una notificación a la aplicación Microsoft Authenticator del dispositivo móvil para que pruebe la cuenta.

    ![Probar la cuenta con la aplicación de autenticación](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Apruebe la notificación en la aplicación Microsoft Authenticator y seleccione **Siguiente**.

     ![Notificación correcta, se establece la conexión entre la aplicación y su cuenta](media/security-info/securityinfo-myprofile-successauthapp.png)

     La información de seguridad se actualiza para usar la aplicación Microsoft Authenticator de forma predeterminada para verificar su identidad cuando se usan la verificación en dos pasos o el restablecimiento de contraseña.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Eliminación de la aplicación autenticador desde los métodos de información de seguridad

Si no desea usar la aplicación autenticadora como método de información de seguridad, puede quitarla de la página **Información de seguridad**. Esto funciona con todas las aplicaciones autenticadora, no solo con la aplicación Microsoft Authenticator. Después de eliminar la aplicación, tendrá que ir a la aplicación autenticadora en el dispositivo móvil y eliminar la cuenta.

>[!Important]
>Si elimina una aplicación autenticadora por error, no existe modo alguno de deshacerlo. Tendrá que agregar la aplicación autenticadora de nuevo, siguiendo los pasos de la sección de [configuración de la aplicación autenticadora](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) de este artículo.

### <a name="to-delete-the-authenticator-app"></a>Pasos para eliminar la aplicación autenticadora

1. En la página **Información de seguridad**, seleccione el vínculo **Eliminar** situado junto a la aplicación Authenticator.

    ![Vínculo para eliminar la aplicación autenticadora desde Información de seguridad](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Seleccione **Sí** en el cuadro de confirmación para eliminar la aplicación autenticadora. Una vez eliminada la aplicación autenticadora, se quitará de su información de seguridad y desaparecerá de la página **Información de seguridad**. Si la aplicación autenticadora es su método predeterminado, el valor predeterminado cambiará a otro método disponible.

3. Abra la aplicación autenticadora en el dispositivo móvil, seleccione **Editar cuentas** y, a continuación, elimine su cuenta profesional o educativa desde la aplicación autenticadora.

    La cuenta se quita completamente de la aplicación autenticadora para las solicitudes de verificación en dos fases y de restablecimiento de contraseña.

## <a name="change-your-default-security-info-method"></a>Cambio de su método de información de seguridad predeterminado

Si desea que las llamadas de teléfono sean el método predeterminado usado al iniciar sesión en su cuenta profesional o educativa mediante la verificación en dos pasos o para las solicitudes de restablecimiento de contraseña, puede establecerlo en la página **Información de seguridad**.

>[!NOTE]
>Si su método de inicio de sesión predeterminado es un mensaje de texto o una llamada a su número de teléfono, el código SMS o la llamada de voz se envía automáticamente durante la autenticación multifactor. A partir de junio de 2021, algunas aplicaciones pedirán a los usuarios que elijan **Texto** o **Llamada** primero. Esta opción evita el envío de demasiados códigos de seguridad para diferentes aplicaciones. Si su método de inicio de sesión predeterminado es la aplicación Microsoft Authenticator ([que se recomienda encarecidamente](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)), la notificación de la aplicación se envía automáticamente.

### <a name="to-change-your-default-security-info-method"></a>Para cambiar el método de información de seguridad predeterminado

1. En la página **Información de seguridad**, seleccione el vínculo **Cambiar** situado junto a la información **Método de inicio de sesión predeterminado**.

    ![Cambio de vínculo del método de inicio de sesión predeterminado](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Elija **Microsoft Authenticator: Notificación** en la lista desplegable de métodos disponibles. Si no usa la aplicación Microsoft Authenticator, seleccione la opción **Aplicación autenticadora o Token de hardware**.

    ![Elección de método para el inicio de sesión predeterminado](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Seleccione **Confirmar**.

    El método predeterminado que se usa para el inicio de sesión cambia en la aplicación Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Métodos de información de seguridad adicionales

Tiene opciones adicionales para determinar cómo su organización se pone en contacto con usted para comprobar su identidad, basándose en lo está intentando hacer. Entre estas opciones se incluyen:

- **Texto de dispositivo móvil.** Escriba el número del dispositivo móvil y obtendrá un código que podrá usar para la verificación en dos pasos o el restablecimiento de contraseña. Para obtener instrucciones detalladas sobre cómo verificar su identidad con un mensaje de texto (SMS), vea [Configuración de la información de seguridad para usar la mensajería de texto (SMS)](security-info-setup-text-msg.md).

- **Llamada a dispositivo móvil o al teléfono del trabajo.** Escriba el número del dispositivo móvil y recibirá una llamada telefónica para la verificación en dos pasos o el restablecimiento de contraseña. Para obtener instrucciones paso a paso sobre cómo comprobar su identidad con un número de teléfono, consulte [Configuración de la información de seguridad para usar llamadas de teléfono](security-info-setup-phone-number.md).

- **Clave de seguridad.** Registre la clave de seguridad compatible con Microsoft y úsela junto con un PIN para la verificación en dos pasos o el restablecimiento de contraseña. Para instrucciones paso a paso sobre cómo comprobar su identidad con una clave de seguridad, consulte [Configuración de la información de seguridad para usar una clave de seguridad](security-info-setup-security-key.md).

- **Dirección de correo electrónico.** Escriba la dirección de correo electrónico profesional o educativa para recibir un correo electrónico para el restablecimiento de contraseña. Esta opción no está disponible para la verificación en dos pasos. Para obtener instrucciones detalladas sobre cómo configurar el correo electrónico, vea [Configuración de la información de seguridad para usar el correo electrónico](security-info-setup-email.md).

- **Preguntas de seguridad.** Responda a algunas preguntas de seguridad creadas por el administrador para su organización. Esta opción solo está disponible para el restablecimiento de contraseña y no para la verificación en dos pasos. Para obtener instrucciones paso a paso sobre cómo configurar las preguntas de seguridad, consulte el artículo [Configuración de la información de seguridad para usar las preguntas de seguridad](security-info-setup-questions.md).

    >[!Note]
    >Si faltan algunas de estas opciones, lo más probable es que su organización no permita esos métodos. Si este es el caso, tendrá que elegir uno de los métodos que sí están disponibles o ponerse en contacto con su administrador para obtener más ayuda.

## <a name="next-steps"></a>Pasos siguientes

- Inicie sesión con la aplicación Microsoft Authenticator; para ello, siga los pasos del artículo [Inicio de sesión con la información de seguridad o la verificación en dos pasos](security-info-setup-signin.md).

- Restablezca la contraseña si ha perdido u olvidado la que tenía acudiendo al [portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com/) o siga los pasos descritos en el artículo [Restablecimiento de la contraseña profesional o educativa](active-directory-passwords-update-your-own-password.md).

- Obtenga soluciones de problemas, sugerencias y ayuda para los problemas de inicio de sesión en el artículo [Cuando no puedes iniciar sesión en tu cuenta de Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
