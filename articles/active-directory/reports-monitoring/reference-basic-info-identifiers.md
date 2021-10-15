---
title: Identificadores de información básica en el registro de inicio de sesión de Azure AD | Microsoft Docs
description: Conozca para qué se usan los identificadores de información básica.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca1ea9524a10ac46a33f6430228d8a45ee66f28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367611"
---
# <a name="basic-info-identifiers-in-the-azure-ad-sign-in-log"></a>Identificadores de información básica en el registro de inicio de sesión de Azure AD

Azure AD registra todos los inicios de sesión en un inquilino de Azure de cara al cumplimiento. Como administrador de TI, debe saber lo que significan los valores de un registro de inicio de sesión, para que pueda interpretar los valores correctamente.
En este artículo se explican los identificadores de la pestaña Información básica del registro de inicios de sesión.

## <a name="unique-identifiers"></a>Identificadores únicos 

En Azure AD, el acceso a los recursos tiene tres componentes destacables:

- **Quién**: la identidad (usuario) que realiza el inicio de sesión. 
- **Cómo**: el cliente (aplicación) utilizado para el acceso.  
- **Qué**: el destino (recurso) al que accede la identidad.


Cada componente tiene un identificador único (id.) asociado. A continuación, se muestra un ejemplo de un usuario que usa Azure Service Management API en Windows para acceder a Azure Portal.

![Apertura de registros de auditoría](./media/reference-basic-info-identifiers/sign-in-details-basic-info.png)

## <a name="tenant-identifiers"></a>Identificadores de inquilino

El registro de inicio de sesión realiza un seguimiento de dos identificadores de inquilino:

- **Inquilino principal**: el inquilino que posee la identidad del usuario. 
- **Inquilino de recurso**: el inquilino que posee el recurso (destino).

Estos identificadores son apropiados en escenarios entre inquilinos. Por ejemplo, para averiguar cómo los usuarios externos al inquilino acceden a los recursos, seleccione todas las entradas en las que el inquilino principal no coincida con el inquilino de recurso.

## <a name="request-id"></a>Id. de solicitud

El identificador de solicitud es un identificador que corresponde a un token emitido. Si busca inicios de sesión que se realizaron con un token específico, primero debe extraer el identificador de solicitud del token.


## <a name="correlation-id"></a>Id. de correlación

El identificador de correlación es un identificador que correlaciona los inicios de sesión de la misma sesión de inicio de sesión. El identificador se implementó a efectos prácticos. No se garantiza su precisión porque el valor se basa en parámetros pasados por un cliente. 




## <a name="next-steps"></a>Pasos siguientes

* [Registros de inicios de sesión en Azure Active Directory](concept-sign-ins.md)
* [¿Qué es el diagnóstico de inicio de sesión en Azure AD?](overview-sign-in-diagnostics.md)
