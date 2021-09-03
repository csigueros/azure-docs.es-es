---
title: 'Migración a Azure AD MFA y a la autenticación de usuario de Azure AD: Azure Active Directory'
description: Guía paso a paso para pasar del servidor Azure MFA local a Azure AD MFA y a la autenticación de usuario de Azure AD
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f10052f6bcb458b98535d54a9b2fa5deb749c38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730806"
---
# <a name="migrate-to-azure-ad-mfa-and-azure-ad-user-authentication"></a>Migración a Azure AD MFA y a la autenticación de usuario de Azure AD

La autenticación multifactor (MFA) ayuda a proteger la infraestructura y los recursos de actores no seguros. El servidor Multi-Factor Authentication (servidor MFA) de Microsoft ya no se ofrece para las nuevas implementaciones. Los clientes que usan el servidor MFA deben pasar a Azure AD Multi-Factor Authentication (Azure AD MFA). 

Hay varias opciones para migrar la autenticación multifactor (MFA) del servidor MFA a Azure Active Directory (Azure AD). Entre ellas se incluyen las siguientes:

* Bueno: mover solo el [servicio MFA a Azure AD](how-to-migrate-mfa-server-to-azure-mfa.md). 
* Mejor: mover el servicio MFA y la autenticación de usuario a Azure AD, que se trata en este artículo.
* Óptimo: mover todas las aplicaciones, el servicio MFA y la autenticación de usuario a Azure AD. Si tiene previsto mover aplicaciones, consulte la sección de este artículo sobre el traslado de aplicaciones a Azure AD. 

Para seleccionar la opción de migración de MFA adecuada para su organización, consulte las consideraciones en [Migración del servidor MFA a Azure Active Directory MFA](how-to-migrate-mfa-server-to-azure-mfa.md). 

En el diagrama siguiente se muestra el proceso para migrar a Azure AD MFA y a la autenticación en la nube, a la vez que se mantienen algunas de las aplicaciones en AD FS. Este proceso permite la migración iterativa de usuarios del servidor MFA a Azure MFA en función de la pertenencia a grupos.

Este paso se explica con detalle en las siguientes secciones de este artículo.

>[!NOTE]
>Si planea mover cualquier aplicación a Azure Active Directory como parte de esta migración, debe hacerlo antes de la migración de MFA. Si mueve todas las aplicaciones, puede omitir secciones del proceso de migración de MFA. Consulte la sección sobre cómo mover aplicaciones al final de este artículo.

## <a name="process-to-migrate-to-azure-ad-and-user-authentication"></a>Proceso para migrar a Azure AD y a la autenticación de usuario

![Proceso para migrar a Azure AD y a la autenticación de usuario.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/mfa-cloud-authentication-flow.png)

## <a name="prepare-groups-and-conditional-access"></a>Preparación de grupos y acceso condicional

Los grupos se usan en tres capacidades para la migración de MFA.
* **Mover de forma iterativa a los usuarios a Azure AD MFA con el lanzamiento preconfigurado.**
Use un grupo creado en Azure AD, también conocido como grupo solo en la nube. Puede usar grupos de seguridad de Azure AD o grupos de Microsoft 365 para mover usuarios a MFA y para las directivas de acceso condicional.  Para obtener más información, consulte cómo crear un grupo de seguridad de Azure AD y esta información general de grupos de Microsoft 365 para administradores.
  >[!IMPORTANT]
  >No se admiten grupos anidados y dinámicos en el proceso de lanzamiento preconfigurado. No use estos tipos de grupos para el esfuerzo de lanzamiento preconfigurado.
* **Directivas de acceso condicional**. Puede usar grupos Azure AD o locales para el acceso condicional.
* **Invocar Azure AD MFA para aplicaciones de AD FS con reglas de notificaciones.**
Esto solo se aplica si tiene aplicaciones en AD FS.
Debe ser un grupo de seguridad de Active Directory local. Una vez que Azure AD MFA sea un método de autenticación adicional, podrá designar grupos de usuarios para usar ese método en cada relación de confianza para usuario autenticado. Por ejemplo, puede llamar a Azure AD MFA para los usuarios que ya ha migrado y al servidor MFA para los que aún no se han migrado. Esto resulta útil tanto en las pruebas como durante la migración. 

>[!NOTE] 
>No se recomienda reutilizar los grupos que se usan para la seguridad. Al usar un grupo de seguridad para proteger un grupo de aplicaciones de alto valor con una directiva de acceso condicional, ese debe ser el único uso de ese grupo.

