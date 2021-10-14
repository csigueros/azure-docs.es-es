---
title: Tutorial para migrar directivas de inicio de sesión de Okta al acceso condicional de Azure Active Directory
titleSuffix: Active Directory
description: En este tutorial va a aprender a migrar directivas de inicio de sesión de Okta al acceso condicional de Azure Active Directory.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 840d2cfd64f6384c8c503e472f7557ababd0d08e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389198"
---
# <a name="tutorial-migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access"></a>Tutorial: Migrar directivas de inicio de sesión de Okta al acceso condicional de Azure Active Directory

En este tutorial va a conocer cómo puede una organización migrar de directivas de inicio de sesión globales o de nivel de aplicación de Okta a directivas de acceso condicional de Azure Active Directory (Azure AD) para proteger el acceso de los usuarios en Azure AD y las aplicaciones conectadas.

En este tutorial se da por hecho que tiene un inquilino de Office 365 federado en Okta para el inicio de sesión y la autenticación multifactor (MFA). También debe tener configurados los agentes de aprovisionamiento del servidor o la nube de Azure AD Connect para el aprovisionamiento de usuarios en Azure AD.

## <a name="prerequisites"></a>Prerrequisitos

Al pasar del inicio de sesión de Okta al acceso condicional de Azure AD, es importante comprender los requisitos de licencia. El acceso condicional de Azure AD exige que los usuarios tengan una licencia de Azure AD Premium P1 asignada antes del registro en la autenticación multifactor de Azure AD.

Antes de realizar cualquiera de los pasos de Unión a Azure AD híbrido, necesita una credencial de administrador de empresa en el bosque local para configurar el registro de punto de conexión de servicio (SCP).

## <a name="catalog-current-okta-sign-on-policies"></a>Catalogación de las directivas de inicio de sesión de Okta actuales

Para completar una transición correcta al acceso condicional, evalúe las directivas de inicio de sesión de Okta existentes para determinar los casos de uso y los requisitos que se van a pasar a Azure AD.

1. Consulte las directivas de inicio de sesión globales; para ello, vaya a **Seguridad** > **Autenticación** > **Inicio de sesión**.

   ![Captura de pantalla que muestra las directivas de inicio de sesión globales.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies.png)

   En este ejemplo la directiva de inicio de sesión global aplica MFA en todas las sesiones fuera de las zonas de red configuradas.

   ![Captura de pantalla que muestra cómo las directivas de inicio de sesión globales aplican MFA.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies-enforce-mfa.png)

2. Vaya a **Aplicaciones** y consulte las directivas de inicio de sesión de nivel de aplicación. Seleccione **Aplicaciones** en el submenú y a continuación seleccione su instancia conectada de Office 365 en la **lista Aplicaciones activas**.

3. Seleccione **Inicio de sesión** y desplácese a la parte inferior de la página.

En el siguiente ejemplo la directiva de inicio de sesión de aplicaciones de Office 365 tiene cuatro reglas independientes:

- **Exigir MFA para sesiones móviles**: exige MFA desde todas las autenticaciones modernas o sesiones de explorador en iOS o Android.
- **Permitir dispositivos Windows de confianza**: evita que se soliciten factores o comprobaciones adicionales para los dispositivos Okta de confianza.
- **Requerir MFA desde dispositivos de Windows que no son de confianza**: exige MFA desde cada autenticación moderna o sesión de explorador en dispositivos Windows que no son de confianza.
- **Bloquear la autenticación heredada**: evita que los clientes de autenticación heredados se conecten al servicio.

  ![Captura de pantalla que muestra reglas de inicio de sesión de Office 365.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-on-rules.png)

## <a name="configure-condition-prerequisites"></a>Configuración de requisitos previos de condición

Las directivas de acceso condicional de Azure AD se pueden configurar de modo que coincidan con las condiciones de Okta en la mayoría de los escenarios sin configuración adicional.

En algunos escenarios es posible que necesite una más configuración antes de configurar las directivas de acceso condicional. Los dos escenarios conocidos en el momento de redacción de este artículo son:

