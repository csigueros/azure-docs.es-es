---
title: Conexión a Azure Data Factory
description: En este artículo se explica cómo conectar Azure Data Factory y Azure Purview para realizar un seguimiento del linaje de datos.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 5ea3b24cbdbd4a4c9a1f11883e8a2231467a7ee3
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278581"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Cómo conectar Azure Data Factory y Azure Purview

En este documento se explican los pasos necesarios para conectar una cuenta de Azure Data Factory con una de Azure Purview para realizar un seguimiento del linaje de datos. El documento también profundiza en el ámbito de cobertura y los patrones de linaje admitidos.

## <a name="view-existing-data-factory-connections"></a>Visualización de conexiones existentes de Data Factory

Es posible conectar varias instancias de Azure Data Factory a una sola de Azure Purview para enviar información de linaje. El límite actual permite conectar hasta 10 cuentas de Data Factory a la vez desde el centro de administración de Purview. Para mostrar la lista de cuentas de Data Factory conectadas a la de Purview, siga estos pasos:

1. En el panel de navegación izquierdo, seleccione **Administración**.
2. En **Lineage connections** (Conexiones de linaje), seleccione **Data Factory**.
3. Aparece la lista de conexiones de Data Factory.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Captura de pantalla que muestra una lista de conexiones de Data Factory." lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. Observe los distintos valores de **Estado** de la conexión:

    - **Conectado**: la factoría de datos está conectada a la cuenta de Purview.
    - **Desconectado**: la factoría de datos tiene acceso al catálogo, pero está conectada a otro catálogo. Como resultado, el linaje de datos no se comunica al catálogo automáticamente.
    - **CannotAccess**: el usuario actual no tiene acceso a la factoría de datos, por lo que se desconoce el estado de la conexión.

>[!Note]
>Para ver las conexiones de Data Factory, debe tener asignado el rol siguiente. No se admite la herencia de roles del grupo de administración.
>- Para la cuenta de Purview creada el **18 de agosto de 2021 o después**: rol **Administradores de colecciones** en la colección raíz.
>- Para la cuenta de Purview creada **antes del 18 de agosto de 2021**: rol integrado de Azure **Propietario**, **Colaborador**, **Lector** o **Administrador de acceso de usuario**.

## <a name="create-new-data-factory-connection"></a>Creación de una nueva conexión de Data Factory

>[!Note]
>Para agregar o quitar las conexiones de Data Factory, debe tener asignado el rol siguiente. No se admite la herencia de roles del grupo de administración.
>- Para la cuenta de Purview creada el **18 de agosto de 2021 o después**: rol **Administradores de colecciones** en la colección raíz.
>- Para la cuenta de Purview creada **antes del 18 de agosto de 2021**: rol **Propietario** o **Administrador de acceso de usuario**.
>
> Además, es necesario que los usuarios sean el "Propietario" o "Colaborador" de la factoría de datos.

Siga los pasos que se indican a continuación para conectar una factoría de datos existente a la cuenta de Purview. También puede [conectar Data Factory a la cuenta de Purview desde ADF](../data-factory/connect-data-factory-to-azure-purview.md).

1. En el panel de navegación izquierdo, seleccione **Administración**.
2. En **Lineage connections** (Conexiones de linaje), seleccione **Data Factory**.
3. En la página **Data Factory connection** (Conexión de Data Factory), seleccione **Nueva**.

4. Seleccione la cuenta de Data Factory en la lista y luego **Aceptar**. También puede filtrar por nombre de suscripción para limitar la lista.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Captura de pantalla que muestra cómo conectar Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    Es posible que algunas instancias de Data Factory estén deshabilitadas si la factoría de datos ya está conectada a la cuenta actual de Purview o si no tiene una identidad administrada.

    Si alguna de las factorías de datos seleccionadas ya está conectada a otra cuenta de Purview, aparece un mensaje de advertencia. Al seleccionar Aceptar, la conexión de Data Factory con la otra cuenta de Purview se interrumpe. No se requieren confirmaciones adicionales.

    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Captura de pantalla que muestra la advertencia de desconexión de Azure Data Factory.":::

