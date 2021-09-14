---
title: 'Uso de Azure AD Multi-Factor Authentication con NPS: Azure Active Directory'
description: Aprenda a usar las capacidades de Azure AD Multi-Factor Authentication con la infraestructura de autenticación de Servidor de directivas de redes (NPS) existente
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/17/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 252eb72358f93b72ca31dd834b704b0044713854
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323120"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-ad-multi-factor-authentication"></a>Integración de la infraestructura existente de Servidor de directivas de redes (NPS) con Azure AD Multi-Factor Authentication

La extensión Servidor de directivas de redes (NPS) para Azure AD Multi-Factor Authentication agrega capacidades de MFA basadas en la nube a la infraestructura de autenticación mediante los servidores existentes. Con la extensión NPS, puede agregar verificación de llamadas de teléfono, mensaje de texto o de aplicaciones de teléfono al flujo de autenticación existente sin tener que instalar, configurar ni mantener servidores nuevos.

La extensión NPS actúa como un adaptador entre RADIUS y Azure AD Multi-Factor Authentication basado en la nube para proporcionar un segundo factor de autenticación a los usuarios federados o sincronizados.

## <a name="how-the-nps-extension-works"></a>Funcionamiento de la extensión NPS

Cuando se usa la extensión NPS para Azure AD Multi-Factor Authentication, el flujo de autenticación incluye los siguientes componentes:

1. **Servidor NAS/VPN** recibe solicitudes de los clientes VPN y las convierte en solicitudes RADIUS para servidores NPS.
2. **Servidor NPS** se conecta a Active Directory Domain Services para realizar la autenticación principal para las solicitudes RADIUS y, cuando se realiza correctamente, pasa la solicitud a todas las extensiones instaladas.  
3. La **extensión NPS** desencadena una solicitud a Azure AD Multi-Factor Authentication para la autenticación secundaria. Una vez que la extensión recibe la respuesta y, si el desafío de MFA se realiza correctamente, se completa la solicitud de autenticación proporcionando al servidor NPS tokens de seguridad que incluyen una notificación de MFA, emitidos por Azure STS.
4. **Azure AD MFA** se comunica con Azure Active Directory (Azure AD) para recuperar los detalles del usuario y realiza la autenticación secundaria con un método de verificación configurado para el usuario.

El diagrama siguiente ilustra este flujo de solicitud de autenticación de alto nivel:

