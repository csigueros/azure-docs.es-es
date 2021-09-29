---
title: 'Administración de trabajadores de primera línea: Azure Active Directory'
description: Obtenga información sobre las capacidades de administración de trabajadores de primera línea que se proporcionan por medio del portal Mi personal.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 26d7e967ceb0208d6e852db9946ac0c260751dd3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732575"
---
# <a name="frontline-worker-management"></a>Administración de trabajadores de primera línea

Los trabajadores de primera línea suponen más del 80 % de la mano de obra global. A pesar de ello, debido a la gran escala, la rápida rotación y la fragmentación de los procesos, los trabajadores de primera línea no suelen disponer de las herramientas necesarias para hacer que sus exigentes trabajos resulten un poco más sencillos. La administración de trabajadores de primera línea lleva la transformación digital a toda la mano de obra de primera línea. La mano de obra puede incluir directivos, trabajadores de primera línea, operaciones y TI.

La administración de trabajadores de primera línea capacita a esta mano de obra al hacer que las siguientes actividades resulten más fáciles de realizar:
- Simplificación de tareas comunes de TI con Mi personal
- Incorporación sencilla de trabajadores de primera línea por medio de una autenticación simplificada
- Aprovisionamiento sin problemas de dispositivos compartidos y cierre de sesión seguro de los trabajadores de primera línea

## <a name="delegated-user-management-through-my-staff"></a>Administración de usuarios delegada por medio de Mi personal

Azure Active Directory (Azure AD) ofrece la posibilidad de delegar la administración de usuarios a los administradores de primera línea por medio del [portal Mi personal](../roles/my-staff-configure.md), lo que ayuda a ahorrar un valioso tiempo y a reducir los riesgos. Al habilitar los restablecimientos de contraseña y la administración telefónica simplificados directamente desde la tienda o la fábrica, los administradores pueden conceder acceso a los empleados sin necesidad de enrutar la solicitud a través del departamento de soporte técnico, TI u operaciones.

![Administración de usuarios delegada en el portal Mi personal](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>Incorporación acelerada con autenticación simplificada

Mi personal también permite a los administradores de primera línea registrar los números de teléfono de los miembros del equipo para [inicio de sesión con SMS](../authentication/howto-authentication-sms-signin.md). En muchos segmentos verticales, los trabajadores de primera línea tienen una combinación de nombre de usuario y contraseña local, una solución que suele resultar engorrosa, costosa y propensa a errores. Cuando TI habilita la autenticación mediante inicio de sesión con SMS, los trabajadores de primera línea pueden iniciar sesión con [inicio de sesión único (SSO)](../manage-apps/what-is-single-sign-on.md) en Microsoft Teams y otras aplicaciones con solo su número de teléfono y un código de acceso de un solo uso (OTP) enviado por SMS. Esto hace que el inicio de sesión de los trabajadores de primera línea sea sencillo y seguro, además de proporcionarles un acceso rápido a las aplicaciones que más necesitan.

![Inicio de sesión con SMS](media/concept-fundamentals-frontline-worker/sms-signin.png)

Los administradores de primera línea también pueden usar la aplicación Managed Home Screen (MHS) para permitir a los trabajadores el acceso a un conjunto determinado de aplicaciones en sus dispositivos dedicados Android inscritos en Intune. Los dispositivos dedicados están inscritos en el [modo de dispositivo compartido de Azure AD](../develop/msal-shared-devices.md). Si se ha configurado en el modo de pantalla completa de varias aplicaciones en la consola de Microsoft Endpoint Manager (MEM), MHS se inicia automáticamente como pantalla principal predeterminada en el dispositivo y se muestra al usuario final como la *única* pantalla principal. Para obtener más información, vea [Configuración de la aplicación Managed Home Screen de Microsoft para Android Enterprise](/mem/intune/apps/app-configuration-managed-home-screen-app).

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>Cierre de sesión seguro de los trabajadores de primera línea desde dispositivos compartidos

Muchas empresas usan dispositivos compartidos para que los trabajadores de primera línea puedan realizar la administración del inventario y transacciones de punto de venta sin la carga de TI que supone el aprovisionamiento y el seguimiento de dispositivos individuales. Con el cierre de sesión de dispositivo compartido, es fácil para un trabajador de primera línea cerrar la sesión de todas las aplicaciones de forma segura en cualquier dispositivo compartido antes de entregarlo a la central o de pasarlo a un compañero en el turno siguiente. Microsoft Teams es una de las aplicaciones que se admiten actualmente en los dispositivos compartidos, y permite a los trabajadores de primera línea ver las tareas que se les han asignado. Una vez que un trabajador cierra la sesión de un dispositivo compartido, Intune y Azure AD borran todos los datos de la empresa para que el dispositivo pueda entregarse de forma segura al siguiente asociado. Puede optar por integrar esta capacidad en todas las aplicaciones [iOS](../develop/msal-ios-shared-devices.md) y [Android](../develop/msal-android-shared-devices.md) de línea de negocio mediante la [Biblioteca de autenticación de Microsoft](../develop/msal-overview.md).

![Cierre de sesión de dispositivo compartido](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre la administración de usuarios delegada, vea la [documentación del usuario de Mi personal](https://support.microsoft.com/account-billing/manage-front-line-users-with-my-staff-c65b9673-7e1c-4ad6-812b-1a31ce4460bd).
- Para el aprovisionamiento de usuarios de entrada desde SAP SuccessFactors, vea el tutorial sobre la [configuración de SAP SuccessFactors para el aprovisionamiento de usuarios de Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
- Para el aprovisionamiento de usuarios de entrada desde Workday, vea el tutorial sobre la [configuración de Workday para el aprovisionamiento automático de usuarios](../saas-apps/workday-inbound-tutorial.md).