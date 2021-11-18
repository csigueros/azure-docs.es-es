---
title: Punto de conexión de Azure AD Connect Sync v2 | Microsoft Docs
description: En este documento se tratan las actualizaciones de la API de los puntos de conexión de Azure AD Connect Sync v2.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 161805ec90daec9814e3ac8b0547bf36a22d7d91
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135272"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>API de punto de conexión de Azure AD Connect Sync V2 
Microsoft ha implementado un nuevo punto de conexión (API) para Azure AD Connect que mejora el rendimiento de las operaciones del servicio de sincronización para Azure Active Directory. Al usar el nuevo punto de conexión V2, experimentará mejoras de rendimiento notables en la exportación y la importación respecto a Azure AD. Este nuevo punto de conexión admite:
    
 - Grupos de sincronización de hasta 250 000 miembros
 - Mejoras en el rendimiento de la exportación y la importación a Azure AD
 
> [!NOTE]
> Actualmente, el nuevo punto de conexión no tiene ningún límite de tamaño de grupo configurado para los grupos de Microsoft 365 que se escriben de manera diferida. Esto puede afectar a las latencias del ciclo de sincronización de Active Directory. Se recomienda aumentar los tamaños de grupo por incrementos.  

>[!NOTE]
> La API de punto de conexión de sincronización de Azure AD Connect V2 está disponible con carácter general pero actualmente solo se puede usar en estos entornos de Azure:
> - Azure Commercial
> - Nube de Azure China
> - La nube de Azure Gobierno de EE. UU. no estará disponible en la nube de Azure Alemania.

## <a name="prerequisites"></a>Requisitos previos  
Para usar el nuevo punto de conexión V2, tendrá que usar Azure AD Connect v2.0. Al implementar AADConnect V2.0, el punto de conexión V2 se habilitará automáticamente.
La compatibilidad con el punto de conexión V2 ya no está disponible para las versiones V1.x. Si tiene que sincronizar grupos con más de 50 000 miembros, debe actualizar a Azure AD Connect V2.0.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes  
 
**¿Cuándo se convertirá el nuevo punto de conexión en el valor predeterminado para las actualizaciones y las nuevas instalaciones?**   El punto de conexión V2 es la configuración predeterminada para AADConnect V2.0 y no es compatible con AADConnect V1.x.
Hay un problema por el cual los clientes que tienen el punto de conexión V2 en ejecución con una versión anterior e intentan actualizar a la versión V1.6 más reciente verán que se restablece la limitación de 50 000 en la pertenencia a grupos. Este problema no se corregirá en la versión 1.6 y se requerirá que los clientes actualicen a AADConnect V2.0 si se trata de un problema para ellos.

## <a name="next-steps"></a>Pasos siguientes

* [Sincronización de Azure AD Connect: comprender y personalizar la sincronización](how-to-connect-sync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](whatis-hybrid-identity.md)
