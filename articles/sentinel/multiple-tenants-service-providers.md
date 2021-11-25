---
title: Administración de varios inquilinos en Microsoft Sentinel como proveedor de servicios de seguridad administrados | Microsoft Docs
description: Cómo incorporar y administrar varios inquilinos en Microsoft Sentinel como un proveedor de servicios de seguridad administrados (MSSP) mediante Azure Lighthouse.
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
ms.openlocfilehash: a4518498edf3d2da14c09d396aff56542d61478f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132713018"
---
# <a name="manage-multiple-tenants-in-microsoft-sentinel-as-an-mssp"></a>Administración de varios inquilinos en Microsoft Sentinel como un MSSP

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Si es un proveedor de servicios de seguridad administrados (MSSP) y usa [Azure Lighthouse](../lighthouse/overview.md) para ofrecer a los clientes servicios del Centro de operaciones de seguridad (SOC), puede administrar directamente los recursos de Microsoft Sentinel de sus clientes desde su inquilino de Azure, sin necesidad de conectarse al inquilino del cliente. 

## <a name="prerequisites"></a>Prerrequisitos

- [Incorporación de Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)

- Para que esto funcione correctamente, el inquilino (de MSSP) debe tener registrados los proveedores de recursos de Microsoft Sentinel en al menos una suscripción. Además, cada uno de los inquilinos de sus clientes debe tener registrados los proveedores de recursos. Si ha registrado Microsoft Sentinel en el inquilino, y los clientes en los suyos, está listo para empezar. Para comprobar el registro, siga estos pasos:

    1. Seleccione **Suscripciones** en Azure Portal y, a continuación, seleccione la suscripción relevante en el menú.

    1. En el menú de navegación de la pantalla de suscripción, en **Configuración**, seleccione **Proveedores de recursos**.

    1. En la pantalla ***Nombre de la suscripción* | Proveedores de recursos**, busque y seleccione *Microsoft. OperationalInsights* y *Microsoft. SecurityInsights*. A continuación, compruebe la columna de **estado**. Si el estado del proveedor es *NotRegistered*, seleccione **Registrar**.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Comprobar los proveedores de recursos":::

## <a name="how-to-access-microsoft-sentinel-in-managed-tenants"></a>Procedimientos para obtener acceso a Microsoft Sentinel en inquilinos administrados

1. En **Directorio + suscripción**, seleccione los directorios delegados (directorio = inquilino) y las suscripciones en las que están ubicadas las áreas de trabajo de Microsoft Sentinel del cliente.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Elección de inquilinos y suscripciones":::

1. Abra Microsoft Sentinel. Verá todas las áreas de trabajo de las suscripciones seleccionadas y podrá trabajar con ellas sin problemas, como con cualquier área de trabajo de su propio inquilino.

> [!NOTE]
> No podrá implementar conectores en Microsoft Sentinel desde un área de trabajo administrada. Para implementar un conector, debe iniciar sesión directamente en el inquilino en el que desea implementar el conector y autenticarlo con los permisos necesarios.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a administrar varios inquilinos de Azure Sentinel sin problemas. Para obtener más información sobre Microsoft Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Microsoft Sentinel](detect-threats-built-in.md).
