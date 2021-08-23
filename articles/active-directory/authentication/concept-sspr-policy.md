---
title: 'Políticas del autoservicio de restablecimiento de contraseña: Azure Active Directory'
description: Conozca las diferentes opciones de la directiva de autoservicio de restablecimiento de contraseña de Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: b66ab52a65dd848997d8f13dde7bfba944ad520b
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111744402"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Restricciones de cuenta y directivas de contraseñas en Azure Active Directory

En Azure Active Directory (Azure AD), hay una directiva de contraseñas que define la configuración, como la complejidad de la contraseña, la longitud o la edad. También hay una directiva que define los caracteres y longitudes de nombres de usuario aceptables.

Cuando se usa el autoservicio de restablecimiento de contraseña (SSPR) para cambiar o restablecer una contraseña en Azure AD, se comprueba la directiva de contraseñas. Si la contraseña no cumple los requisitos de la directiva, se le pedirá al usuario que vuelva a intentarlo. Los administradores de Azure tienen algunas restricciones en el uso de SSPR que son diferentes de las restricciones para cuentas de usuario normales.

En este artículo se describe la configuración de directiva de contraseñas y los requisitos de complejidad asociados a las cuentas de usuario en el inquilino de Azure AD. También se describe cómo puede usar PowerShell para comprobar o establecer la configuración de expiración de contraseña.

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Directivas de nombre de usuario

Cada cuenta que inicia sesión en Azure AD debe tener un valor de atributo de nombre principal de usuario (UPN) único asociado a esa cuenta. En entornos híbridos con un entorno local de Active Directory Domain Services (AD DS) sincronizado con Azure AD mediante Azure AD Connect, el UPN de Azure AD está establecido en el UPN local de forma predeterminada.

En la siguiente tabla, se describen las directivas de nombre de usuario que se aplican tanto a las cuentas de AD DS locales que están sincronizadas con Azure AD como a las cuentas de usuario que solo están en la nube creadas directamente en Azure AD:

| Propiedad | Requisitos de UserPrincipalName |
| --- | --- |
| Caracteres permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Caracteres no permitidos |<ul> <li>Cualquier carácter "\@\" que no separa el nombre de usuario del dominio.</li> <li>No puede contener un carácter de punto "." inmediatamente antes del símbolo "\@\".</li></ul> |
| Restricciones de longitud |<ul> <li>La longitud total no debe superar los 113 caracteres.</li><li>Puede haber hasta 64 caracteres antes del símbolo "\@\"</li><li>Puede haber hasta 48 caracteres después del símbolo "\@\"</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Directivas de contraseña de Azure AD

Una directiva de contraseñas se aplica a todas las cuentas de usuario que se crean y administran directamente en Azure AD. Algunos de los ajustes de esta directiva de contraseñas no se pueden modificar, aunque puede [configurar contraseñas prohibidas personalizadas para la protección de contraseñas de Azure AD](tutorial-configure-custom-password-protection.md) o parámetros de bloqueo de cuentas.

De manera predeterminada, una cuenta se bloquea después de 10 intentos de inicio de sesión no superados con una contraseña incorrecta. El usuario se queda bloqueado durante un minuto. Más intentos de inicio de sesión incorrectos bloquean el usuario durante mayor cantidad de tiempo. El [bloqueo inteligente](howto-password-smart-lockout.md) realiza un seguimiento de los últimos tres códigos hash de contraseña incorrecta para evitar que aumente el contador de bloqueo con la misma contraseña. Si alguien escribe la misma contraseña incorrecta varias veces, este comportamiento no hará que la cuenta se bloquee. Puede definir el umbral de bloqueo inteligente y la duración.

La directiva de contraseñas de Azure AD no se aplica a las cuentas de usuario que se sincronizan desde un entorno de AD DS local mediante Azure AD Connect, a menos que habilite *EnforceCloudPasswordPolicyForPasswordSyncedUsers*.

Se definen las siguientes opciones de directiva de contraseñas de Azure AD. A menos que se indique, no se puede cambiar estos ajustes:

