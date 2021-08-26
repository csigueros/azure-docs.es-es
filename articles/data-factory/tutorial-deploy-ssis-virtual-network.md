---
title: Tutorial para conectar Azure-SSIS Integration Runtime a una red virtual
description: Obtenga información sobre cómo conectar Azure-SSIS Integration Runtime a una red virtual de Azure.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 3b303802d4186f8a7fe42f30f014dc4d06e73146
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638009"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Configuración de Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) para conectarlo a una red virtual

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este tutorial se describen los pasos básicos necesarios para configurar Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) con Azure Portal para conectarse a una red virtual.

Entre los pasos se incluyen:

- Configurar una red virtual.
- Conectar Azure-SSIS IR a una red virtual desde el portal de Azure Data Factory.

## <a name="prerequisites"></a>Requisitos previos

- **Integration Runtime de SSIS de Azure**. Si no tiene una instancia de Azure-SSIS Integration Runtime, [aprovisione una instancia de Azure-SSIS Integration Runtime en Azure Data Factory](tutorial-deploy-ssis-packages-azure.md) antes de comenzar.

- **Permiso del usuario**. El usuario que crea la instancia de Azure-SSIS IR debe tener como mínimo en el recurso de Azure Data Factory alguna de las siguientes [asignaciones de roles](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope):

    - Use el rol Colaborador de red integrado. Este rol requiere el permiso _Microsoft.Network /\*_ , que tiene un ámbito mucho mayor del necesario.
    - Crear un rol personalizado que incluya solo el permiso _Microsoft.Network/virtualNetworks/\*/join/action_ necesario. Si también quiere traer sus propias direcciones IP públicas de Azure-SSIS IR al conectar la instancia a una red virtual de Azure Resource Manager, incluya también el permiso _Microsoft.Network/publicIPAddresses/*/join/action_ en el rol.

- **Red virtual**.

    - Si no tiene una red virtual, [cree una mediante Azure Portal](../virtual-network/quick-create-portal.md).

    - Asegúrese de que el grupo de recursos de la red virtual puede crear y eliminar determinados recursos de Azure Network.
    
        La instancia de Integration Runtime para la integración de SSIS en Azure necesita crear determinados recursos de red en el mismo grupo de recursos de la red virtual. Estos recursos incluyen:
        - Instancia de Azure Load Balancer, con el nombre *\<Guid>-azurebatch-cloudserviceloadbalancer*.
        - Grupo de seguridad de red, con el nombre *\<Guid>-azurebatch-cloudservicenetworksecuritygroup.
        - Una dirección IP pública de Azure llamada -azurebatch-cloudservicepublicip.
    
        Estos recursos se crearán al iniciarse la instancia de Azure-SSIS IR. Se eliminarán cuando esta se detenga. Para evitar que se bloquee la detención en la instancia de Azure-SSIS IR, no vuelva a usar estos recursos de red en los demás recursos.

    - Asegúrese de que no tiene ningún [bloqueo de recursos](../azure-resource-manager/management/lock-resources.md) en el grupo de recursos ni en la suscripción a la que pertenece la red virtual. Si configura un bloqueo de solo lectura o de eliminación, se producirá un error al iniciar o detener la instancia de Azure-SSIS IR, o esta dejará de responder.

    - Asegúrese de no tener ninguna asignación de Azure Policy que impida que se creen los siguientes recursos en el grupo de recursos o en la suscripción a la que pertenece la red virtual:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Los siguientes escenarios de **configuración de red** no se describen en este tutorial:
    - Si trae direcciones IP públicas propias para la instancia de Azure-SSIS IR.
    - Si usa un servidor de sistema de nombres de dominio (DNS) propio.
    - Si usa un grupo de seguridad de red (NSG) en la subred.
    - Si usa Azure ExpressRoute o una ruta definida por el usuario (UDR).
    - Si usa una instancia de Azure-SSIS IR personalizada.
    
    Para obtener más información, consulte la [configuración de la red virtual](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Configuración de una red virtual

Use Azure Portal para configurar una red virtual antes de conectarle una instancia de Azure-SSIS IR.

1. Inicie Microsoft Edge o Google Chrome. Actualmente, estos exploradores web son los únicos que admiten la interfaz de usuario de Data Factory.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **Más servicios**. Filtre y seleccione **Redes virtuales**.

1. Filtre y seleccione su red virtual en la lista.

1. En la página **Red virtual**, seleccione **Propiedades**.

1. Seleccione el botón de copia en **ID. DE RECURSO** para copiar el identificador de recurso de la red virtual en el Portapapeles. Guarde el identificador del Portapapeles en OneNote o en un archivo.

1. En el menú de la izquierda, seleccione **Subredes**.

    - Asegúrese de que la subred que seleccione tenga suficiente espacio de direcciones disponible para usar Azure-SSIS IR. Deje direcciones IP disponibles para al menos dos veces el número de nodos del entorno de ejecución de integración. Azure reserva algunas direcciones IP dentro de cada subred. Estas direcciones no se pueden usar. La primera y la última direcciones IP de las subredes están reservadas para la conformidad con el protocolo y las otras tres se usan para los servicios de Azure. Para más información, consulte [¿Hay alguna restricción en el uso de direcciones IP dentro de estas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - No seleccione GatewaySubnet para implementar Azure-SSIS Integration Runtime. Está dedicado a las puertas de enlace de red virtual.
    - No use una subred que esté ocupada exclusivamente por otros servicios de Azure (por ejemplo, Instancia administrada de SQL Database, App Service, etc.).

1. Compruebe que el proveedor de Azure Batch está registrado en la suscripción de Azure que tiene la red virtual. O bien, registre el proveedor de Azure Batch. Si ya tiene una cuenta de Azure Batch en su suscripción, significa que la suscripción está registrada para Azure Batch. Si crea la instancia de Integration Runtime para la integración de SSIS en Azure en el portal de Data Factory, el proveedor de Azure Batch se registrará automáticamente.

   1. En Azure Portal, en el menú de la izquierda, seleccione **Suscripciones**.

   1. Seleccione su suscripción.

   1. A la izquierda, seleccione **Proveedores de recursos** y confirme que **Microsoft.Batch** es un proveedor registrado.

   ![Confirmación del estado "Registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Si no ve **Microsoft.Batch** en la lista, regístrelo. Para ello, [cree una cuenta de Azure Batch vacía](../batch/batch-account-create-portal.md) en su suscripción. Puede eliminarlo más tarde.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Unión de la instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual

Después de configurar la red virtual de Azure Resource Manager o la red virtual clásica, puede conectar Azure-SSIS Integration Runtime a la red virtual:

1. Inicie Microsoft Edge o Google Chrome. Actualmente, estos exploradores web son los únicos que admiten la interfaz de usuario de Data Factory.

1. En [Azure Portal](https://portal.azure.com), en el menú izquierdo, seleccione **Factorías de datos**. Si no ve **Factorías de datos** en el menú, seleccione **Más servicios** y luego, en la sección **INTELIGENCIA Y ANÁLISIS**, seleccione **Factorías de datos**.

   ![Lista de factorías de datos](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Seleccione su factoría de datos con Azure-SSIS Integration Runtime en la lista. Verá la página principal de la factoría de datos. Seleccione el icono **Crear y supervisar**. Verá la interfaz de usuario de Data Factory en una pestaña independiente.

   ![Página principal Factoría de datos](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. En la interfaz de usuario de Data Factory, vaya a la pestaña **Editar**, seleccione **Conexiones** y vaya a la pestaña **Integration Runtimes** (Entornos de ejecución de integración).

   ![Pestañas "Integration runtimes" (Entornos de ejecución de integración)](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Si Azure-SSIS Integration Runtime está en funcionamiento, en la lista **Integration Runtimes** (Entornos de ejecución de integración), en la columna **Acciones**, seleccione el botón **Detener** de Azure-SSIS Integration Runtime. No puede editar la instancia de Azure-SSIS IR hasta que la detenga.

   ![Detención de Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. En la lista **Integration Runtimes** (Entornos de ejecución de integración), en la columna **Actions** (Acciones), seleccione el botón **Edición** de Azure-SSIS Integration Runtime.

   ![Edición de Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. En el panel de configuración del entorno de ejecución de integración, avance por las secciones **General Settings** (Configuración general) y **SQL Settings** (Configuración de SQL) con el botón **Next** (Siguiente).

1. En la sección **Advanced Settings** (Configuración avanzada):
   1. Seleccione la casilla **Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses** (Seleccionar una red virtual a la que conectar Azure-SSIS Integration Runtime, permitir que ADF cree determinados recursos de red y, opcionalmente, traer direcciones IP públicas propias).

   1. En **Subscription** (Suscripción), seleccione la suscripción de Azure que tiene la red virtual.

   1. En **Location** (Ubicación), está seleccionada la misma ubicación del entorno de ejecución de integración.

   1. En **Type** (Tipo), seleccione el tipo de red virtual: clásica o de Azure Resource Manager. Se recomienda seleccionar una red virtual de Azure Resource Manager, puesto que las redes virtuales clásicas dejarán de utilizarse pronto.

   1. En **VNet Name** (Nombre de red virtual), seleccione el nombre de la red virtual. Debe ser la misma que se usa para SQL Database con puntos de conexión de servicio de red virtual o para Instancia administrada de SQL con un punto de conexión privado para hospedar SSISDB. O bien, la misma que está conectada a la red local. De lo contrario, puede ser cualquier red virtual para traer sus propias direcciones IP públicas estáticas de Azure-SSIS IR.

   1. En **Subnet Name** (Nombre de subred), seleccione el nombre de la subred en la red virtual. Debe ser la misma que se usa para SQL Database con puntos de conexión de servicio de red virtual para hospedar SSISDB. O bien, debe ser una subred diferente de la que se usa en Instancia administrada de SQL con un punto de conexión privado para hospedar SSISDB. De lo contrario, puede ser cualquier subred para traer sus propias direcciones IP públicas estáticas de Azure-SSIS IR.

   1. Seleccione **VNet Validation** (Validación de red virtual). Si la validación es correcta, seleccione **Continuar**.

   ![Configuración avanzada con una red virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. En la página **Resumen**, revise toda la configuración de Azure-SSIS Integration Runtime. Después, seleccione **Actualizar**.

1. Inicie Azure-SSIS Integration Runtime mediante la selección del botón **Iniciar** de la columna **Acciones** de Azure-SSIS IR. Se tarda aproximadamente entre 20 y 30 minutos en iniciar la instancia de Azure-SSIS IR que se conecta a una red virtual.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo [conectar Azure-SSIS IR a una red virtual](join-azure-ssis-integration-runtime-virtual-network.md).