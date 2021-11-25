---
title: Uso de análisis de comportamiento de entidades para detectar amenazas avanzadas | Microsoft Docs
description: Habilite el análisis del comportamiento de usuarios y entidades de Microsoft Sentinel y configure orígenes de datos
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: cff89d82c96cd20db59e0ed0de77f988dacead90
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721354"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-microsoft-sentinel"></a>Habilitación del análisis de comportamiento de usuarios y entidades (UEBA) en Microsoft Sentinel 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Las características UEBA y Páginas de entidad ya tienen **disponibilidad general** en **_todas_** las zonas geográficas y regiones de Microsoft Sentinel. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Requisitos previos

Para habilitar o deshabilitar esta característica (estos requisitos previos no son necesarios para usar la característica):

- El usuario debe ser miembro de la instancia de Azure Active Directory de su organización, no un usuario invitado.

- El usuario debe tener asignados los roles **Administrador global** o **Administrador de seguridad** en Azure AD.

- El usuario debe tener asignado al menos uno de los siguientes **roles de Azure** ([más información sobre RBAC de Azure](roles.md)):
    - **Colaborador de Microsoft Sentinel** en los niveles de área de trabajo o grupo de recursos.
    - **Colaborador de Log Analytics** en los niveles de grupo de recursos o suscripción.

- El área de trabajo no debe tener ningún bloqueo de recursos de Azure aplicado. [Más información sobre el bloqueo de recursos de Azure](../azure-resource-manager/management/lock-resources.md).

> [!NOTE]
> No se requiere ninguna licencia especial para agregar la funcionalidad UEBA a Microsoft Sentinel, aunque se pueden aplicar **cargos adicionales**.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Habilitación del análisis de comportamiento de usuarios y entidades

1. En el menú de navegación de Microsoft Sentinel, seleccione **Comportamiento de la entidad**.

1. En el encabezado **Turn it on** (Activarlo), cambie el control de alternancia a **On** (Activar).

1. Haga clic en el botón **Select data sources** (Seleccionar orígenes de datos).

1. En el panel **selección de orígenes de datos**, active las casillas situadas junto a los orígenes de datos en los que quiere habilitar UEBA y, después, seleccione **Apply** (Aplicar).

    > [!NOTE]
    >
    > En la mitad inferior del panel de **selección de orígenes de datos**, verá una lista de orígenes de datos compatibles con UEBA que aún no ha habilitado. 
    >
    > Una vez que haya habilitado UEBA, tendrá la opción de conectar nuevos orígenes de datos para habilitarlos para UEBA directamente desde el panel del conector de datos si son compatibles con UEBA.

1. Seleccione **Go to entity search** (Ir a búsqueda de entidades). Esto le lleva al panel de búsqueda de entidades, que, desde ahora, es lo que va a ver cuando seleccione **Comportamiento de la entidad** en el menú principal de Microsoft Sentinel.

## <a name="next-steps"></a>Pasos siguientes
En este documento ha aprendido a habilitar y configurar el análisis de comportamiento de usuarios y entidades (UEBA) en Microsoft Sentinel. Para obtener más información sobre Microsoft Sentinel, vea los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Microsoft Sentinel](detect-threats-built-in.md).
