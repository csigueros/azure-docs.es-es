---
title: Nueva versión de la extensión de máquina virtual de Azure para soluciones de SAP | Microsoft Docs
description: Aprenda a implementar la nueva extensión de máquina virtual para SAP.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: OliverDoll
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/22/2021
ms.author: oldoll
ms.openlocfilehash: 8e70919b229ff1c29590a370a201f8b8a0ebc62b
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067393"
---
# <a name="new-version-of-azure-vm-extension-for-sap-solutions"></a>Nueva versión de la extensión de máquina virtual de Azure para soluciones de SAP 
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[std-extension]:vm-extension-for-sap-standard.md (Versión estándar de la extensión de máquina virtual de Azure para soluciones de SAP)
[configure-windows]:vm-extension-for-sap-new.md#a876ee7b-43b4-4782-aa5f-73753b6af0ea (Configuración de la nueva extensión de máquina virtual de Azure para soluciones de SAP con PowerShell)
[troubleshoot-windows]:vm-extension-for-sap-new.md#dee9099b-7b8a-4cdd-86a2-3f6ee964266f (Solución de problemas de Windows)
[troubleshoot-linux]:vm-extension-for-sap-new.md#02783aa4-5443-43f5-bc11-7af19ebf0c36 (Solución de problemas de Linux)
[deployment-guide-4.1]:vm-extension-for-sap-new.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Implementación de cmdlets de Azure PowerShell)
[azure-cli-2]:/cli/azure/install-azure-cli
[configure-linux]:vm-extension-for-sap-new.md#fa4428b9-bed6-459a-9dfb-74cc27454481 (Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con la CLI de Azure)
[configure-windows]:vm-extension-for-sap-new.md#a876ee7b-43b4-4782-aa5f-73753b6af0ea (Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con PowerShell)
[health-check]:vm-extension-for-sap-new.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Comprobaciones de estado)
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[readiness-check]:vm-extension-for-sap-new.md#5774c1db-1d3c-4b34-8448-3afd0b0f18ab (Comprobación de preparación)

## <a name="prerequisites"></a>Requisitos previos

> [!NOTE]
> Instrucción de soporte técnico general: la compatibilidad con la extensión de Azure para SAP se proporciona a través de los canales de soporte técnico de SAP.
> Si necesita ayuda con la extensión de máquina virtual de Azure para soluciones de SAP, abra un caso de soporte técnico con Soporte técnico de SAP.
  
> [!NOTE]
> Asegúrese de desinstalar la extensión de máquina virtual antes de cambiar entre la versión estándar y la nueva de la extensión de Azure para SAP.

> [!NOTE]
> Hay dos versiones de la extensión de VM. En este artículo se trata la **nueva** versión de la extensión de máquina virtual de Azure para SAP. Para obtener instrucciones sobre cómo instalar la versión estándar, consulte [Versión estándar de la extensión de máquina virtual de Azure para soluciones de SAP][std-extension].

* Asegúrese de usar el agente del host 7.21 PL 47 o posterior.
* Asegúrese de que la máquina virtual en la que está habilitada la extensión tiene acceso a management.azure.com.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implementación de cmdlets de Azure PowerShell

Siga los pasos descritos en el artículo [Instalar el módulo de Azure PowerShell](/powershell/azure/install-az-ps)

