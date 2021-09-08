---
title: 'Reprocesamiento de asignaciones para un paquete de acceso en la administración de derechos de Azure AD: Azure Active Directory'
description: Obtenga información sobre cómo volver a procesar asignaciones para un paquete de acceso en la administración de derechos de Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/25/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1b488f8c9331932b82ab0ab55db9c7dcb652add
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129471"
---
# <a name="reprocess-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Reprocesamiento de asignaciones para un paquete de acceso en la administración de derechos de Azure AD

Como administrador de paquetes de acceso, puede volver a evaluar y aplicar automáticamente las asignaciones originales de los usuarios en un paquete de acceso mediante la funcionalidad de reprocesamiento. El reprocesamiento elimina la necesidad de que los usuarios repitan el proceso de solicitud del paquete de acceso si su acceso a los recursos se ha visto afectado por cambios fuera de la administración de derechos.

Por ejemplo, es posible que un usuario se haya quitado manualmente de un grupo, lo que hace que ese usuario pierda el acceso a los recursos necesarios. 

La administración de derechos no bloquea las actualizaciones externas a los recursos del paquete de acceso, por lo que la interfaz de usuario de la administración de derechos no mostraría con precisión este cambio. Por lo tanto, el estado de asignación del usuario se mostraría como "Entregado", aunque el usuario ya no tenga acceso a los recursos. Sin embargo, si se vuelve a procesar la asignación del usuario, se agregará de nuevo a los recursos del paquete de acceso. El reprocesamiento garantiza que las asignaciones de paquetes de acceso estén actualizadas, que los usuarios tengan acceso a los recursos necesarios y que las asignaciones se reflejen con precisión en la interfaz de usuario.

En este artículo, se describe cómo volver a procesar la asignaciones de un paquete de acceso existente.

## <a name="prerequisites"></a>Prerrequisitos

Para usar la administración de derechos de Azure AD y asignar usuario a los paquetes de acceso, debe tener una de las licencias siguientes:

- Azure AD Premium P2
- Licencia de Enterprise Mobility + Security (EMS) E5

## <a name="open-an-existing-access-package-and-reprocess-user-assignments"></a>Apertura de un paquete de acceso existente y reprocesamiento de las asignaciones de usuario

**Requisitos previos de rol:** Administrador global, Administrador de Identity Governance, Administrador de usuarios, Propietario de catálogo, Administrador de paquetes de acceso o Administrador de asignaciones de paquetes de acceso.

Si tiene usuarios que están en el estado "Entregado" pero no tienen acceso a los recursos que forman parte del paquete de acceso, es probable que tenga que volver a procesar las asignaciones para reasignar esos usuarios a los recursos del paquete de acceso. Siga estos pasos para volver a procesar las asignaciones de un paquete de acceso existente:

1.  Inicie sesión en [Azure Portal](https://portal.azure.com).

1.  Haga clic en **Azure Active Directory** y, luego, haga clic en **Gobierno de identidades**.

1.  En el menú de la izquierda, haga clic en **Paquetes de acceso** y luego abra el paquete de acceso con la asignación de usuarios que quiere volver a procesar.

1.  Debajo de **Administrar**, en el lado izquierdo, haga clic en **Asignaciones**.

    ![Administración de derechos en Azure Portal](./media/entitlement-management-reprocess-access-package-assignments/reprocess-access-package-assignment.png)

1.  Seleccione todos los usuarios cuyas asignaciones desea volver a procesar.

1.  Haga clic en **Reprocesar**.

## <a name="next-steps"></a>Pasos siguientes

- [Visualización, incorporación y eliminación de asignaciones en un paquete de acceso](entitlement-management-access-package-assignments.md)
- [Visualización de informes y registros](entitlement-management-reports.md)
