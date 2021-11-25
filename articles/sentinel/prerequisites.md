---
title: Requisitos previos para la implementación de Microsoft Sentinel
description: Obtenga información sobre las actividades previas a la implementación y los requisitos previos para implementar Microsoft Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: bae2e792ab85ec87f1187c632f9582e9d38e87ed
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521137"
---
# <a name="pre-deployment-activities-and-prerequisites-for-deploying-microsoft-sentinel"></a>Actividades previas a la implementación y requisitos previos para implementar Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

En este artículo se presentan las actividades previas a la implementación y los requisitos previos para implementar Microsoft Sentinel.

## <a name="pre-deployment-activities"></a>Actividades previas a la implementación

Antes de implementar Microsoft Sentinel, se recomienda realizar los pasos siguientes para ayudar a que la implementación se centre en proporcionar el máximo valor posible cuanto antes.

1. Determine qué [orígenes de datos](connect-data-sources.md) necesita y los requisitos de tamaño de datos como ayuda para proyectar con precisión el presupuesto y la escala de tiempo de la implementación.

    Puede determinar esta información durante la revisión del caso de uso empresarial o mediante la evaluación de una solución de SIEM actual que ya tenga en funcionamiento. Si ya tiene una solución de SIEM en funcionamiento, analice los datos para comprender qué orígenes de datos proporcionan el máximo valor y se deben incluir en Microsoft Sentinel.

1. Diseñe el área de trabajo de Microsoft Sentinel. Tenga en cuenta parámetros como los siguientes:

    - Si va a usar un solo inquilino o varios inquilinos
    - Cualquier requisito de cumplimiento que tenga para la recopilación y el almacenamiento de datos
    - Control del acceso a los datos de Microsoft Sentinel

    Para obtener más información, consulte [Procedimientos recomendados de arquitectura de áreas de trabajo de Azure Sentinel](best-practices-workspace-architecture.md) y [Diseños de área de trabajo de ejemplo de Azure Sentinel](sample-workspace-designs.md).

1. Una vez identificados los casos de uso empresariales, los orígenes de datos y los requisitos de tamaño de datos, [empiece a planear el presupuesto](azure-sentinel-billing.md), teniendo en cuenta las implicaciones de costos de cada escenario planeado.

    Asegúrese de que el presupuesto cubre el costo de la ingesta de datos para Microsoft Sentinel y Azure Log Analytics, los cuadernos de estrategias que se implementarán, etcétera.

    Para más información, consulte:

    - [Costos y facturación de Microsoft Sentinel](azure-sentinel-billing.md)
    - [Precios de Microsoft Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)
    - [Precios de Log Analytics](https://azure.microsoft.com/pricing/details/monitor/)
    - [Precios de Logic Apps (cuadernos de estrategias)](https://azure.microsoft.com/pricing/details/logic-apps/)
    - [Integración con Azure Data Explorer para conservar registros a largo plazo](store-logs-in-azure-data-explorer.md)

1. Designe a un ingeniero o arquitecto para que dirija la implementación, en función de los requisitos y las escalas de tiempo. Esta persona debe dirigir la implementación y ser el punto de contacto principal del equipo.

## <a name="azure-tenant-requirements"></a>Requisitos del inquilino de Azure

Antes de implementar Microsoft Sentinel, asegúrese de que el inquilino de Azure cumple los siguientes requisitos:

- Se necesita una [licencia y un inquilino de Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md) o una [cuenta individual con un método de pago válido](https://azure.microsoft.com/free/) para acceder a Azure e implementar recursos.

- Una vez que tenga un inquilino, debe tener una [suscripción a Azure](../cost-management-billing/manage/create-subscription.md) para realizar el seguimiento de la creación y facturación de recursos.

- Una vez que tenga una suscripción, necesitará los [permisos pertinentes](../role-based-access-control/index.yml) para empezar a usarla. Si usa una nueva suscripción, se debe designar a un administrador o superior del inquilino de AAD como [propietario o colaborador](../role-based-access-control/rbac-and-directory-admin-roles.md) de la suscripción.

    - Para mantener el acceso con privilegios mínimos disponible, asigne roles en el nivel del grupo de recursos.
    - Para disponer de más control sobre los permisos y el acceso, configure roles personalizados. Para más información, vea [Control de acceso basado en roles](../role-based-access-control/custom-roles.md).
    - Para una mayor separación entre los usuarios y los usuarios de seguridad, es posible que desee usar [RBAC de contexto de recursos](resource-context-rbac.md) o de [nivel de tabla](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043).

    Para más información sobre otros roles y permisos admitidos en Microsoft Sentinel, consulte [Permisos en Microsoft Sentinel](roles.md).

- Se necesita un [área de trabajo de Log Analytics](../azure-monitor/logs/quick-create-workspace.md) para alojar todos los datos que Microsoft Sentinel va a ingerir y usar para sus detecciones, análisis y otras características. Para obtener más información, consulte [Procedimientos recomendados de arquitectura de áreas de trabajo de Microsoft Sentinel](best-practices-workspace-architecture.md).

> [!TIP]
> Al configurar el área de trabajo de Microsoft Sentinel, [cree un grupo de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md) dedicado a Microsoft Sentinel y los recursos que Microsoft Sentinel usa, incluidos el área de trabajo de Log Analytics, los cuadernos de estrategias, los libros, etcétera.
>
> Un grupo de recursos dedicado permite asignar permisos una vez, en el nivel de grupo de recursos, y que los permisos se apliquen automáticamente a los recursos correspondientes. La administración del acceso por medio de un grupo de recursos ayuda a garantizar que se usa Microsoft Sentinel de forma eficaz sin emitir permisos potencialmente inadecuados. Sin un grupo de recursos para Microsoft Sentinel, con los recursos dispersos entre varios grupos de recursos, es posible que un usuario o una entidad de servicio no puedan realizar una acción necesaria ni ver los datos debido a que les falten los permisos suficientes.
>
> Para implementar un mayor control de acceso sobre los recursos por niveles, use grupos de recursos adicionales para hospedar los recursos a los que solo deben acceder esos grupos. El uso de varios niveles de grupos de recursos permite separar el acceso entre esos niveles.
>

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
>[Incorporación a Microsoft Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtención de visibilidad sobre las alertas](get-visibility.md)
