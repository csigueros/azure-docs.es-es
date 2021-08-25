---
title: Consultar una instancia de SQL Database mediante el editor de consultas en Azure Portal (versión preliminar)
description: Aprenda a usar el editor de consultas para ejecutar consultas Transact-SQL (T-SQL) en una base de datos de Azure SQL Database.
titleSuffix: Azure SQL Database
keywords: conectarse a sql database, consultar sql database, azure portal, portal, editor de consultas
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, contperf-fy21q3-portal
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: mathoma
ms.date: 03/01/2021
ms.openlocfilehash: 88ff2a523bb17af28dce0a45ef73af7da5b9d9e4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739714"
---
# <a name="quickstart-use-the-azure-portals-query-editor-preview-to-query-an-azure-sql-database"></a>Inicio rápido: Uso del editor de consultas de Azure Portal (versión preliminar) para consultar una base de datos de Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

El editor de consultas es una herramienta de Azure Portal para ejecutar consultas SQL en una base de datos de Azure SQL Database o en un almacenamiento de datos de Azure Synapse Analytics.

En este inicio rápido, usará el editor de consultas para ejecutar consultas Transact-SQL (T-SQL) en una base de datos.

## <a name="prerequisites"></a>Requisitos previos

### <a name="create-a-database-with-sample-data"></a>Crear una base de datos con datos de ejemplo

Para completar este inicio rápido se necesita la base de datos de ejemplo AdventureWorksLT. Si no tiene una copia de trabajo de la base de datos de ejemplo AdventureWorksLT de Azure SQL Database, en el siguiente inicio rápido obtendrá ayuda para crear una rápidamente:

[Inicio rápido: Creación de una base de datos de Azure SQL Database mediante Azure Portal, PowerShell o la CLI de Azure](single-database-create-quickstart.md)

### <a name="set-an-azure-active-directory-admin-for-the-server-optional"></a>Establecimiento de un administrador de Azure Active Directory para el servidor (opcional)

La configuración de un administrador de Azure Active Directory (Azure AD) le permite usar una identidad única para iniciar sesión en Azure Portal y en la base de datos. Si quiere usar Azure AD para conectar con el editor de consultas, siga los pasos que se indican a continuación.

Este proceso es opcional; en su lugar puede usar la autenticación de SQL para conectar con el editor de consultas.

> [!NOTE]
> * Las cuentas de correo electrónico (por ejemplo, outlook.com, gmail.com, yahoo.com, etc.) aún no se admiten como administradores de Azure AD. Asegúrese de elegir un usuario creado de forma nativa en Azure AD o que esté federado en Azure AD.
> * El inicio de sesión de administrador de Azure AD funciona con cuentas con la autenticación de dos factores habilitada, pero el editor de consultas no la admite.

1. En Azure Portal, vaya al servidor de bases de datos SQL.

2. En el menú **SQL Server**, seleccione **Administrador de Active Directory**.

3. En la barra de herramientas de la página **Administrador de Active Directory** de SQL Server, seleccione **Establecer administrador**.

    ![seleccione active directory](./media/connect-query-portal/select-active-directory.png)

4. En la página **Agregar administrador**, en el cuadro de búsqueda, escriba el usuario o grupo que desea buscar, selecciónelo como administrador y, a continuación, elija el botón **Seleccionar**.

5. De nuevo en la barra de herramientas de la página **Administrador de Active Directory** de SQL Server, seleccione **Guardar**.

## <a name="using-sql-query-editor"></a>Uso del editor de consultas de SQL

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y seleccione la base de datos que desea consultar.

2. En el menú de **Base de datos SQL**, seleccione **Editor de consultas (versión preliminar)** .

    ![buscar editor de consultas](./media/connect-query-portal/find-query-editor.PNG)

### <a name="establish-a-connection-to-the-database"></a>Establecimiento de una conexión con la base de datos

Aunque haya iniciado sesión en el portal, tiene que proporcionar las credenciales para acceder a la base de datos. Puede realizar la conexión mediante la autenticación de SQL o Azure Active Directory para conectarse a la base de datos.

#### <a name="connect-using-sql-authentication"></a>Conexión con la autenticación de SQL

