---
title: 'Inicio de sesión con una clave de seguridad sin contraseña en recursos locales: Azure Active Directory'
description: Aprenda a habilitar el inicio de sesión con una clave de seguridad sin contraseña en recursos locales con Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce8869a15a1c94ab2f3227780de8eb37bbf52b6f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505429"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-by-using-azure-ad"></a>Habilitación del inicio de sesión con una clave de seguridad sin contraseña en recursos locales mediante Azure AD 

Este documento gira en torno a la habilitación de la autenticación sin contraseña en recursos locales en entornos con dispositivos Windows 10 *unidos a Azure Active Directory (Azure AD)* y *unidos a Azure AD híbrido*. Esta funcionalidad de autenticación sin contraseña proporciona un inicio de sesión único (SSO) sin complicaciones a los recursos locales cuando se usan claves de seguridad compatibles con Microsoft.

## <a name="use-sso-to-sign-in-to-on-premises-resources-by-using-fido2-keys"></a>Uso del inicio de sesión único para iniciar sesión en recursos locales mediante claves FIDO2

Azure AD puede emitir vales de concesión de vales (TGT) de Kerberos para uno o varios de los dominios de Active Directory. Con esta funcionalidad los usuarios pueden iniciar sesión en Windows con credenciales modernas, como claves de seguridad FIDO2, y luego acceder a recursos tradicionales basados en Active Directory. Los vales de servicio de Kerberos y la autorización se siguen controlando mediante los controladores de dominio locales de Active Directory.

Se crea un objeto de servidor de Kerberos de Azure AD en la instancia local de Active Directory y luego se publica de forma segura en Azure Active Directory. El objeto no está asociado a ningún servidor físico. Simplemente se trata de un recurso que se puede usar en Azure Active Directory para generar TGT de Kerberos para el dominio de Active Directory.

:::image type="Image" source="./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png" alt-text="Diagrama que muestra cómo obtener un TGT de Azure AD y Active Directory Domain Services." lightbox="./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png":::

1. Un usuario inicia sesión en un dispositivo Windows 10 con una clave de seguridad FIDO2 y se autentica en Azure AD.
1. Azure AD busca en el directorio una clave de servidor de Kerberos que coincida con el dominio de Active Directory local del usuario.

   Azure AD genera un TGT de Kerberos para el dominio de Active Directory local del usuario. El TGT incluye solo el SID del usuario, sin ningún dato de autorización.

1. El TGT se devuelve al cliente junto con su token de actualización principal (PRT) de Azure AD.
1. La máquina cliente se pone en contacto con un controlador de dominio de Azure AD local e intercambia el TGT parcial por un TGT completo.
1. La máquina cliente tiene ahora un PRT de Azure AD y un TGT de Active Directory completo y puede acceder a recursos locales y en la nube.

## <a name="prerequisites"></a>Prerrequisitos

Antes de empezar con los procedimientos de este artículo, la organización debe completar las instrucciones de [Habilitación del inicio de sesión con clave de seguridad sin contraseña en dispositivos Windows 10](howto-authentication-passwordless-security-key.md).

También debe cumplir los siguientes requisitos del sistema:

- Los dispositivos deben ejecutar Windows 10, versión 2004 o posterior.

