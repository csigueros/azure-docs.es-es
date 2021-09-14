---
title: Acceso a una cuenta de Azure Purview protegida
description: Más información sobre cómo acceder a una cuenta de Azure Purview protegida por firewall mediante puntos de conexión privados desde Synapse
author: linda33wj
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: how-to
ms.date: 09/02/2021
ms.author: jingwang
ms.reviewer: jrasnick
ms.openlocfilehash: e147ee8cd6483ab32fee0fe393b104669370ab5c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440232"
---
# <a name="access-a-secured-azure-purview-account-from-azure-synapse-analytics"></a>Acceso a una cuenta protegida de Azure Purview desde Azure Synapse Analytics

En este artículo se describe cómo acceder a una cuenta de Azure Purview protegida desde Azure Synapse Analytics para distintos escenarios de integración.

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Escenarios de implementación de puntos de conexión privados de Azure Purview

Puede usar [puntos de conexión privados de Azure](../../private-link/private-endpoint-overview.md) para las cuentas de Azure Purview a fin de permitir el acceso seguro desde una red virtual (VNet) al catálogo mediante una instancia de Private Link. Purview proporciona diferentes tipos de puntos privados para diversas necesidades de acceso: punto de conexión privado de *cuenta*, punto de conexión privado del *portal* y puntos de conexión privados de *ingesta*. Obtenga más información en [Información general conceptual sobre los puntos de conexión privados de Purview](../../purview/catalog-private-link.md#conceptual-overview). 

Si la cuenta de Purview está protegida por el firewall y deniega el acceso público, asegúrese de seguir la lista de comprobación siguiente para configurar los puntos de conexión privados a fin de que Synapse se pueda conectar correctamente a Purview. 

| Escenario                                                     | Puntos de conexión privados de Azure Purview                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Ejecución del linaje de canalizaciones e informes en Purview](../../purview/how-to-lineage-azure-synapse-analytics.md) | Para que la canalización de Synapse inserte linaje en Purview, se necesitan los puntos de conexión privados de ***cuenta** _ e _*_ingesta_*_ de Purview. <br>- Al usar _*Azure Integration Runtime**, siga los pasos descritos en la sección [Puntos de conexión privados administrados para Purview](#managed-private-endpoints-for-purview) para crear puntos de conexión privados administrados en la red virtual administrada de Synapse.<br>- Al usar el **entorno de ejecución de integración autohospedado**, siga los pasos de [esta sección](../../purview/catalog-private-link-end-to-end.md#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts) para crear los puntos de conexión privados de *cuenta* e *ingesta* en la red virtual del entorno de ejecución de integración. |
| [Detección y exploración de datos mediante Purview en Synapse Studio](how-to-discover-connect-analyze-azure-purview.md) | Para usar la barra de búsqueda en el centro superior de Synapse Studio para buscar datos de Purview y realizar acciones, debe crear los puntos de conexión privados de ***cuenta** _ y _*_portal_*_ de Purview en la red virtual en la que se inicia Synapse Studio. Siga los pasos descritos en [Habilitación del punto de conexión privado de _cuenta* y *portal*](../../purview/catalog-private-link-account-portal.md#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts). |

## <a name="managed-private-endpoints-for-purview"></a>Puntos de conexión privados administrados para Purview

Los [puntos de conexión privados administrados](../security/synapse-workspace-managed-private-endpoints.md) son puntos de conexión privados creados en una red virtual administrada asociada al área de trabajo de Azure Synapse. Al ejecutar el linaje de canalizaciones e informes en una cuenta de Azure Purview protegida por el firewall, asegúrese de que el área de trabajo de Synapse se crea con la opción "Red virtual administrada" habilitada y, después, cree los puntos de conexión privados administrados de ***cuenta** _ e _ *_ingesta_** de Purview como se muestra a continuación.

### <a name="create-managed-private-endpoints"></a>Creación de puntos de conexión privados administrados

Para crear puntos de conexión privados administrados para Purview en Synapse Studio:

1. Vaya a **Administrar** -> **Azure Purview** y haga clic en **Editar** para editar la cuenta de Purview conectada existente, o bien haga clic en **Connect to a Purview account** (Conectar a una cuenta de Purview) para conectarse a una cuenta nueva de Purview.

2. Seleccione **Sí** en **Create managed private endpoints** (Crear puntos de conexión privados administrados). Debe tener el permiso "**workspaces/managedPrivateEndpoint/write**", por ejemplo, el rol Administrador de Synapse o Administrador de datos vinculados de Synapse.

3. Haga clic en el botón **+ Crear todo** para crear por lotes los puntos de conexión privados de Purview necesarios, incluidos los de **_cuenta_ *_ e _* _ingesta_ *_ para los recursos administrados de Purview: Blob Storage, Queue Storage y espacio de nombres de Event Hubs. Debe tener al menos el rol _* Lector** en la cuenta de Purview para que Synapse recupere la información de los recursos administrados de Purview.

   :::image type="content" source="./media/purview-create-all-managed-private-endpoints.png" alt-text="Creación de un punto de conexión privado administrado para la cuenta de Purview conectada.":::

4. En la página siguiente, especifique un nombre para el punto de conexión privado. Se usará a fin de generar nombres para los puntos de conexión privados de ingesta, así como con el sufijo.

   :::image type="content" source="./media/name-purview-private-endpoints.png" alt-text="Asignación de nombres para los puntos de conexión privados administrados para la cuenta de Purview conectada.":::

5. Haga clic en **Crear** para crear los puntos de conexión privados. Después de la creación, se generarán cuatro solicitudes de punto de conexión privado que deben [ser aprobadas por un propietario de Purview](#approve-private-endpoint-connections).

Esta creación de puntos de conexión privados administrados por lotes solo se proporciona en Synapse Studio. Si quiere crear los puntos de conexión privados administrados mediante programación, debe hacerlo de forma individual. Puede encontrar la información de los recursos administrados de Purview en Azure Portal -> cuenta de Purview -> Recursos administrados.

### <a name="approve-private-endpoint-connections"></a>Aprobación de las conexiones de punto de conexión privado

Después de crear los puntos de conexión privados administrados para Purview, verá primero el estado "Pendiente". El propietario de Purview debe aprobar las conexiones de punto de conexión privado para cada recurso.

Si tiene permiso para aprobar la conexión de punto de conexión privado de Purview, desde Synapse Studio: 

1. Vaya a **Administrar** -> **Azure Purview** -> **Editar**.
2. En la lista de puntos de conexión privados, haga clic en el botón **Editar** (lápiz) situado junto al nombre de cada punto de conexión privado.
3. Haga clic en **Manage approvals in Azure portal** (Administrar aprobaciones en Azure Portal) para acceder al recurso.
4. En el recurso especificado, vaya a **Redes** -> **Conexión de punto de conexión privado** para aprobarlo. El punto de conexión privado se denomina `data_factory_name.your_defined_private_endpoint_name` con la descripción "Solicitado por nombre_de_la_factoría_de_datos".
5. Repita esta operación para todos los puntos de conexión privados.

Si no tiene permiso para aprobar la conexión del punto de conexión privado de Purview, pida al propietario de la cuenta de Purview que haga lo siguiente.

- Para el punto de conexión privado de *cuenta*, vaya a Azure Portal -> cuenta de Purview -> Redes -> Private endpoint connection to approve (Conexión de punto de conexión privado para aprobar).
- Para los puntos de conexión privados de *ingesta*, vaya Azure Portal -> la cuenta de Purview -> Recursos administrados, haga clic en la cuenta de Storage y el espacio de nombres de Event Hubs, respectivamente, y apruebe la conexión de punto de conexión privado en la página Redes > Conexión de punto de conexión privado.

### <a name="monitor-managed-private-endpoints"></a>Supervisión de puntos de conexión privados administrados

Puede supervisar los puntos de conexión privados administrados creados para Purview en dos lugares:

- Vaya a **Administración** -> **Azure Purview** -> **Editar** para abrir la cuenta de Purview conectada existente. Para ver todos los puntos de conexión privados pertinentes, debe tener al menos el rol **Lector** en la cuenta de Purview para que Synapse recupere la información de los recursos administrados de Purview. De lo contrario, solo verá el punto de conexión privado de *cuenta* con una advertencia.
- Vaya a **Administrar** -> **Administrar puntos de conexión privados administrados**, donde verá todos los puntos de conexión privados administrados creados en el área de trabajo de Synapse. Si tiene al menos el rol **Lector** en la cuenta de Purview, verá los puntos de conexión privados pertinentes de Purview agrupados. De lo contrario, se muestran por separado en la lista.

## <a name="nextsteps"></a>Pasos siguientes 

- [Conexión de un área de trabajo de Synapse a Azure Purview](quickstart-connect-azure-purview.md)
- [Metadatos y linaje de Azure Synapse Analytics](../../purview/how-to-lineage-azure-synapse-analytics.md)
- [Detección, conexión y exploración de datos en Synapse con Azure Purview](how-to-discover-connect-analyze-azure-purview.md)