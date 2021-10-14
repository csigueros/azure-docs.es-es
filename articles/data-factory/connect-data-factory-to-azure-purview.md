---
title: Conexión de Data Factory a Azure Purview
description: Información sobre la conexión de Data Factory a Azure Purview
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 09/27/2021
ms.openlocfilehash: 6ec86ff575e4848f79dfe8c6e444e47ed6d1f8f1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277904"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Conexión de Data Factory a Azure Purview (versión preliminar)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Purview](../purview/overview.md) es un servicio de gobernanza de datos unificado que le ayuda a administrar y controlar sus datos locales, multinube y de software como servicio (SaaS). Puede conectar su factoría de datos a Azure Purview. Esta conexión permite usar Azure Purview para capturar datos de linaje y detectar y explorar los recursos de Azure Purview.

## <a name="connect-data-factory-to-azure-purview"></a>Conexión de Data Factory a Azure Purview

Tiene dos maneras de conectar Data Factory a Azure Purview:

- [Conectar una cuenta de Azure Purview en Data Factory](#connect-to-azure-purview-account-in-data-factory).
- [Registro de Data Factory en Azure Purview](#register-data-factory-in-azure-purview)

### <a name="connect-to-azure-purview-account-in-data-factory"></a>Conexión de una cuenta de Azure Purview en Data Factory

Debe tener los roles **Propietario** o **Colaborador** en su factoría de datos para conectarse a una cuenta de Azure Purview.

Para establecer la conexión en la interfaz de usuario de creación de Data Factory:

1. En la interfaz de usuario de creación de Azure Data Factory, vaya a **Administrar** -> **Azure Purview** y  seleccione **Conectarse a una cuenta de Purview**. 

    :::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Captura de pantalla para registrar una cuenta de Purview.":::

2. Elija **A partir de una suscripción de Azure** o **Especificar manualmente**. En **A partir de una suscripción de Azure**, puede seleccionar la cuenta a la que tiene acceso.

3. Una vez que se haya conectado, puede ver el nombre de la cuenta de Purview en la pestaña **Cuenta de Purview**.

Si la cuenta de Purview está protegida por el firewall, cree los puntos de conexión privados administrados para Purview. Obtenga más información sobre cómo permitir que Data Factory [acceda a una cuenta de Purview protegida](how-to-access-secured-purview-account.md). Puede hacerlo durante la conexión inicial o editar una conexión existente más adelante.

La información de conexión de Purview se almacena en el recurso de la factoría de datos, como se muestra a continuación. Para establecer la conexión mediante programación, puede actualizar la factoría de datos y agregar la configuración `purviewConfiguration`. Si quiere insertar linaje de las actividades de SSIS, agregue también la etiqueta `catalogUri`.

```json
{
    "name": "ContosoDataFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>",
    "properties": {
        ...
        "purviewConfiguration": {
            "purviewResourceId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.Purview/accounts/<PurviewAccountName>"
        }
    },
    ...
    "identity": {...},
    "tags": {
        "catalogUri": "<PurviewAccountName>.catalog.purview.azure.com //Note: used for SSIS lineage only"
    }
}
```

### <a name="register-data-factory-in-azure-purview"></a>Registro de Data Factory en Azure Purview

Para obtener información sobre cómo registrar Data Factory en Azure Purview, consulte [Cómo conectar Azure Data Factory y Azure Purview](../purview/how-to-link-azure-data-factory.md).

## <a name="set-up-authentication"></a>Configuración de la autenticación

La identidad administrada de la factoría de datos se usa para autenticar las operaciones de inserción de linaje desde la factoría de datos a Purview. 

- En el caso de la cuenta de Purview creada el **18 de agosto de 2021 o después**, conceda el rol **Conservador de datos** de la identidad administrada de la factoría de datos en la **colección raíz** de Purview. Obtenga más información sobre el [control de acceso en Azure Purview](../purview/catalog-permissions.md) y la [adición de roles y restricción del acceso mediante colecciones](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections).

    Al conectar la factoría de datos a Purview en la interfaz de usuario de creación, ADF intenta agregar esta asignación de roles automáticamente. Si tiene el rol **Administradores de colecciones** en la colección raíz de Purview y tiene acceso a la cuenta de Purview desde la red, esta operación se realiza correctamente.

- En la cuenta de Purview creada **antes del 18 de agosto de 2021**, conceda el rol integrado de Azure [**Conservador de datos de Purview (heredado)** ](../role-based-access-control/built-in-roles.md#purview-data-curator-legacy) de la identidad administrada de la factoría de datos en la cuenta de Purview. Obtenga más información sobre el [Control de acceso en Azure Purview: permisos heredados](../purview/catalog-permissions.md#legacy-permission-guide).

    Al conectar la factoría de datos a Purview en la interfaz de usuario de creación, ADF intenta agregar esta asignación de roles automáticamente. Si tiene los roles integrados de Azure **Propietario** o **Administrador de acceso de usuarios** en la cuenta de Purview, esta operación se realiza correctamente.

## <a name="monitor-purview-connection"></a>Supervisión de la conexión de Purview

Una vez que conecte la factoría de datos a una cuenta de Purview, verá la página siguiente con detalles sobre las funcionalidades de integración habilitadas.

:::image type="content" source="./media/data-factory-purview/monitor-purview-connection-status.png" alt-text="Captura de pantalla para supervisar el estado de integración entre Azure Data Factory y Purview.":::

En **Data Lineage - Pipeline** (Linaje de datos: canalización), es posible que vea uno de los siguientes estados:

- **Conectado**: la factoría de datos está conectada correctamente a la cuenta de Purview. Tenga en cuenta que esto indica que la factoría de datos está asociada a una cuenta de Purview y que tiene permiso para insertar linaje en ella. Si la cuenta de Purview está protegida mediante firewall, también debe asegurarse de que el entorno de ejecución de integración usado para ejecutar las actividades y realizar la inserción de linaje puede llegar a la cuenta de Purview. Más información en [Acceso a una cuenta protegida de Azure Purview desde Azure Data Factory](how-to-access-secured-purview-account.md).
- **Desconectado**: la factoría de datos no puede insertar linaje en Purview porque no se ha concedido el rol de administrador de datos de Purview a la identidad administrada de la factoría de datos. Para corregir este problema, vaya a la cuenta de Purview para comprobar las asignaciones de roles y conceda manualmente el rol según sea necesario. Más información en la sección [Configuración de la autenticación](#set-up-authentication).
- **Desconocido**: Data Factory no puede comprobar el estado. Los posibles motivos son:

    - No se puede acceder a la cuenta de Purview desde la red actual porque la cuenta está protegida mediante firewall. Puede iniciar la interfaz de usuario de ADF desde una red privada que tenga conectividad con su cuenta de Purview en su lugar.
    - No tiene permiso para comprobar las asignaciones de roles en la cuenta de Purview. Puede ponerse en contacto con el administrador de la cuenta de Purview para que compruebe las asignaciones de roles. Más información sobre el rol de Purview necesario en la sección [Configuración de la autenticación](#set-up-authentication).

## <a name="report-lineage-data-to-azure-purview"></a>Notificación de datos de linaje a Azure Purview

Una vez que conecte la factoría de datos a una cuenta de Purview, cuando ejecute las canalizaciones, la factoría de datos insertará la información de linaje a la cuenta de Purview. Para obtener más información sobre las funcionalidades admitidas, consulte [Actividades admitidas de Azure Data Factory](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities). Para ver un tutorial completo, consulte [Tutorial: Inserción de datos de linaje de Data Factory en Azure Purview](tutorial-push-lineage-to-purview.md).

## <a name="discover-and-explore-data-using-purview"></a>Detección y exploración de datos mediante Purview

Una vez conectada la factoría de datos a una cuenta de Purview, puede usar la barra de búsqueda ubicada en la parte superior central de la interfaz de usuario de creación de Data Factory para buscar datos y realizar acciones. Para obtener más información, consulte [Detección y exploración de datos en ADF mediante Purview](how-to-discover-explore-purview-data.md).

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Inserción de datos de linaje de Data Factory en Azure Purview](tutorial-push-lineage-to-purview.md)

[Detección y exploración de datos en ADF mediante Purview](how-to-discover-explore-purview-data.md)

[Acceso a una cuenta de Azure Purview protegida](how-to-access-secured-purview-account.md)
