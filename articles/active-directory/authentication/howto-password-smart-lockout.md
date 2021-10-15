---
title: 'Prevención de ataques mediante el bloqueo inteligente: Azure Active Directory'
description: Aprenda de qué manera el bloqueo inteligente de Azure Active Directory ayuda a proteger a su organización frente a los ataques por fuerza bruta que intentan adivinar las contraseñas de los usuarios.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3f8d5fb55d547a1c0602843fb36f19ad45dbc2a
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536588"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Protección de las cuentas de usuario frente a ataques con el bloqueo inteligente de Azure Active Directory

El bloqueo inteligente ayuda a bloquear a los actores malintencionados que intentan adivinar las contraseñas de los usuarios o que usan métodos de fuerza bruta para acceder. El bloqueo inteligente puede reconocer los inicios de sesión que proceden de usuarios válidos y tratarlos de forma distinta a los que provienen de atacantes y otros orígenes desconocidos. Se impide el paso a los atacantes, mientras que los usuarios pueden continuar con el acceso a sus cuentas y ser productivos.

## <a name="how-smart-lockout-works"></a>Cómo funciona el bloqueo inteligente

De manera predeterminada, el bloqueo inteligente bloquea los intentos de inicio de sesión en la cuenta durante un minuto tras 10 intentos fallidos en el caso de los inquilinos de la nube pública de Azure y Azure China 21Vianet, y tras 3 intentos en el caso de los inquilinos de Azure US Government. La cuenta se bloquea de nuevo después de cada intento de inicio de sesión incorrecto, durante un minuto en el primero y más tiempo en los intentos posteriores. Para minimizar las formas en las que un atacante podría evitar este comportamiento, no divulgamos la velocidad a la que crece el período de bloqueo en los intentos de inicio de sesión incorrectos adicionales.

El bloqueo inteligente realiza un seguimiento de los últimos tres códigos hash de contraseña incorrecta para evitar que aumente el contador de bloqueo con la misma contraseña. Si alguien escribe la misma contraseña incorrecta varias veces, este comportamiento no hará que la cuenta se bloquee.

> [!NOTE]
> La funcionalidad de seguimiento de hash no está disponible para los clientes con la autenticación de paso a través habilitada, ya que la autenticación se produce en el entorno local y no en la nube.

