---
title: Configuración de una red virtual para la inserción del entorno de ejecución de integración de Azure-SSIS
description: Aprenda a configurar una red virtual para la inserción del entorno de ejecución de integración de Azure-SSIS.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32930edc0aa02fa537380ceadffb04a1fd5729da
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068818"
---
# <a name="configure-a-virtual-network-for-injection-of-azure-ssis-integration-runtime"></a>Configuración de una red virtual para la inserción del entorno de ejecución de integración de Azure-SSIS

Al usar SQL Server Integration Services (SSIS) en Azure Data Factory (ADF), hay dos métodos para unir el entorno de ejecución de integración de SSIS (IR) de Azure a una red virtual: estándar y rápido. El método rápido inicia el Azure-SSIS IR más rápido y no tiene requisitos de tráfico entrantes, así como menos salientes, pero tiene algunas limitaciones en comparación con el método estándar.

## <a name="compare-the-standard-and-express-virtual-network-injection-methods"></a><a name="compare"></a>Comparación de los métodos de inserción de red virtual estándar y rápido

Esta es una tabla que resalta las diferencias entre los métodos de inserción de red virtual estándar y rápido:

| De comparación | Inserción de la red virtual estándar | Inserción de la red virtual rápida |
|------------|------------------------------------|-----------------------------------|
| **Duración inicial de Azure-SSIS IR** | Aproximadamente 30 minutos. | Aproximadamente 5 minutos. | 
| **Suscripción a Azure o suscripción al grupo de recursos** | *Microsoft.Batch* debe estar registrado como proveedor de recursos en la suscripción de red virtual.<br/><br/>Se debe permitir la creación de una dirección IP pública, un equilibrador de carga y un grupo de seguridad de red (NSG) en el grupo de recursos de red virtual. | *Microsoft.Batch* debe estar registrado como proveedor de recursos en la suscripción de red virtual. | 
| **Subred de red virtual** | La subred no debe estar dedicada a otros servicios de Azure. | La subred no debe estar dedicada a otros servicios de Azure.<br/><br/>La subred seleccionada debe delegarse al servicio *Microsoft.Batch/batchAccounts*. | 
| **Permiso de red virtual** | El usuario que crea Azure-SSIS IR debe tener el permiso _Microsoft.Network/virtualNetworks/\*/join_. | El usuario que crea Azure-SSIS IR debe tener el permiso *Microsoft.Network/virtualNetworks/subnets/join/action*. | 
| **Direcciones IP públicas estáticas** | (Opcional) Traiga sus propias direcciones IP públicas estáticas para Azure-SSIS IR. | (Opcional) Configure la traducción de direcciones de red virtual (NAT) para establecer una dirección IP pública estática para Azure-SSIS IR. | 
| **Servidor DNS personalizado** | Se recomienda reenviar solicitudes DNS sin resolver a resoluciones recursivas de Azure. | Se recomienda reenviar solicitudes DNS sin resolver a resoluciones recursivas de Azure.<br/><br/>Requiere una configuración personalizada estándar para Azure-SSIS IR. | 
| **Tráfico entrante** | El puerto *29876, 29877* debe estar abierto para el tráfico TCP con la etiqueta de servicio *BatchNodeManagement* como origen. | No se requiere. | 
| **Tráfico de salida** | El puerto *443* debe estar abierto para el tráfico TCP con la etiqueta de servicio *AzureCloud* como destino. | El puerto *443* debe estar abierto para el tráfico TCP con la etiqueta de servicio *DataFactoryManagement* como destino. | 
| **Bloqueo de recursos** | No se permite en el grupo de recursos. | No se permite en la red virtual. | 
| **Instancias de Azure-SSIS IR por red virtual** | Sin límite. | Solo uno. | 

La red virtual debe configurarse de forma diferente en función del método de inserción. Si usa el método rápido, consulte el artículo [Método de inserción de red virtual rápido](azure-ssis-integration-runtime-express-virtual-network-injection.md); de lo contrario, consulte el artículo [Método de inyección de red virtual estándar](azure-ssis-integration-runtime-standard-virtual-network-injection.md).
  
## <a name="register-azure-batch-as-a-resource-provider"></a><a name="registerbatch"></a>Registro de Azure Batch como proveedor de recursos

