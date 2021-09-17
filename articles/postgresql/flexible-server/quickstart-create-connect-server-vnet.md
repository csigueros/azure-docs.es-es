---
title: Conexión al servidor flexible de Azure Database for PostgreSQL con acceso privado en Azure Portal
description: En este artículo se muestra cómo crear un servidor flexible de Azure Database for PostgreSQL con acceso privado o red virtual mediante Azure Portal y cómo conectarse a él.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 07/29/2021
ms.openlocfilehash: 772cd5c647c3e03773bd6c3e7f57616093b4c7ce
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694151"
---
# <a name="connect-azure-database-for-postgresql-flexible-server-with-the-private-access-connectivity-method"></a>Conexión al servidor flexible de Azure Database for PostgreSQL con un método de conectividad de acceso privado

El servidor flexible de Azure Database for PostgreSQL es un servicio administrado para ejecutar, administrar y escalar servidores de PostgreSQL de alta disponibilidad en la nube. En este inicio rápido se muestra cómo crear un servidor flexible en una red virtual mediante Azure Portal.

> [!IMPORTANT]
> Actualmente, el servidor flexible de Azure Database for PostgreSQL se encuentra en versión preliminar pública.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de empezar.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Vaya a [Azure Portal](https://portal.azure.com/). Introduzca sus credenciales para iniciar sesión en el portal. La vista predeterminada es el panel del servicio.

## <a name="create-an-azure-database-for-postgresql-flexible-server"></a>Creación de servidor flexible de Azure Database for PostgreSQL

Puede crear un servidor flexible mediante un conjunto definido de [recursos de proceso y almacenamiento](./concepts-compute-storage.md). Cree el servidor dentro en un [grupo de recursos de Azure](../../azure-resource-manager/management/overview.md).

Siga estos pasos para crear un servidor flexible:

1. Busque y seleccione **Servidores de Azure Database for PostgreSQL** en el portal:

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png" alt-text="Captura de pantalla que muestra una búsqueda de servidores de Azure Database for PostgreSQL." lightbox="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png":::

2. Seleccione **Agregar**.

3. En la página **Select Azure Database for PostgreSQL deployment option** (Seleccionar opción de implementación de Azure Database for PostgreSQL), seleccione **Flexible server** (Servidor flexible) como opción de implementación:

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-option.png" alt-text="Captura de pantalla que muestra la opción Servidor flexible." lightbox="./media/quickstart-create-connect-server-vnet/deployment-option.png":::

4. En la pestaña **Aspectos básicos**, escriba la **suscripción**, el **grupo de recursos**, la **región** y el **nombre del servidor**.  Con los valores predeterminados, se aprovisionará la versión 12 de un servidor de PostgreSQL con el plan de tarifa de uso general con 2 núcleos virtuales, 8 GiB de RAM y 28 GiB de almacenamiento. La retención de las copias de seguridad es de **siete** días. Puede usar la carga de trabajo **Desarrollo** para usar un plan de tarifa más reducido de manera predeterminada.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/postgres-create-basics.png" alt-text="Captura de pantalla que muestra la pestaña Aspectos básicos de la página del servidor flexible de Postgres." lightbox="./media/quickstart-create-connect-server-vnet/postgres-create-basics.png":::

5. En la pestaña **Aspectos básicos**, escriba un **nombre de usuario administrador** único y la **contraseña de administrador**.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/db-administrator-account.png" alt-text="Captura de pantalla que muestra la página de información del usuario administrador." lightbox="./media/quickstart-create-connect-server-vnet/db-administrator-account.png":::

6.  Vaya a la pestaña **Redes** y seleccione **acceso privado**. No puede cambiar el método de conectividad después de crear el servidor. Seleccione **Crear una red virtual** para crear una red virtual llamada **vnetenvironment1**. Seleccione **Aceptar** una vez que haya proporcionado el nombre de la red virtual y la información de subred.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-postgres-server.png" alt-text="Captura de pantalla que muestra la pestaña Redes con la nueva red virtual." lightbox="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-postgres-server.png":::

7. Seleccione **Revisar y crear** para revisar la configuración del servidor flexible.

8. Seleccione **Crear** para realizar el aprovisionamiento del servidor. El aprovisionamiento puede tardar unos minutos.

9. Espere hasta que la implementación se complete y se realice correctamente.

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-success.png" alt-text="Captura de pantalla que muestra la configuración de red con la nueva red virtual." lightbox="./media/quickstart-create-connect-server-vnet/deployment-success.png":::

9.  Seleccione **Ir al recurso** para ver cómo se abre la página **Información general** del servidor.

## <a name="create-an-azure-linux-virtual-machine"></a>Crear una máquina virtual Linux de Azure

Puesto que el servidor está en una red virtual, solo puede conectarse a él desde otros servicios de Azure de la misma red virtual. Para conectarse al servidor y administrarlo, vamos a crear una máquina virtual Linux. La máquina virtual debe crearse en la **misma región** y la **misma suscripción**. La máquina virtual Linux se puede usar como túnel SSH para administrar el servidor de bases de datos. 

1. Vaya al grupo de recursos en el que se creó el servidor. Seleccione **Agregar**.
2. Seleccione **Ubuntu Server 18.04 LTS**.
3. En la pestaña **Aspectos básicos**, en **Detalles del proyecto**, asegúrese de que esté seleccionada la suscripción correcta y luego elija **Crear nuevo** grupo de recursos. Escriba *myResourceGroup* para el nombre.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/project-details.png" alt-text="Captura de pantalla de la sección Detalles del proyecto en la que se muestra dónde se selecciona la suscripción de Azure y el grupo de recursos de la máquina virtual." lightbox="../../virtual-machines/linux/media/quick-create-portal/project-details.png"::: 

2. En **Detalles de instancia**, escriba *myVM* en **Nombre de máquina virtual** y elija el mismo valor de **Región** que el del servidor de bases de datos.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/instance-details.png" alt-text="Captura de pantalla de la sección Detalles de la instancia, en la que se proporciona un nombre para la máquina virtual y se selecciona su región, imagen y tamaño." lightbox="../../virtual-machines/linux/media/quick-create-portal/instance-details.png":::

3. En **Cuenta de administrador** , seleccione **Clave pública SSH**.

4. En **Nombre de usuario**, escriba *azureuser*.

5. En **Origen de clave pública SSH**, deje el valor predeterminado **Generar nuevo par de claves** y, a continuación, escriba *myKey* en **Nombre de par de claves**.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png" alt-text="Captura de pantalla de la sección Cuenta de administrador, en la que se selecciona un tipo de autenticación y se proporcionan las credenciales del administrador." lightbox="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png":::

6. En **Reglas de puerto de entrada** > **Puertos de entrada públicos**, elija **Permitir los puertos seleccionados** y luego seleccione **SSH (22)** y **HTTP (80)** en la lista desplegable.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png" alt-text="Captura de pantalla de la sección de reglas de puerto de entrada, donde se seleccionan los puertos en los que se permiten conexiones entrantes." lightbox="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png":::

7. Seleccione la página **Redes** para configurar la red virtual. Para la red virtual, elija **vnetenvironment1**, creada para el servidor de bases de datos.

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png" alt-text="Captura de pantalla de selección de una red virtual existente del servidor de bases de datos." lightbox="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png":::

8. Seleccione **Administrar configuración de subred** para crear una nueva subred para el servidor.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png" alt-text="Captura de pantalla de administración de una subred." lightbox="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png":::

9. Agregue la subred nueva para la máquina virtual.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png" alt-text="Captura de pantalla de la incorporación de una nueva subred para la máquina virtual" lightbox="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png"::: 

10. Una vez creada la subred correctamente, cierre la página.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/subnet-create-success.png" alt-text="Captura de pantalla de la incorporación correcta de una nueva subred para la máquina virtual." lightbox="./media/quickstart-create-connect-server-vnet/subnet-create-success.png":::

11. Seleccione **Revisar + crear**.
12. Seleccione **Crear**. Cuando se abra la ventana **Generar nuevo par de claves**, seleccione **Descargar la clave privada y crear el recurso**. El archivo de clave se descargará como **myKey.pem**.

    >[!IMPORTANT]
    > Asegúrese de saber dónde se descargó el archivo `.pem`. Necesitará esta la ruta de acceso en el siguiente paso.

13. Una vez finalizada la implementación, seleccione **Ir al recurso** para ver la página **Información general** de la máquina virtual.

14. Seleccione la dirección IP pública y cópiela en el portapapeles.

    :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/ip-address.png" alt-text="Captura de pantalla en que se muestra cómo copiar la dirección IP de la máquina virtual." lightbox="../../virtual-machines/linux/media/quick-create-portal/ip-address.png":::

## <a name="install-postgresql-client-tools"></a>Instalación de las herramientas de cliente de PostgreSQL

Cree una conexión SSH con la máquina virtual mediante Bash o PowerShell. En el símbolo del sistema, abra una conexión SSH a la máquina virtual. Reemplace la dirección IP por la de la máquina virtual y reemplace la ruta de acceso al archivo `.pem` por la ruta de acceso a la ubicación en la que se descargó el archivo de clave.

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

> [!TIP]
> Ahora la clave SSH que creó se puede usar la próxima vez que cree una máquina virtual en Azure. Solo tiene que seleccionar la opción **Usar la clave existente almacenada en Azure** en **Origen de clave pública SSH** la próxima vez que cree una máquina virtual. Ya dispone de la clave privada en el equipo, por lo que no tendrá que descargar nada.

Debe instalar la herramienta postgresql-client para conectarse al servidor.

```bash
sudo apt-getupdate
sudo apt-get install postgresql-client
```

Las conexiones a la base de datos se aplican con SSL, por lo que debe descargar el certificado SSL público.

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
```

## <a name="connect-to-the-server-from-azure-linux-virtual-machine"></a>Conexión al servidor desde una máquina virtual Linux de Azure
Con la herramienta de cliente **psql** instalada, ahora podemos conectarnos al servidor desde el entorno local.

```bash
psql --host=mydemoserver-pg.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres --set=sslmode=require --set=sslrootcert=DigiCertGlobalRootCA.crt.pem
```

## <a name="clean-up-resources"></a>Limpieza de recursos
Ha creado un servidor flexible de Azure Database for PostgreSQL en un grupo de recursos. Si no cree que vaya a necesitar estos recursos en el futuro, puede eliminarlos mediante la eliminación del grupo de recursos, o bien, elimine simplemente el servidor de PostgreSQL. Para eliminar el grupo de recursos, siga estos pasos:

1. En Azure Portal, busque y seleccione **Grupos de recursos**.
1. En la lista, seleccione el nombre de su grupo de recursos.
1. En la página de **información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
1. En el cuadro de diálogo de confirmación, escriba el nombre del grupo de recursos y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Conexión con la aplicación Python](connect-python.md)
