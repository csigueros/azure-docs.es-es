---
title: 'Migración a Azure AD MFA con federaciones: Azure Active Directory'
description: Guía paso a paso para pasar de Servidor Azure MFA local a Azure AD MFA con federación
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
ms.openlocfilehash: 3b34538fb4d0c9dc7beb0defd22f0aa78207f0a3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730792"
---
# <a name="migrate-to-azure-ad-mfa-with-federation"></a>Migración a Azure AD MFA con federación

El cambio de la solución de autenticación multifactor (MFA) a Azure Active Directory (Azure AD) es un excelente primer paso en el recorrido a la nube. Considere también la posibilidad de pasar a Azure AD para la autenticación de usuarios en el futuro. Para obtener más información, vea el proceso de migración a Azure AD MFA con autenticación en la nube.

Para migrar a Azure AD MFA con federación, el proveedor de autenticación de Azure MFA se instala en AD FS. La relación de confianza para usuario autenticado de Azure AD y otras relaciones de confianza para usuario autenticado están configuradas para usar Azure MFA para los usuarios migrados.

En el diagrama siguiente se muestra el proceso de esta migración.

![Diagrama de flujo en el que se muestran los pasos del proceso. Coinciden con los títulos de este documento, en el mismo orden.](./media/how-to-migrate-mfa-server-to-azure-mfa-with-federation/mfa-federation-flow.png)

## <a name="create-migration-groups"></a>Creación de grupos de migración

Para crear directivas de acceso condicional, tendrá que asignarlas a grupos. Con este fin, puede usar grupos de seguridad de Azure AD existentes o grupos de Microsoft 365. También puede crear o sincronizar otros nuevos.

También necesitará un grupo de seguridad de Azure AD para migrar de forma iterativa los usuarios a Azure AD MFA. Estos grupos se usan en las reglas de notificaciones.

No reutilice los grupos que se usan para la seguridad. Si va a usar un grupo de seguridad para proteger un grupo de aplicaciones de alto valor con una directiva de acceso condicional, ese debe ser el único uso de ese grupo.

## <a name="prepare-ad-fs"></a>Preparación de AD FS

### <a name="upgrade-ad-fs-server-farm-to-2019-fbl-4"></a>Actualización de la granja de servidores de AD FS a 2019, FBL 4

En AD FS 2019, puede especificar métodos de autenticación adicionales para un usuario de confianza, como una aplicación. La pertenencia a grupos se usa para determinar el proveedor de autenticación. Al especificar un método de autenticación adicional, puede realizar la transición a Azure AD MFA y mantener intacta otra autenticación durante la transición. Para obtener más información, vea [Actualización a AD FS en Windows Server 2016 con una base de datos WID](/windows-server/identity/ad-fs/deployment/upgrading-to-ad-fs-in-windows-server). En el artículo se describe tanto la actualización de la granja a AD FS 2019 como la actualización de FBL a 4.

### <a name="configure-claims-rules-to-invoke-azure-ad-mfa"></a>Configuración de reglas de notificaciones para invocar Azure AD MFA

Ahora que Azure AD MFA es un método de autenticación adicional, puede asignar grupos de usuarios para utilizarlo. Para ello, configure reglas de notificaciones, también conocidas como relaciones de confianza para usuario autenticado. Mediante el uso de grupos, puede controlar qué proveedor de autenticación se llama de forma global o por aplicación. Por ejemplo, puede llamar a Azure AD MFA para los usuarios que se han registrado para obtener información de seguridad combinada o han migrado sus números de teléfono, y llamar al servidor de MFA para aquellos que no lo han hecho.

> [!NOTE]
> Las reglas de notificaciones necesitan un grupo de seguridad local. Antes de realizar cambios en las reglas de notificaciones, realice una copia de seguridad.


#### <a name="back-up-existing-rules"></a>Copia de seguridad de reglas existentes

