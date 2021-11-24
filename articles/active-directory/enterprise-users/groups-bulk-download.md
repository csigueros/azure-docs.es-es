---
title: Descargar una lista de grupos en el portal de Azure Active Directory | Microsoft Docs
description: Descargue las propiedades de grupo de forma masiva en el centro de administración de Azure en Azure Active Directory.
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
ms.openlocfilehash: 298e961eff1736f32d2fa703dfb0079ea758e343
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401390"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Descarga masiva de una lista de grupos en Azure Active Directory

Mediante el portal de Azure Active Directory (Azure AD), puede descargar de forma masiva la lista de todos los grupos de su organización en un archivo de valores separados por comas (CSV). Todos los administradores y usuarios que no son administradores pueden descargar listas de grupos.

## <a name="to-download-a-list-of-groups"></a>Descargar una lista de grupos

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta de la organización.
1. En Azure AD, seleccione **Grupos** > **Descargar grupos**.
1. En la página de **descarga de grupos**, seleccione **Iniciar** para recibir un archivo CSV que enumere los grupos.

   ![El comando para descargar grupos está en la página Todos los grupos](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Comprobar el estado de la descarga

Puede ver el estado de todas las solicitudes masivas pendientes en la página **Resultados de la operación masiva**.

[![Comprobación del estado en la página Resultados de la operación masiva.](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Límites del servicio de descarga de forma masiva

La ejecución de cada actividad masiva para descargar una lista de grupos puede durar hasta una hora. Esto permite la descarga de una lista de al menos 300 000 grupos.

## <a name="next-steps"></a>Pasos siguientes

- [Quitar miembros de un grupo de forma masiva](groups-bulk-remove-members.md)
- [Descarga de miembros de un grupo](groups-bulk-download-members.md)
