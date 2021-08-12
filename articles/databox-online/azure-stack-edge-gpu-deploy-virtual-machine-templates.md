---
title: Implementación de máquinas virtuales en el dispositivo Azure Stack Edge Pro mediante plantillas
description: Describe cómo crear y administrar máquinas virtuales en un dispositivo Azure Stack Edge Pro mediante plantillas.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ef505a5215f4ce7b6e324bd9899f13dafc7a7395
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110493553"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>Implementación de máquinas virtuales en el dispositivo Azure Stack Edge Pro con GPU mediante plantillas

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este tutorial se describe cómo crear y administrar una máquina virtual en el dispositivo Azure Stack Edge Pro mediante plantillas. Estas plantillas son archivos JSON (notación de objetos JavaScript) que definen tanto la infraestructura como la configuración de una máquina virtual. En las plantillas se especifican los recursos que se van a implementar y las propiedades de esos recursos.

Las plantillas son flexibles en entornos diferentes, ya que pueden tomar parámetros como entrada en tiempo de ejecución desde un archivo. La estructura de nomenclatura estándar es `TemplateName.json` para la plantilla y `TemplateName.parameters.json` para el archivo de parámetros. Para obtener más información sobre las plantillas de Resource Manager, vaya a [¿Qué son las plantillas de Resource Manager?](../azure-resource-manager/templates/overview.md)

En este tutorial, usaremos plantillas de ejemplo ya escritas para crear recursos. No necesitará editar el archivo de plantilla y solo podrá modificar los archivos `.parameters.json` para personalizar la implementación en su máquina. 

## <a name="vm-deployment-workflow"></a>Flujo de trabajo de implementación de una máquina virtual

Para implementar máquinas virtuales de Azure Stack Edge Pro en muchos dispositivos, puede usar un solo disco duro virtual preparado con sysprep para todos los dispositivos, la misma plantilla para la implementación y realizar únicamente pequeños cambios en los parámetros de la plantilla para cada ubicación de implementación (estos cambios podrían hacerse de forma manual, como explicamos aquí, o mediante programación). 

El resumen general del flujo de trabajo de implementación mediante plantillas es el siguiente:

1. **Configurar los requisitos previos**. Hay 3 tipos de requisitos previos: del dispositivo, del cliente y de la máquina virtual.

    1. **Requisitos previos del dispositivo**

        1. [Conéctese a Azure Resource Manager](azure-stack-edge-gpu-connect-resource-manager.md) en el dispositivo.
        2. Habilite el proceso a través de la interfaz de usuario local.

    2. **Requisitos previos del cliente**

        1. Descargue las plantillas de máquina virtual y los archivos asociados en el cliente.
        1. Opcionalmente, configure TLS 1.2 en el cliente.
        1. [Descargue e instale el Explorador de Microsoft Azure Storage](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) en el cliente.

    3. **Requisitos previos de la máquina virtual**

        1. Cree un grupo de recursos en la ubicación del dispositivo que contendrá todos los recursos de la máquina virtual.
        1. Cree una cuenta de almacenamiento para cargar el disco duro virtual (VHD) que se usa para crear la imagen de la máquina virtual.
        1. Agregue el identificador URI de la cuenta de almacenamiento local al archivo DNS o hosts del cliente que tiene acceso al dispositivo.
        1. Instale el certificado de almacenamiento de blobs en el dispositivo y en el cliente local que accede al dispositivo. Opcionalmente, instale el certificado de almacenamiento de blobs en el Explorador de Storage.
        1. Cree y cargue un disco duro virtual en la cuenta de almacenamiento creada anteriormente.

2. **Crear una máquina virtual a partir de plantillas**

    1. Cree una imagen de máquina virtual con el archivo de parámetros `CreateImage.parameters.json` y la plantilla de implementación `CreateImage.json`.
    1. Cree una máquina virtual con los recursos creados anteriormente mediante el archivo de parámetros `CreateVM.parameters.json` y la plantilla de implementación `CreateVM.json`.

## <a name="device-prerequisites"></a>Requisitos previos del dispositivo

Configure estos requisitos previos en el dispositivo Azure Stack Edge Pro.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Requisitos previos del cliente

Configure estos requisitos previos en el cliente que se usará para acceder al dispositivo Azure Stack Edge Pro.

