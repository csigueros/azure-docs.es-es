---
title: Tutorial para conectar Azure-SSIS Integration Runtime a una red virtual
description: Aprenda a conectar Azure-SSIS Integration Runtime a una red virtual.
author: swinarko
ms.author: sawinark
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
ms.openlocfilehash: 8ee28e9c310002c0a0b2e29a77b35c71045c9fc4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040878"
---
# <a name="configure-azure-ssis-integration-runtime-to-join-a-virtual-network"></a>Conexión de Azure-SSIS Integration Runtime a una red virtual

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este tutorial se proporcionan los pasos mínimos a través de la interfaz de usuario de Azure Data Factory (ADF) o Azure Portal para conectar Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) a una red virtual con el método de inserción rápida.  Entre los pasos se incluyen:

1. Configure una red virtual para el método de inserción rápida mediante Azure Portal.
1. Conexión de la instancia de Azure-SSIS IR a la red virtual con el método de inserción rápida mediante la interfaz de usuario de ADF

## <a name="prerequisites"></a>Prerrequisitos

- **Azure-SSIS IR:** si aún no tiene una, [cree una instancia de Azure-SSIS IR mediante la interfaz de usuario de ADF](tutorial-deploy-ssis-packages-azure.md) en primer lugar.

- **Red virtual**: si aún no tiene una, [cree una red virtual mediante Azure Portal](../virtual-network/quick-create-portal.md) en primer lugar. Asegúrese de lo siguiente:
  - No hay ningún bloqueo de recursos en la red virtual.
  - Al usuario que crea Azure-SSIS IR se le conceden los permisos de control de acceso basado en rol (RBAC) necesarios para conectarse a la red virtual o subred. Consulte la sección sobre cómo [seleccionar permisos de red virtual](azure-ssis-integration-runtime-express-virtual-network-injection.md#perms).

Las siguientes configuraciones de red virtual no se tratan en este tutorial:

- Si usa una dirección IP pública estática para Azure-SSIS IR.
- Si usa su propio servidor de Sistema de nombres de dominio (DNS).
- Si usa un grupo de seguridad de red (NSG).
- Si usa rutas definidas por el usuario (UDR).

Si desea seguir estos pasos opcionales, consulte el artículo sobre el [método de inserción rápida de la red virtual](azure-ssis-integration-runtime-express-virtual-network-injection.md).

## <a name="configure-a-virtual-network"></a>Configuración de una red virtual

Use Azure Portal para configurar una red virtual antes de intentar conectar su Azure-SSIS IR a ella.

1. Inicie Microsoft Edge o Google Chrome. Actualmente, solo estos exploradores web admiten la interfaz de usuario de ADF.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **Más servicios**. Filtre y seleccione **Redes virtuales**.

1. Filtre y seleccione su red virtual en la lista.

1. En el menú de la izquierda, seleccione **Subredes**:

   - Asegúrese de que hay una subred adecuada a la que pueda conectarse la instancia de Azure-SSIS IR. Para ello, consulte la sección sobre cómo [seleccionar una subred](azure-ssis-integration-runtime-express-virtual-network-injection.md#subnet).

   - Asegúrese de que la subred seleccionada se delega en Azure Batch. Para ello, consulte la sección sobre cómo [delegar una subred en Azure Batch](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet).

1. Asegúrese de que *Microsoft.Batch* es un proveedor de recursos registrado en la suscripción de Azure que tiene la red virtual idónea para la conexión a la misma de la instancia de Azure-SSIS IR. Para obtener instrucciones detalladas, consulte la sección sobre cómo [registrar Azure Batch como proveedor de recursos](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

## <a name="join-azure-ssis-ir-to-the-virtual-network"></a>Conexión de Azure-SSIS IR a la red virtual

Después de configurar una red virtual, puede conectar su Azure-SSIS IR a la red virtual:

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

1. En el panel **Configuración de Integration Runtime**, avance por las páginas **Configuración general** y **Configuración de implementación** seleccionando el botón **Siguiente**.

1. En la página **Configuración avanzada**, haga lo siguiente.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet-express.png" alt-text="Configuración avanzada de la inserción rápida de la red virtual":::

   1. Seleccione la casilla **Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses** (Seleccionar una red virtual a la que conectar Azure-SSIS Integration Runtime, permitir que ADF cree determinados recursos de red y, opcionalmente, traer direcciones IP públicas propias).

   1. En **Subscription** (Suscripción), seleccione la suscripción de Azure que tiene la red virtual.

   1. En **Location** (Ubicación), está seleccionada la misma ubicación del entorno de ejecución de integración.

   1. En **Tipo**, especifique el tipo de la red virtual: **red virtual de Azure Resource Manager**/red virtual clásica. Se recomienda que seleccione **red virtual de Azure Resource Manager**, puesto que la red virtual clásica dejará de utilizarse pronto.

   1. En **VNet Name** (Nombre de red virtual), seleccione el nombre de la red virtual. Debe ser el mismo que se usa para configurar un punto de conexión de servicio de red virtual/un punto de conexión privado para el servidor Azure SQL Database que hospeda SSISDB. O bien, debe ser el mismo unido por Azure SQL Managed Instance que hospeda SSISDB. O bien, la misma que está conectada a la red local.

   1. En **Subnet Name** (Nombre de subred), seleccione el nombre de la subred en la red virtual. Debe ser el mismo que se usa para configurar un punto de conexión de servicio de red virtual para el servidor Azure SQL Database que hospeda SSISDB. O bien, debe ser una subred diferente de la unida por Azure SQL Managed Instance que hospeda SSISDB.

   1. En **Método de inyección de red virtual**, seleccione **Rápido** para la inserción rápida de la red virtual.

   1. Seleccione **VNet Validation** (Validación de red virtual). Si la validación es correcta, seleccione **Continuar**.

1. En la página **Resumen**, revise toda la configuración de Azure-SSIS IR y, a continuación, seleccione **Actualizar**.

1. Inicie Azure-SSIS Integration Runtime mediante la selección del botón **Iniciar** de la columna **Acciones** de Azure-SSIS IR. Se tarda en iniciar la instancia de Azure-SSIS IR que se conecta a una red virtual con el método de inserción rápida unos 5 minutos.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de una red virtual para insertar Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Método de inserción rápida de la red virtual](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Conexión de Azure-SSIS IR a una red virtual mediante la interfaz de usuario de ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Conexión de Azure-SSIS IR a una red virtual mediante Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Para más información acerca de Azure-SSIS IR, consulte los siguientes artículos: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual general acerca de los entornos de ejecución de integración, incluido Azure-SSIS IR. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este tutorial se proporcionan instrucciones paso a paso para crear Azure-SSIS IR. Usa el servidor de Azure SQL Database para hospedar SSISDB. 
- [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial. Proporciona instrucciones sobre el uso de un servidor de Azure SQL Database configurado con un punto de conexión de servicio de red virtual, una regla de firewall de IP, un punto de conexión privado o Azure SQL Managed Instance que se conecta a una red virtual para hospedar SSISDB. Muestra cómo conectar Azure-SSIS IR a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar y comprender la información acerca de Azure-SSIS IR.
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Azure SSIS IR mediante la adición de más nodos.
