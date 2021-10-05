---
title: Implementación de paquetes de SSIS
description: Aprenda a implementar y a ejecutar paquetes de SSIS en Azure Data Factory con el entorno de ejecución integrado de Azure-SSIS.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 09/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e12968b993a68f8eb0da179c6b4de57256a0c63b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219380"
---
# <a name="deploy-ssis-packages"></a>Implementación de paquetes de SSIS

Después de configurar el entorno de ejecución de integración de Azure-SSIS, puede implementar y ejecutar los paquetes en Azure directamente.

## <a name="using-ssisdb"></a>Uso de SSISDB

Si usa SSISDB, puede implementar los paquetes en esta base de datos y ejecutarlos en Azure-SSIS IR mediante las herramientas de SSDT o de SSMS habilitadas para Azure. Estas herramientas se conectan al servidor de bases de datos mediante su punto de conexión de servidor: 

- En el caso de un servidor de Azure SQL Database, el formato del punto de conexión de servidor es `<server name>.database.windows.net`.
- En el caso de una instancia administrada con punto de conexión privado, el formato del punto de conexión de servidor es `<server name>.<dns prefix>.database.windows.net`.
- En el caso de una instancia administrada con punto de conexión público, el formato del punto de conexión de servidor es `<server name>.public.<dns prefix>.database.windows.net,3342`. 

## <a name="using-file-system-azure-files-or-msdb"></a>Uso del sistema de archivos, Azure Files o MSDB

Si no usa SSISDB, puede implementar los paquetes en sistemas de archivos, en Azure Files o en MSDB hospedados por Azure SQL Managed Instance y ejecutarlos en Azure-SSIS IR mediante las utilidades de la línea de comandos [dtutil](/sql/integration-services/dtutil-utility) y [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md). 

Para obtener más información, vea [Implementación de proyectos o paquetes en SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

En ambos casos, también puede ejecutar los paquetes implementados en Azure-SSIS IR mediante la actividad de ejecución de paquetes SSIS de las canalizaciones de Data Factory. Para más información, consulte [Invocación de la ejecución de paquetes SSIS como una actividad de Data Factory de primera clase](./how-to-invoke-ssis-package-ssis-activity.md).

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre cómo aprovisionar Azure-SSIS IR mediante Azure Portal](create-azure-ssis-integration-runtime-portal.md).
- [Más información sobre cómo aprovisionar Azure-SSIS IR mediante Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md).
- [Más información sobre cómo aprovisionar Azure-SSIS IR mediante una plantilla de Azure Resource Manager](create-azure-ssis-integration-runtime-resource-manager-template.md).

Consulte otros temas sobre Azure-SSIS IR en esta documentación:

- [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información sobre los entornos de ejecución de integración en general, incluido Azure-SSIS IR.
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar y comprender la información acerca de Azure-SSIS IR.
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Azure SSIS IR mediante la adición de más nodos.
- [Implementación, ejecución y supervisión de paquetes de SSIS en Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Conexión a SSISDB en Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Conexión a orígenes de datos locales con autenticación de Windows) 
- [Programación de la ejecución de paquetes en Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
