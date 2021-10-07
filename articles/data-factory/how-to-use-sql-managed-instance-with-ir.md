---
title: Uso de Instancia administrada de Azure SQL con Azure-SQL Server Integration Services (SSIS) en Azure Data Factory
description: Obtenga información sobre el uso de Instancia administrada de Azure SQL con SQL Server Integration Services (SSIS) en Azure Data Factory.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: ce75b0439bdf14c8894fe91267ae3b88508a89d4
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400332"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Uso de Instancia administrada de Azure SQL con SQL Server Integration Services (SSIS) en Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Ahora los proyectos, paquetes y cargas de trabajo de SQL Server Integration Services (SSIS) se pueden mover a la nube de Azure. Implemente, ejecute y administre proyectos y paquetes de SSIS en Azure SQL Database o Instancia administrada de SQL con herramientas conocidas, como SQL Server Management Studio (SSMS). En este artículo se resaltan las siguientes áreas específicas al usar Instancia administrada de Azure SQL con Azure-SSIS Integration Runtime (IR):

- [Aprovisionamiento de una instancia de Azure-SSIS IR con el catálogo de SSIS (SSISDB) hospedada por Instancia administrada de Azure SQL](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [Ejecución de paquetes SSIS del trabajo del agente de Instancia administrada de Azure SQL](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Limpieza de los registros de SSISDB del trabajo del agente de Instancia administrada de Azure SQL](#clean-up-ssisdb-logs)
- [Conmutación por error de Azure-SSIS IR con Instancia administrada de Azure SQL](configure-bcdr-azure-ssis-integration-runtime.md)
- [Migración de las cargas de trabajo de SSIS locales a SSIS en ADF con Instancia administrada de Azure SQL como destino de la carga de trabajo de base de datos](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Aprovisionamiento de Azure-SSIS IR con SSISDB hospedado por Instancia administrada de Azure SQL

### <a name="prerequisites"></a>Requisitos previos

1. [Habilite Azure Active Directory (Azure AD) en Instancia administrada de Azure SQL](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance), al elegir la autenticación de Azure Active Directory.

1. Elija cómo conectar Instancia administrada de SQL, a través de un punto de conexión privado o de un punto de conexión público:

    - A través de un punto de conexión privado (preferido)

        1. Elija la red virtual a la que se va a unir Azure-SSIS IR:
            - Dentro de la misma red virtual que la instancia administrada, con una **subred diferente**.
            - Dentro de una red virtual diferente a la de la instancia administrada, a través del emparejamiento de redes virtuales (que está limitado a la misma región debido a las restricciones de emparejamiento de VNet global) o una conexión de red virtual a red virtual.

            Para más información sobre la conectividad de la instancia administrada de Azure SQL, vea [Conexión de la aplicación a Instancia administrada de Azure SQL](../azure-sql/managed-instance/connect-application-instance.md).

        1. [Configure la red virtual](#configure-virtual-network).

    - A través de un punto de conexión público

        Instancia administrada de Azure SQL puede proporcionar conectividad a través de [puntos de conexión públicos](../azure-sql/managed-instance/public-endpoint-configure.md). Los requisitos de entrada y salida deben cumplirse para permitir el tráfico entre la instancia administrada de SQL y Azure-SSIS IR:

        - Cuando Azure-SSIS IR no está dentro de una red virtual (opción preferida).

            **Requisito de entrada de la instancia administrada de SQL**, para permitir el tráfico entrante desde Azure-SSIS IR.

            | Protocolo de transporte | Source | Intervalo de puertos de origen | Destination | Destination port range |
            |---|---|---|---|---|
            |TCP|Etiqueta de servicio de Azure Cloud|*|VirtualNetwork|3342|

            Para obtener más información, consulte [Permitir el tráfico del punto de conexión público en el grupo de seguridad de red](../azure-sql/managed-instance/public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).

        - Cuando Azure-SSIS IR está en una red virtual

            Hay un escenario especial en que la instancia administrada de SQL se encuentra en una región que Azure-SSIS IR no admite y Azure-SSIS IR se encuentra dentro de una red virtual sin emparejamiento de VNet debido a una limitación de emparejamiento de VNet global. En este escenario, **Azure-SSIS IR dentro de una red virtual** conecta la instancia administrada de SQL **a través del punto de conexión público**. Use las reglas de grupo de seguridad de red (NSG) siguientes para permitir el tráfico entre la instancia administrada de SQL y Azure-SSIS IR:

            1. **Requisito de entrada de la instancia administrada de SQL**, para permitir el tráfico entrante desde Azure-SSIS IR.

                | Protocolo de transporte | Source | Intervalo de puertos de origen | Destination |Destination port range |
                |---|---|---|---|---|
                |TCP|Dirección IP estática de Azure-SSIS IR <br> Para obtener más información, consulte el apartado [Traiga su propia dirección IP pública para Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP).|*|VirtualNetwork|3342|

             1. **Requisito de salida de Azure-SSIS IR** para permitir el tráfico saliente a la instancia administrada de SQL.

                | Protocolo de transporte | Source | Intervalo de puertos de origen | Destination |Destination port range |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[Dirección IP del punto de conexión público de la instancia administrada de SQL](../azure-sql/managed-instance/management-endpoint-find-ip-address.md)|3342|

### <a name="configure-virtual-network"></a>Configurar la red virtual

1. **Permiso del usuario**. El usuario que crea la instancia de Azure-SSIS IR debe tener como mínimo en el recurso de Azure Data Factory alguna de las siguientes [asignaciones de roles](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope):

    - Use el rol Colaborador de red integrado. Este rol requiere el permiso _Microsoft.Network /\*_ , que tiene un ámbito mucho mayor del necesario.
    - Crear un rol personalizado que incluya solo el permiso _Microsoft.Network/virtualNetworks/\*/join/action_ necesario. Si también quiere traer sus propias direcciones IP públicas para Azure-SSIS IR mientras realiza la unión a una red virtual de Azure Resource Manager, incluya también el permiso _Microsoft.Network/publicIPAddresses/*/join/Action_ en el rol.

1. **Red virtual**.

    1. Asegúrese de que el grupo de recursos de la red virtual puede crear y eliminar determinados recursos de Azure Network.

        La instancia de Integration Runtime para la integración de SSIS en Azure necesita crear determinados recursos de red en el mismo grupo de recursos de la red virtual. Estos recursos incluyen:
        - Instancia de Azure Load Balancer, con el nombre *\<Guid>-azurebatch-cloudserviceloadbalancer*.
        - Grupo de seguridad de red, con el nombre *\<Guid>-azurebatch-cloudservicenetworksecuritygroup.
        - Una dirección IP pública de Azure llamada -azurebatch-cloudservicepublicip.

        Estos recursos se crearán al iniciarse la instancia de Azure-SSIS IR. Se eliminarán cuando esta se detenga. Para evitar que se bloquee la detención en la instancia de Azure-SSIS IR, no vuelva a usar estos recursos de red en los demás recursos.

    1. Asegúrese de que no tiene ningún [bloqueo de recursos](../azure-resource-manager/management/lock-resources.md) en el grupo de recursos ni en la suscripción a la que pertenece la red virtual. Si configura un bloqueo de solo lectura o de eliminación, se producirá un error al iniciar o detener la instancia de Azure-SSIS IR, o esta dejará de responder.

    1. Asegúrese de no tener ninguna definición de Azure Policy que impida que se creen los siguientes recursos en el grupo de recursos o en la suscripción a la que pertenece la red virtual:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

    1. Regla para permitir el tráfico en el grupo de seguridad de red (NSG), para permitir el tráfico entre la instancia administrada de SQL y Azure-SSIS IR, y el tráfico necesario para Azure-SSIS IR.
        1. **Requisito de entrada de la instancia administrada de SQL**, para permitir el tráfico entrante desde Azure-SSIS IR.

            | Protocolo de transporte | Source | Intervalo de puertos de origen | Destination | Destination port range | Comentarios |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000-11999|Si la directiva de conexión del servidor de SQL Database está establecida en **Proxy**, en lugar de **Redirigir**, solo se necesita el puerto 1433.|

        1. **Requisito de salida de Azure-SSIS IR** para permitir el tráfico saliente a la instancia administrada de SQL y el resto de tráfico necesario para Azure-SSIS IR.

        | Protocolo de transporte | Source | Intervalo de puertos de origen | Destination | Destination port range | Comentarios |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 |Permitir el tráfico saliente a la instancia administrada de SQL. Si la directiva de conexión se establece en **Proxy** en lugar de en **Redirigir**, solo se necesita el puerto 1433. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | Los nodos de Azure-SSIS Integration Runtime en la red virtual usan este puerto para acceder a servicios de Azure, como Azure Storage y Azure Event Hubs. |
        | TCP | VirtualNetwork | * | Internet | 80 | (Opcional). Los nodos de Azure-SSIS Integration Runtime en la red virtual usan este puerto para descargar una lista de revocación de certificados de Internet. Si bloquea este tráfico, puede experimentar una degradación del rendimiento al iniciar el entorno de ejecución de integración y perder capacidad para comprobar el uso de certificados en la lista de revocación de certificados. Si desea restringir aún más el destino a determinados FQDN, consulte [Uso de Azure ExpressRoute o de una ruta definida por el usuario](./azure-ssis-integration-runtime-virtual-network-configuration.md#route).|
        | TCP | VirtualNetwork | * | Storage | 445 | (Opcional). Esta regla solo es necesaria cuando quiera ejecutar un paquete SSIS almacenado en Azure Files. |
        |||||||

        1. **Requisito de entrada de Azure-SSIS IR** para permitir el tráfico necesario para Azure-SSIS IR.

        | Protocolo de transporte | Source | Intervalo de puertos de origen | Destination | Destination port range | Comentarios |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (si conecta el entorno de ejecución de integración a una red virtual de Resource Manager) <br/><br/>10100, 20100, 30100 (si une la instancia de Integration Runtime a una red virtual clásica)| El servicio Data Factory usa estos puertos para comunicarse con los nodos de Azure-SSIS Integration Runtime en la red virtual. <br/><br/> Tanto si crea un grupo de seguridad de red en el nivel de subred como si no lo hace, Data Factory siempre configura uno en el nivel de las tarjetas de interfaz de red (NIC) conectadas a las máquinas virtuales que hospedan Azure-SSIS Integration Runtime. Se permite solo el tráfico entrante desde direcciones IP de Data Factory en los puertos especificados por ese grupo de seguridad de red a nivel de NIC. Aunque si se abren estos puertos al tráfico de Internet en el nivel de la subred, el tráfico de las direcciones IP que no sean de Data Factory se bloquea en el nivel de las tarjetas de interfaz de red. |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (Opcional) Esta regla solo es necesaria cuando el servicio de soporte técnico de Microsoft pide al cliente que abra la solución de problemas avanzada y se puede cerrar justo después de solucionar el problema. La etiqueta de servicio **CorpNetSaw** permite que únicamente las estaciones de trabajo de acceso seguro de la red corporativa de Microsoft usen el Escritorio remoto. Esta etiqueta de servicio no se puede seleccionar desde el portal, y solo está disponible a través de Azure PowerShell o la CLI de Azure. <br/><br/> En el nivel de NIC del NSG, el puerto 3389 está abierto de forma predeterminada, y le permite controlar el puerto 3389 en el nivel de subred del NSG, mientras tanto, por motivos de protección, Azure-SSIS Integration Runtime ha deshabilitado el puerto 3389 de salida de forma predeterminada en la regla de Firewall de Windows en cada nodo del entorno de ejecución de integración. |
        |||||||

    1. Consulte [Configuración de red virtual](azure-ssis-integration-runtime-virtual-network-configuration.md) para obtener más información:
        - Si trae sus propias direcciones IP públicas para Azure-SSIS IR.
        - Si usa su propio servidor de Sistema de nombres de dominio (DNS).
        - Si usa Azure ExpressRoute o una ruta definida por el usuario (UDR).
        - Si usa una instancia personalizada de Azure-SSIS IR.

### <a name="provision-azure-ssis-integration-runtime"></a>Aprovisionamiento de Azure-SSIS Integration Runtime

1. Seleccione el punto de conexión privado o el punto de conexión público de la instancia administrada de SQL.

    Si [aprovisionamiento de Azure-SSIS IR](create-azure-ssis-integration-runtime-portal.md#provision-an-azure-ssis-integration-runtime) tiene lugar en la aplicación Azure Portal/ADF, en la página Configuración de SQL, use el **punto de conexión privado** o el **punto de conexión público** de la instancia administrada de SQL al crear el catálogo de SSIS (SSISDB).

    El nombre de host del punto de conexión público tiene el formato <mi_nombre>.public.<zona_dns>.database.windows.net y el puerto usado para la conexión es 3342.  

    :::image type="content" source="./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png" alt-text="La captura de pantalla muestra la configuración de Integration Runtime con la casilla Create SSIS catalog (Crear catálogo de SSIS) activada y el punto de conexión del servidor de base de datos de catálogo especificado.":::

1. Seleccione autenticación de Azure AD cuando corresponda.

    :::image type="content" source="./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png" alt-text="Punto de conexión público del catálogo":::

    Para más información sobre cómo habilitar la autenticación de Azure AD, vea [Habilitación de Azure AD en Instancia administrada de Azure SQL](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Una Azure-SSIS Integration Runtime a la red virtual cuando corresponda.

    En la página Configuración avanzada, seleccione Virtual Network y la subred con la que se realizará la unión.
    
    Cuando esté dentro de la misma red virtual que la instancia administrada de SQL, elija una **subred diferente** de instancia administrada de SQL. 

    Para obtener más información sobre cómo unir Azure-SSIS IR a una red virtual, consulte [Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual](join-azure-ssis-integration-runtime-virtual-network.md).

    :::image type="content" source="./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png" alt-text="Captura de pantalla que muestra la configuración avanzada de Integration Runtime, donde puede seleccionar una red virtual a la que se una su tiempo de ejecución.":::

Para más información sobre cómo crear una instancia de Azure-SSIS IR, consulte [Creación de una instancia de Integration Runtime de SSIS de Azure en Azure Data Factory](create-azure-ssis-integration-runtime-portal.md#provision-an-azure-ssis-integration-runtime).

## <a name="clean-up-ssisdb-logs"></a>Limpieza de registros de SSISDB

La directiva de retención de registros de SSISDB se define mediante las siguientes propiedades de [catalog.catalog_properties](/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database):

- OPERATION_CLEANUP_ENABLED

    Si el valor es TRUE, se eliminarán del catálogo los detalles y los mensajes de la operación anteriores a RETENTION_WINDOW (días). Si el valor es FALSE, todos los detalles y los mensajes de la operación se almacenan en el catálogo. Nota: Un trabajo de SQL Server realiza la limpieza de operaciones.

- RETENTION_WINDOW

    Número de días que los detalles y los mensajes de la operación estarán almacenados en el catálogo. Si el valor es -1, la ventana de retención es infinita. Nota: Si no quiere realizar ninguna limpieza, establezca OPERATION_CLEANUP_ENABLED en FALSE.

Para eliminar registros de SSISDB que están fuera del intervalo de retención establecido por el administrador, puede desencadenar el procedimiento almacenado `[internal].[cleanup_server_retention_window_exclusive]`. De manera opcional, puede programar la ejecución del trabajo del agente de la instancia administrada de SQL para desencadenar el procedimiento almacenado.

## <a name="next-steps"></a>Pasos siguientes

- [Ejecución de paquetes SSIS del trabajo del agente de Instancia administrada de Azure SQL](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Configuración de la continuidad empresarial y la recuperación ante desastres (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [Migración de cargas de trabajo de SSIS locales a SSIS en ADF](scenario-ssis-migration-overview.md)