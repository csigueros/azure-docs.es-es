---
title: Implementación de una instancia de Hybrid Runbook Worker de usuario de Windows o Linux basada en una extensión en Azure Automation (versión preliminar)
description: En este artículo se describe cómo implementar una instancia de Hybrid Runbook Worker de Windows o Linux basada en una extensión que puede usar para ejecutar runbooks en máquinas Windows en su centro de datos local o en su entorno de nube.
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: ef82f0f5c9a66eff32228b12d8f04e1ffa0d22e9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356506"
---
# <a name="deploy-an-extension-based-windows-or-linux-user-hybrid-runbook-worker-in-automation-preview"></a>Implementación de una instancia de Hybrid Runbook Worker de usuario de Windows o Linux basada en una extensión en Automation (versión preliminar)

La incorporación basada en una extensión es solo para instancias de Hybrid Runbook Worker de **usuario**. Para la incorporación de una instancia de Hybrid Runbook Worker de **sistema**, consulte [Implementación de Hybrid Runbook Worker en Windows basado en agente en Automation](./automation-windows-hrw-install.md) o [Implementación de Hybrid Runbook Worker en Linux basado en agente en Automation](./automation-linux-hrw-install.md). 

Puede usar la característica Hybrid Runbook Worker de usuario de Azure Automation para ejecutar runbooks directamente en una máquina, sea de Azure o no, incluidos servidores registrados con [otros habilitados para Azure Arc](../azure-arc/servers/overview.md). En la máquina o servidor que hospeda el rol, puede ejecutar runbooks directamente en él y en los recursos del entorno para administrar esos recursos locales.

Azure Automation almacena y administra los runbooks y después los entrega a una o más máquinas seleccionadas. En este artículo se describe cómo implementar una instancia de Hybrid Runbook Worker de usuario en una máquina Windows o Linux, cómo quitar el trabajo y cómo quitar un grupo de instancias de Hybrid Runbook Worker.

