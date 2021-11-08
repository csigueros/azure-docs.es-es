---
title: 'Solución de problemas: Faltan datos en los registros de actividad descargados | Microsoft Docs'
description: Proporciona una solución para los datos que faltan en los registros de actividad de Azure Active Directory descargados.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9084d30ad8fe978defa7f336030535f7b4a97a1a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995414"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>No encuentro todos los datos en los registros de actividad de Azure Active Directory que he descargado

## <a name="symptoms"></a>Síntomas

Descargue los registros de actividad (auditoría o inicios de sesión) y no veo todos los registros del tiempo que elegí. ¿Por qué? 

 ![Notificación](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Causa

Al descargar los registros de actividad en Azure Portal, limitamos la escala a 250 000 registros, con el más reciente primero. 

## <a name="resolution"></a>Solución

Puede sacar provecho de la [API de creación de informes de Azure AD](concept-reporting-api.md) para capturar hasta un millones de registros en cualquier momento dado.

## <a name="next-steps"></a>Pasos siguientes

* [P+F sobre los informes de Azure Active Directory](reports-faq.yml)

