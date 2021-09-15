---
title: Creación de máquinas virtuales en un conjunto de escalado flexible usando una plantilla de ARM
description: Obtenga información sobre cómo crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible usando una plantilla de ARM.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 64fc87455b035503891319de1b8fd06090586b4b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868538"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-an-arm-template"></a>Versión preliminar: creación de máquinas virtuales en un conjunto de escalado flexible usando una plantilla de ARM

**Se aplica a:** :heavy_check_mark: Conjuntos de escalado flexibles


En este artículo se describe cómo usar una plantilla de ARM para crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible. Para obtener más información sobre los conjuntos de escalado flexibles, consulte [Modo de orquestación flexible para conjuntos de escalado de máquinas virtuales](flexible-virtual-machine-scale-sets.md). 


> [!IMPORTANT]
> En el modo de orquestación flexible, los conjuntos de escalado de máquinas virtuales se encuentran actualmente en versión preliminar pública. No es necesario ningún procedimiento de participación para usar la funcionalidad de versión preliminar pública que se describe a continuación.
> Esta versión preliminar se ofrece sin contrato de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


> [!CAUTION]
> El modo de orquestación se define al crear el conjunto de escalado y no se puede cambiar ni actualizar más adelante.

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="register-for-flexible-orchestration-mode"></a>Registro para el modo de orquestación flexible

