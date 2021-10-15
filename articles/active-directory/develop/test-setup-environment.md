---
title: Configuración de un entorno de prueba para la aplicación
titleSuffix: Microsoft identity platform
description: Aprenda a configurar un entorno de prueba de Azure Active Directory para que pueda probar la aplicación integrada con la Plataforma de identidad de Microsoft.  Evalúe si necesita un inquilino independiente para las pruebas o si puede usar el inquilino de producción.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2021
ms.author: arcrowe
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: e695a7028432b1044c0c1896447d474b2716097f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367623"
---
# <a name="set-up-your-applications-azure-ad-test-environment"></a>Configuración del entorno de prueba de Azure AD en la aplicación

Como desarrollador, usa el ciclo de vida de desarrollo de software y mueve la aplicación entre entornos de desarrollo, prueba y producción.  Al usar este proceso para las aplicaciones protegidas por la Plataforma de identidad de Microsoft, debe configurar un entorno de Azure Active Directory (Azure AD) que se usará para las pruebas.  Este entorno se puede usar en las primeras fases de prueba del ciclo de vida de desarrollo, así como en un entorno de prueba permanente a largo plazo.  En función de los requisitos de aislamiento de recursos, puede usar el inquilino de producción de Azure AD de su organización o un inquilino completamente independiente para las pruebas.  

En este artículo, aprenderá a configurar un entorno de prueba de Azure AD para que pueda probar la aplicación integrada con la Plataforma de identidad de Microsoft.  Evalúe el nivel de aislamiento requerido y si necesita un inquilino independiente para las pruebas o si puede usar el inquilino de producción.

## <a name="decide-the-level-of-isolation-needed"></a>Determinación del nivel de aislamiento necesario
En general, es más sencillo y plantea menos sobrecarga usar el inquilino de producción como un entorno de prueba. Sin embargo, esta es solo una opción viable si puede lograr el nivel adecuado de aislamiento entre los recursos de prueba y producción.  El aislamiento es especialmente importante para escenarios con privilegios elevados.

Configure el entorno de prueba en un inquilino independiente (no en el inquilino de producción de la organización) si:
- Tiene un conjunto de recursos que requiere una configuración única para todo el inquilino. Por ejemplo, es posible que la aplicación necesite acceder a los recursos del inquilino por sí misma y no en nombre de un usuario (usa permisos de solo aplicación).  El acceso de solo aplicación requiere el consentimiento del administrador que se aplica a todo el inquilino y esos permisos son difíciles de delimitar de forma segura dentro de un límite de inquilino.
- Tiene una tolerancia a riesgos mínima para el acceso no autorizado por parte de los miembros del inquilino a los recursos de prueba.
- Los cambios de configuración podrían tener un impacto crítico en el entorno de producción.
- No puede crear usuarios de prueba ni datos de prueba asociados en el inquilino.
- Tiene previsto realizar inicios de sesión automatizados en la aplicación para pruebas y el inquilino de producción ha configurado directivas de métodos de autenticación que requieren cierta interacción del usuario.  Por ejemplo, si se requiere la autenticación multifactor para todos los usuarios, no podrá realizar inicios de sesión automatizados para las pruebas de integración.
- Debe asegurarse de que los administradores globales no pueden administrar recursos de prueba específicos ni acceder a ellos. Deberá aislar ese recurso en un inquilino independiente con administradores globales independientes.
- Agregar cargas de trabajo o recursos que no son de producción al inquilino de producción [superaría los límites de servicio o las limitaciones](test-throttle-service-limits.md) para dicho inquilino.