Antes de configurar nuevas reglas de notificaciones, haga una copia de seguridad de las reglas existentes. Tendrá que restaurar estas reglas como parte de los pasos de limpieza. 

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

> [!NOTE]
> Las directivas de control de acceso no se pueden configurar para que se invoque un proveedor de autenticación específico en función de la pertenencia a grupos. 

 
Para realizar la transición de las directivas de control de acceso a reglas de autenticación adicionales, ejecute el siguiente comando para cada una de las relaciones de confianza para usuario autenticado mediante el proveedor de autenticación del servidor MFA:


```powershell
Set-AdfsRelyingPartyTrust -TargetName AppA -AccessControlPolicyName $Null
```

 

Este comando moverá la lógica de la directiva de control de acceso actual a Reglas de autenticación adicionales.


#### <a name="set-up-the-group-and-find-the-sid"></a>Configuración del grupo y búsqueda del SID

Necesitará un grupo específico en el que colocar a los usuarios para los que quiera invocar Azure AD MFA. Necesitará el identificador de seguridad (SID) de ese grupo.

Para buscar el SID del grupo, use el comando siguiente, con el nombre del grupo.

`Get-ADGroup “GroupName”`

![Imagen de la captura de pantalla en la que se muestran los resultados del script Get-ADGroup.](./media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/find-the-sid.png)

#### <a name="setting-the-claims-rules-to-call-azure-mfa"></a>Establecimiento de las reglas de notificaciones para llamar a Azure MFA

Los cmdlets de PowerShell siguientes invocan Azure AD MFA para los usuarios del grupo cuando no están en la red corporativa. Reemplace "YourGroupSid" por el SID encontrado mediante la ejecución del cmdlet anterior.

Asegúrese de revisar [Procedimiento para elegir proveedores de autenticación adicionales en 2019](/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server). 

 > [!IMPORTANT]
> Copia de seguridad de las reglas de notificaciones existentes

 

#### <a name="set-global-claims-rule"></a>Configuración de la regla de notificaciones global 

Ejecute el siguiente cmdlet de PowerShell: 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

 

El comando devuelve las reglas de autenticación adicionales actuales para la relación de confianza para usuario autenticado. Anexe las reglas siguientes a las reglas de notificación actuales:

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


#### <a name="set-per-application-claims-rule"></a>Establecimiento de la regla de notificaciones en cada aplicación

En este ejemplo se modifican las reglas de notificación en una relación de confianza para usuario autenticado (aplicación) concreta y se incluye la información que se debe anexar.

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

A fin de configurar Azure AD MFA para AD FS, debe configurar cada servidor AD FS. Si tiene varios servidores de AD FS en la granja, puede configurarlos de forma remota mediante Azure AD PowerShell.

Para obtener instrucciones paso a paso sobre este procedimiento, vea [Configuración de los servidores de AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa) en el artículo [Configuración de Azure AD MFA como proveedor de autenticación con AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa).

Cuando haya configurado los servidores, puede agregar Azure AD MFA como un método de autenticación adicional. 

![Captura de pantalla en la que se muestra la pantalla Edit authentication methods (Editar métodos de autenticación) con Azure MFA y Azure Mutli-factor authentication Server (Servidor de Azure Mutli-factor Authentication) seleccionados](./media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/edit-authentication-methods.png)

## <a name="prepare-azure-ad-and-implement"></a>Preparación e implementación de Azure AD

### <a name="ensure-supportsmfa-is-set-to-true"></a>Asegúrese de que SupportsMFA está establecido en True.

Para los dominios federados, MFA se puede aplicar mediante el acceso condicional de Azure AD o por medio del proveedor de federación local. Cada dominio federado de Azure AD tiene una marca SupportsMFA. Cuando la marca SupportsMFA está establecida en True, Azure AD redirige a los usuarios a MFA en AD FS o a otros proveedores de federación. Por ejemplo, si un usuario accede a una aplicación para la que se ha configurado una directiva de acceso condicional que necesita MFA, se le redirigirá a AD FS. Al agregar Azure AD MFA como método de autenticación en AD FS se puede invocar Azure AD MFA después de completar las configuraciones.

