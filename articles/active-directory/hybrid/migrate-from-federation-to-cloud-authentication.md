---
title: Migración de la federación a la autenticación en la nube en Azure Active Directory
description: Este artículo contiene información acerca de cómo mover un entorno de identidad híbrida de la federación a la autenticación en la nube.
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.date: 07/08/2021
ms.author: baselden
author: BarbaraSelden
manager: MartinCo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd25928e245e6561be53ace29b017f476da7f1f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178050"
---
# <a name="migrate-from-federation-to-cloud-authentication"></a>Migración de la federación a la autenticación en la nube 

En este artículo, aprenderá a implementar la autenticación de usuario en la nube con la [sincronización de hash de contraseña (PHS)](whatis-phs.md) o la [autenticación transferida (PTA)](how-to-connect-pta.md) de Azure Active Directory. Aunque presentamos el caso de uso para pasar de [Servicios de federación de Active Directory (AD FS)](whatis-fed.md) a los métodos de autenticación en la nube, la guía también se aplica en gran medida a otros sistemas locales.

Antes de continuar, se recomienda que revise nuestra guía sobre [cómo elegir el método de autenticación adecuado](choose-ad-authn.md) y compare los métodos más adecuados para su organización.

Se recomienda usar PHS para la autenticación en la nube.

## <a name="staged-rollout"></a>Lanzamiento preconfigurado

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]

El lanzamiento preconfigurado supone una excelente forma de probar de forma selectiva grupos de usuarios con funcionalidades de autenticación en la nube, como Azure AD Multi-Factor Authentication (MFA), Acceso condicional, Identity Protection para credenciales filtradas, Identity Governance y otros, antes de utilizar sus propios dominios. 

