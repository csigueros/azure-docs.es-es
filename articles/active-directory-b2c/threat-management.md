---
title: 'Mitigación de ataques a credenciales: Azure AD B2C'
titleSuffix: Azure AD B2C
description: Obtenga información sobre las técnicas de detección y mitigación de ataques a credenciales (ataques a contraseña) en Azure Active Directory B2C, incluidas las características de bloqueo de cuentas inteligentes.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 99363e4f9f67e6ceca0d163131a2f2ff64b09857
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546980"
---
# <a name="mitigate-credential-attacks-in-azure-ad-b2c"></a>Mitigación de ataques a credenciales en Azure AD B2C

Los ataques a credenciales pueden dar lugar a un acceso no autorizado a los recursos. Las contraseñas establecidas por los usuarios deben tener una complejidad razonable. Azure AD B2C tiene de técnicas de mitigación para los ataques a credenciales. La mitigación incluye la detección de ataques a credenciales por fuerza bruta y ataques de diccionario a credenciales. Mediante el uso de diversas señales, Azure Active Directory B2C (Azure AD B2C) analiza la integridad de las solicitudes. Azure AD B2C está diseñado para diferenciar de forma inteligente los usuarios previstos frente a los hackers y las redes de robots (botnets).

Azure AD B2C usa una sofisticada estrategia para bloquear las cuentas. Las cuentas se bloquean en función de la dirección IP de la solicitud y las contraseñas escritas. El tiempo que dura el bloqueo también aumenta en función de la probabilidad de que sea un ataque. Una vez que se ha intentado una contraseña sin éxito diez veces (el umbral de intento predeterminado), se produce un bloqueo de un minuto. La próxima vez que un inicio de sesión no tenga éxito después de que la cuenta se desbloquee (es decir, después de que el servicio haya desbloqueado automáticamente la cuenta una vez que expire el período de bloqueo), se producirá otro bloqueo de un minuto y continuará para cada inicio de sesión incorrecto. Escribir repetidamente la misma contraseña, o una parecida, no cuenta como varios inicios de sesión incorrectos.

> [!NOTE]
> Esta característica es compatible con [flujos de usuario, directivas personalizadas](user-flow-overview.md) y flujos de [ROPC](add-ropc-policy.md). Se activa de forma predeterminada, por lo que no es preciso configurarla en los flujos de usuario ni en las directivas personalizadas.

## <a name="unlock-accounts"></a>Desbloqueo de cuentas

Los diez primeros períodos de bloqueo son de un minuto. Los diez períodos de bloqueo siguientes son ligeramente más largos y la duración aumenta después de cada diez períodos de bloqueo. El contador de bloqueos se restablece en cero tras un inicio de sesión correcto, cuando la cuenta no está bloqueada. Los períodos de bloqueo pueden durar hasta cinco horas. Los usuarios deben esperar a que expire la duración del bloqueo. Sin embargo, el usuario puede realizar el desbloqueo mediante el [flujo de usuario de contraseña](add-password-reset-policy.md) de autoservicio.

## <a name="manage-password-protection-settings"></a>Administración de la configuración de protección con contraseña

Para administrar la configuración de protección con contraseña, incluido el umbral de bloqueo:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com)
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En el menú de la izquierda, seleccione **Azure AD B2C**. O bien, seleccione **Todos los servicios** y busque y seleccione **Azure AD B2C**.
1. En **Seguridad**, seleccione **Métodos de autenticación (Vista preliminar)** y, a continuación, seleccione **Protección con contraseña**.
1. En **Bloqueo inteligente personalizado**, especifique la configuración de protección de contraseñas deseada:

   - **Umbral de bloqueo**: número de intentos de inicio de sesión infructuosos permitidos antes de que la cuenta se bloquee por primera vez. Si el primer inicio de sesión después de un bloqueo también es infructuoso, la cuenta se bloquea de nuevo.
   - **Duración del bloqueo, en segundos**: duración mínima de cada bloqueo, en segundos. Si una cuenta se bloquea varias veces, aumenta la duración del bloqueo.

       ![Página Protección con contraseña de Azure Portal en la configuración de Azure AD](./media/threat-management/portal-02-password-protection.png)
    <br />*Establezca el umbral de bloqueo en 5 en la configuración de **Protección con contraseña***.

1. Seleccione **Guardar**.

## <a name="testing-the-password-protection-settings"></a>Prueba de la configuración de protección con contraseña

La característica de bloqueo inteligente usa muchos factores para determinar cuándo se debe bloquear una cuenta, pero el principal es el patrón de contraseña. La característica de bloqueo inteligente considera pequeñas variaciones de una contraseña como un conjunto y se cuentan como un intento. Por ejemplo:

- Contraseñas como 12456! y 1234567! (o newAccount1234 y newaccount1234) son tan similares que el algoritmo los interpreta como errores humanos y los cuenta como un único intento.
- Variaciones mayores en el patrón, como 12456! y ABCD2!, se cuentan como intentos independientes.

Cuando pruebe la característica de bloqueo inteligente, use un patrón distintivo para cada contraseña que escriba. Considere la posibilidad de usar aplicaciones web de generación de contraseñas, como [https://passwordsgenerator.net/](https://passwordsgenerator.net/).

Cuando se alcance el umbral de bloqueo inteligente, recibirá el siguiente mensaje mientras la cuenta está bloqueada: **La cuenta está bloqueada temporalmente para evitar el uso no autorizado. Vuelva a intentarlo más adelante**. Los mensajes de error se pueden [localizar](localization-string-ids.md#sign-up-or-sign-in-error-messages).

> [!NOTE]
> Al probar el bloqueo inteligente, las solicitudes de inicio de sesión pueden ser controladas por distintos centros de datos debido a la naturaleza de distribución geográfica y de equilibrio de carga del servicio de autenticación de Azure AD. En ese escenario, dado que cada centro de datos de Azure AD hace un seguimiento de manera independiente, se puede tardar más que el número de intentos de bloqueo definido para producir un bloqueo. Un usuario tiene un número máximo (threshold_limit * datacenter_count) de intentos fallidos antes de bloquearse completamente.

## <a name="viewing-locked-out-accounts"></a>Visualización de las cuentas bloqueadas

Para más información sobre las cuentas bloqueadas, puede comprobar el [informe de actividad de inicio de sesión](../active-directory/reports-monitoring/concept-sign-ins.md) de Active Directory. En **Estado**, seleccione **Error**. Los intentos de inicio de sesión con un **código de error de inicio de sesión** de `50053` indican una cuenta bloqueada:

![Sección del informe de inicio de sesión de Azure AD que muestra la cuenta bloqueada](./media/threat-management/portal-01-locked-account.png)

Para más información sobre cómo ver el informe de actividad de inicio de sesión en Azure Active Directory, consulte [Códigos de error de los informes de actividad de inicio de sesión](../active-directory/reports-monitoring/concept-sign-ins.md).

