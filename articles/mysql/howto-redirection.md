---
title: 'Conexión con redireccionamiento: Azure Database for MySQL'
description: En este artículo se describe cómo puede configurar la aplicación para que se conecte a Azure Database for MySQL con redireccionamiento.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: 44275c7e6469c63f037a7b24d5435aa5ce3941d6
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652353"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Conexión a Azure Database for MySQL con redireccionamiento

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

En este tema se explica cómo conectar una aplicación del servidor de Azure Database for MySQL con modo de redireccionamiento. El redireccionamiento pretende reducir la latencia de red entre las aplicaciones cliente y los servidores MySQL al permitir que las aplicaciones se conecten directamente a los nodos del servidor back-end.

## <a name="before-you-begin"></a>Antes de empezar
Inicie sesión en [Azure Portal](https://portal.azure.com). Cree un servidor de Azure Database for MySQL con la versión de motor 5.6, 5.7 u 8.0. 

Para obtener información detallada sobre cómo crear un servidor de Azure Database for MySQL mediante [Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) o la [CLI de Azure](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!IMPORTANT]
> Actualmente no se admite el redireccionamiento con [Private Link para Azure Database for MySQL](concepts-data-access-security-private-link.md).

## <a name="enable-redirection"></a>Habilitación del redireccionamiento

En el servidor de Azure Database for MySQL, configure el parámetro `redirect_enabled` en `ON` para permitir conexiones con el modo de redireccionamiento. Para actualizar este parámetro del servidor, use [Azure Portal](howto-server-parameters.md) o la [CLI de Azure](howto-configure-server-parameters-using-cli.md).

## <a name="php"></a>PHP

La compatibilidad con el redireccionamiento en aplicaciones PHP está disponible a través de la extensión [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure), desarrollada por Microsoft. 

La extensión mysqlnd_azure está disponible para agregarse a las aplicaciones PHP a través de PECL y se recomienda encarecidamente instalar y configurar la extensión mediante el [paquete PECL](https://pecl.php.net/package/mysqlnd_azure) publicado oficialmente.

> [!IMPORTANT]
> La compatibilidad con el redireccionamiento en la extensión [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) de PHP se encuentra actualmente en versión preliminar.

### <a name="redirection-logic"></a>Lógica de redireccionamiento

>[!IMPORTANT]
> La lógica y el comportamiento del redireccionamiento a partir de la versión 1.1.0 se actualizó y **se recomienda utilizar la versión 1.1.0+** .

El comportamiento del redireccionamiento viene determinado por el valor de `mysqlnd_azure.enableRedirect`. En la tabla siguiente se describe el comportamiento del redireccionamiento según el valor de este parámetro a partir de la **versión 1.1.0 +** .

Si usa una versión anterior de la extensión mysqlnd_azure (versión 1.0.0-1.0.3), el comportamiento del redireccionamiento viene determinado por el valor de `mysqlnd_azure.enabled`. Los valores válidos son `off` (actúa del mismo modo que el comportamiento descrito en la tabla siguiente) y `on` (actúa como `preferred` en la tabla siguiente).  

|**Valor de mysqlnd_azure.enableRedirect**| **Comportamiento**|
|----------------------------------------|-------------|
|`off` o `0`|No se usará el redireccionamiento. |
|`on` o `1`|- Si la conexión no usa SSL en el lado del controlador, no se realizará ninguna conexión. Se devolverá el siguiente error: *"mysqlnd_azure.enableRedirect está activado, pero la opción SSL no está establecida en la cadena de conexión. El redireccionamiento solo es posible con SSL.".*<br>- Si se usa SSL en el lado del controlador, pero no se admite el redireccionamiento en el servidor, se anulará la primera conexión y se devolverá el siguiente error: *"Conexión anulada porque el redireccionamiento no está habilitado en el servidor MySQL o el paquete de red no cumple el protocolo de redireccionamiento.".*<br>- Si el servidor MySQL admite el redireccionamiento, pero se produjo un error en la conexión redirigida por cualquier motivo, se anula también la primera conexión del proxy. Se devuelve el error de la conexión redirigida.|
|`preferred` o `2`<br> (valor predeterminado)|- mysqlnd_azure usará el redireccionamiento si es posible.<br>- Si la conexión no usa SSL en el lado del controlador, el servidor no admite el redireccionamiento o la conexión redirigida no se puede conectar por cualquier motivo no grave mientras la conexión del proxy sigue siendo válida, se revertirá a la primera conexión del proxy.|

En las secciones siguientes del documento se describe cómo instalar la extensión `mysqlnd_azure` mediante PECL y cómo establecer el valor de este parámetro.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Requisitos previos 
- Versiones de PHP 7.2.15 y posterior y 7.3.2 y posterior.
- PHP PEAR 
- php-mysql
- Servidor de Azure Database for MySQL

1. Instale [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) con [PECL](https://pecl.php.net/package/mysqlnd_azure). Se recomienda utilizar la versión 1.1.0+.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Para buscar el directorio de la extensión (`extension_dir`), ejecute lo siguiente:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Cambie los directorios a la carpeta devuelta y asegúrese de que `mysqlnd_azure.so` se encuentra en esta carpeta. 

4. Para buscar la carpeta de los archivos .ini, ejecute lo siguiente: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Cambie los directorios a la carpeta devuelta. 

6. Cree un nuevo archivo .ini para `mysqlnd_azure`. Asegúrese de que el nombre se encuentre después de mysqnld al ordenarlos alfabéticamente, ya que los módulos se cargan según el orden de los nombres de los archivos ini. Por ejemplo, si el archivo `mysqlnd` .ini se denomina `10-mysqlnd.ini`, asigne al archivo ini mysqlnd el nombre `20-mysqlnd-azure.ini`.

7. En el nuevo archivo .ini, agregue las líneas siguientes para habilitar el redireccionamiento.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Requisitos previos 
- Versiones de PHP 7.2.15 y posterior y 7.3.2 y posterior.
- php-mysql
- Servidor de Azure Database for MySQL

1. Para determinar si está ejecutando una versión x64 o x86 de PHP, ejecute el siguiente comando:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Descargue la versión x64 o x86 correspondiente del archivo DLL [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) de [PECL](https://pecl.php.net/package/mysqlnd_azure) que coincida con su versión de PHP. Se recomienda utilizar la versión 1.1.0+.

3. Extraiga el archivo ZIP y busque el archivo DLL denominado `php_mysqlnd_azure.dll`.

4. Para buscar el directorio de la extensión (`extension_dir`), ejecute el comando siguiente:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Copie el archivo de `php_mysqlnd_azure.dll` en el directorio devuelto en el paso 4. 

6. Use el siguiente comando para buscar la carpeta PHP que contiene el archivo `php.ini`:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Modifique el archivo `php.ini` y agregue las siguientes líneas adicionales para habilitar el redireccionamiento. 

    En la sección de extensiones dinámicas: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    En la sección de configuración del módulo:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Confirmación del redireccionamiento

También puede confirmar que el redireccionamiento está configurado con el siguiente código PHP de ejemplo. Cree un archivo PHP denominado `mysqlConnect.php` y pegue el código siguiente. Reemplace el nombre del servidor, el nombre de usuario y la contraseña por sus propios valores. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre las cadenas de conexión, consulte [Cadenas de conexión](howto-connection-string.md).
