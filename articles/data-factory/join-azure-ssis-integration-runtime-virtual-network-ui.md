---
title: Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual mediante interfaz de usuario
description: Aprenda a usar la interfaz de usuario de Azure Data Factory Studio para unir una instancia de Azure-SSIS Integration Runtime a una red virtual de Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d02a5d1025e470d8751d87816cbcc6395be41593
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403131"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network-with-azure-data-factory-studio-ui"></a>Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual con la interfaz de usuario de Azure Data Factory Studio

En esta sección se muestra cómo unir una instancia de Azure-SSIS Integration Runtime existente a una red virtual (clásica o de Azure Resource Manager) mediante Azure Portal y la interfaz de usuario de Azure Data Factory Studio. 

Antes de conectar Azure-SSIS Integration Runtime a la red virtual debe configurar correctamente esta última. Siga los pasos de la sección que se aplica a su tipo de red virtual (clásica o de Azure Resource Manager). Después, siga los pasos de la tercera sección para conectar Azure-SSIS Integration Runtime a la red virtual. 

## <a name="configure-an-azure-resource-manager-virtual-network"></a>Configuración de una red virtual creada con el método de Azure Resource Manager

Use el portal para configurar una red virtual de Azure Resource Manager antes conectar Azure-SSIS Integration Runtime a ella.

1. Inicie Microsoft Edge o Google Chrome. Actualmente, estos exploradores web son los únicos que admiten la interfaz de usuario de Data Factory. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 

1. Seleccione **Más servicios**. Filtre y seleccione **Redes virtuales**. 

1. Filtre y seleccione su red virtual en la lista. 

1. En la página **Red virtual**, seleccione **Propiedades**. 

1. Seleccione el botón de copia en **ID. DE RECURSO** para copiar el identificador de recurso de la red virtual en el Portapapeles. Guarde el identificador del Portapapeles en OneNote o en un archivo. 

1. En el menú de la izquierda, seleccione **Subredes**. Asegúrese de que el número de direcciones disponibles es mayor que el número de nodos de Azure-SSIS Integration Runtime. 

1. Compruebe que el proveedor de Azure Batch está registrado en la suscripción de Azure que tiene la red virtual. O bien, registre el proveedor de Azure Batch. Si ya tiene una cuenta de Azure Batch en su suscripción, significa que la suscripción está registrada para Azure Batch. Si crea la instancia de Integration Runtime para la integración de SSIS en Azure en el portal de Data Factory, el proveedor de Azure Batch se registrará automáticamente. 

   1. En Azure Portal, en el menú de la izquierda, seleccione **Suscripciones**. 

   1. Seleccione su suscripción. 

   1. A la izquierda, seleccione **Proveedores de recursos** y confirme que **Microsoft.Batch** es un proveedor registrado. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="Confirmación del ":::estado &quot;Registrado&quot;.

   Si no ve **Microsoft.Batch** en la lista, regístrelo. Para ello, [cree una cuenta de Azure Batch vacía](../batch/batch-account-create-portal.md) en su suscripción. Puede eliminarlo más tarde. 

## <a name="configure-a-classic-virtual-network"></a>Configuración de una red virtual a través del método clásico

Use el portal para configurar una red virtual clásica antes conectar Azure-SSIS Integration Runtime a ella. 

1. Inicie Microsoft Edge o Google Chrome. Actualmente, estos exploradores web son los únicos que admiten la interfaz de usuario de Data Factory. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 

1. Seleccione **Más servicios**. Filtre y seleccione **Redes virtuales (clásicas)** . 

1. Filtre y seleccione su red virtual en la lista. 

1. En la página **Red virtual (clásica)** , seleccione **Propiedades**. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png" alt-text="Identificador de recurso de red virtual clásica":::

1. Seleccione el botón de copia en **ID. DE RECURSO** para copiar el identificador de recurso de la red clásica en el Portapapeles. Guarde el identificador del Portapapeles en OneNote o en un archivo. 

1. En el menú de la izquierda, seleccione **Subredes**. Asegúrese de que el número de direcciones disponibles es mayor que el número de nodos de Azure-SSIS Integration Runtime. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png" alt-text="Número de direcciones disponibles en la red virtual":::

