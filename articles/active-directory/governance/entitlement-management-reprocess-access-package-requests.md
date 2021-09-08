---
title: 'Reprocesamiento de solicitudes para un paquete de acceso en la administración de derechos de Azure AD: Azure Active Directory'
description: Obtenga información sobre cómo volver a procesar solicitudes para un paquete de acceso en la administración de derechos de Azure Active Directory.
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
ms.openlocfilehash: a85d796f8b66cca16d4d3c01ecbfc5f6c43c79f9
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129470"
---
# <a name="reprocess-requests-for-an-access-package-in-azure-ad-entitlement-management"></a>Reprocesamiento de solicitudes para un paquete de acceso en la administración de derechos de Azure AD

Como administrador de paquetes de acceso, puede reintentar automáticamente la solicitud de acceso de un usuario a un paquete de acceso en cualquier momento mediante la funcionalidad de reprocesamiento. El reprocesamiento elimina la necesidad de que los usuarios repitan el proceso de solicitud del paquete de acceso si su acceso a los recursos no se aprovisiona correctamente.

> [!NOTE]
> Puede volver a procesar una solicitud durante un máximo de 14 días a partir del momento en que se completó la solicitud original. Para las solicitudes que se completaron hace más de 14 días, los usuarios tendrán que cancelar y realizar nuevas solicitudes en MyAccess.

En este artículo, se describe cómo volver a procesar las solicitudes de un paquete de acceso existente.

## <a name="prerequisites"></a>Prerrequisitos

Para usar la administración de derechos de Azure AD y asignar usuario a los paquetes de acceso, debe tener una de las licencias siguientes:

- Azure AD Premium P2
- Licencia de Enterprise Mobility + Security (EMS) E5

## <a name="open-an-existing-access-package-and-reprocess-user-requests"></a>Apertura de un paquete de acceso existente y reprocesamiento de las solicitudes de usuario

**Requisitos previos de rol:** Administrador global, Administrador de Identity Governance, Administrador de usuarios, Propietario de catálogo, Administrador de paquetes de acceso o Administrador de asignaciones de paquetes de acceso.

Si tiene un conjunto de usuarios cuyas solicitudes están en el estado "Entregado parcialmente" o "Error", es posible que tenga que volver a procesar algunas de esas solicitudes. Siga estos pasos para volver a procesar las solicitudes de un paquete de acceso existente:

1.  Inicie sesión en [Azure Portal](https://portal.azure.com).

1.  Haga clic en **Azure Active Directory** y, luego, haga clic en **Gobierno de identidades**.

1.  En el menú de la izquierda, haga clic en **Paquetes de acceso** y luego abra el paquete de acceso.

1.  Debajo de **Administrar** en el lado izquierdo, haga clic en **Solicitudes**.

1.  Seleccione todos los usuarios cuyas solicitudes desea volver a procesar.

1.  Haga clic en **Reprocesar**.

## <a name="next-steps"></a>Pasos siguientes

- [Ver las solicitudes de un paquete de acceso](entitlement-management-access-package-requests.md)
- [Aprobación o denegación de solicitudes de acceso](entitlement-management-request-approve.md)