---
title: Investigación de riesgos en Azure Active Directory Identity Protection
description: Aprenda a investigar los usuarios, las detecciones y los inicios de sesión de riesgo en Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5caf348b1865b47521ec9df776fd08cc36db47fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739976"
---
# <a name="how-to-investigate-risk"></a>Instrucciones: Investigación de riesgos

Identity Protection proporciona a las organizaciones tres informes que pueden usar para investigar los riesgos de identidad en su entorno. Estos informes son sobre los **usuarios de riesgo**, los **inicios de sesión de riesgo** y las **detecciones de riesgo**. La investigación de eventos es clave para comprender e identificar mejor los puntos débiles de la estrategia de seguridad.

Los tres informes permiten descargar eventos en formato .CSV para analizarlos posteriormente, fuera de Azure Portal. Los informes sobre usuarios e inicios de sesión de riesgo permiten descargar las 2500 entradas más recientes, mientras que el informe sobre detecciones de riesgo permite descargar los 5000 registros más recientes.

Las organizaciones pueden beneficiarse de las integraciones de Microsoft Graph API para agregar datos con otros orígenes a los que pueden tener acceso como organización.

Los tres informes se encuentran en **Azure Portal** > **Azure Active Directory** > **Seguridad**.

## <a name="navigating-the-reports"></a>Navegación por los informes

Cada informe se inicia con una lista de todas las detecciones correspondientes al período mostrado en la parte superior del informe. Cada informe permite agregar o quitar columnas en función de la preferencia del administrador. Los administradores pueden elegir descargar los datos en formato CSV o JSON. Los informes se pueden filtrar con los filtros de la parte superior del informe.

La selección de entradas individuales puede habilitar entradas adicionales en la parte superior del informe, como la capacidad de confirmar que un inicio de sesión es de riesgo o seguro, confirmar que un usuario está en peligro o descartar el riesgo del usuario.

La selección de entradas individuales expande una ventana de detalles debajo de las detecciones. La vista de detalles permite a los administradores investigar y realizar acciones en cada detección. 

![Ejemplo de informe de Identity Protection que muestra inicios de sesión de riesgo y los detalles](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Usuarios de riesgo

Con la información que proporciona el informe de usuarios de riesgo, los administradores pueden buscar lo siguiente:

- ¿Qué usuarios están en riesgo, lo han corregido o lo han descartado?
- Detalles sobre las detecciones
- Historial de todos los inicios de sesión de riesgo
- Historial de riesgos
 
A continuación, los administradores pueden elegir tomar medidas en estos eventos. Los administradores pueden optar por:

- Restablecer la contraseña del usuario
- Confirmar el peligro del usuario
- Descartar el riesgo del usuario
- Bloquear el inicio de sesión del usuario
- Investigar con más detalle con Azure ATP

## <a name="risky-sign-ins"></a>Inicios de sesión no seguros

El informe de inicios de sesión de riesgo contiene datos que se pueden filtrar hasta los últimos 30 días (1 mes).

Con la información que proporciona el informe de inicios de sesión de riesgo, los administradores pueden buscar lo siguiente:

- Qué inicios de sesión están clasificados como de riesgo, confirmados en peligro, confirmados seguros, descartados o corregidos.
- Niveles de riesgo agregado y en tiempo real asociado con los intentos de inicio de sesión.
- Tipos de detección desencadenados.
- Directivas de acceso condicionales aplicadas.
- Detalles de MFA.
- Información del dispositivo
- Información de la aplicación
- Información de la ubicación.

A continuación, los administradores pueden elegir tomar medidas en estos eventos. Los administradores pueden optar por:

- Confirmar el peligro del inicio de sesión
- Confirmar la seguridad del inicio de sesión

> [!NOTE] 
> Identity Protection evalúa el riesgo de todos los flujos de autenticación, si es interactivo o no interactivo. Sin embargo, el informe de inicio de sesión muestra solo los inicios de sesión interactivos. Puede ver inicios de sesión peligrosos que se produjeron en inicios de sesión no interactivos, pero el inicio de sesión no se mostrará en el informe de inicios de sesión de Azure AD.

## <a name="risk-detections"></a>Detecciones de riesgo

El informe de detecciones de riesgo contiene datos que se pueden filtrar hasta los últimos 90 días (3 mes).

Con la información que proporciona el informe de detecciones de riesgo, los administradores pueden buscar lo siguiente:

- Información sobre cada detección de riesgo, incluido el tipo.
- Otros riesgos desencadenados al mismo tiempo.
- Ubicación del intento de inicio de sesión.
- Vinculación para obtener más detalles de Microsoft Cloud App Security (MCAS).

Los administradores pueden elegir volver al informe de riesgo del usuario o de inicios de sesión para realizar acciones en función de la información recopilada.

> [!NOTE] 
> Nuestro sistema puede detectar que el evento de riesgo que ha contribuido a la puntuación de riesgo del usuario de riesgo era un falso positivo o que se ha corregido el riesgo del usuario con la aplicación de directivas, como al completar una solicitud de MFA o un cambio de contraseña seguro. Por lo tanto, el sistema descartará el estado de riesgo y se mostrará un detalle de riesgo de "La IA confirmó que el inicio de sesión es seguro" y dejará de contribuir al riesgo del usuario. 


## <a name="next-steps"></a>Pasos siguientes

- [Directivas disponibles para mitigar los riesgos](concept-identity-protection-policies.md)

- [Habilitación de las directivas de riesgo de inicios de sesión y de usuarios](howto-identity-protection-configure-risk-policies.md)
