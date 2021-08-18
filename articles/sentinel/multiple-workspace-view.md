---
title: Procesamiento de incidentes de Azure Sentinel en muchas áreas de trabajo a la vez | Microsoft Docs
description: Cómo ver incidentes en varias áreas de trabajo simultáneamente en Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: yelevin
ms.openlocfilehash: 59550287dfa749e22334f9f238efa5be70b70252
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779985"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>Procesamiento de incidentes en varias áreas de trabajo a la vez 

 Para aprovechar al máximo las funcionalidades de Azure Sentinel, Microsoft recomienda usar un entorno con una sola área de trabajo. Sin embargo, hay algunos casos de uso que requieren tener varias áreas de trabajo y, a veces, por ejemplo, para un [proveedor de servicios de seguridad administrados (MSSP)](./multiple-tenants-service-providers.md) y sus clientes, estas deben estar en varios inquilinos. La opción **Vista de varias áreas de trabajo** permite ver y trabajar con incidentes de seguridad en varias áreas de trabajo al mismo tiempo, incluso entre los inquilinos, lo que permite mantener la visibilidad y el control completos de la capacidad de respuesta de seguridad de la organización.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="entering-multiple-workspace-view"></a>Acceso a la vista de varias áreas de trabajo

Cuando abra Azure Sentinel, se le mostrará una lista de todas las áreas de trabajo para las que tiene derechos de acceso en todos los inquilinos y suscripciones seleccionados. A la izquierda de los nombres de las áreas de trabajo hay una casilla. Cuando haga clic en el nombre de una sola área de trabajo, se le dirigirá a esta. Para elegir varias áreas de trabajo, haga clic en todas las casillas correspondientes y, a continuación, en el botón **Vista de varias áreas de trabajo** en la parte superior de la página.

> [!IMPORTANT]
> Actualmente, la vista de varias áreas de trabajo admite un máximo de 10 áreas de trabajo que se muestran simultáneamente. 
> 
> Si activa más de 10, se mostrará un mensaje de advertencia.

Tenga en cuenta que en la lista de áreas de trabajo, puede ver el directorio, la suscripción, la ubicación y el grupo de recursos asociados a cada área de trabajo. El directorio se corresponde con el inquilino.

   ![Elección de varias áreas de trabajo](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>Procesamiento de incidentes

En **Vista de varias áreas de trabajo**, de momento solo hay disponible la pantalla **Incidentes**. Su aspecto y funcionamiento son prácticamente iguales que los de la pantalla **Incidentes** habitual. Sin embargo, hay algunas diferencias importantes:

   ![Vista de incidentes en varias áreas de trabajo](./media/multiple-workspace-view/incidents.png)

- Los contadores de la parte superior de la página (*Incidentes abiertos*, *Nuevos incidentes*, *En curso*, etc.) muestran las cifras correspondientes a todas las áreas de trabajo seleccionadas en conjunto.

- De este modo, verá los incidentes de todas las áreas de trabajo y los directorios seleccionados (inquilinos) en una sola lista unificada. Puede filtrar la lista por el área de trabajo y el directorio, además de los filtros disponibles en la pantalla **Incidentes** habitual.

- Deberá tener permisos de lectura y escritura en todas las áreas de trabajo desde las que haya seleccionado incidentes. Si solo tiene permisos de lectura en algunas áreas de trabajo y selecciona incidentes en estas, se le mostrarán mensajes de advertencia. No podrá modificar esos incidentes ni ningún otro que haya seleccionado (aunque tenga permisos para los demás).

- Si elige un solo incidente y hace clic en **Ver detalles completos** o **Acciones** > **Investigar**, a partir de ese momento se encontrará en el contexto de datos del área de trabajo del incidente, no en otros.

## <a name="next-steps"></a>Pasos siguientes
En este documento, aprendió a consultar los incidentes en varias áreas de trabajo de Azure Sentinel simultáneamente, así como a trabajar con ellos. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).

