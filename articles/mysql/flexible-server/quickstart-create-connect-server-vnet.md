---
title: Conexión al servidor flexible de Azure Database for MySQL con acceso privado en Azure Portal
description: Este artículo le ayudará a usar Azure Portal para crear y conectarse a un servidor flexible de Azure Database for MySQL en el acceso privado.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/18/2021
ms.openlocfilehash: b1168833242aa69b8e1bff5d7bd810f2c3db019d
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693591"
---
# <a name="connect-azure-database-for-mysql-flexible-server-with-private-access-connectivity-method"></a>Conexión al servidor flexible de Azure Database for MySQL con un método de conectividad de acceso privado

Servidor flexible de Azure Database for MySQL es un servicio administrado que se usa para ejecutar, administrar y escalar servidores MySQL de alta disponibilidad en la nube. En este inicio rápido se muestra cómo crear un servidor flexible en una red virtual mediante Azure Portal.

> [!IMPORTANT]
> Actualmente, la opción de implementación Servidor flexible de Azure Database for MySQL se encuentra en versión preliminar pública.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de empezar.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal
Vaya a [Azure Portal](https://portal.azure.com/). Introduzca sus credenciales para iniciar sesión en el portal. La vista predeterminada es el panel del servicio.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Creación de un servidor flexible de Azure Database for MySQL

Puede crear un servidor flexible mediante un conjunto definido de [recursos de proceso y almacenamiento](./concepts-compute-storage.md). Cree el servidor dentro en un [grupo de recursos de Azure](../../azure-resource-manager/management/overview.md).

Siga estos pasos para crear un servidor flexible:

1. Busque y seleccione **Azure Database for MySQL** en el portal:

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png" alt-text="Captura de pantalla que muestra una búsqueda de servidores de Azure Database for MySQL." lightbox="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png":::

2. Seleccione **Agregar**.

3. En la página **Seleccionar opción de implementación de Azure Database for MySQL**, seleccione **Servidor flexible** como opción de implementación:

    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-option.png" alt-text="Captura de pantalla que muestra la opción Servidor flexible." lightbox="./media/quickstart-create-connect-server-vnet/deployment-option.png":::

4. En la pestaña **Aspectos básicos**, escriba la **suscripción**, el **grupo de recursos**, la **región**, el **nombre de usuario de administrador** y la **contraseña de administrador**.  Con los valores predeterminados, se aprovisionará un servidor MySQL de la versión 5.7 con una SKU por ráfagas mediante 1 núcleo virtual, memoria de 2 GiB y almacenamiento de 32 GiB. La retención de copia de seguridad es de 7 días. La configuración se puede cambiar.

    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/mysql-flexible-server-create-portal.png" alt-text="Captura de pantalla que muestra la pestaña Conceptos básicos de la página Servidor flexible." lightbox="./media/quickstart-create-connect-server-vnet/mysql-flexible-server-create-portal.png":::

   > [!TIP]
   > Para agilizar la carga de datos durante la migración, se recomienda aumentar IOPS hasta el tamaño máximo admitido por el tamaño de proceso y, posteriormente, volver a escalarlo para ahorrar costos.

5.  Vaya a la pestaña **Redes** y seleccione **acceso privado**. No puede cambiar el método de conectividad después de crear el servidor. Seleccione **Crear una red virtual** para crear la nueva red virtual **vnetenvironment1**.

    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-mysql-server.png" alt-text="Captura de pantalla que muestra la pestaña Redes con la nueva red virtual." lightbox="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-mysql-server.png":::

6. Seleccione **Aceptar** una vez que haya proporcionado el nombre de la red virtual y la información de subred.
    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/show-server-vnet-information.png" alt-text="revisión de la información de red virtual":::

7. Seleccione **Revisar y crear** para revisar la configuración del servidor flexible.

8. Seleccione **Crear** para realizar el aprovisionamiento del servidor. El aprovisionamiento puede tardar unos minutos.

9. Espere hasta que la implementación se complete y se realice correctamente.

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-success.png" alt-text="Captura de pantalla que muestra la configuración de red con la nueva red virtual." lightbox="./media/quickstart-create-connect-server-vnet/deployment-success.png":::

9.  Seleccione **Ir al recurso** para ver cómo se abre la página **Información general** del servidor.

## <a name="create-azure-linux-virtual-machine"></a>Creación de una máquina virtual Linux de Azure

Puesto que el servidor está en la red virtual, solo puede conectarse al servidor desde otros servicios de Azure en la misma red virtual que el servidor. Para conectarse al servidor y administrarlo, vamos a crear una máquina virtual Linux. La máquina virtual debe crearse en la **misma región** y la **misma suscripción**. La máquina virtual Linux se puede usar como túnel SSH para administrar el servidor de bases de datos. 

1. Vaya al grupo de recursos en el que se creó el servidor. Seleccione **Agregar**.
2. Seleccione **Ubuntu Server 18.04 LTS**.
3. En la pestaña **Aspectos básicos**, en **Detalles del proyecto**, asegúrese de que esté seleccionada la suscripción correcta y luego elija **Crear nuevo** grupo de recursos. Escriba *myResourceGroup* para el nombre.

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/project-details.png" alt-text="Captura de pantalla de la sección Detalles del proyecto en la que se muestra dónde se selecciona la suscripción de Azure y el grupo de recursos de la máquina virtual" lightbox="../../virtual-machines/linux/media/quick-create-portal/project-details.png"::: 

2. En **Detalles de instancia**, escriba *myVM* en **Nombre de máquina virtual** y elija la misma **Región** que el servidor de bases de datos.

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/instance-details.png" alt-text="Captura de pantalla de la sección Detalles de la instancia, en la que se proporciona un nombre para la máquina virtual y se selecciona su región, imagen y tamaño]" lightbox="../../virtual-machines/linux/media/quick-create-portal/instance-details.png":::