- **Ubicaciones de red de Okta a ubicaciones con nombre en Azure AD**: siga las instrucciones de [Uso de la condición de ubicación en una directiva de acceso condicional](../conditional-access/location-condition.md#named-locations) para configurar ubicaciones con nombre en Azure AD.
- **Confianza en dispositivo de Okta a acceso condicional basado en dispositivo**: el acceso condicional ofrece dos opciones posibles a la hora de evaluar el dispositivo de un usuario:

  - [Uso de la unión a Azure AD híbrido](#hybrid-azure-ad-join-configuration), que es una característica habilitada en el servidor de Azure AD Connect que sincroniza dispositivos actuales de Windows, como Windows 10, Windows Server 2016 y Windows Server 2019, con Azure AD.

  - [Inscripción del dispositivo en Endpoint Manager](#configure-device-compliance) y asignación de una directiva de cumplimiento.

### <a name="hybrid-azure-ad-join-configuration"></a>Configuración de la unión a Azure AD híbrido

Para habilitar Unión a Azure AD híbrido en el servidor de Azure AD Connect, ejecute el asistente para configuración. Debe realizar algunos pasos después de la configuración para inscribir dispositivos automáticamente.

>[!NOTE]
>Unión a Azure AD híbrido no es compatible con los agentes de aprovisionamiento en la nube de Azure AD Connect.

1. Siga estas [instrucciones](../devices/hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join) para habilitar Unión a Azure AD híbrido.

1. En la página **Configuración de SCP**, seleccione la lista desplegable **Servicio de autenticación**. Seleccione la dirección URL del proveedor de federación de Okta y luego **Agregar**. Escriba las credenciales de administrador de empresa local y seleccione **Siguiente**.

   ![Captura de pantalla que muestra la configuración de SCP.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/scp-configuration.png)

1. Si ha bloqueado la autenticación heredada en los clientes de Windows en la directiva de inicio de sesión global o de nivel de aplicación, cree una regla para permitir que finalice el proceso de Unión a Azure AD híbrido.

1. Permita toda la pila de autenticación heredada en todos los clientes de Windows. También puede ponerse en contacto con el equipo de soporte técnico de Okta para habilitar su cadena de cliente personalizada en las directivas de aplicación existentes.

### <a name="configure-device-compliance"></a>Configurar la compatibilidad del dispositivo

Unión a Azure AD híbrido es un reemplazo directo de la confianza de dispositivo Okta en Windows. Las directivas de acceso condicional también pueden mirar el cumplimiento de los dispositivos que se han inscrito completamente en Endpoint Manager:

- **Información general del cumplimiento**: vea las [directivas de cumplimiento de dispositivos de Intune](/mem/intune/protect/device-compliance-get-started#:~:text=Reference%20for%20non-compliance%20and%20Conditional%20Access%20on%20the,applicable%20%20...%20%203%20more%20rows).
- **Cumplimiento de dispositivos**: cree [directivas en Intune](/mem/intune/protect/create-compliance-policy).
- **Inscripción de Windows**: si ha optado por implementar Unión a Azure AD híbrido, puede implementar otra directiva de grupo para completar el [proceso de inscripción automática de estos dispositivos en Intune](/windows/client-management/mdm/enroll-a-windows-10-device-automatically-using-group-policy).
- **Inscripción de iOS/iPadOS**: antes de inscribir un dispositivo iOS, se deben realizar [configuraciones adicionales](/mem/intune/enrollment/ios-enroll) en la consola de administración de Endpoint Manager.
- **Inscripción de Android** : antes de inscribir un dispositivo Android, se deben realizar [configuraciones adicionales](/mem/intune/enrollment/android-enroll) en la consola de administración de Endpoint Manager.

## <a name="configure-azure-ad-multi-factor-authentication-tenant-settings"></a>Configuración del inquilino de autenticación multifactor de Azure AD

Antes de realizar la conversión al acceso condicional, confirme la configuración base del inquilino de autenticación multifactor de Azure AD de la organización.

1. Vaya a [Azure Portal](https://portal.azure.com) e inicie sesión con una cuenta de administrador global.

1. Seleccione **Azure Active Directory** > **Usuarios** > **Autenticación multifactor** para ir al portal heredado de autenticación multifactor de Azure AD.

   ![Captura de pantalla que muestra el portal heredado de autenticación multifactor de Azure AD.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/legacy-azure-ad-portal.png)

   También puede usar el vínculo heredado al [portal de autenticación multifactor de Azure AD](https://aka.ms/mfaportal).

1. En el menú heredado de **autenticación multifactor**, cambie el menú de estado en **Habilitado** y **Aplicado** para confirmar que no tiene usuarios habilitados para la MFA heredada. Si el inquilino tiene usuarios en las siguientes vistas, debe deshabilitarlos en el menú heredado. Solo entonces se pueden aplicar las directivas de acceso condicional en la cuenta.

   ![Captura de pantalla que muestra la deshabilitación de un usuario en el portal heredado de autenticación multifactor de Azure AD.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/disable-user-legacy-azure-ad-portal.png)

   El campo **Aplicado** también debe estar vacío.

   ![Captura de pantalla que muestra que el campo Aplicado vacío en el portal heredado de autenticación multifactor de Azure AD.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enforced-empty-legacy-azure-ad-portal.png)

1. Seleccione la opción **Configuración del servicio**. Cambie la selección de **Contraseñas de la aplicación** para **no permitir a los usuarios crear contraseñas de aplicación para iniciar sesión en aplicaciones que no son de explorador**.

1. Asegúrese de que las casillas **Omitir autenticación multifactor para solicitudes de usuarios federados en mi intranet** y **Permitir a los usuarios recordar la autenticación multifactor en dispositivos en los que confían (entre uno y 365 días)** están desactivadas y seleccione **Guardar**.

  >[!NOTE]
   >Vea los [procedimientos recomendados para configurar la solicitud de MFA](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).
   ![Captura de pantalla que muestra las casillas desactivadas en el portal heredado de autenticación multifactor de Azure AD.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/uncheck-fields-legacy-azure-ad-portal.png)

## <a name="configure-conditional-access-policies"></a>Configuración de directivas de acceso condicional

Después de configurar los requisitos previos y establecer la configuración base, es hora de compilar la primera directiva de acceso condicional.

1. Para configurar directivas de acceso condicional en Azure AD, vaya a [Azure Portal](https://portal.azure.com). Seleccione **Ver** en **Administrar Azure Active Directory**.

   Configure las directivas de acceso condicional con los [procedimientos recomendados para implementar y diseñar el acceso condicional](../conditional-access/plan-conditional-access.md#understand-conditional-access-policy-components).

1. Para imitar la directiva de MFA de inicio de sesión global de Okta, [cree una directiva](../conditional-access/howto-conditional-access-policy-all-users-mfa.md).

1. Cree una [regla de acceso condicional basada en la confianza del dispositivo](../conditional-access/require-managed-devices.md).

   Esta directiva, como cualquier otra de este tutorial, se puede dirigir a una aplicación concreta, a un grupo de prueba de usuarios o a ambos.

   ![Captura de pantalla que muestra las pruebas de un usuario.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/test-user.png)

   ![Captura de pantalla que muestra que las pruebas de un usuario se ha realizado correctamente.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/success-test-user.png)

1. Después de configurar la directiva basada en la ubicación y la directiva de confianza del dispositivo, es hora de configurar la directiva equivalente de [bloqueo de la autenticación heredada](../conditional-access/howto-conditional-access-policy-block-legacy.md).

Con estas tres directivas de acceso condicional, la experiencia de directivas de inicio de sesión de Okta original se ha replicado en Azure AD. Los siguientes pasos conllevan la inscripción del usuario mediante autenticación multifactor de Azure y las pruebas de las directivas.

## <a name="enroll-pilot-members-in-azure-ad-multi-factor-authentication"></a>Inscripción de miembros piloto en la autenticación multifactor de Azure AD

Después de configurar las directivas de acceso condicional, los usuarios deben registrarse en los métodos de autenticación multifactor de Azure. Puede ser necesario que los usuarios se registren a través de varios métodos diferentes.

1. Para el registro individual, dirija a los usuarios al [panel de inicio de sesión de Microsoft](https://aka.ms/mfasetup) para que escriban manualmente la información de registro.

1. Los usuarios pueden ir a la [página de información de seguridad de Microsoft](https://aka.ms/mysecurityinfo) para escribir información o administrar el formulario de registro de MFA.

Consulte [esta guía](../authentication/howto-registration-mfa-sspr-combined.md) para comprender completamente el proceso de registro de MFA.  

Vaya al [panel de inicio de sesión de Microsoft](https://aka.ms/mfasetup). Después de iniciar sesión con MFA de Okta, se le pide que se registre en MFA con Azure AD.

>[!NOTE]
>Si el registro de ese usuario ya se ha realizado en el pasado, se le lleva la página de información **Mi seguridad** una vez que se ha satisfecho la solicitud de MFA.
Vea la [documentación del usuario para la inscripción en MFA](../user-help/security-info-setup-signin.md).

## <a name="enable-conditional-access-policies"></a>Habilitar directivas de acceso condicional

1. Para poner en marcha las pruebas, cambie las directivas creadas en los ejemplos anteriores a **Enabled test user login** (Inicio de sesión de usuario de prueba habilitado). 

   ![Captura de pantalla que muestra la habilitación de un usuario de prueba.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enable-test-user.png)

1. En el siguiente panel de **inicio de sesión** de Office 365 se pide al usuario de prueba John Smith que inicie sesión con MFA de Okta y autenticación multifactor de Azure AD.

   ![Captura de pantalla que muestra el panel de inicio de sesión de Azure.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-in-through-okta.png)

1. Complete la comprobación de MFA mediante Okta.

   ![Captura de pantalla que muestra la comprobación de MFA mediante Okta.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta.png)

1. Una vez que el usuario ha satisfecho la solicitud de MFA de Okta, se le pide el acceso condicional. Asegúrese de que las directivas se han configurado correctamente y cumplen las condiciones para que MFA las desencadene.

   ![Captura de pantalla que muestra la comprobación de MFA mediante Okta en la que se solicita acceso condicional.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta-prompted-ca.png)

## <a name="cut-over-from-sign-on-to-conditional-access-policies"></a>Migración total del inicio de sesión a las directivas de acceso condicional

Después de realizar pruebas exhaustivas en los miembros piloto para asegurarse de que el acceso condicional está en vigor según lo previsto, los miembros restantes de la organización se pueden agregar a las directivas de acceso condicional una vez completado el registro.

Para evitar la solicitud doble entre la autenticación multifactor de Azure y la MFA de Okta, anule la MFA de Okta mediante la modificación de las directivas de inicio de sesión.

El paso final de la migración al acceso condicional se puede realizar por etapas o como una migración total.

1. Vaya a la consola de administración de Okta, seleccione **Seguridad** > **Autenticación** y luego vaya a **Directiva de inicio de sesión**.

   >[!NOTE]
   > Establezca las directivas globales en **inactivas** solo si todas las aplicaciones de Okta están protegidas por sus propias directivas de inicio de sesión de aplicación.
1. Establezca la directiva **Aplicar MFA** en **Inactiva**. También puede asignar la directiva a un nuevo grupo que no incluya usuarios de Azure AD.

   ![Captura de pantalla que muestra la directiva de inicio de sesión de MFA global como inactiva.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-policy-inactive.png)

1. En el panel de la directiva de inicio de sesión de nivel de aplicación, actualice las directivas a **inactivas**, para ello, seleccione la opción **Deshabilitar regla**. También puede asignar la directiva a un nuevo grupo que no incluya usuarios de Azure AD.

1. Asegúrese de que hay al menos una directiva de inicio de sesión de nivel de aplicación habilitada para la aplicación que permite el acceso sin MFA.

   ![Captura de pantalla que muestra el acceso a la aplicación sin MFA.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/application-access-without-mfa.png)

1. Después de deshabilitar las directivas de inicio de sesión de Okta o de excluir a los usuarios de Azure AD migrados de los grupos de cumplimiento, *solo* se solicita a los usuarios acceso condicional la siguiente vez que inician sesión.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la migración de Okta a Azure AD, vea:

- [Migración de aplicaciones desde Okta a Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
- [Migración de la federación de Okta a Azure AD](migrate-okta-federation-to-azure-active-directory.md)
- [Migración del aprovisionamiento de la sincronización de Okta a la sincronización basada en Azure AD Connect](migrate-okta-sync-provisioning-to-azure-active-directory.md)
