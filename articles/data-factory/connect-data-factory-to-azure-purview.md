---
title: Conexión de Data Factory a Azure Purview
description: Información sobre la conexión de Data Factory a Azure Purview
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 08/10/2021
ms.openlocfilehash: 1d03eb65ebc976691ee76dc4dd208baa7445d92e
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201635"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Conexión de Data Factory a Azure Purview (versión preliminar)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Purview](../purview/overview.md) es un servicio de gobernanza de datos unificado que le ayuda a administrar y controlar sus datos locales, multinube y de software como servicio (SaaS). Puede conectar su factoría de datos a Azure Purview. Esta conexión le permite usar Azure Purview para capturar datos de linaje, así como para detectar y explorar los activos de Azure Purview.

## <a name="connect-data-factory-to-azure-purview"></a>Conexión de Data Factory a Azure Purview

Tiene dos maneras de conectar Data Factory a Azure Purview:

- [Conectar una cuenta de Azure Purview en Data Factory](#connect-to-azure-purview-account-in-data-factory).
- [Registro de Data Factory en Azure Purview](#register-data-factory-in-azure-purview)

### <a name="connect-to-azure-purview-account-in-data-factory"></a>Conexión de una cuenta de Azure Purview en Data Factory

Para establecer la conexión, debe tener los roles **Propietario** o **Colaborador** en su factoría de datos.

1. En la interfaz de usuario de creación de Azure Data Factory, vaya a **Administrar** -> **Azure Purview** y  seleccione **Conectarse a una cuenta de Purview**. 

    :::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Captura de pantalla para registrar una cuenta de Purview.":::

2. Elija **A partir de una suscripción de Azure** o **Especificar manualmente**. En **A partir de una suscripción de Azure**, puede seleccionar la cuenta a la que tiene acceso.

3. Una vez que se haya conectado, puede ver el nombre de la cuenta de Purview en la pestaña **Cuenta de Purview**.

Al conectar una factoría de datos a Purview, la interfaz de usuario de Azure Data Factory también intenta conceder a la identidad administrada de la factoría el rol **Conservador de datos de Purview** en la cuenta de Purview. La identidad administrada se usa para autenticar las operaciones de inserción de linaje desde la factoría de datos a Purview. Si tiene los roles **Propietario** o **Administrador de acceso de usuarios** en la cuenta de Purview, esta operación se realiza correctamente. Si no es así, aparecerá una advertencia como la siguiente:

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Captura de pantalla de la advertencia de registro de una cuenta de Purview.":::

Para resolver el problema, vaya a Azure Portal -> su cuenta de Purview -> Control de acceso (IAM) y compruebe se ha concedido el rol **Conservador de datos de Purview** a la identidad administrada de la factoría de datos. Si no es así, agregue manualmente la asignación de roles.

### <a name="register-data-factory-in-azure-purview"></a>Registro de Data Factory en Azure Purview

Para obtener información sobre cómo registrar Data Factory en Azure Purview, consulte [Cómo conectar Azure Data Factory y Azure Purview](../purview/how-to-link-azure-data-factory.md). 

## <a name="report-lineage-data-to-azure-purview"></a>Notificación de datos de linaje a Azure Purview

Una vez conectada la factoría de datos a una cuenta de Purview, al ejecutar la actividad Copiar, Flujo de datos o Ejecutar paquete SSIS, puede obtener el linaje entre los conjuntos de datos creados por los procesos de datos y tener una visión general de todo el proceso de flujo de trabajo entre los orígenes de datos y el destino. Para obtener más información sobre las funcionalidades admitidas, consulte [Actividades admitidas de Azure Data Factory](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities). Para ver un tutorial completo, consulte [Tutorial: Inserción de datos de linaje de Data Factory en Azure Purview](tutorial-push-lineage-to-purview.md).

## <a name="discover-and-explore-data-using-purview"></a>Detección y exploración de datos mediante Purview

Una vez conectada la factoría de datos a una cuenta de Purview, puede usar la barra de búsqueda ubicada en la parte superior central de la interfaz de usuario de Azure Data Factory para buscar datos y realizar acciones. Para obtener más información, consulte [Detección y exploración de datos en ADF mediante Purview](how-to-discover-explore-purview-data.md).

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Inserción de datos de linaje de Data Factory en Azure Purview](tutorial-push-lineage-to-purview.md)

[Detección y exploración de datos en ADF mediante Purview](how-to-discover-explore-purview-data.md)

[Guía del usuario del linaje de Azure Purview Data Catalog](../purview/catalog-lineage-user-guide.md)