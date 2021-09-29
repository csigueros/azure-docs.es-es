---
title: 'Aplicaciones cliente aprobadas con acceso condicional: Azure Active Directory'
description: Aprenda a establecer el uso obligatorio de aplicaciones cliente aprobadas para el acceso a aplicaciones en la nube con acceso condicional en Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f6a2d54b8669ddfbb2a1427ffffad97a7d539f5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587202"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Procedimientos: Uso obligatorio de aplicaciones cliente aprobadas para el acceso a aplicaciones en la nube mediante el acceso condicional

Los usuarios utilizan sus dispositivos móviles habitualmente para tareas personales y profesionales. A la vez que se aseguran de que el personal sigue siendo productivo, las organizaciones también quieren evitar la pérdida de datos de aplicaciones potencialmente no seguras. Con el acceso condicional, las organizaciones pueden restringir el acceso a aplicaciones cliente aprobadas (con capacidad para la autenticación moderna).

En este artículo se presentan dos escenarios para configurar directivas de acceso condicional para recursos como Microsoft 365, Exchange Online y SharePoint Online.

- [Escenario 1: Las aplicaciones de Microsoft 365 requieren una aplicación cliente aprobada](#scenario-1-microsoft-365-apps-require-an-approved-client-app)
- [Escenario 2: Exchange Online y SharePoint Online requieren una aplicación cliente aprobada](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

En el acceso condicional, esta funcionalidad se conoce como requerir una aplicación cliente aprobada. Para una lista de las aplicaciones cliente aprobadas, consulte el [requisito de las aplicaciones cliente aprobadas](concept-conditional-access-grant.md#require-approved-client-app).

> [!NOTE]
> Para requerir aplicaciones cliente aprobadas para dispositivos iOS y Android, estos dispositivos deben registrarse primero en Azure AD.

## <a name="scenario-1-microsoft-365-apps-require-an-approved-client-app"></a>Escenario 1: Las aplicaciones de Microsoft 365 requieren una aplicación cliente aprobada.

En este escenario, Contoso ha decidido que los usuarios que usan dispositivos móviles pueden acceder a todos los servicios de Microsoft 365, siempre y cuando usen aplicaciones cliente aprobadas, como Outlook Mobile, OneDrive y Microsoft Teams. Todos sus usuarios ya inician sesión con credenciales de Azure AD y tienen licencias asignadas que incluyen Azure AD Premium P1 o P2 y Microsoft Intune.

Las organizaciones deben completar los tres pasos siguientes para requerir el uso de una aplicación cliente aprobada en dispositivos móviles.

**Paso 1: Directiva para los clientes de autenticación moderna basada en Android e iOS que requieren el uso de una aplicación cliente aprobada al acceder a Exchange Online.**

1. Inicie sesión en **Azure Portal** como administrador global, administrador de seguridad o administrador de acceso condicional.
1. Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Asignaciones**, seleccione **Usuarios y grupos**.
   1. En **Incluir**, seleccione **Todos los usuarios** o **Usuarios y grupos** específicos a los que desee aplicar esta directiva. 
   1. Seleccione **Listo**.
1. En **Aplicaciones en la nube o acciones** > **Incluir**, seleccione **Office 365**.
1. En **Condiciones**, seleccione **Plataformas de dispositivo**.
   1. Establezca **Configurar** en **Sí**.
   1. Incluya **Android** e **iOS**.
1. En **Condiciones**, seleccione **Aplicaciones cliente (versión preliminar)** .
   1. Establezca **Configurar** en **Sí**.
   1. Seleccione **Aplicaciones móviles y clientes de escritorio** y **Clientes de autenticación moderna**.
1. En **Controles de acceso** > **Conceder**, seleccione **Conceder acceso**, **Requerir aplicación cliente aprobada** y **Seleccionar**.
1. Confirme la configuración y establezca **Habilitar directiva** en **Activado**.
1. Seleccione **Crear** para crear y habilitar la directiva.

**Paso 2: Configuración de una directiva de acceso condicional de Azure AD para Exchange Online con Active Sync (EAS)**

1. Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Asignaciones**, seleccione **Usuarios y grupos**.
   1. En **Incluir**, seleccione **Todos los usuarios** o **Usuarios y grupos** específicos a los que desee aplicar esta directiva. 
   1. Seleccione **Listo**.
1. En **Aplicaciones en la nube o acciones** > **Incluir**, seleccione **Office 365 Exchange Online**.
1. En **Condiciones**:
   1. **Aplicaciones cliente (versión preliminar)** :
      1. Establezca **Configurar** en **Sí**.
      1. Seleccione **Aplicaciones móviles y clientes de escritorio** y **Clientes de Exchange ActiveSync**.
1. En **Controles de acceso** > **Conceder**, seleccione **Conceder acceso**, **Requerir aplicación cliente aprobada** y **Seleccionar**.
1. Confirme la configuración y establezca **Habilitar directiva** en **Activado**.
1. Seleccione **Crear** para crear y habilitar la directiva.

**Paso 3: Configuración de la directiva de protección de aplicaciones de Intune para aplicaciones cliente de iOS y Android.**

Revise el artículo [Creación y asignación de directivas de protección de aplicaciones](/intune/apps/app-protection-policies) para conocer los pasos para crear directivas de protección de aplicaciones para Android e iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Escenario 2: Exchange Online y SharePoint Online requieren una aplicación cliente aprobada

En este escenario, Contoso ha decidido que los usuarios solo pueden acceder al correo electrónico y a los datos de SharePoint en dispositivos móviles, siempre que usen una aplicación cliente aprobada, como Outlook Mobile. Todos sus usuarios ya inician sesión con credenciales de Azure AD y tienen licencias asignadas que incluyen Azure AD Premium P1 o P2 y Microsoft Intune.

Las organizaciones deben completar los tres pasos siguientes para requerir el uso de una aplicación cliente aprobada en dispositivos móviles y clientes de Exchange ActiveSync.

**Paso 1: Directiva para los clientes de autenticación moderna basada en Android e iOS que requieren el uso de una aplicación cliente aprobada al acceder a Exchange Online y SharePoint Online.**

1. Inicie sesión en **Azure Portal** como administrador global, administrador de seguridad o administrador de acceso condicional.
1. Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Asignaciones**, seleccione **Usuarios y grupos**.
   1. En **Incluir**, seleccione **Todos los usuarios** o **Usuarios y grupos** específicos a los que desee aplicar esta directiva. 
   1. Seleccione **Listo**.
1. En **Aplicaciones en la nube o acciones** > **Incluir**, seleccione **Office 365 Exchange Online** y **Office 365 SharePoint Online**.
1. En **Condiciones**, seleccione **Plataformas de dispositivo**.
   1. Establezca **Configurar** en **Sí**.
   1. Incluya **Android** e **iOS**.
1. En **Condiciones**, seleccione **Aplicaciones cliente (versión preliminar)** .
   1. Establezca **Configurar** en **Sí**.
   1. Seleccione **Aplicaciones móviles y clientes de escritorio** y **Clientes de autenticación moderna**.
1. En **Controles de acceso** > **Conceder**, seleccione **Conceder acceso**, **Requerir aplicación cliente aprobada** y **Seleccionar**.
1. Confirme la configuración y establezca **Habilitar directiva** en **Activado**.
1. Seleccione **Crear** para crear y habilitar la directiva.

**Paso 2: Directiva para los clientes de Exchange ActiveSync que requieren el uso de una aplicación cliente aprobada.**

1. Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Asignaciones**, seleccione **Usuarios y grupos**.
   1. En **Incluir**, seleccione **Todos los usuarios** o **Usuarios y grupos** específicos a los que desee aplicar esta directiva. 
   1. Seleccione **Listo**.
1. En **Aplicaciones en la nube o acciones** > **Incluir**, seleccione **Office 365 Exchange Online**.
1. En **Condiciones**:
   1. **Aplicaciones cliente (versión preliminar)** :
      1. Establezca **Configurar** en **Sí**.
      1. Seleccione **Aplicaciones móviles y clientes de escritorio** y **Clientes de Exchange ActiveSync**.
1. En **Controles de acceso** > **Conceder**, seleccione **Conceder acceso**, **Requerir aplicación cliente aprobada** y **Seleccionar**.
1. Confirme la configuración y establezca **Habilitar directiva** en **Activado**.
1. Seleccione **Crear** para crear y habilitar la directiva.

**Paso 3: Configuración de la directiva de protección de aplicaciones de Intune para aplicaciones cliente de iOS y Android.**

Revise el artículo [Creación y asignación de directivas de protección de aplicaciones](/intune/apps/app-protection-policies) para conocer los pasos para crear directivas de protección de aplicaciones para Android e iOS. 

## <a name="next-steps"></a>Pasos siguientes

[¿Qué es el acceso condicional?](overview.md)

[Componentes de acceso condicional](concept-conditional-access-policies.md)

[Directivas de acceso condicional habituales](concept-conditional-access-policy-common.md)
