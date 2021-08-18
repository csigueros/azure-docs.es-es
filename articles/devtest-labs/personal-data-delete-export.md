---
title: Exportación y eliminación de datos personales de Azure DevTest Labs
description: Obtenga información sobre cómo eliminar y exportar datos personales desde el servicio de Azure DevLast Labs para cumplir las obligaciones contraídas en virtud del Reglamento general de protección de datos (RGPD).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 21c2187b603794f60f736c89d06983f5784730ce
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282824"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportación y eliminación de datos personales de Azure DevTest Labs
En este artículo se proporcionan los pasos para eliminar y exportar datos personales desde el servicio de Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>¿Qué datos personales recopila DevTest Labs?
DevTest Labs recopila dos elementos principales de los datos personales del usuario: la dirección de correo electrónico del usuario y el id. de objeto de usuario. Esta información es fundamental para que el servicio proporcione características en servicio a los administradores y usuarios del laboratorio.

### <a name="user-email-address"></a>Dirección de correo electrónico del usuario
DevTest Labs usa la dirección de correo electrónico del usuario para enviar las notificaciones de correo electrónico de apagado automático a los usuarios del laboratorio. El correo electrónico notifica a los usuarios que la máquina se va a apagar. Si quieren, los usuarios pueden posponer el apagado u omitirlo. Configure la dirección de correo electrónico en el nivel de laboratorio o en el nivel de la VM.

**Configuración del correo electrónico en el laboratorio:**

![Configuración del correo electrónico en el nivel del laboratorio](./media/personal-data-delete-export/lab-user-email.png)

**Configuración del correo electrónico en la VM:**

