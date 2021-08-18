---
title: Protección contra la filtración de datos en áreas de trabajo de Azure Synapse Analytics
description: En este artículo se explica la protección contra la filtración de datos en Azure Synapse Analytics.
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 792197b3558a16706fee52204fa90a8ef8d0735a
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113232059"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Protección contra la filtración de datos en áreas de trabajo de Azure Synapse Analytics
En este artículo se explica la protección contra la filtración de datos en Azure Synapse Analytics.

## <a name="securing-data-egress-from-synapse-workspaces"></a>Protección de los datos de salida de las áreas de trabajo de Synapse
Las áreas de trabajo de Azure Synapse Analytics admiten la habilitación de la protección contra la filtración de datos. Con la protección contra la filtración, puede protegerse frente a intrusos malintencionados que acceden a los recursos de Azure y extraen datos confidenciales en ubicaciones fuera del ámbito de la organización. En el momento de crear el área de trabajo, puede optar por configurarla con una red virtual administrada y protección adicional contra la filtración de datos. Cuando se crea un área de trabajo con una [red virtual administrada](./synapse-workspace-managed-vnet.md), la integración de datos y los recursos de Spark se implementan en esta. Los grupos de SQL dedicados y los grupos de SQL sin servidor del área de trabajo tienen funcionalidades multiinquilino y, como tal, deben existir fuera de la red virtual administrada. En el caso de las áreas de trabajo con protección contra la filtración de datos, los recursos dentro de la red virtual administrada siempre se comunican a través de [puntos de conexión privados administrados](./synapse-workspace-managed-private-endpoints.md) y los recursos de Synapse SQL solo pueden conectarse a recursos de Azure autorizados (destinos de conexiones de punto de conexión privado administrado aprobados desde el área de trabajo). 

> [!Note]
> Una vez creado el área de trabajo, no se puede cambiar su configuración de la red virtual administrada y de la protección contra la filtración de datos.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>Administración de la salida de datos del área de trabajo de Synapse a destinos aprobados
Una vez que se ha creado el área de trabajo con la protección frente a la filtración de datos habilitada, los propietarios del recurso de área de trabajo pueden administrar la lista de inquilinos de Azure AD aprobados del área de trabajo. Los usuarios con los [permisos adecuados](./synapse-workspace-access-control-overview.md) en el área de trabajo pueden usar Synapse Studio para crear solicitudes de conexión de punto de conexión privado administrado a los recursos de los inquilinos de Azure AD aprobados del área de trabajo. La creación de puntos de conexión privados administrados se bloqueará si el usuario intenta crear una conexión de punto de conexión privado a un recurso de un inquilino no aprobado.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>Área de trabajo de ejemplo con la protección contra la filtración de datos habilitada
Vamos a usar un ejemplo para ilustrar la protección contra la filtración de datos en las áreas de trabajo de Synapse. Contoso tiene recursos de Azure en el inquilino A y el inquilino B, y es necesario que estos recursos se conecten de forma segura. Se ha creado un área de trabajo de Synapse en el inquilino A y se ha agregado el inquilino B como inquilino de Azure AD aprobado. En el diagrama se muestran las conexiones de punto de conexión privado a cuentas de Azure Storage en el inquilino A y el inquilino B que han aprobado los propietarios de la cuenta de almacenamiento. En el diagrama también se muestra la creación de puntos de conexión privados bloqueados. La creación de este punto de conexión privado se bloqueó dado que el destino era una cuenta de Azure Storage en el inquilino de Azure AD de Fabrikam, que no es un inquilino de Azure AD aprobado en el área de trabajo de Contoso.

:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="En este diagrama se muestra cómo se implementa la protección contra la filtración de datos en las áreas de trabajo de Synapse":::

>[!IMPORTANT]
>
> Los recursos de inquilinos que no sean el inquilino del área de trabajo no deben tener implantadas reglas de firewall que impidan que los grupos de SQL se conecten a ellos. Los recursos de la red virtual administrada del área de trabajo, como los clústeres de Spark, pueden conectarse a recursos protegidos mediante firewall a través de vínculos privados administrados.
> >

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [crear un área de trabajo con la protección contra la filtración de datos habilitada](./how-to-create-a-workspace-with-data-exfiltration-protection.md)

Más información sobre [Red virtual del área de trabajo administrada](./synapse-workspace-managed-vnet.md)

Más información sobre los [puntos de conexión privados administrados](./synapse-workspace-managed-private-endpoints.md)

[Creación de puntos de conexión privados administrados a los orígenes de datos](./how-to-create-managed-private-endpoints.md)
