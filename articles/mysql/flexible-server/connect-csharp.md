---
title: 'Inicio rápido: Conexión mediante C#: Servidor flexible de Azure Database for MySQL'
description: En este inicio rápido se proporciona un ejemplo de código de C# (.NET) que puede usar para conectarse a Servidor flexible de Azure Database for MySQL y realizar consultas de datos.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 01/16/2021
ms.openlocfilehash: dd4da95bd3352897d62f36265544a10dbd37f825
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122643590"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Inicio rápido: Uso de .NET (C#) para conectarse y consultar datos en Servidor flexible de Azure Database for MySQL

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

En este tutorial rápido se muestra cómo conectarse a una instancia de Azure Database for MySQL mediante una aplicación de C#. Se indica cómo usar instrucciones SQL para consultar, insertar, actualizar y eliminar datos en la base de datos.

## <a name="prerequisites"></a>Requisitos previos

Para esta guía de inicio rápido, necesitará lo siguiente:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free).
- Cree un Servidor flexible de Azure Database for MySQL mediante [Azure Portal](./quickstart-create-server-portal.md) <br/> o la [CLI de Azure](./quickstart-create-server-cli.md) si no tiene uno.
- En función de si usa el acceso público o privado, complete **UNA** de las acciones siguientes para habilitar la conectividad.
- [Creación de una base de datos y un usuario que no sea administrador](../howto-create-users.md)

[¿Tiene problemas? Háganoslo saber.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="create-a-c-project"></a>Cree un proyecto de C#
En un símbolo del sistema, ejecute:

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>Obtención de información sobre la conexión
Obtenga la información de conexión necesaria para conectarse a Azure Database for MySQL. Necesitará el nombre completo del servidor y las credenciales de inicio de sesión.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el menú izquierdo de Azure Portal, seleccione **Todos los recursos** y, luego, busque el servidor que ha creado, por ejemplo, **mydemoserver**.
3. Seleccione el nombre del servidor.
4. En el panel **Información general** del servidor, anote el **nombre del servidor** y el **nombre de inicio de sesión del administrador del servidor**. Si olvida la contraseña, puede restablecerla en este panel.
 :::image type="content" source="./media/connect-csharp/server-overview-name-login.png" alt-text="Nombre del servidor de Azure Database for MySQL":::

## <a name="step-1-connect-and-insert-data"></a>Paso 1: Conexión e inserción de datos
Use el código siguiente para conectarse y leer los datos mediante las instrucciones SQL `CREATE TABLE` y `INSERT INTO`. El código usa los métodos de la clase `MySqlConnection`:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) para establecer una conexión con MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) para establecer la propiedad CommandText.
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) para ejecutar los comandos de base de datos.

Reemplace los parámetros `Server`, `Database`, `UserID` y `Password` por los valores especificados al crear el servidor y la base de datos.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[¿Tiene algún problema? Háganoslo saber.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-2-read-data"></a>Paso 2: Lectura de datos

Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL `SELECT`. El código usa la clase `MySqlConnection` con métodos:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) para establecer una conexión con MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) para establecer la propiedad CommandText.
- [ExecuteReaderAsync()](/dotnet/api/system.data.common.dbcommand.executereaderasync) para ejecutar los comandos de base de datos.
- [ReadAsync()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync) para avanzar a los registros de los resultados. A continuación, el código usa GetInt32() y GetString() para analizar los valores del registro.


Reemplace los parámetros `Server`, `Database`, `UserID` y `Password` por los valores especificados al crear el servidor y la base de datos.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[¿Tiene problemas? Háganoslo saber.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-3-update-data"></a>Paso 3: Actualización de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL `UPDATE`. El código usa la clase `MySqlConnection` con el método:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) para establecer una conexión con MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) para establecer la propiedad CommandText.
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) para ejecutar los comandos de base de datos.


Reemplace los parámetros `Server`, `Database`, `UserID` y `Password` por los valores especificados al crear el servidor y la base de datos.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```


## <a name="step-4-delete-data"></a>Paso 4: Eliminación de datos
Use el código siguiente para conectarse y eliminar los datos mediante la instrucción SQL `DELETE`.

El código usa la clase `MySqlConnection` con el método
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) para establecer una conexión con MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) para establecer la propiedad CommandText.
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) para ejecutar los comandos de base de datos.


Reemplace los parámetros `Server`, `Database`, `UserID` y `Password` por los valores especificados al crear el servidor y la base de datos.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Para limpiar todos los recursos utilizados durante esta guía de inicio rápido, elimine el grupo de recursos con el siguiente comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Administración de un servidor de Azure Database for MySQL mediante Azure Portal](./how-to-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Administración con la CLI de Azure de una instancia de Azure Database for MySQL: Servidor único](./how-to-manage-server-cli.md)


