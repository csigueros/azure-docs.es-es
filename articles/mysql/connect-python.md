---
title: 'Inicio rápido: Conexión mediante Python: Azure Database for MySQL'
description: En este tutorial rápido se proporcionan varios ejemplos de código de Python que se pueden usar para conectarse a Azure Database for MySQL y consultar datos en este servicio.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: f1e54096e2008997bd86cc9cc1bda5363fa41744
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122643626"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Inicio rápido: Uso de Python para conectarse y consultar datos en Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

En este inicio rápido, va a conectar a una instancia de Azure Database for MySQL mediante Python. Puede usar instrucciones SQL para consultar, insertar, actualizar y eliminar datos de la base de datos en las plataformas Mac, Ubuntu Linux y Windows. 

## <a name="prerequisites"></a>Requisitos previos
Para esta guía de inicio rápido, necesitará lo siguiente:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free).
- [Inicio rápido: Creación de un servidor único de Azure Database for MySQL mediante Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md). <br/> o la [CLI de Azure](./quickstart-create-mysql-server-database-using-azure-cli.md) si no tiene uno.
- En función de si usa el acceso público o privado, complete **UNA** de las acciones siguientes para habilitar la conectividad.

   |Acción| Método de conectividad|Guía paso a paso|
   |:--------- |:--------- |:--------- |
   | **Configurar reglas de firewall** | Público | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
   | **Configurar el punto de conexión de servicio** | Público | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
   | **Configuración del vínculo privado** | Private | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [Creación de una base de datos y un usuario que no sea administrador](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>Instalación de Python y el conector de MySQL

Instale Python y el conector de MySQL para Python en su propio equipo de la forma siguiente: 

> [!NOTE]
> Este inicio rápido usa la [guía del desarrollador de Python o conector de MySQL](https://dev.mysql.com/doc/connector-python/en/).

1. Descargue e instale [Python 3.7 o superior](https://www.python.org/downloads/) para su sistema operativo. Asegúrese de agregar Python a `PATH`, porque el conector de MySQL lo requiere.
   
2. Abra un símbolo del sistema o un shell de `bash` y compruebe la versión de Python mediante la ejecución de `python -V` con el modificador V mayúscula.
   
3. El instalador de paquetes de `pip` se incluye en las versiones más recientes de Python. Actualice `pip` a la versión más reciente mediante la ejecución de `pip install -U pip`. 
   
   Si `pip` no está instalado, puede descargarlo e instalarlo con `get-pip.py`. Para más información, consulte [Instalación](https://pip.pypa.io/en/stable/installing/). 
   
4. Utilice `pip` para instalar el conector de MySQL para Python y sus dependencias:
   
   ```bash
   pip install mysql-connector-python
   ```
   
[¿Tiene problemas? Háganoslo saber.](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>Obtención de información sobre la conexión

Obtenga la información de conexión necesaria para conectarse a Azure Database for MySQL en Azure Portal. Necesitará el nombre del servidor, el nombre de la base de datos y las credenciales de inicio de sesión.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
   
1. En la barra de búsqueda del portal, busque y seleccione el servidor de Azure Database for MySQL que ha creado, como **mydemoserver**.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Nombre del servidor de Azure Database for MySQL":::
   
1. En la página **Información general** del servidor, anote el **nombre del servidor** y el **nombre de inicio de sesión del administrador del servidor**. Si olvida la contraseña, puede restablecerla también en esta página.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Nombre del servidor de Azure Database for MySQL 2":::

## <a name="step-1-create-a-table-and-insert-data"></a>Paso 1: Crear una tabla e insertar datos

Use el código siguiente para conectarse al servidor y a la base de datos, crear una tabla y cargar los datos mediante una instrucción SQL **INSERT**. El código importa la biblioteca mysql.connector y utiliza el método:
- Función [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) para conectarse a Azure Database for MySQL mediante los [argumentos](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) de la colección de configuración. 
- El método [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) ejecuta la consulta SQL en la base de datos MySQL. 
- [cursor.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) cuando haya terminado de usar un cursor.
- [conn.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) para cerrar la conexión.

> [!IMPORTANT]
> - SSL está habilitado de manera predeterminada. Es posible que tenga que descargar el [certificado de SSL DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) para conectarse desde el entorno local.
> - Reemplace los marcadores de posición `<mydemoserver>`, `<myadmin>`, `<mypassword>`y `<mydatabase>` por los valores de la base de datos y el servidor de MySQL.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [mysql.connector.ClientFlag.SSL],
  'ssl_ca': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
}

# Construct connection string

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

[¿Tiene problemas? Háganoslo saber.](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>Paso 2: Lectura de datos

Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **SELECT**. El código importa la biblioteca mysql.connector y usa el método [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) ejecuta la consulta SQL en la base de datos MySQL. 

El código lee las filas de datos con el método [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html), mantiene el conjunto de resultados en una fila de colección y usa un iterador `for` para recorrer las filas.

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>Paso 3: Actualización de datos

Use el código siguiente para conectarse y actualizar los datos mediante la instrucción SQL **UPDATE**. El código importa la biblioteca mysql.connector y usa el método [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) ejecuta la consulta SQL en la base de datos MySQL. 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>Paso 4: Eliminación de datos

Use el código siguiente para conectarse y eliminar datos mediante la instrucción SQL **DELETE**. El código importa la biblioteca mysql.connector y usa el método [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) ejecuta la consulta SQL en la base de datos MySQL. 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
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
> [Administración de un servidor de Azure Database for MySQL mediante Azure Portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Administración con la CLI de Azure de una instancia de Azure Database for MySQL: Servidor único](./how-to-manage-single-server-cli.md)

[¿No encuentra lo que busca? Háganoslo saber.](https://aka.ms/mysql-doc-feedback)
