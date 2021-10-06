---
title: Acceso condicional en Azure Synapse Analytics
description: Artículos en los que se explica el acceso condicional en Azure Synapse Analytics
author: meenalsri
ms.author: mesrivas
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/07/2021
ms.sub-service: security
ms.custom: template-concept
ms.openlocfilehash: 4461ac0874eef735b7d01a3fc9c2c4158ddcb62d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594596"
---
# <a name="conditional-access-in-azure-synapse-analytics"></a>Acceso condicional en Azure Synapse Analytics

Ahora puede configurar directivas de acceso condicional para áreas de trabajo de Azure Synapse. El acceso condicional es una herramienta proporcionada por Azure Active Directory para combinar varias señales, como el tipo y la ubicación IP del dispositivo, a fin de tomar decisiones para conceder acceso, bloquearlo o aplicar la autenticación multifactor para un recurso. Las directivas de acceso condicional se configuran en Azure Active Directory. Obtenga más información sobre el [acceso condicional](../../active-directory/conditional-access/overview.md).


## <a name="configure-conditional-access"></a>Configurar el acceso condicional
En los pasos siguientes se muestra cómo configurar una directiva de acceso condicional para áreas de trabajo de Azure Synapse.

1. Inicie sesión en Azure Portal mediante una cuenta con *permisos de administrador global*, seleccione **Azure Active Directory** y después **Seguridad** en el menú. 
2. Seleccione **Acceso condicional**, elija **+ Nueva directiva** y proporcione un nombre para la directiva.
3. En **Asignaciones**, seleccione **Usuarios y grupos**, active la opción **Seleccionar usuarios y grupos**, y seleccione el usuario o el grupo de Azure AD para el acceso condicional. Haga clic en Seleccionar y después en Listo.
4. Seleccione **Aplicaciones en la nube** y haga clic en **Seleccionar aplicaciones**. Seleccione **Puerta de enlace de Microsoft Azure Synapse**. Después, haga clic en Seleccionar y Listo.
5. En **Controles de acceso**, seleccione **Conceder**, active la directiva que quiera aplicar y seleccione Listo.
6. Establezca la opción **Habilitar directiva** en **Activada** y, después, seleccione Crear.


## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre las directivas de acceso condicional y sus componentes.
- [Directivas de acceso condicional habituales](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)
- [Creación de una directiva de acceso condicional](../../active-directory/conditional-access/concept-conditional-access-policies.md)