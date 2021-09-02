---
title: Migración de Amazon RDS para MySQL a Azure Database for MySQL mediante MySQL Workbench
description: En este artículo se describe cómo migrar Amazon RDS para MySQL a Azure Database for MySQL mediante el Asistente para migración de MySQL Workbench.
author: HJToland3
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 05/21/2021
ms.openlocfilehash: c744517dfab088fa4edb104b2c5aebda2818ab8f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653596"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-mysql-workbench"></a>Migración de Amazon RDS para MySQL a Azure Database for MySQL mediante MySQL Workbench

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Puede usar varias utilidades, como las de exportación/importación de MySQL Workbench, Azure Database Migration Service (DMS), y volcado y restauración de MySQL, para migrar Amazon RDS para MySQL a Azure Database for MySQL. Sin embargo, el uso del Asistente para migración de MySQL Workbench proporciona una manera fácil y cómoda de mover las bases de datos de Amazon RDS para MySQL a Azure Database for MySQL.

Con el Asistente para migración, puede seleccionar cómodamente los esquemas y objetos que se van a migrar. También permite ver los registros del servidor para identificar errores y cuellos de botella en tiempo real. Como resultado, puede editar y modificar tablas o estructuras y objetos de base de datos durante el proceso de migración cuando se detecta un error y, a continuación, reanudar la migración sin tener que volver a empezar desde cero.

> [!NOTE]
> También puede usar el Asistente para migración para migrar otros orígenes, como Microsoft SQL Server, Oracle, PostgreSQL, MariaDB, etc., que están fuera del ámbito de este artículo.

## <a name="prerequisites"></a>Requisitos previos

Antes de iniciar el proceso de migración, se recomienda asegurarse de que varios parámetros y características están configurados correctamente, como se describe a continuación.

- Asegúrese de que el juego de caracteres de las bases de datos de origen y de destino sea el mismo.
- Establezca el tiempo de espera en un tiempo razonable en función de la cantidad de datos o la carga de trabajo que quiere importar o migrar.
- Establezca el parámetro `max_allowed_packet parameter` en una cantidad razonable en función del tamaño de la base de datos que desea importar o migrar.
- Compruebe que todas las tablas usan InnoDB, ya que Azure Database for MySQL Server solo admite el motor de almacenamiento de InnoDB.
- Quite, reemplace o modifique todos los desencadenadores, procedimientos almacenados y otras funciones que contengan definidores de usuario raíz o superusuario (Azure Database for MySQL no admite el privilegio de superusuario). Para reemplazar los definidores por el nombre del usuario administrador que ejecuta el proceso de importación, ejecute el comando siguiente:

  ```
  DELIMITER; ;/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
  DELIMITER;
  /* Modified to */
  DELIMITER;
  /*!50003 CREATE*//*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
  DELIMITER;

  ```

- Si las funciones definidas por el usuario (UDF) se ejecutan en el servidor de bases de datos, debe eliminar el privilegio de la base de datos MySQL. Para determinar si alguna UDF se ejecuta en el servidor, use la consulta siguiente:

  ```
  SELECT * FROM mysql.func;
  ```

  Si detecta que se están ejecutando UDF, puede quitarlas mediante la consulta siguiente:

  ```
  DROP FUNCTION your_UDFunction;
  ```

- Asegúrese de que el servidor en el que se ejecuta la herramienta y, en última instancia, la ubicación de exportación, tienen suficiente espacio en disco y capacidad de proceso (núcleos virtuales, CPU y memoria) para realizar la operación de exportación, especialmente al exportar una base de datos muy grande.
- Cree una ruta de acceso entre la instancia local o de AWS y Azure Database for MySQL si la carga de trabajo está detrás de firewalls u otras capas de seguridad de red.

## <a name="begin-the-migration-process"></a>Inicio del proceso de migración

1. Para iniciar el proceso de migración, inicie sesión en MySQL Workbench y, a continuación, seleccione el icono de inicio.
2. En la barra de navegación izquierda, seleccione el icono del Asistente para migración, como se muestra en la captura de pantalla siguiente.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/begin-the-migration.png" alt-text="Pantalla de inicio de MySQL Workbench":::

   Se abre la página **Información general** del Asistente para migración, como se muestra a continuación.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/migration-wizard-welcome.png" alt-text="Página de bienvenida del Asistente para migración de MySQL Workbench":::

