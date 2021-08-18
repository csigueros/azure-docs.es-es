---
title: 'Problemas comunes: Azure Database Migration Service'
description: Obtenga información sobre cómo solucionar problemas conocidos y errores asociados con el uso de Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: e5c10b4830a1bba5ff4db07b81ee447e5d33b731
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750834"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Solucionar problemas y errores comunes de Azure Database Migration Service

En este artículo se describen algunos problemas y errores comunes que pueden encontrarse los usuarios de Azure Database Migration Service. El artículo también incluye información sobre cómo resolver dichos problemas y errores.

> [!NOTE]
> Comunicación sin prejuicios
>
> Microsoft admite un entorno diverso e inclusivo. En este artículo se incluyen referencias a la palabra _esclavo_. En la [guía de estilo para la comunicación sin prejuicios](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) de Microsoft se reconoce que se trata de una palabra excluyente. Se usa en este artículo por coherencia, ya que actualmente es la palabra que aparece en el software. Cuando el software se actualice para quitarla, este artículo se actualizará en consecuencia.
>

## <a name="migration-activity-in-queued-state"></a>Actividad de migración en estado en cola

Al crear nuevas actividades en un proyecto de Azure Database Migration Service, las actividades permanecen en un estado en cola.

| Causa         | Resolución |
| ------------- | ------------- |
| Este problema se produce cuando la instancia de Azure Database Migration Service ha alcanzado la capacidad máxima para tareas simultáneas. Cualquier actividad nueva se pone en cola hasta que haya capacidad disponible. | Valide la instancia de Data Migration Service que tiene actividades en ejecución en los proyectos. Puede seguir creando nuevas actividades que se agregarán automáticamente a la cola para su ejecución. Tan pronto como se complete cualquiera de las actividades en ejecución existentes, la siguiente actividad en cola empezará a ejecutarse y el estado cambiará automáticamente a estado de ejecución. No es necesario que realice ninguna otra acción para iniciar la migración de la actividad en cola.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Se han seleccionado para la migración más bases de datos que el número máximo

El siguiente error se produce al crear una actividad para un proyecto de migración de base de datos a Azure SQL Database o Instancia administrada de Azure SQL:

* **Error**: "Error de validación de la configuración de migración", "errorDetail":"Se han seleccionado para la migración más del número máximo de '4' objetos de 'Bases de datos'."

| Causa         | Resolución |
| ------------- | ------------- |
| Este error se muestra cuando se han seleccionado más de cuatro bases de datos para una única actividad de migración. Actualmente, cada actividad de migración está limitada a cuatro bases de datos. | Seleccione un máximo de cuatro bases de datos por actividad de migración. Si tiene que migrar más de cuatro bases de datos en paralelo, aprovisione otra instancia de Azure Database Migration Service. Actualmente, cada suscripción admite hasta dos instancias de Azure Database Migration Service.<br><br> |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Error al intentar detener Azure Database Migration Service

Recibe el siguiente error al detener la instancia de Azure Database Migration Service:

* **Error**: No se pudo detener el servicio. Error: {'error':{'code':'InvalidRequest','message':'Se están ejecutando actualmente una o más actividades. Para detener el servicio, espere hasta que se hayan completado las actividades o deténgalas manualmente y vuelva a intentarlo.'}}

| Causa         | Resolución |
| ------------- | ------------- |
| Este error se muestra cuando la instancia de servicio que está intentando detener incluye actividades que siguen en ejecución o están presentes en proyectos de migración. <br><br><br><br><br><br> | Asegúrese de que no hay ninguna actividad en ejecución en la instancia de Azure Database Migration Service que intenta detener. También puede eliminar las actividades o proyectos antes de intentar detener el servicio. Los siguientes pasos muestran cómo quitar proyectos para limpiar la instancia de servicio de migración eliminando todas las tareas en ejecución:<br>1. Install-Module -Name AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription -SubscriptionName "\<subName>" <br> 4. Remove-AzureRmDataMigrationProject -Name \<projectName> -ResourceGroupName \<rgName> -ServiceName \<serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Error al intentar iniciar Azure Database Migration Service

