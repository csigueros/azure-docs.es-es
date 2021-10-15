---
title: Planeamiento de una implementación de autenticación sin contraseña en Azure Active Directory
description: Instrucciones para implementar la autenticación sin contraseña
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/28/2021
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1c8f1117053e6076d2281036ecc63f1cbf97d15
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352745"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Planeamiento de una implementación de autenticación sin contraseña en Azure Active Directory

Las contraseñas son un vector de ataque principal. Los actores con malas intenciones usan ataques de ingeniería social, suplantación de identidad (phishing) y ataques de difusión de contraseñas para ponerlas en peligro. Una estrategia de autenticación sin contraseña mitiga el riesgo de estos ataques.

Microsoft ofrece las [tres opciones siguientes de autenticación sin contraseña](concept-authentication-passwordless.md) que se integran con Azure Active Directory (Azure AD):

* [Aplicación Microsoft Authenticator](./concept-authentication-passwordless.md#microsoft-authenticator-app): convierte cualquier teléfono iOS o Android en una credencial segura sin contraseña, ya que permite a los usuarios iniciar sesión en cualquier plataforma o explorador.

* [Claves de seguridad compatibles con FIDO2](./concept-authentication-passwordless.md#fido2-security-keys): son útiles para los usuarios que inician sesión en máquinas compartidas, como quioscos, en situaciones en las que se restringe el uso de teléfonos, y para identidades con privilegios elevados. 

* [Windows Hello para empresas](./concept-authentication-passwordless.md#windows-hello-for-business): está más indicado para los usuarios que trabajan en sus equipos Windows dedicados. 

> [!NOTE]
> Para crear una versión sin conexión de este plan con todos los vínculos, utilice la funcionalidad de impresión en PDF del explorador.

## <a name="use-the-passwordless-methods-wizard"></a>Uso del asistente para métodos sin contraseña

[Azure Portal](https://portal.azure.com/) tiene ahora un asistente para los métodos sin contraseña que le ayudará a seleccionar el método adecuado para cada una de las audiencias. Si aún no ha determinado los métodos adecuados, vea [https://aka.ms/passwordlesswizard](https://aka.ms/passwordlesswizard), y vuelva a este artículo para seguir planeando los métodos seleccionados. **Necesita derechos de administrador para acceder a este asistente.**

## <a name="passwordless-authentication-scenarios"></a>Escenarios de autenticación sin contraseña

Los métodos de autenticación sin contraseña de Microsoft permiten muchos escenarios. Tenga en cuenta las necesidades de su organización, los requisitos previos y las funcionalidades de cada método de autenticación para seleccionar su estrategia de autenticación sin contraseña. 

En la tabla siguiente se enumeran los métodos de autenticación sin contraseña por tipo de dispositivo. Nuestras recomendaciones se indican en **negrita**.

| Tipos de dispositivo| Método de autenticación sin contraseña |
| - | - |
| Dispositivos dedicados que no son Windows| <li> **Aplicación Microsoft Authenticator** <li> Llaves de seguridad |
| Equipos Windows 10 dedicados (versión 1703 y posteriores)| <li> **Windows Hello para empresas** <li> Llaves de seguridad |
| Equipos Windows 10 dedicados (anteriores a la versión 1703)| <li> **Windows Hello para empresas** <li> Aplicación Microsoft Authenticator |
| Dispositivos compartidos: tabletas y dispositivos móviles| <li> **Aplicación Microsoft Authenticator** <li> Inicio de sesión con contraseña de un solo uso |
| Quioscos (heredado)| **Aplicación Microsoft Authenticator** |
| Quioscos y equipos compartidos ‎(Windows 10)| <li> **Llaves de seguridad** <li> Aplicación Microsoft Authenticator |


## <a name="prerequisites"></a>Requisitos previos 

Asegúrese de cumplir los requisitos previos antes de iniciar la implementación sin contraseña.

### <a name="required-roles"></a>Roles necesarios

Estos son los roles con privilegios mínimos necesarios para esta implementación:
<p>

| Rol de Azure AD| Descripción |
| - | -|
| Administrador global| Para implementar la experiencia de registro combinado. |
| Administrador de autenticación| Para implementar y administrar métodos de autenticación. |
| Usuario| Para configurar la aplicación Authenticator en el dispositivo, o para inscribir el dispositivo de clave de seguridad para el inicio de sesión web o de Windows 10. |

Como parte de este plan de implementación, se recomienda habilitar la autenticación sin contraseña para todas las [cuentas con privilegios](../privileged-identity-management/pim-configure.md).

### <a name="microsoft-authenticator-app-and-security-keys"></a>Aplicación Microsoft Authenticator y claves de seguridad

Los requisitos previos se determinan por los métodos de autenticación sin contraseña seleccionados.

| Requisito previo| Aplicación Microsoft Authenticator| Llaves de seguridad FIDO2|
| - | -|-|
| Se ha habilitado el [registro combinado en Multi-Factor Authentication (MFA) de Azure AD y el autoservicio de restablecimiento de contraseña (SSPR)](howto-registration-mfa-sspr-combined.md).| √| √|
| [Los usuarios pueden realizar Azure AD MFA](howto-mfa-getstarted.md)| √| √|
| [Los usuarios se han registrado en Azure AD MFA y SSPR](howto-registration-mfa-sspr-combined.md)| √| √|
| [Los usuarios han registrado sus dispositivos móviles en Azure Active Directory](../devices/overview.md)| √| |
| Windows 10 versión 1809 o superior con un explorador admitido, como Microsoft Edge o Mozilla Firefox (versión 67 o superior). Microsoft recomienda la versión 1903 o posteriores para tener compatibilidad nativa.| | √|
| Claves de seguridad compatibles Asegúrese de que usa una [clave de seguridad FIDO2 verificada y probada por Microsoft](concept-authentication-passwordless.md), o cualquier otra clave de seguridad FIDO2 compatible.| | √|


### <a name="windows-hello-for-business"></a>Windows Hello para empresas

Los requisitos previos y las rutas de implementación de Windows Hello para empresas dependen en gran medida de si la implementación se realiza en una configuración local, híbrida o solo en la nube. También depende de la estrategia de conexión del dispositivo. 

Seleccione Windows Hello para empresas y [complete el asistente](https://aka.ms/passwordlesswizard) para determinar los requisitos previos y la implementación adecuados para su organización.

![Selección de Windows Hello para empresas en el asistente](media/howto-authentication-passwordless-deployment/passwordless-wizard-select.png)


El asistente usará las entradas para confeccionar un plan paso a paso que puede seguir.

## <a name="plan-the-project"></a>Planeamiento del proyecto

Cuando fracasan los proyectos tecnológicos, normalmente se debe a expectativas incorrectas relacionadas con el impacto, los resultados y las responsabilidades. Para evitar estos problemas, [asegúrese de que involucra a las partes interesadas adecuadas](../fundamentals/active-directory-deployment-plans.md) y que estas conocen bien sus roles.

### <a name="plan-a-pilot"></a>Planeamiento de un piloto

Al implementar la autenticación sin contraseña, debe habilitar primero uno o varios grupos piloto. Puede crear grupos específicamente para este propósito. Agregue a los grupos los usuarios que van a participar en la prueba piloto. A continuación, habilite los nuevos métodos de autenticación sin contraseña para los grupos seleccionados. Consulte [Procedimientos recomendados para un piloto](../fundamentals/active-directory-deployment-plans.md).

### <a name="plan-communications"></a>Planeamiento de las comunicaciones

Las comunicaciones con los usuarios finales deben incluir la información siguiente:

* [Instrucciones sobre el registro combinado para Azure AD MFA y SSPR](howto-registration-mfa-sspr-combined.md)

* [Descarga de la aplicación Microsoft Authenticator](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a)

* [Registro en la aplicación Microsoft Authenticator](howto-authentication-passwordless-phone.md)

* [Inicio de sesión con el teléfono](https://support.microsoft.com/account-billing/sign-in-to-your-accounts-using-the-microsoft-authenticator-app-582bdc07-4566-4c97-a7aa-56058122714c)

Microsoft proporciona plantillas de comunicación para los usuarios finales. Descargue el [material de lanzamiento de autenticación](https://aka.ms/MFAtemplates) para ayudar a esbozar las comunicaciones. Los materiales de lanzamiento incluyen pósteres personalizables y plantillas de correo electrónico que puede usar para informar a los usuarios sobre las próximas opciones de autenticación sin contraseña de su organización.

## <a name="plan-user-registration"></a>Planeamiento del registro del usuario

Los usuarios registran su método sin contraseña como parte del **flujo de trabajo de información de seguridad combinado** en [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Azure AD registra el registro de claves de seguridad y la aplicación Microsoft Authenticator, y cualquier otro cambio en los métodos de autenticación. 

En el caso de que sea la primera vez para un usuario que no tiene una contraseña, los administradores pueden proporcionar un [código de pase de acceso temporal](howto-authentication-temporary-access-pass.md) para registrar su información de seguridad en [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo.md). Se trata de un código de acceso con un tiempo limitado y satisface los requisitos de autenticación sólida. **El Pase de acceso temporal es un proceso que se realiza por usuario**.

Este método también se puede usar para facilitar la recuperación cuando el usuario ha perdido u olvidado su factor de autenticación, como una llave de seguridad o la aplicación Microsoft Authenticator, pero debe iniciar sesión para **registrar un nuevo método de autenticación sólida**. 

>[!NOTE] 
> Si no puede usar la clave de seguridad o la aplicación Microsoft Authenticator en algunos escenarios, se puede usar la autenticación multifactor con un nombre de usuario y una contraseña, junto con otro método registrado como opción de reserva.

## <a name="plan-for-and-deploy-the-microsoft-authenticator-app"></a>Planeación e implementación de la aplicación Microsoft Authenticator

La [aplicación Microsoft Authenticator](concept-authentication-passwordless.md) convierte cualquier teléfono Android o iOS en una credencial segura sin contraseña. Se puede descargar gratis de Google Play o Apple App Store. Haga que los usuarios [descarguen la aplicación Microsoft Authenticator](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a) y que sigan las instrucciones para habilitar el inicio de sesión en el teléfono.

### <a name="technical-considerations"></a>Consideraciones técnicas

**Servicios de federación de Active Directory (AD FS)** : cuando un usuario habilita la credencial sin contraseña de Microsoft Authenticator, la autenticación predeterminada para ese usuario siempre es enviar una notificación para su aprobación. Se impide que los usuarios de un inquilino híbrido se dirijan a AD FS para iniciar sesión, a menos que seleccionen "Use su contraseña en su lugar". Este proceso también omite las directivas de acceso condicional locales y los flujos de autenticación transferida (PTA). Sin embargo, si se especifica login_hint, el usuario se reenvía a AD FS y se omite la opción de usar la credencial sin contraseña.

**Servidor Azure MFA**: los usuarios finales habilitados para la autenticación multifactor a través de un servidor Azure MFA local de la organización pueden crear y usar una credencial de inicio de sesión único telefónico sin contraseña. Si el usuario intenta actualizar varias instalaciones (5 o más) de Microsoft Authenticator con la credencial, este cambio puede dar lugar a un error.

> [!IMPORTANT]
> A partir del 1 de julio de 2019, Microsoft ya no ofrecerá el servidor MFA para implementaciones nuevas. Los clientes nuevos que quieran exigir la autenticación multifactor (MFA) durante los eventos de inicio de sesión deben usar Azure AD Multi-Factor Authentication basado en la nube. Los clientes existentes que hayan activado el Servidor MFA antes del 1 de julio de 2019 podrán descargar la versión más reciente y las actualizaciones futuras, así como generar credenciales de activación como de costumbre. Se recomienda pasar del servidor Azure MFA a Azure Active Directory MFA.

**Registro de dispositivos**: para usar la aplicación Authenticator para la autenticación sin contraseña, el dispositivo debe estar registrado en el inquilino de Azure AD y no puede ser un dispositivo compartido. Un dispositivo solo se puede registrar en un único inquilino. Este límite significa que solo se admite una cuenta profesional o educativa para el inicio de sesión en el teléfono con la aplicación Microsoft Authenticator.

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Implementación del inicio de sesión en el teléfono con la aplicación Microsoft Authenticator

Siga los pasos del artículo [Habilitación del inicio de sesión sin contraseña con la aplicación Microsoft Authenticator](howto-authentication-passwordless-phone.md) para habilitar la aplicación de Microsoft Authenticator como método de autenticación sin contraseña en la organización.

### <a name="testing-microsoft-authenticator-app"></a>Prueba de la aplicación Microsoft Authenticator

Los siguientes son casos de prueba de ejemplo de autenticación sin contraseña con la aplicación Microsoft Authenticator:

| Escenario| Resultados esperados |
| - |-|
| El usuario puede registrar la aplicación Microsoft Authenticator| El usuario puede registrar la aplicación desde https://aka.ms/mysecurityinfo. |
| El usuario puede habilitar el inicio de sesión en el teléfono| El inicio de sesión en el teléfono está configurado para una cuenta profesional. |
| El usuario puede acceder a una aplicación con el inicio de sesión en el teléfono| El usuario pasa por el flujo de inicio de sesión en el teléfono y llega a la aplicación. |
| Prueba de la reversión del registro de inicio de sesión en el teléfono desactivando el inicio de sesión sin contraseña de Microsoft Authenticator. Haga esto en la pantalla Métodos de autenticación del portal de Azure AD.| Los usuarios habilitados anteriormente no pueden usar el inicio de sesión sin contraseña desde Microsoft Authenticator. |
| Quitar el inicio de sesión con teléfono de la aplicación Microsoft Authenticator| La cuenta profesional ya no está disponible en Microsoft Authenticator |


### <a name="troubleshoot-phone-sign-in"></a>Solución de problemas de inicio de sesión en el teléfono


| Escenario| Solución |
| - |-|
| El usuario no puede realizar el registro combinado.| Asegúrese de que el [registro combinado](concept-registration-mfa-sspr-combined.md) está habilitado. |
| El usuario no puede habilitar el inicio de sesión en el teléfono en la aplicación Authenticator.| Asegúrese de que el usuario está en el ámbito de la implementación. |
| El usuario NO está en el ámbito de la autenticación sin contraseña, pero se le ofrece la opción de inicio de sesión sin contraseña, que no puede completar.| Se produce cuando el usuario ha habilitado el inicio de sesión de la aplicación en el teléfono antes de que se haya creado la directiva. Para habilitar el inicio de sesión, agregue el usuario al grupo de usuarios habilitados para el inicio de sesión sin contraseña. Para bloquear el inicio de sesión: pida al usuario que quite sus credenciales de la aplicación. |


## <a name="plan-for-and-deploy-fido2-compliant-security-keys"></a>Planeación e implementación de claves de seguridad compatibles con FIDO2

Habilite claves de seguridad compatibles. Esta es una lista de [proveedores de claves de seguridad FIDO2](concept-authentication-passwordless.md) que proporcionan claves que se sabe que son compatibles con la experiencia sin contraseña.

### <a name="plan-security-key-lifecycle"></a>Planeación del ciclo de vida de las claves de seguridad

Prepare y planee el ciclo de vida de las claves.

**Distribución de las claves**: planee cómo aprovisionará las claves a su organización. Puede tener un proceso de aprovisionamiento centralizado o permitir que los usuarios finales compren llaves compatibles con FIDO 2.0.

 **Activación de las claves**: los usuarios finales deben activar la clave de seguridad. Los usuarios finales registran sus llaves de seguridad en [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) y habilitan el segundo factor (PIN o biométrico) al usarlas por primera vez. En el caso de que sea la primera vez, los usuarios pueden usar TAP para registrar su información de seguridad.

 **Deshabilitación de una clave**: si un administrador quiere quitar una clave FIDO2 asociada a una cuenta de usuario, puede hacerlo mediante la eliminación de la clave del método de autenticación del usuario, como se muestra a continuación. Para más información, vea [Deshabilitación de una clave](howto-authentication-passwordless-security-key.md#disable-a-key).

 

**Emisión de una nueva clave**: el usuario puede registrar la nueva clave FIDO2 yendo a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). 

### <a name="technical-considerations"></a>Consideraciones técnicas

Hay tres tipos de implementaciones de inicio de sesión sin contraseña disponibles con las claves de seguridad:

* Aplicaciones web de Azure AD en un explorador compatible

* Dispositivos Windows 10 unidos a Azure AD

* Dispositivos Windows 10 unidos a Azure AD híbrido 

  * Proporciona acceso a los recursos locales y a los basados en la nube. Para más información sobre el acceso a los recursos locales, consulte [Inicio de sesión único en los recursos locales mediante claves de FIDO2](howto-authentication-passwordless-security-key-on-premises.md).

**Para las aplicaciones web de Azure AD y los dispositivos Windows unidos a Azure AD**, use:

* Windows 10 versión 1809 o superior con un explorador admitido, como Microsoft Edge o Mozilla Firefox (versión 67 o superior).

* La versión 1809 de Windows 10 admite el inicio de sesión de FIDO2 y puede requerir la implementación del software del fabricante de la clave de FIDO2. Se recomienda utilizar la versión 1903 o posterior.

**Para dispositivos unidos a un dominio de Azure AD híbrido**, use: 

* Windows 10, versión 2004 o posterior. 

* Servidores de dominio totalmente revisados que ejecutan Windows Server 2016 o 2019. 

* Versión más reciente de Azure AD Connect.

#### <a name="enable-windows-10-support"></a>Habilitación de la compatibilidad con Windows 10

Para habilitar el inicio de sesión de Windows 10 con claves de seguridad FIDO2, es necesario habilitar la funcionalidad del proveedor de credenciales en Windows 10. Elija alguna de las acciones siguientes:

* [Habilitar proveedor de credenciales con Intune](howto-authentication-passwordless-security-key-windows.md)

  * Se recomienda la implementación de Intune.

* [Habilitar un proveedor de credenciales con un paquete de aprovisionamiento](howto-authentication-passwordless-security-key-windows.md)

  * Si no es posible la implementación de Intune, los administradores deben implementar un paquete en cada equipo para habilitar la funcionalidad del proveedor de credenciales. La instalación del paquete se puede llevar a cabo con una de las siguientes opciones:
    * Directiva de grupo o Configuration Manager
    * Instalación local en una máquina Windows 10

* [Habilitar un proveedor de credenciales con la directiva de grupo](howto-authentication-passwordless-security-key-windows.md)

   * Solo se admite para los dispositivos unidos a Azure AD híbridos.

#### <a name="enable-on-premises-integration"></a>Habilitación de la integración local

Siga los pasos del artículo [Habilitación del inicio de sesión con una clave de seguridad sin contraseña en recursos locales (versión preliminar)](howto-authentication-passwordless-security-key-on-premises.md).

> [!IMPORTANT] 
> Estos pasos también se deben completar para que los dispositivos unidos a Azure AD híbridos usen claves de seguridad FIDO2 para el inicio de sesión de Windows 10.


### <a name="key-restrictions-policy"></a>Directiva de restricciones de claves

Al implementar la clave de seguridad, también puede restringir el uso de claves FIDO2 solo a fabricantes específicos aprobados por su organización. La restricción de claves requiere el GUID de atestación de Authenticator (AAGUID). [Existen dos formas de obtener el AAGUID](howto-authentication-passwordless-security-key.md#security-key-authenticator-attestation-guid-aaguid).

![Imposición de restricciones de claves](media/howto-authentication-passwordless-deployment/security-key-enforce-key-restriction.png)


Si la clave de seguridad está restringida y el usuario intenta registrar la clave de seguridad FIDO2, recibe el siguiente error:

![Error de clave de seguridad cuando la clave está restringida](media/howto-authentication-passwordless-deployment/security-key-restricted-error.png)


Si el AAGUID se restringe después de que el usuario haya registrado la clave de seguridad, verá el mensaje siguiente:

![Vista para el usuario cuando se restringe el AAGUID](media/howto-authentication-passwordless-deployment/security-key-block-user-window.png)


*Clave FIDO2 bloqueada por directiva de restricción de claves

### <a name="deploy-fido2-security-key-sign-in"></a>Implementación del inicio de sesión con clave de seguridad FIDO2

Siga los pasos descritos en el artículo [Habilitación del inicio de sesión sin contraseña con clave de seguridad](howto-authentication-passwordless-security-key.md) para habilitar las claves de seguridad FIDO2 como método de autenticación sin contraseña en su organización. 

### <a name="testing-security-keys"></a>Prueba de las llaves de seguridad

Los siguientes son casos de prueba de ejemplo para la autenticación sin contraseña con claves de seguridad.

#### <a name="passwordless-fido-sign-in-to-azure-active-directory-joined-windows-10-devices"></a>Inicio de sesión con FIDO sin contraseña en dispositivos Windows 10 unidos a Azure Active Directory


| Escenario (compilación de Windows)| Resultados esperados |
| - |-|
| El usuario puede registrar el dispositivo FIDO2 (1809)| El usuario puede registrar el dispositivo FIDO2 en Configuración > Cuentas > Opciones de inicio de sesión > Llave de seguridad |
| El usuario puede restablecer el dispositivo FIDO2 (1809)| El usuario puede restablecer el dispositivo FIDO2 mediante el software del fabricante |
| El usuario puede iniciar sesión con el dispositivo FIDO2 (1809)| El usuario puede seleccionar la opción Clave de seguridad en la ventana de inicio de sesión e iniciar sesión correctamente. |
| El usuario puede registrar el dispositivo FIDO2 (1903)| El usuario puede registrar el dispositivo FIDO2 en Configuración > Cuentas > Opciones de inicio de sesión > Llave de seguridad |
| El usuario puede restablecer el dispositivo FIDO2 (1903)| El usuario puede restablecer el dispositivo FIDO2 en Configuración > Cuentas > Opciones de inicio de sesión > Llave de seguridad |
| El usuario puede iniciar sesión con el dispositivo FIDO2 (1903)| El usuario puede seleccionar la opción Clave de seguridad en la ventana de inicio de sesión e iniciar sesión correctamente. |


#### <a name="passwordless-fido-sign-in-to-azure-ad-web-apps"></a>Inicio de sesión FIDO sin contraseña en aplicaciones web de Azure AD


| Escenario| Resultados esperados |
| - |-|
| El usuario puede registrar el dispositivo FIDO2 en aka.ms/mysecurityinfo con Microsoft Edge| El registro se realizará correctamente |
| El usuario puede registrar el dispositivo FIDO2 en aka.ms/mysecurityinfo con Firefox| El registro se realizará correctamente |
| El usuario puede iniciar sesión en OneDrive en línea mediante el dispositivo FIDO2 con Microsoft Edge| El inicio de sesión debería realizarse correctamente |
| El usuario puede iniciar sesión en OneDrive en línea mediante el dispositivo FIDO2 con Firefox| El inicio de sesión debería realizarse correctamente |
| Prueba de reversión del registro de dispositivos FIDO2 mediante la desactivación de las claves de seguridad FIDO2 en la ventana de métodos de autenticación del portal de Azure Active Directory| Los usuarios: <li> Se les solicitará que inicien sesión con su clave de seguridad. <li> Iniciaran sesión correctamente y verán el siguiente error: "Your company policy requires that you use a different method to sign in" (La directiva de la empresa requiere que use otro método para iniciar sesión). <li>Pueden seleccionar un método diferente e iniciar sesión correctamente. Cierre la ventana e inicie sesión de nuevo para comprobar que no ven el mismo mensaje de error. |


### <a name="troubleshoot-security-key-sign-in"></a>Solución de problemas de inicio de sesión con clave de seguridad

| Escenario| Solución |
| - | -|
| El usuario no puede realizar el registro combinado.| Asegúrese de que el [registro combinado](concept-registration-mfa-sspr-combined.md) está habilitado. |
| El usuario no puede agregar una clave de seguridad a la [configuración de seguridad](https://aka.ms/mysecurityinfo).| Asegúrese de que las [llaves de seguridad](howto-authentication-passwordless-security-key.md) estén habilitadas. |
| El usuario no puede agregar una clave de seguridad en las opciones de inicio de sesión de Windows 10.| [Asegúrese de que las claves de seguridad para el inicio de sesión de Windows](concept-authentication-passwordless.md) estén habilitadas. |
| **Mensaje de error**: Hemos detectado que este sistema operativo o explorador no admite las claves de seguridad FIDO2.| Los dispositivos de seguridad FIDO2 sin contraseña solo se pueden registrar en exploradores compatibles (Microsoft Edge, Firefox versión 67) en Windows 10 versión 1809 o posteriores. |
| **Mensaje de error**: La directiva de la empresa requiere que use otro método para iniciar sesión.| Asegúrese de que las claves de seguridad estén habilitadas en el inquilino. |
| El usuario no puede administrar mi llave de seguridad en la versión 1809 de Windows 10| La versión 1809 requiere que use el software de administración de llaves de seguridad proporcionado por el proveedor de llaves FIDO2. Póngase en contacto con el proveedor para obtener soporte técnico. |
| Creo que mi clave de seguridad FIDO2 puede ser defectuosa, ¿cómo puedo probarla?| Vaya a [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), escriba las credenciales de una cuenta de prueba, conecte la clave de seguridad sospechosa, seleccione el botón + situado en la parte superior derecha de la pantalla, seleccione Crear y continúe con el proceso de creación. Si se produce un error en este escenario, es posible que el dispositivo esté defectuoso. |

## <a name="manage-passwordless-authentication"></a>Administración de la autenticación sin contraseña

Para administrar los métodos de autenticación sin contraseña del usuario en [Azure Portal](https://portal.azure.com/), seleccione su cuenta de usuario y, después, seleccione Métodos de autenticación.

### <a name="microsoft-graph-apis"></a>Microsoft Graph API 

También puede administrar los métodos de autenticación sin contraseña mediante la API de métodos de autenticación de Microsoft Graph. Por ejemplo:

* Puede recuperar los detalles de la clave de seguridad FIDO2 de un usuario y eliminarla si el usuario ha perdido la clave.

* Puede recuperar los detalles del registro de Microsoft Authenticator del usuario y eliminarlos si el usuario ha perdido el teléfono.

* Administre las directivas del método de autenticación para las claves de seguridad y la aplicación Microsoft Authenticator.

Para más información sobre los métodos de autenticación que se pueden administrar en Microsoft Graph, consulte [Introducción a la API de métodos de autenticación de Azure AD](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true).

### <a name="rollback"></a>Reversión

Aunque la autenticación sin contraseña es una característica ligera con un impacto mínimo en los usuarios finales, puede que sea necesario revertirla.

Para ello, es necesario que el administrador inicie sesión en Azure Portal, seleccione los métodos de autenticación seguros que quiera y cambie la opción Habilitar a No. Este proceso desactiva la funcionalidad sin contraseña para todos los usuarios.

![Reversión sin contraseña](media/howto-authentication-passwordless-deployment/passwordless-rollback.png)

A los usuarios que ya han registrado dispositivos de seguridad FIDO2 se les pide que utilicen el dispositivo de seguridad en el siguiente inicio de sesión y, a continuación, se les muestra el siguiente error:

![Ventana de error para la reversión de contraseñas](media/howto-authentication-passwordless-deployment/passswordless-rollback-error-window.png)

### <a name="reporting-and-monitoring"></a>Creación de informes y supervisión

Azure AD tiene informes que proporcionan información técnica y empresarial. Haga que los propietarios de las aplicaciones empresariales y técnicas asuman la propiedad de estos informes y los consuman en función de los requisitos de su organización.

En la tabla siguiente se proporcionan algunos ejemplos de escenarios de informes típicos:

| Administración de los riesgos.| Aumento de la productividad| Gobernanza y cumplimiento| otro|
|-|-|-|-|
| Tipos de informes| Métodos de autenticación: usuarios registrados para el registro de seguridad combinado| Métodos de autenticación: usuarios registrados para la notificación de aplicaciones| Inicios de sesión: revisar quién tiene acceso al inquilino y cómo. |
| Acciones posibles| Dirigirse a los usuarios no registrados todavía| Impulsar la adopción de la aplicación Microsoft Authenticator o llaves de seguridad| Revocar el acceso o aplicar directivas de seguridad adicionales para administradores |


#### <a name="track-usage-and-insights"></a>Uso y conclusiones del seguimiento

Azure AD agrega entradas a los registros de auditoría cuando:

* Un administrador realiza cambios en la sección Métodos de autenticación.

* Un usuario realiza cualquier tipo de cambio en sus credenciales dentro de Azure AD.

* Un usuario habilita o deshabilita su cuenta en una clave de seguridad o restablece el segundo factor para la clave de seguridad en su máquina Win 10. Consulte los ID de evento: 4670 y 5382.

**Azure AD conserva la mayoría de los datos de auditoría durante 30 días** y hace que estén disponibles en la API o el portal de administración de Azure para que pueda descargarlos en sus sistemas de análisis. Si necesita una retención más larga, exporte y consuma los registros en una herramienta SIEM como [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk o Sumo Logic. Se recomienda una retención más larga para la auditoría, el análisis de tendencias y otras necesidades empresariales según corresponda.

Hay dos pestañas en el panel de actividad Métodos de autenticación: Registro y Uso.

En la [pestaña Registro](https://portal.azure.com/) se muestra el número de usuarios que pueden realizar la autenticación sin contraseña, así como otros métodos de autenticación. En esta pestaña se muestran dos gráficos:

* Usuarios registrados por método de autenticación.

* Registro reciente por método de autenticación.

![Pestaña Registro para ver los métodos de autenticación](media/howto-authentication-passwordless-deployment/monitoring-registration-tab.png)

En la pestaña [Uso](https://portal.azure.com/) se muestran los inicios de sesión por método de autenticación.

![Pestaña Uso para ver los métodos de autenticación](media/howto-authentication-passwordless-deployment/monitoring-usage-tab.png)

Para más información, consulte [Seguimiento de los métodos de autenticación registrados y el uso en la organización de Azure AD](howto-authentication-methods-activity.md).

#### <a name="sign-in-activity-reports"></a>Informes de actividad de inicio de sesión

Use el [informe de actividad de inicio de sesión](../reports-monitoring/concept-sign-ins.md) para realizar un seguimiento de los métodos de autenticación usados para iniciar sesión en las distintas aplicaciones. 

Seleccione la fila del usuario y, a continuación, seleccione la pestaña **Detalles de la autenticación** para ver qué método de autenticación se usó para qué actividad de inicio de sesión.

![Informe de la actividad de inicio de sesión](media/howto-authentication-passwordless-deployment/reporting-sign-in-activity.png)

## <a name="next-steps"></a>Pasos siguientes

* [Obtener información sobre cómo funciona la autenticación con contraseñas](concept-authentication-passwordless.md)

* [Implementación de otras características de identidad](../fundamentals/active-directory-deployment-plans.md)