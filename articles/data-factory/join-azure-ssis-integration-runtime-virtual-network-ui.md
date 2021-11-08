---
title: Combinación del entorno de ejecución de integración de SSIS de Azure con una red virtual a través de Azure Portal
description: Aprenda a combinar el entorno de ejecución de integración de SSIS de Azure con una red virtual a través de Azure Portal.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15b2a1bbac1d89c74a2fdea2f5ce3af51261b29e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087901"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network-via-azure-portal"></a>Combinación del entorno de ejecución de integración de SSIS de Azure con una red virtual a través de Azure Portal

En este artículo se muestra cómo combinar el entorno de ejecución de integración Integration Services (SSIS) de Azure existente en Azure Data Factory (ADF) con una red virtual a través de la interfaz de usuario de Azure Portal/ADF. 

Antes de combinar Azure-SSIS IR con una red virtual, primero debe configurar correctamente la red virtual. Consulte el artículo [Configuración de una red virtual para insertar Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md). A continuación, siga los pasos de la sección siguiente que se aplican al tipo de red virtual (Azure Resource Manager/clásico). Por último, siga los pasos de la última sección para combinar la Azure-SSIS IR con la red virtual. 

## <a name="configure-an-azure-resource-manager-virtual-network"></a>Configuración de una red virtual creada con el método de Azure Resource Manager

Use Azure Portal para configurar una red virtual de Azure Resource Manager antes de intentar combinarla con Azure-SSIS IR.

1. Inicie Microsoft Edge o Google Chrome. Actualmente, solo estos exploradores web admiten la interfaz de usuario de ADF. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 

1. Seleccione **Más servicios**. Filtre y seleccione **Redes virtuales**. 

1. Filtre y seleccione su red virtual en la lista. 

