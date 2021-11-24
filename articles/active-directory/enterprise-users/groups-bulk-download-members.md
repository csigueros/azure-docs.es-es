---
title: 'Lista de pertenencia a grupos de descarga masiva: portal de Azure Active Directory | Microsoft Docs'
description: Agregue usuarios de forma masiva en el Centro de administración de Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 10/26/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d27f1182f7e7308a58b97705ef20d5f2a726eb7
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400155"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Descarga masiva de miembros de un grupo en Azure Active Directory

Mediante el portal de Azure Active Directory (Azure AD), puede descargar de forma masiva los miembros de un grupo de su organización en un archivo de valores separados por comas (CSV). Todos los administradores y usuarios que no son administradores pueden descargar listas de pertenencia a grupos.

## <a name="to-bulk-download-group-membership"></a>Descarga masiva de pertenencias al grupo

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta de la organización.
1. En Azure AD, seleccione **Grupos** > **Todos los grupos**.
1. Abra el grupo cuya pertenencia quiere descargar y, a continuación, seleccione los **miembros**.
1. En la página de **miembros**, seleccione **Descargar miembros** para descargar un archivo CSV que enumera los miembros del grupo.

   ![El comando Descargar miembros está en la página de perfil del grupo](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Comprobar el estado de la descarga

Puede ver el estado de todas las solicitudes masivas pendientes en la página **Resultados de la operación masiva**.

[![Comprobación del estado en la página Resultados de la operación masiva.](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Límites del servicio de descarga de forma masiva

La ejecución de cada actividad masiva para descargar una lista de miembros de grupo puede durar hasta una hora. Esto permite la descarga de una lista de al menos 500 000 miembros.

## <a name="next-steps"></a>Pasos siguientes

- [Importación masiva de miembros de grupo](groups-bulk-import-members.md)
- [Quitar miembros de un grupo de forma masiva](groups-bulk-download-members.md)
