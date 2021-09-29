---
title: 'Resolución de problemas de inicio de sesión con el acceso condicional: Azure Active Directory'
description: En este artículo se describe qué hacer cuando las directivas de acceso condicional producen resultados inesperados
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d53f67c4035d8b97bd3117c5386b371b711a5e7b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606729"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Resolución de problemas de inicio de sesión con el acceso condicional

La información de este artículo se puede usar para solucionar problemas de resultados inesperados de inicio de sesión relacionados con el acceso condicional mediante mensajes de error y el registro de inicios de sesión de Azure AD.

## <a name="select-all-consequences"></a>Selección de "todas" las consecuencias

El marco de trabajo de acceso condicional le proporciona una gran flexibilidad de configuración. Sin embargo, una gran flexibilidad también implica revisar cuidadosamente cada directiva de configuración antes de liberarla para evitar resultados no deseados. En este contexto, debe prestar especial atención a las asignaciones que afectan a conjuntos completos, como **todos los usuarios / grupos / aplicaciones en la nube**.

Las organizaciones deberían evitar las siguientes configuraciones:

**Para todos los usuarios, todas las aplicaciones en la nube:**

- **Bloquear acceso**: esta configuración bloquea toda la organización.
- **Requerir que el dispositivo esté marcado como compatible**: para usuarios que aún no han inscrito sus dispositivos, esta directiva bloquea todo el acceso, incluido el acceso al portal de Intune. Si es un administrador y no tiene un dispositivo inscrito, esta directiva le impide volver a acceder Azure Portal para cambiar la directiva.
- **Requerir dispositivo unido a Azure AD híbrido**: este acceso al bloqueo de directivas también ofrece la posibilidad de bloquear el acceso a todos los usuarios de su organización si no tiene un dispositivo unido a Azure AD híbrido.
- **Requerir la directiva de protección de aplicaciones**: este acceso al bloqueo de directivas también ofrece la posibilidad de bloquear el acceso a todos los usuarios de su organización si no tiene una directiva de Intune. Si es un administrador sin una aplicación cliente que tenga una directiva de protección de aplicaciones de Intune, esta directiva le impedirá acceder a portales como el de Intune o el de Azure.

**Para todos los usuarios, todas las aplicaciones en la nube, todas las plataformas de dispositivos:**

- **Bloquear acceso**: esta configuración bloquea toda la organización.

## <a name="conditional-access-sign-in-interrupt"></a>Interrupción de inicio de sesión del acceso condicional

El primer método consiste en revisar el mensaje de error que aparece. En el caso de los problemas de inicio de sesión al usar un explorador web, la página de error en sí contiene información detallada. Esta información por sí sola puede describir cuál es el problema y sugerir una solución.

![Error de inicio de sesión: se requiere un dispositivo compatible](./media/troubleshoot-conditional-access/image1.png)

En el error anterior, el mensaje indica que solo se puede obtener acceso a la aplicación desde dispositivos o aplicaciones cliente que cumplan la directiva de administración de dispositivos móviles de la empresa. En este caso, la aplicación y el dispositivo no cumplen la directiva.

## <a name="azure-ad-sign-in-events"></a>Eventos de inicio de sesión de Azure AD

El segundo método para obtener información detallada sobre la interrupción del inicio de sesión consiste en revisar los eventos de inicio de sesión de Azure AD para ver qué directivas de acceso condicional se han aplicado y por qué.

Para obtener más información sobre el problema, haga clic en **Más detalles** en la página inicial del error. Al hacer clic en **Más detalles** se mostrará información de resolución de problemas que resulta útil al buscar en los eventos de inicio de sesión de Azure AD el evento de error concreto que ha visto el usuario o al abrir un incidente de soporte técnico con Microsoft.

![Más detalles de un inicio de sesión de explorador web interrumpido de acceso condicional.](./media/troubleshoot-conditional-access/image2.png)

Para averiguar qué directiva o directivas de acceso condicional se han aplicado y por qué, haga lo siguiente.