Las implementaciones federadas que usan AD FS 2016 y AD FS 2019 pueden habilitar ventajas similares mediante el [bloqueo de extranet de AD FS y el bloqueo inteligente de extranet](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

El bloqueo inteligente está siempre activado para todos los clientes de Azure AD con la configuración predeterminada, que ofrece la combinación correcta de seguridad y facilidad de uso. Para personalizar la configuración del bloqueo inteligente con valores específicos de su organización, los usuarios necesitan una licencia de Azure AD Premium P1 o superior.

El uso del bloqueo inteligente no garantiza que nunca se bloqueará un usuario original. Cuando el bloqueo inteligente bloquea una cuenta de usuario, hacemos todo lo posible para no bloquear al usuario original. El servicio de bloqueo intenta garantizar que los actores no válidos no puedan obtener acceso a una cuenta de usuarios genuina. Se aplican las siguientes consideraciones:

* Cada centro de datos de Azure AD realiza un seguimiento del bloqueo de forma independiente. Un usuario tiene cierto número de intentos (*threshold_limit * datacenter_count*), si el usuario visita cada centro de datos.
* El bloqueo inteligente compara los datos de una ubicación desconocida con los de una conocida para diferenciar entre un actor no válido y el usuario original. Las ubicaciones conocidas y desconocidas tienen contadores de bloqueo independientes.

El bloqueo inteligente puede integrarse en implementaciones híbridas que usen la autenticación de hash de contraseña o la autenticación de paso a través, para impedir que los atacantes bloqueen las cuentas de Active Directory Domain Services (AD DS) en el entorno local. Mediante el establecimiento correcto de directivas de bloqueo inteligente en Azure AD, se pueden filtrar los atacantes antes de que lleguen al entorno local de AD DS.

Al usar la [autenticación de paso a través](../hybrid/how-to-connect-pta.md), se aplican las consideraciones siguientes:

* El umbral de bloqueo de Azure AD es **menor** que el umbral de bloqueo de cuenta de AD DS. Establezca los valores de modo que el umbral de bloqueo de cuenta de AD DS sea al menos dos o tres veces mayor que el umbral de bloqueo de Azure AD.
* La duración del bloqueo de Azure AD debe ser mayor que la duración de Restablecer el contador de bloqueos tras de AD DS. La duración de Azure AD se establece en segundos, mientras que la duración de AD se establece en minutos.

Por ejemplo, si quiere que la duración de bloqueo inteligente de Azure AD sea mayor que AD DS, Azure AD sería de 120 segundos (2 minutos), mientras que AD local se establece en 1 minuto (60 segundos). Si desea que el umbral de bloqueo de Azure AD sea 5, el umbral de bloqueo de AD local debe ser 10.  Esta configuración garantizaría que el bloqueo inteligente impida que las cuentas de AD local se bloqueen por ataques por fuerza bruta en las cuentas de Azure AD.

> [!IMPORTANT]
> Actualmente, un administrador no puede desbloquear cuentas en la nube de los usuarios si estos han sido bloqueados por la capacidad de bloqueo inteligente. El administrador deberá esperar a que expire la duración del bloqueo. Pero el usuario puede desbloquear mediante el autoservicio de restablecimiento de contraseña (SSPR) desde un dispositivo o una ubicación de confianza.

## <a name="verify-on-premises-account-lockout-policy"></a>Comprobación de la directiva de bloqueo de cuenta local

Para verificar la directiva de bloqueo de cuenta de AD DS en el entorno local, siga los pasos a continuación desde un sistema unido a un dominio con privilegios de administrador:

1. Abra las herramientas de administración de directivas de grupo.
2. Edite la directiva de grupo que incluye la directiva de bloqueo de cuentas de su organización, por ejemplo, la **directiva de dominio predeterminada**.
3. Vaya a **Configuración del equipo** > **Directivas** > **Configuración de Windows** > **Configuración de seguridad** > **Directivas de cuenta** > **Directiva de bloqueo de cuenta**.
4. Compruebe los valores de **Umbral de bloqueo de cuenta** y **Restablecer contador de bloqueo de cuenta tras**.

![Modificación de la directiva de bloqueo de cuentas de Active Directory local](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Administración de los valores de bloqueo inteligente de Azure AD

En función de los requisitos de su organización, puede personalizar los valores de bloqueo inteligente de Azure AD. Para personalizar la configuración del bloqueo inteligente con valores específicos de su organización, los usuarios necesitan una licencia de Azure AD Premium P1 o superior. La personalización de la configuración del bloqueo inteligente no está disponible para los inquilinos de Azure China 21Vianet.

Para comprobar o modificar los valores de bloqueo inteligente para su organización, complete estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque y seleccione *Azure Active Directory*, a continuación, seleccione **Seguridad** > **Métodos de autenticación** > **Protección por contraseña**.
1. Establezca la opción **Umbral de bloqueo** en función del número de inicios de sesión con error permitidos en una cuenta antes de su primer bloqueo.

    El valor predeterminado es 10 en el caso de los inquilinos de Azure Public y 3 en el de los de Azure US Government.

1. En **Lockout duration in seconds** (Duración de bloqueo en segundos), establezca la longitud en segundos de cada bloqueo.

    El valor predeterminado es 60 segundos (un minuto).

> [!NOTE]
> Si el primer inicio de sesión después de un bloqueo también produce un error, la cuenta se bloquea de nuevo. Si una cuenta se bloquea de forma repetida, aumenta la duración del bloqueo.

![Personalización de la directiva de bloqueo inteligente de Azure AD en Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="testing-smart-lockout"></a>Prueba del bloqueo inteligente

Cuando se desencadena el umbral de bloqueo inteligente, aparecerá el siguiente mensaje mientras la cuenta está bloqueada:

*Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo y, si sigue teniendo problemas, póngase en contacto con su administrador.*

Al probar el bloqueo inteligente, las solicitudes de inicio de sesión pueden ser controladas por distintos centros de datos debido a la naturaleza de distribución geográfica y de equilibrio de carga del servicio de autenticación de Azure AD. En ese escenario, dado que cada centro de datos de Azure AD hace un seguimiento de manera independiente, se puede tardar más que el número de intentos de bloqueo definido para producir un bloqueo. Un usuario tiene un número máximo (*threshold_limit * datacenter_count*) de intentos fallidos antes de bloquearse completamente.

El bloqueo inteligente realiza un seguimiento de los últimos tres códigos hash de contraseña incorrecta para evitar que aumente el contador de bloqueo con la misma contraseña. Si alguien escribe la misma contraseña incorrecta varias veces, este comportamiento no hará que la cuenta se bloquee.


## <a name="default-protections"></a>Protecciones predeterminadas
Además del bloqueo inteligente, Azure AD también protege frente a ataques mediante el análisis de señales, incluidos el tráfico IP y la identificación de comportamientos anómalos. Azure AD bloqueará estos inicios de sesión malintencionados de manera predeterminada y devolverá el [código de error AADSTS50053: IdsLocked](../develop/reference-aadsts-error-codes.md) independientemente de la validez de la contraseña.

## <a name="next-steps"></a>Pasos siguientes

Para personalizar aún más esta experiencia, puede [configurar contraseñas prohibidas personalizadas para la protección de contraseñas de Azure AD](tutorial-configure-custom-password-protection.md).

Para ayudar a los usuarios a restablecer o cambiar su contraseña desde un explorador web, puede [configurar el autoservicio de restablecimiento de contraseña de Azure AD](tutorial-enable-sspr.md).
