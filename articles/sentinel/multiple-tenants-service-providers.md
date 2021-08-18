---
title: Administración de varios inquilinos en Azure Sentinel como proveedor de servicios de seguridad administrados | Microsoft Docs
description: Cómo incorporar y administrar varios inquilinos en Azure Sentinel como un proveedor de servicios de seguridad administrados (MSSP) mediante Azure Lighthouse.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: cf2a9380b404876ef9c963d54d9069ebd66fef56
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725147"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Administración de varios inquilinos en Azure Sentinel como un MSSP

Si es un proveedor de servicios de seguridad administrados (MSSP) y usa [Azure Lighthouse](../lighthouse/overview.md) para ofrecer a los clientes servicios del Centro de operaciones de seguridad (SOC), puede administrar directamente los recursos de Azure Sentinel de sus clientes desde su inquilino de Azure, sin necesidad de conectarse al inquilino del cliente. 

## <a name="prerequisites"></a>Prerrequisitos

- [Incorporación de Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)

- Para que esto funcione correctamente, el inquilino (de MSSP) debe tener registrados los proveedores de recursos de Azure Sentinel en al menos una suscripción. Además, cada uno de los inquilinos de sus clientes debe tener registrados los proveedores de recursos. Si ha registrado Azure Sentinel en el inquilino, y los clientes en los suyos, está listo para empezar. Para comprobar el registro, siga estos pasos:

    1. Seleccione **Suscripciones** en Azure Portal y, a continuación, seleccione la suscripción relevante en el menú.

    1. En el menú de navegación de la pantalla de suscripción, en **Configuración**, seleccione **Proveedores de recursos**.

    1. En la pantalla ***Nombre de la suscripción* | Proveedores de recursos**, busque y seleccione *Microsoft. OperationalInsights* y *Microsoft. SecurityInsights*. A continuación, compruebe la columna de **estado**. Si el estado del proveedor es *NotRegistered*, seleccione **Registrar**.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Comprobar los proveedores de recursos":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>Procedimientos para obtener acceso a Azure Sentinel en inquilinos administrados

1. En **Directorio + suscripción**, seleccione los directorios delegados (directorio = inquilino) y las suscripciones en las que están ubicadas las áreas de trabajo de Azure Sentinel del cliente.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Elección de inquilinos y suscripciones":::

1. Abra Azure Sentinel. Verá todas las áreas de trabajo de las suscripciones seleccionadas y podrá trabajar con ellas sin problemas, como con cualquier área de trabajo de su propio inquilino.

> [!NOTE]
> No podrá implementar conectores en Azure Sentinel desde un área de trabajo administrada. Para implementar un conector, debe iniciar sesión directamente en el inquilino en el que desea implementar el conector y autenticarlo con los permisos necesarios.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a administrar varios inquilinos de Azure Sentinel sin problemas. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).

