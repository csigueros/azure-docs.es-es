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
ms.openlocfilehash: 8560ea04e57553f556122cfa1986cabb03442a4e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399036"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>API de punto de conexión de Azure AD Connect Sync V2 
Microsoft ha implementado un nuevo punto de conexión (API) para Azure AD Connect que mejora el rendimiento de las operaciones del servicio de sincronización para Azure Active Directory. Al usar el nuevo punto de conexión V2, experimentará mejoras de rendimiento notables en la exportación y la importación respecto a Azure AD. Este nuevo punto de conexión admite:
    
 - Grupos de sincronización de hasta 250 000 miembros.
 - Mejoras en el rendimiento de la exportación y la importación a Azure AD.
 
> [!NOTE]
> Actualmente, el nuevo punto de conexión no tiene ningún límite de tamaño de grupo configurado para los grupos de Microsoft 365 que se escriben de manera diferida. Esto puede afectar a las latencias del ciclo de sincronización de Active Directory. Se recomienda aumentar los tamaños de grupo por incrementos.  

>[!NOTE]
> La API de punto de conexión de sincronización de Azure AD Connect V2 está disponible con carácter general pero actualmente solo se puede usar en estos entornos de Azure:
> - Azure Commercial
> - Nube de Azure China
> - La nube de Azure Gobierno de EE. UU. no estará disponible en la nube de Azure Alemania.

## <a name="prerequisites"></a>Requisitos previos  
Para usar el nuevo punto de conexión V2, tendrá que usar Azure AD Connect v2.0. Al implementar AADConnect V2.0, el punto de conexión V2 se habilitará automáticamente.
Hay un problema conocido por el que la actualización a la compilación 1.6 más reciente restablece el límite de pertenencia a grupos a 50 000. Cuando un servidor se actualiza a AADConnect 1.6, el cliente debe volver a aplicar los cambios de reglas que aplicaron al aumentar inicialmente el límite de pertenencia a grupos a 250 000 antes de habilitar la sincronización para el servidor. 

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes  
 
**¿Cuándo se convertirá el nuevo punto de conexión en el valor predeterminado para las actualizaciones y las nuevas instalaciones?**   El punto de conexión V2 es la configuración predeterminada de AADConnect V2.0 y recomendamos a los clientes que actualicen a AADConnect V2.0 para aprovechar las ventajas de este punto de conexión.
Hay un problema por el cual los clientes que tienen el punto de conexión V2 en ejecución con una versión anterior e intentan actualizar a la versión V1.6 más reciente verán que se restablece la limitación de 50 000 en la pertenencia a grupos. Cuando un servidor se actualiza a AADConnect 1.6, el cliente debe volver a aplicar los cambios de reglas que aplicaron al aumentar inicialmente el límite de pertenencia a grupos a 250 000 antes de habilitar la sincronización para el servidor. 

## <a name="next-steps"></a>Pasos siguientes

* [Sincronización de Azure AD Connect: comprender y personalizar la sincronización](how-to-connect-sync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](whatis-hybrid-identity.md)