3. En **Cuenta de administrador** , seleccione **Clave pública SSH**.

4. En **Nombre de usuario**, escriba *azureuser*.

5. En **Origen de clave pública SSH**, deje el valor predeterminado **Generar nuevo par de claves** y, a continuación, escriba *myKey* en **Nombre de par de claves**.

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png" alt-text="Captura de pantalla de la sección Cuenta de administrador, en la que se selecciona un tipo de autenticación y se especifican las credenciales del administrador" lightbox="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png":::

6. En **Reglas de puerto de entrada** > **Puertos de entrada públicos**, elija **Permitir los puertos seleccionados** y luego seleccione **SSH (22)** y **HTTP (80)** en la lista desplegable.

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png" alt-text="Captura de pantalla de la sección Reglas de puerto de entrada, donde se seleccionan los puertos en los que se permiten conexiones entrantes" lightbox="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png":::

7. Seleccione la página **Redes** para configurar la red virtual. Para la red virtual, elija la red virtual **vnetenvironment1** creada para el servidor de bases de datos.

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png" alt-text="Captura de pantalla de selección de una red virtual existente del servidor de bases de datos" lightbox="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png":::

8. Seleccione **Administrar configuración de subred** para crear una nueva subred para el servidor.

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png" alt-text="Captura de pantalla de administración de una subred" lightbox="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png":::

9. Agregue una nueva subred para la máquina virtual.

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png" alt-text="Captura de pantalla de la adición de una nueva subred para la máquina virtual" lightbox="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png"::: 

10. Una vez que la subred se haya creado correctamente, cierre la página.
   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/subnetcreate-success.png" alt-text="Captura de pantalla del éxito con la adición de una nueva subred para la máquina virtual" lightbox="./media/quickstart-create-connect-server-vnet/subnetcreate-success.png":::

11. Seleccione **Revisar + crear**.
12. Seleccione **Crear**. Cuando se abra la ventana **Generar nuevo par de claves**, seleccione **Descargar la clave privada y crear el recurso**. El archivo de clave se descargará como **myKey.pem**.

   >[!IMPORTANT]
   > Asegúrese de que sabe dónde se descargó el archivo `.pem`, ya que necesitará la ruta de acceso en el paso siguiente.

13. Cuando la implementación finalice, seleccione **Ir al recurso**.
   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-create-success.png" alt-text="Captura de pantalla de la implementación correcta" lightbox="./media/quickstart-create-connect-server-vnet/vm-create-success.png":::

11. En la página de la nueva máquina virtual, seleccione la dirección IP pública y cópiela en el portapapeles.
   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/ip-address.png" alt-text="Captura de pantalla en que se muestra cómo copiar la dirección IP de la máquina virtual" lightbox="../../virtual-machines/linux/media/quick-create-portal/ip-address.png":::

## <a name="install-mysql-client-tools"></a>Instalación de herramientas de cliente de MySQL

Cree una conexión SSH con la máquina virtual mediante Bash o PowerShell. En el símbolo del sistema, abra una conexión SSH a la máquina virtual. Reemplace la dirección IP por la de la máquina virtual y reemplace la ruta de acceso al archivo `.pem` por la ruta de acceso a la ubicación en la que se descargó el archivo de clave.

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

> [!TIP]
> Ahora la clave SSH que creó se puede usar la próxima vez que cree una máquina virtual en Azure. Solo tiene que seleccionar la opción **Usar la clave existente almacenada en Azure** en **Origen de clave pública SSH** la próxima vez que cree una máquina virtual. Ya dispone de la clave privada en el equipo, por lo que no tendrá que descargar nada.

Debe instalar la herramienta mysql-client para poder conectarse al servidor.

```bash
sudo apt-getupdate
sudo apt-get install mysql-client
```

Las conexiones a la base de datos se aplican con SSL, por lo que debe descargar el certificado SSL público.

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
```

## <a name="connect-to-the-server-from-azure-linux-virtual-machine"></a>Conexión al servidor desde una máquina virtual Linux de Azure
Con la herramienta de cliente [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) instalada, ahora podemos conectarnos al servidor desde el entorno local.

```bash
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

## <a name="clean-up-resources"></a>Limpieza de recursos
Ha creado un servidor flexible de Azure Database for MySQL en un grupo de recursos. Si no cree que vaya a necesitar estos recursos en el futuro, puede eliminarlos mediante la eliminación del grupo de recursos o bien puede eliminar simplemente el servidor de MySQL. Para eliminar el grupo de recursos, siga estos pasos:

1. En Azure Portal, busque y seleccione **Grupos de recursos**.
1. En la lista, seleccione el nombre de su grupo de recursos.
1. En la página de **información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
1. En el cuadro de diálogo de confirmación, escriba el nombre del grupo de recursos y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Compilación de una aplicación web PHP (Laravel) con MySQL](tutorial-php-database-app.md)
