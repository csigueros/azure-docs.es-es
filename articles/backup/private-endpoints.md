---
title: Creación y uso de puntos de conexión privados para Azure Backup
description: Comprenda el proceso de creación de puntos de conexión privados para Azure Backup en el que su uso ayuda a preservar la seguridad de los recursos.
ms.topic: conceptual
ms.date: 08/19/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df65aad1247f21c4deda3f7ee71f657a3b288168
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444246"
---
# <a name="create-and-use-private-endpoints-for-azure-backup"></a>Creación y uso de puntos de conexión privados para Azure Backup

En este artículo se ofrece información acerca del proceso de creación de [puntos de conexión privados para Azure Backup](private-endpoints-overview.md) y los escenarios en los que ayudan a preservar la seguridad de los recursos.

## <a name="before-you-start"></a>Antes de comenzar

Asegúrese de haber leído los [requisitos previos](private-endpoints-overview.md#before-you-start) y los [escenarios admitidos](private-endpoints-overview.md#recommended-and-supported-scenarios) antes de continuar con la creación de puntos de conexión privados.

Estos detalles le ayudarán a comprender las limitaciones y condiciones que deben cumplirse antes de crear puntos de conexión privados para los almacenes.

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>Introducción a la creación de puntos de conexión privados para Backup

En las siguientes secciones se tratan los pasos necesarios para crear y usar puntos de conexión privados para Azure Backup dentro de las redes virtuales.

>[!IMPORTANT]
> Se recomienda encarecidamente que siga los pasos en la misma secuencia que se mencionan en este documento. Si no lo hace, puede que el almacén sea incompatible con el uso de puntos de conexión privados y que tenga que reiniciar el proceso con un nuevo almacén.

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

Los puntos de conexión privados para Backup solo se pueden crear para almacenes de Recovery Services que no tengan ningún elemento protegido (o que no hayan intentado protegerse o registrarse en él en el pasado). Por lo tanto, se recomienda crear un nuevo almacén para comenzar. Para más información sobre la creación de un nuevo almacén, consulte [Creación y configuración de un almacén de Recovery Services](backup-create-rs-vault.md).

Consulte [esta sección](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) para aprender a crear un almacén mediante el cliente de Azure Resource Manager. Esto crea un almacén con su identidad administrada ya habilitada.

## <a name="enable-managed-identity-for-your-vault"></a>Habilitar la identidad administrada del almacén

Las identidades administradas permiten al almacén crear y usar puntos de conexión privados. En esta sección se habla sobre cómo habilitar la identidad administrada del almacén.

1. Vaya al almacén de Recovery Services -> **Identidad**.

    ![Cambiar el estado de la identidad a activada](./media/private-endpoints/identity-status-on.png)

1. Cambie el **Estado** a **Activado** y seleccione **Guardar**.

1. Se genera una **Id. de objeto**, que es la identidad administrada del almacén.

    >[!NOTE]
    >Una vez habilitada, la identidad administrada **NO** debe deshabilitarse (ni siquiera temporalmente). Deshabilitar la identidad administrada puede provocar un comportamiento incoherente.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Concesión de permisos al almacén para crear los puntos de conexión privados necesarios

Para crear los puntos de conexión privados necesarios para Azure Backup, el almacén (la identidad administrada del almacén) debe tener permisos para los siguientes grupos de recursos:

- El grupo de recursos que contiene la red virtual de destino
- El grupo de recursos donde se crearán los puntos de conexión privados
- El grupo de recursos que contiene las zonas DNS privadas, como se explica [aquí](#create-private-endpoints-for-azure-backup) de forma detallada.

Se recomienda conceder el rol **Colaborador** para estos tres grupos de recursos en el almacén (identidad administrada). En los pasos siguientes se describe cómo hacer esto para un determinado grupo de recursos (esto se debe hacer para cada uno de los tres grupos de recursos):

1. Vaya al grupo de recursos y navegue a **Control de acceso (IAM)** en la barra de la izquierda.
1. Una vez en **Control de acceso**, vaya a **Agregar una asignación de roles**.

    ![Adición de una asignación de roles](./media/private-endpoints/add-role-assignment.png)

1. En el panel de **Agregar asignación de roles**, seleccione **Colaborador** como **Rol** y use el **Nombre** del almacén como **Entidad de seguridad**. Seleccione el almacén y, cuando haya terminado, seleccione **Guardar**.

    ![Elegir rol y entidad de seguridad](./media/private-endpoints/choose-role-and-principal.png)

Para administrar los permisos de forma más detallada, consulte [Crear roles y permisos manualmente](#create-roles-and-permissions-manually).

## <a name="create-private-endpoints-for-azure-backup"></a>Creación de puntos de conexión privados para Azure Backup

En esta sección se explica cómo crear un punto de conexión privado para el almacén.

1. Navegue hasta el almacén creado anteriormente y vaya a **Conexiones de punto de conexión privado** en la barra de navegación izquierda. Seleccione **+ Punto de conexión privado** en la parte superior para comenzar la creación de un nuevo punto de conexión privado para este almacén.

    ![Creación de un punto de conexión privado](./media/private-endpoints/new-private-endpoint.png)

1. Una vez que empiece el proceso para **Crear un punto de conexión privado**, se le pedirá que especifique los detalles para crear la conexión de punto de conexión privado.
  
    1. **Aspectos básicos**: Rellene los detalles básicos de los puntos de conexión privados. La región debe ser la misma que la del almacén y el recurso de los que se hará una copia de seguridad.

        ![Rellene los detalles básicos](./media/private-endpoints/basics-tab.png)

    1. **Recursos**: esta pestaña requiere que seleccione el recurso de PaaS para el que quiere crear la conexión. Seleccione **Microsoft.RecoveryServices/vaults** del tipo de recurso para la suscripción deseada. Una vez hecho esto, elija el nombre del almacén de Recovery Services como **Recurso** y **AzureBackup** como el **Subrecurso de destino**.

        ![Selección del recurso para la conexión](./media/private-endpoints/resource-tab.png)

    1. **Configuración**: En Configuración, especifique la red virtual y la subred en la que desea que se cree el punto de conexión privado. Esta será la red virtual donde está presente la máquina virtual.

        Para conectarse de manera privada, necesita los registros DNS necesarios. Según la configuración de la red, puede elegir una de las siguientes opciones:

          - Integrar el punto de conexión privado con una zona DNS privada: seleccione **Sí** si quiere realizar la integración.
          - Usar el servidor DNS personalizado: seleccione **No** si quiere usar su propio servidor DNS.

        La administración de registros DNS para ambas opciones se [describe más adelante](#manage-dns-records).

          ![Especificación de la red virtual y la subred](./media/private-endpoints/configuration-tab.png)

    1. Opcionalmente, puede agregar **Etiquetas** para el punto de conexión privado.
    1. Continúe para **Revisar y crear** una vez que haya terminado de escribir los detalles. Una vez finalizada la validación, seleccione **Crear** para crear el punto de conexión privado.

## <a name="approve-private-endpoints"></a>Aprobación de puntos de conexión privados

Si el usuario que crea el punto de conexión privado también es el propietario del almacén de Recovery Services, el punto de conexión privado creado anteriormente se aprobará automáticamente. De lo contrario, el propietario del almacén debe aprobar el punto de conexión privado antes de poder usarlo. En esta sección se describe la aprobación manual de los puntos de conexión privados a través de Azure Portal.

Consulte la sección [Aprobación manual de los puntos de conexión privados mediante el cliente de Azure Resource Manager](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) para usar el cliente de Azure Resource Manager para la aprobación de puntos de conexión privados.

1. En el almacén de Recovery Services, vaya a **Conexiones de punto de conexión privado** en la barra de la izquierda.
1. Seleccione la conexión de punto de conexión privado que desea aprobar.
1. Seleccione **Aprobar** en la barra superior. También puede seleccionar **Rechazar** o **Quitar** si desea rechazar o eliminar la conexión del punto de conexión.

    ![Aprobación de puntos de conexión privados](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>Administración de registros DNS

Tal y como se ha descrito anteriormente, necesita los registros DNS necesarios en sus servidores o zonas DNS privados para conectarse de manera privada. Puede integrar su punto de conexión privado directamente con zonas DNS privadas de Azure, o usar los servidores DNS personalizados para ello, en función de las preferencias de red. Tendrá que realizar este procedimiento con los tres servicios: Backup, blobs y colas.

Además, si el servidor o la zona DNS están presentes en una suscripción diferente de la que contiene el punto de conexión privado, consulte también [Creación de entradas DNS cuando la zona DNS o servidor DNS está presente en otra suscripción](#create-dns-entries-when-the-dns-serverdns-zone-is-present-in-another-subscription). 

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>Durante la integración de puntos de conexión privados con las zonas DNS privadas de Azure

Si decide integrar su punto de conexión privado con zonas DNS privadas, Backup agregará los registros DNS necesarios. Puede ver las zonas DNS privadas que se están usando en la **Configuración DNS** del punto de conexión privado. Si estas zonas DNS no están presentes, se crearán automáticamente al crear el punto de conexión privado. Sin embargo, debe comprobar que la red virtual (que contiene los recursos de los que se va a realizar la copia de seguridad) esté vinculada correctamente a las tres zonas DNS privadas, como se describe a continuación.

![Configuración DNS en la zona DNS privada de Azure](./media/private-endpoints/dns-configuration.png)

#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>Validación de vínculos de red virtual en zonas DNS privadas

Haga lo siguiente para **cada zona DNS privada** indicada anteriormente (para Backup, blobs y colas):

1. Vaya a la opción **Vínculos de red virtual** correspondiente en la barra de navegación izquierda.
1. Debe ver una entrada para la red virtual para la que ha creado el punto de conexión privado, como se muestra a continuación:

    ![Red virtual para el punto de conexión privado](./media/private-endpoints/virtual-network-links.png)

1. Si no ve ninguna entrada, agregue un vínculo de red virtual a todas las zonas DNS que no tengan una.

    ![Agregar el vínculo de red virtual](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>Durante el uso de un servidor DNS personalizado o archivos de host

Si usa servidores DNS personalizados, deberá crear las zonas DNS necesarias y agregar los registros DNS necesarios para los puntos de conexión privados a los servidores DNS. En el caso de los blobs y las colas, también puede usar reenviadores condicionales.

#### <a name="for-the-backup-service"></a>En el caso del servicio de Backup

1. En el servidor DNS, cree una zona DNS para Backup según la siguiente convención de nomenclatura:

    |Zona |Servicio |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  Copia de seguridad        |

    >[!NOTE]
    > En el texto anterior, `<geo>` hace referencia al código de región (por ejemplo, *eus* y *ne* para Este de EE. UU. y Norte de Europa respectivamente). Consulte las siguientes listas para ver los códigos de región:
    >
    > - [Todas las nubes públicas](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [China](/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [Alemania](../germany/germany-developer-guide.md#endpoint-mapping)
    > - [US Gov](../azure-government/documentation-government-developer-guide.md)

1. A continuación, es necesario agregar los registros DNS necesarios. Para ver los registros que deben agregarse a la zona DNS de Backup, navegue hasta el punto de conexión privado que creó anteriormente y vaya a la opción **Configuración DNS** en la barra de navegación izquierda.

    ![Configuración DNS para el servidor DNS personalizado](./media/private-endpoints/custom-dns-configuration.png)

1. Agregue una entrada para cada FQDN e IP que se muestra como registros de tipo D en la zona DNS para Backup. Si usa un archivo de host para la resolución de nombres, realice las entradas correspondientes en el archivo de host para cada IP y FQDN según el formato siguiente:

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>Como se muestra en la captura de pantalla anterior, los FQDN se describen como `xxxxxxxx.<geo>.backup.windowsazure.com` y no como `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com`. En tales casos, asegúrese de incluir (y, si es necesario, agregar) el elemento `.privatelink.` según el formato indicado.

#### <a name="for-blob-and-queue-services"></a>En el caso de los servicios de blob y cola

En el caso de los blobs y las colas, puede usar reenviadores condicionales o crear zonas DNS en el servidor DNS.

##### <a name="if-using-conditional-forwarders"></a>Si usa reenviadores condicionales

Si usa reenviadores condicionales, agregue los reenviadores para los FQDN de blobs y colas de la siguiente manera:

|FQDN  |IP  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>Si usa zonas DNS privadas

Si usa zonas DNS para blobs y colas, primero deberá crear estas zonas DNS y agregar después los registros D necesarios.

|Zona |Servicio  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  Blob     |
|`privatelink.queue.core.windows.net`     | Cola        |

En este momento, solo crearemos las zonas para los blobs y las colas al usar servidores DNS personalizados. La adición de registros DNS se realizará más adelante en dos pasos:

1. Cuando se registra la primera instancia de copia de seguridad; es decir, cuando se configura la copia de seguridad por primera vez.
1. Cuando se ejecuta la primera copia de seguridad.

Realizaremos estos pasos en las secciones siguientes.

## <a name="use-private-endpoints-for-backup"></a>Uso de puntos de conexión privados para Backup

Una vez que los puntos de conexión privados creados para el almacén de la red virtual se han aprobado, puede empezar a usarlos para realizar las copias de seguridad y restauraciones.

>[!IMPORTANT]
>Asegúrese de haber completado correctamente todos los pasos mencionados previamente en el documento antes de continuar. En resumen, debe haber completado los pasos de la siguiente lista:
>
>1. Se creó un (nuevo) almacén de Recovery Services
>2. Se habilitó el almacén para usar la identidad administrada asignada por el sistema
>3. Se asignaron los permisos relevantes a la identidad administrada del almacén
>4. Se creó un punto de conexión privado para el almacén
>5. Se aprobó el punto de conexión privado (si no se aprobó automáticamente)
>6. Se garantizó que todos los registros DNS se agregaron correctamente (excepto los registros de blobs y colas para los servidores personalizados, que se tratarán en las secciones siguientes).

### <a name="check-vm-connectivity"></a>Comprobación de la conectividad de VM

En la VM de la red bloqueada, asegúrese de cumplir con lo siguiente:

1. La VM debe tener acceso a AAD.
2. Ejecute **nslookup** en la dirección URL de copia de seguridad (`xxxxxxxx.privatelink.<geo>.backup. windowsazure.com`) de la VM para confirmar la conectividad. Esto debe devolver la dirección IP privada asignada a la red virtual.

### <a name="configure-backup"></a>Configuración de la copia de seguridad

Una vez que confirme que se completó correctamente la lista de comprobación anterior y el acceso, puede seguir configurando la copia de seguridad de las cargas de trabajo en el almacén. Si usa un servidor DNS personalizado, deberá agregar entradas DNS para los blobs y las colas que están disponibles después de configurar la primera copia de seguridad.

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>Registros DNS para blobs y colas (solo para servidores DNS o archivos de host personalizados) después del primer registro

Después de configurar la copia de seguridad para al menos un recurso en un almacén habilitado para puntos de conexión privados, agregue los registros DNS necesarios para los blobs y las colas tal como se describe a continuación.

1. Navegue hasta el grupo de recursos y busque el punto de conexión privado que creó.
1. Aparte del nombre de punto de conexión privado que asignó, verá que se crearon otros dos puntos de conexión privados. Estos comienzan con `<the name of the private endpoint>_ecs` y tienen el sufijo `_blob` y `_queue` respectivamente.

    ![Recursos de punto de conexión privado](./media/private-endpoints/private-endpoint-resources.png)

1. Navegue a cada uno de estos puntos de conexión privados. En la opción de configuración DNS de cada uno de los dos puntos de conexión privados, verá un registro con un FQDN y una dirección IP. Agregue ambos valores al servidor DNS personalizado, además de los descritos anteriormente.
Si usa un archivo de host, realice las entradas correspondientes en el archivo de host para cada IP o FQDN según el formato siguiente:

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![Configuración DNS de blob](./media/private-endpoints/blob-dns-configuration.png)

Además de lo anterior, es necesaria otra entrada después de la primera copia de seguridad, que se describe [más adelante](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup).

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Copia de seguridad y restauración de cargas de trabajo en VM de Azure (SQL y SAP HANA)

Una vez que se crea y se aprueba el punto de conexión privado, no se requiere ningún otro cambio del lado cliente para usar el punto de conexión privado (a menos que esté usando grupos de disponibilidad de SQL, que se describen más adelante en esta sección). Toda la comunicación y la transferencia de datos desde la red segura al almacén se realizará a través del punto de conexión privado. Sin embargo, si quita los puntos de conexión privados del almacén después de haber registrado un servidor (SQL o SAP HANA), deberá volver a registrar el contenedor con el almacén. No es necesario detener la protección de los mismos.

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>Registros DNS para blobs (solo para servidores DNS o archivos de host personalizados) después de la primera copia de seguridad

Después de ejecutar la primera copia de seguridad y de usar un servidor DNS personalizado (sin reenvío condicional), es probable que se produzca un error en la copia de seguridad. Si eso ocurre, haga lo siguiente:

1. Navegue hasta el grupo de recursos y busque el punto de conexión privado que creó.
1. Además de los tres puntos de conexión privados descritos anteriormente, ahora verá un cuarto punto de conexión privado cuyo nombre empieza por `<the name of the private endpoint>_prot` y contiene el sufijo `_blob`.

    ![Punto de conexión privado con el sufijo "prot"](./media/private-endpoints/private-endpoint-prot.png)

1. Navegue hasta este nuevo punto de conexión privado. En la opción Configuración DNS, verá un registro con un FQDN y una dirección IP. Agregue los valores al servidor DNS privado, además de los descritos anteriormente.

    Si usa un archivo de host, realice las entradas correspondientes en el archivo de host para cada IP y FQDN según el formato siguiente:

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>En este momento, debe poder ejecutar **nslookup** desde la VM y resolver las direcciones IP privadas cuando se realice en las direcciones URL de almacenamiento y copia de seguridad del almacén.

### <a name="when-using-sql-availability-groups"></a>Durante el uso de grupos de disponibilidad de SQL

Cuando use grupos de disponibilidad de SQL, deberá aprovisionar el reenvío condicional en el DNS de grupo de disponibilidad personalizado como se describe a continuación:

1. Inicie sesión en su controlador de dominio.
1. En la aplicación DNS, agregue reenviadores condicionales para las tres zonas DNS (Backup, blobs y colas) a la dirección IP 168.63.129.16 o la dirección IP del servidor DNS personalizado, según sea necesario. Las siguientes capturas de pantallas se muestran cuando se reenvía a la dirección IP del host de Azure. Si usa su propio servidor DNS, reemplácelo por la dirección IP del servidor DNS.

    ![Reenviadores condicionales en el administrador de DNS](./media/private-endpoints/dns-manager.png)

    ![Nuevo reenviador condicional](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>Copia de seguridad y restauración mediante el agente de MARS

Al usar el agente de MARS para realizar una copia de seguridad de los recursos locales, asegúrese de que la red local (que contiene los recursos de los que se va a realizar la copia de seguridad) está emparejada con la red virtual de Azure que contiene un punto de conexión privado para el almacén, para que pueda usarla. Después, puede continuar con la instalación del agente de MARS y configurar la copia de seguridad como se detalla aquí. Sin embargo, debe asegurarse de que toda la comunicación para la copia de seguridad se produzca solo a través de la red emparejada.

Pero si quita los puntos de conexión privados del almacén después de haber registrado un agente de MARS, deberá volver a registrar el contenedor con el almacén. No es necesario detener la protección de los mismos.

## <a name="deleting-private-endpoints"></a>Eliminación de puntos de conexión privados

Consulte [esta sección](/rest/api/virtualnetwork/privateendpoints/delete) para obtener información sobre cómo eliminar puntos de conexión privados.

## <a name="additional-topics"></a>Otros temas

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Creación de un almacén de Recovery Services mediante el cliente de Azure Resource Manager

Puede crear el almacén de Recovery Services y habilitar su identidad administrada (se requiere la habilitación de la identidad administrada, como veremos más adelante) mediante el cliente de Azure Resource Manager. A continuación, se muestra un ejemplo de cómo hacerlo:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

El archivo JSON anterior debe tener el siguiente contenido:

Solicitud JSON:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Respuesta JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>El almacén creado en este ejemplo mediante Azure Resource Manager ya se ha creado con una identidad administrada asignada por el sistema.

### <a name="managing-permissions-on-resource-groups"></a>Administración de permisos en grupos de recursos

La identidad administrada del almacén debe tener los permisos siguientes en el grupo de recursos y la red virtual donde se crearán los puntos de conexión privados:

- `Microsoft.Network/privateEndpoints/*` Esto es necesario para realizar operaciones CRUD en puntos de conexión privados en el grupo de recursos. Debe asignarse en el grupo de recursos.
- `Microsoft.Network/virtualNetworks/subnets/join/action` Esto es necesario en la red virtual en la que la dirección IP privada se conecta con el punto de conexión privado.
- `Microsoft.Network/networkInterfaces/read` Esto es necesario en el grupo de recursos para que se cree la interfaz de red para el punto de conexión privado.
- Rol de colaborador de zona DNS privada: este rol ya existe y se puede usar para proporcionar los permisos `Microsoft.Network/privateDnsZones/A/*` y `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read`.

Puede usar uno de los métodos siguientes para crear roles con los permisos necesarios:

#### <a name="create-roles-and-permissions-manually"></a>Crear roles y permisos manualmente

Cree los siguientes archivos JSON y use el comando de PowerShell que se encuentra al final de la sección para crear roles:

//PrivateEndpointContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//NetworkInterfaceReaderRoleDef.json

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//PrivateEndpointSubnetContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Usar un script

1. Inicie **Cloud Shell** en Azure Portal y seleccione **Cargar archivo** en la ventana de PowerShell.

    ![Seleccionar Cargar archivo en la ventana de PowerShell](./media/private-endpoints/upload-file-in-powershell.png)

1. Cargue el siguiente script: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Vaya a la carpeta principal (por ejemplo: `cd /home/user`)

1. Ejecute el siguiente script:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Estos son los parámetros:

    - **subscription**: **SubscriptionId que tiene el grupo de recursos en el que se va a crear el punto de conexión privado para el almacén y la subred en la que se adjuntará el punto de conexión privado del almacén

    - **vaultPEResourceGroup**: El grupo de recursos donde se creará el punto de conexión privado para el almacén

    - **vaultPESubnetResourceGroup**: El grupo de recursos de la subred a la que se unirá el punto de conexión privado

    - **vaultMsiName**: Nombre del archivo MSI del almacén, que es igual que **VaultName**

1. Complete la autenticación y el script tomará el contexto de la suscripción especificada que se indicó anteriormente. Se crearán los roles adecuados si faltan en el inquilino y se asignarán roles al archivo MSI del almacén.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Creación de puntos de conexión privados mediante Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Puntos de conexión privados aprobados automáticamente

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  

$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $VMResourceGroupName
$subnet = $vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq '<subnetName>'}


$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Aprobación manual de los puntos de conexión privados mediante el cliente de Azure Resource Manager

1. Use **GetVault** para obtener el id. de conexión de punto de conexión privado para el punto de conexión privado.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Esto devolverá el identificador de conexión del punto de conexión privado. El nombre de la conexión se puede recuperar utilizando la primera parte del identificador de conexión como se indica a continuación:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Obtenga el **identificador de conexión del punto de conexión privado** (y el **nombre del punto de conexión privado**, donde sea necesario) de la respuesta y reemplácelo en el siguiente JSON y el URI de Azure Resource Manager e intente cambiar el estado a "Aprobado/Rechazado/Desconectado", como se muestra en el ejemplo siguiente:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON:

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="set-up-proxy-server-for-recovery-services-vault-with-private-endpoint"></a>Configuración del servidor proxy para el almacén de Recovery Services con un punto de conexión privado

Para configurar un servidor proxy para una máquina virtual de Azure o una máquina local, siga estos pasos:

1. Agregue los siguientes dominios en la excepción y omita el servidor proxy.
   
   | Servicio | Nombres de dominio | Port |
   | ------- | ------ | ---- |
   | Azure Backup | *.backup.windowsazure.com | 443 |
   | Azure Storage | *.blob.core.windows.net <br><br> *.queue.core.windows.net <br><br> *.blob.storage.azure.net | 443 |
   | Azure Active Directory <br><br> Direcciones URL de dominio actualizadas mencionadas en las secciones 56 y 59 de [Microsoft 365 Common y Office Online](/microsoft-365/enterprise/urls-and-ip-address-ranges?view=o365-worldwide&preserve-view=true#microsoft-365-common-and-office-online). | *.msftidentity.com, *.msidentity.com, account.activedirectory.windowsazure.com, accounts.accesscontrol.windows.net, adminwebservice.microsoftonline.com, api.passwordreset.microsoftonline.com, autologon.microsoftazuread-sso.com, becws.microsoftonline.com, clientconfig.microsoftonline-p.net, companymanager.microsoftonline.com, device.login.microsoftonline.com, graph.microsoft.com, graph.windows.net, login.microsoft.com, login.microsoftonline.com, login.microsoftonline-p.com, login.windows.net, logincert.microsoftonline.com, loginex.microsoftonline.com, login-us.microsoftonline.com, nexus.microsoftonline-p.com, passwordreset.microsoftonline.com, provisioningapi.microsoftonline.com <br><br> 20.190.128.0/18, 40.126.0.0/18, 2603:1006:2000::/48, 2603:1007:200::/48, 2603:1016:1400::/48, 2603:1017::/48, 2603:1026:3000::/48, 2603:1027:1::/48, 2603:1036:3000::/48, 2603:1037:1::/48, 2603:1046:2000::/48, 2603:1047:1::/48, 2603:1056:2000::/48, 2603:1057:2::/48 <br><br> *.hip.live.com, *.microsoftonline.com, *.microsoftonline-p.com, *.msauth.net, *.msauthimages.net, *.msecnd.net, *.msftauth.net, *.msftauthimages.net, *.phonefactor.net, enterpriseregistration.windows.net, management.azure.com, policykeyservice.dc.ad.msft.net | Según corresponda. |

1. Permita el acceso a estos dominios en el servidor proxy y vincule la zona DNS privada (`*.privatelink.<geo>.backup.windowsazure.com`, `*.privatelink.blob.core.windows.net`, `*.privatelink.queue.core.windows.net`) con la red virtual donde se crea el servidor proxy o use un servidor DNS personalizado con las entradas DNS correspondientes. <br><br> La red virtual en la que se ejecuta el servidor proxy y la red virtual donde se crea la NIC del punto de conexión privado deben estar emparejadas, lo que permitiría al servidor proxy redirigir las solicitudes a la dirección IP privada. 

En el diagrama siguiente se muestra una configuración con un servidor proxy cuya red virtual está vinculada a una zona DNS privada con las entradas DNS necesarias. El servidor proxy también puede tener su propio servidor DNS personalizado y los dominios señalados anteriormente se pueden reenviar de forma condicional a 169.63.129.16.

:::image type="content" source="./media/private-endpoints/setup-with-proxy-server-inline.png" alt-text="Diagrama que muestra una configuración con un servidor proxy." lightbox="./media/private-endpoints/setup-with-proxy-server-expanded.png":::

### <a name="create-dns-entries-when-the-dns-serverdns-zone-is-present-in-another-subscription"></a>Creación de entradas DNS cuando la zona DNS/servidor DNS está presente en otra suscripción

En esta sección, trataremos los casos en los que se usa una zona DNS que está presente en una suscripción o un grupo de recursos que es diferente del que contiene el punto de conexión privado para el almacén de Recovery Services, como una topología en estrella tipo hub-and-spoke. Como la identidad administrada que se usa para crear los puntos de conexión privados (y las entradas DNS) solo tiene permisos en el grupo de recursos en el que se crean los puntos de conexión privados, se requieren también las entradas DNS necesarias. Use los siguientes scripts de PowerShell para crear entradas DNS.
  
>[!Note]
>Consulte todo el proceso que se describe a continuación para lograr los resultados necesarios. El proceso debe repetirse dos veces: una vez durante la primera detección (para crear entradas DNS necesarias para las cuentas de almacenamiento de comunicación) y, después, una vez durante la primera copia de seguridad (para crear entradas DNS necesarias para las cuentas de almacenamiento de back-end).

#### <a name="step-1-get-required-dns-entries"></a>Paso 1: Obtención de las entradas DNS necesarias

Use el script [PrivateIP.ps1](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/PrivateIP.ps1) para enumerar todas las entradas DNS que deben crearse.

>[!Note]
>En la sintaxis siguiente, el valor de `subscription` hace referencia a la suscripción donde se va a crear el punto de conexión privado del almacén.

**Sintaxis para usar el script**

```azurepowershell
./PrivateIP.ps1 -Subscription "<VaultPrivateEndpointSubscriptionId>" -VaultPrivateEndpointName "<vaultPrivateEndpointName>" -VaultPrivateEndpointRGName <vaultPrivateEndpointRGName> -DNSRecordListFile dnsentries.txt
```

**Salida de ejemplo**

```
ResourceName                                                                 DNS                                                                       PrivateIP
<vaultId>-ab-pod01-fc1         privatelink.eus.backup.windowsazure.com         10.12.0.15
<vaultId>-ab-pod01-fab1        privatelink.eus.backup.windowsazure.com         10.12.0.16
<vaultId>-ab-pod01-prot1       privatelink.eus.backup.windowsazure.com         10.12.0.17
<vaultId>-ab-pod01-rec2        privatelink.eus.backup.windowsazure.com         10.12.0.18
<vaultId>-ab-pod01-ecs1        privatelink.eus.backup.windowsazure.com         10.12.0.19
<vaultId>-ab-pod01-id1         privatelink.eus.backup.windowsazure.com         10.12.0.20
<vaultId>-ab-pod01-tel1        privatelink.eus.backup.windowsazure.com         10.12.0.21
<vaultId>-ab-pod01-wbcm1       privatelink.eus.backup.windowsazure.com         10.12.0.22
abcdeypod01ecs114        privatelink.blob.core.windows.net       10.12.0.23
abcdeypod01ecs114        privatelink.queue.core.windows.net      10.12.0.24
abcdeypod01prot120       privatelink.blob.core.windows.net       10.12.0.28
abcdeypod01prot121       privatelink.blob.core.windows.net       10.12.0.32
abcdepod01prot110       privatelink.blob.core.windows.net       10.12.0.36
abcdeypod01prot121       privatelink.blob.core.windows.net       10.12.0.30
abcdeypod01prot122       privatelink.blob.core.windows.net       10.12.0.34
abcdepod01prot120       privatelink.blob.core.windows.net       10.12.0.26

```

#### <a name="step-2-create--dns-entries"></a>Paso 2: Creación de entradas DNS

Cree las entradas DNS correspondientes a las anteriores. En función del tipo de DNS que use, tiene dos alternativas para crear entradas DNS.

**Caso 1**: si usa un servidor DNS personalizado, debe crear manualmente las entradas para cada registro del script anterior y comprobar que el FQDN (ResourceName.DNS) se resuelve en una dirección IP privada de la red virtual.

**Caso 2**: si usa una zona DNS privada de Azure, puede usar el script [CreateDNSEntries.ps1](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/CreateDNSEntries.ps1) para crear automáticamente entradas DNS en la zona DNS privada. En la sintaxis siguiente, `subscription` es donde existe la zona DNS privada.

**Sintaxis para usar el script**

```azurepowershell
/CreateDNSEntries.ps1 -Subscription <PrivateDNSZoneSubId> -DNSResourceGroup <PrivateDNSZoneRG> -DNSRecordListFile dnsentries.txt
```

#### <a name="summary-of-the-entire-process"></a>Resumen de todo el proceso

Para configurar correctamente el punto de conexión privado para RSV mediante esta solución alternativa, debe hacer lo siguiente:

1. Cree un punto de conexión privado para el almacén (como se describió anteriormente en el artículo).
1. Desencadene la detección. La detección de SQL/HANA producirá un error con _UserErrorVMInternetConnectivityIssue_, porque las entradas DNS no están presentes para la cuenta de almacenamiento de comunicación.
1. Ejecute los scripts para obtener las entradas DNS y cree las entradas DNS correspondientes para la cuenta de almacenamiento de comunicación mencionada anteriormente en esta sección.
1. Vuelva a desencadenar la detección. Esta vez, la detección debe realizarse correctamente.
1. Desencadene la copia de seguridad. La copia de seguridad de SQL/HANA y MARS podría producir un error, porque las entradas DNS no están presentes para las cuentas de almacenamiento de back-end, como se mencionó anteriormente en esta sección.
1. Ejecute los scripts para crear entradas DNS para la cuenta de almacenamiento de back-end.
1. Vuelva a desencadenar la copia de seguridad. Esta vez, las copias de seguridad deben realizarse correctamente.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="can-i-create-a-private-endpoint-for-an-existing-backup-vaultbr"></a>¿Puedo crear un punto de conexión privado para un almacén de Azure Backup existente?<br>

No, los puntos de conexión privados solo se pueden crear para nuevos almacenes de Azure Backup. Por lo tanto, el almacén no debe haber tenido ningún elemento protegido. De hecho, no se puede realizar ningún intento de proteger ningún elemento en el almacén antes de crear puntos de conexión privados.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-create-private-endpoints-for-this-vaultbr"></a>Intenté proteger un elemento en el almacén, pero se produjo un error y el almacén todavía no contiene elementos protegidos. ¿Se pueden crear puntos de conexión privados para este almacén?<br>

No, el almacén no debe haber tenido ningún intento de proteger ningún elemento en el pasado.

### <a name="i-have-a-vault-thats-using-private-endpoints-for-backup-and-restore-can-i-later-add-or-remove-private-endpoints-for-this-vault-even-if-i-have-backup-items-protected-to-itbr"></a>Tengo un almacén que utiliza puntos de conexión privados con fines de copias de seguridad y restauración. ¿Puedo agregar o quitar puntos de conexión privados para este almacén más tarde, aunque tenga elementos de copia de seguridad protegidos?<br>

Sí. Si ya creó puntos de conexión privados para un almacén y hay elementos de copia de seguridad protegidos en él, más adelante podrá agregar o quitar puntos de conexión privados según sea necesario.

### <a name="can-the-private-endpoint-for-azure-backup-also-be-used-for-azure-site-recoverybr"></a>¿Se puede usar también el punto de conexión privado de Azure Backup en Azure Site Recovery?<br>

No, el punto de conexión privado para Azure Backup solo se puede usar en Azure Backup. Tendrá que crear un nuevo punto de conexión privado para Azure Site Recovery, si es compatible con el servicio.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-private-endpointsbr"></a>Me faltó uno de los pasos de este artículo y procedí a proteger el origen de datos. ¿Puedo seguir usando puntos de conexión privados?<br>

No seguir los pasos del artículo y proceder con la protección de elementos puede provocar que el almacén no pueda usar puntos de conexión privados. Por lo tanto, se recomienda que consulte esta lista de comprobación antes de continuar con la protección de los elementos.

### <a name="can-i-use-my-own-dns-server-instead-of-using-the-azure-private-dns-zone-or-an-integrated-private-dns-zonebr"></a>¿Puedo usar mi propio servidor DNS en lugar de usar la zona DNS privada de Azure o una zona DNS privada integrada?<br>

Sí, puede usar sus propios servidores DNS. Sin embargo, asegúrese de agregar todos los registros DNS necesarios como se sugiere en esta sección.

### <a name="do-i-need-to-perform-any-additional-steps-on-my-server-after-ive-followed-the-process-in-this-articlebr"></a>¿Es necesario realizar algún paso adicional en el servidor después de haber seguido el proceso detallado en este artículo?<br>

Después de seguir el proceso detallado en este artículo, no es necesario realizar ningún trabajo adicional para usar puntos de conexión privados para copias de seguridad y restauración.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre todas las [características de seguridad de Azure Backup](security-overview.md).