### <a name="configure-conditional-access-policies"></a>Configuración de directivas de acceso condicional

Si ya usa el acceso condicional para determinar cuándo se solicita a los usuarios MFA, no necesitará ningún cambio en las directivas. A medida que los usuarios se migran a la autenticación en la nube, comenzarán a usar Azure AD MFA tal como se define en las directivas de acceso condicional existentes. Ya no se redirigirán a AD FS ni al servidor MFA.

Si los dominios federados tienen SupportsMFA establecido en false, es probable que esté aplicando MFA en AD FS mediante reglas de notificaciones. En este caso, deberá analizar las reglas de notificaciones en la relación de confianza para usuario autenticado de Azure AD y crear directivas de acceso condicional que admitan los mismos objetivos de seguridad.

Si necesita configurar directivas de acceso condicional, debe hacerlo antes de habilitar el lanzamiento preconfigurado. Para obtener más información, consulte los siguientes recursos:
* [Planeamiento de la implementación del acceso condicional](../conditional-access/plan-conditional-access.md)
* [Directivas de acceso condicional habituales](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="prepare-ad-fs"></a>Preparación de AD FS 

Si no tiene ninguna aplicación en AD FS que requiera MFA, puede omitir esta sección e ir a la sección Preparación del lanzamiento preconfigurado.

### <a name="upgrade-ad-fs-server-farm-to-2019-fbl-4"></a>Actualización de la granja de servidores de AD FS a 2019, FBL 4

En AD FS 2019, Microsoft lanzó una nueva funcionalidad que proporciona la capacidad de especificar métodos de autenticación adicionales para un usuario autenticado, como una aplicación. Esto se hace mediante la pertenencia a grupos para determinar el proveedor de autenticación. Al especificar un método de autenticación adicional, puede realizar la transición a Azure AD MFA y mantener intacta otra autenticación durante la transición. 

Para obtener más información, vea [Actualización a AD FS en Windows Server 2016 con una base de datos WID](/windows-server/identity/ad-fs/deployment/upgrading-to-ad-fs-in-windows-server). En el artículo se describe tanto la actualización de la granja a AD FS 2019 como la actualización de FBL a 4.

### <a name="configure-claims-rules-to-invoke-azure-ad-mfa"></a>Configuración de reglas de notificaciones para invocar Azure AD MFA

Ahora que Azure AD MFA es un método de autenticación adicional, puede asignar grupos de usuarios para utilizarlo. Para ello, configure reglas de notificaciones, también conocidas como *relaciones de confianza para usuario autenticado*. Mediante el uso de grupos, puede controlar qué proveedor de autenticación se llama de forma global o por aplicación. Por ejemplo, puede llamar a Azure AD MFA para los usuarios que se han registrado para obtener información de seguridad combinada o han migrado sus números de teléfono, y llamar al servidor de MFA para aquellos que no lo han hecho. 

>[!NOTE]
>Las reglas de notificaciones necesitan un grupo de seguridad local. 

#### <a name="back-up-existing-rules"></a>Copia de seguridad de reglas existentes 

Antes de configurar nuevas reglas de notificaciones, haga una copia de seguridad de las reglas existentes. Tendrá que restaurarlas como parte de los pasos de limpieza. 

En función de la configuración, es posible que también tenga que copiar la regla existente y anexar las reglas que se crean para la migración.

Para ver las reglas globales existentes, ejecute lo siguiente:  
```powershell
Get-AdfsAdditionalAuthenticationRule
```

Para ver las relaciones de confianza para usuario autenticado existentes, ejecute el siguiente comando y reemplace RPTrustName por el nombre de la regla de notificaciones de relación de confianza para usuario autenticado: 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

#### <a name="access-control-policies"></a>Directivas de control de acceso

>[!NOTE]
>Las directivas de control de acceso no se pueden configurar para que se invoque un proveedor de autenticación específico en función de la pertenencia a grupos. 

Para realizar la transición de las directivas de control de acceso a reglas de autenticación adicionales, ejecute el siguiente comando para cada una de las relaciones de confianza para usuario autenticado mediante el proveedor de autenticación del servidor MFA:

```powershell
Set-AdfsRelyingPartyTrust -**TargetName AppA -AccessControlPolicyName $Null**
```

Este comando moverá la lógica de la directiva de control de acceso actual a Reglas de autenticación adicionales.

#### <a name="set-up-the-group-and-find-the-sid"></a>Configuración del grupo y búsqueda del SID

Necesitará un grupo específico en el que colocar a los usuarios para los que quiera invocar Azure AD MFA. Necesitará el identificador de seguridad (SID) de ese grupo.
Para buscar el SID del grupo, use el comando siguiente, con el nombre del grupo `Get-ADGroup “GroupName”`.

![Comando de PowerShell para obtener el SID del grupo.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/find-the-sid.png)

#### <a name="setting-the-claims-rules-to-call-azure-mfa"></a>Establecimiento de las reglas de notificaciones para llamar a Azure MFA

Los cmdlets de PowerShell siguientes invocan Azure AD MFA para los usuarios del grupo cuando no están en la red corporativa. Debe reemplazar "YourGroupSid" por el SID encontrado mediante la ejecución del cmdlet anterior.

Asegúrese de revisar [Procedimiento para elegir proveedores de autenticación adicionales en 2019](/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server#how-to-choose-additional-auth-providers-in-2019). 

>[!IMPORTANT] 
>Haga una copia de seguridad de las reglas de notificaciones existentes antes de continuar.

##### <a name="set-global-claims-rule"></a>Configuración de la regla de notificaciones global 

Ejecute el siguiente comando y reemplace RPTrustName por el nombre de la regla de notificaciones de la relación de confianza para usuario autenticado: 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

El comando devuelve las reglas de autenticación adicionales actuales para la relación de confianza para usuario autenticado.  
Anexe las reglas siguientes a las reglas de notificación actuales:

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

En el ejemplo siguiente se da por supuesto que las reglas de notificación actuales están configuradas para solicitar MFA cuando los usuarios se conectan desde fuera de la red. En este ejemplo se incluyen las reglas adicionales que debe anexar.

```PowerShell
Set-AdfsAdditionalAuthenticationRule -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
 c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
“YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value==“YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

##### <a name="set-per-application-claims-rule"></a>Establecimiento de la regla de notificaciones en cada aplicación

En este ejemplo se modifican las reglas de notificación en una relación de confianza para usuario autenticado (aplicación) específica. Se incluyen las reglas adicionales que debe anexar.

```PowerShell
Set-AdfsRelyingPartyTrust -TargetName AppA -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
“YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value==“YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

### <a name="configure-azure-ad-mfa-as-an-authentication-provider-in-ad-fs"></a>Configuración de Azure AD MFA como proveedor de autenticación en AD FS

A fin de configurar Azure AD MFA para AD FS, debe configurar cada servidor de AD FS. Si tiene varios servidores de AD FS en la granja, puede configurarlos de forma remota mediante Azure AD PowerShell.

Para obtener instrucciones paso a paso sobre este proceso, consulte [Configuración de los servidores de AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Cuando haya configurado los servidores, puede agregar Azure AD MFA como un método de autenticación adicional. 

![Captura de pantalla de cómo agregar Azure AD MFA como método de autenticación adicional.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/edit-authentication-methods.png)


## <a name="prepare-staged-rollout"></a>Preparación del lanzamiento preconfigurado 

Ahora está listo para habilitar la característica de lanzamiento preconfigurado. El lanzamiento preconfigurado le ayuda a trasladar de forma iterativa a los usuarios a PHS o PTA. 

* Asegúrese de revisar los [escenarios admitidos](../hybrid/how-to-connect-staged-rollout.md#supported-scenarios). 
* En primer lugar, deberá realizar el [trabajo previo para PHS](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-password-hash-sync) o el [trabajo previo para PTA](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-pass-through-authentication). Se recomienda PHS. 
* A continuación, realizará el [trabajo previo para el inicio de sesión único de conexión directa](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-seamless-sso). 
* [Habilite el lanzamiento preconfigurado de la autenticación en la nube](../hybrid/how-to-connect-staged-rollout.md#enable-a-staged-rollout-of-a-specific-feature-on-your-tenant) para el método de autenticación seleccionado. 
* Agregue los grupos que creó para el lanzamiento preconfigurado. Recuerde que agregará usuarios a grupos de forma iterativa y que no pueden ser grupos dinámicos ni grupos anidados. 

## <a name="register-users-for-azure-mfa"></a>Registro de usuarios en Azure MFA

Hay dos maneras de registrar usuarios para Azure MFA: 

* Registro para la seguridad combinada (MFA y autoservicio de restablecimiento de contraseña) 
* Migración de números de teléfono desde el servidor MFA

La aplicación Microsoft Authenticator se puede usar como método de inicio de sesión sin contraseña, así como un segundo factor para MFA con cualquiera de los métodos.

### <a name="register-for-combined-security-registration-recommended"></a>Registro para el registro de seguridad combinado (recomendado)

Se recomienda que los usuarios se registren para la información de seguridad combinada, que es un único lugar para registrar sus métodos de autenticación y dispositivos para MFA y SSPR. Aunque es posible migrar datos del servidor MFA a Azure AD MFA, se pueden presentar los siguientes desafíos:

* Solo se pueden migrar números de teléfono.
* Las aplicaciones de autenticador tendrán que volver a registrarse.
* Se pueden migrar datos obsoletos.

Microsoft ofrece plantillas de comunicación que puede proporcionar a los usuarios para guiarlos por el proceso de registro combinado. Estas incluyen plantillas para correo electrónico, pósteres, cartelería de mesa y otros recursos. Los usuarios registran su información en `https://aka.ms/mysecurityinfo`, desde donde acceden a la pantalla de registro de seguridad combinada. 

Se recomienda [proteger el proceso de registro de seguridad con acceso condicional](../conditional-access/howto-conditional-access-policy-registration.md), para lo que es necesario que el registro se produzca desde un dispositivo o ubicación de confianza. Para obtener información sobre el seguimiento de los estados de registro, consulte [Actividad del método de autenticación para Azure Active Directory](howto-authentication-methods-activity.md).
> [!NOTE]
> Los usuarios que TIENEN que registrar su información de seguridad combinada desde una ubicación o dispositivo que no es de confianza pueden recibir un Pase de acceso temporal, o bien se les puede excluir temporalmente de la directiva.

### <a name="migrate-phone-numbers-from-mfa-server"></a>Migración de números de teléfono desde el servidor MFA

Aunque puede migrar los números de teléfono y tokens de hardware de MFA registrados de los usuarios, no puede migrar los registros de dispositivos, como su configuración de la aplicación Microsoft Authenticator. La migración de números de teléfono puede dar lugar a la migración de números obsoletos y hacer que los usuarios sean más propensos a limitarse a la MFA basada en teléfono en lugar de configurar métodos más seguros, como [el inicio de sesión sin contraseña con la aplicación Microsoft Authenticator](howto-authentication-passwordless-phone.md). Por lo tanto, se recomienda que, independientemente de la ruta de migración que elija, todos los usuarios se registren para obtener [información de seguridad combinada](howto-registration-mfa-sspr-combined.md). La información de seguridad combinada también permite a los usuarios registrarse para el autoservicio de restablecimiento de contraseña.

Si hacer que los usuarios registren su información de seguridad combinada no es una opción viable, se pueden exportar los usuarios y sus números de teléfono desde el servidor MFA e importar los números de teléfono en Azure AD. 

#### <a name="export-user-phone-numbers-from-mfa-server"></a>Exportación de números de teléfono de usuario desde el servidor MFA 

1. Abra la consola de administración del servidor Multi-Factor Authentication en el servidor de MFA. 
1. Elija **Archivo** > **Exportar usuarios**.
3)  Guarde el archivo CSV. El nombre predeterminado es Multi-Factor Authentication Users.csv.

#### <a name="interpret-and-format-the-csv-file"></a>Interpretación y formato del archivo .csv

El archivo .csv contiene una serie de campos que no son necesarios para la migración y deberá editarse y formatearse antes de importar los números de teléfono a Azure AD. 

Al abrir el archivo .csv, las columnas de interés incluyen Nombre de usuario, Teléfono principal, Código de país principal, Código de país de copia de seguridad, Teléfono de copia de seguridad y Extensión de copia de seguridad. Debe interpretar estos datos y dar formato, según sea necesario.

#### <a name="tips-to-avoid-errors-during-import"></a>Sugerencias para evitar errores durante la importación

* El archivo CSV deberá modificarse antes de usar la API de métodos de autenticación para importar los números de teléfono en Azure AD. 
* Se recomienda simplificar el archivo .csv a tres columnas: UPN, PhoneType y PhoneNumber. 

  ![Captura de pantalla de un ejemplo csv.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/csv-example.png)

* Asegúrese de que el nombre de usuario del servidor MFA exportado coincide con el valor UserPrincipalName de Azure AD. Si no es así, actualice el nombre de usuario en el archivo CSV para que coincida con el de Azure AD; de lo contrario, no se podrá encontrar al usuario.

Es posible que los usuarios ya hayan registrado números de teléfono en Azure AD. Al importar los números de teléfono mediante la API de métodos de autenticación, debe decidir si quiere sobrescribir el número de teléfono existente o agregar el número importado como un número de teléfono alternativo.

Los cmdlets de PowerShell siguientes toman el archivo .csv que proporciona y agregan los números de teléfono exportados como un número de teléfono para cada UPN mediante la API de métodos de autenticación. Reemplace "myPhones" por el nombre del archivo .csv.


```powershell
$csv = import-csv myPhones.csv
$csv|% { New-MgUserAuthenticationPhoneMethod -UserId $_.UPN -phoneType $_.PhoneType -phoneNumber $_.PhoneNumber} 
```

Para obtener más información sobre cómo administrar los métodos de autenticación de los usuarios, consulte [Administración de los métodos de autenticación para Azure AD Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

### <a name="add-users-to-the-appropriate-groups"></a>Adición de usuarios a los grupos adecuados 

* Si ha creado directivas de acceso condicional, agregue los usuarios adecuados a esos grupos. 
* Si ha creado grupos de seguridad locales para reglas de notificaciones, agregue los usuarios adecuados a esos grupos. 
* Solo después de agregar usuarios a las reglas de acceso condicional adecuadas, agregue usuarios al grupo que creó para el lanzamiento preconfigurado. Una vez hecho esto, empezarán a usar el método de autenticación de Azure que seleccionó (PHS o PTA) y Azure AD MFA cuando sean necesarios para realizar la autenticación multifactor.

> [!IMPORTANT] 
> No se admiten grupos anidados y dinámicos en el proceso de lanzamiento preconfigurado. No use estos tipos de grupos. 

No se recomienda reutilizar los grupos que se usan para la seguridad. Por tanto, si va a usar un grupo de seguridad para proteger un grupo de aplicaciones de alto valor con una directiva de acceso condicional, ese debe ser el único uso de ese grupo.

## <a name="monitoring"></a>Supervisión

Hay una serie de [libros de Azure Monitor](../reports-monitoring/howto-use-azure-monitor-workbooks.md) e informes de uso y conclusiones disponibles para supervisar la implementación. Se pueden encontrar en Azure AD en el panel de navegación en **Supervisión**. 

### <a name="monitoring-staged-rollout"></a>Supervisión del lanzamiento preconfigurado

En la sección [libros](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Workbooks), seleccione **Plantillas públicas**. En la sección **Autenticación híbrida**, seleccione el libro **Grupos, usuarios e inicios de sesión en el lanzamiento preconfigurado**.

Este libro se puede usar para supervisar lo siguiente: 
* Usuarios y grupos agregados al lanzamiento preconfigurado.
* Usuarios y grupos eliminados del lanzamiento preconfigurado.
* Errores de inicio de sesión para los usuarios en el lanzamiento preconfigurado y los motivos de los errores.

### <a name="monitoring-azure-mfa-registration"></a>Supervisión del registro de Azure MFA
El registro de Azure MFA se puede supervisar por medio del [informe de uso y conclusiones de los métodos de autenticación](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthenticationMethodsMenuBlade/AuthMethodsActivity/menuId/AuthMethodsActivity). Este informe se puede encontrar en Azure AD. Seleccione **Supervisión** y después **Uso y conclusiones**. 

![Captura de pantalla de cómo buscar el informe de uso y conclusiones.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/usage-report.png)

En Uso y conclusiones, seleccione **Métodos de autenticación**. 

Puede encontrar información detallada sobre el registro de Azure MFA en la pestaña Registro. Puede explorar en profundidad para ver una lista de usuarios registrados si selecciona el hipervínculo **Usuarios registrados para Azure Multi-Factor Authentication**.

![Captura de pantalla de la pestaña Registro.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/registration-tab.png)

### <a name="monitoring-app-sign-in-health"></a>Supervisión del estado de inicio de sesión de la aplicación

Supervise las aplicaciones que ha movido a Azure AD con el libro de estado de inicio de sesión de la aplicación o el informe de uso de la actividad de la aplicación.

* **Libro de estado de inicio de sesión de la aplicación**. Consulte [Supervisión del estado de inicio de sesión de la aplicación para resiliencia](../fundamentals/monitor-sign-in-health-for-resilience.md) para obtener instrucciones detalladas sobre el uso de este libro.
* **Informe de uso de la actividad de la aplicación de Azure AD**. Este [informe](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsageAndInsightsMenuBlade/Azure%20AD%20application%20activity) se puede usar para ver los inicios de sesión correctos y con errores para aplicaciones individuales, así como la capacidad de explorar en profundidad y ver la actividad de inicio de sesión de una aplicación específica. 

## <a name="clean-up-tasks"></a>Tareas de limpieza

Una vez que haya movido todos los usuarios a la autenticación en la nube de Azure AD y a Azure MFA, debería estar listo para retirar el servidor MFA. Se recomienda que revise los registros del servidor MFA para asegurarse de que ningún usuario o aplicación lo usa antes de quitar el servidor.

### <a name="convert-your-domains-to-managed-authentication"></a>Conversión de dominios a autenticación administrada

Ahora debe [convertir los dominios federados de Azure AD en administrados](../hybrid/migrate-from-federation-to-cloud-authentication.md#convert-domains-from-federated-to-managed) y quitar la configuración de lanzamiento preconfigurado. Esto garantiza que los nuevos usuarios usen la autenticación en la nube sin agregarse a los grupos de migración.

### <a name="revert-claims-rules-on-ad-fs-and-remove-mfa-server-authentication-provider"></a>Reversión de las reglas de notificaciones en AD FS y eliminación del proveedor de autenticación del servidor MFA

Siga los pasos descritos en [Configuración de reglas de notificaciones para invocar Azure AD MFA](#configure-claims-rules-to-invoke-azure-ad-mfa) a fin de revertir las reglas de notificaciones de copia de seguridad y quitar las reglas de notificaciones AzureMFAServerAuthentication. 

Por ejemplo, quite lo siguiente de las reglas: 

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value ==
“**YourGroupSID**"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders",
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
Value=="YourGroupSid"]) => issue(Type =
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value =
"AzureMfaServerAuthentication");’
```


### <a name="disable-mfa-server-as-an-authentication-provider-in-ad-fs"></a>Deshabilitación del servidor MFA como proveedor de autenticación en AD FS

Este cambio garantiza que solo se use Azure MFA como proveedor de autenticación.

1. Abra la **consola de administración de AD FS**.
1. En **Servicios**, haga clic con el botón derecho en **Métodos de autenticación** y seleccione **Editar métodos Multi-Factor Authentication**. 
1. Desactive la casilla **Servidor Azure Multi-Factor Authentication**. 

### <a name="decommission-the-mfa-server"></a>Retirada del servidor MFA

Siga el proceso de retirada del servidor empresarial para quitar los servidores MFA del entorno.

Entre las posibles consideraciones al retirar el servidor MFA se incluyen las siguientes: 

* Se recomienda que revise los registros del servidor MFA para asegurarse de que ningún usuario o aplicación lo usa antes de quitar el servidor.
* Desinstalación del servidor Multi-Factor Authentication desde el panel de control en el servidor.
* Opcionalmente, limpie los registros y los directorios de datos restantes después de realizar primero una copia de seguridad. 
* Desinstale el SDK del servidor web Multi-Factor Authentication, si procede, incluidos los archivos que hayan quedado en los directorios inetpub\wwwroot\MultiFactorAuthWebServiceSdk o MultiFactorAuth.
* Para las versiones de servidor MFA anteriores a la 8.0, también puede ser necesario quitar el servicio web Multi-Factor Auth Phone App.

## <a name="move-application-authentication-to-azure-active-directory"></a>Traslado de la autenticación de la aplicación a Azure Active Directory

Si migra toda la autenticación de la aplicación junto con la autenticación de usuario y MFA, podrá quitar partes significativas de la infraestructura local, lo que reduce los costes y los riesgos. Si mueve toda la autenticación de la aplicación, puede omitir la fase de [Preparación de AD FS](#prepare-ad-fs) y simplificar la migración de MFA.

El proceso para mover toda la autenticación de la aplicación se muestra en el diagrama siguiente.

![Proceso para migrar aplicaciones a Azure AD MFA.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/mfa-app-migration-flow.png)

Si no es posible mover todas las aplicaciones antes de la migración, mueva las aplicaciones que pueda antes de comenzar.
Para obtener más información sobre cómo migrar aplicaciones a Azure, consulte [Recursos para migrar aplicaciones a Azure Active Directory](../manage-apps/migration-resources.md).

## <a name="next-steps"></a>Pasos siguientes

- [Migración desde el servidor Microsoft MFA a Azure Multi-factor Authentication (información general)](how-to-migrate-mfa-server-to-azure-mfa.md)
- [Migración de aplicaciones de Windows Active Directory a Azure Active Directory](../manage-apps/migrate-application-authentication-to-azure-active-directory.md)
- [Planificación de la estrategia de autenticación en la nube](../fundamentals/active-directory-deployment-plans.md)