Después de implementar correctamente un trabajo de runbook, revise la [ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o en otro entorno de nube.

## <a name="prerequisites"></a>Requisitos previos

### <a name="machine-minimum-requirements"></a>Requisitos mínimos de la máquina
- Dos núcleos
- 4 GB de RAM
- La identidad administrada asignada por el sistema debe estar habilitada en la máquina virtual de Azure o en el servidor habilitado para Arc.  Si la identidad administrada asignada por el sistema no está habilitada, se habilitará como parte del proceso de adición.
- Las máquinas que no son de Azure deben tener instalado el agente de servidores habilitados para Azure Arc (el agente de la máquina conectada). Para obtener instrucciones para instalar `AzureConnectedMachineAgent`, consulte [Conexión de máquinas híbridas a Azure desde Azure Portal](../azure-arc/servers/onboard-portal.md).

### <a name="supported-operating-systems"></a>Sistemas operativos admitidos
| Windows | Linux (x64)|
|---|---|
| &#9679; Windows Server 2019 (incluido Server Core) <br> &#9679; Windows Server 2016, versiones 1709 y 1803 (no se incluye Server Core), y <br> &#9679; Windows Server 9679 R2, 2012, 2012 <br><br> | &#9679; Debian GNU/Linux 7 y 8 <br> &#9679; Ubuntu 18.04 y 20.04 LTS, <br> &#9679; SUSE Linux Enterprise Server 15 y 15.1 (no se publicaron las versiones 13 ni 14 de SUSE), y <br> &#9679; Red Hat Enterprise Linux Server 7 y 8 |

### <a name="other-requirements"></a>Otros requisitos
| Windows | Linux (x64)|
|---|---|
| Windows PowerShell 5.1 (descargue WMF 5.1). PowerShell Core no se admite.| La protección de Linux no debe estar habilitada.  |
| .NET Framework 4.6.2 o posterior. ||

### <a name="package-requirements-for-linux"></a>Requisitos de paquetes para Linux

| Paquete necesario | Descripción |Versión mínima|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteca GNU C| 2.5-12 |
|Openssl| Bibliotecas OpenSSL | 1.0 (se admiten TLS 1.1 y TLS 1.2)|
|Curl | Cliente web de cURL | 7.15.5|
|Python ctypes | Biblioteca de funciones externas para Python| Se requieren Python 2.x o Python 3.x |
|PAM | Módulos de autenticación conectables|

| Paquete opcional | Descripción | Versión mínima|
|--------------------- | --------------------- | -------------------|
| PowerShell Core | Para ejecutar runbooks de PowerShell, es necesario instalar PowerShell Core. Para obtener instrucciones, consulte [Instalación de PowerShell Core en Linux](/powershell/scripting/install/installing-powershell-core-on-linux). | 6.0.0 |

### <a name="network-requirements"></a>Requisitos de red

### <a name="proxy-server-use"></a>Uso del servidor proxy

Si se usa un servidor proxy para la comunicación entre Azure Automation y las máquinas que ejecutan la instancia de Hybrid Runbook Worker basada en extensión, asegúrese de que sea posible acceder a los recursos adecuados. El tiempo de expiración para las solicitudes de Hybrid Runbook Worker y los servicios de Automation es de 30 segundos. Después de tres intentos, se produce un error en una solicitud.

### <a name="firewall-use"></a>Uso del firewall

Si usa un firewall para restringir el acceso a Internet, tendrá que configurarlo para que permita el acceso. El siguiente puerto y las direcciones URL son necesarios para Hybrid Runbook Worker, y para [State Configuration de Automation](./automation-dsc-overview.md) a fin de comunicarse con Azure Automation.

| Propiedad | Descripción |
|---|---|
|Port | 443 para el acceso a Internet de salida|
|Dirección URL global |*.azure-automation.net|
|Dirección URL global de US Gov Virginia |*.azure-automation.us|
|Servicio del Agente |`https://<workspaceId>.agentsvc.azure-automation.net`|

## <a name="create-hybrid-worker-group"></a>Creación de un grupo de Hybrid Worker 

Siga estos pasos para crear un grupo de Hybrid Worker en Azure Portal.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Vaya a su cuenta de Automation. 

1. En **Automatización de procesos**, seleccione **Grupos de Hybrid Worker**. 

1. Seleccione **+Creación de un grupo de Hybrid Worker**. 

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-groups-portal.png" alt-text="Selección de la opción de grupos de Hybrid Worker en el portal.":::

1. En la pestaña  **Aspectos básicos** , en el cuadro de texto **Nombre**, escriba un nombre para su grupo de Hybrid Worker. 

1. Para la opción **Use run as credential** (Usar credencial de ejecutar como): 
   - Si selecciona **No**, la extensión híbrida se instalará con la cuenta del sistema local.
   - Si selecciona **Sí**, en la lista desplegable, seleccione el recurso de credencial.

1. Seleccione **Siguiente** para avanzar a la pestaña **Hybrid Workers**. Puede seleccionar máquinas virtuales de Azure o servidores habilitados para Azure Arc para añadirlos a este grupo de Hybrid Worker. Si no selecciona ninguna máquina, se creará un grupo de Hybrid Worker vacío. Todavía puede agregar máquinas más adelante. 

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/basics-tab-portal.png" alt-text="Escritura del nombre y las credenciales en la pestaña Aspectos básicos.":::

1. Seleccione **Agregar máquinas** para ir a la página para **agregar máquinas como Hybrid Worker**. Solo verá las máquinas que no forman parte de ningún otro grupo de Hybrid Worker. 

1. Active la casilla situada junto a las máquinas que desea agregar al grupo de Hybrid Worker. Si no ve la máquina que no es de Azure, asegúrese de que el agente de la máquina conectada de Azure Arc está instalado en la máquina.  

1. Seleccione **Agregar**. 

1. Seleccione **Siguiente** para ir a la pestaña **Revisar y crear**. 

1. Seleccione **Crear**. La extensión de Hybrid Worker se instala en la máquina y la instancia de Hybrid Worker se registra en el grupo de Hybrid Worker. La adición del Hybrid Worker al grupo se produce inmediatamente, mientras que la instalación de la extensión puede tardar unos minutos. Seleccione **Actualizar** para ver el nuevo grupo. Seleccione el nombre del grupo para ver los detalles del Hybrid Worker.

   > [!NOTE]
   > Una máquina seleccionada no se agregará a un grupo de Hybrid Worker si ya forma parte de otro grupo de Hybrid Worker.

## <a name="add-a-machine-to-a-hybrid-worker-group"></a>Adición de una máquina a un grupo de Hybrid Worker

También puede agregar máquinas a un grupo de Hybrid Worker existente.

1. En **Automatización de procesos**, seleccione **Grupos de Hybrid Worker** y, a continuación, el grupo de Hybrid Worker existente para ir a la página **Grupo de Hybrid Worker**.

1. En **Grupo de Hybrid Worker**, seleccione **Hybrid Workers**.

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-group-add-machine.png" alt-text="Botón Agregar para agregar máquinas al grupo existente.":::

1. Seleccione **+Agregar** para ir a la página para **agregar máquinas como Hybrid Worker**. Solo verá las máquinas que no forman parte de ningún otro grupo de Hybrid Worker. 

1. Active la casilla situada junto a las máquinas que desea agregar al grupo de Hybrid Worker. Si no ve la máquina que no es de Azure, asegúrese de que el agente de la máquina conectada de Azure Arc está instalado en la máquina.

1. Seleccione **Agregar** para agregar la máquina al grupo. Una vez agregada, puede ver el tipo de máquina como máquina virtual de Azure o servidor habilitado para Arc. El campo **Plataforma** muestra la instancia de Worker como **Basada en agente (V1)** o **Basada en extensión (V2)** .

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-group-platform.png" alt-text="Campo de plataforma que muestra si está basada en agente o en extensión":::.

## <a name="delete-a-hybrid-runbook-worker"></a>Eliminación de una instancia de Hybrid Runbook Worker

Puede eliminar la instancia de Hybrid Runbook Worker desde el portal.

1. En **Automatización de procesos**, seleccione **Grupos de Hybrid Worker** y, a continuación, el grupo de Hybrid Worker para ir a la página **Grupo de Hybrid Worker**.

1. En **Grupo de Hybrid Worker**, seleccione **Hybrid Workers**.

1. Active la casilla situada junto a las máquinas que desea eliminar del grupo de Hybrid Worker.

1. Seleccione **Eliminar**.  Se mostrará una advertencia en un cuadro de diálogo **Eliminar Hybrid Worker** que indica que la instancia de Hybrid Worker seleccionada se eliminaría permanentemente. Seleccione **Eliminar**. Esta operación eliminará la extensión del trabajo **basado en extensión (V2)** o quitará la entrada **basada en agente (V1)** del portal. Sin embargo, deja la instancia de Hybrid Worker obsoleta en la máquina virtual. Para desinstalar manualmente el agente, vea [Desinstalación de agente](../azure-monitor/agents/agent-manage.md#uninstall-agent).

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/delete-machine-from-group.png" alt-text="Eliminación de máquina virtual del grupo existente":::.

   > [!NOTE]
   > Una instancia de Hybrid Worker puede coexistir con ambas plataformas: **basada en agente (V1)** y **basada en extensión (V2)** . Si opta por la opción **Basada en extensión (V2)** en una instancia de Hybrid Worker que ya se ejecuta **Basada en agente (V1)** , podría ver dos entradas de la instancia de Hybrid Runbook Worker en el grupo: una con la plataforma **Basada en extensión (V2)** y otra **Basada en agente (V1)** .

## <a name="delete-a-hybrid-runbook-worker-group"></a>Eliminación de un grupo de Hybrid Runbook Worker

Puede eliminar un grupo de Hybrid Runbook Worker vacío desde el portal.

1. En **Automatización de procesos**, seleccione **Grupos de Hybrid Worker** y, a continuación, el grupo de Hybrid Worker para ir a la página **Grupo de Hybrid Worker**.

1. Seleccione **Eliminar**. Se mostrará una advertencia en un cuadro de diálogo para quitar las máquinas que se definen como Hybrid Workers en el grupo de Hybrid Worker. Si ya hay un trabajo agregado al grupo, primero tendrá que eliminar el trabajo del grupo.

1. Seleccione **Sí**. Se eliminará el grupo de Hybrid Worker.

## <a name="use-azure-resource-manager-template"></a>Usar plantillas de Azure Resource Manager

Puede usar una plantilla de Azure Resource Manager (ARM) para crear una nueva máquina virtual de Azure en Windows y conectarla a una cuenta de Automation y un grupo de Hybrid Worker existente. Consulte [¿Qué son las plantillas de Resource Manager?](../azure-resource-manager/templates/overview.md) para obtener más información sobre las plantillas de ARM.

### <a name="review-the-template"></a>Revisión de la plantilla

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationAccount": {
      "type": "string"
    },
    "automationAccountLocation": {
      "type": "string"
    },
    "workerGroupName": {
      "type": "string"
    },
    "virtualMachineName": {
      "type": "string",
      "defaultValue": "simple-vm",
      "metadata": {
        "description": "Name of the virtual machine."
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "minLength": 12,
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "vmLocation": {
      "type": "string",
      "defaultValue": "North Central US",
      "metadata": {
        "description": "Location for the VM."
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_DS1_v2",
      "metadata": {
        "description": "Size of the virtual machine."
      }
    },
    "osVersion": {
      "type": "string",
      "defaultValue": "2019-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "2016-Nano-Server",
        "2016-Datacenter-with-Containers",
        "2016-Datacenter",
        "2019-Datacenter",
        "2019-Datacenter-Core",
        "2019-Datacenter-Core-smalldisk",
        "2019-Datacenter-Core-with-Containers",
        "2019-Datacenter-Core-with-Containers-smalldisk",
        "2019-Datacenter-smalldisk",
        "2019-Datacenter-with-Containers",
        "2019-Datacenter-with-Containers-smalldisk"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version."
      }
    },
    "dnsNameForPublicIP": {
      "type": "string",
      "metadata": {
        "description": "DNS name for the public IP"
      }
    },
    "_CurrentDateTimeInTicks": {
      "type": "string",
      "defaultValue": "[utcNow('yyyy-MM-dd')]"
    }
  },
  "variables": {
    "nicName": "myVMNict",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
    "vmName": "[parameters('virtualMachineName')]",
    "virtualNetworkName": "MyVNETt",
    "publicIPAddressName": "myPublicIPt",
    "networkSecurityGroupName": "default-NSGt",
    "UniqueStringBasedOnTimeStamp": "[uniqueString(deployment().name, parameters('_CurrentDateTimeInTicks'))]"
  },
  "resources": [
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[parameters('vmLocation')]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
        }
      }
    },
    {
      "comments": "Default Network Security Group for template",
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2020-08-01",
      "name": "[variables('networkSecurityGroupName')]",
      "location": "[parameters('vmLocation')]",
      "properties": {
        "securityRules": [
          {
            "name": "default-allow-3389",
            "properties": {
              "priority": 1000,
              "access": "Allow",
              "direction": "Inbound",
              "destinationPortRange": "3389",
              "protocol": "Tcp",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationAddressPrefix": "*"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
      ],
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]",
              "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[variables('publicIPAddressName')]",
        "[variables('virtualNetworkName')]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[variables('nicName')]"
      ],
      "identity": {
             "type": "SystemAssigned"
      } ,
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "[parameters('osVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        }
      }
    },
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2021-06-22",
      "name": "[parameters('automationAccount')]",
      "location": "[parameters('automationAccountLocation')]",
      "properties": {
        "sku": {
          "name": "Basic"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('workerGroupName'),'/',guid('AzureAutomationJobName', variables('UniqueStringBasedOnTimeStamp')))]",
          "type": "hybridRunbookWorkerGroups/hybridRunbookWorkers",
          "apiVersion": "2021-06-22",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))]",
            "[resourceId('Microsoft.Compute/virtualMachines', variables('vmName'))]"
          ],
          "properties": {
            "vmResourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
          }
        }
      ]
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'),'/HybridWorkerExtension')]",
      "apiVersion": "2020-12-01",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))]",
        "[resourceId('Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Automation.HybridWorker",
        "type": "HybridWorkerForWindows",
        "typeHandlerVersion": "0.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "AutomationAccountURL": "[reference(resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))).AutomationHybridServiceUrl]"
        }
      }
    }
  ],
  "outputs": {
    "output1": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))).AutomationHybridServiceUrl]"
    }
  }
}
```

Recursos de Azure definidos en la plantilla:

- hybridRunbookWorkerGroups/hybridRunbookWorkers
- [Microsoft.Compute/virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions): 

### <a name="review-parameters"></a>Revisión de parámetros

Revise los parámetros usados en esta plantilla.

|Propiedad |Descripción |
|---|---|
|automationAccount| Nombre de la cuenta de Automation existente. |
|automationAccountLocation | Región de la cuenta de Automation existente. |
|workerGroupName | Nombre del grupo de Hybrid Worker existente. |
|virtualMachineName| Nombre de la VM que se va a crear. El valor predeterminado es `simple-vm`.|
|adminUsername| Nombre de usuario administrador para la VM. |
|adminPassword| Contraseña de administrador de la máquina virtual. |
|vmLocation| Región de la nueva máquina virtual. El valor predeterminado es `North Central US`.|
|vmSize| Tamaño de la nueva máquina virtual. El valor predeterminado es `Standard_DS1_v2`. |
|osVersion| Sistema operativo de la nueva máquina virtual de Windows. El valor predeterminado es `2019-Datacenter`.|
|dnsNameForPublicIP| Nombre DNS para la dirección IP pública. |


## <a name="next-steps"></a>Pasos siguientes

* Consulte [Ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o de otros entornos de nube.

* Para aprender a solucionar problemas con las instancias de Hybrid Runbook Worker, consulte [Solución de incidencias de Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).