Recibe el siguiente error al iniciar la instancia de Azure Database Migration Service:

* **Error**: El servicio no se inicia. Error: {'errorDetail':'No se pudo iniciar el servicio, póngase en contacto con el soporte técnico de Microsoft'}

| Causa         | Resolución |
| ------------- | ------------- |
| Este error se muestra cuando la instancia anterior produjo un error internamente. Este error se produce con poca frecuencia y el equipo de ingeniería es consciente de ello. <br> | Elimine la instancia del servicio que no se puede iniciar y, después, aprovisione uno nuevo para reemplazarlo. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Error al restaurar la base de datos mientras se migraba la instancia administrada de SQL a Azure SQL DB

Al realizar una migración en línea desde SQL Server a Instancia administrada de Azure SQL, la transición no se completa y devuelve el siguiente error:

* **Error**: Error de operación de restauración para el identificador de operación "operationId". Código 'AuthorizationFailed', mensaje 'El cliente 'clientId' con el identificador de objeto 'objectId' no tiene autorización para realizar la acción 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' en el ámbito '/Subscriptions / subscriptionId'.'.

| Causa         | Resolución    |
| ------------- | ------------- |
| Este error indica que la entidad de seguridad de la aplicación que se está usando para la migración en línea de SQL Server a Instancia administrada de SQL no tiene permiso de colaboración en la suscripción. Ciertas llamadas API con instancias administradas requieren actualmente este permiso en la suscripción para la operación de restauración. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Use el `Get-AzureADServicePrincipal` cmdlet de PowerShell con el `-ObjectId` disponible en el mensaje de error para mostrar el nombre del identificador de aplicación que se está usando.<br><br> Valide los permisos para esta aplicación y asegúrese de que tiene el [rol Colaborador](../role-based-access-control/built-in-roles.md#contributor) en el nivel de suscripción. <br><br> El equipo de ingeniería de Azure Database Migration Service está trabajando para restringir el acceso necesario del rol de contribución actual en la suscripción. Si tiene un requisito empresarial que no permite el uso del rol de contribución, póngase en contacto con el soporte técnico de Azure para obtener ayuda adicional. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Error al eliminar la NIC asociada con Azure Database Migration Service

Al intentar eliminar una tarjeta de interfaz de red asociada con Azure Database Migration Service, se produce el siguiente error en el intento de eliminación:

* **Error**: No se puede eliminar la NIC asociada a Azure Database Migration Service debido a que el servicio DMS está utilizando la NIC

| Causa         | Resolución    |
| ------------- | ------------- |
| Este problema se produce cuando la instancia de Azure Database Migration Service todavía puede estar presente y haciendo uso de la NIC. <br><br><br><br><br><br><br><br> | Para eliminar esta NIC, elimine la instancia de servicio DMS, lo que elimina automáticamente la NIC usada por el servicio.<br><br> **Importante**: Asegúrese de que la instancia de Azure Database Migration Service que se va a eliminar no tiene ninguna actividad en ejecución.<br><br> Después de eliminar todos los proyectos y actividades asociados a la instancia de Azure Database Migration Service, puede eliminar la instancia de servicio. La NIC usada por la instancia de servicio se limpia automáticamente como parte de la eliminación del servicio. |

## <a name="connection-error-when-using-expressroute"></a>Error de conexión al usar ExpressRoute

Al intentar conectarse al origen en el Asistente para proyectos de servicio de Azure Database Migration, se produce un error en la conexión cuando se agota el tiempo de espera si se usa ExpressRoute para la conectividad.

| Causa         | Resolución    |
| ------------- | ------------- |
| Cuando se usa [ExpressRoute](https://azure.microsoft.com/services/expressroute/), Azure Database Migration Service [requiere](./tutorial-sql-server-to-azure-sql.md) que se aprovisionen tres puntos de conexión de servicio en la subred de Virtual Network asociada con el servicio:<br> --Punto de conexión de Service Bus<br> --Punto de conexión de Storage<br> --Punto de conexión de base de datos de destino (p. ej., punto de conexión de SQL, punto de conexión de Cosmos DB)<br><br><br><br><br> | [Habilite](./tutorial-sql-server-to-azure-sql.md) los puntos de conexión de servicio necesarios para la conectividad de ExpressRoute entre el origen y Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Error de tiempo de espera de bloqueo al migrar una base de datos MySQL a Azure DB for MySQL

Al migrar una base de datos MySQL a una instancia de Azure Database for MySQL a través de Azure Database Migration Service, se produce el siguiente error de tiempo de espera de bloqueo en la migración:

* **Error**: Error de migración de base de datos: no se pudo cargar el archivo. No se pudo iniciar el proceso de carga del archivo 'n' RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 Mensaje: [MySQL][ODBC Driver][mysqld] Se ha superado el tiempo de expiración de bloqueo; pruebe a reiniciar la transacción

| Causa         | Resolución    |
| ------------- | ------------- |
| Este error se produce cuando la migración no se completa debido al tiempo de expiración de bloqueo durante la migración. | Considere la posibilidad de aumentar el valor del parámetro de servidor **'innodb_lock_wait_timeout'** . El valor máximo permitido es 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Error al conectarse a SQL Server de origen cuando se usa el puerto dinámico o instancia con nombre

Cuando intenta conectar Azure Database Migration Service al origen de SQL Server que se ejecuta en la instancia con nombre o en un puerto dinámico, se produce el siguiente error en la conexión:

* **Error**: 1: Error en la conexión de SQL. Se ha producido un error relacionado con la red o específico de la instancia al establecer una conexión en SQL Server. No se encontró el servidor o no era accesible. Compruebe que el nombre de la instancia es correcto y que SQL Server está configurado para admitir conexiones remotas. (proveedor: Interfaces de red de SQL; error: 26: Error al buscar el servidor/instancia especificado)

| Causa         | Resolución    |
| ------------- | ------------- |
| Este problema se produce cuando la instancia de SQL Server de origen a la que intenta conectarse Azure Database Migration Service tiene un puerto dinámico o bien está usando una instancia con nombre. El servicio SQL Server Browser escucha en el puerto UDP 1434 las conexiones entrantes a una instancia con nombre o cuando se usa un puerto dinámico. El puerto dinámico puede cambiar cada vez que se reinicia el servicio de SQL Server. Puede comprobar el puerto dinámico asignado a una instancia mediante la configuración de red en el Administrador de configuración de SQL Server.<br><br><br> |Compruebe que Azure Database Migration Service puede conectarse al servicio de SQL Server Browser de origen en el puerto UDP 1434 y a la instancia de SQL Server a través del puerto TCP asignado dinámicamente según proceda. |

## <a name="additional-known-issues"></a>Otros problemas conocidos

* [Problemas conocidos y limitaciones de migración con las migraciones en línea a Azure SQL Database](./index.yml)
* [Problemas conocidos y limitaciones de migración con las migraciones en línea a Azure Database for PostgreSQL](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>Pasos siguientes

* Consulte el artículo [PowerShell en Azure Database Migration Service](/powershell/module/azurerm.datamigration#data_migration).
* Consulte el artículo [Cómo configurar parámetros del servidor en Azure Database for MySQL mediante Azure Portal](../mysql/howto-server-parameters.md).
* Consulte el artículo [Introducción a los requisitos previos para usar Azure Database Migration Service](./pre-reqs.md).
* Consulte las [preguntas más frecuentes sobre el uso de Azure Database Migration Service](./faq.yml).