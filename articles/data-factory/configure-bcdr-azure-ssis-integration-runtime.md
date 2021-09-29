---
title: Configuración de Azure-SSIS Integration Runtime para continuidad empresarial y recuperación ante desastres (BCDR)
description: En este artículo se describe cómo configurar Azure-SSIS Integration Runtime en Azure Data Factory con el grupo de conmutación por error de Azure SQL Database o Instancia administrada para la continuidad empresarial y la recuperación ante desastres (BCDR).
services: data-factory
ms.service: data-factory
ms.subservice: integration-services
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/05/2021
ms.openlocfilehash: 2da56452a8674940b3d81ffd06c722886fd07ed7
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218023"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>Configuración de Azure-SSIS Integration Runtime para continuidad empresarial y recuperación ante desastres (BCDR) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure SQL Database o Instancia administrada, y SQL Server Integration Services (SSIS) en Azure Data Factory (ADF) se pueden combinar como la solución de todas las plataformas como servicio (PaaS) recomendada para realizar la migración de SQL Server. Puede implementar los proyectos de SSIS en la base de datos de catálogo de SSIS (SSISDB) hospedada por Azure SQL Database o Instancia administrada, y ejecutar los paquetes SSIS en Azure SSIS Integration Runtime (IR) en ADF.

Para la continuidad empresarial y la recuperación ante desastres (BCDR), Azure SQL Database o Instancia administrada se pueden configurar con un [grupo de replicación geográfica o conmutación por error](../azure-sql/database/auto-failover-group-overview.md), donde SSISDB en una región principal de Azure con acceso de lectura y escritura (rol principal) se replicará de manera continuada en una región secundaria con acceso de solo lectura (rol secundario). Cuando se produce un desastre en la región principal, se desencadena una conmutación por error, donde las SSISDB principal y secundaria intercambiarán los roles.

Para BCDR, también puede configurar un par de Azure-SSIS IR en espera dual que funcione en sincronización con el grupo de conmutación por error de Azure SQL Database o Instancia administrada. Esto le permite tener un par de Azure-SSIS IR en ejecución que, en un momento dado, solo uno puede acceder a la SSISDB principal para capturar y ejecutar paquetes, así como escribir registros de ejecución de paquetes (rol principal), mientras que el otro solo puede hacer lo mismo para los paquetes implementados en otro lugar, por ejemplo en Azure Files (rol secundario). Cuando se produce la conmutación por error de SSISDB, los Azure-SSIS IR principal y secundario también intercambiarán los roles y, si los dos están en ejecución, el tiempo de inactividad será prácticamente nulo.

En este artículo se describe cómo configurar Azure-SSIS IR con un grupo de conmutación por error de Azure SQL Database o Instancia administrada para BCDR.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Configuración de un par de Azure-SSIS IR en espera dual con un grupo de conmutación por error de Azure SQL Database

Para configurar un par de Azure-SSIS IR en espera dual que funcione en sincronización con un grupo de conmutación por error de Azure SQL Database, complete los pasos siguientes.

1. Con la interfaz de usuario de ADF o Azure Portal puede crear una instancia de Azure-SSIS IR con el servidor de Azure SQL Database principal para hospedar SSISDB en la región primaria. Si tiene una instancia de Azure-SSIS IR existente que ya está conectada a la SSIDB que hospeda el servidor de Azure SQL Database principal y sigue en ejecución, debe detenerla primero para volver a configurarla. Esta será la instancia de Azure-SSIS IR principal.

   Al [seleccionar usar SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) en la página **Configuración de la implementación** del panel **Configuración de Integration Runtime**, active también la casilla **Use dual standby Azure-SSIS Integration Runtime pair with SSISDB failover** (Usar el par de Azure-SSIS Integration Runtime en modo de espera dual con conmutación por error de SSISDB). En **Dual standby pair name** (Nombre de par en espera dual), escriba un nombre para identificar el par de Azure-SSIS IR principal y secundario. Al completar la creación de la instancia de Azure-SSIS IR principal, se iniciará y adjuntará a una SSISDB principal que se creará en su nombre con acceso de lectura y escritura. Si lo acaba de volver a configurar, tendrá que reiniciarlo.