>[!Note]
>Ahora se admite la adición de un máximo de 10 factorías de datos a la vez. Si quiere agregar más de 10 factorías de datos a la vez, rellene una incidencia de soporte técnico.

### <a name="how-authentication-works"></a>Funcionamiento de la autenticación

La identidad administrada de la factoría de datos se usa para autenticar las operaciones de inserción de linaje desde la factoría de datos a Purview. Al conectar la factoría de datos a Purview en la interfaz de usuario, agrega automáticamente la asignación de roles.

- En el caso de la cuenta de Purview creada el **18 de agosto de 2021 o después**, conceda el rol **Conservador de datos** de la identidad administrada de la factoría de datos en la **colección raíz** de Purview. Obtenga más información sobre el [control de acceso en Azure Purview y ](../purview/catalog-permissions.md)[Adición de roles y restricción del acceso mediante colecciones](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections).

- En la cuenta de Purview creada **antes del 18 de agosto de 2021**, conceda el rol integrado de Azure [**Conservador de datos de Purview (heredado)** ](../role-based-access-control/built-in-roles.md#purview-data-curator-legacy) de la identidad administrada de la factoría de datos en la cuenta de Purview. Obtenga más información sobre el [Control de acceso en Azure Purview: permisos heredados](../purview/catalog-permissions.md#legacy-permission-guide).

### <a name="remove-data-factory-connections"></a>Eliminación de conexiones de Data Factory

Para quitar una conexión de Data Factory, haga lo siguiente:

1. En la página **Data Factory connection** (Conexión de Data Factory), seleccione el botón **Quitar** situado junto a una o más conexiones de Data Factory.
2. Seleccione **Confirmar** en el menú emergente para eliminar las conexiones de Data Factory seleccionadas.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="Captura de pantalla que muestra cómo seleccionar factorías de datos para quitar la conexión." lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="supported-azure-data-factory-activities"></a>Actividades admitidas de Azure Data Factory

Azure Purview captura el linaje en tiempo de ejecución de las siguientes actividades de Azure Data Factory:

- [Copiar datos](../data-factory/copy-activity-overview.md)
- [Flujo de datos](../data-factory/concepts-data-flow-overview.md)
- [Ejecución de paquetes SSIS](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

> [!IMPORTANT]
> Azure Purview anula el linaje si el origen o el destino usan un sistema de almacenamiento de datos no admitido.

La integración entre Data Factory y Purview solo admite un subconjunto de los sistemas de datos que admite Data Factory, como se explica en las secciones siguientes.

[!INCLUDE[data-factory-supported-lineage-capabilities](includes/data-factory-common-supported-capabilities.md)]

### <a name="execute-ssis-package-support"></a>Compatibilidad de ejecución de paquetes SSIS

Consulte los [almacenes de datos admitidos](how-to-lineage-sql-server-integration-services.md#supported-data-stores).

## <a name="access-secured-azure-purview-account"></a>Acceso a cuenta de Azure Purview protegida
      
Si la cuenta de Purview está protegida por el firewall, aprenda a permitir que Data Factory [acceda a una cuenta de Purview protegida](../data-factory/how-to-access-secured-purview-account.md) mediante puntos de conexión privados de Purview.

## <a name="bring-data-factory-lineage-into-purview"></a>Inserción del linaje de Data Factory en Purview

Para ver un tutorial completo, siga el [Tutorial: Inserción de datos de linaje de Data Factory en Azure Purview](../data-factory/turorial-push-lineage-to-purview.md).

## <a name="supported-lineage-patterns"></a>Patrones de linaje admitidos

Hay varios patrones de linaje que Azure Purview admite. Los datos de linaje generados se basan en el tipo de origen y el receptor usados en las actividades de Data Factory. Aunque Data Factory admite más de 80 orígenes y receptores, Azure Purview solo admite un subconjunto, como se indica en [Actividades admitidas de Azure Data Factory](#supported-azure-data-factory-activities).

Para configurar Data Factory para enviar información de linaje, vea [Introducción al linaje](catalog-lineage-user-guide.md#get-started-with-lineage).

Entre otras maneras de buscar información en la vista de linaje se incluyen las siguientes:

- En la pestaña **Linaje**, mantenga el mouse sobre las formas para obtener una vista previa de la información adicional sobre el recurso en la información sobre herramientas.
- Seleccione el nodo o el borde para ver el tipo de recurso al que pertenece o para cambiar de recurso.
- Las columnas de un conjunto de datos se muestran en el lado izquierdo de la pestaña **Linaje**. Para obtener más información sobre el linaje de nivel de columna, vea [Linaje de nivel de columna](catalog-lineage-user-guide.md#dataset-column-lineage).

### <a name="data-lineage-for-11-operations"></a>Linaje de datos de operaciones 1:1

El patrón más común para capturar el linaje de datos es mover datos de un único conjunto de datos de entrada a un único conjunto de datos de salida, con un proceso intermedio.

Un ejemplo de este patrón sería el siguiente:

- 1 origen por entrada: *Customer* (tabla SQL)
- 1 receptor por salida: *Customer1.csv* (Azure Blob)
- 1 proceso: *CopyCustomerInfo1\#Customer1.csv* (actividad de copia de Data Factory)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="Captura de pantalla que muestra el linaje de una operación de copia uno a uno de Data Factory.":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>Movimiento de datos con linaje 1:1 y compatibilidad con caracteres comodín

Otro escenario común para capturar el linaje es usar un carácter comodín para copiar archivos de un único conjunto de datos de entrada en un único conjunto de datos de salida. El carácter comodín permite que la actividad de copia coincida con varios archivos para copiarlos mediante una parte común del nombre de archivo. Azure Purview captura el linaje de nivel de archivo de cada archivo individual copiado por la actividad de copia correspondiente.

Un ejemplo de este patrón sería el siguiente:

* Origen por entrada: *CustomerCall\*.csv* (ruta ADLS Gen2)
* Receptor por salida: *CustomerCall\*.csv* (archivo de Azure Blob)
* 1 proceso: *CopyGen2ToBlob\#CustomerCall.csv* (actividad de copia de Data Factory)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="Captura de pantalla que muestra el linaje de una operación de copia uno a uno con compatibilidad con caracteres comodín." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>Movimiento de datos con linaje n:1

Puede usar las actividades de Data Flow para realizar operaciones de datos, como combinar, unir, etc. Se puede usar más de un conjunto de datos de origen para generar un conjunto de datos de destino. En este ejemplo, Azure Purview captura el linaje de nivel de archivo de archivos de entrada individuales en una tabla SQL que forma parte de una actividad de Data Flow.

Un ejemplo de este patrón sería el siguiente:

* 2 orígenes por entradas: *Customer.csv*, *Sales.parquet* (ruta ADLS Gen2)
* 1 receptor por salida: *Company data* (tabla de Azure SQL)
* 1 proceso: *DataFlowBlobsToSQL* (actividad de Data Flow de Data Factory)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="Captura de pantalla que muestra el linaje de una operación de Data Flow A D F n a 1." lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>Linaje de conjuntos de recursos

Un conjunto de recursos es un objeto lógico del catálogo que representa muchos archivos de partición en el almacenamiento subyacente. Para obtener más información, vea [Descripción de los conjuntos de recursos](concept-resource-sets.md). Cuando Azure Purview captura el linaje desde Azure Data Factory, aplica las reglas para normalizar los archivos de partición individuales y crear un único objeto lógico.

En el ejemplo siguiente se genera un conjunto de recursos de Azure Data Lake Gen2 a partir de Azure Blob:

* 1 origen por entrada: *Employee\_management.csv* (Azure Blob)
* 1 receptor por salida: *Employee\_management.csv* (Azure Data Lake Gen2)
* 1 proceso: *CopyBlobToAdlsGen2\_RS* (actividad de copia de Data Factory)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="Captura de pantalla que muestra el linaje de un conjunto de recursos." lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Inserción de datos de linaje de Data Factory en Azure Purview](../data-factory/turorial-push-lineage-to-purview.md)

[Guía de usuario del linaje de Data Catalog](catalog-lineage-user-guide.md)

[Vínculo a Azure Data Share para linaje](how-to-link-azure-data-share.md)