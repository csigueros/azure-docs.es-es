---
title: Configuración de controles adicionales para cumplir el nivel de impacto de FedRAMP High
description: Instrucciones detalladas sobre cómo configurar controles adicionales para cumplir los niveles de impacto de FedRAMP High.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: celested
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed80ffbe92777066e09d443f3e5319ad721be653
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110540304"
---
# <a name="configure-additional-controls-to-achieve-fedramp-high-impact-level"></a>Configuración de controles adicionales para cumplir el nivel de impacto de FedRAMP High

La siguiente lista de controles y mejoras de control puede requerir la configuración del inquilino de Azure Active Directory (Azure AD).

Cada fila de las tablas siguientes proporciona instrucciones prescriptivas. Estas instrucciones le ayudan a desarrollar la respuesta de su organización a las responsabilidades compartidas respecto al control o la mejora del control.

## <a name="audit-and-accountability"></a>Auditoría y responsabilidad

Las instrucciones de la tabla siguiente pertenecen a:

* AU-02 Auditoría de eventos
* AU-03 Contenido de la auditoría
* AU-06 Revisión, análisis e informes de auditoría

| Identificador y subparte del control| Responsabilidades e instrucciones del cliente |
| - | - |
| AU-02 <br>AU-03 <br>AU-03(1)<br>AU-03(2)| Asegúrese de que el sistema es capaz de auditar los eventos definidos en la parte A de AU-02. Coordine con otras entidades dentro del subconjunto de eventos auditables de la organización para admitir investigaciones posteriores. Implemente la administración centralizada de los registros de auditoría.<p>Todas las operaciones de ciclo de vida de la cuenta (creación, modificación, habilitación, deshabilitación y eliminación de cuentas) se auditan dentro de los registros de auditoría de Azure AD. Todos los eventos de autenticación y autorización se auditan en los registros de inicio de sesión de Azure AD y los riesgos detectados se auditan en los registros de Identity Protection. Puede transmitir cada uno de estos registros directamente a una solución de administración de eventos e información de seguridad (SIEM), como Azure Sentinel. También puede usar Azure Event Hubs para integrar registros con soluciones SIEM de terceros.<p>Eventos de auditoría<li> [Informes de actividad de auditoría en el portal de Azure Active Directory](../reports-monitoring/concept-audit-logs.md)<li> [Informes de actividad de inicio de sesión en el portal de Azure Active Directory](../reports-monitoring/concept-sign-ins.md)<li>[Cómo: Investigar los riesgos](../identity-protection/howto-identity-protection-investigate-risk.md)<p>Integraciones de SIEM<li> [Azure Sentinel: conexión de datos desde Azure Active Directory (Azure AD)](../../sentinel/connect-azure-active-directory.md)<li>[Transmisión a un centro de eventos de Azure y a otros SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |
| AU-06<br>AU-06(1)<br>AU-06(3)<br>AU-06(4)<br>AU-06(5)<br>AU-06(6)<br>AU-06(7)<br>AU-06(10)<br>| Revisar y analizar los registros de auditoría al menos una vez a la semana para identificar actividades inapropiadas o inusuales, e informar de los resultados al personal adecuado. <p>Las instrucciones proporcionadas anteriormente para AU-02 y AU-03 permiten revisar semanalmente los registros de auditoría y los informes al personal adecuado. No puede cumplir estos requisitos solo con Azure AD. También debe usar una solución SIEM como Azure Sentinel. Para más información, vea [¿Qué es Azure Sentinel?](../../sentinel/overview.md) |

## <a name="incident-response"></a>Respuesta a incidentes

Las instrucciones de la tabla siguiente pertenecen a:

* IR-04 Tratamiento de incidentes

* IR-05 Supervisión de incidentes

| Identificador y subparte del control| Responsabilidades e instrucciones del cliente |
| - | - |
| IR-04<br>IR-04(1)<br>IR-04(2)<br>IR-04(3)<br>IR-04(4)<br>IR-04(6)<br>IR-04(8)<br>IR-05<br>IR-05(1)| Implemente funcionalidades de control y supervisión de incidentes. Ahí se incluye la gestión automatizada de incidentes, la reconfiguración dinámica, la continuidad de las operaciones, la correlación de la información, las amenazas internas, la correlación con organizaciones externas, la supervisión de incidentes y el seguimiento automatizado. <p>Los registros de auditoría registran todos los cambios de configuración. Los eventos de autenticación y autorización se auditan en los registros de inicio de sesión, y los riesgos detectados se auditan en los registros de Identity Protection. Puede transmitir cada uno de estos registros directamente a una solución SIEM, como Azure Sentinel. También puede usar Azure Event Hubs para integrar registros con soluciones SIEM de terceros. Automatice la reconfiguración dinámica en función de los eventos dentro de SIEM mediante Microsoft Graph o Azure AD PowerShell.<p>Eventos de auditoría<br><li>[Informes de actividad de auditoría en el portal de Azure Active Directory](../reports-monitoring/concept-audit-logs.md)<li>[Informes de actividad de inicio de sesión en el portal de Azure Active Directory](../reports-monitoring/concept-sign-ins.md)<li>[Cómo: Investigar los riesgos](../identity-protection/howto-identity-protection-investigate-risk.md)<p>Integraciones de SIEM<li>[Azure Sentinel: conexión de datos desde Azure Active Directory (Azure AD)](../../sentinel/connect-azure-active-directory.md)<li>[Transmisión a un centro de eventos de Azure y a otros SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)<p>Reconfiguración dinámica<li>[Módulo AzureAD](/powershell/module/azuread/)<li>[Overview of Microsoft Graph (Información general de Microsoft Graph)](/graph/overview?view=graph-rest-1.0&preserve-view=true) |

## <a name="personnel-security"></a>Seguridad del personal

Las instrucciones de la tabla siguiente pertenecen a:

* PS-04 Finalización del contrato del personal

| Identificador y subparte del control| Responsabilidades e instrucciones del cliente |
| - | - |
| PS-04<br>PS-04(2)| Informar automáticamente al personal responsable de deshabilitar el acceso al sistema. <p>Deshabilite las cuentas y revoque todos los autenticadores y credenciales asociados en un plazo de 8 horas. <p>Configure el aprovisionamiento (incluida la deshabilitación tras la finalización) de las cuentas de Azure AD desde sistemas de RR. UU. externos, Active Directory local o directamente en la nube. Ponga fin a todo el acceso al sistema revocando las sesiones existentes. <p>Aprovisionamiento de cuentas<li> Consulte instrucciones detalladas en AC-02. <p>Revocación de todos los autenticadores asociados <li> [Revocación del acceso de usuario en un emergencia de Azure Active Directory](../enterprise-users/users-revoke-access.md) |


## <a name="system-and-information-integrity"></a>Integridad del sistema y de la información

Las instrucciones de la tabla siguiente pertenecen a:

* SI-04 Supervisión del sistema de información

 Identificador y subparte del control| Responsabilidades e instrucciones del cliente |
| - | - |
| SI-04<br>SI-04(1)| Implementar un sistema de supervisión y detección de intrusos en todo el sistema de información. <p>Incluya todos los registros de Azure AD (auditoría, inicio de sesión, Identity Protection) en la solución de supervisión del sistema de información. <p>Transmita registros de Azure AD a una solución SIEM (consulte IA-04). |

## <a name="next-steps"></a>Pasos siguientes

[Configuración de controles de acceso](fedramp-access-controls.md)

[Configuración de los controles de identificación y autenticación](fedramp-identification-and-authentication-controls.md)

[Configuración de otros controles](fedramp-other-controls.md)
 