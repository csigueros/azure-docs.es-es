---
title: Crear una instancia de Integration Runtime de Azure SSIS en Azure Data Factory
description: Obtenga información sobre cómo crear un entorno de ejecución de integración de Azure SSIS en Azure Data Factory para que pueda implementar y ejecutar paquetes SSIS en Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/19/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3a3e6c8318309959eb2a709837040db42575b91b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215384"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Crear una instancia de Integration Runtime de Azure SSIS en Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este artículo se describen los pasos para aprovisionar una instancia de Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) en Azure Data Factory (ADF). Azure-SSIS Integration Runtime admite:

- La ejecución de paquetes implementados en el catálogo de SSIS (SSISDB) hospedados por un servidor de Azure SQL Database o por Instancia administrada (modelo de implementación de proyectos)
- La ejecución de paquetes implementados en el sistema de archivos, en Azure Files o en una base de datos de SQL Server (MSDB) hospedados por Instancia administrada de Azure SQL (modelo de implementación de paquetes)

Después de aprovisionar una instancia de Azure-SSIS IR, puede usar herramientas conocidas para implementar y ejecutar los paquetes en Azure. Estas herramientas ya están habilitadas para Azure e incluyen SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) y utilidades de la línea de comandos, como [dtutil](/sql/integration-services/dtutil-utility) y [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

En el tutorial [Aprovisionamiento de Azure-SSIS IR ](./tutorial-deploy-ssis-packages-azure.md) se muestra cómo crear una instancia de Azure-SSIS IR mediante Azure Portal o la aplicación Data Factory. También se muestra cómo usar de manera opcional un servidor o una instancia administrada de Azure SQL Database para hospedar SSISDB. Este artículo es una extensión del tutorial y en él se describe cómo hacer las siguientes tareas opcionales:

- Usar un servidor de Azure SQL Database con reglas de firewall de IP/puntos de conexión de servicio de red virtual o una instancia administrada con un punto de conexión privado para hospedar SSISDB. Como requisito previo, debe configurar los permisos y valores de la red virtual para que la instancia de Azure-SSIS IR se una a una red virtual.

- Use la autenticación de Azure Active Directory (Azure AD) con la identidad administrada asignada por el usuario o por el sistema especificada para la factoría de datos para conectarse a un servidor o a una instancia administrada de Azure SQL Database. Como requisito previo, debe agregar la identidad administrada asignada por el usuario o por el sistema especificada para la factoría de datos como un usuario de base de datos capaz de crear instancias de SSISDB.

- Unir la instancia de Azure-SSIS IR a una red virtual o configurar IR autohospedado como proxy para que Azure-SSIS IR tenga acceso a los datos locales.

En este artículo se muestra cómo aprovisionar una instancia de Azure-SSIS IR mediante [Azure Portal](create-azure-ssis-integration-runtime-portal.md), [Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md) y una [plantilla de Azure Resource Manager](create-azure-ssis-integration-runtime-resource-manager-template.md).

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Suscripción de Azure**. Si aún no tiene una suscripción, puede crear una cuenta de [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Servidor Azure SQL Database o SQL Managed Instance (opcional)** . Si aún no tiene un servidor de bases de datos o una instancia administrada, cree uno en Azure Portal antes de empezar. A su vez, Data Factory creará una instancia de SSISDB en este servidor de bases de datos. 

  Le recomendamos que cree el servidor de bases de datos o la instancia administrada en la misma región de Azure que el entorno de ejecución de integración. Esta configuración permite que el entorno de ejecución de integración escriba registros de ejecución en SSISDB sin traspasar regiones de Azure.

  Tenga en cuenta los siguientes puntos:

  - La instancia de SSISDB puede crearse en su nombre como una base de datos única, como parte de un grupo elástico o en una instancia administrada. Se puede acceder a ella en una red pública o mediante la unión a una red virtual. Para más información sobre cómo elegir entre SQL Database y SQL Managed Instance para hospedar SSISDB, consulte la sección [Comparación entre SQL Database y SQL Managed Instance](#comparison-of-sql-database-and-sql-managed-instance) de este artículo. 
  
    Si usa un servidor de Azure SQL Database con reglas de firewall de IP/puntos de conexión de servicio de red virtual o una instancia administrada de SQL con punto de conexión privado para hospedar SSISDB, o si necesita acceder a datos locales sin configurar IR autohospedado, debe unir la instancia de Azure-SSIS IR a una red virtual. Para más información, consulte [Unión de una instancia de Azure-SSIS IR a una red virtual](./join-azure-ssis-integration-runtime-virtual-network.md).

  - Confirme que el valor **Permitir el acceso a servicios de Azure** está habilitado para el servidor de bases de datos. Esta configuración no es aplicable si para hospedar SSISDB se usa el servidor de Azure SQL Database con reglas de firewall de red/puntos de conexión de servicio de red virtual o una instancia administrada de SQL con punto de conexión privado. Para más información, consulte [Protección de Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Para habilitar este valor mediante PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Agregue la dirección IP de la máquina cliente, o un intervalo de direcciones IP que la incluya, a la lista de direcciones IP de cliente de la configuración del firewall del servidor de bases de datos. Para más información, consulte [Reglas de firewall de nivel de base de datos y de nivel de servidor de Azure SQL Database](../azure-sql/database/firewall-configure.md).

  - Puede conectarse al servidor de bases de datos mediante la autenticación de SQL con sus credenciales de administrador del servidor o por medio de la autenticación de Azure Active Directory con la identidad administrada asignada por el usuario o por el sistema especificada para la factoría de datos. En el último de los casos, debe agregar la identidad administrada asignada por el usuario o por el sistema especificada para la factoría de datos a un grupo de Azure AD con permisos de acceso al servidor de bases de datos. Para más información, consulte [Habilitar la autenticación de Azure AD para una instancia de Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md).

  - Confirme que el servidor de bases de datos no tiene aún una instancia de SSISDB. El aprovisionamiento de Azure-SSIS IR no admite el uso de una instancia de SSISDB existente.

- **Red virtual de Azure Resource Manager (opcional)** . Debe tener una red virtual de Azure Resource Manager si se cumple al menos una de las siguientes condiciones:

  - Va a hospedar SSISDB en un servidor de Azure SQL Database con reglas de firewall de IP/puntos de conexión de servicio de red virtual o una instancia administrada con un punto de conexión privado.

  - Quiere conectarse a almacenes de datos locales desde paquetes SSIS que se ejecutan en Azure-SSIS IR sin configurar IR autohospedado.

- **Azure PowerShell (opcional)** . Siga las instrucciones que se indican en [Instalación y configuración de Azure PowerShell](/powershell/azure/install-az-ps), si quiere ejecutar un script de PowerShell para aprovisionar Azure-SSIS IR.

### <a name="regional-support"></a>Compatibilidad regional

Para ver una lista de regiones de Azure en las que Data Factory y Azure-SSIS IR están disponibles, consulte [Disponibilidad de Data Factory y SSIS IR por región](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Comparación entre SQL Database e Instancia administrada de SQL

En la tabla siguiente se comparan determinadas características de un servidor de Azure SQL Database y SQL Managed Instance y su relación con Azure SSIR IR:

| Característica | SQL Database| SQL Managed Instance |
|---------|--------------|------------------|
| **Programación** | El agente SQL Server no está disponible.<br/><br/>Consulte [Programar una ejecución de paquetes en una canalización de Data Factory](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity).| El agente de Instancia administrada está disponible. |
| **Autenticación** | Puede crear una instancia de SSISDB con un usuario de base de datos independiente que represente cualquier grupo de Azure AD con la identidad administrada de la factoría de datos como miembro del rol **db_owner**.<br/><br/>Consulte [Habilitación de la autenticación de Azure AD para crear una instancia de SSISDB en el servidor de Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-authentication-on-azure-sql-database). | Puede crear una instancia de SSISDB con un usuario de base de datos independiente que represente la identidad administrada de la factoría de datos. <br/><br/>Consulte [Habilitación de la autenticación de Azure AD para crear una instancia de SSISDB en Instancia administrada de Azure SQL](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-authentication-on-azure-sql-managed-instance). |
| **Nivel de servicio** | Al crear una instancia de Azure-SSIS IR con el servidor de Azure SQL Database, puede seleccionar el nivel de servicio de SSISDB. Hay varios niveles de servicio. | Cuando crea una instancia de Azure-SSIS IR con la instancia administrada, no puede seleccionar el nivel de servicio de SSISDB. Todas las bases de datos de la instancia administrada comparten el mismo recurso asignado a esa instancia. |
| **Red virtual** | Su instancia de Azure-SSIS IR puede unirse a una red virtual de Azure Resource Manager si usa un servidor de Azure SQL Database con reglas de firewall de IP/puntos de conexión de servicio de red virtual. | Su instancia de Azure-SSIS IR puede unirse a una red virtual Azure Resource Manager si usa una instancia administrada con un punto de conexión privado. La red virtual es necesaria cuando no se habilita un punto de conexión público para la instancia administrada.<br/><br/>Si une la instancia de Azure-SSIS IR a la misma red virtual que la instancia administrada, asegúrese de que ambas instancias se encuentran en subredes diferentes. Si une la instancia de Azure-SSIS IR a una red virtual diferente de aquella de la instancia administrada, se recomienda realizar un emparejamiento de red virtual o una conexión de red virtual a red virtual. Consulte [Conexión de la aplicación a una instancia administrada de Azure SQL Database](../azure-sql/managed-instance/connect-application-instance.md). |
| **Transacciones distribuidas** | Esta característica se admite mediante transacciones elásticas. No se admiten las transacciones del Coordinador de transacciones distribuidas de Microsoft (MSDTC). Si los paquetes SSIS usan MSDTC para coordinar las transacciones distribuidas, considere la posibilidad de migrar a transacciones elásticas de Azure SQL Database. Para más información, consulte [Transacciones distribuidas en bases de datos en la nube](../azure-sql/database/elastic-transactions-overview.md). | No compatible. |
| | | |


## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre cómo aprovisionar Azure-SSIS IR mediante Azure Portal](create-azure-ssis-integration-runtime-portal.md).
- [Más información sobre cómo aprovisionar Azure-SSIS IR mediante Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md).
- [Más información sobre cómo aprovisionar Azure-SSIS IR mediante una plantilla de Azure Resource Manager](create-azure-ssis-integration-runtime-resource-manager-template.md).
- [Implementación y ejecución de los paquetes SSIS en Azure Data Factory](create-azure-ssis-integration-runtime-deploy-packages.md).

Consulte otros temas sobre Azure-SSIS IR en esta documentación:

- [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información sobre los entornos de ejecución de integración en general, incluido Azure-SSIS IR.
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar y comprender la información acerca de Azure-SSIS IR.
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Azure SSIS IR mediante la adición de más nodos.
- [Implementación, ejecución y supervisión de paquetes de SSIS en Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Conexión a SSISDB en Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Conexión a orígenes de datos locales con autenticación de Windows) 
- [Programación de la ejecución de paquetes en Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
