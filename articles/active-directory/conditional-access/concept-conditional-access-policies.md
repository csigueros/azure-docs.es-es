---
title: 'Creación de una directiva de acceso condicional: Azure Active Directory'
description: ¿Cuáles son todas las opciones disponibles para crear una directiva de acceso condicional y qué significan?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 674f4cf80d68095ebd7b134c679b60b45ff2a548
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619351"
---
# <a name="building-a-conditional-access-policy"></a>Creación de una directiva de acceso condicional

Como se explica en el artículo [¿Qué es el acceso condicional?](overview.md), una directiva de acceso condicional es una instrucción if-then, de **asignaciones** y **controles de acceso**. Una directiva de acceso condicional reúne las señales para tomar decisiones y aplicar las directivas de la organización.

¿Cómo una organización crea estas directivas? ¿Qué se necesita? ¿Cómo se aplican?

![Acceso condicional (señales + decisiones + aplicación = directivas)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

Se pueden aplicar varias directivas de acceso condicional a un usuario individual en cualquier momento. En este caso, se tienen que satisfacer todas las directivas que se aplican. Por ejemplo, si una directiva exige la autenticación multifactor (MFA) y otra requiere un dispositivo compatible, tendrá que completar la MFA y usar un dispositivo compatible. A todas las asignaciones se les asigna **la operación lógica AND**. Si tiene más de una asignación configurada, se deben satisfacer todas las asignaciones para desencadenar una directiva.

Todas las directivas se aplican en dos fases:

- Fase 1: Recopilación de detalles de la sesión 
   - Recopile los detalles de la sesión, como la ubicación de red y la identidad del dispositivo, que serán necesarios para la evaluación de la directiva. 
   - La fase 1 de la evaluación de la directiva se produce para todas las directivas habilitadas, así como para las directivas en [modo de solo informe](concept-conditional-access-report-only.md).
- Fase 2: Cumplimiento 
   - Use los detalles de la sesión recopilados en la fase 1 para identificar los requisitos que no se han cumplido. 
   - Si hay una directiva que está configurada para bloquear el acceso, con el control de concesión de bloqueo, la aplicación se detendrá aquí y se bloqueará al usuario. 
   - Se le pedirá al usuario que complete los requisitos de control de concesión adicionales que no se cumplieron durante la fase 1 en el orden siguiente, hasta que se satisfaga la directiva:  
      - Multi-Factor Authentication 
      - Directiva de protección de aplicaciones/aplicaciones cliente aprobada 
      - Dispositivo administrado (combinación de Azure AD compatible o híbrido) 
      - Términos de uso 
      - Controles personalizados  
   - Una vez satisfechos todos los controles de concesión, aplique controles de sesión (aplicación forzada, Microsoft Cloud App Security y duración del token). 
   - La fase 2 de la evaluación de directivas se realiza para todas las directivas habilitadas. 

## <a name="assignments"></a>Assignments

La parte de las asignaciones controla el quién, el qué y el dónde de una directiva de acceso condicional.

### <a name="users-and-groups"></a>Usuarios y grupos

[Usuarios y grupos](concept-conditional-access-users-groups.md) asigna a quién incluirá y a quién excluirá la directiva. Esta asignación puede incluir a todos los usuarios, grupos específicos de usuarios, roles de directorio o usuarios invitados externos. 

### <a name="cloud-apps-or-actions"></a>Aplicaciones o acciones en la nube

Las [aplicaciones o acciones en la nube](concept-conditional-access-cloud-apps.md) puede incluir o excluir las aplicaciones en la nube, las acciones del usuario o los contextos de autenticación que estarán sujetos a la directiva.

### <a name="conditions"></a>Condiciones

Una directiva puede contener varias [condiciones](concept-conditional-access-conditions.md).

#### <a name="sign-in-risk"></a>Riesgo de inicio de sesión

En el caso de las organizaciones con [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md), las detecciones de riesgo generadas pueden influir en las directivas de acceso condicional.

#### <a name="device-platforms"></a>Plataformas de dispositivo

Es posible que las organizaciones con varias plataformas de sistemas operativos de dispositivo quieran aplicar directivas específicas en las distintas plataformas. 

La información que se usa para calcular la plataforma de dispositivo procede de orígenes no comprobados, como cadenas de agente de usuario que se pueden modificar.

#### <a name="locations"></a>Ubicaciones

Los datos de ubicación se proporcionan mediante datos de geolocalización de direcciones IP. Los administradores pueden elegir definir las ubicaciones y elegir marcar algunas como de confianza, como aquellas para las ubicaciones de red de su organización.

#### <a name="client-apps"></a>Aplicaciones cliente

De manera predeterminada, todas las directivas de acceso condicional recién creadas se aplicarán a todos los tipos de aplicaciones cliente, incluso si la condición de las aplicaciones cliente no está configurada.

El comportamiento de la condición de las aplicaciones cliente se actualizó en agosto de 2020. Si ya tiene directivas de acceso condicional, estas permanecerán sin cambios. Sin embargo, si hace clic en una directiva existente, se habrá quitado el botón de alternancia de configuración, y estarán seleccionadas las aplicaciones cliente a las que se aplica la directiva.

#### <a name="device-state"></a>Estado del dispositivo

Este control se usa para excluir dispositivos que están unidos a Azure AD híbrido o que están marcados como compatibles en Intune. Esta exclusión se puede realizar para bloquear dispositivos no administrados. 

#### <a name="filters-for-devices-preview"></a>Filtros para dispositivos (versión preliminar)

Este control permite dirigirse a dispositivos específicos en función de sus atributos en una directiva.

## <a name="access-controls"></a>Controles de acceso

La parte de controles de acceso de la directiva de acceso condicional controla cómo se aplica una directiva.

### <a name="grant"></a>Conceder

[Concesión](concept-conditional-access-grant.md) proporciona a los administradores un medio de aplicación de directivas en el que pueden bloquear o conceder acceso.

#### <a name="block-access"></a>Bloquear acceso

El bloqueo de acceso hace justamente eso, bloqueará el acceso bajo las asignaciones especificadas. El control de bloqueo es eficaz y se debe manejar con el conocimiento adecuado.

#### <a name="grant-access"></a>Conceder acceso

Conceder acceso puede desencadenar la aplicación de uno o más controles. 

- Requerir la autenticación multifactor (Azure AD Multi-Factor Authentication)
- Requerir que el dispositivo esté marcado como compatible (Intune)
- Requerir un dispositivo unido a Azure AD híbrido
- Requerir aplicación cliente aprobada
- Requerir la directiva de protección de aplicaciones
- Requerir cambio de contraseña
- Requerir condiciones de uso

Los administradores pueden elegir si requerir uno de los controles anteriores o todos los controles seleccionados mediante las opciones siguientes. El valor predeterminado para varios controles es requerirlos todos.

- Requerir todos los controles seleccionados (control y control)
- Requerir uno de los controles seleccionados (control o control)

### <a name="session"></a>Sesión

[Controles de sesión](concept-conditional-access-session.md) puede limitar la experiencia. 

- Usar restricciones que exige la aplicación
   - Actualmente solo funciona con Exchange Online y SharePoint Online.
      - Pasa información del dispositivo para permitir el control de la experiencia que concede acceso completo o limitado.
- Utilizar el Control de aplicaciones de acceso condicional
   - Usa señales de Microsoft Cloud App Security para hacer cosas como: 
      - Bloquear las acciones de descargar, cortar, copiar e imprimir documentos confidenciales.
      - Supervisar el comportamiento de sesión de riesgo.
      - Requerir el etiquetado de archivos confidenciales.
- Frecuencia de inicio de sesión
   - Capacidad para cambiar la frecuencia de inicio de sesión predeterminada para la autenticación moderna.
- Sesión del explorador persistente
   - Permite a los usuarios permanecer conectados después de cerrar y volver a abrir la ventana del explorador.

## <a name="simple-policies"></a>Directivas simples

Una directiva de acceso condicional debe contener, como mínimo, lo siguiente para que se aplique:

- El **nombre** de la directiva.
- **Assignments**
   - Los **usuarios o grupos** a los que se les va a aplicar la directiva.
   - Las **aplicaciones o acciones en la nube** a las que se les va a aplicar la directiva.
- **Controles de acceso**
   - Controles para **conceder** o **bloquear** el acceso

![Directiva de acceso condicional en blanco](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

El artículo [Directivas de acceso condicional habituales](concept-conditional-access-policy-common.md) incluye algunas directivas que pensamos que serían útiles para la mayoría de las organizaciones.

## <a name="next-steps"></a>Pasos siguientes

[Creación de una directiva de acceso condicional](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json#create-a-conditional-access-policy)

[Simulación del comportamiento de inicio de sesión mediante la herramienta What If de acceso condicional](troubleshoot-conditional-access-what-if.md)

[Planeamiento de una implementación de Azure AD Multi-Factor Authentication basada en la nube](../authentication/howto-mfa-getstarted.md)

[Administración del cumplimiento del dispositivo con Intune](/intune/device-compliance-get-started)

[Microsoft Cloud App Security y acceso condicional](/cloud-app-security/proxy-intro-aad)
