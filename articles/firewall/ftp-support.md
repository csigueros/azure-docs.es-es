---
title: Compatibilidad de FTP con Azure Firewall
description: De manera predeterminada, el FTP activo está deshabilitado en Azure Firewall. Puede habilitarlo mediante PowerShell, la CLI y las plantillas de ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 09/23/2021
ms.author: victorh
ms.openlocfilehash: 7aeb7c596f1b4ca286cc9fce22965418c7c61c85
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084213"
---
# <a name="azure-firewall-ftp-support"></a>Compatibilidad de FTP con Azure Firewall

Para admitir el FTP, un firewall debe tener en cuenta los siguientes aspectos clave:
- Modo FTP: activo o pasivo.
- Ubicación del cliente o servidor: Internet o intranet.
- Dirección del flujo: entrante o saliente. 

Azure Firewall admite escenarios de FTP activo y pasivo. Para obtener más información sobre el modo FTP, consulte [FTP activo frente a FTP pasivo: una explicación definitiva](https://slacksite.com/other/ftp.html). 

De forma predeterminada, el FTP pasivo está habilitado y la compatibilidad con el FTP activo está deshabilitada para protegerse frente a ataques de devolución de FTP mediante el comando PORT de FTP. 

Sin embargo, puede habilitar el FTP activo durante la implementación mediante Azure PowerShell, la CLI de Azure o una plantilla de Azure ARM. Azure Firewall puede admitir el FTP activo y pasivo simultáneamente. *ActiveFTP* es una propiedad de Firewall que se puede habilitar para las SKU estándar y las premium, para los firewalls de redes virtuales y centros seguros, y cuando se usan directivas de Firewall y reglas clásicas.


## <a name="supported-scenarios"></a>Escenarios admitidos

En la tabla siguiente se muestra la configuración necesaria para admitir varios escenarios de FTP:


|Escenario de Firewall  |Modo FTP activo   |Modo FTP pasivo  |
|---------|---------|---------|
|Red virtual-Red virtual     |Reglas de red para configurar:<br>- Permitir desde la red virtual de origen al puerto IP de destino 21<br>- Permitir desde el puerto IP de destino 20 a la red virtual de origen |Reglas de red para configurar:<br>- Permitir desde la red virtual de origen al puerto IP de destino 21<br>- Permitir desde la red virtual de origen a la IP de destino \<Range of Data Ports>|
|Red virtual de salida-Internet<br><br>(Cliente FTP en red virtual, servidor en Internet)      |No compatible *|**Condición previa**: configure el servidor FTP para que acepte los canales de control y de datos de distintas direcciones IP de origen. Como alternativa, configure Azure Firewall con una única dirección IP pública.<br><br>Reglas de red para configurar:<br>- Permitir desde la red virtual de origen al puerto IP de destino 21<br>- Permitir desde la red virtual de origen a la IP de destino \<Range of Data Ports> |
|DNAT de entrada<br><br>(Cliente FTP en Internet, servidor en red virtual)      |Regla DNAT para configurar:<br>- DNAT del origen de Internet al puerto IP de la red virtual 21<br><br>Regla de red para configurar:<br>- Permitir desde el puerto IP de la red virtual 20 al origen de Internet |**Condiciones previas**:<br>Configure el servidor FTP para que acepte los canales de control y de datos de distintas direcciones IP de origen.<br><br>Sugerencia: Azure Firewall admite un número limitado de reglas DNAT. Es importante configurar el servidor FTP para que use un rango de puertos pequeño en el canal de datos.<br><br>Reglas DNAT para configurar:<br>- DNAT del origen de Internet al puerto IP de la red virtual 21<br>- DNAT del origen de Internet a la IP de la red virtual \<Range of Data Ports> |

\* El FTP activo no funcionará cuando el cliente FTP deba llegar a un servidor FTP de Internet. El FTP activo usa un comando PORT del cliente FTP que indica al servidor FTP qué dirección IP y puerto usar para el canal de datos. Este comando PORT usa la dirección IP privada del cliente, que no se puede cambiar. El tráfico del lado cliente que recorre Azure Firewall será tráfico con NAT para las comunicaciones basadas en Internet, por lo que el servidor FTP considera el comando PORT como no válido. Se trata de una limitación general del FTP activo cuando se usa con una NAT del lado del cliente. 


## <a name="deploy-using-azure-powershell"></a>Implementación mediante Azure PowerShell

Para realizar la implementación mediante Azure PowerShell, use el parámetro `AllowActiveFTP`. Para más información, consulte [Creación de un firewall con Permitir FTP activo](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-).

## <a name="deploy-using-azure-cli"></a>Implementación con la CLI de Azure

Para realizar la implementación mediante la CLI de Azure, use el parámetro `--allow-active-ftp`. Para más información, consulte [az network firewall create](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters). 

## <a name="deploy-azure-resource-manager-arm-template"></a>Implementación de una plantilla de Azure Resource Manager (ARM)

Para realizar la implementación con una plantilla de ARM, use el campo `AdditionalProperties`:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Para obtener más información, consulte [Microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Pasos siguientes

Para aprender a implementar una instancia de Azure Firewall, consulte [Implementación y configuración de Azure Firewall mediante Azure PowerShell](deploy-ps.md).