| Propiedad | Requisitos |
| --- | --- |
| Caracteres permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \` ~ " ( ) ; < ></li> <li>espacio en blanco</li></ul> |
| Caracteres no permitidos | Caracteres Unicode. |
| Restricciones de contraseña |<ul><li>Un mínimo de 8 caracteres y un máximo de 256 caracteres.</li><li>requiere al menos tres de los cuatro requisitos siguientes:<ul><li>Caracteres en minúsculas.</li><li>Caracteres en mayúsculas.</li><li>Números (0-9).</li><li>Símbolos (vea las anteriores restricciones de contraseña).</li></ul></li></ul> |
| Duración de la expiración de la contraseña (vigencia máxima de la contraseña) |<ul><li>Valor predeterminado: **90** días.</li><li>El valor se puede configurar con el cmdlet `Set-MsolPasswordPolicy`del módulo Active Directory para Windows PowerShell.</li></ul> |
| Notificación de expiración de contraseña (cuándo se notifica a los usuarios la expiración de la contraseña) |<ul><li>Valor predeterminado: **14** días (antes de que expire la contraseña).</li><li>El valor se puede configurar con el cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Expiración de las contraseñas (permitir que las contraseñas no expiren nunca) |<ul><li>Valor predeterminado: **falso** (indica que las contraseñas tienen una fecha de expiración).</li><li>El valor se puede configurar para cuentas de usuario individuales mediante el cmdlet `Set-MsolUser`.</li></ul> |
| Historial de cambios de contraseña | La última contraseña *no* puede usarse de nuevo cuando el usuario cambia una contraseña. |
| Historial de restablecimientos de contraseña | La última contraseña *puede* usarse de nuevo cuando el usuario restablece una contraseña olvidada. |

## <a name="administrator-reset-policy-differences"></a>Diferencias entre directivas de restablecimiento de administrador

De forma predeterminada, las cuentas de administrador están habilitadas para el autoservicio de restablecimiento de contraseña, y se aplica una robusta directiva de restablecimiento de contraseña de *dos puertas* de forma predeterminada. Esta directiva puede ser diferente de la que se ha definido para los usuarios y no se puede cambiar. Siempre debe probar la funcionalidad de restablecimiento de contraseña como usuario sin los roles de administrador de Azure asignados.

Con una directiva de dos puertas, los administradores no tienen posibilidad de usar preguntas de seguridad.

Una directiva de dos puertas requiere dos elementos de los datos de autenticación, como una dirección de correo electrónico, una aplicación de autenticador o un número de teléfono. Se aplica una directiva de dos puertas en las siguientes circunstancias:

* Todos los roles de administrador siguientes se ven afectados:
  * Administrador del departamento de soporte técnico
  * Administrador del soporte técnico del servicio
  * Administrador de facturación
  * Soporte para asociados de nivel 1
  * Soporte para asociados de nivel 2
  * Administrador de Exchange
  * Administrador de buzones de correo
  * Administrador de Skype Empresarial
  * Administrador de usuarios
  * Escritores de directorios
  * Administrador global y administrador de la compañía
  * Administrador de SharePoint
  * Administrador de cumplimiento
  * Administrador de aplicaciones
  * Administrador de seguridad
  * Administrador de roles con privilegios
  * Administrador de Intune
  * Administrador local de dispositivo unido a Azure AD
  * Administrador del servicio de proxy de la aplicación
  * Administrador de Dynamics 365
  * Administrador de servicios de Power BI
  * Administrador de autenticación
  * Administrador de contraseñas
  * Administrador de autenticación con privilegios

* Una vez transcurridos 30 días en una suscripción de prueba
* Se ha configurado un dominio personalizado para el inquilino de Azure AD, como *contoso.com*; o
* Azure AD Connect sincroniza identidades desde el directorio local

Puede deshabilitar el uso de SSPR para las cuentas de administrador mediante el cmdlet de PowerShell [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings). El parámetro `-SelfServePasswordResetEnabled $False` deshabilita SSPR para los administradores.

### <a name="exceptions"></a>Excepciones

Las directivas de una puerta necesitan información de autenticación, como una dirección de correo electrónico o un número de teléfono. Se aplica una directiva de una puerta en las siguientes circunstancias:

* Se encuentra en los primeros 30 días de una suscripción de prueba; o
* No se ha configurado un dominio personalizado para el inquilino de Azure AD, así que se usa el predeterminado, * *.onmicrosoft.com*. No es conveniente utilizar el dominio * *.onmicrosoft.com* predeterminado para producción y
* Azure AD Connect no sincroniza identidades

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>Directivas de expiración de contraseñas

Un *administrador global* o un *administrador de usuarios* puede usar el módulo [Microsoft Azure AD para Windows PowerShell](/powershell/module/Azuread/) al configurar las contraseñas de usuario para que no expiren.

También puede usar cmdlets de PowerShell para quitar la configuración de no expirar nunca o ver qué contraseñas de usuario están configuradas para que no expiren nunca.

Esta guía se aplica a otros proveedores (como Intune y Microsoft 365) que también dependen de Azure AD para los servicios de identidad y directorio. La expiración de contraseñas es la única parte de la directiva que se puede cambiar.

> [!NOTE]
> De forma predeterminada, solo las contraseñas de cuentas de usuario que no se sincronizan a través de Azure AD Connect pueden configurarse para que no expiren. Para obtener más información sobre la sincronización de directorios, vea el artículo sobre cómo [conectar AD con Azure AD](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization#password-expiration-policy).

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>Establecer o comprobar directivas de contraseña mediante PowerShell

Para empezar, [descargue e instale el módulo de Azure AD PowerShell ](/powershell/module/Azuread/) y [conéctelo al inquilino de Azure AD](/powershell/module/azuread/connect-azuread#examples).

Una vez instalado el módulo, use los pasos a continuación para completar cada tarea según sea necesario.

### <a name="check-the-expiration-policy-for-a-password"></a>Comprobación de la directiva de expiración de una contraseña

1. Abra un símbolo del sistema de PowerShell y [conéctese con su inquilino de Azure AD](/powershell/module/azuread/connect-azuread#examples) mediante una cuenta de *administrador global* o *administrador de usuarios*.
1. Ejecute uno de los siguientes comandos para un usuario individual o para todos los usuarios:

   * Para ver si la contraseña de un solo usuario está configurada para no expirar nunca, ejecute el siguiente cmdlet. Reemplace `<user ID>` por el id. del usuario que quiere comprobar, como *driley\@contoso.onmicrosoft.com*:

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * Para ver la configuración **La contraseña nunca expira** de todos los usuarios, ejecute el siguiente cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>Configuración de una contraseña para que caduque

1. Abra un símbolo del sistema de PowerShell y [conéctese con su inquilino de Azure AD](/powershell/module/azuread/connect-azuread#examples) mediante una cuenta de *administrador global* o *administrador de usuarios*.
1. Ejecute uno de los siguientes comandos para un usuario individual o para todos los usuarios:

   * Para establecer la contraseña de un usuario para que expire, ejecute el cmdlet siguiente. Reemplace `<user ID>` por el id. del usuario que quiere comprobar, como *driley\@contoso.onmicrosoft.com*.

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * Para establecer las contraseñas de todos los usuarios de la organización de modo que expiren, use el siguiente cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>Configure una contraseña para que no caduque nunca

1. Abra un símbolo del sistema de PowerShell y [conéctese con su inquilino de Azure AD](/powershell/module/azuread/connect-azuread#examples) mediante una cuenta de *administrador global* o *administrador de usuarios*.
1. Ejecute uno de los siguientes comandos para un usuario individual o para todos los usuarios:

   * Para establecer la contraseña de un usuario para que no expire nunca, ejecute el cmdlet siguiente. Reemplace `<user ID>` por el id. del usuario que quiere comprobar, como *driley\@contoso.onmicrosoft.com*.

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * Para configurar las contraseñas de todos los usuarios de una organización para que nunca expiren, ejecute el siguiente cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > La antigüedad de las contraseñas establecidas en `-PasswordPolicies DisablePasswordExpiration` sigue basándose en el atributo `pwdLastSet`. En función del `pwdLastSet` atributo, si cambia la expiración a `-PasswordPolicies None`, todas las contraseñas que tengan una `pwdLastSet` cuya antigüedad sea superior a 90 días requieren que el usuario las cambie la próxima vez que se inicie sesión. Este cambio puede afectar a gran cantidad de usuarios.

## <a name="next-steps"></a>Pasos siguientes

Para empezar a trabajar con SSPR, consulte [Tutorial: Habilitación del autoservicio de restablecimiento de contraseña de Azure Active Directory para que los usuarios puedan desbloquear su cuenta o restablecer contraseñas](tutorial-enable-sspr.md)

Si usted o los usuarios tienen problemas con SSPR, consulte [Solución de problemas de autoservicio de restablecimiento de contraseña](./troubleshoot-sspr.md).
