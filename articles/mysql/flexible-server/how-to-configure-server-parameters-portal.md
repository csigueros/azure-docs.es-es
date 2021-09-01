---
title: 'Configuración de parámetros de servidor: Azure Database for MySQL: servidor flexible en Azure Portal'
description: 'En este artículo se describe cómo configurar los parámetros de servidor MySQL en Azure Database for MySQL: servidor flexible mediante Azure Portal.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 2d02a4e0e63bd0861c0a414f3d56e9c62e19dc8a
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652361"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Configuración de parámetros de servidor en Azure Database for MySQL: servidor flexible mediante Azure Portal

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Actualmente, Azure Database for MySQL: servidor flexible se encuentra en versión preliminar pública.

Puede administrar la configuración de Azure Database for MySQL: servidor flexible con los parámetros de servidor. Los parámetros de servidor se configuran con los valores predeterminados y recomendados al crear el servidor.  

En este artículo se describe cómo ver y configurar estos parámetros mediante Azure Portal. La hoja de parámetros de servidor de Azure Portal muestra el parámetro de servidor modificable y no modificable. Los parámetros de servidor no modificables están atenuados.

>[!Note]
> Estos parámetros se pueden actualizar globalmente en el nivel de servidor con la [CLI de Azure](./how-to-configure-server-parameters-cli.md) o [Azure Portal](./how-to-configure-server-parameters-portal.md).

## <a name="configure-server-parameters"></a>Configuración de parámetros del servidor

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y luego busque su instancia de Azure Database for MySQL: servidor flexible.
2. En la sección **CONFIGURACIÓN**, haga clic en **Parámetros del servidor** para abrir la página de parámetros de servidor de Azure Database for MySQL: servidor flexible.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Página Parámetros del servidor de Azure Portal":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. Busque cualquier parámetro de servidor que tenga que ajustar. Revise la columna **Descripción** para entender el propósito y los valores permitidos.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Menú desplegable de enumeración":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. Haga clic en **Guardar** para guardar los cambios.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Guardar o descartar cambios":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. Los parámetros estáticos son los que requieren que se reinicie el servidor para poder aplicarse. Si va a modificar el parámetro estático, se le pedirá que **reinicie ahora** o **reinicie más tarde**.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Reiniciar al guardar el parámetro estático":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. Si ha guardado los nuevos valores para los parámetros, siempre puede revertir todos los elementos a los valores predeterminados; para ello, seleccione **Restablecer todos los valores predeterminados**.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Restablecer todos los valores predeterminados":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>Establecimiento de parámetros de servidor no modificables

Si el parámetro de servidor que quiere actualizar no aparece en Azure Portal, también puede establecer el parámetro en el nivel de conexión mediante `init_connect`. De este modo se establecen los parámetros del servidor para cada cliente con conexión al servidor. 

1. En la sección **CONFIGURACIÓN**, haga clic en **Parámetros del servidor** para abrir la página Parámetros del servidor de Azure Database for MySQL.
2. Busque `init_connect`.
3. Agregue los parámetros del servidor con el formato: `SET parameter_name=YOUR_DESIRED_VALUE` en valor la columna de valor.

    Por ejemplo, puede cambiar el conjunto de caracteres del servidor estableciendo `init_connect` en `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`.
4. Haga clic en **Guardar** para guardar los cambios.

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
>Debe reiniciar el servidor para asegurarse de que las tablas de zona horaria se rellenen correctamente.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

Para ver los valores de zonas horarias disponibles, ejecute el comando siguiente:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Establecimiento de la zona horaria de nivel global

La zona horaria de nivel global se puede establecer desde la página **Parámetros del servidor** en Azure Portal. En el ejemplo siguiente se establece la zona horaria global en el valor "US/Pacific".

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="Establecimiento del parámetro de zona horaria":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>Establecimiento de la zona horaria de nivel de sesión

La zona horaria de nivel de sesión se puede establecer mediante la ejecución del comando `SET time_zone` desde una herramienta como la línea de comandos de MySQL o MySQL Workbench. En el ejemplo siguiente se establece la zona horaria en **US/Pacific**.

```sql
SET time_zone = 'US/Pacific';
```

Consulte [Date and Time Functions](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz) (Funciones de fecha y hora) en la documentación de MySQL.

## <a name="next-steps"></a>Pasos siguientes

- Cómo configurar [parámetros de servidor en la CLI de Azure](./how-to-configure-server-parameters-cli.md)