Compruebe periódicamente si existen actualizaciones para los cmdlets de PowerShell, que normalmente se actualizan mensualmente. Siga los pasos descritos en [este](/powershell/azure/install-az-ps#update-the-azure-powershell-module) artículo. Salvo que se indique lo contrario en las notas de SAP [1928533] o [2015553], le recomendamos que trabaje con la versión más reciente de los cmdlets de Azure PowerShell.

Para comprobar la versión de los cmdlets de Azure PowerShell que están instalados en su equipo, ejecute este comando de PowerShell:

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implementación de la CLI de Azure

Siga los pasos descritos en el artículo [Instalación de la CLI de Azure](/cli/azure/install-azure-cli)

Compruebe regularmente si hay actualizaciones de la CLI de Azure, que normalmente se actualiza mensualmente.

Para comprobar la versión de la CLI de Azure que está instalada en el equipo, ejecute este comando:

```console
az --version
```
 
## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-powershell"></a><a name="a876ee7b-43b4-4782-aa5f-73753b6af0ea"></a>Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con PowerShell
 
La nueva extensión de VM para SAP usa una identidad administrada asignada a la máquina virtual para acceder a los datos de supervisión y configuración de la máquina virtual. Para instalar la nueva extensión de Azure para SAP mediante PowerShell, primero debe asignar esa identidad a la máquina virtual y concederle acceso a todos los recursos que usa esa máquina virtual, por ejemplo, discos e interfaces de red.

> [!NOTE]
> En los pasos siguientes se necesitan privilegios de propietario para el grupo de recursos o en recursos individuales (máquinas virtuales, discos de datos e interfaces de red).

1. Asegúrese de usar el agente del host 7.21 PL 47 o posterior.
1. Asegúrese de desinstalar la versión estándar de la extensión de VM para SAP. No se admite la instalación de ambas versiones de la extensión de VM para SAP en la misma máquina virtual.
1. Asegúrese de tener instalada la versión más reciente del cmdlet de Azure PowerShell (al menos 4.3.0). Para más información, consulte [Implementación de cmdlets de Azure PowerShell][deployment-guide-4.1].
1. Ejecute el siguiente cmdlet de PowerShell.
    Para ver la lista de entornos disponibles, ejecute el cmdlet `Get-AzEnvironment`. Si desea usar una instancia global de Azure, el entorno es **AzureCloud**. Para Azure China 21Vianet, seleccione **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```
 
## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-azure-cli"></a><a name="fa4428b9-bed6-459a-9dfb-74cc27454481"></a>Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con la CLI de Azure
 
La nueva extensión de VM para SAP usa una identidad administrada asignada a la máquina virtual para acceder a los datos de supervisión y configuración de la máquina virtual.

> [!NOTE]
> En los pasos siguientes se necesitan privilegios de propietario para el grupo de recursos o en recursos individuales (máquinas virtuales, discos de datos, etc.)

1. Asegúrese de usar SAP Host Agent 7.21 PL 47 o posterior.
1. Asegúrese de desinstalar la versión actual de la extensión de VM para SAP. No puede instalar las dos versiones de la extensión de VM para SAP en la misma máquina virtual. 
1. Instale la versión más reciente de la [CLI de Azure 2.0][azure-cli-2] (versión 2.19.1 o posterior).
1. Inicie sesión con su cuenta de Azure:

   ```azurecli
   az login
   ```

1. Instale la extensión AEM de la CLI de Azure. Asegúrese de que usa la versión 0.2.2 o posterior.
  
   ```azurecli
   az extension add --name aem
   ```
  
1. Habilite la nueva extensión:
  
   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> --install-new-extension
   ```
 
 
## <a name="readiness-check"></a><a name="5774c1db-1d3c-4b34-8448-3afd0b0f18ab"></a>Comprobación de preparación

Con esta comprobación, se asegura de que la extensión de Azure para SAP subyacente proporcione todas las métricas que aparecen dentro de la aplicación SAP.

### <a name="run-the-readiness-check-on-a-windows-vm"></a>Ejecución de la comprobación de la preparación en una máquina virtual Windows

1. Inicie sesión en la máquina virtual de Azure (no se necesita una cuenta de administrador).
1. Abra un explorador web y vaya a http://127.0.0.1:11812/azure4sap/metrics.
1. El explorador debe mostrar o descargar un archivo XML con los datos de supervisión de la máquina virtual. Si no lo hace, asegúrese de que la extensión de Azure para SAP esté instalada.
1. Compruebe el contenido del archivo XML. El archivo XML al que se puede acceder desde http://127.0.0.1:11812/azure4sap/metrics contiene todos los contadores de rendimiento de Azure completados para SAP. También contiene un resumen y un indicador de mantenimiento del estado de la extensión de Azure para SAP.
1. Compruebe el valor del elemento **Provider Health Description** (Descripción de mantenimiento del proveedor). Si el valor no es **Aceptar**, siga las instrucciones del capítulo [Comprobaciones de estado][health-check].
 
### <a name="run-the-readiness-check-on-a-linux-vm"></a>Ejecución de la comprobación de la preparación en una máquina virtual Linux

1. Conéctese a la máquina virtual de Azure mediante SSH.
1. Compruebe la salida del siguiente comando.
   ```bash
   curl http://127.0.0.1:11812/azure4sap/metrics
   ```
   **Resultado esperado**: Devuelve un documento XML que contiene la información de supervisión de la máquina virtual, sus discos e interfaces de red.
   1. Conéctese a la máquina virtual de Azure mediante SSH.

1. Compruebe la salida del siguiente comando.

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **Resultado esperado**: Devuelve un documento XML que contiene la información de supervisión de la máquina virtual, sus discos e interfaces de red.

Si la comprobación anterior no fue correcta, ejecute estas comprobaciones adicionales:

1. Asegúrese de que waagent esté instalado y habilitado.

   a.  Ejecute `sudo ls -al /var/lib/waagent/`:

     **Resultado esperado**: muestra el contenido del directorio de waagent.

   b.  Ejecute `ps -ax | grep waagent`:

   **Resultado esperado**: muestra una entrada similar a: `python /usr/sbin/waagent -daemon`.

1. Asegúrese de que la extensión de Azure para SAP esté instalada y en ejecución.

   a.  Ejecute `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'`:

   **Resultado esperado**: muestra el contenido del directorio de la extensión de Azure para SAP.

   b. Ejecute `ps -ax | grep AzureEnhanced`:

   **Resultado esperado**: muestra una entrada similar a: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`.

1. Instale SAP Host Agent tal y como se describe en la nota de SAP [1031096], y compruebe la salida de `saposcol`.

   a.  Ejecute `/usr/sap/hostctrl/exe/saposcol -d`:

   b.  Ejecute `dump ccm`:

   c.  Compruebe si la métrica **Virtualization_Configuration\Enhanced Monitoring Access** es **true**.

Si ya tiene instalado un servidor de aplicaciones de SAP NetWeaver ABAP, abra la transacción ST06 y compruebe si la supervisión está habilitada.

Si se produce un error en alguna de estas comprobaciones o necesita información detallada acerca de cómo volver a implementar la extensión, consulte [Solución de problemas de Windows][troubleshoot-windows] o [Solución de problemas de Linux][troubleshoot-linux].
 
## <a name="health-checks"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Comprobaciones de estado

Si parte de los datos de infraestructura no se entregan correctamente según lo indicado en las pruebas que se describen en [Comprobación de preparación][readiness-check], ejecute las comprobaciones de estado que se describen en este capítulo para comprobar si la configuración de la infraestructura de Azure y la extensión de Azure para SAP se han configurado correctamente.

### <a name="health-checks-using-powershell"></a>Comprobaciones de estado mediante PowerShell

1. Asegúrese de tener instalada la versión más reciente del cmdlet de Azure PowerShell, tal y como se describe en [Implementación de cmdlets de Azure PowerShell][deployment-guide-4.1].
1. Ejecute el siguiente cmdlet de PowerShell. Para ver la lista de entornos disponibles, ejecute el cmdlet `Get-AzEnvironment`. Para usar Azure global, seleccione el entorno **AzureCloud**. Para Azure China 21Vianet, seleccione **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```
1. El script comprueba la configuración de la máquina virtual que ha seleccionado.

Asegúrese de que todos los resultados de la comprobación del estado de mantenimiento sean **OK**. Si algunas comprobaciones no muestran **Aceptar**, ejecute el cmdlet de actualización como se describe en [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con la CLI de Azure][configure-linux] o [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con PowerShell][configure-windows]. Repita las comprobaciones descritas en [Comprobación de preparación][readiness-check] y este capítulo. Si las comprobaciones siguen indicando problemas en algunos contadores o en todos ellos, consulte [Solución de problemas de Linux][troubleshoot-linux] o [Solución de problemas de Windows][troubleshoot-windows].

### <a name="health-checks-using-azure-cli"></a>Comprobaciones de estado mediante la CLI de Azure

Para ejecutar la comprobación de estado de la extensión de máquina virtual de Azure para SAP mediante la CLI de Azure:
 
1. Instale la [CLI de Azure 2.0][azure-cli-2]. Asegúrese de usar al menos la versión 2.19.1 o posterior (utilice la más reciente). 
1. Inicie sesión con su cuenta de Azure:
   ```azurecli
   az login
   ```

1. Instale la extensión AEM de la CLI de Azure. Asegúrese de que usa la versión 0.2.2 o posterior.
   ```azurecli
   az extension add --name aem
   ```

1. Compruebe la instalación de la extensión: 
   ```azurecli
   az vm aem verify -g <resource-group-name> -n <vm name> 
   ```
El script comprueba la configuración de la máquina virtual que seleccione.

Asegúrese de que todos los resultados de la comprobación del estado de mantenimiento sean **OK**. Si algunas comprobaciones no muestran **Aceptar**, ejecute el cmdlet de actualización como se describe en [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con la CLI de Azure][configure-linux] o [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con PowerShell][configure-windows]. Repita las comprobaciones descritas en [Comprobación de preparación][readiness-check] y este capítulo. Si las comprobaciones siguen indicando problemas en algunos contadores o en todos ellos, consulte [Solución de problemas de Linux][troubleshoot-linux] o [Solución de problemas de Windows][troubleshoot-windows].


## <a name="troubleshooting-for-windows"></a><a name="dee9099b-7b8a-4cdd-86a2-3f6ee964266f"></a>Solución de problemas de Windows
 
### <a name="azure-performance-counters-do-not-show-up-at-all"></a>No aparecen contadores de rendimiento de Azure
El proceso AzureEnhancedMonitoring recopila métricas de rendimiento en Azure. Si el proceso no se está ejecutando en la VM, no se recopilará ninguna métrica de rendimiento.

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>El directorio de instalación de la extensión de Azure para SAP está vacío
##### <a name="issue"></a>Incidencia
El directorio de instalación C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;versión> está vacío.
##### <a name="solution"></a>Solución
La extensión no está instalada. Determine si se trata de un problema de proxy (como se describió anteriormente). Es posible que tenga que reiniciar la máquina o volver a instalar la extensión de VM.
 
### <a name="some-azure-performance-counters-are-missing"></a>Faltan algunos contadores de rendimiento de Azure

El proceso AzureEnhancedMonitoring de Windows recopila métricas de rendimiento en Azure. El proceso obtiene datos de varios orígenes. Algunos datos de configuración se recopilan localmente, y algunas métricas de rendimiento se leen de Azure Monitor.

Si la solución de problemas de la nota de SAP [1999351] no resuelve el problema, abra un mensaje de servicio al cliente de SAP acerca del componente BC-OP-NT-AZR para Windows o BC-OP-LNX-AZR para una máquina virtual Linux. Adjunte el archivo de registro C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;versión>\\logapp.txt al incidente.

## <a name="troubleshooting-for-linux"></a><a name="02783aa4-5443-43f5-bc11-7af19ebf0c36"></a>Solución de problemas de Linux

### <a name="azure-performance-counters-do-not-show-up-at-all"></a>No aparecen contadores de rendimiento de Azure
Un demonio recopila las métricas de rendimiento en Azure. Si el demonio no se está ejecutando, no se recopilará ninguna métrica de rendimiento.

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>El directorio de instalación de la extensión de Azure para SAP está vacío
##### <a name="issue"></a>Incidencia
El directorio /var/lib/waagent/ no contiene un subdirectorio para la extensión de Azure para SAP.
##### <a name="solution"></a>Solución
La extensión no está instalada. Determine si se trata de un problema de proxy (como se describió anteriormente). Es posible que tenga que reiniciar la máquina o volver a instalar la extensión de VM.
 
### <a name="some-azure-performance-counters-are-missing"></a>Faltan algunos contadores de rendimiento de Azure

Un demonio recopila las métricas de rendimiento en Azure, y obtiene datos de varios orígenes. Algunos datos de configuración se recopilan localmente y algunas métricas de rendimiento se leen de Azure Monitor.
Para ver una lista completa y actualizada de los problemas conocidos, consulte la nota de SAP [1999351], que contiene información adicional sobre la solución de problemas de la extensión de Azure para SAP.
Si la solución de problemas descrita en la nota de SAP [1999351] no resuelve el problema, vuelva a instalar la extensión tal y como se describe en [Configuración de la extensión de Azure para SAP][configure-windows]. Si el problema continúa, abra un mensaje de servicio al cliente de SAP acerca del componente BC-OP-NT-AZR para una máquina virtual Windows o BC-OP-LNX-AZR para una máquina virtual Linux. Adjunte el archivo de registro /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-&lt;version>/logapp.txt al incidente.


## <a name="azure-extension-error-codes"></a>Códigos de error de extensión de Azure
 
Todos los identificadores de error tienen una etiqueta única en forma de a-#, donde # es un número. Permite una búsqueda rápida de un error específico y posibles soluciones.
 
| Id. de error | Descripción del error | Soluciones |
|---|---|---|
| `a-0116` | sin token de autenticación | Más información:<br />La extensión no puede obtener el token de autenticación para acceder a las métricas de máquina virtual en Azure Monitor. Para entregar métricas de máquina virtual, necesita acceso a recursos de máquina virtual, como la propia máquina virtual, todos los discos y todas las NIC conectadas a una máquina virtual.<br />Solución:<br />Habilite la identidad administrada de la máquina virtual y asígnele un rol de lector para un grupo de recursos de máquina virtual. Cuando se usa un script de instalación, el script lo hace por usted. Normalmente no es necesario habilitar y asignar manualmente la identidad administrada de la máquina virtual. |

## <a name="next-steps"></a>Pasos siguientes
* [Implementación de Azure Virtual Machines para SAP NetWeaver](./deployment-guide.md)
* [Implementación y planeamiento de Azure Virtual Machines para SAP NetWeaver](./planning-guide.md)