1. [Descargue el Explorador de Storage](https://azure.microsoft.com/features/storage-explorer/) si lo usa para cargar un disco duro virtual. Si lo prefiere, puede descargar AzCopy para cargar el disco duro virtual. Es posible que tenga que configurar TLS 1.2 en la máquina cliente si ejecuta versiones anteriores de AzCopy. 
1. [Descargue las plantillas de máquina virtual y los archivos de parámetros](https://aka.ms/ase-vm-templates) en la máquina cliente. Descomprímalos en un directorio que usará como directorio de trabajo.


## <a name="vm-prerequisites"></a>Requisitos previos de la máquina virtual

Configure estos requisitos previos para crear los recursos que se necesitarán para crear la máquina virtual. 

    
### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y administran los recursos de Azure, como la cuenta de almacenamiento, el disco y el disco administrado.

> [!IMPORTANT]
> Todos los recursos se crean en la misma ubicación que el dispositivo y la ubicación se establece en **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

A continuación se muestra una salida de ejemplo.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Cree una cuenta de almacenamiento en el grupo de recursos creado en el paso anterior. Esta cuenta es una **cuenta de almacenamiento local** que se usará para cargar la imagen de disco virtual de la VM.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Con Azure Resource Manager solo se pueden crear cuentas de almacenamiento local, como las cuentas de almacenamiento con redundancia local (Standard_LRS o Premium_LRS). Para crear cuentas de almacenamiento en capas, vea los pasos que se describen en el tema sobre [incorporación y conexión a cuentas de almacenamiento en el dispositivo Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-add-storage-accounts.md).

A continuación se muestra una salida de ejemplo.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

Para obtener la clave de la cuenta de almacenamiento, ejecute el comando `Get-AzureRmStorageAccountKey`. A continuación se muestra una salida de ejemplo del comando.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>Incorporación del identificador URI del blob al archivo hosts

Asegúrese de que ya ha agregado el identificador URI del blob en el archivo hosts del cliente que está usando para conectarse al almacenamiento de blobs. **Ejecute el Bloc de notas como administrador** y agregue la entrada siguiente para el identificador URI del blob en el archivo `C:\windows\system32\drivers\etc\hosts`:

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

En un entorno típico, tendría configurado el DNS para que todas las cuentas de almacenamiento señalasen al dispositivo Azure Stack Edge Pro con una entrada `*.blob.devicename.domainname.com`.

### <a name="optional-install-certificates"></a>(Opcional) Instalación de certificados

Omita este paso si va a conectarse a través del Explorador de Storage con *http*. Si usa *https*, deberá instalar los certificados adecuados en el Explorador de Storage. En este caso, instale el certificado del punto de conexión de blob. Para más información, consulte cómo crear y cargar certificados en [Administración de certificados](azure-stack-edge-gpu-manage-certificates.md). 

### <a name="create-and-upload-a-vhd"></a>Creación y carga de un disco duro virtual

Asegúrese de que tiene una imagen de disco virtual que puede usar para la carga en el paso posterior. Siga los pasos que se indican en el tema sobre la [creación de una imagen de máquina virtual](azure-stack-edge-gpu-create-virtual-machine-image.md). 

Copie las imágenes de disco que se van a usar en los blobs en páginas en la cuenta de almacenamiento local que creó en los pasos anteriores. Puede usar una herramienta como [Explorador de Storage](https://azure.microsoft.com/features/storage-explorer/) o [AzCopy para cargar el disco duro virtual en la cuenta de almacenamiento](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) que creó en los pasos anteriores. 

### <a name="use-storage-explorer-for-upload"></a>Uso del Explorador de Storage para la carga

1. Abra el Explorador de Storage. Vaya a **Editar** y asegúrese de que la aplicación esté establecida en **API de Azure Stack de destino**.

    ![Establecimiento del destino en API de Azure Stack](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Instale el certificado de cliente con formato PEM. Vaya a **Editar > Certificados SSL > Importar certificados**. Seleccione el certificado de cliente.

    ![Importación del certificado del punto de conexión de Blob Storage](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Si usa certificados generados por el dispositivo, descargue y convierta el certificado `.cer` del punto de conexión de Blog Storage al formato `.pem`. Ejecute el siguiente comando: 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Si trae su propio certificado, use el certificado raíz de la cadena de firma con formato `.pem`.

3. Después de haber importado el certificado, reinicie el Explorador de Storage para que los cambios surtan efecto.

    ![Reinicio de Explorador de Storage](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. En el panel izquierdo, haga clic con el botón derecho en **Cuentas de almacenamiento** y seleccione **Conectar a Azure Storage**. 

    ![Conectar a Azure Storage 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Seleccione **Usar un nombre y clave de la cuenta de almacenamiento**. Seleccione **Next** (Siguiente).

    ![Conectar a Azure Storage 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. En **Conectarse con el nombre y la clave**, indique los valores de **Nombre para mostrar**, **Nombre de cuenta** de almacenamiento, **Clave de cuenta** de Azure Storage. Seleccione el valor **Otros** en Dominio de almacenamiento y especifique la cadena de conexión `<device name>.<DNS domain>`. Si no ha instalado un certificado en el Explorador de Storage, marque la opción **Usar HTTP**. Seleccione **Next** (Siguiente).

    ![Conectarse con el nombre y la clave](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Revise el **Resumen de conexiones** y seleccione **Conectar**.

8. La cuenta de almacenamiento aparece en el panel izquierdo. Seleccione y expanda la cuenta de almacenamiento. Seleccione **Contenedores de blob**, haga clic con el botón derecho y seleccione **Crear contenedor de blobs**. Escriba el nombre del contenedor de blobs.

9. Seleccione el contenedor que acaba de crear y, en el panel de la derecha, seleccione **Cargar > Cargar archivos**. 

    ![Carga del archivo de disco duro virtual 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Busque y seleccione el disco duro virtual que desea cargar en **Archivos seleccionados**. Seleccione **Blob en páginas** como **Tipo de blob** y elija **Cargar**.

    ![Carga de archivo de disco duro virtual 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. Una vez cargado el disco duro virtual en el contenedor de blobs, seleccione el disco duro virtual, haga clic con el botón derecho y, a continuación, seleccione **Propiedades**. 

    ![Carga de archivo de disco duro virtual 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Copie y guarde el identificador **URI**, ya que lo usará en los pasos posteriores.

    ![Copia de URI](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)


## <a name="create-image-for-your-vm"></a>Creación de una imagen para la máquina virtual

Para crear una imagen para la máquina virtual, edite el archivo de parámetros `CreateImage.parameters.json` y, a continuación, implemente la plantilla `CreateImage.json` que usa este archivo de parámetros.


### <a name="edit-parameters-file"></a>Edición del archivo de parámetros

El archivo `CreateImage.parameters.json` toma los parámetros siguientes: 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM image>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
    }
```

Edite el archivo `CreateImage.parameters.json` para que incluya los siguientes valores para el dispositivo Azure Stack Edge Pro:

1. Proporcione el tipo de sistema operativo correspondiente al disco duro virtual que va a cargar. El tipo de sistema operativo puede ser Windows o Linux.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Cambie el identificador URI de la imagen al URI de la imagen que cargó en el paso anterior:

   ```json
   "imageUri": {
       "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
       },
   ```

   Si usa *http* con el Explorador de Storage, cámbielo a un identificador URI *http*.

3. Proporcione un nombre de imagen único. Esta imagen se usa para crear la máquina virtual en los pasos posteriores. 

   Este es el ejemplo de JSON que se usa en este artículo.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Linux"
        },
        "imageName": {
          "value": "myaselinuximg"
        },
        "imageUri": {
          "value": "https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd"
        }
      }
    }
    ```

5. Guarde el archivo de parámetros.


### <a name="deploy-template"></a>Implementar plantilla 

Implemente la plantilla `CreateImage.json`. Esta plantilla implementa los recursos de imagen que se usarán para crear máquinas virtuales en el paso posterior.

> [!NOTE]
> Si se produce un error de autenticación al implementar la plantilla, es posible que las credenciales de Azure para esta sesión hayan expirado. Vuelva a ejecutar el comando `login-AzureRM` para conectarse de nuevo a Azure Resource Manager en el dispositivo Azure Stack Edge Pro.

1. Ejecute el siguiente comando: 
    
    ```powershell
    $templateFile = "Path to CreateImage.json"
    $templateParameterFile = "Path to CreateImage.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```
    Este comando implementa un recurso de imagen. Para consultar el recurso, ejecute el siguiente comando:

    ```powershell
    Get-AzureRmImage -ResourceGroupName <Resource Group Name> -name <Image Name>
    ``` 
    Este es un ejemplo de salida de una imagen creada correctamente.
    
    ```powershell
    PS C:\WINDOWS\system32> login-AzureRMAccount -EnvironmentName aztest -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest
    
   PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateImage\CreateImage.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateImage\CreateImage.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment4"
        
    DeploymentName          : deployment4
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:06:57 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Linux
                              imageName        String                     myaselinuximg
                              imageUri         String
                              https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd
    
    Outputs                 :
    DeploymentDebugLogLevel :    
    PS C:\WINDOWS\system32>
    ```
    
## <a name="create-vm"></a>Creación de una máquina virtual

### <a name="edit-parameters-file-to-create-vm"></a>Edición del archivo de parámetros para crear una máquina virtual
 
Para crear una máquina virtual, use el archivo de parámetros `CreateVM.parameters.json`. Toma los parámetros siguientes.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network, use ASEVNET>"
        },
        "subnetName": {
            "value": "<Name for the subnet, use ASEVNETsubNet>"
        },
        "vnetRG": {
            "value": "<Resource group for Vnet, use ASERG>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Asigne los parámetros adecuados en `CreateVM.parameters.json` para el dispositivo Azure Stack Edge Pro.

1. Proporcione un nombre único, un nombre de interfaz de red y un nombre de ipconfig. 
1. Escriba un nombre de usuario, una contraseña y un tamaño de máquina virtual admitido.
1. Cuando se habilita la interfaz de red para proceso, se crean automáticamente un conmutador virtual y una red virtual en esa interfaz de red. Puede consultar la red virtual existente para obtener el nombre de la red virtual, el nombre de la subred y el nombre del grupo de recursos de la red virtual.

    Ejecute el siguiente comando:

    ```powershell
    Get-AzureRmVirtualNetwork
    ```
    Este es la salida de ejemplo:
    
    ```powershell
    
    PS C:\WINDOWS\system32> Get-AzureRmVirtualNetwork
    
    Name                   : ASEVNET
    ResourceGroupName      : ASERG
    Location               : dbelocal
    Id                     : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft
                             .Network/virtualNetworks/ASEVNET
    Etag                   : W/"990b306d-18b6-41ea-a456-b275efe21105"
    ResourceGuid           : f8309d81-19e9-42fc-b4ed-d573f00e61ed
    ProvisioningState      : Succeeded
    Tags                   :
    AddressSpace           : {
                               "AddressPrefixes": [
                                 "10.57.48.0/21"
                               ]
                             }
    DhcpOptions            : null
    Subnets                : [
                               {
                                 "Name": "ASEVNETsubNet",
                                 "Etag": "W/\"990b306d-18b6-41ea-a456-b275efe21105\"",
                                 "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/provider
                             s/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                 "AddressPrefix": "10.57.48.0/21",
                                 "IpConfigurations": [],
                                 "ResourceNavigationLinks": [],
                                 "ServiceEndpoints": [],
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
    VirtualNetworkPeerings : []
    EnableDDoSProtection   : false
    EnableVmProtection     : false
    
    PS C:\WINDOWS\system32>
    ```

    Utilice ASEVNET para el nombre de la red virtual, ASEVNETsubNet para el nombre de la subred y ASERG para el nombre del grupo de recursos de la red virtual.
    
1. Ahora necesitará una dirección IP estática para asignarla a la máquina virtual que se encuentra en la red de subred definida anteriormente. Reemplace **PrivateIPAddress** por esta dirección en el archivo de parámetros. Para que la máquina virtual obtenga una dirección IP del servidor DCHP local, deje en blanco el valor `privateIPAddress`.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Guarde el archivo de parámetros.

    Este es el ejemplo de JSON que se usa en este artículo.
    
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "vmName": {
              "value": "VM1"
          },
          "adminUsername": {
              "value": "Administrator"
          },
          "Password": {
              "value": "Password1"
          },
        "imageName": {
          "value": "myaselinuximg"
        },
        "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        "vnetName": {
          "value": "ASEVNET"
        },
        "subnetName": {
          "value": "ASEVNETsubNet"
        },
        "vnetRG": {
          "value": "aserg"
        },
        "nicName": {
          "value": "nic5"
        },
        "privateIPAddress": {
          "value": ""
        },
        "IPConfigName": {
          "value": "ipconfig5"
        }
      }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Implementación de la plantilla para crear una máquina virtual

Implemente la plantilla de creación de máquinas virtuales `CreateVM.json`. Esta plantilla crea una interfaz de red a partir de la red virtual existente y la máquina virtual a partir de la imagen implementada.

1. Ejecute el siguiente comando: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    La creación de la máquina virtual tardará entre 15 y 20 minutos. Este es un ejemplo de salida de una máquina virtual creada correctamente.
    
    ```powershell
    PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "Deployment6"
       
    DeploymentName          : Deployment6
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:51:28 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              vmName           String                     VM1
                              adminUsername    String                     Administrator
                              password         String                     Password1
                              imageName        String                     myaselinuximg
                              vmSize           String                     Standard_NC4as_T4_v3
                              vnetName         String                     ASEVNET
                              vnetRG           String                     aserg
                              subnetName       String                     ASEVNETsubNet
                              nicName          String                     nic5
                              ipConfigName     String                     ipconfig5
                              privateIPAddress  String
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\WINDOWS\system32
    ```   

    También puede ejecutar el comando `New-AzureRmResourceGroupDeployment` de forma asincrónica con el parámetro `–AsJob`. Este es un ejemplo de salida cuando el cmdlet se ejecuta en segundo plano. Después, puede consultar el estado del trabajo que se crea mediante el cmdlet `Get-Job` .

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    ```

7. Compruebe si la máquina virtual se ha aprovisionado correctamente. Ejecute el siguiente comando:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Conexión a una máquina virtual

Los pasos para conectarse pueden ser diferentes dependiendo de si se ha creado una máquina virtual Windows o Linux.

### <a name="connect-to-windows-vm"></a>Conexión a una máquina virtual Windows

Siga estos pasos para conectarse a una máquina virtual Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Conexión a una máquina virtual Linux

Siga estos pasos para conectarse a una máquina virtual Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]



## <a name="next-steps"></a>Pasos siguientes

[Cmdlets de Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)