Consulte el plan de implementación del lanzamiento preconfigurado para conocer los [escenarios admitidos](how-to-connect-staged-rollout.md#supported-scenarios) y [no admitidos](how-to-connect-staged-rollout.md#unsupported-scenarios). Se recomienda usar el lanzamiento preconfigurado para probar antes de la migración de los dominios.

Para obtener información sobre cómo configurar el lanzamiento preconfigurado, consulte la [guía interactiva](https://mslearn.cloudguides.com/guides/Test%20migration%20to%20cloud%20authentication%20using%20staged%20rollout%20in%20Azure%20AD) para la migración a la autenticación en la nube mediante el lanzamiento preconfigurado en Azure AD).

## <a name="migration-process-flow"></a>Flujo del proceso de migración

![Flujo de proceso para migrar a la autenticación en la nube](media/deploy-cloud-user-authentication/process-flow-migration.png)

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar la migración, asegúrese de que cumple estos requisitos previos.

### <a name="required-roles"></a>Roles necesarios

Para utilizar el lanzamiento preconfigurado, debe ser administrador global en el inquilino. 

Para habilitar el inicio de sesión único de conexión directa en un bosque específico de Windows Active Directory, debe ser administrador de dominio.

### <a name="step-up-azure-ad-connect-server"></a>Actualización del servidor de Azure AD Connect a una edición superior

Instale [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) o [actualice a la versión más reciente](how-to-upgrade-previous-version.md). Al actualizar el servidor de Azure AD Connect a una edición superior, se reduce el tiempo de migración de AD FS a los métodos de autenticación en la nube de posibles horas a minutos. 

### <a name="document-current-federation-settings"></a>Documentación de la configuración actual de la federación

Para buscar la configuración actual de la federación, ejecute el cmdlet [Get-MsolDomainFederationSettings](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login). 

Compruebe los valores de configuración que podrían haberse personalizado para su documentación de diseño e implementación de federación. En concreto, busque las personalizaciones en **PreferredAuthenticationProtocol**, **SupportsMfa** y **PromptLoginBehavior**.

### <a name="back-up-federation-settings"></a>Copia de seguridad de la configuración de federación

Aunque esta implementación no cambia ningún otro usuario de confianza en la granja de AD FS, puede hacer una copia de seguridad de la configuración:

 - Use la [herramienta de restauración rápida de AD FS](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) de Microsoft para restaurar una granja existente o crear una nueva.

- Exporte la relación de confianza del usuario autenticado de la Plataforma de identidad de Microsoft 365 y las reglas de notificación personalizadas asociadas que agregó mediante el siguiente ejemplo de PowerShell:

    ```powershell

    (Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"

    ```

## <a name="plan-the-project"></a>Planeamiento del proyecto

Cuando fracasan los proyectos tecnológicos, normalmente se debe a expectativas incorrectas relacionadas con el impacto, los resultados y las responsabilidades. Para evitar estos problemas, [asegúrese de que involucra a las partes interesadas adecuadas](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) y que estas conocen bien sus roles.

### <a name="plan-communications"></a>Planeamiento de las comunicaciones

Después de la migración a la autenticación en la nube, cambia la experiencia de inicio de sesión del usuario para acceder a Microsoft 365 y otros recursos autenticados mediante Azure AD. Los usuarios que están fuera de la red solo ven la página de inicio de sesión de Azure AD. 

Comunique de forma proactiva a los usuarios cómo y cuándo va a cambiar su experiencia, y cómo obtener soporte técnico si tienen cualquier problema.

### <a name="plan-the-maintenance-window"></a>Planeación de la ventana de mantenimiento

Después de la conversión del dominio, puede que Azure AD siga enviando solicitudes de autenticación heredadas de Exchange Online a los servidores de AD FS hasta cuatro horas después. El retraso se debe a que la caché de Exchange Online para la [autenticación de aplicaciones heredadas](../fundamentals/concept-fundamentals-block-legacy-authentication.md) puede tardar hasta cuatro horas en tener en cuenta el paso de la federación a la autenticación en la nube.

Durante esta ventana de cuatro horas, puede solicitar a los usuarios credenciales varias veces al volver a autenticarse en las aplicaciones que usan la autenticación heredada. El usuario aún puede autenticarse correctamente en AD FS, pero Azure AD ya no acepta el token emitido del usuario porque se ha quitado la confianza de la federación.

Los clientes heredados existentes (Exchange ActiveSync, Outlook 2010/2013) no se ven afectados porque Exchange Online mantiene una caché de sus credenciales durante un período de tiempo establecido. La caché se usa para autenticar al usuario en silencio. El usuario no tiene que volver a AD FS. Las credenciales almacenadas en el dispositivo para estos clientes se utilizan para volver a autenticarse en modo silencioso una vez que se borre la caché. No se espera que los usuarios reciban mensajes de contraseña como resultado del proceso de conversión de dominio.

Los clientes de autenticación moderna (Office 2016 y Office 2013, las aplicaciones iOS y Android) usan un token de actualización válido para obtener nuevos tokens de acceso para un acceso continuado a los recursos en lugar de volver a AD FS. Estos clientes son inmunes a los avisos de contraseña resultantes del proceso de conversión de dominio. Los clientes seguirán funcionando sin ninguna configuración adicional.

### <a name="plan-for-rollback"></a>Planeación para la reversión

> [!TIP]
> Considere la posibilidad de planear la transición de dominios fuera del horario comercial por si es necesario realizar una reversión. 

Para planear la reversión, utilice la [configuración de federación actual documentada](#document-current-federation-settings) y consulte la [documentación de diseño e implementación de la federación](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide). 

El proceso de reversión debe incluir la conversión de dominios administrados a dominios federados mediante el cmdlet [Convert-MSOLDomainToFederated](/powershell/module/msonline/convert-msoldomaintofederated). Si es necesario, también debe incluir la configuración de reglas de notificaciones adicionales.

## <a name="migration-considerations"></a>Consideraciones de migración 

Estas son las consideraciones clave para la migración.

### <a name="plan-for-customizations-settings"></a>Planeamiento de la configuración de personalizaciones 

El archivo onload.js no se puede duplicar en Azure AD. Si la instancia de AD FS está muy personalizada y se basa en una configuración de personalización específica del archivo onload.js, compruebe si Azure AD puede cumplir los requisitos de personalización actuales y planee en consecuencia. Comunique estos próximos cambios a los usuarios.

#### <a name="sign-in-experience"></a>Experiencia de inicio de sesión

No se puede personalizar la experiencia de inicio de sesión de Azure AD. Independientemente de cómo iniciaron sesión los usuarios anteriormente, necesita un nombre de dominio completo, como el nombre principal de usuario (UPN) o el correo electrónico, para iniciar sesión en Azure AD. 

#### <a name="organization-branding"></a>Personalización de marca de la organización

Puede [personalizar la página de inicio de sesión de Azure AD](../fundamentals/customize-branding.md). Se deben esperar algunos cambios visuales de AD FS en las páginas de inicio de sesión después de la conversión. 

>[!NOTE] 
>La personalización de marca de la organización no está disponible en licencias gratuitas de Azure AD a menos que tenga una licencia de Microsoft 365.

### <a name="plan-for-conditional-access-policies"></a>Planeamiento de directivas de acceso condicional

Evalúe si actualmente usa el acceso condicional para la autenticación o si usa directivas de control de acceso en AD FS. 

Considere la posibilidad de reemplazar las directivas de control de acceso de AD FS por las [directivas de acceso condicional](../conditional-access/overview.md) de Azure AD equivalentes y las [reglas de acceso de cliente de Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules). Puede usar grupos de Azure AD o locales para el acceso condicional.

**Deshabilitar la autenticación heredada:** debido al mayor riesgo asociado a los protocolos de autenticación heredados, cree una [directiva de acceso condicional para bloquear la autenticación heredada](../conditional-access/howto-conditional-access-policy-block-legacy.md).

### <a name="plan-support-for-mfa"></a>Planeamiento de la compatibilidad con MFA

Cada dominio federado de Azure AD tiene una marca SupportsMFA. 

**Si la marca SupportsMFA está establecida en True**, Azure AD redirige a los usuarios para realizar la autenticación multifactor en AD FS u otros proveedores de federación. Por ejemplo, si un usuario accede a una aplicación para la que se ha configurado una directiva de acceso condicional que necesita MFA, se le redirigirá a AD FS. Al agregar Azure AD MFA como método de autenticación en AD FS, se puede invocar este método después de completar las configuraciones.

**Si la marca SupportsMFA está establecida en False**, es probable que no esté usando Azure MFA; probablemente utilizará reglas de notificaciones en usuarios de confianza de AD FS para invocar MFA. 

Puede comprobar el estado de la marca **SupportsMFA** con el siguiente cmdlet de Windows PowerShell:
```powershell
 Get-MsolDomainFederationSettings –DomainName yourdomain.com
 ```

>[!NOTE] 
>Servidor Microsoft MFA está llegando al final del ciclo de vida de soporte técnico; si lo usa, debe pasar a Azure AD MFA. Para más información, consulte la **[documentación sobre migración de Servidor Microsoft MFA a Azure Multi-Factor Authentication](../authentication/how-to-migrate-mfa-server-to-azure-mfa.md)** .
Si planea usar Azure AD MFA, le recomendamos que use el **[registro combinado para el autoservicio de restablecimiento de contraseña (SSPR) y Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md)** para que los usuarios registren sus métodos de autenticación una sola vez. 

## <a name="plan-for-implementation"></a>Planeamiento de la implementación

En esta sección se incluye el trabajo que se debe hacer antes de cambiar el método de inicio de sesión y convertir los dominios.

### <a name="create-necessary-groups-for-staged-rollout"></a>Creación de los grupos necesarios para el lanzamiento preconfigurado

*Si no usa el lanzamiento preconfigurado, omita este paso.*

Cree grupos para el lanzamiento preconfigurado. También deberá crear grupos para las directivas de acceso condicional si decide agregarlas.

Se recomienda usar un grupo controlado en Azure AD, también conocido como grupo solo en la nube. Puede usar grupos de seguridad de Azure AD o grupos de Microsoft 365 para mover usuarios a MFA y para las directivas de acceso condicional. Para obtener más información, consulte [cómo crear un grupo de seguridad de Azure AD](../fundamentals/active-directory-groups-create-azure-portal.md) y la [información general de los grupos de Microsoft 365 para administradores](/microsoft-365/admin/create-groups/office-365-groups).

Los miembros de un grupo se habilitan automáticamente para el lanzamiento preconfigurado. No se admiten grupos anidados y dinámicos en el lanzamiento preconfigurado.

### <a name="pre-work-for-sso"></a>Trabajo previo para el inicio de sesión único

La versión del inicio de sesión único que use depende del sistema operativo del dispositivo y del estado de unión.

- **Para Windows 10, Windows Server 2016 y versiones posteriores**, se recomienda usar el SSO a través del [token de actualización principal (PRT)](../devices/concept-primary-refresh-token.md) con [dispositivos unidos a Azure AD](../devices/concept-azure-ad-join.md), [dispositivos unidos a Azure AD híbrido](../devices/concept-azure-ad-join-hybrid.md) y [dispositivos registrados en Azure AD](../devices/concept-azure-ad-register.md). 

- **Para dispositivos con Windows 7 y 8.1**, se recomienda usar el [inicio de sesión único de conexión directa](how-to-connect-sso.md) con unión a un dominio para registrar el equipo en Azure AD. No es necesario sincronizar estas cuentas como hace en los dispositivos con Windows 10. Sin embargo, debe completar este [trabajo previo para el inicio de sesión único de conexión directa mediante PowerShell](how-to-connect-staged-rollout.md#pre-work-for-seamless-sso).

### <a name="pre-work-for-phs-and-pta"></a>Trabajo previo para PHS y PTA

En función del método de inicio de sesión elegido, debe completar el [trabajo previo para PHS](how-to-connect-staged-rollout.md#pre-work-for-password-hash-sync) o [para PTA](how-to-connect-staged-rollout.md#pre-work-for-pass-through-authentication).

## <a name="implement-your-solution"></a>Implementación de la solución

Por último, cambie el método de inicio de sesión a PHS o PTA, según lo planeado, y convierta los dominios de la federación a la autenticación en la nube. 

### <a name="using-staged-rollout"></a>Con lanzamiento preconfigurado

Si usa el lanzamiento preconfigurado, siga los pasos de los vínculos siguientes:

1. [Habilite el lanzamiento preconfigurado de una característica específica en el inquilino](how-to-connect-staged-rollout.md#enable-staged-rollout).

2. Una vez completadas las pruebas, [convierta los dominios de federados a administrados](#convert-domains-from-federated-to-managed).

### <a name="without-using-staged-rollout"></a>Sin lanzamiento preconfigurado 

Hay dos opciones para habilitar este cambio:

- **Opción A:** cambiar con Azure AD Connect.
  
  *Disponible si ha configurado inicialmente su entorno federado de AD FS/ping con Azure AD Connect.*

- **Opción B:** cambiar con Azure AD Connect y PowerShell.
 
  *Disponible si inicialmente no ha configurado los dominios federados mediante Azure AD Connect o si usa servicios de federación de terceros.*

Para elegir una de estas opciones, debe saber cuál es la configuración actual.

#### <a name="verify-current-azure-ad-connect-settings"></a>Comprobación de la configuración actual de Azure AD Connect

Inicie sesión en el [portal de Azure AD](https://aad.portal.azure.com/), seleccione **Azure AD Connect** y verifique la configuración de **USER SIGN_IN** tal como se muestra en este diagrama:

![Comprobación de la configuración actual de Azure AD Connect](media/deploy-cloud-user-authentication/current-user-settings-on-azure-ad-portal.png)


**Para verificar cómo se ha configurado la federación:**

1. En el servidor de Azure AD Connect, abra **Azure AD Connect** y seleccione **Configurar**.

2. En **Tareas adicionales > Administrar federación**, seleccione **Ver configuración de federación**. 

    ![Ver Administrar federación](media/deploy-cloud-user-authentication/manage-federation.png)

    Si la configuración de AD FS aparece en esta sección, puede suponer con seguridad que AD FS se configuró originalmente con Azure AD Connect. Consulte la imagen siguiente como ejemplo:

    ![Ver configuración de AD FS](media/deploy-cloud-user-authentication/federation-configuration.png)

    Si AD FS no aparece en la configuración actual, debe convertir manualmente los dominios de identidad federada a identidad administrada mediante PowerShell.

#### <a name="option-a"></a>Opción A

**Cambio de federación al nuevo método de inicio de sesión mediante Azure AD Connect**

1. En el servidor de Azure AD Connect, abra **Azure AD Connect** y seleccione **Configurar**.

2. En la página **Tareas adicionales**, seleccione **Cambiar inicio de sesión de usuario** y, después, seleccione **Siguiente**.
   
    ![Ver Tareas adicionales](media/deploy-cloud-user-authentication/additional-tasks.png)

3. En la página **Conectar con Azure AD**, proporcione las credenciales de la cuenta de administrador global.

4. En la página **Inicio de sesión de usuario**:

    - Si selecciona el botón de opción **Autenticación transferida**, active **Habilitar inicio de sesión único** y, luego, seleccione **Siguiente**.

    -  Si selecciona el botón de opción **Sincronización de hash de contraseña**, asegúrese de activar la casilla **No convertir las cuentas de usuario**. La opción está en desuso. Seleccione **Habilitar inicio de sesión único** y, después, **Siguiente**.

      ![Activar Habilitar inicio de sesión único en la página Inicio de sesión de usuario](media/deploy-cloud-user-authentication/user-sign-in.png)

5. En la página **Habilitar inicio de sesión único**, escriba las credenciales de la cuenta de administrador del dominio y, después, seleccione **Siguiente**.

    ![Página Habilitar inicio de sesión único](media/deploy-cloud-user-authentication/enable-single-sign-on.png)

    Las credenciales de la cuenta de administrador de dominio son necesarias para habilitar el inicio de sesión único de conexión directa. Durante el proceso se realizan las acciones siguientes, que requieren estos permisos elevados:
      - Se crea una cuenta de equipo denominada AZUREADSSO (que representa a Azure AD) en la instancia local de Active Directory.
      - La clave de descifrado de Kerberos de la cuenta de equipo se comparte de manera segura con Azure AD.
      - Se crean dos nombres de entidad de seguridad de servicio (SPN) de Kerberos que representan dos direcciones URL que se usan en el inicio de sesión de Azure AD.

    Las credenciales de administrador de dominio no se almacenan en Azure AD Connect ni en Azure AD y se descartan cuando el proceso finaliza correctamente. Se usan para activar esta característica.

6. En la página **Listo para configurar**, asegúrese de que la casilla **Inicie el proceso de sincronización cuando se complete la configuración** esté activada. A continuación, seleccione **Configurar**.

    ![Página Listo para configurar](media/deploy-cloud-user-authentication/ready-to-configure.png)

 > [!IMPORTANT] 
 > En este momento, todos los dominios federados cambiarán a la autenticación administrada. El método de inicio de sesión de usuario seleccionado es el nuevo método de autenticación.

1. Abra el portal de Azure AD, seleccione **Azure Active Directory** y, después, seleccione **Azure AD Connect**.

2. Compruebe la configuración de estos parámetros:

      - **Federación** está establecido en **Deshabilitado**.
      - **Inicio de sesión único de conexión directa** está establecido en **Habilitado**.
      - **Sincronización de hash de contraseña** está establecido en **Habilitado**.

  ![ Nueva verificación de la configuración actual del usuario](media/deploy-cloud-user-authentication/reverify-settings.png)

3. En caso de que cambie a PTA, siga los pasos que se indican a continuación.

##### <a name="deploy-more-authentication-agents-for-pta"></a>Implementación de más agentes de autenticación para PTA

>[!NOTE]
> Para la autenticación transferida, es necesario implementar agentes ligeros en el servidor de Azure AD Connect y en el equipo local que ejecuta Windows Server. Para reducir la latencia, instale los agentes lo más cerca posible de los controladores de Active Directory.

La mayoría de los clientes solo necesitarán dos o tres agentes de autenticación para obtener alta disponibilidad y la capacidad necesaria. Un inquilino puede tener registrados hasta 12 agentes. El primer agente siempre se instala en el propio servidor de Azure AD Connect. Para conocer las limitaciones de agentes y las opciones de implementación de agentes, consulte [Autenticación de paso a través de Azure AD: limitaciones actuales](how-to-connect-pta-current-limitations.md).

1. Seleccione **Autenticación de paso a través**.
2. En la página **Autenticación de paso a través**, seleccione el botón **Descargar**.
3. En la página **Descargar agente**, seleccione **Accept terms and download** (Aceptar los términos y descargar).

    Comienzan a descargarse más agentes de autenticación. Instale el agente de autenticación secundario en un servidor unido a un dominio.

4. Ejecute la instalación del agente de autenticación. Durante la instalación, debe especificar las credenciales de una cuenta de administrador global.

    ![ Agente de autenticación de Microsoft Azure AD Connect](media/deploy-cloud-user-authentication/install-azure-ad-connect-installation-agent.png)

5. Una vez instalado el agente de autenticación, puede volver a la página de mantenimiento de PTA para comprobar el estado de los agentes adicionales.

#### <a name="option-b"></a>Opción B

**Cambio de federación al nuevo método de inicio de sesión mediante Azure AD Connect y PowerShell**

*Disponible si inicialmente no ha configurado los dominios federados mediante Azure AD Connect o si usa servicios de federación de terceros.*

En el servidor de Azure AD Connect, siga los pasos del 1 al 5 de [Opción A](#option-a). Observará que en la página Inicio de sesión de usuario, la opción **No configurar** está ya seleccionada.

![ Ver la opción No configurar en la página de inicio de sesión del usuario](media/deploy-cloud-user-authentication/do-not-configure-on-user-sign-in-page.png)

1. Abra el portal de Azure AD, seleccione **Azure Active Directory** y, después, seleccione **Azure AD Connect**. 

2. Compruebe la configuración de estos parámetros:

  - **Federación** está establecido en **Habilitado**.
  - **Inicio de sesión único de conexión directa** está establecido en **Deshabilitado**.
  - **Sincronización de hash de contraseña** está establecido en **Habilitado**.

    ![ Verificación de la configuración actual del usuario en Azure Portal](media/deploy-cloud-user-authentication/verify-current-user-settings-on-azure-ad-portal.png)

**Solo en el caso de PTA**, siga estos pasos para instalar más servidores de agente de PTA.

1. Abra el portal de Azure AD, seleccione **Azure Active Directory** y, después, seleccione **Azure AD Connect**.

2. Seleccione **Autenticación de paso a través**. Compruebe que el estado es **Activo**.

    ![ Configuración de la autenticación transferida](media/deploy-cloud-user-authentication/pass-through-authentication-settings.png)

   Si el agente de autenticación no está activo, lleva a cabo estos [pasos de solución de problemas](tshoot-connect-pass-through-authentication.md) antes de continuar con el proceso de conversión de dominio en el paso siguiente. Si convierte los dominios antes de validar que los agentes de PTA están instalados correctamente y que su estado es **Activo** en Azure Portal, se arriesga a provocar una interrupción de la autenticación.

3. [Implemente más agentes de autenticación](#deploy-more-authentication-agents-for-pta).

### <a name="convert-domains-from-federated-to-managed"></a>Convertir dominios de federado a administrado

**En este punto, la autenticación federada sigue activa y operativa para los dominios.** Para continuar con la implementación, debe convertir cada dominio de identidad federada a identidad administrada.

>[!IMPORTANT]
> No tiene que convertir todos los dominios al mismo tiempo. Puede empezar con un dominio de prueba en el inquilino de producción o con el dominio que tenga el menor número de usuarios.

**Realice la conversión mediante el módulo de PowerShell de Azure AD:**

1. En PowerShell, inicie sesión en Azure AD con una cuenta de administrador global.

2. Para convertir el primero dominio, ejecute el comando siguiente:
   ```powershell
    Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
    ```
    Consulte [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication).

3. En el portal de Azure AD, seleccione **Azure Active Directory > Azure AD Connect**.

4. Compruebe que el dominio se ha convertido a administrado, para lo que debe ejecutar el siguiente comando:
    ```powershell
    Get-MsolDomain -DomainName <domain name>
    ```
## <a name="complete-your-migration"></a>Finalización de la migración

Complete las siguientes tareas para comprobar el método de registro y finalizar el proceso de conversión.

### <a name="test-the-new-sign-in-method"></a>Prueba del nuevo método de inicio de sesión

Cuando el inquilino usaba la identidad federada, los usuarios eran redirigidos desde la página de inicio de sesión de Azure AD hasta el entorno de AD FS. Ahora que el inquilino está configurado para usar el nuevo método de inicio de sesión en lugar de la autenticación federada, ya no se redirige a los usuarios a AD FS. 

**En su lugar, los usuarios inician sesión directamente en la página de inicio de sesión de Azure AD.**

Siga los pasos de este vínculo para la [validación del inicio de sesión con PHS/PTA e inicio de sesión único de conexión directa](how-to-connect-staged-rollout.md#validation) (si es necesario).

### <a name="remove-a-user-from-staged-rollout"></a>Eliminación de un usuario del lanzamiento preconfigurado

Si ha usado el lanzamiento preconfigurado, recuerde desactivar sus características una vez que haya terminado la migración. 

**Para deshabilitar la característica de lanzamiento preconfigurado, deslice el control de nuevo a Desactivado.**

### <a name="sync-userprincipalname-updates"></a>Sincronización de actualizaciones de UserPrincipalName

Históricamente, las actualizaciones para el atributo **UserPrincipalName**, que usa el servicio de sincronización desde el entorno local, han estado bloqueadas, a menos que se cumplieran las siguientes condiciones:

   - El usuario está en un dominio de identidad administrada (no federada).
   - Al usuario no se le ha asignado una licencia.

Para saber cómo comprobar o activar esta característica, consulte [Sincronización de actualizaciones de userPrincipalName](how-to-connect-syncservice-features.md).

## <a name="manage-your-implementation"></a>Administrar la implementación

### <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Sustitución de la clave de descifrado de Kerberos de inicio de sesión único de conexión directa

Se recomienda que sustituya la clave de descifrado de Kerberos al menos cada 30 días para que se corresponda con la forma en que los miembros del dominio de Active Directory envían los cambios de contraseña. Como no hay ningún dispositivo asociado conectado al objeto de la cuenta de equipo AZUREADSSO, la sustitución debe realizarse manualmente.

Consulte la pregunta frecuente sobre [cómo se puede sustituir la clave de descifrado de Kerberos de la cuenta de equipo AZUREADSSO](how-to-connect-sso.md).

### <a name="monitoring-and-logging"></a>Supervisión y registro

Supervise los servidores que ejecutan los agentes de autenticación para mantener la disponibilidad de la solución. Además de los contadores de rendimiento generales del servidor, los agentes de autenticación exponen objetos de rendimiento que pueden usarse para comprender las estadísticas de autenticación y los errores.

Los agentes de autenticación registran las operaciones en los registros de eventos de Windows que se encuentran bajo los registros de aplicaciones y servicios. También puede activar el registro para solucionar problemas.

Para confirmar las distintas acciones realizadas en el lanzamiento preconfigurado, puede [auditar eventos para PHS, PTA o inicio de sesión único de conexión directa](how-to-connect-staged-rollout.md#auditing).

### <a name="troubleshoot"></a>Solucionar problemas

El equipo de soporte técnico debe saber solucionar cualquier problema de autenticación que surja durante el cambio de federación a administrado, o después. Use la siguiente documentación de solución de problemas para ayudar al equipo de soporte técnico a familiarizarse con los pasos comunes para la solución de problemas comunes y las acciones adecuadas que pueden ayudar a aislar y resolver el problema.

-  [PHS de Azure AD](tshoot-connect-password-hash-synchronization.md)
- [PTA de Azure AD](tshoot-connect-pass-through-authentication.md)
- [SSO de conexión directa de Azure AD](tshoot-connect-sso.md)

## <a name="decommission-ad-fs-infrastructure"></a>Retirada de la infraestructura de AD FS

### <a name="migrate-app-authentication-from-ad-fs-to-azure-ad"></a>Migración de la autenticación de aplicaciones de AD FS a Azure AD

La migración requiere la evaluación de cómo está configurada la aplicación en el entorno local y, a continuación, la asignación de esa configuración a Azure AD.

Si planea seguir usando AD FS con aplicaciones locales y SaaS mediante el protocolo SAML/WS-FED u Oauth, usará AD FS y Azure AD después de convertir los dominios para la autenticación de usuario. En este caso, puede proteger sus aplicaciones y recursos locales con acceso híbrido seguro (SHA) por medio de [Azure AD Application Proxy](../app-proxy/what-is-application-proxy.md) o una de las [integraciones de asociados de Azure AD](../manage-apps/secure-hybrid-access.md). Con Application Proxy o uno de nuestros asociados, puede proporcionar acceso remoto seguro a sus aplicaciones locales. Los usuarios se benefician al conectarse fácilmente a sus aplicaciones desde cualquier dispositivo después de un [inicio de sesión único](../manage-apps/add-application-portal-setup-sso.md).

Puede mover las aplicaciones SaaS que están actualmente federadas con ADFS a Azure AD. Vuelva a realizar la configuración para la autenticación con Azure AD por medio de un conector integrado de la [Galería de aplicaciones de Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) o mediante el [registro de la aplicación en Azure AD](../develop/quickstart-register-app.md).

Para más información, consulte: 

- [Traslado de la autenticación de las aplicaciones de los Servicios de federación de Active Directory (AD FS) a Azure Active Directory](../manage-apps/migrate-adfs-apps-to-azure.md) y
- [Cuaderno de estrategias de migración de aplicaciones de AD FS a Azure AD para desarrolladores](/samples/azure-samples/ms-identity-adfs-to-aad/ms-identity-dotnet-adfs-to-aad)

### <a name="remove-relying-party-trust"></a>Eliminación de la relación de confianza del usuario autenticado

Si tiene Azure AD Connect Health, puede [supervisar el uso](how-to-connect-health-adfs.md) desde Azure Portal. En caso de que el uso muestre que no es necesaria ninguna autenticación nueva y haya comprobado que todos los usuarios y clientes se están autenticando correctamente mediante Azure AD, es seguro quitar la relación de confianza para usuario autenticado en Microsoft 365.

Si no usa AD FS para otros fines (es decir, para otras relaciones de confianza), es seguro retirar AD FS en este momento.

## <a name="next-steps"></a>Pasos siguientes

- [Consulte más información sobre la migración de aplicaciones](../manage-apps/migration-resources.md).
- [Implemente otras características de identidad](../fundamentals/active-directory-deployment-plans.md).