Para poder implementar conjuntos de escalado de máquinas virtuales en el modo de orquestación flexible, antes es preciso [registrar la suscripción en la característica en vista previa](flexible-virtual-machine-scale-sets.md#register-for-flexible-orchestration-mode). El registro puede tardar varios minutos en terminar.

## <a name="arm-template"></a>Plantilla ARM 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Las plantillas de Resource Manager permiten implementar grupos de recursos relacionados. En una única plantilla, puede crear el conjunto de escalado de máquinas virtuales, instalar aplicaciones y configurar reglas de escalado automático. Con el uso de variables y parámetros, esta plantilla se puede reutilizar para actualizar los conjuntos de escalado existentes o crear más. Puede implementar plantillas mediante Azure Portal, la CLI de Azure o Azure PowerShell, o bien desde las canalizaciones de integración continua o entrega continua (CI/CD).


## <a name="review-the-template"></a>Revisión de la plantilla

```armasm
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources"
        }
      },
      "vmSku": {
        "type": "string",
        "defaultValue": "Standard_D1_v2",
        "metadata": {
          "description": "Size of VMs in the VM Scale Set."
        }
      },
      "vmssName": {
        "type": "string",
        "metadata": {
          "description": "String used as a base for naming resources (9 characters or less). A hash is prepended to this string for some resources, and resource-specific information is appended."
        }
      },
      "instanceCount": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1,
        "maxValue": 100,
        "metadata": {
          "description": "Number of VM instances (100 or less)."
        }
      },
      "adminUsername": {
        "type": "string",
        "metadata": {
          "description": "Admin username on all VMs."
        }
      },
      "authenticationType": {
        "type": "string",
        "defaultValue": "sshPublicKey",
        "allowedValues": [
          "sshPublicKey",
          "password"
        ],
        "metadata": {
          "description": "Type of authentication to use on the Virtual Machine. SSH key is recommended."
        }
      },
      "adminPasswordOrKey": {
        "type": "securestring",
        "metadata": {
          "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
        }
      },
      "_artifactsLocation": {
        "type": "string",
        "defaultValue": "[deployment().properties.templatelink.uri]",
        "metadata": {
          "description": "The base URI where artifacts required by this template are located"
        }
      },
      "_artifactsLocationSasToken": {
        "type": "securestring",
        "defaultValue": "",
        "metadata": {
          "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
        }
      }
    },
    "variables": {
      "addressPrefix": "10.0.0.0/16",
      "subnetPrefix": "10.0.0.0/24",
      "networkApiVersion": "2020-11-01",
      "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",
      "publicIPAddressName": "[concat(parameters('vmssName'), 'pip')]",
      "subnetName": "[concat(parameters('vmssName'), 'subnet')]",
      "loadBalancerName": "[concat(parameters('vmssName'), 'lb')]",
      "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
      "networkSecurityGroupName": "[concat(parameters('vmssName'), 'nsg')]",
      "bePoolName": "[concat(parameters('vmssName'), 'bepool')]",
      "lbRuleName": "[concat(parameters('vmssName'), 'lbrule')]",
      "lbProbeName": "[concat(parameters('vmssName'), 'lbprobe')]",
      "bePoolConfigID": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', variables('loadBalancerName'),variables('bePoolName'))]",
      "lbProbeID": "[resourceId('Microsoft.Network/loadBalancers/probes', variables('loadBalancerName'),variables('lbProbeName'))]",
      "nicName": "[concat(parameters('vmssName'), 'nic')]",
      "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",
      "frontEndIPConfigID": "[resourceId('Microsoft.Network/loadBalancers/frontendIPConfigurations', variables('loadBalancerName'),'loadBalancerFrontEnd')]",
      "osType": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "16.04-LTS",
        "version": "latest"
      },
      "imageReference": "[variables('osType')]",
      "linuxConfiguration": {
        "disablePasswordAuthentication": true,
        "ssh": {
          "publicKeys": [
            {
              "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
              "keyData": "[parameters('adminPasswordOrKey')]"
            }
          ]
        }
      }
    },
    "resources": [
              {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2020-06-01",
            "name": "[variables('networkSecurityGroupName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowPort9000",
                        "properties": {
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "9000",
                            "sourceAddressPrefix": "Internet",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 100,
                            "direction": "Inbound"
                        }
                    }
                ]
            }
        },
      {
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2020-06-01",
        "name": "[variables('virtualNetworkName')]",
        "location": "[parameters('location')]",
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
        "type": "Microsoft.Network/publicIPAddresses",
        "apiVersion": "2020-06-01",
        "name": "[variables('publicIPAddressName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard"
        },
        "properties": {

          "publicIPAllocationMethod": "Static",
          "dnsSettings": {
            "domainNameLabel": "[parameters('vmssName')]"
          }
        }
      },
      {
        "type": "Microsoft.Network/loadBalancers",
        "apiVersion": "2020-06-01",
        "name": "[variables('loadBalancerName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard"
        },
        "dependsOn": [
          "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
        ],
        "properties": {
          "frontendIPConfigurations": [
            {
              "name": "LoadBalancerFrontEnd",
              "properties": {
                "publicIPAddress": {
                  "id": "[variables('publicIPAddressID')]"
                }
              }
            }
          ],
          "backendAddressPools": [
            {
              "name": "[variables('bePoolName')]"
            }
          ],
          "probes": [
            {
              "name": "[variables('lbProbeName')]",
              "properties": {
                "port": 9000,
                "protocol": "Tcp",
                "numberOfProbes": 2,
                "intervalInSeconds": 5
              }
            }
          ],
          "loadBalancingRules": [
            {
              "name": "[variables('lbRuleName')]",
              "properties": {
                "frontendIPConfiguration": {
                  "id": "[variables('frontEndIPConfigID')]"
                },
                "backendAddressPool": {
                  "id": "[variables('bePoolConfigID')]"
                },
                "probe": {
                  "id": "[variables('lbProbeID')]"
                },
                "loadDistribution": "Default",
                "backendPort": 9000,
                "frontendPort": 9000,
                "protocol": "Tcp",
                "idleTimeoutInMinutes": 4,
                "enableFloatingIP": false,
                "enableTcpReset": false,
                "disableOutboundSnat": false
              }
             }
          ]
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets",
        "apiVersion": "2021-03-01",
        "name": "[parameters('vmssName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('vmSku')]",
          "tier": "Standard",
          "capacity": "[parameters('instanceCount')]"
        },
        "dependsOn": [
          "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
          "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
        ],
        "properties": {
          "orchestrationMode": "Flexible",
          "platformFaultDomainCount": 1,
          "singlePlacementGroup": false,
          "virtualMachineProfile": {
            "storageProfile": {
              "osDisk": {
                "createOption": "FromImage",
                "caching": "ReadWrite"
              },
              "imageReference": "[variables('imageReference')]"
            },
            "osProfile": {
              "computerNamePrefix": "[parameters('vmssName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPasswordOrKey')]",
              "linuxConfiguration": "[if(equals(parameters('authenticationType'), 'password'), json('null'), variables('linuxConfiguration'))]"
            },
            "networkProfile": {
              "networkApiVersion": "[variables('networkApiVersion')]",
              "networkInterfaceConfigurations": [
                {
                  "name": "[variables('nicName')]",
                  "properties": {
                    "primary": true,
                    "ipConfigurations": [
                      {
                        "name": "[variables('ipConfigName')]",
                        "properties": {
                          "primary": true,
                          "subnet": {
                            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]"
                          },
                          "loadBalancerBackendAddressPools": [
                            {
                              "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', variables('loadBalancerName'), variables('bePoolName'))]"
                            }
                          ]
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "lapextension",
                  "properties": {
                    "publisher": "Microsoft.Azure.Extensions",
                    "type": "CustomScript",
                    "typeHandlerVersion": "2.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                      "fileUris": [
                        "[uri(parameters('_artifactsLocation'), concat('installserver.sh', parameters('_artifactsLocationSasToken')))]",
                        "[uri(parameters('_artifactsLocation'), concat('workserver.py', parameters('_artifactsLocationSasToken')))]"
                      ],
                      "commandToExecute": "bash installserver.sh"
                    }
                  }
                }
              ]
            }
          }
        }
      },
      {
        "type": "Microsoft.Insights/autoscaleSettings",
        "apiVersion": "2015-04-01",
        "name": "[concat(parameters('vmssName'), '-autoscalehost')]",
        "location": "[parameters('location')]",
        "dependsOn": [
          "[resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('vmSSName'))]"
        ],
        "properties": {
          "name": "[concat(parameters('vmssName'), '-autoscalehost')]",
          "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('vmSSName'))]",
          "enabled": true,
          "profiles": [
            {
              "name": "Profile1",
              "capacity": {
                "minimum": "1",
                "maximum": "10",
                "default": "1"
              },
              "rules": [
                {
                  "metricTrigger": {
                    "metricName": "Percentage CPU",
                    "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('vmSSName'))]",
                    "timeGrain": "PT1M",
                    "statistic": "Average",
                    "timeWindow": "PT5M",
                    "timeAggregation": "Average",
                    "operator": "GreaterThan",
                    "threshold": 60
                  },
                  "scaleAction": {
                    "direction": "Increase",
                    "type": "ChangeCount",
                    "value": "1",
                    "cooldown": "PT1M"
                  }
                },
                {
                  "metricTrigger": {
                    "metricName": "Percentage CPU",
                    "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('vmSSName'))]",
                    "timeGrain": "PT1M",
                    "statistic": "Average",
                    "timeWindow": "PT5M",
                    "timeAggregation": "Average",
                    "operator": "LessThan",
                    "threshold": 30
                  },
                  "scaleAction": {
                    "direction": "Decrease",
                    "type": "ChangeCount",
                    "value": "1",
                    "cooldown": "PT1M"
                  }
                }
              ]
            }
          ]
        }
      }
    ]
  }
```

Estos recursos se definen en la plantilla:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)

