---
title: Conectar un área de trabajo de Synapse a Azure Purview 
description: Conectar un área de trabajo de Synapse a una cuenta de Azure Purview.
author: Jejiang
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: quickstart
ms.date: 09/29/2021
ms.author: jejiang
ms.reviewer: jrasnick
ms.openlocfilehash: 894df32142cf29e59e40b1e9218f4090bbda93f0
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351623"
---
# <a name="quickstartconnect-a-synapse-workspace-to-an-azure-purview-account"></a>Inicio rápido: Conectar un área de trabajo de Synapse a una cuenta de Azure Purview

En este inicio rápido, registrará una cuenta de Azure Purview en un área de trabajo de Synapse. Esa conexión le permite detectar recursos de Azure Purview e interactuar con ellos mediante las funcionalidades de Synapse, e insertar información de linaje a Purview.

Puede realizar las siguientes tareas en Synapse:
- Usar el cuadro de búsqueda de la parte superior para buscar recursos de Purview basados en palabras clave. 
- Descripción de los datos basados en metadatos, [linaje](../../purview/catalog-lineage-user-guide.md) y anotaciones 
- Conectar esos datos al área de trabajo con servicios vinculados o conjuntos de datos de integración. 
- Analizar esos conjuntos de datos con Synapse Apache Spark, Synapse SQL y Data Flow 
- Ejecutar canalizaciones e [insertar información de linaje en Purview](../../purview/how-to-lineage-azure-synapse-analytics.md).

## <a name="prerequisites"></a>Requisitos previos 
- [Cuenta de Azure Purview](../../purview/create-catalog-portal.md) 
- [Área de trabajo de Synapse](../quickstart-create-workspace.md) 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Permisos para conectar una cuenta de Azure Purview 

Para conectar una cuenta de Azure Purview a un área de trabajo de Synapse, necesita un rol **Colaborador** en dicha área otorgado por la Administración de identidad y acceso de Azure Portal y necesita acceso a esa cuenta de Azure Purview. Para más información, consulte [Permisos de Azure Purview](../../purview/catalog-permissions.md).

## <a name="connect-an-azure-purview-account"></a>Conexión de una cuenta de Azure Purview  

Siga los pasos para conectar una cuenta de Azure Purview:

