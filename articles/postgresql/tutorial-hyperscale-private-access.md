---
title: 'Creación de un grupo de servidores con acceso privado (versión preliminar): Azure Database for PostgreSQL: Hiperescala (Citus)'
description: Conexión de una máquina virtual a un punto de conexión privado del grupo de servidores
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: tutorial
ms.date: 10/15/2021
ms.openlocfilehash: d024f68b68fd381641c0dc7aaf5aedefd3930119
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075181"
---
# <a name="create-server-group-with-private-access-preview-in-azure-database-for-postgresql---hyperscale-citus"></a>Creación de un grupo de servidores con acceso privado (versión preliminar): Azure Database for PostgreSQL: Hiperescala (Citus)

En este tutorial se crea una máquina virtual y un grupo de servidores de Hiperescala (Citus) y se establece el [acceso privado](concepts-hyperscale-private-access.md) entre ellos.

## <a name="create-a-virtual-network"></a>Creación de una red virtual

En primer lugar, configuraremos un grupo de recursos y una red virtual. Contendrán el grupo de servidores y la máquina virtual.

```sh
az group create \
    --name link-demo \
    --location eastus

az network vnet create \
    --resource-group link-demo \
    --name link-demo-net \
    --address-prefix 10.0.0.0/16

az network nsg create \
    --resource-group link-demo \
    --name link-demo-nsg

az network vnet subnet create \
    --resource-group link-demo \
    --vnet-name link-demo-net \
    --name link-demo-subnet \
    --address-prefixes 10.0.1.0/24 \
    --network-security-group link-demo-nsg
```

## <a name="create-a-virtual-machine"></a>Creación de una máquina virtual

Para la demostración, usaremos una máquina virtual que ejecuta Debian Linux y el cliente PostgreSQL `psql`.

```sh
# provision the VM
az vm create \
    --resource-group link-demo \
    --name link-demo-vm \
    --vnet-name link-demo-net \
    --subnet link-demo-subnet \
    --nsg link-demo-nsg \
    --public-ip-address link-demo-net-ip \
    --image debian \
    --admin-username azureuser \
    --generate-ssh-keys

# install psql database client
az vm run-command invoke \
    --resource-group link-demo \
    --name link-demo-vm \
    --command-id RunShellScript \
    --scripts \
        "sudo touch /home/azureuser/.hushlogin" \
        "sudo DEBIAN_FRONTEND=noninteractive apt-get update" \
        "sudo DEBIAN_FRONTEND=noninteractive apt-get install -q -y postgresql-client"
```

## <a name="create-a-server-group-with-a-private-link"></a>Creación de un grupo de servidores con un vínculo privado

1. Haga clic en **Crear un recurso** en la esquina superior izquierda de Azure Portal.

2. En la página **Nuevo**, seleccione **Bases de datos** y, en la página **Bases de datos**, seleccione **Azure Database for PostgreSQL**.

3. Para la opción de implementación, seleccione el botón **Crear** en **Grupo de servidores de hiperescala (Citus)** .

4. Rellene el formulario del nuevo servidor con la siguiente información:

    - **Grupo de recursos**: `link-demo`
    - **Nombre del grupo de servidores**: `link-demo-sg`
    - **Ubicación**: `East US`.
    - **Contraseña**: (su elección)

    > [!NOTE]
    >
    > El nombre del grupo de servidores debe ser único globalmente en Azure porque crea una entrada DNS. Si `link-demo-sg` no está disponible, elija otro nombre y ajuste los pasos siguientes según sea necesario.

5. Seleccione **Configurar grupo de servidores**, elija el plan **Básico** y seleccione **Guardar**.

6. Seleccione **Siguiente: Redes** en la parte inferior de la página.

7. Seleccione **Acceso privado (versión preliminar)** .

    > [!NOTE]
    >
    > El acceso privado está disponible para la versión preliminar solo en [determinadas regiones](concepts-hyperscale-limits.md#regions).
    >
    > Si la opción de acceso privado no se puede seleccionar para el grupo de servidores aunque el grupo de servidores está dentro de una región permitida, abra una [solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) de Azure e incluya su identificador de suscripción de Azure para obtener acceso.

8. Aparece una pantalla llamada **Crear un punto de conexión privado**. Escriba estos valores y seleccione **Aceptar**:

    - **Grupo de recursos**: `link-demo`
    - **Ubicación**: `(US) East US`.
    - **Nombre**: `link-demo-sg-c-pe1`
    - **Target sub resource (Subrecurso de destino)** : `coordinator`
    - **Red virtual**: `link-demo-net`
    - **Subred**: `link-demo-subnet`
    - **Integrar con la zona DNS privada**: sí

9. Después de crear el punto de conexión privado, seleccione **Revisar y crear** para crear el grupo de servidores de Hiperescala (Citus).

## <a name="access-the-server-group-privately-from-the-virtual-machine"></a>Acceso privado al grupo de servidores desde la máquina virtual

El vínculo privado permite que la máquina virtual se conecte al grupo de servidores e impide que los hosts externos lo hagan. En este paso, comprobaremos que el cliente de la base de datos `psql` de nuestra máquina virtual puede comunicarse con el nodo de coordinación del grupo de servidores.

```sh
# save db URI
#
# obtained from Settings -> Connection Strings in the Azure portal
#
# replace {your_password} in the string with your actual password
PG_URI='host=c.link-demo-sg.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require'

# attempt to connect to server group with psql in the virtual machine
az vm run-command invoke \
    --resource-group link-demo \
    --name link-demo-vm \
    --command-id RunShellScript \
    --scripts "psql '$PG_URI' -c 'SHOW citus.version;'" \
    --query 'value[0].message' \
    | xargs printf
```

Debería ver un número de versión para Citus en la salida. Si es así, psql pudo ejecutar el comando y el vínculo privado funcionó.

## <a name="clean-up-resources"></a>Limpieza de recursos

Hemos visto cómo crear un vínculo privado entre una máquina virtual y un grupo de servidores de Hiperescala (Citus). Ahora podemos desaprovisionar los recursos.

Elimine el grupo de recursos y sus recursos se desaprovisionarán:

```sh
az group delete --resource-group link-demo

# press y to confirm
```

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre el [acceso privado](concepts-hyperscale-private-access.md) (versión preliminar)
* Más información sobre los [puntos de conexión privados](/azure/private-link/private-endpoint-overview)
* Más información sobre las [redes virtuales](/azure/virtual-network/concepts-and-best-practices)
* Más información sobre las [zonas DNS privadas](/azure/dns/private-dns-overview)