![Diagrama del flujo de autenticación para la autenticación del usuario por medio de un servidor VPN a servidor NPS y la extensión NPS de Azure AD Multi-Factor Authentication](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>Comportamiento del protocolo RADIUS y la extensión NPS

Como RADIUS es un protocolo UDP, el emisor presupone la pérdida de paquetes y espera una respuesta. Después de un período de tiempo, puede agotarse el tiempo de espera de la conexión. Si es así, el paquete se reenvía, ya que el emisor asume que el paquete no llegó al destino. En el escenario de autenticación de este artículo, los servidores VPN envían la solicitud y esperan una respuesta. Si se agota el tiempo de espera de la conexión, el servidor VPN envía la solicitud de nuevo.

![Diagrama del flujo de paquetes UDP de RADIUS y solicitudes después del tiempo de espera en la respuesta del servidor NPS](./media/howto-mfa-nps-extension/radius-flow.png)

Es posible que el servidor NPS no responda a la solicitud original del servidor VPN antes de que se agote el tiempo de espera de la conexión, ya que la solicitud de MFA podría seguir procesándose. Puede que el usuario no haya respondido correctamente a la solicitud de MFA, por lo que la extensión NPS de Azure AD Multi-Factor Authentication está esperando a que se complete ese evento. En esta situación, el servidor NPS identifica las solicitudes adicionales del servidor VPN como una solicitud duplicada. El servidor NPS descarta estas solicitudes duplicadas del servidor VPN.

![Diagrama del servidor NPS que descarta las solicitudes duplicadas del servidor RADIUS](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

Si observa los registros del servidor NPS, puede ver que se descartan estas solicitudes adicionales. Este comportamiento es así por diseño para impedir que el usuario final obtenga varias solicitudes para un único intento de autenticación. Las solicitudes descartadas del registro de eventos del servidor NPS no indican que haya un problema con el servidor NPS o la extensión NPS de Azure AD Multi-Factor Authentication.

Para minimizar las solicitudes descartadas, se recomienda que los servidores VPN estén configurados con un tiempo de espera de al menos 60 segundos. Si es necesario, o para reducir las solicitudes descartadas en los registros de eventos, puede aumentar el valor de tiempo de espera del servidor VPN a 90 o 120 segundos.

Debido a este comportamiento del protocolo UDP, el servidor NPS podría recibir una solicitud duplicada y enviar otro mensaje de MFA, incluso después de que el usuario ya haya respondido a la solicitud inicial. Para evitar esta condición de tiempo, la extensión NPS de Azure AD Multi-Factor Authentication sigue filtrando y descartando las solicitudes duplicadas hasta 10 segundos después de haber enviado una respuesta correcta al servidor VPN.

![Diagrama del servidor NPS que sigue descartando las solicitudes duplicadas del servidor VPN durante diez segundos después de la devolución de una respuesta correcta.](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

Una vez más, es posible que vea solicitudes descartadas en los registros de eventos del servidor NPS, incluso si la solicitud de Azure AD Multi-Factor Authentication se ha completado correctamente. Este es el comportamiento esperado y no indica ningún problema con el servidor NPS o la extensión NPS de Azure AD Multi-Factor Authentication.

## <a name="plan-your-deployment"></a>Planeamiento de la implementación

La extensión de NPS controla automáticamente la redundancia, por lo que no necesita ninguna configuración especial.

Puede crear tantos servidores NPS habilitados para Azure AD Multi-Factor Authentication como necesite. Si instala varios servidores, debe usar un certificado de cliente distinto para cada uno de ellos. La creación de un certificado para cada servidor significa que puede actualizar individualmente cada certificado y no preocuparse por el tiempo de inactividad en los servidores.

Los servidores VPN enrutan las solicitudes de autenticación, por lo que tienen que ser conscientes de los nuevos servidores NPS habilitados para Azure AD Multi-Factor Authentication.

## <a name="prerequisites"></a>Requisitos previos

La extensión de NPS está diseñada para funcionar con la infraestructura existente. Asegúrese de que cumple los siguientes requisitos previos antes de empezar.

### <a name="licenses"></a>Licencias

La extensión NPS para Azure AD Multi-Factor Authentication está disponible para los clientes que tienen [licencias de Azure AD Multi-Factor Authentication](./concept-mfa-howitworks.md) (incluidas con Azure AD Premium P1 y Premium P2 o Enterprise Mobility + Security). Las licencias basadas en el consumo de Azure AD Multi-Factor Authentication, como las licencias por usuario o por autenticación, no son compatibles con la extensión NPS.

### <a name="software"></a>Software

Windows Server 2012 o superior.

### <a name="libraries"></a>Bibliotecas

Debe instalar manualmente la siguiente biblioteca:

- [Visual C++ Redistributable para Visual Studio 2015](https://www.microsoft.com/download/details.aspx?id=48145)

Las siguientes bibliotecas se instalan automáticamente con la extensión.

- [Paquetes redistribuibles de Visual C++ para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Módulo Microsoft Azure Active Directory para Windows PowerShell versión1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

El Módulo Microsoft Azure Active Directory para Windows PowerShell también se instala a través de un script de configuración que se ejecuta como parte del proceso de instalación, si todavía no está presente. No es necesario instalar este módulo con antelación si aún no está instalado.

### <a name="azure-active-directory"></a>Azure Active Directory

Todos los usuarios que utilizan la extensión NPS deben estar sincronizados con Azure AD mediante Azure AD Connect y estar registrados en MFA.

Para instalar la extensión, necesita el *identificador de inquilino* y las credenciales de administrador para el inquilino de Azure AD. Para obtener el identificador de inquilino, complete los pasos siguientes:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global del inquilino de Azure.
1. Busque y seleccione el servicio **Azure Active Directory**.
1. En la página **Información general**, se muestra la *información del inquilino*. Junto al *identificador de inquilino*, seleccione el icono **Copiar**, tal y como se muestra en la siguiente captura de pantalla de ejemplo:

   ![Obtención del identificador de inquilino en Azure Portal](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>Requisitos de red

El servidor NPS debe ser capaz de comunicarse con las siguientes direcciones URL a través de los puertos 80 y 443.

* *https:\//adnotifications.windowsazure.com*
* *https:\//login.microsoftonline.com*
* *https:\//credentials.azure.com*

Además, se requiere conectividad con las direcciones URL siguientes para completar la [instalación del adaptador mediante el script de PowerShell proporcionado](#run-the-powershell-script):

* *https:\//login.microsoftonline.com*
* *https:\//provisioningapi.microsoftonline.com*
* *https:\//aadcdn.msauth.net*
* *https:\//www.powershellgallery.com*
* *https:\//go.microsoft.com*
* *https:\//aadcdn.msftauthimages.net*

## <a name="prepare-your-environment"></a>Preparación del entorno

Antes de instalar la extensión de NPS, prepare el entorno para controlar el tráfico de autenticación.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Habilitar el rol NPS en un servidor unido a dominio

El servidor NPS se conecta a Azure AD y autentica las solicitudes de MFA. Elija un servidor para este rol. Se recomienda elegir un servidor que no controle solicitudes de otros servicios, puesto que la extensión de NPS produce errores para cualquier solicitud que no sea RADIUS. El servidor NPS debe estar configurado como servidor de autenticación principal y secundario para su entorno. No puede redirigir mediante proxy solicitudes RADIUS a otro servidor.

1. En el servidor, abra el **Administrador del servidor**. Seleccione el **Asistente para agregar roles y características** en el menú *Inicio rápido*.
2. Elija **Instalación basada en características o en roles** como tipo de instalación.
3. Seleccione el rol de servidor **Servicios de acceso y directivas de redes**. Puede emerger una ventana para informarle de las características necesarias adicionales para ejecutar este rol.
4. Continúe con los pasos del asistente hasta la página *Confirmación*. Cuando esté listo, seleccione **Instalar**.

La instalación del rol del servidor NPS puede tardar unos minutos. Cuando termine, continúe con las siguientes secciones para configurar este servidor para que administre las solicitudes RADIUS entrantes desde la solución VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configurar la solución VPN para comunicarse con el servidor NPS

Según la solución VPN que use, así varían los pasos para configurar la directiva de autenticación RADIUS. Configure la directiva de VPN de modo que apunte al servidor NPS RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Sincronizar los usuarios del dominio en la nube

Puede que este paso ya se haya completado en el inquilino, pero es conveniente comprobar que Azure AD Connect ha sincronizado las bases de datos recientemente.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Seleccione **Azure Active Directory** > **Azure AD Connect**.
3. Compruebe que el estado de sincronización es **Habilitado** y que la última sincronización fue hace menos de una hora.

Si necesita iniciar una nueva ronda de sincronización, consulte [Sincronización de Azure AD Connect: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Determinación de los métodos de autenticación que pueden utilizar los usuarios

Existen dos factores que afectan a la disponibilidad de los métodos de autenticación con una implementación de extensión NPS:

* El algoritmo de cifrado de contraseña utilizado entre el cliente RADIUS (VPN, servidor Netscaler u otros) y los servidores NPS.
   - **PAP** es compatible con todos los métodos de autenticación de Azure AD Multi-Factor Authentication en la nube: llamada de teléfono, mensaje de texto unidireccional, notificación de aplicación móvil, token de hardware de OATH y código de verificación de la aplicación móvil.
   - **CHAPV2** y **EAP** admiten llamadas de teléfono y notificaciones de aplicación móvil.

    > [!NOTE]
    > Al implementar la extensión NPS, use estos factores para estudiar qué métodos están disponibles para los usuarios. Si el cliente RADIUS es compatible con PAP, pero el cliente UX no tiene campos de entrada para un código de verificación, las dos opciones compatibles son la llamada de teléfono y notificación de aplicación móvil.
    >
    > Además, independientemente del protocolo de autenticación que se use (PAP, CHAP o EAP), si el método de MFA está basado en texto (SMS, código de verificación de la aplicación móvil o token de hardware OATH) y es necesario que el usuario escriba un código o texto en el campo de entrada de la interfaz de usuario del cliente de VPN, es posible que la autenticación se realice correctamente. *No obstante*, los atributos RADIUS configurados en la directiva de acceso a la red *no* se reenvían al cliente RADIUS (el dispositivo de acceso a la red, como VPN Gateway). Como resultado, el cliente de VPN puede tener más acceso del deseado, menos o ninguno.

* Los métodos de entrada que la aplicación cliente (VPN, servidor Netscaler u otros) puede controlar. Por ejemplo, ¿tiene el cliente de VPN algún medio para que el usuario escriba un código de comprobación de aplicación móvil o de texto?

Puede [deshabilitar los métodos de autenticación no compatibles](howto-mfa-mfasettings.md#verification-methods) en Azure.

### <a name="register-users-for-mfa"></a>Registro de usuarios en MFA

Antes de implementar y usar la extensión NPS, es necesario que los usuarios a los que se les pide Azure AD Multi-Factor Authentication estén registrados en MFA. Para probar la extensión mientras se implementa, también necesita al menos una cuenta de prueba totalmente registrada en Azure AD Multi-Factor Authentication.

Si tiene que crear y configurar una cuenta de prueba, siga estos pasos:

1. Inicie sesión en [https://aka.ms/mfasetup](https://aka.ms/mfasetup) con una cuenta de prueba.
2. Siga las indicaciones para configurar un método de verificación.
3. En Azure Portal, como usuario administrador, [cree una directiva de acceso condicional](howto-mfa-getstarted.md#plan-conditional-access-policies) para requerir la autenticación multifactor para la cuenta de pruebas.

> [!IMPORTANT]
>
> Asegúrese de que los usuarios se hayan registrado correctamente en Azure AD Multi-Factor Authentication. Si los usuarios solo se han registrado previamente para el autoservicio de restablecimiento de contraseña (SSPR), *StrongAuthenticationMethods* está habilitado para su cuenta. Azure AD Multi-Factor Authentication se aplica cuando se configura *StrongAuthenticationMethods*, aunque el usuario solo esté registrado en SSPR.
>
> Se puede habilitar el registro de seguridad combinado que configura SSPR y Azure AD Multi-Factor Authentication al mismo tiempo. Para obtener más información, consulte [Habilitación del registro de información de seguridad combinado en Azure Active Directory](howto-registration-mfa-sspr-combined.md).
>
> También puede [obligar a los usuarios a volver a registrar los métodos de autenticación](howto-mfa-userdevicesettings.md#manage-user-authentication-options) si previamente solo habilitaron SSPR.

## <a name="install-the-nps-extension"></a>Instalación de la extensión de NPS

> [!IMPORTANT]
> Instale la extensión NPS en un servidor diferente al punto de acceso VPN.

### <a name="download-and-install-the-nps-extension-for-azure-ad-mfa"></a>Descarga e instalación de la extensión NPS para Azure AD MFA

Para descargar e instalar la extensión NPS, realice los pasos siguientes:

1. [Descargue la extensión de NPS](https://aka.ms/npsmfa) desde el Centro de descarga de Microsoft.
1. Copie el archivo binario en el Servidor de directivas de redes que quiere configurar.
1. Ejecute *setup.exe* y siga las instrucciones de instalación. Si se producen errores, asegúrese de que las [bibliotecas de la sección de requisitos previos](#libraries) se han instalado correctamente.

#### <a name="upgrade-the-nps-extension"></a>Actualización de la extensión NPS

Si luego actualiza una instalación de una extensión NPS existente, complete los pasos siguientes para evitar un reinicio del servidor subyacente:

1. Desinstale la versión existente.
1. Ejecute el nuevo instalador.
1. Reinicie el servicio del *Servidor de directivas de redes (IAS)* .

### <a name="run-the-powershell-script"></a>Ejecución del script de PowerShell

El instalador crea un script de PowerShell en `C:\Program Files\Microsoft\AzureMfa\Config` (donde `C:\` es la unidad de instalación). Este script de PowerShell realiza las siguientes acciones cada vez que se ejecuta:

* Crea un certificado autofirmado.
* Asocia la clave pública del certificado a la entidad de servicio en Azure AD.
* Almacena el certificado en el almacén de certificados del equipo local.
* Concede acceso a la clave privada del certificado para el usuario de red.
* Reinicia el servicio NPS.

A menos que desee utilizar sus propios certificados (en lugar de los certificados autofirmados que genera el script de PowerShell), ejecute el script de PowerShell para completar la instalación de la extensión NPS. Si instala la extensión en varios servidores, cada uno debe tener su propio certificado.

Para proporcionar capacidades de equilibrio de carga o redundancia, repita estos pasos en servidores NPS adicionales según desee:

1. Abra una ventana de Windows PowerShell como administrador.
1. Cambie los directorios a la ubicación en la que el instalador creó el script de PowerShell:

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. Ejecute el script de PowerShell creado por el instalador.

   Es posible que tenga que habilitar primero TLS 1.2 para que PowerShell pueda conectarse y descargar paquetes correctamente:
   
   `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`

   > [!IMPORTANT]
   > En el caso de los clientes que usan Azure Government o las nubes de Azure China 21Vianet, edite primero los cmdlets `Connect-MsolService` en el script *AzureMfaNpsExtnConfigSetup. PS1* para incluir los parámetros *AzureEnvironment* para la nube requerida. Por ejemplo, especifique *-AzureEnvironment USGovernment* o *-AzureEnvironment AzureChinaCloud*.
   >
   > Para más información, consulte la [referencia de parámetros de Connect-MsolService](/powershell/module/msonline/connect-msolservice#parameters).

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. Cuando se le solicite, inicie sesión como administrador en Azure AD.
1. PowerShell le pide el identificador de inquilino. Use el GUID del *identificador de inquilino* que ha copiado desde Azure Portal en la sección de requisitos previos.
1. Cuando finaliza el script, se muestra un mensaje de operación correcta.  

Si ha expirado el certificado de equipo anterior y se ha generado un nuevo certificado, debe eliminar todos los certificados expirados. Tener los certificados expirados puede provocar incidencias con el inicio de la extensión NPS.

> [!NOTE]
> Si utiliza certificados propios en lugar de generar certificados con el script de PowerShell, asegúrese de que se ajustan a la convención de nomenclatura de NPS. El nombre del firmante debe ser **CN=\<TenantID\>,OU=Microsoft NPS Extension**.

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Pasos adicionales de Microsoft Azure Government o de Azure China 21Vianet

En el caso de los clientes que usan las nubes de Azure Government o Azure China 21Vianet, se necesitan los siguientes pasos de configuración adicionales en cada servidor NPS.

> [!IMPORTANT]
> Configure estos valores del Registro solo si es cliente de Azure Government o de Azure China 21Vianet.

1. Si es cliente de Azure Government o de Azure China 21Vianet, abra el **Editor del Registro** en el servidor NPS.
1. Vaya a `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`.
1. Para clientes de Azure Government, establezca los siguientes valores de clave:

    | Clave del Registro       | Value |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. En el caso de los clientes de Azure China 21Vianet, establezca los siguientes valores de clave:

    | Clave del Registro       | Value |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. Repita los dos pasos anteriores para establecer los valores de clave del Registro para cada servidor NPS.
1. Reinicie el servicio NPS para cada servidor NPS.

    Para reducir el impacto, retire cada servidor NPS de la rotación de NLB de uno en uno y espere a que se purguen todas las conexiones.

### <a name="certificate-rollover"></a>Sustitución del certificado

Con la versión *1.0.1.32* de la extensión NPS se admite la lectura de varios certificados. Esta funcionalidad le ayuda a facilitar las actualizaciones graduales de certificados antes de su expiración. Si en la organización se ejecuta una versión anterior de la extensión NPS, actualice a la versión *1.0.1.32* o superior.

Los certificados creados por el script `AzureMfaNpsExtnConfigSetup.ps1` son válidos durante 2 años. Supervise los certificados para que no expiren. Los certificados para la extensión NPS se encuentran en el almacén de certificados del *equipo local*, en la carpeta *Personal* y *se emiten* para el identificador de inquilino proporcionado al script de instalación.

Cuando un certificado se aproxima a la fecha de expiración, se debe crear un nuevo certificado para reemplazarlo.  Este proceso se realiza de nuevo mediante la ejecución del script `AzureMfaNpsExtnConfigSetup.ps1` y se debe mantener el mismo identificador de inquilino cuando se le solicite. Este proceso se debe repetir en cada servidor de NPS del entorno.

## <a name="configure-your-nps-extension"></a>Configuración de la extensión de NPS

Con el entorno preparado y la extensión NPS ahora instalada en los servidores necesarios, puede configurar la extensión.

En esta sección se incluyen consideraciones de diseño y sugerencias para las implementaciones correctas de la extensión de NPS.

### <a name="configuration-limitations"></a>Limitaciones de configuración

- La extensión NPS para Azure AD Multi-Factor Authentication no incluye herramientas para migrar usuarios y configuraciones desde el servidor MFA a la nube. Por este motivo se recomienda usar la extensión para las nuevas implementaciones en lugar de la existente. Si usa la extensión en una implementación existente, los usuarios tendrán que autenticarse de nuevo para rellenar los detalles de MFA en la nube.  
- La extensión NPS usa el UPN del entorno de AD DS local para identificar al usuario en Azure AD Multi-Factor Authentication a fin de realizar la autenticación secundaria. La extensión se puede configurar para que utilice un identificador diferente como identificador de inicio de sesión alternativo o un campo personalizado de AD DS que no sea UPN. Consulte el artículo de [opciones de configuración avanzada para la extensión NPS para Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md) para más información.
- No todos los protocolos de cifrado son compatibles con todos los métodos de comprobación.
   - **PAP** admite llamadas de teléfono, mensajes de texto unidireccionales, notificaciones de aplicación móvil y códigos de comprobación de aplicación móvil
   - **CHAPV2** y **EAP** admiten llamadas de teléfono y notificaciones de aplicación móvil.

### <a name="control-radius-clients-that-require-mfa"></a>Control de clientes RADIUS que requieren MFA

Una vez que habilite MFA para un cliente RADIUS mediante la extensión de NPS, son necesarias todas las autenticaciones de este cliente para ejecutar MFA. Si desea habilitar MFA para algunos clientes RADIUS pero no para otros, puede configurar dos servidores NPS e instalar la extensión solo en uno de ellos.

Configure los clientes RADIUS para los que desea que se exija MFA para enviar solicitudes en el servidor NPS configurado con la extensión y otros clientes RADIUS en el servidor NPS no configurado con la extensión.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Preparación para los usuarios que no están inscritos en MFA

Si tiene usuarios que no están inscritos en MFA, puede determinar lo que sucederá cuando intenten realizar la autenticación. Para controlar este comportamiento, use el ajuste *REQUIRE_USER_MATCH* en la ruta de acceso del registro *HKLM\Software\Microsoft\AzureMFA*. Esta opción tiene una única opción de configuración:

| Clave | Value | Valor predeterminado |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | No establecido (equivalente a TRUE) |

Este ajuste determina qué hacer cuando un usuario no está inscrito para MFA. Cuando la clave no existe, no está establecida o está establecida en *TRUE*, y el usuario no está inscrito, se produce un error en la extensión al completar el desafío de MFA.

Cuando la clave está establecida en *FALSE* y el usuario no está inscrito, la autenticación continúa sin realizar el desafío de MFA. Si un usuario está inscrito en MFA, debe autenticarse con MFA aunque *REQUIRE_USER_MATCH* esté establecido en *FALSE*.

Puede crear esta clave y establecerla en *FALSE* mientras los usuarios se incorporan, y puede que no todos estén inscritos en Azure AD Multi-Factor Authentication aún. No obstante, puesto que el establecimiento de la clave permite a los usuarios que no estén inscritos en MFA iniciar sesión, debe quitar esta clave antes de pasar a producción.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="nps-extension-health-check-script"></a>Script de comprobación del estado de la extensión NPS

El siguiente script está disponible para realizar los pasos básicos de comprobación del estado al momento de solucionar problemas de la extensión de NPS.

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>¿Cómo se puede comprobar que el certificado de cliente está instalado según lo previsto?

Busque el certificado autofirmado creado por el instalador en el almacén de certificados y compruebe que la clave privada tenga permisos concedidos al usuario *NETWORK SERVICE*. El certificado tiene el nombre de firmante de **CN \<tenantid\>, OU = Microsoft NPS Extension**.

Los certificados autofirmados generados por el script `AzureMfaNpsExtnConfigSetup.ps1` tienen una duración de validez de dos años. Al comprobar que el certificado está instalado, también debe comprobar que el certificado no ha expirado.

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>¿Cómo se puede comprobar que el certificado de cliente está asociado a mi inquilino en Azure AD?

Abra el símbolo del sistema de PowerShell y ejecute el comando siguiente:

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Estos comandos imprimen todos los certificados que asocian a su inquilino con su instancia de la extensión de NPS en la sesión de PowerShell. Busque el certificado al exportar el certificado de cliente como un archivo *X.509 codificado base 64 (.cer)* sin la clave privada y compárelo con la lista de PowerShell.

El comando siguiente creará un archivo denominado *npscertificate* en la raíz de la unidad *C:* con el formato *.cer*.

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Una vez que ejecute este comando, vaya a la raíz de la unidad *C:* , busque el archivo y haga doble clic en él. Vaya a los detalles y desplácese hacia abajo hasta llegar a la "huella digital" y compárela con la huella digital del certificado instalado en el servidor. Las huellas digitales del certificado deben coincidir.

Las marcas de tiempo *válido-desde* y *válido-hasta*, que se encuentran en un formato legible, se pueden utilizar para filtrar desajustes evidentes si el comando devuelve más de un certificado.

### <a name="why-cannot-i-sign-in"></a>¿Por qué no puedo iniciar sesión?

Compruebe que la contraseña no ha expirado. La extensión NPS no admite el cambio de contraseñas como parte del flujo de trabajo del inicio de sesión. Póngase en contacto con el personal de TI de la organización para obtener más ayuda.

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>¿Por qué las solicitudes provocan un error de token de ADAL?

Este error puede deberse a varias razones. Use los pasos siguientes para solucionar el problema:

1. Reinicie el servidor NPS.
2. Compruebe que el certificado de cliente esté instalado según lo previsto.
3. Compruebe que el certificado esté asociado a su inquilino en Azure AD.
4. Compruebe que `https://login.microsoftonline.com/` es accesible desde el servidor que ejecuta la extensión.

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>¿Por qué produce un error de autenticación en los registros HTTP que indica que no se encuentra el usuario?

Compruebe que se está ejecutando AD Connect y que el usuario está presente en el entorno de AD DS local y en Azure AD.

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>¿Por qué aparecen errores de conexión de HTTP en los registros con errores en todas las autenticaciones?

Compruebe que https://adnotifications.windowsazure.com es accesible desde el servidor que ejecuta la extensión NPS.

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>¿Por qué la autenticación no funciona a pesar de tener un certificado válido?

Si ha expirado el certificado de equipo anterior y se ha generado un nuevo certificado, elimine todos los certificados expirados. Los certificados expirados pueden provocar incidencias con el inicio de la extensión NPS.

Para comprobar si tiene un certificado válido, compruebe el *almacén de certificados de la cuenta del equipo local* mediante el uso de MMC y asegúrese de que el certificado no ha superado la fecha de expiración. Para generar un nuevo certificado válido, vuelva a ejecutar los pasos descritos en [Ejecución del script del instalador de PowerShell](#run-the-powershell-script).

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>¿Por qué veo solicitudes descartadas en los registros del servidor NPS?

Un servidor VPN puede enviar solicitudes repetidas al servidor NPS si el valor de tiempo de espera es demasiado bajo. El servidor NPS detecta estas solicitudes duplicadas y las descarta. Este comportamiento es así por diseño y no indica ningún problema con el servidor NPS ni la extensión NPS de Azure AD Multi-Factor Authentication.

Para obtener más información sobre por qué se ven los paquetes descartados en los registros del servidor NPS, consulte [Comportamiento del protocolo RADIUS y la extensión NPS](#radius-protocol-behavior-and-the-nps-extension) al principio de este artículo.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Administración de los protocolos TLS/SSL y conjuntos de cifrado

Se recomienda deshabilitar o quitar los conjuntos de cifrado más antiguos o débiles a menos que los exija la organización. Se puede encontrar información sobre cómo completar esta tarea en el artículo sobre [Administración de conjuntos de cifrado y protocolos SSL/TLS de AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs).

### <a name="additional-troubleshooting"></a>Solución de problemas adicional

Puede encontrar una guía de solución de problemas adicional y posibles soluciones en el artículo [Resolución de mensajes de error de la extensión de NPS para Azure AD Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Pasos siguientes

- [Información general y configuración del servidor de directivas de redes en Windows Server](/windows-server/networking/technologies/nps/nps-top)

- Configurar los identificadores alternativos de inicio de sesión o una lista de excepciones para las direcciones IP que no deben realizar la comprobación de dos pasos en [Opciones de configuración avanzada para la extensión NPS para Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md)

- Obtenga información sobre cómo integrar [Puerta de enlace de Escritorio remoto](howto-mfa-nps-extension-rdg.md) y los [servidores VPN](howto-mfa-nps-extension-vpn.md) con la extensión NPS

- [Resolución de mensajes de error de la extensión de NPS para Azure AD Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