1. En la página **Inicio de sesión**, en **Autenticación de SQL Server**, escriba un **Inicio de sesión** y **Contraseña** para un usuario que tenga acceso a la base de datos. Si no está seguro, use el inicio de sesión y la contraseña para el administrador de servidor del servidor de la base de datos.

    ![iniciar sesión](./media/connect-query-portal/login-menu.png)

2. Seleccione **Aceptar**.

#### <a name="connect-using-azure-active-directory"></a>Clientes que usan Azure Active Directory

En el **Editor de consultas (versión preliminar)** , vea la página **Inicio de sesión** en la sección **Autenticación de Active Directory**. La autenticación se realiza automáticamente, por lo que si es administrador de Azure AD de la base de datos, aparece un mensaje que indica que ha iniciado sesión. Seleccione el botón **Continuar como** *\<your user or group ID>* . Si la página indica que no ha iniciado sesión correctamente, puede que tenga que actualizarla.

### <a name="query-a-database-in-sql-database"></a>Consulta de una base de datos en SQL Database

Las consultas de ejemplo siguientes se deben ejecutar correctamente en la base de datos de ejemplo AdventureWorksLT.

#### <a name="run-a-select-query"></a>Ejecución de una consulta SELECT

1. Pegue la siguiente consulta en el editor de consultas:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Seleccione **Ejecutar** y, a continuación, revise los resultados en el panel **Resultados**.

   ![resultados del editor de consultas](./media/connect-query-portal/query-editor-results.png)

3. Tiene la opción de guardar la consulta como un archivo .sql o exportar los datos devueltos como un archivo .json, .csv o .xml.

#### <a name="run-an-insert-query"></a>Ejecución de una consulta INSERT

Ejecute la siguiente instrucción T-SQL [INSERT](/sql/t-sql/statements/insert-transact-sql/) para agregar un nuevo producto en la tabla `SalesLT.Product`.

1. Reemplace la consulta anterior por esta otra.

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Seleccione **Ejecutar** para insertar una nueva fila en la tabla `Product`. El panel **Mensajes** muestra **Consulta realizada correctamente: Filas afectadas: 1**.


#### <a name="run-an-update-query"></a>Ejecución de una consulta UPDATE

Ejecute la siguiente instrucción T-SQL [UPDATE](/sql/t-sql/queries/update-transact-sql/) para modificar el nuevo producto.

1. Reemplace la consulta anterior por esta otra.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Seleccione **Ejecutar** para actualizar la fila especificada en la tabla `Product`. El panel **Mensajes** muestra **Consulta realizada correctamente: Filas afectadas: 1**.

#### <a name="run-a-delete-query"></a>Ejecución de una consulta DELETE

Ejecute la siguiente instrucción T-SQL [DELETE](/sql/t-sql/statements/delete-transact-sql/) para quitar el nuevo producto.

1. Reemplace la consulta anterior por esta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Seleccione **Ejecutar** para eliminar la fila especificada en la tabla `Product`. El panel **Mensajes** muestra **Consulta realizada correctamente: Filas afectadas: 1**.


## <a name="troubleshooting-and-considerations"></a>Solución de problemas y consideraciones

Hay algunos aspectos que debe conocer al trabajar con el Editor de consultas.

### <a name="configure-local-network-settings"></a>Configuración de la red local

Si obtiene uno de los siguientes errores en el editor de consultas:
 - *La configuración de la red local puede estar impidiendo que el editor de consultas emita consultas. Haga clic aquí para obtener instrucciones sobre cómo establecer la configuración de la red*
 - *No se ha podido establecer una conexión con el servidor. Esto puede indicar un problema con la configuración del firewall local o el proxy de red*

Esto se debe a que el editor de consultas usa los puertos 443 y 1443 para comunicarse. Tiene que asegurarse de que ha habilitado el tráfico HTTPS saliente en estos puertos. Las instrucciones siguientes le indican cómo hacerlo, en función del sistema operativo. Es posible que tenga que trabajar con el equipo de TI de la empresa para la concesión de aprobación para abrir esta conexión en la red local.

#### <a name="steps-for-windows"></a>Pasos para Windows

1. Abra **Firewall de Windows Defender**.
2. En el menú del lado izquierdo, seleccione **Configuración avanzada**.
3. En **Firewall de Windows Defender con seguridad avanzada**, seleccione **Reglas de salida** en el menú de la izquierda.
4. Seleccione **Nueva regla...** en el menú de la derecha.