1. En el menú de la izquierda, seleccione **Subredes**:

   - Asegúrese de que hay una subred adecuada a la que pueda conectarse la instancia de Azure-SSIS IR. Para ello, consulte la sección sobre cómo [seleccionar una subred](azure-ssis-integration-runtime-standard-virtual-network-injection.md#subnet).

   - Si usa el método exprés de inserción de red virtual, asegúrese de que la subred seleccionada se delegue en Azure Batch. Para ello, consulte la sección sobre cómo [delegar una subred en Azure Batch](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet).

1. Asegúrese de que *Microsoft.Batch* es un proveedor de recursos registrado en la suscripción de Azure que tiene la red virtual idónea para la conexión a la misma de la instancia de Azure-SSIS IR. Para obtener instrucciones detalladas, consulte la sección [Registrar Azure Batch como proveedor de recursos](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

## <a name="configure-a-classic-virtual-network"></a>Configuración de una red virtual a través del método clásico

Use Azure Portal para configurar una red virtual clásica antes de intentar combinarla con Azure-SSIS IR. 

1. Inicie Microsoft Edge o Google Chrome. Actualmente, solo estos exploradores web admiten la interfaz de usuario de ADF. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 

1. Seleccione **Más servicios**. Filtre y seleccione **Redes virtuales (clásicas)** . 

1. Filtre y seleccione su red virtual en la lista. 

1. En la página **Red virtual (clásica)** , seleccione **Propiedades**. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png" alt-text="Identificador de recurso de red virtual clásica":::

1. Seleccione el botón de copia en **ID. DE RECURSO** para copiar el identificador de recurso de la red virtual clásica en el Portapapeles. Guarde el identificador del Portapapeles en OneNote o en un archivo. 

1. En el menú de la izquierda, seleccione **Subredes**. Asegúrese de que el número de direcciones IP disponibles en la subred seleccionada sea superior al doble del número de nodos Azure-SSIS IR seleccionados. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png" alt-text="Número de direcciones disponibles en la red virtual":::

1. Una **MicrosoftAzureBatch** al rol **Colaborador de la máquina virtual clásica** de la red virtual. 

   1. En el menú de la izquierda, seleccione **Control de acceso (IAM)** y, después, la pestaña **Asignaciones de roles**. 

      Botones :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png" alt-text="&quot;Control de acceso&quot; y &quot;Agregar&quot;":::

   1. Seleccione **Agregar asignación de roles**.

   1. En la página **Agregar asignación de roles**, en **Rol**, seleccione **Colaborador de la máquina virtual clásica**. En el cuadro **Seleccionar**, pegue **ddbf3205-c6bd-46ae-8127-60eb93363864** y seleccione **MicrosoftAzureBatch** en la lista de resultados de la búsqueda. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png" alt-text="Resultados de la búsqueda en la página &quot;Agregar asignación de roles&quot;":::

   1. Seleccione **Guardar** para guardar la configuración y cerrar la página. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png" alt-text="Guardado de la configuración de acceso":::

   1. Confirme que ve **MicrosoftAzureBatch** en la lista de colaboradores. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png" alt-text="Confirmación del acceso a Azure Batch":::

1. Asegúrese de que *Microsoft.Batch* es un proveedor de recursos registrado en la suscripción de Azure que tiene la red virtual idónea para la conexión a la misma de la instancia de Azure-SSIS IR. Para obtener instrucciones detalladas, consulte la sección [Registrar Azure Batch como proveedor de recursos](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

## <a name="join-azure-ssis-ir-to-the-virtual-network"></a>Conexión de Azure-SSIS IR a la red virtual

Después de configurar una red virtual de Azure Resource Manager o clásica, puede combinar su Azure-SSIS IR con la red virtual:

1. Inicie Microsoft Edge o Google Chrome. Actualmente, solo estos exploradores web admiten la interfaz de usuario de ADF. 

1. En [Azure Portal](https://portal.azure.com), en el menú izquierdo, seleccione **Factorías de datos**. Si no ve **Factorías de datos** en el menú, seleccione **Más servicios** y luego, en la sección **INTELIGENCIA Y ANÁLISIS**, seleccione **Factorías de datos**. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png" alt-text="Lista de factorías de datos":::

1. Seleccione la instancia de ADF con Azure-SSIS IR en la lista. Verá la página principal de la instancia de ADF. Seleccione el icono **Crear y supervisar**. Verá la interfaz de usuario de ADF en una pestaña independiente. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png" alt-text="Página principal Factoría de datos":::

1. En la interfaz de usuario de ADF, vaya a la pestaña **Editar**, seleccione **Conexiones** y vaya a la pestaña **Entornos de ejecución de integración**. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png" alt-text="Pestaña&quot;Integration runtimes&quot; ":::(Entornos de ejecución de integración)

1. Si Azure-SSIS Integration Runtime está en funcionamiento, en la lista **Integration Runtimes** (Entornos de ejecución de integración), en la columna **Acciones**, seleccione el botón **Detener** de Azure-SSIS Integration Runtime. No puede editar la instancia de Azure-SSIS IR hasta que la detenga. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png" alt-text="Detención de Integration Runtime":::

1. En la lista **Integration Runtimes** (Entornos de ejecución de integración), en la columna **Actions** (Acciones), seleccione el botón **Edición** de Azure-SSIS Integration Runtime. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png" alt-text="Edición de Integration Runtime":::

1. En el panel **Configuración de un entorno de ejecución de integración**, avance por las páginas de **Configuración general** y **Configuración de implementación** seleccionando el botón **Siguiente**.

1. En la página de **Configuración avanzada**, haga lo siguiente.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="Configuración avanzada con una red virtual":::

   1. Seleccione la casilla **Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses** (Seleccionar una red virtual a la que conectar Azure-SSIS Integration Runtime, permitir que ADF cree determinados recursos de red y, opcionalmente, traer direcciones IP públicas propias). 

   1. En **Subscription** (Suscripción), seleccione la suscripción de Azure que tiene la red virtual.

   1. En **Location** (Ubicación), está seleccionada la misma ubicación del entorno de ejecución de integración.

   1. En **Tipo**, especifique el tipo de la red virtual: **red virtual de Azure Resource Manager**/red virtual clásica. Se recomienda que seleccione **red virtual de Azure Resource Manager**, puesto que la red virtual clásica dejará de utilizarse pronto.

   1. En **VNet Name** (Nombre de red virtual), seleccione el nombre de la red virtual. Debe ser el mismo que se usa para configurar un punto de conexión de servicio de red virtual/un punto de conexión privado para el servidor Azure SQL Database que hospeda SSISDB. O bien, debe ser el mismo unido por Azure SQL Managed Instance que hospeda SSISDB. O bien, la misma que está conectada a la red local. De lo contrario, puede ser cualquier red virtual para traer sus propias direcciones IP públicas estáticas de Azure-SSIS IR.

   1. En **Subnet Name** (Nombre de subred), seleccione el nombre de la subred en la red virtual. Debe ser el mismo que se usa para configurar un punto de conexión de servicio de red virtual para el servidor Azure SQL Database que hospeda SSISDB. O bien, debe ser una subred diferente de la unida por Azure SQL Managed Instance que hospeda SSISDB. De lo contrario, puede ser cualquier subred para traer sus propias direcciones IP públicas estáticas de Azure-SSIS IR.

   1. En **Método de inyección de red virtual**, seleccione el método de la inyección de red virtual: **Rápido**/**Estándar**. 
   
      Para comparar estos métodos, consulte el artículo [Comparación de los métodos de inyección de red virtual estándar y rápido](azure-ssis-integration-runtime-virtual-network-configuration.md#compare). 
   
      Si selecciona **Rápido,** consulte el artículo [Método de inyección de red virtual rápido](azure-ssis-integration-runtime-express-virtual-network-injection.md). 
      
      Si selecciona **Estándar,** consulte el artículo [Método de inyección de red virtual estándar](azure-ssis-integration-runtime-standard-virtual-network-injection.md).  Con este método, también puede:

      1. Activar la casilla **Traer direcciones IP públicas estáticas para Azure-SSIS Integration Runtime** para elegir si quiere traer sus propias direcciones IP públicas estáticas para Azure-SSIS IR, de modo que pueda habilitarlas en el firewall para los almacenes de datos.

         Si activa esta casilla, haga lo siguiente:

         1. En **First static public IP address** (Primera dirección IP pública estática), seleccione la primera dirección IP pública estática que [cumpla los requisitos](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip) de su instancia de Azure-SSIS IR. Si no tiene ninguna, haga clic en el vínculo **Crear nueva** para crear direcciones IP públicas estáticas en Azure Portal y, a continuación, haga clic en el botón Actualizar aquí para poder seleccionarlas.
      
         1. En **Second static public IP address** (Segunda dirección IP pública estática), seleccione la primera dirección IP pública estática que [cumpla los requisitos](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip) de su instancia de Azure-SSIS IR. Si no tiene ninguna, haga clic en el vínculo **Crear nueva** para crear direcciones IP públicas estáticas en Azure Portal y, a continuación, haga clic en el botón Actualizar aquí para poder seleccionarlas.

   1. Seleccione **VNet Validation** (Validación de red virtual). Si la validación es correcta, seleccione **Continuar**. 

1. En la página **Resumen**, revise toda la configuración de Azure-SSIS IR y, a continuación, seleccione **Actualizar**.

1. Inicie Azure-SSIS IR mediante la selección del botón **Iniciar** de la columna **Acciones** de Azure-SSIS IR. Toma unos 5/20-30 minutos iniciar el Azure-SSIS IR que se une a una red virtual con el método de inyección exprés/estándar, respectivamente. 

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de una red virtual para insertar Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Método rápido de inserción de la red virtual](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Método estándar de inserción de red virtual](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Unión de Azure-SSIS IR a una red virtual mediante Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Para más información acerca de Azure-SSIS IR, consulte los siguientes artículos: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual general acerca de los entornos de ejecución de integración, incluido Azure-SSIS IR. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este tutorial se proporcionan instrucciones paso a paso para crear Azure-SSIS IR. Usa el servidor de Azure SQL Database para hospedar SSISDB. 
- [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial. Proporciona instrucciones sobre el uso de un servidor de Azure SQL Database configurado con un punto de conexión de servicio de red virtual, una regla de firewall de IP, un punto de conexión privado o Azure SQL Managed Instance que se conecta a una red virtual para hospedar SSISDB. Muestra cómo conectar Azure-SSIS IR a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar y comprender la información acerca de Azure-SSIS IR.
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Azure SSIS IR mediante la adición de más nodos.