Si no se aplica ninguna de estas condiciones, siga estos pasos para [configurar el entorno de prueba en el inquilino de producción](#set-up-a-test-environment-in-your-production-tenant).  Sin embargo, si alguna de ellas se aplica, debe [configurar un entorno de prueba en un inquilino independiente](#set-up-a-test-environment-in-a-separate-tenant).

## <a name="set-up-a-test-environment-in-a-separate-tenant"></a>Configuración de un entorno de prueba en un inquilino independiente
Si no puede restringir de forma segura la aplicación de prueba en el inquilino de producción, puede crear un inquilino independiente con fines de desarrollo y pruebas. 

### <a name="get-a-test-tenant"></a>Obtención de un inquilino de prueba
Si aún no tiene un inquilino de prueba dedicado, puede crear uno de forma gratuita mediante el Programa de desarrolladores de Microsoft 365 o crear uno manualmente. 

#### <a name="join-the-microsoft-365-developer-program-recommended"></a>Participación en el Programa de desarrolladores de Microsoft 365 (recomendado) 
El [Programa de desarrolladores de Microsoft 365](https://developer.microsoft.com/microsoft-365/dev-program) es gratuito y puede tener cuentas de usuario de prueba y paquetes de datos de ejemplo agregados automáticamente al inquilino.
1. Haga clic en el botón **Unirse ahora** en la pantalla.
2. Inicie sesión con una nueva cuenta Microsoft o use una cuenta (profesional) existente que ya tenga.
3. En la página de registro, seleccione su región, escriba un nombre de empresa y acepte los términos y condiciones del programa antes de hacer clic en **Siguiente**.
4. Haga clic en **Configurar plan**. Especifique la región en la que quiere crear el nuevo inquilino, cree un nombre de usuario y un dominio, y escriba una contraseña. Así creará un nuevo inquilino y el primer administrador del inquilino.
5. Escriba la información de seguridad, que es necesaria para proteger la cuenta de administrador de su nuevo inquilino. Esto configurará la autenticación multifactor para la cuenta.

#### <a name="manually-create-a-tenant"></a>Creación manual de un inquilino  
Puede [crear manualmente un inquilino](quickstart-create-new-tenant.md), que estará vacío tras la creación y tendrá que configurarse con datos de prueba.

### <a name="populate-your-tenant-with-users"></a>Relleno del inquilino con usuarios  
Para mayor comodidad, puede invitarse a usted mismo y a otros miembros del equipo de desarrollo a ser usuarios invitados en el inquilino.  Esto creará objetos invitados independientes en el inquilino de prueba, pero significa que solo tiene que administrar un conjunto de credenciales para la cuenta corporativa y la cuenta de prueba.
1. En [Azure Portal](https://portal.azure.com), haga clic en **Azure Active Directory**.
2. Vaya a **Usuarios**.
3. Haga clic en **Nuevo usuario invitado** e invite a la dirección de correo electrónico de su cuenta profesional.
4. Repita este procedimiento para otros miembros del equipo de desarrollo o de pruebas para la aplicación.

También puede crear usuarios de prueba en el inquilino de prueba.  Si usó uno de los paquetes de ejemplo de Microsoft 365, es posible que ya tenga algunos usuarios de prueba en el inquilino.  Si no es así, debería poder crear algunos usted mismo como administrador de inquilinos.
1. En [Azure Portal](https://portal.azure.com), haga clic en **Azure Active Directory**.
2. Vaya a **Usuarios**.
3. Haga clic en **Usuario nuevo** y cree algunos usuarios de prueba en el directorio.

### <a name="get-an-azure-ad-subscription-optional"></a>Obtención de una suscripción de Azure AD (opcional)
Si quiere probar todas las características de Azure AD Premium en la aplicación, deberá registrar el inquilino para una [licencia Premium P1 o Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).

Si se suscribió usando el Programa de desarrolladores de Microsoft 365, el inquilino de prueba incluirá las licencias P2 de Azure AD.  Si no es así, todavía puede habilitar una [evaluación gratuita de Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/) de un mes.

### <a name="create-and-configure-an-app-registration"></a>Creación y configuración del registro de una aplicación
Deberá crear un registro de aplicación para usarlo en el entorno de prueba.  Debe ser un registro independiente del registro final de la aplicación de producción, para mantener el aislamiento de seguridad entre el entorno de prueba y el de producción.  La configuración de la aplicación depende del tipo de aplicación que se está creando.  Para más información, consulte los pasos de registro de aplicaciones para el escenario de su aplicación en el panel de navegación izquierdo, como este artículo sobre el [registro de aplicaciones web](scenario-web-app-sign-user-app-registration.md).
   
### <a name="populate-your-tenant-with-policies"></a>Relleno del inquilino con directivas
Si la aplicación la usará una única organización principalmente (normalmente denominada inquilino único) y tiene acceso a ese inquilino de producción, debe intentar replicar la configuración del inquilino de producción que puede afectar al comportamiento de la aplicación.  Esto reducirá las posibilidades de errores inesperados al trabajar en producción.

#### <a name="conditional-access-policies"></a>Directivas de acceso condicional 
La replicación de directivas de acceso condicional garantiza que no se produzca ningún acceso bloqueado inesperado al pasar a producción y que la aplicación pueda controlar adecuadamente los errores que probablemente reciba.

Es posible que un administrador de la empresa deba visualizar las directivas de acceso condicional del inquilino de producción.  
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con su cuenta del inquilino de producción.
1. Vaya a **Azure Active Directory** > **Aplicaciones empresariales** > **Acceso condicional**.
1. Consulte la lista de directivas del inquilino.  Haga clic en la primera.
1. Vaya a **Aplicaciones en la nube o acciones**.  
1. Si la directiva solo se aplica a un grupo seleccionado de aplicaciones, pase a la siguiente directiva.  Si no es así, es probable que también se aplique a la aplicación cuando pase a producción.  Debe copiar la directiva en el inquilino de prueba.

En una nueva pestaña o sesión del explorador, vaya a [Azure Portal](https://portal.azure.com) e inicie sesión en el inquilino de prueba.
1. Vaya a **Azure Active Directory** > **Aplicaciones empresariales** > **Acceso condicional**.
1. Haga clic en **Nueva directiva**.
1. Copie la configuración de la directiva del inquilino de producción, identificada mediante los pasos anteriores.

#### <a name="permission-grant-policies"></a>Directivas de concesión de permisos
La replicación de directivas de concesión de permisos garantiza que no se produzca ninguna solicitud inesperada de consentimiento del administrador al pasar a producción. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con su cuenta del inquilino de producción.
1. Haga clic en **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales**. 
1. En el inquilino de producción, vaya a **Azure Active Directory** > **Aplicaciones empresariales** > **Consentimiento y permisos** > **Configuración del consentimiento de los usuarios**.  Copie la configuración en el inquilino de prueba.  
    
#### <a name="token-lifetime-policies"></a>Directivas de vigencia de tokens
La replicación de directivas de vigencia de tokens garantiza que los tokens emitidos para la aplicación no expiren en producción de forma inesperada.  
 
Actualmente, las directivas de vigencia de tokens solo se pueden administrar mediante PowerShell. Obtenga información sobre las [vigencias de tokens configurables](active-directory-configurable-token-lifetimes.md) para aprender a identificar las directivas de vigencia de tokens que se aplican a toda la organización de producción.  Copie esas directivas en el inquilino de prueba.
 
## <a name="set-up-a-test-environment-in-your-production-tenant"></a>Configuración de un entorno de prueba en el inquilino de producción
Si puede restringir de forma segura la aplicación de prueba en el inquilino de producción, continúe y configure el inquilino con fines de prueba.

### <a name="create-and-configure-an-app-registration"></a>Creación y configuración del registro de una aplicación
Deberá crear un registro de aplicación para usarlo en el entorno de prueba.  Debe ser un registro independiente del registro final de la aplicación de producción, para mantener el aislamiento de seguridad entre el entorno de prueba y el de producción.  La configuración de la aplicación depende del tipo de aplicación que se está creando.  Para más información, consulte los [pasos de registro de aplicaciones para el escenario de su aplicación](scenario-web-app-sign-user-app-registration.md) en el panel de navegación izquierdo.

### <a name="create-some-test-users"></a>Creación de algunos usuarios de prueba
Deberá crear algunos usuarios de prueba con datos de prueba asociados para usarlos durante las pruebas de los escenarios.  Es posible que un administrador deba realizar este paso.
1. En [Azure Portal](https://portal.azure.com), haga clic en **Azure Active Directory**.
2. Vaya a **Usuarios**.
3. Haga clic en **Usuario nuevo** y cree algunos usuarios de prueba en el directorio.

### <a name="add-the-test-users-to-a-group-optional"></a>Incorporación de los usuarios de prueba a un grupo (opcional)
Para mayor comodidad, puede asignar todos estos usuarios a un grupo, lo que facilita otras operaciones de asignación.  
1. En [Azure Portal](https://portal.azure.com), haga clic en **Azure Active Directory**.
2. Vaya a **Grupos**.
3. Haga clic en **Nuevo grupo**.
4. Seleccione **Seguridad** o **Microsoft 365** para el tipo de grupo.
5. Asigne un nombre al grupo.
6. Agregue los usuarios de prueba creados en el paso anterior.

### <a name="restrict-your-test-application-to-specific-users"></a>Restricción de la aplicación de prueba a usuarios específicos
Puede restringir los usuarios del inquilino que pueden usar la aplicación de prueba a usuarios o grupos específicos, mediante la asignación de usuarios.  Cuando [creó una aplicación mediante Registros de aplicaciones](#create-and-configure-an-app-registration), también se creó una representación de la aplicación en **Aplicaciones empresariales**.  Use la opción **Aplicaciones empresariales** para restringir quién puede usar la aplicación en el inquilino.

> [!IMPORTANT]
> Si se trata de una [aplicación multiinquilino](v2-supported-account-types.md), esta operación no impedirá que los usuarios de otros inquilinos inicien sesión y utilicen la aplicación.  Solo restringirá a los usuarios del inquilino en el que está configurada la asignación de usuarios. 

Para obtener instrucciones detalladas sobre cómo restringir una aplicación a usuarios específicos de un inquilino, vaya a [Restricción de la aplicación a un conjunto de usuarios](howto-restrict-your-app-to-a-set-of-users.md).

## <a name="next-steps"></a>Pasos siguientes
 
Obtenga información sobre las [limitaciones y los límites de servicio](test-throttle-service-limits.md) que podría alcanzar al configurar un entorno de prueba.

Para obtener información más detallada sobre los entornos de prueba, vea [Protección de entornos de Azure con Azure Active Directory](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/).
  