1. Inicie sesión en **Azure Portal** como administrador global, de seguridad o lector global.
1. Vaya a **Azure Active Directory** > **Inicios de sesión**.
1. Busque el evento del inicio de sesión que se va a revisar. Agregue o quite filtros y columnas para filtrar la información innecesaria.
   1. Agregue filtros para limitar el ámbito:
      1. **Id. de correlación** si tiene un evento específico para investigar.
      1. **Acceso condicional** para ver el mensaje de error y de confirmación de la directiva. Defina el ámbito del filtro para mostrar solo los errores a fin de limitar los resultados.
      1. **Nombre de usuario** para ver información relacionada con usuarios específicos.
      1. **Fecha** con el ámbito definido para el período de tiempo en cuestión.

   ![Selección del filtro de acceso condicional en el registro de inicios de sesión](./media/troubleshoot-conditional-access/image3.png)

1. Una vez que se haya encontrado el evento de inicio de sesión que corresponde al error de inicio de sesión del usuario, seleccione la pestaña **Acceso condicional**. En la pestaña Acceso condicional se mostrará la directiva o directivas específicas que han provocado la interrupción del inicio de sesión.
   1. La información de la pestaña **Solución de problemas y soporte técnico** puede proporcionar un motivo claro de la causa del error de inicio de sesión, como, por ejemplo, un dispositivo que no cumple los requisitos de cumplimiento.
   1. Para seguir investigando, explore en profundidad la configuración de las directivas haciendo clic en el **nombre de la directiva**. Al hacer clic en el **nombre de la directiva** se mostrará la interfaz de usuario de configuración de la directiva seleccionada para revisarla y editarla.
   1. El **usuario de cliente** y los **detalles del dispositivo** usados para la evaluación de la directiva de acceso condicional también están disponibles en las pestañas **Información básica**, **Ubicación**, **Información del dispositivo**, **Detalles de autenticación** y **Detalles adicionales** del evento de inicio de sesión.

### <a name="policy-details"></a>Detalles de la directiva

Al seleccionar los puntos suspensivos situados a la derecha de la directiva en un evento de inicio de sesión, se muestran los detalles de la directiva. Esto ofrece a los administradores información adicional sobre si una directiva se aplicó correctamente o no.

   ![Pestaña Acceso condicional de un evento de inicio de sesión](./media/troubleshoot-conditional-access/image5.png)

   ![Detalles de directiva (versión preliminar)](./media/troubleshoot-conditional-access/policy-details.png)

En el lado izquierdo se proporcionan los detalles recopilados en el inicio de sesión y en el lado derecho se detalla si cumplen los requisitos de las directivas de acceso condicional aplicadas. Las directivas de acceso condicional solo se aplican cuando se cumplen todas las condiciones o no se han configurado.

Si la información del evento no es suficiente para comprender los resultados de inicio de sesión o para ajustar la directiva a fin de obtener los resultados deseados, se puede abrir un incidente de soporte técnico. Vaya a la pestaña **Solución de problemas y soporte técnico** de ese evento de inicio de sesión y seleccione **Crear una solicitud de soporte técnico**.

![Pestaña Solución de problemas y soporte técnico del evento de inicio de sesión](./media/troubleshoot-conditional-access/image6.png)

Al enviar el incidente, proporcione el id. de solicitud y la fecha y hora del evento de inicio de sesión en los detalles del envío del incidente. Esta información permitirá al servicio de soporte técnico de Microsoft encontrar el evento que le preocupa.

### <a name="conditional-access-error-codes"></a>Códigos de error del acceso condicional

| Código de error de inicio de sesión | Cadena de error |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="what-to-do-if-you-are-locked-out-of-the-azure-portal"></a>¿Qué se debe hacer si está bloqueado en Azure Portal?

Si está bloqueado en Azure Portal debido a una configuración incorrecta de una directiva de acceso condicional:

- Compruebe si hay otros administradores en su organización que aún no estén bloqueados. Un administrador con acceso a Azure Portal puede deshabilitar la directiva que está afectando al inicio de sesión. 
- Si ninguno de los administradores de la organización puede actualizar la directiva, envíe una solicitud de soporte técnico. El servicio de soporte técnico de Microsoft puede revisar y, tras la confirmación, actualizar las directivas de acceso condicional que impidan el acceso.

## <a name="next-steps"></a>Pasos siguientes

- [Informes de actividad de inicio de sesión en el portal de Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Solución de problemas de acceso condicional con la herramienta What If](troubleshoot-conditional-access-what-if.md)