Si la marca SupportsMFA está establecida en False, es probable que no use Azure MFA; probablemente utilice reglas de notificaciones en usuarios de confianza de AD FS para invocar MFA.

Puede comprobar el estado de la marca SupportsMFA con el siguiente [cmdlet de Windows PowerShell](/powershell/module/msonline/get-msoldomainfederationsettings?view=azureadps-1.0):

```powershell
Get-MsolDomainFederationSettings –DomainName yourdomain.com
```

Si la marca SupportsMFA está establecida en false o está en blanco para el dominio federado, puede establecerla en true mediante el siguiente cmdlet de Windows PowerShell:

```powershell
Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true
```

Esta configuración permite decidir si usar el servidor MFA o Azure MFA en AD FS.

### <a name="configure-conditional-access-policies-if-needed"></a>Configuración de directivas de acceso condicional si son necesarias

Si usa el acceso condicional para determinar cuándo se solicita a los usuarios MFA, no es necesario cambiar las directivas.

Si en los dominios federados SupportsMFA está establecido en false, analice las reglas de notificaciones en la relación de confianza para usuario autenticado de Azure AD y cree directivas de acceso condicional que admitan los mismos objetivos de seguridad.

Después de crear directivas de acceso condicional para aplicar los mismos controles que AD FS, puede realizar una copia de seguridad y quitar las personalizaciones de las reglas de notificación en el usuario de confianza de Azure AD.

Para obtener más información, consulte los siguientes recursos:

* [Planeamiento de la implementación del acceso condicional](../conditional-access/plan-conditional-access.md)

* [Directivas de acceso condicional habituales](../conditional-access/concept-conditional-access-policy-common.md)


## <a name="register-users-for-azure-mfa"></a>Registro de usuarios en Azure MFA

Hay dos maneras de registrar usuarios para Azure MFA: 

* Registro para la seguridad combinada (MFA y autoservicio de restablecimiento de contraseña) 

* Migración de números de teléfono desde el servidor MFA

La aplicación Microsoft Authenticator se puede usar en el modo sin contraseña. También se puede usar como segundo factor para MFA con cualquier método de registro.

### <a name="register-for-combined-security-registration-recommended"></a>Registro para el registro de seguridad combinado (recomendado)

Haga que los usuarios se registren para la información de seguridad combinada, que es un único lugar para registrar sus métodos de autenticación y dispositivos para MFA y SSPR. Aunque es posible migrar datos del servidor MFA Azure a Azure AD MFA, presenta los siguientes desafíos:

* Solo se pueden migrar números de teléfono.

* Las aplicaciones de autenticador tendrán que volver a registrarse.

* Se pueden migrar datos obsoletos.

Microsoft ofrece plantillas de comunicación que puede proporcionar a los usuarios para guiarlos por el proceso de registro combinado. Estas incluyen plantillas para correo electrónico, pósteres, contenidos de tablas y otros recursos. Los usuarios registran su información en [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo), desde donde acceden a la pantalla de registro de seguridad combinada. 

Se recomienda [proteger el proceso de registro de seguridad con acceso condicional](../conditional-access/howto-conditional-access-policy-registration.md), para lo que es necesario que el registro se produzca desde un dispositivo o ubicación de confianza.

> [!NOTE]
> Los usuarios que TIENEN que registrar su información de seguridad combinada desde una ubicación o dispositivo que no es de confianza pueden recibir un Pase de acceso temporal, o bien se les puede excluir temporalmente de la directiva.

### <a name="migrate-phone-numbers-from-mfa-server"></a>Migración de números de teléfono desde el servidor MFA

