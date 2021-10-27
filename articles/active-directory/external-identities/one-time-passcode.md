---
title: 'Autenticación de código de acceso de un solo uso para usuarios invitados de B2B: Azure AD'
description: Cómo usar el código de acceso de un solo uso de correo electrónico para autenticar a los usuarios invitados de B2B sin necesidad de una cuenta de Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/15/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019, contperf-fy21q4-portal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3667740745b5e81a1a1df6439610d8def2216c1
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073285"
---
# <a name="email-one-time-passcode-authentication"></a>Autenticación con código de acceso de un solo uso por correo electrónico

La característica de código de acceso de un solo uso por correo electrónico es una manera de autenticar a los usuarios de colaboración B2B cuando no se pueden autenticar a través de otros medios, como Azure AD, la cuenta Microsoft (MSA) o los proveedores de identidades sociales. Cuando un usuario invitado B2B intenta canjear la invitación o iniciar sesión en los recursos compartidos, puede solicitar un código de acceso temporal, que se envía a su dirección de correo electrónico. A continuación, escribe el código de acceso para continuar con el inicio de sesión.

Puede habilitar esta característica en cualquier momento en Azure Portal configurando el proveedor de identidades de código de acceso de un solo uso por correo electrónico en la configuración de External Identities del inquilino. Puede optar por habilitar la característica, deshabilitarla o esperar a que se habilite automáticamente a partir del 1 de noviembre de 2021.