### <a name="define-a-scale-set"></a>Definición de un conjunto de escalado

Para crear una escala con una plantilla, debe definir los recursos adecuados. Las partes principales del tipo de recurso del conjunto de escalado de máquinas virtuales son:

| Propiedad                     | Descripción de la propiedad                                  | Valor de la plantilla de ejemplo                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Tipo de recurso de Azure que se creará                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | Nombre del conjunto de escalado                                       | myScaleSet                                |
| ubicación                     | Ubicación donde se creará el conjunto de escalado                     | Este de EE. UU.                                   |
| sku.name                     | Tamaño de VM para cada instancia de conjunto de escalado                  | Standard_A1                               |
| sku.capacity                 | Número de instancias de VM que se crearán inicialmente           | 2                                         |
| imageReference               | Imagen personalizada o plataforma que se usará para las instancias de VM | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | Prefijo de nombre para cada instancia de VM                     | myvmss                                    |
| osProfile.adminUsername      | Nombre de usuario para cada instancia de VM                        | azureuser                                 |
| osProfile.adminPassword      | Contraseña para cada instancia de VM                        | P@ssw0rd!                                 |

Para personalizar una plantilla de conjunto de escalado, puede cambiar el tamaño de la máquina virtual o la capacidad inicial. Otra opción es usar una plataforma diferente o una imagen personalizada.