1. Una **MicrosoftAzureBatch** al rol **Colaborador de la máquina virtual clásica** de la red virtual. 

   1. En el menú de la izquierda, seleccione **Control de acceso (IAM)** y, después, la pestaña **Asignaciones de roles**. 

       Botones :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png" alt-text="&quot;Control de acceso&quot; y &quot;Agregar&quot;":::

   1. Seleccione **Agregar asignación de roles**.

   1. En la página **Agregar asignación de roles**, en **Rol**, seleccione **Colaborador de la máquina virtual clásica**. En el cuadro **Seleccionar**, pegue **ddbf3205-c6bd-46ae-8127-60eb93363864** y seleccione **Microsoft Azure Batch** en la lista de resultados de la búsqueda. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png" alt-text="Resultados de la búsqueda en la página &quot;Agregar asignación de roles&quot;":::

   1. Seleccione **Guardar** para guardar la configuración y cerrar la página. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png" alt-text="Guardado de la configuración de acceso":::

   1. Confirme que ve **Microsoft Azure Batch** en la lista de colaboradores. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png" alt-text="Confirmación del acceso a Azure Batch":::

1. Compruebe que el proveedor de Azure Batch está registrado en la suscripción de Azure que tiene la red virtual. O bien, registre el proveedor de Azure Batch. Si ya tiene una cuenta de Azure Batch en su suscripción, significa que la suscripción está registrada para Azure Batch. Si crea la instancia de Integration Runtime para la integración de SSIS en Azure en el portal de Data Factory, el proveedor de Azure Batch se registrará automáticamente. 

   1. En Azure Portal, en el menú de la izquierda, seleccione **Suscripciones**. 

   1. Seleccione su suscripción. 

   1. A la izquierda, seleccione **Proveedores de recursos** y confirme que **Microsoft.Batch** es un proveedor registrado. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="Confirmación del ":::estado &quot;Registrado&quot;.

   Si no ve **Microsoft.Batch** en la lista, regístrelo. Para ello, [cree una cuenta de Azure Batch vacía](../batch/batch-account-create-portal.md) en su suscripción. Puede eliminarlo más tarde. 

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Unión de la instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual

Después de configurar la red virtual de Azure Resource Manager o la red virtual clásica, puede conectar Azure-SSIS Integration Runtime a la red virtual:

1. Inicie Microsoft Edge o Google Chrome. Actualmente, estos exploradores web son los únicos que admiten la interfaz de usuario de Data Factory. 