La migración de números de teléfono puede dar lugar a la migración de números obsoletos y hacer que los usuarios sean más propensos a limitarse a la MFA basada en teléfono en lugar de configurar métodos más seguros, como [el inicio de sesión sin contraseña con la aplicación Microsoft Authenticator](howto-authentication-passwordless-phone.md). No se pueden migrar registros de dispositivos, como la configuración de la aplicación Microsoft Authenticator. Se recomienda que todos los usuarios se registren para obtener [información de seguridad combinada](howto-registration-mfa-sspr-combined.md). La información de seguridad combinada también registra a los usuarios para el autoservicio de restablecimiento de contraseña.

Si hacer que los usuarios registren su información de seguridad combinada no es una opción viable, se pueden exportar los usuarios y sus números de teléfono desde el servidor MFA e importar los números de teléfono en Azure AD.
 

#### <a name="export-user-phone-numbers-from-mfa-server"></a>Exportación de números de teléfono de usuario desde el servidor MFA 

1. Abra la consola de administración del Servidor Multi-Factor Authentication en el servidor de MFA. 

1. Seleccione **Archivo** y después **Exportar usuarios**.

1. **Guarde** el archivo .csv. El nombre predeterminado es Multi-Factor Authentication Users.csv

#### <a name="interpret-and-format-the-csv--file"></a>Interpretación y formato del archivo .csv

El archivo .csv contiene muchos campos que no son necesarios para la migración. Edite y aplique formato antes de importar los números de teléfono en Azure AD. 

Al abrir el archivo .csv, las columnas de interés son las siguientes:

* Nombre de usuario
* Teléfono principal
* Código de país principal
* Código de país de reserva
* Teléfono de reserva
* Extensión de reserva

Tendrá que interpretar, limpiar y dar formato a los datos.

#### <a name="tips-to-avoid-errors-during-import"></a>Sugerencias para evitar errores durante la importación

* Modifique el archivo .csv antes de usar la API de métodos de autenticación para importar los números de teléfono en Azure AD. 

* Simplifique el archivo .csv a tres columnas: UPN, PhoneType y PhoneNumber. 

* Asegúrese de que el nombre de usuario del servidor MFA exportado coincide con el valor UserPrincipalName de Azure AD. Si no es así, actualice el nombre de usuario en el archivo CSV para que coincida con el de Azure AD; de lo contrario, no se podrá encontrar al usuario.

Es posible que los usuarios ya hayan registrado números de teléfono en Azure AD. Al importar los números de teléfono mediante la API de métodos de autenticación, debe decidir si quiere sobrescribir el número de teléfono existente o agregar el número importado como un número de teléfono alternativo.

Los cmdlets de PowerShell siguientes toman el archivo .csv que proporciona y agregan los números de teléfono exportados como un número de teléfono para cada UPN mediante la API de métodos de autenticación. Reemplace "myPhones" por el nombre del archivo .csv.

```powershell

$csv = import-csv myPhones.csv

$csv|% { New-MgUserAuthenticationPhoneMethod -UserId $_.UPN -phoneType $_.PhoneType -phoneNumber $_.PhoneNumber} 

```

### <a name="add-users-to-the-appropriate-groups"></a>Adición de usuarios a los grupos adecuados

* Si ha creado directivas de acceso condicional, agregue los usuarios adecuados a esos grupos. 

* Si ha creado grupos de seguridad locales para reglas de notificaciones, agregue los usuarios adecuados a esos grupos.

No se recomienda reutilizar los grupos que se usan para la seguridad. Por tanto, si va a usar un grupo de seguridad para proteger un grupo de aplicaciones de alto valor con una directiva de acceso condicional, ese debe ser el único uso de ese grupo.

## <a name="monitoring"></a>Supervisión