### <a name="add-a-sample-application"></a>Adición de una aplicación de ejemplo

Para probar el conjunto de escalado, instale una aplicación web básica. Cuando se implementa un conjunto de escalado, las extensiones de VM pueden proporcionar tareas de configuración y automatización posteriores a la implementación, como la instalación de una aplicación. Los scripts se pueden descargar desde Azure Storage o GitHub, o se pueden proporcionar a Azure Portal en el tiempo de ejecución de la extensión. Para aplicar una extensión al conjunto de escalado, agregue la sección *extensionProfile* en el ejemplo anterior del recurso. Normalmente, el perfil de extensión define las propiedades siguientes:

- Tipo de extensión
- Editor de la extensión
- Versión de la extensión
- Ubicación de los scripts de configuración o instalación
- Comandos que se ejecutarán en las instancias de VM

La plantilla usa la extensión de script personalizada para instalar [Bottle](https://bottlepy.org/docs/dev/), una plataforma web de Python y un servidor HTTP sencillo.

Se definen dos scripts en **fileUris** - *installserver.sh* y *workserver.py*. Estos archivos se descargan desde GitHub y, a continuación, *commandToExecute* ejecuta `bash installserver.sh` para instalar y configurar la aplicación.

## <a name="deploy-the-template"></a>Implementación de la plantilla

También puede implementar una plantilla de Resource Manager mediante la CLI de Azure:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az deployment group create -g myResourceGroup -f azuredeploy.json --parameters _artifactsLocation=https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/application-workloads/python/vmss-bottle-autoscale/azuredeploy.json
```

Responda a los avisos para proporcionar un nombre para el conjunto de escalado, el recuento de instancias y las credenciales de administrador de las instancias de VM. El proceso de creación del conjunto de escalado y de los recursos compatibles tarda unos minutos.

## <a name="validate-the-deployment"></a>Validación de la implementación

Para ver el conjunto de escalado en acción, acceda a la aplicación web de ejemplo en un explorador web. Obtenga la dirección IP pública del equilibrador de carga con [az network public-ip list](/cli/azure/network/public-ip), tal y como se indica a continuación:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Escriba la dirección IP pública del equilibrador de carga en un explorador web con el formato *http:\//publicIpAddress:9000/do_work*. El equilibrador de carga distribuye el tráfico a una de las instancias de VM, como se muestra en el ejemplo siguiente:

![Página web predeterminada de NGINX](../virtual-machine-scale-sets/media/virtual-machine-scale-sets-create-template/running-python-app.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, puede usar el comando [az group delete](/cli/azure/group) para quitar el grupo de recursos, el conjunto de escalado y todos los recursos relacionados como se indica a continuación. El parámetro `--no-wait` devuelve el control a la petición de confirmación sin esperar a que finalice la operación. El parámetro `--yes` confirma que desea eliminar los recursos sin pedir confirmación adicional.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Obtenga información sobre cómo crear un conjunto de escalado flexible en Azure Portal.](flexible-virtual-machine-scale-sets-portal.md)