![Diagrama de información general del código de acceso de un solo uso por correo electrónico](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - **A partir del 1 de noviembre de 2021**, comenzaremos a implementar un cambio para activar la característica de código de acceso de un solo uso por correo electrónico para todos los inquilinos existentes y habilitarla de manera predeterminada para los nuevos inquilinos. Si no desea permitir que esta característica se active automáticamente, puede [deshabilitarla](one-time-passcode.md#disable-email-one-time-passcode).
> - La configuración del código de acceso de un solo uso por correo electrónico en Azure Portal se ha movido de **Configuración de colaboración externa** a **All identity providers** (Todos los proveedores de identidades).

> [!NOTE]
> Los usuarios con código de acceso de un solo uso deben iniciar sesión con un vínculo que incluya el contexto del inquilino (por ejemplo, `https://myapps.microsoft.com/?tenantid=<tenant id>` o `https://portal.azure.com/<tenant id>`, o en el caso de un dominio verificado, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Los vínculos directos a aplicaciones y los recursos también funcionan siempre que incluyan el contexto del inquilino. Actualmente, los usuarios invitados no pueden iniciar sesión con puntos de conexión sin contexto de inquilino. Por ejemplo, si se usan `https://myapps.microsoft.com`, `https://portal.azure.com`, se producirá un error.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Experiencia de usuario para los usuarios invitados de código de acceso de un solo uso

Cuando la característica de código de acceso de un solo uso por correo electrónico está habilitada, los usuarios nuevos invitados [que cumplan ciertas condiciones](#when-does-a-guest-user-get-a-one-time-passcode) usarán la autenticación con código de acceso de un solo uso. Los usuarios invitados que canjearon una invitación antes de la habilitación del código de acceso de un solo uso por correo electrónico seguirán usando su mismo método de autenticación.

Con la autenticación de código de acceso de un solo uso, el usuario invitado puede canjear su invitación haciendo clic en un vínculo directo o mediante el correo electrónico de invitación. En cualquier caso, un mensaje en el explorador indica que se enviará un código a la dirección de correo electrónico del usuario invitado. El usuario invitado selecciona **Enviar código**:

   ![Captura de pantalla que muestra el botón Enviar código.](media/one-time-passcode/otp-send-code.png)

Se envía un código de acceso a la dirección de correo electrónico del usuario. El usuario recupera el código de acceso del correo electrónico y lo escribe en la ventana del explorador:

   ![Captura de pantalla que muestra la página Escribir código.](media/one-time-passcode/otp-enter-code.png)

Ahora el usuario invitado se ha autenticado y puede ver el recurso compartido o continuar con el inicio de sesión.

> [!NOTE]
> Los códigos de acceso de un solo uso son válidos durante 30 minutos. Después de 30 minutos, ese código de acceso de un solo uso específico ya no será válido y el usuario deberá solicitar uno nuevo. Las sesiones del usuario expiran después de 24 horas. Después de ese tiempo, el usuario invitado recibe un nuevo código de acceso cuando accede al recurso. La expiración de la sesión proporciona mayor seguridad, en especial cuando un usuario invitado deja su empresa o ya no necesita tener acceso.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>¿Cuándo un usuario invitado obtiene un código de acceso de un solo uso?

Cuando un usuario invitado canjea una invitación o usa un vínculo a un recurso que se ha compartido con él, recibe un código de acceso de un solo uso si:

- No tiene una cuenta de Azure AD
- No tiene una cuenta de Microsoft
- El inquilino invitado no ha configurado la federación con redes sociales (como [Google](google-federation.md)) u otros proveedores de identidades.

En el momento de la invitación, no hay ninguna indicación de que el usuario al que está invitando usará la autenticación de código de acceso de un solo uso. Pero cuando el usuario invitado inicia sesión, la autenticación de código de acceso de un solo uso será el método de reserva si no se puede usar ningún otro método de autenticación.


> [!NOTE]
> Cuando un usuario canjea un código de acceso de un solo uso y más adelante obtiene una MSA, una cuenta de Azure AD u otra cuenta federada, seguirá autenticándose con un código de acceso de un solo uso. Si desea actualizar el método de autenticación del usuario, puede [restablecer su estado de canje](reset-redemption-status.md).

### <a name="example"></a>Ejemplo

Se invita al usuario invitado teri@gmail.com a Fabrikam, que no tiene configurada la federación de Google. Teri no tiene una cuenta de Microsoft. Recibe un código de acceso de un solo uso para la autenticación.

## <a name="enable-email-one-time-passcode"></a>Habilitación del código de acceso de un solo uso por correo electrónico

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global de Azure AD.

2. En el panel de navegación, seleccione **Azure Active Directory**.

3. Seleccione **External Identities** > **Todos los proveedores de identidades**.

4. Seleccione **Email one-time passcode** (Código de acceso de un solo uso por correo electrónico) para abrir el panel de configuración.

5. En **Email one-time passcode for guests** (Código de acceso de un solo uso por correo electrónico para invitados), seleccione una de las opciones siguientes:

   - **Automatically enable email one-time passcode for guests starting \<date\>** (Habilitar automáticamente el código de acceso de un solo uso por correo electrónico para los invitados a partir del \<date\>) si no quiere habilitar la característica inmediatamente y quiere esperar a la fecha de habilitación automática del 1 de noviembre de 2021.
   - **Enable email one-time passcode for guests effective now** (Habilitar el código de acceso de un solo uso por correo electrónico para invitados desde este momento) para habilitar la característica ahora.
   - **Sí** para habilitar la característica ahora si ve un botón de alternancia Sí/No (este botón de alternancia aparece si la característica se deshabilitó anteriormente).

   ![Botón de alternancia de código de acceso de un solo uso por correo electrónico habilitado](media/one-time-passcode/enable-email-otp-options.png)

5. Seleccione **Guardar**.

## <a name="disable-email-one-time-passcode&quot;></a>Deshabilitación del código de acceso de un solo uso por correo electrónico

A partir del 1 de noviembre de 2021, comenzaremos a implementar un cambio para activar la característica de código de acceso de un solo uso por correo electrónico para todos los inquilinos existentes y habilitarla de manera predeterminada para los nuevos inquilinos.  En ese momento, Microsoft dejará de admitir el canje de invitaciones mediante la creación de inquilinos y cuentas de Azure AD no administradas (&quot;virales&quot; o &quot;Just-In-Time") para escenarios de colaboración B2B. Vamos a habilitar la característica de código de acceso de un solo uso por correo electrónico, ya que proporciona un método eficaz de autenticación de reserva para usuarios invitados. No obstante, puede deshabilitar esta característica si prefiere no utilizarla.

> [!NOTE]
>
> Si la característica de código de acceso de un solo uso por correo electrónico se ha habilitado en el inquilino y la desactiva, los usuarios invitados que hayan canjeado un código de acceso de un solo uso no podrán iniciar sesión. Puede [restablecer el estado de canje](reset-redemption-status.md) para que puedan iniciar sesión de nuevo con otro método de autenticación.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Para deshabilitar la característica de código de acceso de un solo uso por correo electrónico

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global de Azure AD.

2. En el panel de navegación, seleccione **Azure Active Directory**.

3. Seleccione **External Identities** > **Todos los proveedores de identidades**.

4. Seleccione **Email one-time passcode** (Código de acceso de un solo uso por correo electrónico) y, después, en **Email one-time passcode for guests** (Código de acceso de un solo uso por correo electrónico para invitados), seleccione **Disable email one-time passcode for guests** (Deshabilitar código de acceso de un solo uso por correo electrónico para invitados) (o **No** si previamente la característica se habilitó, deshabilitó o se optó por participar en la versión preliminar).

   ![Botón de alternancia de código de acceso de un solo uso por correo electrónico deshabilitado](media/one-time-passcode/disable-email-otp-options.png)

   > [!NOTE]
   > La configuración del código de acceso de un solo uso por correo electrónico en Azure Portal se ha movido de **Configuración de colaboración externa** a **All identity providers** (Todos los proveedores de identidades).
   > Si ve un botón de alternancia en lugar de las opciones de código de acceso de un solo uso por correo electrónico, significa que ya ha habilitado o deshabilitado la característica o que ha participado en la versión preliminar de esta. Seleccione **No** para deshabilitar esta característica.

5. Seleccione **Guardar**.

## <a name="note-for-public-preview-customers"></a>Nota para los clientes de la versión preliminar pública

Si previamente ha participado en la versión preliminar pública del código de acceso de un solo uso por correo electrónico, la fecha de habilitación automática de la características del 1 de noviembre de 2021 no es aplicable a su caso, por lo que sus procesos empresariales relacionados no se verán afectados. Además, en Azure Portal, en las propiedades de **Email one-time passcode for guests** (Código de acceso de un solo uso por correo electrónico para invitados), no verá la opción **Automatically enable email one-time passcode for guests starting\<date\>** (Habilitar automáticamente el código de acceso de un solo uso por correo electrónico para invitados a partir del \<date\>). En su lugar, verá el botón **Yes**/**No** (Si/No):

![Participación en el código de acceso de un solo uso por correo electrónico](media/one-time-passcode/enable-email-otp-opted-in.png)

Sin embargo, si prefiere no participar en la característica y permitir que se habilite automáticamente el 1 de noviembre de 2021, puede revertir a la configuración predeterminada mediante el [tipo de recurso de configuración del método de autenticación por correo electrónico](/graph/api/resources/emailauthenticationmethodconfiguration) de Microsoft Graph API. Después de revertir a la configuración predeterminada, las siguientes opciones estarán disponibles en **Email one-time passcode for guests** (Código de acceso de un solo uso por correo electrónico para invitados):

![Habilitación de la participación en el código de acceso de un solo uso por correo electrónico](media/one-time-passcode/email-otp-options.png)

- **Automatically enable email one-time passcode for guests starting \<date\>** (Habilitar automáticamente el código de acceso de un solo uso por correo electrónico para invitados a partir del \<date\>). (Valor predeterminado) Si la característica de código de acceso de un solo uso por correo electrónico no está habilitada para el inquilino, se activará automáticamente a partir del 1 de noviembre de 2021. Si desea que la característica se habilite desde ese momento, no tiene que hacer nada más. Si ya ha habilitado o deshabilitado la característica, esta opción no estará disponible.

- **Enable email one-time passcode for guests effective now** (Habilitar el código de acceso de un solo uso por correo electrónico para invitados desde este momento). Activa la característica de código de acceso de un solo uso por correo electrónico para el inquilino.

- **Disable email one-time passcode for guests** (Deshabilitar el código de acceso de un solo uso por correo electrónico para invitados). Desactiva la característica de código de acceso de un solo uso por correo electrónico para el inquilino y evita que la característica se active el 1 de noviembre de 2021.

## <a name="note-for-azure-us-government-customers"></a>Nota para los clientes de Azure Gobierno de EE. UU.

La característica de código de acceso de un solo uso por correo electrónico está deshabilitada de forma predeterminada en la nube de Azure Gobierno de EE UU. Los asociados no podrán iniciar sesión a menos que esta característica esté habilitada. A diferencia de la nube pública de Azure, la nube de Azure US Government no admite el canje de invitaciones con cuentas de autoservicio de Azure Active Directory.

 ![Código de acceso de un solo uso por correo electrónico deshabilitada](media/one-time-passcode/enable-email-otp-disabled.png)

Para habilitar la característica de código de acceso de un solo uso por correo electrónico en la nube de Azure Gobierno de EE. UU.:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global de Azure AD.
2. En el panel de navegación, seleccione **Azure Active Directory**.
3. Seleccione **Relaciones de organización** > **All identity providers** (Todos los proveedores de identidades).

   > [!NOTE]
   > - Si no ve la opción **Relaciones organizativas**, busque "External Identities" en la barra de búsqueda de la parte superior.

4. Seleccione **Email one-time passcode for guests** (Código de acceso de un solo uso por correo electrónico para invitados) y, luego, **Sí**.
5. Seleccione **Guardar**.

Para más información sobre las limitaciones actuales, consulte [Nubes de Azure Gobierno de EE. UU](current-limitations.md#azure-us-government-clouds).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Por qué todavía veo la opción "Automatically enable email one-time passcode for guests starting October 2021" (Habilitar automáticamente el código de acceso de un solo uso por correo electrónico para invitados a partir de octubre de 2021) seleccionada en la configuración del código de acceso de un solo uso por correo electrónico?**

Debido a las programaciones de implementación, comenzaremos a implementar el cambio para habilitar el código de acceso de un solo uso por correo electrónico de manera predeterminada globalmente el 1 de noviembre de 2021. Hasta entonces, es posible que vea la opción "Automatically enable email one-time passcode for guests starting October 2021" (Habilitar automáticamente el código de acceso de un solo uso por correo electrónico para invitados a partir de octubre de 2021) seleccionada en la configuración del código de acceso de un solo uso por correo electrónico.

**¿Cuál es la experiencia del usuario para los invitados durante el lanzamiento global?**

Durante el lanzamiento global, la experiencia del usuario para los invitados depende de la configuración del código de acceso de un solo uso por correo electrónico y del escenario del invitado. 

Antes de implementar el cambio en su región, los invitados verán el comportamiento siguiente.

- Con el código de acceso de un solo uso por correo electrónico habilitado:

  - Si un invitado tiene una cuenta existente de Azure AD no administrada, seguirá iniciando sesión con su cuenta de Azure AD no administrada.
  - Si un invitado canjeó previamente una invitación a su inquilino mediante una cuenta de Azure AD no administrada y restablece su estado de canje y lo vuelve a invitar, seguirá iniciando sesión con su cuenta de Azure AD no administrada.
  - Si un invitado no tiene una cuenta existente de Azure AD no administrada, realizará el canje mediante la autenticación de código de acceso de un solo uso por correo electrónico.

- Con el código de acceso de un solo uso por correo electrónico deshabilitado:

  - Si un invitado tiene una cuenta existente de Azure AD no administrada, seguirá iniciando sesión con su cuenta de Azure AD no administrada.
  - Si un invitado canjeó previamente una invitación a su inquilino mediante una cuenta de Azure AD no administrada y restablece su estado de canje y lo vuelve a invitar, seguirá iniciando sesión con su cuenta de Azure AD no administrada.
  - Si un invitado no tiene una cuenta existente de Azure AD no administrada, realizará el canje mediante un vínculo de código de acceso de un solo uso por correo electrónico, pero puede recibir un error de inicio de sesión si no se agrega a Azure Portal previamente.

Después de implementar el cambio en su región, los invitados verán el comportamiento siguiente.

- Con el código de acceso de un solo uso por correo electrónico habilitado:

  - Si un invitado tiene una cuenta existente de Azure AD no administrada, usará el código de acceso de un solo uso por correo electrónico para canjear e iniciar sesión en el futuro.
  - Si un invitado canjeó previamente una invitación a su inquilino mediante una cuenta de Azure AD no administrada y restablece su estado de canje y lo vuelve a invitar, usará el código de acceso de un solo uso por correo electrónico para canjear e iniciar sesión en el futuro.
  - Si un invitado no tiene una cuenta de Azure AD no administrada, usará el código de acceso de un solo uso por correo electrónico para canjear e iniciar sesión en el futuro.

- Con el código de acceso de un solo uso por correo electrónico deshabilitado:

  - Si un invitado tiene una cuenta existente de Azure AD no administrada, usará una cuenta de Microsoft para realizar el canje. Terminará con dos cuentas (la cuenta de Azure AD no administrada y la cuenta de Microsoft). Para evitar que esto suceda, le recomendamos encarecidamente que habilite el código de acceso de un solo uso por correo electrónico.
  - Si un invitado canjeó previamente una invitación a su inquilino mediante una cuenta de Azure AD no administrada y restablece su estado de canje y lo vuelve a invitar, usará una cuenta de Microsoft para realizar el canje. Terminará con dos cuentas (la cuenta de Azure AD no administrada y la cuenta de Microsoft). Para evitar que esto suceda, le recomendamos encarecidamente que habilite el código de acceso de un solo uso por correo electrónico.
  - Si un invitado no tiene una cuenta de Azure AD no administrada, usará una cuenta de Microsoft para canjear e iniciar sesión en el futuro.

Para obtener más información sobre las diferentes rutas de canje, consulte [Canje de invitación de colaboración B2B](redemption-experience.md).

**¿Esto significa que la opción "¿No tiene una cuenta? Cree una" del registro de autoservicio va a desaparecer?**

Es fácil confundir el [registro de autoservicio en el contexto de External Identities](self-service-sign-up-overview.md) con el registro de autoservicio para los usuarios comprobados por correo electrónico, pero son dos características diferentes. La característica que va a desaparecer es el [registro de autoservicio con usuarios comprobados por correo electrónico](../enterprise-users/directory-self-service-signup.md), lo que hace que los invitados creen una cuenta de Azure AD no administrada. Sin embargo, el registro de autoservicio para External Identities seguirá estando disponible, lo que da lugar a que los invitados se registren en su organización con [diversos proveedores de identidades](identity-providers.md).  

**¿Qué recomienda hacer Microsoft con las cuentas de Microsoft (MSA) existentes?**

Cuando se admita la capacidad de deshabilitar la cuenta de Microsoft en las configuraciones de los proveedores de identidades (no disponible actualmente), se recomienda encarecidamente deshabilitar la cuenta de Microsoft y habilitar el código de acceso de un solo uso por correo electrónico. A continuación, debe restablecer el estado de canje de los invitados existentes con cuentas de Microsoft para que puedan volver a realizar el canje mediante la autenticación de código de acceso de un solo uso por correo electrónico y usar el código de acceso de un solo uso por correo electrónico para iniciar sesión en el futuro.

**¿Este cambio incluye la integración de SharePoint y OneDrive con Azure AD B2B?**

No, la implementación global del cambio para habilitar el código de acceso de un solo uso por correo electrónico de manera predeterminada que comienza el 1 de noviembre de 2021 no incluye la integración de SharePoint y OneDrive con Azure AD B2B. Para obtener información sobre cómo habilitar la integración para que la colaboración de SharePoint y OneDrive use funcionalidades de B2B o cómo deshabilitar esta integración, consulte [Integración de SharePoint y OneDrive con Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration).
