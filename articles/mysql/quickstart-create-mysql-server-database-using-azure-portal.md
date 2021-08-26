---
title: 'Inicio rápido: Creación de un servidor: Azure Portal (Azure Database for MySQL)'
description: Este artículo le ayudará a usar Azure Portal para crear un servidor de Azure Database for MySQL de ejemplo en unos cinco minutos.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: 6e1e76d9afad049d79c68db93c08c935d190fbb6
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122643583"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Inicio rápido: Creación de un servidor de Azure Database for MySQL mediante Azure Portal

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL es un servicio administrado que se usa para ejecutar, administrar y escalar bases de datos MySQL de alta disponibilidad en la nube. En esta guía de inicio rápido se explica cómo usar Azure Portal para crear un servidor único de Azure Database for MySQL. También se muestra cómo conectarse al servidor.

## <a name="prerequisites"></a>Requisitos previos
Se necesita una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de empezar.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Creación a un servidor único de Azure Database for MySQL
1. Vaya a [Azure Portal](https://portal.azure.com/) para crear una base de datos de servidor único de MySQL. Busque **Azure Database for MySQL** y selecciónelo.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Enlace de Azure Database for MySQL":::

1. Seleccione **Agregar**.

2. En la página **Select Azure Database for MySQL deployment option** (Seleccionar opción de implementación de Azure Database for MySQL), seleccione **Single server** (Un solo servidor):
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Captura de pantalla que muestra la opción de un solo servidor.":::

3. Escriba la configuración básica para un nuevo servidor único:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Captura de pantalla que muestra la página de creación del servidor MySQL.":::

   **Configuración** | **Valor sugerido** | **Descripción**
   ---|---|---
   Suscripción | Su suscripción | Seleccione la suscripción de Azure que desee.
   Resource group | **myresourcegroup** | Escriba un nuevo grupo de recursos o uno existente de la suscripción.
   Nombre de servidor | **mydemoserver** | Escriba un nombre único. El nombre del servidor solo puede contener letras minúsculas, números y el carácter de guion (-). Debe contener entre 3 y 63 caracteres.
   Origen de datos |**None** | Seleccione **None** para crear un servidor desde cero. Seleccione **Copia de seguridad** solo si va a restaurar a partir de una copia de seguridad geográfica de un servidor existente.
   Location |Su ubicación deseada | Seleccione una ubicación de la lista.
   Versión | La versión principal más reciente| Use la versión principal más reciente. Consulte [todas las versiones admitidas](concepts-supported-versions.md).
   Proceso y almacenamiento | Uso de los valores predeterminados| El plan de tarifa predeterminado es **De uso general** con **cuatro núcleos virtuales** y **100 GB** de almacenamiento. La retención de copias de seguridad se establece en **siete días** con opción de copia de seguridad con **redundancia geográfica**.<br/>Revise la página de [precios](https://azure.microsoft.com/pricing/details/mysql/) y actualice los valores predeterminados si es necesario.
   Nombre de usuario administrador | **mydemoadmin** | Escriba su nombre de usuario administrador del servidor. El nombre de usuario administrador no puede ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** ni **public**.
   Contraseña | Una contraseña | Una contraseña nueva para el usuario administrador del servidor. La contraseña debe tener entre 8 y 128 caracteres de longitud y debe contener una combinación de letras mayúsculas o minúsculas, números y caracteres no alfanuméricos (!, $, #,%, etc.).
  

   > [!NOTE]
   > Considere la posibilidad de usar el plan de tarifa Básico si menos proceso y E/S resultan adecuados para su carga de trabajo. Tenga en cuenta que los servidores que se creen en el plan de tarifa Básico no se podrán escalar más adelante a De uso general u Optimizada para memoria.

4. Seleccione **Revisar y crear** para realizar el aprovisionamiento del servidor.

5. Espere hasta que la página del portal muestre **Se completó la implementación** . Seleccione **Ir al recurso** para ir a la página del servidor recién creado:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="Captura de pantalla que muestra el mensaje de que se completó la implementación.":::

[¿Tiene problemas? Háganoslo saber.](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Configuración de una regla de firewall de nivel de servidor

De forma predeterminada, el nuevo servidor está protegido con un firewall. Para conectarse, debe proporcionar acceso a la dirección IP completando estos pasos:

1. Vaya a **Seguridad de conexión** en el panel izquierdo del recurso del servidor. Si no sabe cómo encontrar el recurso, consulte [Apertura de recursos](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Captura de pantalla que muestra la página Seguridad de conexión > Reglas de firewall.":::

2. Ahora, seleccione **Agregar dirección IP del cliente actual** y, después, **Guardar**.

   > [!NOTE]
   > Para evitar problemas de conectividad, compruebe si la red permite el tráfico de salida por el puerto 3306 que usa Azure Database for MySQL. 

Puede agregar más direcciones IP o proporcionar un intervalo de direcciones IP para conectarse al servidor desde esas direcciones. Para más información, consulte [Reglas de firewall del servidor de Azure Database for MySQL](./concepts-firewall-rules.md).


[¿Tiene problemas? Háganoslo saber.](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Conexión al servidor con mysql.exe
Puede usar [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) o [MySQL Workbench](./connect-workbench.md) para conectarse al servidor desde su entorno local. En este inicio rápido, se usará mysql.exe en [Azure Cloud Shell](../cloud-shell/overview.md) para conectarse al servidor.


1. Abra Azure Cloud Shell en el portal; para ello, seleccione el primer botón de la barra de herramientas, como se muestra en la siguiente captura de pantalla. Anote el nombre del servidor, el nombre del administrador del servidor y la suscripción del nuevo servidor en la sección **Información general**, tal como se muestra en la captura de pantalla.

    > [!NOTE]
    > Si es la primera vez que abre Cloud Shell, verá un símbolo del sistema para crear un grupo de recursos y una cuenta de almacenamiento. Esta es una instalación de un único paso. Se adjuntará automáticamente para todas las sesiones.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Captura de pantalla que muestra Cloud Shell en Azure Portal.":::
2. Ejecute el siguiente comando en el terminal de Azure Cloud Shell. Reemplace los valores que se muestra aquí por el nombre del servidor real y el nombre de usuario administrador. Para Azure Database for MySQL, debe agregar `@\<servername>` al nombre de usuario administrador, como se muestra aquí: 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Este es su aspecto en el terminal de Cloud Shell:

      ```
      Requesting a Cloud Shell.Succeeded.
      Connecting terminal...

      Welcome to Azure Cloud Shell

      Type "az" to use Azure CLI
      Type "help" to learn about Cloud Shell

      user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
      Enter password:
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 64796
      Server version: 5.6.42.0 Source distribution

      Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
      Oracle is a registered trademark of Oracle Corporation and/or its
      affiliates. Other names may be trademarks of their respective
      owners.

      Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
      mysql>
      ```
3. En el mismo terminal de Azure Cloud Shell, cree la base de datos denominada `guest`.
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Cambie a la base de datos `guest`:
      ```
      mysql> USE guest;
      Database changed
      ```
5. Escriba `quit` y, a continuación, seleccione **Entrar** para salir de MySQL.

[¿Tiene problemas? Háganoslo saber.](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Limpieza de recursos
Ha creado un servidor de Azure Database for MySQL en un grupo de recursos.  Si no cree que vaya a necesitar estos recursos en el futuro, puede eliminarlos mediante la eliminación del grupo de recursos o bien puede eliminar simplemente el servidor de MySQL. Para eliminar el grupo de recursos, siga estos pasos:
1. En Azure Portal, busque y seleccione **Grupos de recursos**.
2. En la lista, seleccione el nombre de su grupo de recursos.
3. En la página de **información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
4. En el cuadro de diálogo de confirmación, escriba el nombre del grupo de recursos y seleccione **Eliminar**.

Para eliminar el servidor, puede seleccionar **Eliminar** en la página de **información general** del servidor, como se muestra aquí:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Captura de pantalla que muestra el botón Eliminar en la página de información general del servidor.":::

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
>[Compilación de una aplicación PHP en Windows con MySQL](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[Compilación de una aplicación PHP en Linux con MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[¿No encuentra lo que busca? Háganoslo saber.](https://aka.ms/mysql-doc-feedback)