![Configuración del correo electrónico en el nivel de la VM](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Id. de objeto de usuario
DevTest Labs usa el id. de objeto de usuario para mostrar las tendencias de costo de mes a mes y el costo por información de recursos para los administradores del laboratorio. Les permite realizar un seguimiento de los costos y administrar los umbrales de su laboratorio. 

**Tendencia de costo estimado para el mes en curso:** 
![Tendencia de costo estimado para el mes en curso](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Costo estimado de valor mensual hasta la fecha por recurso:** 
![Costo estimado de valor mensual hasta la fecha por recurso](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>¿Por qué necesitamos estos datos personales?
El servicio de DevTest Labs usa los datos personales con fines operativos. Estos datos son fundamentales para que el servicio ofrezca las características clave. Si establece una directiva de retención en la dirección de correo electrónico del usuario, los usuarios del laboratorio no recibirán notificaciones oportunas de correo electrónico de apagado automático tras eliminar su dirección de correo electrónico de nuestro sistema. De forma similar, el administrador del laboratorio no podrá ver las tendencias de costos de mes a mes ni el costo por recurso para las máquinas de sus laboratorios si los id. de objeto de usuario se eliminan en función de una directiva de retención. Por lo tanto, estos datos deben conservarse mientras el recurso del usuario esté activo en el laboratorio.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>¿Cómo puedo hacer que el sistema olvide mis datos personales?
Como usuario del laboratorio, si quiere que estos datos personales se eliminen, puede hacerlo mediante la eliminación del recurso correspondiente en el laboratorio. El servicio de DevTest Labs anonimiza los datos personales eliminados 30 después de que el usuario los elimine.

Por ejemplo, si elimina la VM o quita la dirección de correo electrónico, el servicio de DevTest Labs anonimiza estos datos 30 días después de eliminar el recurso. La directiva de retención de 30 días tras la eliminación sirve para garantizar que se proporciona una proyección de costos precisa de mes a mes para el administrador de laboratorio.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>¿Cómo puedo solicitar la exportación de mis datos personales?
Puede exportar datos de uso del laboratorio y personales mediante el Azure Portal o PowerShell. Los datos se exportan como dos archivos CSV diferentes:

- **disks.csv**: contiene información sobre los discos que usan las diferentes máquinas virtuales
- **virtualmachines.csv**: contiene información sobre las máquinas virtuales del laboratorio.

### <a name="azure-portal"></a>Azure Portal
Como usuario del laboratorio, puede solicitar una exportación de los datos personales que almacena el servicio de DevTest Labs. Para solicitar una exportación, vaya a la opción **Datos personales** en la página **Introducción** del laboratorio. Al seleccionar el botón **Solicitar exportación** se inicia la creación de un archivo de Excel descargable en la cuenta de almacenamiento del administrador del laboratorio. A continuación, puede ponerse en contacto con él para ver los datos.

1. Seleccione **Datos personales** en el menú de la izquierda. 

    ![Página de datos personales](./media/personal-data-delete-export/personal-data-page.png)
2. Seleccione el **grupo de recursos** que contiene el laboratorio.

    ![Selección de un grupo de recursos](./media/personal-data-delete-export/select-resource-group.png)
3. Seleccione la **cuenta de almacenamiento** en el grupo de recursos.
4. En la página **Cuenta de almacenamiento**, seleccione **Blobs**.

    ![Selección del icono de blobs](./media/personal-data-delete-export/select-blobs-tile.png)
5. Seleccione el contenedor denominado **labresourceusage** en la lista de contenedores.

    ![Selección del contenedor del blob](./media/personal-data-delete-export/select-blob-container.png)
6. Seleccione la **carpeta** que nombró tras el laboratorio. Encontrará archivos **CSV** para **discos** y **máquinas virtuales** del laboratorio en esta carpeta. Puede descargar estos archivos CSV, filtrar el contenido para el usuario del laboratorio que solicita un acceso y compartirla con él.

    ![Descargar archivo CSV](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell&quot;></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage=&quot;The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

Los componentes clave del ejemplo anterior son:

- El comando Invoke-AzureRmResourceAction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Dos parámetros de acción
    - **blobStorageAbsoluteSasUri**: el URI de la cuenta de almacenamiento con el token de firma de acceso compartido (SAS). En el script de PowerShell, este valor se puede pasar en lugar de la clave de almacenamiento.
    - **usageStartDate**: la fecha de inicio para extraer datos, donde la fecha de finalización es la fecha actual en la que se ejecuta la acción. La granularidad es en el nivel de día, por lo que incluso si agrega información de hora, esta se omitirá.

### <a name="exported-data---a-closer-look"></a>Datos exportados: un examen más profundo
Ahora observemos más de cerca los datos exportados. Como se mencionó anteriormente, una vez que se exportan correctamente los datos, habrá dos archivos CSV. 

El archivo **virtualmachines.csv** contiene las siguientes columnas de datos:

| Nombre de la columna | Descripción |
| ----------- | ----------- | 
| SubscriptionId | Identificador de la suscripción en la que existe el laboratorio. |
| LabUId | Identificador GUID único para el laboratorio. |
| LabName | Nombre del laboratorio. |
| LabResourceId | Identificador de recurso de laboratorio completo. |
| ResourceGroupName | Nombre del grupo de recursos que contiene la máquina virtual. | 
| ResourceId | Identificador de recurso completo de la máquina virtual. |
| ResourceUId | GUID de la máquina virtual. |
| Nombre | Nombre de la máquina virtual. |
| CreatedTime | Fecha y hora a la que se creó la máquina virtual. |
| DeletedDate | Fecha y hora a la que se eliminó la máquina virtual. Si está vacío, la eliminación no se ha producido todavía. |
| ResourceOwner | Propietario de la máquina virtual. Si el valor está vacío, se trata de una máquina virtual reclamable o creada por una entidad de servicio. |
| PricingTier | Plan de tarifa de la máquina virtual. |
| ResourceStatus | Estado de disponibilidad de la máquina virtual. Activo, si todavía existe, o Inactivo, si la máquina virtual se ha eliminado. |
| ComputeResourceId | Identificador del recurso del proceso de máquina virtual completo. |
| Reclamable | Se establece en true si la máquina virtual es una máquina virtual reclamable. | 
| EnvironmentId | El identificador de recursos del entorno en el que se creó la máquina virtual. Está vacío cuando la máquina virtual no se creó como parte de un recurso de entorno. |
| ExpirationDate | Fecha de expiración de la máquina virtual. Se establece en Empty si no se ha establecido una fecha de expiración.
| GalleryImageReferenceVersion |  Versión de la imagen base de la máquina virtual. |
| GalleryImageReferenceOffer | Oferta de la imagen base de la máquina virtual. |
| GalleryImageReferencePublisher | Publicador de la imagen base de la máquina virtual. |
| GalleryImageReferenceSku | SKU de la imagen base de la máquina virtual. |
| GalleryImageReferenceOsType | Tipo de sistema operativo de la imagen base de la máquina virtual. |
| CustomImageId | Identificador completo de la imagen personalizada base de la máquina virtual. |

A continuación se enumeran las columnas de datos incluidas en **disks.csv**:

| Nombre de la columna | Descripción | 
| ----------- | ----------- | 
| SubscriptionId | Identificador de la suscripción que contiene el laboratorio. |
| LabUId | GUID del laboratorio. |
| LabName | Nombre del laboratorio. | 
| LabResourceId | Identificador de recurso completo del laboratorio. | 
| ResourceGroupName | Nombre del grupo de recursos que contiene el laboratorio. | 
| ResourceId | Identificador de recurso completo de la máquina virtual. |
| ResourceUId | GUID de la máquina virtual. |
 |Nombre | El nombre del disco conectado. |
| CreatedTime |Fecha y hora en que se creó el disco de datos. |
| DeletedDate | Fecha y hora en que se eliminó el disco de datos. |
| ResourceStatus | Estado del recurso. Activo, si el recurso existe. Inactivo, si se ha eliminado. |
| DiskBlobName | Nombre del blob para el disco de datos. |
| DiskSizeGB | Tamaño del disco de datos. |
| DiskType | Tipo del disco de datos. 0 para Estándar, 1 para Premium. |
| LeasedByVmId | Identificador de recurso de la máquina virtual a la que se ha conectado el disco de datos. |


> [!NOTE]
> Si trabaja con varios laboratorios y quiere obtener información general, las dos columnas de clave son **LabUID** y **ResourceUId**, que son los identificadores únicos en las suscripciones.

Los datos exportados se pueden manipular y visualizar mediante herramientas, como SQL Server, Power BI, etc. Esta característica es especialmente útil si quiere informar del uso del laboratorio al equipo de administración, que puede que no esté usando la misma suscripción de Azure que usted.

## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes: 

- [Set policies for a lab](devtest-lab-set-lab-policy.md) (Definición de directivas para un laboratorio)
- [Preguntas más frecuentes](devtest-lab-faq.yml)
