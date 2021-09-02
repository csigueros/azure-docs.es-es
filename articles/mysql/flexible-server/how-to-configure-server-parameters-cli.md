---
title: 'Configuración de parámetros de servidor - CLI de Azure: servidor flexible de Azure Database for MySQL'
description: En este artículo se describe cómo configurar los parámetros de servicio en el servidor flexible de Azure Database for MySQL mediante la utilidad de la línea de comandos de la CLI de Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: bd1be8c63e295aedead72fdd5c6ce407179dc660
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652388"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Configuración de parámetros de servidor en el servidor flexible de Azure Database for MySQL mediante la CLI de Azure

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Actualmente, Azure Database for MySQL: servidor flexible se encuentra en versión preliminar pública.

Puede enumerar, mostrar y actualizar los parámetros de un servidor flexible de Azure Database for MySQL con la CLI de Azure, la utilidad de la línea de comandos de Azure. Los parámetros de servidor se configuran con los valores predeterminados y recomendados al crear el servidor.  

En este artículo se describe cómo enumerar, mostrar y actualizar los parámetros del servidor mediante la CLI de Azure.

>[!Note]
> Estos parámetros se pueden actualizar globalmente en el nivel de servidor; use la [CLI de Azure](./how-to-configure-server-parameters-cli.md) o [Azure Portal](./how-to-configure-server-parameters-portal.md).

## <a name="prerequisites"></a>Prerrequisitos

Para seguir esta guía, necesitará:

- [Un servidor flexible de Azure Database for MySQL](quickstart-create-server-cli.md)
- La utilidad de línea de comandos [CLI de Azure](/cli/azure/install-azure-cli) o usar Azure Cloud Shell en el explorador.

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>Enumeración de parámetros del servidor en el servidor de red de Azure Database for MySQL

Para obtener una lista de todos los parámetros en un servidor y sus valores, ejecute el comando [az mysql flexible-server parameter list](/cli/azure/mysql/flexible-server/parameter).

Puede enumerar los parámetros del servidor **mydemoserver.mysql.database.azure.com** en el grupo de recursos **myresourcegroup**.
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
Para ver la definición de cada uno de los parámetros enumerados, consulte la sección de referencia de MySQL en [Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) (Variables del sistema del servidor).

## <a name="show-server-parameter-details"></a>Visualización de los detalles de los parámetros de servidor

Para mostrar los detalles de un parámetro específico de un servidor, ejecute el comando [az mysql flexible-server parameter show](/cli/azure/mysql/flexible-server/parameter).

En este ejemplo se muestran detalles del parámetro **slow\_query\_log** del servidor **mydemoserver.mysql.database.azure.com** en el grupo de recursos **myresourcegroup**.
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>Modificación del valor de los parámetros del servidor

También puede modificar el valor de un determinado parámetro del servidor; esta acción actualizará el valor de configuración subyacente del motor del servidor MySQL. Para actualizar el parámetro, use el comando [az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter). 

Para actualizar el parámetro **slow\_query\_log** del servidor **mydemoserver.mysql.database.azure.com** en el grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
Si desea restablecer el valor de un parámetro, omita el parámetro opcional `--value` y el servicio aplicará el valor predeterminado. Para el ejemplo anterior, sería:
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
Este código restablece **slow\_query\_log** al valor predeterminado **OFF**. 

## <a name="setting-non-modifiable-server-parameters"></a>Establecimiento de parámetros de servidor no modificables

Si el parámetro de servidor que quiere actualizar no aparece en Azure Portal, también puede establecer el parámetro en el nivel de conexión mediante `init_connect`. De este modo, se establecen los parámetros del servidor para cada cliente con conexión al servidor.

Actualice el parámetro del servidor **init\_connect** del servidor **mydemoserver.mysql.database.azure.com** en el grupo de recursos **myresourcegroup** para establecer valores como el juego de caracteres.
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> `init_connect` se puede usar para cambiar los parámetros que no requieren privilegios elevados en el nivel de sesión. Para comprobar si puede establecer el parámetro mediante `init_connect`, ejecute el comando `set session parameter_name=YOUR_DESIRED_VALUE;` y si se producen errores que indican **acceso denegado y que necesita los privilegios elevados**, no puede establecer el parámetro con "init_connect".

## <a name="working-with-the-time-zone-parameter"></a>Trabajo con el parámetro de zona horaria

### <a name="populating-the-time-zone-tables"></a>Relleno de las tablas de la zona horaria

Las tablas de la zona horaria del servidor se pueden rellenar mediante una llamada al procedimiento almacenado `mysql.az_load_timezone` desde una herramienta como la línea de comandos de MySQL o MySQL Workbench.

> [!NOTE]
> Si ejecuta el comando `mysql.az_load_timezone` de MySQL Workbench, es posible que primero tenga que desactivar el modo de actualización segura mediante `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
>Debe reiniciar el servidor para asegurarse de que las tablas de zona horaria se rellenen correctamente.<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

Para ver los valores de zonas horarias disponibles, ejecute el comando siguiente:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Establecimiento de la zona horaria de nivel global

La zona horaria de nivel global se puede establecer mediante el comando [az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter).

El siguiente comando actualiza el parámetro **time\_zone** del servidor **mydemoserver.mysql.database.azure.com** en el grupo de recursos **myresourcegroup** a **US/Pacific**.

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Establecimiento de la zona horaria de nivel de sesión

La zona horaria de nivel de sesión se puede establecer mediante la ejecución del comando `SET time_zone` desde una herramienta como la línea de comandos de MySQL o MySQL Workbench. En el ejemplo siguiente se establece la zona horaria en **US/Pacific**.  

```sql
SET time_zone = 'US/Pacific';
```

Consulte [Date and Time Functions](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz) (Funciones de fecha y hora) en la documentación de MySQL.


## <a name="next-steps"></a>Pasos siguientes

- Cómo configurar [parámetros del servidor en Azure Portal](./how-to-configure-server-parameters-portal.md)