3. Para determinar si tiene instalado un controlador ODBC para MySQL Server, seleccione **Open ODBC Administrator** (Abrir administrador ODBC).

   En nuestro caso, en la pestaña **Controladores**, observará que ya hay dos controladores ODBC de MySQL Server instalados.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/obdc-administrator-page.png" alt-text="Página Administrador de orígenes de datos OBDC":::

   Si no tiene instalado un controlador ODBC de MySQL, use el instalador de MySQL que utilizó para instalar MySQL Workbench para instalar el controlador. Para obtener más información sobre la instalación del controlador ODBC de MySQL, consulte los siguientes recursos:

   - [MySQL - MySQL Connector/ODBC Developer Guide - 4.1 Installing Connector/ODBC on Windows](https://dev.mysql.com/doc/connector-odbc/en/connector-odbc-installation-binary-windows.html)
   - [ODBC Driver for MySQL: How to Install and Set up Connection (Step-by-step) [Controlador ODBC para MySQL: Cómo instalar y configurar la conexión (paso a paso)], {coding}Sight (codingsight.com)](https://codingsight.com/install-and-configure-odbc-drivers-for-mysql/)

4. Cierre el cuadro de diálogo **Administrador de orígenes de datos ODBC** y continúe con el proceso de migración.

## <a name="configure-source-database-server-connection-parameters"></a>Configuración de parámetros de conexión del servidor de bases de datos de origen

1. En la página **Información general**, seleccione **Iniciar migración**.

   Aparece la página **Selección de origen**. Use esta página para proporcionar información sobre el RDBMS desde el que realiza la migración y los parámetros para la conexión.

2. En el campo **Sistema de base de datos**, seleccione **MySQL**.
3. En el campo **Conexión almacenada**, seleccione una de las opciones de conexión guardadas para ese RDBMS.

   Para guardar las conexiones, puede marcar la casilla de la parte inferior de la página y proporcionar un nombre para la preferencia.

4. En el campo **Método de conexión**, seleccione **Standard TCP/IP** (TCP/IP estándar).
5. En el campo **Nombre de host**, especifique el nombre del servidor de bases de datos de origen.
6. En el campo **Puerto**, especifique **3306** y, a continuación, escriba el nombre de usuario y la contraseña para conectarse al servidor.
7. En el campo **Base de datos**, escriba el nombre de la base de datos que desea migrar, si lo conoce. De lo contrario, déjelo en blanco.
8. Seleccione **Probar conexión** para comprobar la conexión a la instancia de MySQL Server.

   Si ha especificado los parámetros correctos, aparece un mensaje que indica un intento de conexión correcto.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/source-connection-parameters.png" alt-text="Página de parámetros de conexión de la base de datos de origen":::

9. Seleccione **Next** (Siguiente).

## <a name="configure-target-database-server-connection-parameters"></a>Configuración de los parámetros de conexión del servidor de bases de datos de destino

1. En la página **Selección de destino**, establezca los parámetros para conectarse a la instancia de MySQL Server de destino mediante un proceso similar al de configuración de la conexión al servidor de origen.
2. Para comprobar que la conexión se ha realizado correctamente, seleccione **Probar conexión**.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/target-connection-parameters.png" alt-text="Página de parámetros de conexión de base de datos de destino":::

3. Seleccione **Next** (Siguiente).

## <a name="select-the-schemas-to-migrate"></a>Selección de los esquemas que se van a migrar

El Asistente para migración se comunicará con la instancia de MySQL Server y capturará una lista de esquemas del servidor de origen.

1. Seleccione **Mostrar registros** para ver esta operación.

   En la captura de pantalla siguiente se muestra cómo se recuperan los esquemas del servidor de bases de datos de origen.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/retrieve-schemas.png" alt-text="Página de captura de la lista de esquemas":::

2. Seleccione **Siguiente** para comprobar que todos los esquemas se capturaron correctamente.

   En la captura de pantalla siguiente se muestra la lista de los esquemas capturados.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/schemas-selection.png" alt-text="Página de selección de esquemas":::

   Solo puede migrar los esquemas que aparecen en esta lista.

3. Seleccione los esquemas que quiere migrar y, a continuación, seleccione **Siguiente**.

## <a name="object-migration"></a>Migración de objeto

A continuación, especifique los objetos que desea migrar.

1. Elija **Mostrar selección** y, a continuación, en **Objetos disponibles**, seleccione y agregue los objetos que desea migrar.

   Cuando haya agregado los objetos, aparecerán en **Objetos que se van a migrar**, como se muestra en la captura de pantalla siguiente.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/source-objects.png" alt-text="Página de selección de objetos de origen":::

   En este escenario, hemos seleccionado todos los objetos de la tabla.

2. Seleccione **Next** (Siguiente).

## <a name="edit-data"></a>Edición de datos

En esta sección, tiene la opción de editar los objetos que desea migrar.

1. En la página **Manual Editing** (Edición manual), observe el menú desplegable **Ver** de la esquina superior derecha.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/manual-editing.png" alt-text="Página de selección de Edición manual":::

   El cuadro desplegable **Ver** incluye tres elementos:

   - **Todos los objetos**: muestra todos los objetos. Con esta opción, puede editar manualmente el código SQL generado antes de aplicarlo al servidor de bases de datos de destino. Para ello, seleccione el objeto y elija Show Code and Messages (Mostrar código y mensajes). Puede ver (y editar) el código MySQL generado que corresponde al objeto seleccionado.
   - **Migration problems** (Problemas de migración): muestra los problemas que se produjeron durante la migración, que puede revisar y comprobar.
   - **Asignación de columnas**: muestra información de asignación de columnas. Puede usar esta vista para editar el nombre y cambiar la columna del objeto de destino.

2. Seleccione **Next** (Siguiente).

## <a name="create-the-target-database"></a>Creación de la base de datos de destino

1. Active la casilla **Create schema in target RDBMS** (Crear esquema en RDBMS de destino).

   También puede optar por mantener los esquemas ya existentes para que no se modifiquen ni actualicen.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-target-database.png" alt-text="Página de opciones de creación de destino":::

   En este artículo, hemos elegido crear el esquema en el RDBMS de destino, pero también puede activar la casilla **Create a SQL script file** (Crear un archivo de script SQL) para guardar el archivo en el equipo local o para otros fines.

2. Seleccione **Next** (Siguiente).

## <a name="run-the-mysql-script-to-create-the-database-objects"></a>Ejecución del script MySQL para crear los objetos de base de datos

Puesto que hemos elegido crear el esquema en el RDBMS de destino, el script SQL migrado se ejecutará en el servidor MySQL de destino. Puede ver su progreso como se muestra en la captura de pantalla siguiente:

:::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-schemas.png" alt-text="Página de creación de esquemas":::

1. Una vez completada la creación de los esquemas y sus objetos, seleccione **Siguiente**.

   En la página **Create Target Results** (Resultados de creación de destino), se muestra una lista de los objetos creados y la notificación de los errores que se encontraron al crearlos, como se muestra en la captura de pantalla siguiente.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-target-results.png" alt-text="Página de resultados de creación de destino":::

2. Revise los detalles de esta página para comprobar que todo se ha completado según lo previsto.

   En el ejemplo de este artículo, no tenemos ningún error. Si no es necesario solucionar ningún mensaje de error, puede editar el script de migración.

3. En el cuadro **Objeto**, seleccione el objeto que desea editar.
4. En **SQL CREATE script for selected object** (Script SQL CREATE para el objeto seleccionado), modifique el script SQL y, a continuación, seleccione **Aplicar** para guardar los cambios.
5. Seleccione **Recreate Objects** (Volver a crear objetos) para ejecutar el script, incluidos los cambios.

   Si se produce un error en el script, es posible que tenga que editar el script generado. A continuación, puede corregir manualmente el script SQL y volver a ejecutarlo todo. En este artículo, no cambiamos nada y dejamos el script tal como está.

6. Seleccione **Next** (Siguiente).

## <a name="transfer-data"></a>Transferencia de datos

Esta parte del proceso mueve los datos de la instancia de base de datos de MySQL Server de origen a la instancia de base de datos de MySQL de destino recién creada. Use la página **Data Transfer Setup** (Configuración de la transferencia de datos) para configurar este proceso.

:::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/data-transfer-setup.png" alt-text="Página de configuración de la transferencia de datos":::

Esta página proporciona opciones para configurar la transferencia de datos. Para los fines de este artículo, aceptaremos los valores predeterminados.

1. Para comenzar el proceso real de transferencia de datos, seleccione **Siguiente**.

   El progreso del proceso de transferencia de datos aparece como se muestra en la captura de pantalla siguiente.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/bulk-data-transfer.png" alt-text="Página Transferencia de datos masiva":::

   > [!NOTE]
   > La duración del proceso de transferencia de datos está directamente relacionada con el tamaño de la base de datos que se va a migrar. Cuanto mayor sea la base de datos de origen, más tiempo llevará el proceso, posiblemente hasta unas horas para bases de datos más grandes.

2. Una vez completada la transferencia, seleccione **Siguiente**.

   Aparece la página **Informe de migración**, que proporciona un informe que resume todo el proceso, como se muestra en la captura de pantalla siguiente:

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/migration-report.png" alt-text="Página del informe de progreso de la migración":::

3. Seleccione **Finalizar** para cerrar el Asistente para migración.

   La migración se ha completado correctamente.

## <a name="verify-consistency-of-the-migrated-schemas-and-tables"></a>Comprobación de la coherencia de las tablas y los esquemas migrados

1. A continuación, inicie sesión en la instancia de base de datos de destino de MySQL para comprobar que las tablas y los esquemas migrados son coherentes con la base de datos de origen de MySQL.

   En nuestro caso, puede ver que todos los esquemas (sakila, moda, items, customer, clothes, world y world_x) de la base de datos Amazon RDS for MySQL: **MyjolieDB** se han migrado correctamente a la instancia Azure Database for MySQL: **azmysql**.

2. Para comprobar los recuentos de tablas y filas, ejecute la siguiente consulta en ambas instancias:

      `SELECT COUNT (*) FROM sakila.actor;`

   En la captura de pantalla siguiente puede ver que el recuento de filas de MySQL de Amazon RDS es 200, que coincide con la instancia de Azure Database for MySQL.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/table-row-size-source.png" alt-text="Base de datos de origen de tamaños de tabla y fila":::

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/table-row-size-target.png" alt-text="Base de datos de destino de tamaños de tabla y fila":::

   Aunque puede ejecutar la consulta anterior en cada tabla y esquema únicos, supondrá mucho trabajo si gestiona cientos de miles o incluso millones de tablas. Puede usar las consultas siguientes para comprobar el tamaño del esquema (base de datos) y la tabla en su lugar.

3. Para comprobar el tamaño de la base de datos, ejecute la consulta siguiente:

    ```
   SELECT table_schema AS "Database", 
   ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS "Size (MB)" 
   FROM information_schema.TABLES 
   GROUP BY table_schema;
   ```

4. Para comprobar el tamaño de la tabla, ejecute la siguiente consulta:

   ```
   SELECT table_name AS "Table",
   ROUND(((data_length + index_length) / 1024 / 1024), 2) AS "Size (MB)"
   FROM information_schema.TABLES
   WHERE table_schema = "database_name"
   ORDER BY (data_length + index_length) DESC;
   ```

   En las capturas de pantalla siguientes verá que el tamaño del esquema (base de datos) de la instancia MySQL de Amazon RDS de origen es el mismo que el de la instancia de Azure Database for MySQL de destino.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/database-size-source.png" alt-text="Base de datos de origen de tamaños de base de datos":::

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/database-size-target.png" alt-text="Base de datos de destino de tamaños de base de datos":::

   Puesto que los tamaños de esquema (base de datos) son los mismos en ambas instancias, no es realmente necesario comprobar los tamaños de tabla individuales. En cualquier caso, siempre puede usar la consulta anterior para comprobar los tamaños de tabla, según sea necesario.

   Ha comprobado que la migración se completó correctamente.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo migrar bases de datos a Azure Database for MySQL, consulte la [Guía de migración de base de datos](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Vea el vídeo sobre [migración fácil de aplicaciones de MySQL/PostgreSQL a Azure Managed Service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), que contiene una demostración que muestra cómo migrar aplicaciones de MySQL a Azure Database for MySQL.