En el **Asistente para nueva regla de salida**, siga estos pasos:

1. Seleccione **Puerto** como tipo de regla que quiere crear. Seleccione **Siguiente**.
2. Seleccione **TCP**.
3. Seleccione **Puertos remotos específicos** y escriba "443, 1443". Luego seleccione **Siguiente**.
4. Seleccione "Permitir la conexión si es segura".
5. Seleccione **Siguiente** y luego otra vez **Siguiente**.
5. Mantenga seleccionados "Dominio", "Privado" y "Público".
6. Asigne un nombre a la regla, por ejemplo, "Acceso al editor de consultas de Azure SQL" y, opcionalmente, una descripción. Luego seleccione **Finalizar**.

#### <a name="steps-for-mac"></a>Pasos para Mac
1. Abra **Preferencias del sistema** (menú Apple > Preferencias del sistema).
2. Haga clic en **Seguridad y privacidad**.
3. Haga clic en **Firewall**.
4. Si Firewall está desactivado, seleccione **Haga clic en el candado para realizar cambios** en la parte inferior y seleccione **Activar Firewall**.
4. Haga clic en **Opciones de Firewall**.
5. En la ventana **Seguridad y privacidad**, seleccione esta opción: "Permitir automáticamente que el software firmado reciba conexiones entrantes".

#### <a name="steps-for-linux"></a>Pasos para Linux
Ejecute estos comandos para actualizar iptables
  ```
  sudo iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT
  sudo iptables -A OUTPUT -p tcp --dport 1443 -j ACCEPT
  ```

### <a name="connection-considerations"></a>Consideraciones sobre la conexión

* En el caso de las conexiones públicas al editor de consultas, tiene que [agregar la dirección IP saliente a las reglas de firewall permitidas del servidor](firewall-create-server-level-portal-quickstart.md) para acceder a las bases de datos y los almacenamientos de datos.

* Si tiene una conexión de Private Link configurada en el servidor y se está conectando al editor de consultas desde una dirección IP de la red virtual privada, el editor de consultas funciona sin necesidad de agregar la dirección IP del cliente a las reglas de firewall del servidor de la base de datos SQL.

* Los permisos RBAC más básicos necesarios para usar el editor de consultas son el acceso de lectura al servidor y la base de datos. Cualquier persona con este nivel de acceso puede acceder a las características del editor de consultas. Para limitar el acceso a usuarios concretos, debe evitar que puedan iniciar sesión en el editor de consultas con credenciales de Azure Active Directory o autenticación de SQL. Si no pueden asignarse a sí mismos como administradores de AAD del servidor ni acceder o agregar una cuenta de administrador de SQL, no deberían poder usar el editor de consultas.

* El editor de consultas no puede conectarse a una base de datos `master`.

* El editor de consultas no puede conectarse a una base de datos de réplica con `ApplicationIntent=ReadOnly`.

* Si ve este mensaje de error "El encabezado X-CSRF-Signature no se pudo validar", realice la acción siguiente para resolver el problema:

    * Asegúrese de que el reloj del equipo está establecido en la hora y la zona horaria adecuadas. También puede intentar hacer coincidir la zona horaria del equipo con Azure si busca la zona horaria de la ubicación de la instancia, como Este de EE. UU., Pacífico, etc.
    * Si se encuentra en una red proxy, asegúrese de que el encabezado de solicitud "X-CSRF-Signature" no se esté modificando ni quitando.

### <a name="other-considerations"></a>Otras consideraciones

* Al presionar la tecla **F5**, se actualiza la página del editor de consultas y se perderá cualquier consulta en la que se esté trabajando.

* La ejecución de consultas tienen un tiempo de expiración de 5 minutos.

* El Editor de consultas solo admite la proyección cilíndrica de tipos de datos geográficos.

* No hay compatibilidad con IntelliSense para las tablas y vistas de la base de datos, pero el editor admite autocompletar en los nombres que ya se han escrito.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las instrucciones Transact-SQL (T-SQL) que se admiten en Azure SQL Database, consulte [Resolución de diferencias de Transact-SQL durante la migración a SQL Database](transact-sql-tsql-differences-sql-server.md).
