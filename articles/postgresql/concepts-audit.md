---
title: Registro de auditoría en Azure Database for PostgreSQL con un único servidor
description: Conceptos para el registro de auditoría de pgAudit en Azure Database for PostgreSQL con un único servidor.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 8a60c1db1ca3b3037aded6ed7d7a88f237edfe4e
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458707"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Registro de auditoría en Azure Database for PostgreSQL con un único servidor

Registro de auditoría de actividades de base de datos en Azure Database for PostgreSQL: único servidor disponible por medio de la extensión de auditoría de PostgreSQL, [pgAudit](https://www.pgaudit.org/). La extensión pgAudit proporciona registro de auditoría detallado de objetos y de sesiones.

> [!NOTE]
> La extensión pgAudit se encuentra en versión preliminar en Azure Database for PostgreSQL. Solo se puede habilitar en servidores optimizados para memoria y de uso general.

Si quiere obtener registros de nivel de recurso de Azure de operaciones como el escalado de procesos y de almacenamiento, vea [Introducción a los registros de plataforma de Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Consideraciones de uso

De manera predeterminada, las instrucciones de registro de pgAudit se emiten junto con las instrucciones de registro normales mediante la utilidad de registro estándar de Postgres. En Azure Database for PostgreSQL, estos archivos .log se pueden descargar mediante Azure Portal o la CLI de Azure. El almacenamiento máximo de la colección de archivos es de 1 GB. Cada archivo está disponible durante un máximo de siete días. El valor predeterminado es tres días. Este servicio es una opción de almacenamiento a corto plazo.

Como alternativa, puede configurar todos los registros de modo que se envíen al almacén de registros de Azure Monitor para su análisis posteriormente en Log Analytics. Si habilita el registro de recursos de Monitor, los registros se envían automáticamente en formato JSON a Azure Storage, Azure Event Hubs o los registros de Monitor, en función de su elección.

Al habilitar pgAudit, se genera un gran volumen de registros en un servidor, lo que afecta al rendimiento y al almacenamiento de registros. Es recomendable usar los registros de Monitor, lo que ofrece opciones de almacenamiento a más largo plazo, así como características de análisis y alertas. Desactive el registro estándar para reducir el impacto sobre el rendimiento del registro adicional:

   1. Establezca el parámetro `logging_collector` en **Off**.
   1. Reinicie el servidor para aplicar este cambio.

Para aprender a configurar el registro en Storage, Event Hubs o los registros de Monitor, vea la sección sobre registros de recursos de [Registros en Azure Database for PostgreSQL con un único servidor](concepts-server-logs.md).

## <a name="install-pgaudit"></a>Instalación de pgAudit

Para instalar pgAudit, tiene que incluirlo en las bibliotecas precargadas compartidas del servidor. Un cambio en el parámetro `shared_preload_libraries` de Postgres exige reiniciar el servidor para que tenga efecto. Puede cambiar los parámetros mediante el [portal](howto-configure-server-parameters-using-portal.md), la [CLI](howto-configure-server-parameters-using-cli.md) o [API REST](/rest/api/postgresql/singleserver/configurations/createorupdate).

Para usar el [portal](https://portal.azure.com):

   1. Seleccione su servidor de Azure Database for PostgreSQL.
   1. A la izquierda, en **Configuración**, seleccione **Parámetros del servidor**.
   1. Busque **shared_preload_libraries**.
   1. Seleccione **PGAUDIT**.
   
      :::image type="content" source="./media/concepts-audit/share-preload-parameter.png" alt-text="Captura de pantalla que muestra cómo Azure Database for PostgreSQL habilita shared_preload_libraries para PGAUDIT.":::

   1. Reinicie el servidor para aplicar el cambio.
   1. Compruebe que `pgaudit` está cargado en `shared_preload_libraries` mediante la ejecución de la consulta siguiente en psql:
   
        ```SQL
      show shared_preload_libraries;
      ```
      Debería ver `pgaudit` en el resultado de la consulta que devuelve`shared_preload_libraries`.

   1. Conéctese al servidor mediante un cliente como psql y habilite la extensión pgAudit:
   
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Si ve un error, confirme que ha reiniciado el servidor después de guardar `shared_preload_libraries`.

## <a name="pgaudit-settings"></a>Configuración de pgAudit

Con pgAudit puede configurar el registro de auditoría de sesión u objeto. [El registro de auditoría de sesión](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emite registros detallados de las instrucciones ejecutadas. [El registro de auditoría de objetos](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) es una auditoría con un ámbito limitado a relaciones específicas. Puede optar por configurar uno o ambos tipos de registro.

> [!NOTE]
> La configuración de pgAudit se especifica globalmente y no en un nivel de base de datos o rol.

Después de [instalar pgAudit](#install-pgaudit), puede configurar sus parámetros para iniciar el registro.

Para configurar pgAudit en el [portal](https://portal.azure.com):

   1. Seleccione su servidor de Azure Database for PostgreSQL.
   1. A la izquierda, en **Configuración**, seleccione **Parámetros del servidor**.
   1. Busque los parámetros **pg_audit**.
   1. Seleccione el parámetro de configuración adecuado para editarlo. Por ejemplo, para iniciar el registro, establezca **pgaudit.log** en **WRITE**.
   
       :::image type="content" source="./media/concepts-audit/pgaudit-config.png" alt-text="Captura de pantalla que muestra cómo Azure Database for PostgreSQL configura el registro con pgAudit.":::
   1. Haga clic en **Guardar** para guardar los cambios.

La [documentación de pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) proporciona la definición de cada parámetro. Primero pruebe los parámetros y confirme que obtiene el comportamiento esperado. Por ejemplo:

- Cuando el valor **pgaudit.log_client** está activado, redirige los registros a un proceso de cliente como psql en lugar de escribir en un archivo. En general, deje este valor deshabilitado.
- El parámetro **pgaudit.log_level** solo está habilitado cuando **pgaudit.log_client** está activado.

> [!NOTE]
> En Azure Database for PostgreSQL, **pgaudit.log** no se puede establecer con un acceso directo de signo menos (`-`) como se describe en la documentación de pgAudit. Todas las clases de instrucción necesarias, como READ y WRITE, deben especificarse individualmente.

### <a name="audit-log-format"></a>Formato de los registros de auditoría

Cada entrada de auditoría se indica mediante `AUDIT:` cerca del principio de la línea de registro. El formato del resto de la entrada se detalla en la [documentación de pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Si necesita algún otro campo para satisfacer sus requisitos de auditoría, use el parámetro `log_line_prefix` de Postgres. La cadena `log_line_prefix` se genera al principio de cada línea de registro de Postgres. Por ejemplo, la siguiente opción de `log_line_prefix` proporciona la marca de tiempo, el nombre de usuario, el nombre de la base de datos y el identificador de proceso siguiente:

```
t=%m u=%u db=%d pid=[%p]:
```

Para obtener más información sobre `log_line_prefix`, vea la [documentación de PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="get-started"></a>Introducción

Para empezar a trabajar rápidamente, establezca **pgaudit.log** en **WRITE**. Luego abra los registros para revisar la salida.

## <a name="view-audit-logs"></a>Visualización de registros de auditoría

Si usa archivos .log, los registros de auditoría se incluyen en el mismo archivo que los registros de errores de PostgreSQL. Puede descargar los archivos de registro del [portal](howto-configure-server-logs-in-portal.md) o de la [CLI](howto-configure-server-logs-using-cli.md).

Si usa el registro de recursos de Azure, la forma de acceder a los registros depende del punto de conexión que seleccione. En el caso de Storage, vea [Registros de recursos de Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). En el caso de Event Hubs, vea también [Registros de recursos de Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

En el caso de registros de Monitor, los registros se envían al área de trabajo seleccionada. Los registros de Postgres usan el modo de recopilación `AzureDiagnostics`, por lo que se pueden consultar desde la tabla `AzureDiagnostics`, como se muestra. Para obtener más información sobre las consultas y las alertas, vea [Consultas de registro en Azure Monitor](../azure-monitor/logs/log-query-overview.md).

Use esta consulta para comenzar. Puede configurar alertas basadas en las consultas.

Busque todos los registros de Postgres de un servidor determinado del último día:

```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Pasos siguientes

- [Obtenga información sobre el registro en Azure Database for PostgreSQL](concepts-server-logs.md).
- Aprenda a establecer parámetros mediante [Azure Portal](howto-configure-server-parameters-using-portal.md), la [CLI de Azure](howto-configure-server-parameters-using-cli.md) o [API REST](/rest/api/postgresql/singleserver/configurations/createorupdate).