1. Vaya a  [https://web.azuresynapse.net](https://web.azuresynapse.net) e inicie sesión en el área de trabajo de Synapse. 
2. Vaya a **Administrar** -> **Azure Purview**, seleccione **Conectarse a una cuenta de Purview**.
3. Puede elegir **A partir de una suscripción de Azure** o **Especificar manualmente**. En **A partir de una suscripción de Azure**, puede seleccionar la cuenta a la que tiene acceso.
4. Una vez conectado, puede ver el nombre de la cuenta de Purview en la pestaña **Cuenta de Azure Purview**. 

Si la cuenta de Purview está protegida por el firewall, cree los puntos de conexión privados administrados para Purview. Obtenga más información sobre cómo permitir que Azure Synapse [acceda a una cuenta de Purview protegida](how-to-access-secured-purview-account.md). Puede hacerlo durante la conexión inicial o editar una conexión existente más adelante.

La información de conexión de Purview se almacena en el recurso del área de trabajo de Synapse, como se muestra a continuación. Para establecer la conexión mediante programación, puede actualizar el área de trabajo de Synapse y agregar la configuración `purviewConfiguration`.

```json
{
    "name": "ContosoSynapseWorkspace",
    "type": "Microsoft.Synapse/workspaces",
    "location": "<region>",
    "properties": {
        ...
        "purviewConfiguration": {
            "purviewResourceId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.Purview/accounts/<PurviewAccountName>"
        }
    },
    "identity": {...},
    ...
}
```

## <a name="set-up-authentication"></a>Configuración de la autenticación

La identidad administrada del área de trabajo de Synapse se usa para autenticar las operaciones de inserción de linaje desde el área de trabajo de Synapse a Purview.

- En el caso de la cuenta de Purview creada el **18 de agosto de 2021 o después**, conceda el rol **Conservador de datos** de la identidad administrada del área de trabajo de Synapse en la **colección raíz** de Purview. Obtenga más información sobre el [control de acceso en Azure Purview](../../purview/catalog-permissions.md) y la [adición de roles y restricción del acceso mediante colecciones](../../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections).

    Al conectar el área de trabajo de Synapse a Purview en Synapse Studio, Synapse intenta agregar dicha asignación de roles automáticamente. Si tiene el rol **Administradores de colecciones** en la colección raíz de Purview y tiene acceso a la cuenta de Purview desde la red, esta operación se realiza correctamente.

- En el caso de la cuenta de Purview creada **antes del 18 de agosto de 2021**, conceda el rol integrado de Azure [**Conservador de datos de Purview (heredado)** ](../../role-based-access-control/built-in-roles.md#purview-data-curator-legacy) de la identidad administrada del área de trabajo de Synapse en la cuenta de Purview. Obtenga más información sobre el [Control de acceso en Azure Purview: permisos heredados](../../purview/catalog-permissions.md#legacy-permission-guide).

    Al conectar el área de trabajo de Synapse a Purview en Synapse Studio, Synapse intenta agregar dicha asignación de roles automáticamente. Si tiene los roles integrados de Azure **Propietario** o **Administrador de acceso de usuarios** en la cuenta de Purview, esta operación se realiza correctamente.

## <a name="monitor-purview-connection"></a>Supervisión de la conexión de Purview

Una vez que conecte el área de trabajo de Synapse a una cuenta de Purview, verá la página siguiente con detalles sobre las funcionalidades de integración habilitadas.

:::image type="content" source="./media/monitor-purview-connection-status.png" alt-text="Captura de pantalla para supervisar el estado de integración entre Azure Synapse y Purview.":::

En **Data Lineage - Synapse Pipeline** (Linaje de datos: canalización de Synapse), es posible que vea uno de los siguientes estados:

- **Connected** (Conectado): el área de trabajo de Synapse está conectada correctamente a la cuenta de Purview. Tenga en cuenta que esto indica que el área de trabajo de Synapse está asociada a una cuenta de Purview y que tiene permiso para insertar linaje en ella. Si la cuenta de Purview está protegida mediante firewall, también debe asegurarse de que el entorno de ejecución de integración usado para ejecutar las actividades y realizar la inserción de linaje puede llegar a la cuenta de Purview. Obtendrá más información al respecto en [Acceso a una cuenta de Azure Purview protegida](how-to-access-secured-purview-account.md).
- **Disconnected** (Desconectado): el área de trabajo de Synapse no puede insertar linaje en Purview porque no se ha concedido el rol de administrador de datos de Purview a la identidad administrada del área de trabajo de Synapse. Para corregir este problema, vaya a la cuenta de Purview para comprobar las asignaciones de roles y conceda manualmente el rol según sea necesario. Más información en la sección [Configuración de la autenticación](#set-up-authentication).
- **Unknown** (Desconocido): Azure Synapse no puede comprobar el estado. Los posibles motivos son:

    - No se puede acceder a la cuenta de Purview desde la red actual porque la cuenta está protegida mediante firewall. Puede iniciar Synapse Studio desde una red privada que tenga conectividad con su cuenta de Purview en su lugar.
    - No tiene permiso para comprobar las asignaciones de roles en la cuenta de Purview. Puede ponerse en contacto con el administrador de la cuenta de Purview para que compruebe las asignaciones de roles. Más información sobre el rol de Purview necesario en la sección [Configuración de la autenticación](#set-up-authentication).

## <a name="report-lineage-to-azure-purview"></a>Notificación del linaje a Azure Purview

Una vez que conecte el área de trabajo de Synapse a una cuenta de Purview, cuando se ejecutan canalizaciones, Synapse notifica la información de linaje a la cuenta de Purview. Para obtener más información sobre las funcionalidades admitidas y un tutorial completo, consulte [Metadatos y linaje de Azure Synapse Analytics](../../purview/how-to-lineage-azure-synapse-analytics.md).

## <a name="discover-and-explore-data-using-purview"></a>Detección y exploración de datos mediante Purview

Una vez que conecte el área de trabajo de Synapse a una cuenta de Purview, puede usar la barra de búsqueda en la parte superior central del área de trabajo de Synapse para buscar datos y realizar acciones. Obtenga más información en [Detección, conexión y exploración de datos en Synapse con Azure Purview](how-to-discover-connect-analyze-azure-purview.md).

## <a name="nextsteps"></a>Pasos siguientes 

[Detección, conexión y exploración de datos en Synapse con Azure Purview](how-to-discover-connect-analyze-azure-purview.md)

[Metadatos y linaje de Azure Synapse Analytics](../../purview/how-to-lineage-azure-synapse-analytics.md)

[Acceso a una cuenta de Azure Purview protegida](how-to-access-secured-purview-account.md)

[Registro y análisis de recursos de Azure Synapse en Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)

[Obtención de linaje de Power BI a Azure Purview](../../purview/how-to-lineage-powerbi.md)

[Conexión de Azure Data Share y Azure Purview](../../purview/how-to-link-azure-data-share.md)