Con Azure Portal, puede registrar Azure Batch, la infraestructura subyacente para SSIS en ADF, como un proveedor de recursos en una suscripción de Azure que tenga la red virtual para que Azure-SSIS IR se una. Si ya ha usado Azure Batch o ha creado Azure-SSIS IR a través de la interfaz de usuario de ADF en esa suscripción, ya está registrada. De lo contrario, puede hacerlo siguiendo estos pasos:

1. Después de seleccionar la red virtual en Azure Portal, seleccione la suscripción resaltada en su página **Información general**.  

1. En el menú de la izquierda, seleccione **Proveedores de recursos**.

1. Seleccione y registre *Microsoft.Batch* si aún no está registrado.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="Confirmación del ":::estado &quot;Registrado&quot;.

Si no ve *Microsoft.Batch* en la lista, puede [crear una cuenta de Azure Batch vacía](../batch/batch-account-create-portal.md) en esa suscripción para eliminarla más adelante. 

## <a name="delegate-a-subnet-to-azure-batch"></a><a name="delegatesubnet">Delegar una subred en Azure Batch</a>

Si usa la inserción de red virtual rápida, debe delegar la subred, en la que se insertará la instancia de Azure-SSIS IR, en Azure Batch, la infraestructura subyacente para SSIS en ADF. Con Azure Portal, puede hacerlo siguiendo estos pasos:

1. Después de seleccionar la red virtual en Azure Portal, seleccione **Subredes** en el menú de la izquierda.

1. Seleccione el nombre de la subred para abrir su panel y asegúrese de que tiene direcciones IP disponibles al menos dos veces el número de nodos de Azure-SSIS IR.

1. Seleccione *Microsoft.Batch/batchAccounts* en el menú desplegable **Delegar subred a un servicio**.

1. Seleccione el botón **Guardar**.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/delegate-subnet-to-batch.png" alt-text="Delegar subred en Azure Batch":::

## <a name="grant-virtual-network-permissions"></a><a name="grantperms"></a>Concesión de permisos de red virtual

Con Azure Portal, puede conceder al usuario que crea Azure-SSIS IR los permisos de control de acceso basado en rol (RBAC) necesarios para unirse a la red virtual o subred. Puede hacerlo siguiendo estos pasos:

1. Después de seleccionar la red virtual en Azure Portal, si usa la inserción de red virtual estándar, puede seleccionar **Control de acceso (IAM)** en el menú de la izquierda. Si usa la inserción de  red virtual rápida, puede seleccionar **Subredes** en el menú  de la izquierda, seleccionar la fila de subred y, después, seleccionar **Administrar usuarios** en el menú superior para abrir la página **Control de acceso**.

1. Seleccione el botón **Agregar asignación de roles** para abrir la página **Agregar asignación de roles**.

1. Seleccione *Colaborador de red* o su rol personalizado en la lista **Rol** y seleccione el botón **Siguiente**.

1. Seleccione la opción **Usuario, grupo o entidad de servicio** en la sección **Asignar acceso a**.

1. Seleccione el vínculo **Seleccionar miembros** para buscar y seleccione el usuario que crea Azure-SSIS IR.

1. Seleccione los botones **Seleccionar**, **Siguiente** y **Revisar y asignar**.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/grant-virtual-network-permissions.png" alt-text="Concesión de permisos de red virtual":::

## <a name="next-steps"></a>Pasos siguientes

- [Método de inserción de la red virtual rápido](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Método de inserción de red virtual estándar](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Unión de Azure-SSIS IR a una red virtual mediante la interfaz de usuario de ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Unión de Azure-SSIS IR a una red virtual mediante Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Para más información acerca de Azure-SSIS IR, consulte los siguientes artículos: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual general acerca de los entornos de ejecución de integración, incluido Azure-SSIS IR. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este tutorial se proporcionan instrucciones paso a paso para crear Azure-SSIS IR. Usa el servidor de Azure SQL Database para hospedar SSISDB. 
- [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial. Proporciona instrucciones sobre el uso de un servidor de Azure SQL Database configurado con un punto de conexión de servicio de red virtual, una regla de firewall de IP, un punto de conexión privado o Azure SQL Managed Instance que se conecta a una red virtual para hospedar SSISDB. Muestra cómo unir Azure-SSIS IR a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar y comprender la información acerca de Azure-SSIS IR.
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Azure SSIS IR mediante la adición de más nodos.