1. En [Azure Portal](https://portal.azure.com), en el menú izquierdo, seleccione **Factorías de datos**. Si no ve **Factorías de datos** en el menú, seleccione **Más servicios** y luego, en la sección **INTELIGENCIA Y ANÁLISIS**, seleccione **Factorías de datos**. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png" alt-text="Lista de factorías de datos":::

1. Seleccione su factoría de datos con Azure-SSIS Integration Runtime en la lista. Verá la página principal de la factoría de datos. Seleccione el icono **Crear y supervisar**. Verá la interfaz de usuario de Data Factory en una pestaña independiente. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png" alt-text="Página principal Factoría de datos":::

1. En la interfaz de usuario de Data Factory, vaya a la pestaña **Editar**, seleccione **Conexiones** y vaya a la pestaña **Integration Runtimes** (Entornos de ejecución de integración). 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png" alt-text="Pestaña&quot;Integration runtimes&quot; ":::(Entornos de ejecución de integración)

1. Si Azure-SSIS Integration Runtime está en funcionamiento, en la lista **Integration Runtimes** (Entornos de ejecución de integración), en la columna **Acciones**, seleccione el botón **Detener** de Azure-SSIS Integration Runtime. No puede editar la instancia de Azure-SSIS IR hasta que la detenga. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png" alt-text="Detención de Integration Runtime":::

1. En la lista **Integration Runtimes** (Entornos de ejecución de integración), en la columna **Actions** (Acciones), seleccione el botón **Edición** de Azure-SSIS Integration Runtime. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png" alt-text="Edición de Integration Runtime":::

1. En el panel de configuración del entorno de ejecución de integración, avance por las secciones **General Settings** (Configuración general) y **SQL Settings** (Configuración de SQL) con el botón **Next** (Siguiente). 

1. En la sección **Advanced Settings** (Configuración avanzada): 

   1. Seleccione la casilla **Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses** (Seleccionar una red virtual a la que conectar Azure-SSIS Integration Runtime, permitir que ADF cree determinados recursos de red y, opcionalmente, traer direcciones IP públicas propias). 

   1. En **Subscription** (Suscripción), seleccione la suscripción de Azure que tiene la red virtual.

   1. En **Location** (Ubicación), está seleccionada la misma ubicación del entorno de ejecución de integración.

   1. En **Type** (Tipo), seleccione el tipo de red virtual: clásica o de Azure Resource Manager. Se recomienda seleccionar una red virtual de Azure Resource Manager, puesto que las redes virtuales clásicas dejarán de utilizarse pronto.

   1. En **VNet Name** (Nombre de red virtual), seleccione el nombre de la red virtual. Debe ser la misma que se usa para SQL Database con puntos de conexión de servicio de red virtual o para Instancia administrada de SQL con un punto de conexión privado para hospedar SSISDB. O bien, la misma que está conectada a la red local. De lo contrario, puede ser cualquier red virtual para traer sus propias direcciones IP públicas estáticas de Azure-SSIS IR.

   1. En **Subnet Name** (Nombre de subred), seleccione el nombre de la subred en la red virtual. Debe ser la misma que se usa para SQL Database con puntos de conexión de servicio de red virtual para hospedar SSISDB. O bien, debe ser una subred diferente de la que se usa en la Instancia administrada de SQL con un punto de conexión privado para hospedar SSISDB. De lo contrario, puede ser cualquier subred para traer sus propias direcciones IP públicas estáticas de Azure-SSIS IR.

   1. Active la casilla **Bring static public IP addresses for your Azure-SSIS Integration Runtime** (Traer direcciones IP públicas estáticas para Azure-SSIS Integration Runtime) para elegir si quiere traer sus propias direcciones IP públicas estáticas para Azure-SSIS IR, de modo que pueda habilitarlas en el firewall para los orígenes de datos.

      Si activa esta casilla, haga lo siguiente:

      1. En **First static public IP address** (Primera dirección IP pública estática), seleccione la primera dirección IP pública estática que [cumpla los requisitos](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP) de su instancia de Azure-SSIS IR. Si no tiene ninguna, haga clic en el vínculo **Crear nueva** para crear direcciones IP públicas estáticas en Azure Portal y, a continuación, haga clic en el botón actualizar aquí para poder seleccionarlas.
      
      1. En **Second static public IP address** (Segunda dirección IP pública estática), seleccione la primera dirección IP pública estática que [cumpla los requisitos](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP) de su instancia de Azure-SSIS IR. Si no tiene ninguna, haga clic en el vínculo **Crear nueva** para crear direcciones IP públicas estáticas en Azure Portal y, a continuación, haga clic en el botón actualizar aquí para poder seleccionarlas.

   1. Seleccione **VNet Validation** (Validación de red virtual). Si la validación es correcta, seleccione **Continuar**. 

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="Configuración avanzada con una red virtual":::

1. En la página **Resumen**, revise toda la configuración de Azure-SSIS Integration Runtime. Después, seleccione **Actualizar**.

1. Inicie Azure-SSIS Integration Runtime mediante la selección del botón **Iniciar** de la columna **Acciones** de Azure-SSIS IR. Se tarda aproximadamente entre 20 y 30 minutos en iniciar la instancia de Azure-SSIS IR que se conecta a una red virtual. 

## <a name="next-steps"></a>Pasos siguientes
- [Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual: información general](join-azure-ssis-integration-runtime-virtual-network.md)
- [Detalles de configuración de la red virtual de Azure-SSIS Integration Runtime](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual con Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Para más información acerca de Azure-SSIS IR, consulte los siguientes artículos: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual general acerca de los entornos de ejecución de integración, incluido Azure-SSIS IR. 
- [Tutorial: Implementación de paquetes SSIS en Azure](./tutorial-deploy-ssis-packages-azure.md). En este tutorial se proporcionan instrucciones paso a paso para crear Azure-SSIS IR. Usa Azure SQL Database para hospedar el catálogo de SSIS. 
- [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial. Proporciona instrucciones acerca del uso de Azure SQL Database con puntos de conexión de servicio de red virtual o una Instancia administrada de SQL en una red virtual para hospedar el catálogo SSIS. Muestra cómo conectar Azure-SSIS IR a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo obtener información acerca de Azure-SSIS IR. Proporciona descripciones del estado de la información devuelta. 
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Integration Runtime para la integración de SSIS en Azure mediante la adición de nodos.