El registro de Azure MFA se puede supervisar por medio del [informe de uso y conclusiones de los métodos de autenticación](https://portal.azure.com/). Este informe se puede encontrar en Azure AD. Seleccione **Supervisión** y después **Uso y conclusiones**. 

En Uso y conclusiones, seleccione **Métodos de autenticación**. 

Puede encontrar información detallada sobre el registro de Azure MFA en la pestaña Registro. Puede explorar en profundidad para ver una lista de usuarios registrados si selecciona el hipervínculo **Usuarios compatibles con Azure Multi-Factor Authentication**.

![Imagen de la pantalla de actividad Métodos de autenticación en la que se muestran los registros de usuario en MFA](./media/how-to-migrate-mfa-server-to-azure-mfa-with-federation/authentication-methods.png)

   

## <a name="clean-up-steps&quot;></a>Pasos de limpieza

Cuando haya completado la migración a Azure MFA y esté listo para retirar el servidor MFA, haga lo siguiente: 

1. Revierta las reglas de notificación en AD FS a su configuración previa a la migración y quite el proveedor de autenticación del servidor MFA.

1. Quite el servidor MFA como proveedor de autenticación en AD FS. Esto garantizará que todos los usuarios usen Azure MFA, ya que será el único método de autenticación adicional habilitado. 

1. Retire el servidor MFA.

### <a name=&quot;revert-claims-rules-on-ad-fs-and-remove-mfa-server-authentication-provider&quot;></a>Reversión de las reglas de notificaciones en AD FS y eliminación del proveedor de autenticación del servidor MFA

Siga los pasos descritos en Configuración de reglas de notificaciones para invocar Azure AD MFA a fin de revertir las reglas de notificaciones de copia de seguridad y quitar las reglas de notificaciones AzureMFAServerAuthentication. 

Por ejemplo, quite lo siguiente de las reglas: 

 
```console
c:[Type == &quot;https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid&quot;, Value ==
“**YourGroupSID**&quot;] => issue(Type = &quot;https://schemas.microsoft.com/claims/authnmethodsproviders&quot;,
Value = &quot;AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
Value=="YourGroupSid"]) => issue(Type =
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value =
"AzureMfaServerAuthentication");’
```

### <a name="disable-mfa-server-as-an-authentication-provider-in-ad-fs"></a>Deshabilitación del servidor MFA como proveedor de autenticación en AD FS

Este cambio garantiza que solo se use Azure MFA como proveedor de autenticación.

1. Abra la **consola de administración de AD FS**.

1. En **Servicios**, haga clic con el botón derecho en **Métodos de autenticación** y seleccione **Editar métodos Multi-Factor Authentication**. 

1. Desactive la casilla situada junto a **Azure Multi-Factor Authentication Server**. 

### <a name="decommission-the-mfa-server"></a>Retirada del servidor MFA

Siga el proceso de retirada del servidor empresarial para quitar los servidores MFA del entorno.

Entre las posibles consideraciones al retirar los servidores MFA se incluyen las siguientes: 

* Revise los registros de los servidores MFA para asegurarse de que ningún usuario o aplicación lo usa antes de quitar el servidor.

* Desinstalación del servidor Multi-Factor Authentication desde el panel de control en el servidor

* Opcionalmente, limpie los registros y los directorios de datos restantes después de realizar primero una copia de seguridad. 

* Desinstale el SDK del servidor web Multi-Factor Authentication si procede, incluidos los archivos que hayan quedado en los directorios etpub\wwwroot\MultiFactorAuthWebServiceSdk y MultiFactorAuth.

* Para las versiones del servidor MFA anteriores a la 8.0, también puede ser necesario quitar el servicio web Multi-Factor Auth Phone App.

## <a name="next-steps"></a>Pasos siguientes

- [Habilitación de la sincronización de hash de contraseñas](../hybrid/whatis-phs.md)
- [Más información sobre el acceso condicional](../conditional-access/overview.md)
- [Migración de aplicaciones a Azure AD](../manage-apps/migrate-application-authentication-to-azure-active-directory.md)

 

 

 
