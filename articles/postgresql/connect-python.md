---
title: 'Inicio rápido: Conexión con Python: Azure Database for PostgreSQL (servidor único)'
description: En esta guía de inicio rápido se proporcionan ejemplos de código de Python que puede usar para conectarse y consultar datos de Azure Database for PostgreSQL de un servidor único.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: fc02862b6d980854d20e90724d9192e33693b5db
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137131"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Inicio rápido: Uso de Python para conectarse y consultar datos en un servidor único Azure Database for PostgreSQL

En esta guía de inicio rápido, obtendrá información sobre cómo conectarse a la base de datos en Azure Database for PostgreSQL (servidor único) y ejecutar instrucciones SQL para realizar consultas mediante Python en macOS, Ubuntu Linux o Windows.

> [!TIP]
> Si está pensando en compilar una aplicación de Django con PostgreSQL, consulte el tutorial, [Implementación de una aplicación web de Django de Python con PostgreSQL](../app-service/tutorial-python-postgresql-app.md) tutorial.


## <a name="prerequisites"></a>Requisitos previos
Para esta guía de inicio rápido, necesitará lo siguiente:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free).
- Crear un único servidor Azure Database for PostgreSQL mediante [Azure Portal](./quickstart-create-server-database-portal.md) <br/> o la [CLI de Azure](./quickstart-create-server-database-azure-cli.md) si no tiene uno.
- En función de si usa el acceso público o privado, complete **UNA** de las acciones siguientes para habilitar la conectividad.

  |Acción| Método de conectividad|Guía paso a paso|
  |:--------- |:--------- |:--------- |
  | **Configurar reglas de firewall** | Público | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Configurar el punto de conexión de servicio** | Público | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Configuración del vínculo privado** | Private | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7 o 3.6+.

- Instalador de paquetes [pip](https://pip.pypa.io/en/stable/installing/) más reciente.
- Instalar [psycopg2](https://pypi.python.org/pypi/psycopg2-binary/) mediante `pip install psycopg2-binary` en un terminal o una ventana del símbolo del sistema. Para más información, consulte [Instalación`psycopg2`](https://www.psycopg.org/docs/install.html).

## <a name="get-database-connection-information"></a>Obtener información de conexión de base de datos
La conexión a una base de datos Azure Database for PostgreSQL requiere el nombre completo del servidor y las credenciales de inicio de sesión. Puede obtener esta información en el Azure Portal.

1. En el [Azure Portal](https://portal.azure.com/), busque y seleccione el nombre del servidor de Azure Database for PostgreSQL.
1. En la página de **Información general** del servidor, copie el nombre completo del **Servidor** y el **Nombre del usuario administrador**. El **nombre completo del servidor** siempre tiene el formato *\<my-server-name>.postgres.database.azure.com* y el **nombre del usuario administrador** siempre tiene el formato *\<my-admin-username>@\<my-server-name>* .

   También necesitará la contraseña de administrador. Si la olvida, puede restablecerla desde esta página.

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Nombre de servidor de Azure Database for PostgreSQL":::

> [!IMPORTANT]
>  Reemplace los siguientes valores:
>   - `<server-name>` y `<admin-username>` con los valores que copió del Azure Portal.
>   - `<admin-password>` con la contraseña del servidor.
>   - `<database-name>` se ha creado automáticamente una base de datos predeterminada llamada *postgres* al crear el servidor. Puede cambiar el nombre de la base de datos o [crear una nueva](https://www.postgresql.org/docs/current/sql-createdatabase.html) mediante comandos SQL.

## <a name="step-1-connect-and-insert-data"></a>Paso 1: Conexión e inserción de datos
El siguiente ejemplo de código se conecta a la base de datos de Azure Database for PostgreSQL con
-  la función [psycopg2.connect](https://www.psycopg.org/docs/connection.html) y carga los datos con la instrucción SQL **INSERT**.
- la función [cursor.execute](https://www.psycopg.org/docs/cursor.html#execute) ejecuta la consulta SQL en la base de datos.

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string)
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

Cuando el código se ejecuta correctamente, genera el siguiente resultado:

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Salida de la línea de comandos":::


[¿Tiene problemas? Háganoslo saber.](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>Paso 2: Lectura de datos
El siguiente ejemplo de código se conecta a la base de datos de Azure Database for PostgreSQL y usa
- [cursor.execute](https://www.psycopg.org/docs/cursor.html#execute) con la instrucción SQL **SELECT** para leer los datos.
- [cursor.fetchall()](https://www.psycopg.org/docs/cursor.html#cursor.fetchall) acepta una consulta y devuelve un conjunto de resultados para iterar mediante

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[¿Tiene problemas? Háganoslo saber.](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>Paso 3: Actualización de datos
En el ejemplo de código siguiente se usa [cursor.execute](https://www.psycopg.org/docs/cursor.html#execute) con la instrucción SQL **UPDATE** para actualizar los datos.

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[¿Tiene problemas? Háganoslo saber.](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>Paso 5: Eliminación de datos
En el ejemplo de código siguiente se ejecuta [cursor.execute](https://www.psycopg.org/docs/cursor.html#execute) con la instrucción SQL **DELETE** para eliminar un elemento de inventario que se ha insertado previamente.

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[¿Tiene problemas? Háganoslo saber.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Limpieza de recursos

Para limpiar todos los recursos utilizados durante esta guía de inicio rápido, elimine el grupo de recursos con el siguiente comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Administración de un servidor de Azure Database for MySQL mediante Azure Portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Administración con la CLI de Azure de una instancia de Azure Database for MySQL: Servidor único](./how-to-manage-server-cli.md)<br/>

[¿No encuentra lo que busca? Háganoslo saber.](https://aka.ms/postgres-doc-feedback)