1. Con Azure Portal, puede comprobar si se ha creado la SSISDB principal en la página **Información general** del servidor de Azure SQL Database principal. Una vez que se haya creado, puede [crear un grupo de conmutación por error para los servidores de Azure SQL Database principal y secundario, y agregarle la SSISDB](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#2---create-the-failover-group) en la página **Grupos de conmutación por error**. Después de crear el grupo de conmutación por error, puede comprobar si la SSISDB principal se ha replicado en una secundaria con acceso de solo lectura en la página **Información general** del servidor de Azure SQL Database secundario.

1. Con la interfaz de usuario de ADF o Azure Portal puede crear otra instancia de Azure-SSIS IR con el servidor de Azure SQL Database secundario para hospedar SSISDB en la región secundaria. Esta será la instancia de Azure-SSIS IR secundaria. Para operaciones completas de BCDR, asegúrese de que todos los recursos de los que depende también se crean en la región secundaria, por ejemplo Azure Storage para almacenar scripts o archivos de configuración personalizados, ADF para la orquestación o programación de ejecuciones de paquetes, etc.

   Al [seleccionar usar SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) en la página **Configuración de la implementación** del panel **Configuración de Integration Runtime**, active también la casilla **Use dual standby Azure-SSIS Integration Runtime pair with SSISDB failover** (Usar el par de Azure-SSIS Integration Runtime en modo de espera dual con conmutación por error de SSISDB). En **Dual standby pair name** (Nombre de par en espera dual), escriba el mismo nombre para identificar el par de Azure-SSIS IR principal y secundario. Al completar la creación de la instancia de Azure-SSIS IR secundaria, se iniciará y adjuntará a la SSISDB secundaria.

1. Si quiere tener un tiempo de inactividad prácticamente nulo cuando se produzca la conmutación por error de SSISDB, mantenga las instancias de Azure-SSIS IR en ejecución. Solo la instancia de Azure-SSIS IR principal puede acceder a la SSISDB principal para capturar y ejecutar paquetes, así como escribir registros de ejecución de paquetes, mientras que la instancia de Azure-SSIS IR secundaria solo puede hacer lo mismo para los paquetes implementados en otra parte, por ejemplo, en Azure Files.

   Si quiere minimizar el costo de ejecución, puede detener la instancia de Azure-SSIS IR secundaria después de crearla. Cuando se produce la conmutación por error de SSISDB, las instancias de Azure-SSIS IR principal y secundaria intercambiarán los roles. Si la instancia de Azure-SSIS IR principal se detiene, tendrá que reiniciarla. En función de si se inserta en una red virtual y el método de inserción utilizado, tardará entre 5 o aproximadamente 20-30 minutos en ejecutarse.

1. Si [usa ADF para la orquestación o programación de ejecuciones de paquetes](./how-to-invoke-ssis-package-ssis-activity.md), asegúrese de que todas las canalizaciones de ADF adecuadas con actividades de Ejecución de paquetes SSIS y los desencadenadores asociados se copian en el ADF secundario con los desencadenadores deshabilitados inicialmente. Cuando se produce la conmutación por error de SSISDB, tendrá que habilitarlos.

1. Puede [probar el grupo de conmutación por error de Azure SQL Database](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#3---test-failover) y comprobar en la [página de supervisión de Azure-SSIS IR en el portal de ADF](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) si las instancias principal y secundaria de Azure-SSIS IR tienen roles intercambiados. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Configuración de un par de Azure-SSIS IR en espera dual con un grupo de conmutación por error de Azure SQL Managed Instance

Para configurar un par de Azure-SSIS IR en espera dual que funcione en sincronización con un grupo de conmutación por error de Azure SQL Managed Instance, complete los pasos siguientes.

1. Con Azure Portal, puede [crear un grupo de conmutación por error para las instancias administradas de Azure SQL principal y secundaria](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal) en la página **Grupos de conmutación por error** de la instancia administrada de Azure SQL principal.

1. Con la interfaz de usuario de ADF o Azure Portal puede crear una instancia de Azure-SSIS IR con la instancia administrada de Azure SQL principal para hospedar SSISDB en la región primaria. Si tiene una instancia de Azure-SSIS IR existente que ya está conectada a la SSIDB que hospeda la instancia administrada de Azure SQL principal y sigue en ejecución, debe detenerla primero para volver a configurarla. Esta será la instancia de Azure-SSIS IR principal.

   Al [seleccionar usar SSISDB](./create-azure-ssis-integration-runtime-portal.md#creating-ssisdb) en la página **Configuración de la implementación** del panel **Configuración de Integration Runtime**, active también la casilla **Use dual standby Azure-SSIS Integration Runtime pair with SSISDB failover** (Usar el par de Azure-SSIS Integration Runtime en modo de espera dual con conmutación por error de SSISDB). En **Dual standby pair name** (Nombre de par en espera dual), escriba un nombre para identificar el par de Azure-SSIS IR principal y secundario. Al completar la creación de la instancia de Azure-SSIS IR principal, se iniciará y adjuntará a una SSISDB principal que se creará en su nombre con acceso de lectura y escritura. Si lo acaba de volver a configurar, tendrá que reiniciarlo. También puede comprobar si la SSISDB principal se ha replicado en una secundaria con acceso de solo lectura en la página **Información general** de la instancia administrada de Azure SQL secundaria.

1. Con la interfaz de usuario de ADF o Azure Portal puede crear otra instancia de Azure-SSIS IR con la instancia administrada de Azure SQL secundaria para hospedar SSISDB en la región secundaria. Esta será la instancia de Azure-SSIS IR secundaria. Para operaciones completas de BCDR, asegúrese de que todos los recursos de los que depende también se crean en la región secundaria, por ejemplo Azure Storage para almacenar scripts o archivos de configuración personalizados, ADF para la orquestación o programación de ejecuciones de paquetes, etc.

   Al [seleccionar usar SSISDB](./create-azure-ssis-integration-runtime-portal.md#creating-ssisdb) en la página **Configuración de la implementación** del panel **Configuración de Integration Runtime**, active también la casilla **Use dual standby Azure-SSIS Integration Runtime pair with SSISDB failover** (Usar el par de Azure-SSIS Integration Runtime en modo de espera dual con conmutación por error de SSISDB). En **Dual standby pair name** (Nombre de par en espera dual), escriba el mismo nombre para identificar el par de Azure-SSIS IR principal y secundario. Al completar la creación de la instancia de Azure-SSIS IR secundaria, se iniciará y adjuntará a la SSISDB secundaria.

1. Azure SQL Managed Instance puede proteger la información confidencial de las bases de datos, como SSISDB, si los cifra mediante la clave maestra de base de datos (DMK). DMK a su vez se cifra mediante la clave maestra de servicio (SMK) de forma predeterminada. Desde septiembre de 2021, SMK se replica desde la instancia principal de Azure SQL Managed Instance a la secundaria durante la creación del grupo de conmutación por error. Si el grupo de conmutación por error se creó antes, elimine todas las bases de datos de usuario, incluida SSISDB, de la instancia secundaria de Azure SQL Managed Instance y vuelva a crear el grupo de conmutación por error.

1. Si quiere tener un tiempo de inactividad prácticamente nulo cuando se produzca la conmutación por error de SSISDB, mantenga las instancias de Azure-SSIS IR en ejecución. Solo la instancia de Azure-SSIS IR principal puede acceder a la SSISDB principal para capturar y ejecutar paquetes, así como escribir registros de ejecución de paquetes, mientras que la instancia de Azure-SSIS IR secundaria solo puede hacer lo mismo para los paquetes implementados en otra parte, por ejemplo, en Azure Files.

   Si quiere minimizar el costo de ejecución, puede detener la instancia de Azure-SSIS IR secundaria después de crearla. Cuando se produce la conmutación por error de SSISDB, las instancias de Azure-SSIS IR principal y secundaria intercambiarán los roles. Si la instancia de Azure-SSIS IR principal se detiene, tendrá que reiniciarla. En función de si se inserta en una red virtual y el método de inserción utilizado, tardará entre 5 o aproximadamente 20-30 minutos en ejecutarse.

1. Si [usa el agente de Azure SQL Managed Instance para la orquestación y programación de ejecuciones de paquetes](./how-to-invoke-ssis-package-managed-instance-agent.md), asegúrese de que todos los trabajos de SSIS adecuados con sus pasos de trabajo y las programaciones asociadas se copian en la instancia administrada de Azure SQL secundaria con las programaciones deshabilitadas inicialmente. Complete los pasos siguientes con SSMS.

   1. Para cada trabajo de SSIS, haga clic con el botón derecho y seleccione los elementos de menú desplegable **Script Job as** (Incluir trabajo como), **CREATE To** (Crear en) y **Nueva ventana del Editor de consultas** para generar su script.

      :::image type="content" source="media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png" alt-text="Generación del script de trabajo de SSIS":::

   1. Para cada script de trabajo de SSIS generado, busque el comando para ejecutar el procedimiento almacenado `sp_add_job` y modifique o quite la asignación de valores al argumento `@owner_login_name` según sea necesario.

   1. Para cada script de trabajo de SSIS actualizado, ejecútelo en la instancia administrada de Azure SQL secundaria para copiar el trabajo con sus pasos de trabajo y programaciones asociadas.

   1. Con el script siguiente, cree un trabajo de T-SQL para habilitar o deshabilitar las programaciones de trabajos de SSIS según el rol de SSISDB principal o secundario, respectivamente, en las instancias administradas de Azure SQL principal y secundaria, y ejecútelo de forma periódica. Cuando se produce la conmutación por error de SSISDB, las programaciones de trabajos de SSIS deshabilitadas se habilitarán y viceversa.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. Si [usa ADF para la orquestación o programación de ejecuciones de paquetes](./how-to-invoke-ssis-package-ssis-activity.md), asegúrese de que todas las canalizaciones de ADF adecuadas con actividades de Ejecución de paquetes SSIS y los desencadenadores asociados se copian en el ADF secundario con los desencadenadores deshabilitados inicialmente. Cuando se produce la conmutación por error de SSISDB, tendrá que habilitarlos.

1. Puede [probar el grupo de conmutación por error de Azure SQL Managed Instance](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal#test-failover) y comprobar en la [página de supervisión de Azure-SSIS IR en el portal de ADF](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) si las instancias principal y secundaria de Azure-SSIS IR tienen roles intercambiados. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Asociación de una instancia nueva de Azure-SSIS IR a una SSISDB existente hospedada por Azure SQL Database o Instancia administrada

Si se produce un desastre que afecta a la instancia de Azure-SSIS IR existente pero no a Azure SQL Database ni a Instancia administrada en la misma región, puede reemplazarlo por una nueva en otra región. Para adjuntar la SSISDB existente hospedada por Azure SQL Database o Instancia administrada a una nueva instancia de Azure-SSIS IR, complete los pasos siguientes.

1. Si la instancia de Azure-SSIS IR existente todavía está en ejecución, primero debe detenerla mediante la interfaz de usuario de Azure Portal o ADF, o bien Azure PowerShell. Si el desastre afecta también a ADF en la misma región, puede omitir este paso.

1. Con SSMS, ejecute el comando siguiente para SSISDB en Azure SQL Database o Instancia administrada para actualizar los metadatos que permitirán las conexiones desde la nueva instancia de ADF o Azure-SSIS IR.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. Con la [interfaz de usuario de Azure Portal o ADF](./create-azure-ssis-integration-runtime-portal.md), o [Azure PowerShell](./create-azure-ssis-integration-runtime-powershell.md), cree la instancia de ADF o Azure-SSIS IR con el nombre *NuevaInstanciaDeADF*/*NuevaInstanciaDeAzureSSISIR*, respectivamente, en otra región. Si usa la interfaz de usuario de Azure Portal o ADF, puede omitir el error de conexión de prueba en la página **Configuración de implementación** del panel **Configuración de Integration Runtime**.

## <a name="next-steps"></a>Pasos siguientes

Puede tener en cuenta estas otras opciones de configuración para Azure-SSIS IR:

- [Configuración de almacenes de paquetes para Azure-SSIS IR](./create-azure-ssis-integration-runtime-portal.md#creating-azure-ssis-ir-package-stores)

- [Configuración de instalaciones personalizadas para Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Configuración de la inyección de redes virtuales para la instancia de Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md)

- [Configuración del entorno de ejecución de integración autohospedado como un proxy para Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)