- Debe ejecutar [Azure AD Connect, versión 1.4.32.0 o posterior](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
  - Para obtener más información sobre las opciones de autenticación híbrida de Azure AD disponibles, vea los artículos siguientes:
    - [Seleccione el método de autenticación adecuado para su solución de identidad híbrida de Azure Active Directory](../hybrid/choose-ad-authn.md)
    - [Selección del tipo de instalación que se usará para Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md)

- Los controladores de dominio de Windows Server deben tener instaladas las revisiones de los servidores siguientes:
    - [Windows Server 2016](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
    - [Windows Server 2019](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="supported-scenarios"></a>Escenarios admitidos

El escenario de este artículo admite el inicio de sesión único en las dos instancias siguientes:

- Recursos en la nube, como aplicaciones de Microsoft 365 y otras habilitadas para Lenguaje de marcado de aserción de seguridad (SAML).
- Recursos locales, y autenticación integrada de Windows en sitios web. Los recursos pueden incluir sitios web y sitios de SharePoint que requieran autenticación de IIS o recursos que usen autenticación de NTLM.

### <a name="unsupported-scenarios"></a>Escenarios no admitidos

No se admiten los escenarios siguientes:

- Implementación unida a Active Directory Domain Services (AD DS) de Windows Server (solo dispositivos locales).
- Escenarios de Protocolo de Escritorio remoto (RDP), infraestructura de escritorio virtual (VDI) y Citrix mediante una clave de seguridad.
- S/MIME con una clave de seguridad.
- *Ejecutar como* con una clave de seguridad.
- Inicio de sesión en un servidor con una clave de seguridad.

## <a name="install-the-azure-ad-kerberos-powershell-module"></a>Instalación del módulo de PowerShell de Kerberos de Azure AD

El [módulo de PowerShell de Kerberos de Azure AD](https://www.powershellgallery.com/packages/AzureADHybridAuthenticationManagement) proporciona características de administración de FIDO2 para administradores.

1. Abra un símbolo del sistema de PowerShell con la opción Ejecutar como administrador.
1. Instale el módulo de PowerShell de Kerberos de Azure AD:

   ```powershell
   # First, ensure TLS 1.2 for PowerShell gallery access.
   [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12

   # Install the Azure AD Kerberos PowerShell Module.
   Install-Module -Name AzureADHybridAuthenticationManagement -AllowClobber
   ```

> [!NOTE]
> - El módulo de PowerShell de Kerberos de Azure AD usa el [módulo AzureADPreview de PowerShell](https://www.powershellgallery.com/packages/AzureADPreview) para proporcionar características avanzadas de administración de Azure Active Directory. Si el [módulo AzureAD de PowerShell](https://www.powershellgallery.com/packages/AzureAD) ya está instalado en el equipo local, la instalación descrita aquí podría generar un error debido a un conflicto. Para evitar conflictos durante la instalación, asegúrese de incluir la marca de opción "-AllowClobber".
> - Puede instalar el módulo de PowerShell de Kerberos de Azure AD en cualquier equipo desde el que pueda acceder al controlador de dominio de Active Directory local, sin dependencia en la solución Azure AD Connect.
> - El módulo de PowerShell de Kerberos de Azure AD se distribuye por medio de la [Galería de PowerShell](https://www.powershellgallery.com/). La Galería de PowerShell es el repositorio central del contenido de PowerShell. En ella puede encontrar módulos útiles de PowerShell que contienen comandos de PowerShell y recursos de Desired State Configuration (DSC).

## <a name="create-a-kerberos-server-object"></a>Creación de un objeto de servidor de Kerberos

Los administradores usan el módulo de PowerShell de Kerberos de Azure AD para crear un objeto de servidor de Kerberos de Azure AD en su directorio local.

Estos pasos deben ejecutarse en todos los dominios y bosques de la organización que contengan usuarios de Azure AD:

1. Abra un símbolo del sistema de PowerShell con la opción Ejecutar como administrador.
1. Ejecute los siguientes comandos de PowerShell para crear un nuevo objeto de servidor de Kerberos de Azure AD en el dominio de Active Directory local y en el inquilino de Azure Active Directory.

   > [!NOTE]
   > Reemplace `contoso.corp.com` del ejemplo siguiente por el nombre de dominio de Active Directory local.

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter an Azure Active Directory global administrator username and password.
   $cloudCred = Get-Credential

   # Enter a domain administrator username and password.
   $domainCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
   ```

   > [!NOTE]
   > Si trabaja en una máquina unida a dominio con una cuenta con privilegios de administrador de dominio, puede omitir el parámetro "-DomainCredential". Si no se proporciona el parámetro "-DomainCredential", se usa la credencial de inicio de sesión de Windows actual para acceder al controlador de dominio de Active Directory local.

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter an Azure Active Directory global administrator username and password.
   $cloudCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   # Use the current windows login credential to access the on-prem AD.
   Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred
   ```

   > [!NOTE]
   > Si la organización protege el inicio de sesión basado en contraseña y aplica métodos de autenticación moderna, como autenticación multifactor, FIDO2 o tecnología de tarjeta inteligente, debe usar el parámetro `-UserPrincipalName` con el nombre principal de usuario (UPN) de un administrador global.
   > - Reemplace `contoso.corp.com` del ejemplo siguiente por el nombre de dominio de Active Directory local.
   > - Reemplace `administrator@contoso.onmicrosoft.com` en el ejemplo siguiente por el UPN de un administrador global.

   ```powershell
   # Specify the on-premises Active Directory domain. A new Azure AD
   # Kerberos Server object will be created in this Active Directory domain.
   $domain = "contoso.corp.com"

   # Enter a UPN of an Azure Active Directory global administrator
   $userPrincipalName = "administrator@contoso.onmicrosoft.com"

   # Enter a domain administrator username and password.
   $domainCred = Get-Credential

   # Create the new Azure AD Kerberos Server object in Active Directory
   # and then publish it to Azure Active Directory.
   # Open an interactive sign-in prompt with given username to access the Azure AD.
   Set-AzureADKerberosServer -Domain $domain -UserPrincipalName $userPrincipalName -DomainCredential $domainCred
   ```

### <a name="view-and-verify-the-azure-ad-kerberos-server"></a>Visualización y comprobación del servidor de Kerberos de Azure AD

Para ver y comprobar el servidor de Kerberos de Azure AD recién creado, use el siguiente comando:

```powershell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Este comando genera las propiedades del servidor Kerberos de Azure AD. Puede revisar las propiedades para comprobar que todo está en orden.

> [!NOTE]
> La ejecución en otro dominio tras suministrar la credencial da lugar a que se conecte por medio de NTLM y a que luego genere un error. Si los usuarios están en el grupo de seguridad Usuarios protegidos de Active Directory, siga estos pasos para resolver el problema: inicie sesión como otro usuario de dominio en **ADConnect** y no proporcione "-domainCredential". Se usa el vale de Kerberos del usuario que ha iniciado la sesión. Para confirmarlo, ejecute `whoami /groups` a fin de validar si el usuario tiene los permisos necesarios en Active Directory para ejecutar el comando anterior.
 
| Propiedad | Descripción |
| --- | --- |
| ID | Identificador único del objeto controlador de dominio de AD DS. A veces a este identificador se le conoce como su *ranura* o su *id. de rama*. |
| DomainDnsName | Nombre de dominio DNS del dominio de Active Directory. |
| ComputerAccount | Objeto de cuenta de equipo del objeto de servidor Kerberos de Azure AD (controlador de dominio). |
| UserAccount | Objeto de cuenta de usuario deshabilitado que contiene la clave de cifrado del TGT del servidor Kerberos de Azure AD. El nombre de dominio de esta cuenta es `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>`. |
| KeyVersion | Versión de la clave de cifrado del TGT del servidor Kerberos de Azure AD. La versión se asigna cuando se crea la clave. Luego se incrementa cada vez que se rota la clave. Los incrementos se basan en los metadatos de replicación y probablemente sean mayores que uno. Por ejemplo, el valor inicial de *KeyVersion* podría ser *192272*. La primera vez que se gira la clave, la versión podría avanzar hasta *212621*. Lo importante es comprobar que el valor de *KeyVersion* del objeto local y el valor de *CloudKeyVersion* del objeto de nube sean los mismos. |
| KeyUpdatedOn | Fecha y hora en que se actualizó o creó la clave de cifrado del TGT del servidor Kerberos de Azure AD. |
| KeyUpdatedFrom | Controlador de dominio en el que se actualizó por última vez la clave de cifrado del TGT del servidor Kerberos de Azure AD. |
| CloudId | Identificador del objeto de Azure AD. Debe coincidir con el identificador de la primera línea de la tabla. |
| CloudDomainDnsName | *DomainDnsName* del objeto de Azure AD. Debe coincidir con el elemento *DomainDnsName* de la segunda línea de la tabla. |
| CloudKeyVersion | *KeyVersion* del objeto de Azure AD. Debe coincidir con el elemento *KeyVersion* de la quinta línea de la tabla. |
| CloudKeyUpdatedOn | *KeyUpdatedOn* del objeto de Azure AD. Debe coincidir con el elemento *KeyUpdatedOn* de la sexta línea de la tabla. |
| | |

### <a name="rotate-the-azure-ad-kerberos-server-key"></a>Rotación de la clave de servidor de Kerberos de Azure AD

Las claves *krbtgt* de cifrado de servidor de Kerberos de Azure AD se deben rotar periódicamente. Se recomienda seguir la misma programación que se emplea para rotar todas las demás claves *krbtgt* del controlador de dominio de Active Directory.

> [!WARNING]
> Aunque hay otras herramientas que podrían rotar las claves *krbtgt*, debe usar las que se mencionan en este documento para rotar las claves *krbtgt* del servidor de Kerberos de Azure AD. Esto garantiza que las claves se actualicen tanto en Active Directory local como en Azure AD.

```powershell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="remove-the-azure-ad-kerberos-server"></a>Eliminación del servidor de Kerberos de Azure AD

Si quiere revertir el escenario y quitar el servidor de Kerberos de Azure AD de Active Directory local y Azure AD, ejecute el siguiente comando:

```powershell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multiforest-and-multidomain-scenarios"></a>Escenarios de varios bosques y varios dominios

El objeto de servidor de Kerberos de Azure AD se representa en Azure AD como un objeto *KerberosDomain*. Cada dominio de Active Directory local se representa como un único objeto *KerberosDomain* en Azure AD.

Por ejemplo, imagine que la organización tiene un bosque de Active Directory con dos dominios: `contoso.com` y `fabrikam.com`. Si opta por permitir que Azure AD emita TGT de Kerberos para todo el bosque, hay dos objetos *KerberosDomain* en Azure AD, un objeto *KerberosDomain* para `contoso.com` y el otro para `fabrikam.com`. Si tiene varios bosques de Active Directory, tendrá un objeto *KerberosDomain* para cada dominio de cada bosque.

Siga las instrucciones de [Creación de un objeto de servidor de Kerberos](#create-a-kerberos-server-object) en cada dominio y bosque de la organización que contiene usuarios de Azure AD.

## <a name="known-behavior"></a>Comportamiento conocido

Si la contraseña ha expirado, se bloquea el inicio de sesión con FIDO. La expectativa es que los usuarios restablezcan sus contraseñas para poder iniciar sesión mediante FIDO.

## <a name="troubleshooting-and-feedback"></a>Solución de problemas y comentarios

Si tiene problemas o quiere compartir comentarios sobre esta característica de inicio de sesión con clave de seguridad sin contraseña, hágalo por medio de la aplicación Centro de opiniones de Windows de este modo:

1. Abra **Centro de opiniones** y asegúrese de que ha iniciado sesión.
1. Para enviar comentarios, seleccione las siguientes categorías:
   - Categoría: Seguridad y privacidad
   - Subcategoría: FIDO
1. Para capturar registros, use la opción **Volver a crear mi problema**.

## <a name="passwordless-security-key-sign-in-faq"></a>Preguntas frecuentes sobre el inicio de sesión con clave de seguridad sin contraseña

Estas son algunas respuestas a preguntas frecuentes sobre el inicio de sesión sin contraseña:

### <a name="does-passwordless-security-key-sign-in-work-in-my-on-premises-environment"></a>¿Funciona el inicio de sesión con clave de seguridad sin contraseña en mi entorno local?

La característica no funciona en un entorno de AD DS local puro.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Mi organización exige autenticación en dos fases para acceder a los recursos. ¿Qué puedo hacer para admitir este requisito?

Las claves de seguridad tienen varios factores de forma. Póngase en contacto con el fabricante del dispositivo para ver cómo se pueden habilitar sus dispositivos con un PIN o reconocimiento biométrico como segundo factor.

### <a name="can-administrators-set-up-security-keys"></a>¿Pueden los administradores configurar claves de seguridad?

Se está trabajando en esta capacidad para la versión de disponibilidad general (GA) de esta característica.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>¿Dónde puedo encontrar claves de seguridad compatibles?

Para obtener información sobre claves de seguridad compatibles, vea [Claves de seguridad FIDO2](concept-authentication-passwordless.md#fido2-security-keys).

### <a name="what-can-i-do-if-i-lose-my-security-key"></a>¿Qué puedo hacer si pierdo mi clave de seguridad?

Para recuperar una clave de seguridad, inicie sesión en Azure Portal y luego vaya a la página **Información de seguridad**.

### <a name="what-can-i-do-if-im-unable-to-use-the-fido-security-key-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>¿Qué puedo hacer si no puedo usar la clave de seguridad FIDO inmediatamente después de crear una máquina unida a Azure AD híbrido?

Si está realizando una instalación limpia de una máquina unida a Azure AD híbrido, después del proceso de unión a dominio y reinicio, debe iniciar sesión con una contraseña y esperar a que se sincronice la directiva para poder usar la clave de seguridad FIDO para iniciar sesión.

- Compruebe el estado actual mediante la ejecución de `dsregcmd /status` en una ventana del símbolo del sistema y asegúrese de que los estados **AzureAdJoined** y **DomainJoined** aparezcan como *SÍ*.
- Este retraso de la sincronización es una limitación conocida de los dispositivos unidos a dominio y no es específico de FIDO.

### <a name="what-if-im-unable-to-get-single-sign-on-to-my-ntlm-network-resource-after-i-sign-in-with-fido-and-get-a-credential-prompt"></a>¿Qué ocurre si no puedo lograr el inicio de sesión único en mi recurso de red de NTLM después de iniciar sesión con FIDO y obtener un símbolo del sistema de credenciales?

Asegúrese de que se hayan revisado suficientes controladores de dominio para responder a tiempo y atender la solicitud de recursos. Para ver si un controlador de dominio ejecuta la característica, ejecute `nltest /dsgetdc:contoso /keylist /kdc` y revise la salida.

> [!NOTE]
> El modificador `/keylist` del comando `nltest` está disponible en el cliente Windows 10, v2004 y posterior.


### <a name="do-fido2-security-keys-work-in-a-windows-login-with-rodc-present-in-the-hybrid-environment"></a>¿Funcionan las claves de seguridad FIDO2 en un inicio de sesión de Windows con RODC presente en el entorno híbrido?

Un inicio de sesión de Windows FIDO2 busca un controlador de dominio grabable para intercambiar el TGT del usuario. Siempre que tenga al menos un controlador de dominio grabable por sitio, el inicio de sesión funciona bien. 

## <a name="next-steps"></a>Pasos siguientes

[Más información sobre la autenticación sin contraseña](concept-authentication-passwordless.md)
