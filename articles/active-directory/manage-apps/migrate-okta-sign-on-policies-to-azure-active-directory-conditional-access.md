---
title: Tutorial para migrar directivas de inicio de sesión de Okta al acceso condicional de Azure Active Directory
titleSuffix: Active Directory
description: Aprenda a migrar directivas de inicio de sesión de Okta al acceso condicional de Azure Active Directory
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: f8967d0674689328bf0f761a5f39c17c505d1bd6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440256"
---
# <a name="tutorial-migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access"></a>Tutorial: Migrar directivas de inicio de sesión de Okta al acceso condicional de Azure Active Directory

En este tutorial, aprenderá cómo las organizaciones pueden migrar de directivas de inicio de sesión globales o de nivel de aplicación en Okta a directivas de acceso condicional (CA) de Azure Active Directory (AD) para proteger el acceso de los usuarios en Azure AD y las aplicaciones conectadas.

En este tutorial se supone que tiene un inquilino de Office 365 federado en Okta para el inicio de sesión y la autenticación multifactor (MFA). También debe tener configurados los agentes de aprovisionamiento del servidor o la nube de Azure AD Connect para el aprovisionamiento de usuarios en Azure AD.

## <a name="prerequisites"></a>Prerrequisitos

Al cambiar del inicio de sesión de Okta al CA de Azure AD, es importante comprender los requisitos de licencia. El CA de Azure AD requiere que los usuarios tengan una licencia de Azure AD Premium P1 asignada antes del registro para la autenticación multifactor de Azure AD.

Antes de realizar cualquiera de los pasos para la unión a Azure AD híbrida, necesitará una credencial de administrador de empresa en el bosque local para configurar el registro de punto de conexión de servicio (SCP).

## <a name="step-1---catalog-current-okta-sign-on-policies"></a>Paso 1: Catalogar las directivas de inicio de sesión de Okta actuales

Para completar una transición correcta al CA, se deben evaluar las directivas de inicio de sesión de Okta existentes para determinar los casos de uso y los requisitos que se van a pasar a Azure AD.

1. Compruebe las directivas de inicio de sesión globales; para ello, vaya a **Seguridad**, seleccione **Autenticación** y, a continuación, **Iniciar sesión**.

   ![Imagen que muestra las directivas de inicio de sesión global](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies.png)

   En este ejemplo, nuestra directiva de inicio de sesión global está aplicando MFA en todas las sesiones fuera de nuestras zonas de red configuradas.

   ![Imagen que muestra las directivas de inicio de sesión global con MFA aplicado](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies-enforce-mfa.png)

2. A continuación, vaya a **Aplicaciones** y compruebe las directivas de inicio de sesión en el nivel de aplicación. Seleccione **Aplicaciones** en el submenú y a continuación seleccione su instancia conectada de Office 365 en la **lista Aplicaciones activas**.

3. Finalmente, seleccione **Iniciar sesión** y desplácese a la parte inferior de la página.

En el siguiente ejemplo, la directiva de Office 365 de inicio de sesión de la aplicación tiene cuatro reglas independientes.

- **Exigir MFA para sesiones móviles**: requiere MFA para todas las autenticaciones modernas o sesiones de explorador en iOS o Android.

- **Permitir dispositivos Windows de confianza**: impide que se soliciten factores o comprobaciones adicionales para los dispositivos Okta de confianza.

- **Requerir MFA desde dispositivos de Windows de confianza**: requiere MFA desde cada autenticación moderna o sesión de explorador en dispositivos Windows que no son de confianza.

- **Bloquear la autenticación heredada**: Impide que los clientes de autenticación heredados se conecten al servicio.

  ![Imagen que muestra las reglas de inicio de sesión de Office 365](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-on-rules.png)

## <a name="step-2---configure-condition-pre-requisites"></a>Paso 2: Configurar requisitos previos de condición

Las directivas de CA de Azure AD se pueden configurar para que coincidan con las condiciones de Okta para la mayoría de los escenarios sin configuración adicional.

En algunos escenarios, es posible que necesite una configuración adicional antes de configurar las directivas de CA. Los dos escenarios conocidos en el momento de redacción de este artículo son:

- **Ubicación de red de Okta a ubicaciones con nombre en Azure AD**: siga[este artículo](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition#named-locations) para configurar ubicaciones con nombre en Azure AD.

- **Confianza del dispositivo de Okta para CA basada en dispositivos**: CA ofrece dos opciones posibles al evaluar el dispositivo de un usuario.

  - [Unión a Azure AD híbrido](#hybrid-azure-ad-join-configuration): característica habilitada en el servidor de Azure AD Connect que sincroniza dispositivos actuales de Windows, como Windows 10, Server 2016 y 2019, con Azure AD.

  - [Inscriba el dispositivo en Microsoft Endpoint Manager](#configure-device-compliance) y asigne una directiva de cumplimiento.

### <a name="hybrid-azure-ad-join-configuration"></a>Configuración de la unión a Azure AD híbrido

La habilitación de la unión a Azure AD híbrida se puede realizar en el servidor de Azure AD Connect mediante la ejecución del asistente para configuración. Después de la configuración, se deben seguir varios pasos para inscribir automáticamente los dispositivos.

>[!NOTE]
>La unión a Azure AD híbrido no es compatible con los agentes de aprovisionamiento en la nube de Azure AD Connect.

1. Siga estas [instrucciones](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains#configure-hybrid-azure-ad-join) para habilitar la unión a Azure AD híbrido.

2. En la página de configuración de SCP, seleccione la lista desplegable **Servicio de autenticación**. Elija la dirección URL del proveedor de federación de Okta seguida de **Agregar**. Escriba sus credenciales de administrador de empresa local y, a continuación, seleccione **Siguiente**.

   ![Imagen que muestra la configuración de SCP](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/scp-configuration.png)

3. Si ha bloqueado la autenticación heredada en los clientes de Windows en la directiva de inicio de sesión global o de nivel de aplicación, cree una regla para permitir que finalice el proceso de unión a Azure AD híbrido.

4. Puede permitir toda la pila de autenticación heredada para todos los clientes de Windows o ponerse en contacto con el soporte técnico de Okta para habilitar su cadena de cliente personalizada en las directivas de aplicación existentes.

### <a name="configure-device-compliance"></a>Configurar la compatibilidad del dispositivo

Aunque la unión a Azure AD híbrido es un reemplazo directo de la confianza del dispositivo de Okta en Windows, las directivas de CA también pueden ver el cumplimiento de los dispositivos totalmente inscritos en Microsoft Endpoint Manager.

- **Información general del cumplimiento**: consulte las [directivas de cumplimiento de dispositivos en Microsoft Intune](https://docs.microsoft.com/mem/intune/protect/device-compliance-get-started#:~:text=Reference%20for%20non-compliance%20and%20Conditional%20Access%20on%20the,applicable%20%20...%20%203%20more%20rows).

- **Directivas de cumplimiento**: cree [directivas en Microsoft Intune](https://docs.microsoft.com/mem/intune/protect/create-compliance-policy).

- **Inscripción de Windows**: si ha optado por implementar la unión a Azure AD híbrida, se puede implementar una directiva de grupo adicional para completar el [proceso de inscripción automática de estos dispositivos en Microsoft Intune](https://docs.microsoft.com/windows/client-management/mdm/enroll-a-windows-10-device-automatically-using-group-policy).

- **Inscripción de iOS/iPadOS**: antes de inscribir un dispositivo iOS, se deben realizar [configuraciones adicionales](https://docs.microsoft.com/mem/intune/enrollment/ios-enroll) en la Consola de administración de puntos de conexión.

- **Inscripción de Android**: antes de inscribir un dispositivo Android, se deben realizar [configuraciones adicionales](https://docs.microsoft.com/mem/intune/enrollment/android-enroll) en la Consola de administración de puntos de conexión.

## <a name="step-3---configure-azure-ad-multi-factor-authentication-tenant-settings"></a>Paso 3: Configuración del inquilino de autenticación multifactor de Azure AD

Antes de realizar la conversión a CA, confirme la configuración de los inquilinos de autenticación multifactor de Azure AD para la organización.

1. Navegue a [Azure Portal](https://portal.azure.com) e inicie sesión con una cuenta de administrador global.

2. Seleccione **Azure Active Directory**, seguido de **Usuarios** y, a continuación, **Autenticación multifactor**. Esto le llevará al portal heredado de Azure MFA.

   ![imagen que muestra el portal de autenticación multifactor heredado de Azure AD](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/legacy-azure-ad-portal.png)

En su lugar, puede usar **<https://aka.ms/mfaportal>** .

4. En el menú heredado de **Azure MFA**, cambie el menú de estado en **habilitado** y **aplicado** para confirmar que no tiene usuarios habilitados para la MFA heredada. Si el inquilino tiene usuarios en las siguientes vistas, debe deshabilitarlos en el menú heredado. Solo entonces, las directivas de entidad de certificación tendrán efecto en su cuenta.

   ![Imagen que muestra la deshabilitación de un usuario el portal de autenticación multifactor heredado de Azure AD](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/disable-user-legacy-azure-ad-portal.png)

   El campo **aplicado** también debe estar vacío.

   ![imagen que muestra que el campo aplicado está vacío en el portal de autenticación multifactor de Azure AD](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enforced-empty-legacy-azure-ad-portal.png)

5. Después de confirmar que no hay usuarios configurados para MFA heredado, seleccione la opción **Configuración del servicio**. Cambie la selección de **Contraseñas de la aplicación** para **no permitir a los usuarios crear contraseñas de aplicación para iniciar sesión en aplicaciones que no son de explorador**.

6. Asegúrese de que los cuadros **Omitir autenticación multifactor para solicitudes de usuarios federados en mi intranet** y **Permitir a los usuarios recordar la autenticación multifactor en dispositivos en los que confían (entre uno y 365 días)** están desactivados y, a continuación, seleccione **Guardar**.

   >[!NOTE]
   >Consulte los [procedimientos recomendados para configurar la solicitud de la MFA](https://aka.ms/mfaprompts).

   ![Imagen que muestra los campos sin comprobar en el portal de autenticación multifactor heredada de Azure AD](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/uncheck-fields-legacy-azure-ad-portal.png)

## <a name="step-4---configure-ca-policies"></a>Paso 4: Configuración de directivas de CA

Después de configurar los requisitos previos y establecer la configuración base, es el momento de compilar la primera directiva de CA.

1. Para configurar las directivas de CA en Azure AD, vaya a [Azure Portal](https://portal.azure.com). Seleccione **Vista** en Administración de Azure Active Directory.

2. La configuración de directivas de CA debe tener en cuenta los [procedimientos recomendados para implementar y diseñar CA](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access#understand-conditional-access-policy-components).

3. Para imitar la directiva de MFA de inicio de sesión global de Okta, [cree una directiva](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-all-users-mfa).

4. Cree una [regla de CA basada en la confianza de dispositivos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices).

5. Esta directiva, como cualquier otra de este tutorial, se puede dirigir a una aplicación específica, a un grupo de prueba de usuarios o a ambos.

   ![Imagen que muestra el usuario de prueba](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/test-user.png)

   ![Imagen que muestra el éxito en la prueba del usuario](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/success-test-user.png)

6. Después de configurar la directiva basada en ubicación y la directiva de confianza del dispositivo, es el momento de configurar la directiva equivalente de [**Bloqueo de la autenticación heredada**](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-block-legacy).

Con estas tres directivas de CA, la experiencia de directivas de inicio de sesión de Okta original se ha replicado en Azure AD. Los siguientes pasos implican la inscripción del usuario en Azure MFA y la prueba de las directivas.

## <a name="step-5---enroll-pilot-members-in-azure-ad-multi-factor-authentication"></a>Paso 5: Inscribir miembros piloto en la autenticación multifactor de Azure AD

Una vez configuradas las directivas de CA, los usuarios tendrán que registrarse para los métodos de Azure MFA. Puede ser necesario que los usuarios se registren a través de varios métodos diferentes.

1. Para el registro individual, puede dirigir a los usuarios a <https://aka.ms/mfasetup> para que escriban manualmente la información de registro.

2. El usuario puede ir a <https://aka.ms/mysecurityinfo> para especificar información o administrar el formulario de registro de MFA.

Consulte [esta guía](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined) para comprender completamente el proceso de registro de MFA.  

Vaya a <https://aka.ms/mfasetup> después de iniciar sesión con Okta MFA y se le indicará que se registre para MFA en Azure AD.

>[!NOTE]
>Si el registro ya se ha producido en el pasado para ese usuario, se le mostrará en la página de información **Mi seguridad** después de satisfacer la solicitud de MFA.

Consulte la [documentación del usuario final para la inscripción en MFA](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-signin).

## <a name="step-6---enable-ca-policies"></a>Paso 6: Habilitar directivas de CA

1. Para poner en marcha las pruebas, cambie las directivas creadas en los ejemplos anteriores a **Enabled test user login** (Inicio de sesión de usuario de prueba habilitado). 

   ![Imagen que muestra la habilitación del usuario de prueba](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enable-test-user.png)

2. En el siguiente inicio de sesión en Office 365, se pide al usuario de prueba John Smith que inicie sesión con la autenticación multifactor de Okta y Azure AD.

   ![Imagen que muestra el inicio de sesión mediante Okta](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-in-through-okta.png)

3. Complete la comprobación de MFA mediante Okta.

   ![Imagen que muestra la comprobación de autenticación multifactor mediante Okta](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta.png)

4. Una vez que el usuario complete la solicitud de MFA de Okta, se le pedirá la CA. Asegúrese de que las directivas se han configurado correctamente y están dentro de las condiciones que se deben desencadenar para la autenticación multifactor.

   ![Imagen que muestra la verificación por MFA mediante la solicitud de entidad de certificación de Okta](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta-prompted-ca.png)

## <a name="step-7---cutover-from-sign-on-to-ca-policies"></a>Paso 7: Cambio del inicio de sesión a las directivas de CA

Después de realizar pruebas exhaustivas en los miembros piloto para asegurarse de que la CA está en vigor según lo previsto, los miembros de la organización restantes se pueden agregar a las directivas de CA una vez completado el registro.

Para evitar la solicitud doble entre la autenticación multifactor de Azure y la de Okta, debe rechazar la autenticación multifactor de Okta modificando las directivas de inicio de sesión.

El paso final de la migración a la entidad de certificación se puede realizar por etapas o como transferencia.

1. Vaya a la consola de administración de Okta, seleccione **Seguridad**, seguido de **Autenticación** y, a continuación, vaya a la **Directiva de inicio de sesión**.

>[!NOTE]
>Las directivas globales deben establecerse en inactivas solo si todas las aplicaciones de Okta están protegidas por sus propias directivas de inicio de sesión de aplicación.

2. Establezca Aplicar directiva de autenticación multifactor en **Inactivo** o asigne la directiva a un nuevo grupo que no incluya los usuarios Azure AD.

   ![Imagen que muestra la directiva de autenticación multifactor como inactiva](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-policy-inactive.png)

3. En la directiva de inicio de sesión de nivel de aplicación, actualice las directivas a inactivas seleccionando la opción **Deshabilitar regla**. También puede asignar la directiva a un nuevo grupo que no incluya usuarios de Azure AD.

4. Asegúrese de que hay al menos una directiva de inicio de sesión de nivel de aplicación habilitada para la aplicación que permite el acceso sin autenticación multifactor.

   ![Imagen que muestra el acceso a la aplicación sin autenticación multifactor](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/application-access-without-mfa.png)

5. Después de deshabilitar las directivas de inicio de sesión de Okta o excluir los usuarios de Azure AD de los grupos de cumplimiento, **solo** se debe solicitar a los usuarios la entidad de certificación en su próximo inicio de sesión.

## <a name="next-steps"></a>Pasos siguientes

- [Migración de aplicaciones desde Okta a Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)

- [Migración de la federación de Okta a Azure AD](migrate-okta-federation-to-azure-active-directory.md)

- [Migración del aprovisionamiento de sincronización de Okta a la sincronización basada en Azure AD Connect](migrate-okta-sync-provisioning-to-azure-active-directory.md)
