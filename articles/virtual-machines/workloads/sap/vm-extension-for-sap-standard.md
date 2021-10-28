---
title: Versión estándar de la extensión de máquina virtual de Azure para soluciones de SAP | Microsoft Docs
description: Aprenda a implementar la extensión de máquina virtual estándar para SAP.
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
ms.openlocfilehash: 8e723e39364207e1075d66eeafcdf12b74e16624
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261393"
---
# <a name="standard-version-of-azure-vm-extension-for-sap-solutions"></a>Versión estándar de la extensión de máquina virtual de Azure para soluciones de SAP
[new-extension]:vm-extension-for-sap-new.md (Nueva versión de la extensión de máquina virtual de Azure para soluciones de SAP)
[configure-windows]:vm-extension-for-sap-standard.md#cb095b34-51c9-41f3-aeed-30a16072a1dc (Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con PowerShell)
[configure-linux]:vm-extension-for-sap-standard.md#c691e304-3524-4bfd-8612-992d5715a689 (Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con la CLI de Azure)
[configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[troubleshoot-windows]:vm-extension-for-sap-standard.md#f566b77e-5427-451b-b4ca-6b93114d51e8 (Solución de problemas de Windows)
[troubleshoot-linux]:vm-extension-for-sap-standard.md#a4dae567-e7fd-4d4b-8279-510b8e5fae4a (Solución de problemas de Linux)
[healthcheck]:vm-extension-for-sap-standard.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Comprobación del estado de la configuración de la extensión de Azure para SAP)
[deployment-guide-4.1]:vm-extension-for-sap-standard.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Implementación de cmdlets de Azure PowerShell)
[deploy-cli]:vm-extension-for-sap-standard.md#1ded9453-1330-442a-86ea-e0fd8ae8cab3 (Implementación de la CLI de Azure)
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[azure-cli-2]:/cli/azure/install-azure-cli
[msdn-set-Azvmaemextension]:/powershell/module/az.compute/set-azvmaemextension
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[readiness-check]:vm-extension-for-sap-standard.md#6b2561a0-b476-4579-847d-6c68117eb26e (Comprobación de preparación)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Recursos SAP)
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[deployment-guide-contact-support]:vm-extension-for-sap-standard.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (Solución de problemas de la extensión de Azure para SAP: póngase en contacto con el soporte técnico)
[deployment-guide-run-the-script]:vm-extension-for-sap-standard.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (Solución de problemas de la extensión de Azure para SAP: ejecute el script de configuración)
[deployment-guide-redeploy-after-sysprep]:vm-extension-for-sap-standard.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (Solución de problemas de la extensión de Azure para SAP: repita la implementación después de sysprep)
[deployment-guide-fix-internet-connection]:vm-extension-for-sap-standard.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 (Solución de problemas de la extensión de Azure para SAP: corrija los problemas de conexión a Internet)



## <a name="prerequisites"></a>Requisitos previos

> [!NOTE]
> Instrucción de soporte técnico general: la asistencia correspondiente a la extensión de Azure para SAP se proporciona a través de los canales de soporte técnico de SAP.
> Si necesita ayuda con la extensión de máquina virtual de Azure para soluciones de SAP, abra un caso de soporte técnico con Soporte técnico de SAP.
  
> [!NOTE]
> Asegúrese de desinstalar la extensión de máquina virtual antes de cambiar entre la versión estándar y la nueva de la extensión de Azure para SAP.

> [!NOTE]
> Hay dos versiones de la extensión de VM. En este artículo se trata la versión **estándar** de la extensión de máquina virtual de Azure para SAP. Para instrucciones sobre cómo instalar la versión nueva, consulte [Versión nueva de la extensión de máquina virtual de Azure para soluciones de SAP][new-extension].


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


## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-powershell"></a><a name="cb095b34-51c9-41f3-aeed-30a16072a1dc"></a>Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con PowerShell 
 
Para instalar la extensión de Azure para SAP mediante PowerShell:

1. Asegúrese de tener instalada la versión más reciente del cmdlet de Azure PowerShell. Para más información, consulte [Implementación de cmdlets de Azure PowerShell][deployment-guide-4.1].
1. Ejecute el siguiente cmdlet de PowerShell. Para ver la lista de entornos disponibles, ejecute el cmdlet `Get-AzEnvironment`. Si desea usar una instancia global de Azure, el entorno es **AzureCloud**. Para Azure China 21Vianet, seleccione **AzureChinaCloud**. 
   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   
   Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> 
   ``` 
Después de proporcionar los datos de la cuenta, el script implementa las extensiones requeridas y habilita las funciones necesarias. Esto puede tardar varios minutos.
Para más información sobre `Set-AzVMAEMExtension`, consulte [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Ejecución correcta del cmdlet de Azure específico de SAP Set-AzVMAEMExtension][deployment-guide-figure-900]

La configuración `Set-AzVMAEMExtension` realiza todos los pasos para configurar la recopilación de datos de host para SAP.

La salida del script incluye la información siguiente:

* Confirmación de que se ha configurado la recopilación de datos para el disco del SO y todos los discos de datos adicionales.
* Los próximos dos mensajes confirman la configuración de las métricas de almacenamiento de una cuenta de almacenamiento específica.
* Una línea de la salida muestra el estado de la actualización real de la configuración de la extensión de máquina virtual para SAP.
* Otra línea de la salida confirma que la configuración se implementó o se actualizó.
* La última línea de la salida es informativa. Muestra las opciones para probar la configuración de la extensión de máquina virtual para SAP.
* Para comprobar que todos los pasos de la configuración de la extensión de máquina virtual de Azure para SAP se ejecutaron correctamente y que la infraestructura de Azure proporciona los datos necesarios, compruebe si la extensión de Azure para SAP está preparada, tal como se describe en [Comprobación de preparación][readiness-check].
* Espere de 15 a 30 minutos para que Diagnósticos de Azure recopile los datos pertinentes.

## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-azure-cli"></a><a name="c691e304-3524-4bfd-8612-992d5715a689"></a>Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con la CLI de Azure
 
Para instalar la extensión de máquina virtual de Azure para SAP mediante la CLI de Azure:

1. Asegúrese de tener instalada la versión más reciente de la CLI de Azure. Para más información, consulte [Implementación de la CLI de Azure][deploy-cli]. 

1. Inicie sesión con su cuenta de Azure:
   ```azurecli
   az login
   ```

1. Instale la extensión AEM de la CLI de Azure. Asegúrese de que usa la versión 0.2.2 o posterior.
   ```azurecli
   az extension add --name aem
   ```

1. Habilite la extensión: 
   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> 
   ```

1. Compruebe que la extensión de Azure para SAP esté activa en la máquina virtual Linux de Azure. Compruebe si existe el archivo /var/lib/AzureEnhancedMonitor/PerfCounters. Si existe, en un símbolo del sistema, ejecute este comando para mostrar la información recopilada por la extensión de Azure para SAP:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   El resultado tendrá un aspecto similar al siguiente:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Actualización de la configuración de la extensión de Azure para SAP

Actualice la configuración de la extensión de Azure para SAP en cualquiera de estos escenarios:
* El equipo conjunto de Microsoft y SAP extiende las funcionalidades de la extensión de máquina virtual y solicita más o menos contenedores.
* Microsoft presenta una versión nueva de la infraestructura de Azure subyacente que brinda los datos y la extensión de Azure para SAP debe adaptarse a esos cambios.
* Puede agregar más discos de datos adicionales a la máquina virtual de Azure, o bien quitar uno. En este caso, actualice la colección de datos relacionados con el almacenamiento. Agregar o eliminar puntos de conexión o asignar direcciones IP a una máquina virtual no afecta a la configuración de la extensión.
* Cambia el tamaño de la máquina virtual de Azure, por ejemplo, de A5 a cualquier otro tamaño de máquina virtual.
* Agrega interfaces de red nuevas a la máquina virtual de Azure.

Para actualizar los valores, debe actualizar la configuración de la extensión de Azure para SAP con los pasos descritos en [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con la CLI de Azure][configure-linux] o [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con PowerShell][configure-windows].

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Comprobaciones y solución de problemas

Después de implementar la máquina virtual de Azure y de configurar la extensión de Azure para SAP correspondiente, compruebe si todos los componentes de la extensión funcionan según lo previsto.

Ejecute la comprobación de preparación de la extensión de Azure para SAP tal como se describe en [Comprobación de preparación][readiness-check]. Si todos los resultados de la comprobación de la preparación son positivos y todos los contadores de rendimiento pertinentes son correctos, la extensión de Azure para SAP se configuró correctamente. Puede continuar con la instalación de SAP Host Agent, tal y como se describe en las notas de SAP en [Recursos de SAP][deployment-guide-2.2]. Si la comprobación de la preparación indica que faltan contadores, ejecute la comprobación de estado de la extensión de Azure para SAP, tal como se describe en [Comprobación del estado de la configuración de la extensión de Azure para SAP][healthcheck]. Para más opciones de solución de problemas, consulte [Solución de problemas de Windows][troubleshoot-windows] o [Solución de problemas de Linux][troubleshoot-linux].

### <a name="readiness-check"></a><a name="6b2561a0-b476-4579-847d-6c68117eb26e"></a>Comprobación de preparación

Con esta comprobación, se asegura de que la extensión de Azure para SAP subyacente proporcione todas las métricas que aparecen dentro de la aplicación SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Ejecución de la comprobación de la preparación en una máquina virtual Windows
 
1. Inicie sesión en la máquina virtual de Azure (no se necesita una cuenta de administrador).
1. Abra una ventana de símbolo del sistema.
1. En el símbolo del sistema, cambie el directorio a la carpeta de instalación de la extensión de Azure para SAP: C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;versión>\\drop.

   La *versión* que aparece en la ruta de acceso a la extensión puede variar. Si ve carpetas de varias versiones de la extensión en la carpeta de instalación, revise la configuración del servicio de Windows AzureEnhancedMonitoring y cambie a la carpeta indicada como *Ruta de acceso al ejecutable*.

   ![Propiedades del servicio que ejecuta la extensión de Azure para SAP][deployment-guide-figure-1000]

1. En el símbolo del sistema, ejecute **azperflib.exe** sin ningún parámetro.

   > [!NOTE]
   > Azperflib.exe se ejecuta en un bucle y actualiza los contadores recopilados cada 60 segundos. Para finalizar el bucle, cierre la ventana de símbolo del sistema.
   >
   >

Si la extensión de Azure para SAP no está instalada, o el servicio AzureEnhancedMonitoring no se está ejecutando, la extensión no se configuró correctamente. Para información detallada sobre cómo solucionar problemas de la extensión, consulte [Solución de problemas de Windows][troubleshoot-windows] o [Solución de problemas de Linux][troubleshoot-linux].

> [!NOTE]
> Azperflib.exe es un componente que no se puede usar para fines propios. Proporciona datos de la infraestructura de Azure relacionados con la VM para SAP Host Agent de manera exclusiva.
> 
##### <a name="check-the-output-of-azperflibexe"></a>Revise la salida del archivo azperflib.exe.
 
La salida del archivo azperflib.exe muestra todos los contadores de rendimiento de Azure para SAP rellenados. En la parte inferior de la lista de los contadores recopilados, encontrará un resumen y un indicador que muestra el estado de la extensión de Azure para SAP.

![Salida de la comprobación del estado mediante la ejecución de azperflib.exe, que indica que no hay problemas][deployment-guide-figure-1100]
<a name="figure-11"></a>

Vea el valor de **Counters total** (Total de contadores) en la salida, que se notifica como vacío, y el valor de **Health status** (Estado de mantenimiento), que se muestra en la ilustración anterior.

Puede interpretar los valores del resultado de la siguiente manera:

| Valores del resultado de azperflib.exe | Estado de mantenimiento de la extensión de Azure para SAP |
| --- | --- |
| **Llamadas a API: no disponible** | Los contadores que no están disponibles puede que no se utilicen con la configuración de las máquinas virtuales o que haya errores. Consulte **Estado de mantenimiento**. |
| **Total de contadores: vacíos** |Los siguientes dos contadores Azure Storage pueden estar vacíos: <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>Todos los demás contadores deben tener valores. |
| **Estado de mantenimiento** |Solo es correcto si el estado del resultado muestra **Correcto**. |
| **Diagnóstico** |Información detallada sobre el estado de mantenimiento. |

Si el valor de **Estado de mantenimiento** no es **Correcto**, siga las instrucciones de [Comprobación del estado de la configuración de la extensión de Azure para SAP][healthcheck].
 
##### <a name="run-the-readiness-check-on-a-linux-vm"></a>Ejecución de la comprobación de la preparación en una máquina virtual Linux

1. Conéctese a la máquina virtual de Azure mediante SSH.
1. Compruebe la salida de la extensión de Azure para SAP.

   a.  Ejecute `more /var/lib/AzureEnhancedMonitor/PerfCounters`:

   **Resultado esperado**: devuelve una lista de contadores de rendimiento. El archivo no debe estar vacío.

   b. Ejecute `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`:

   **Resultado esperado**: devuelve una línea en la que el error es **none**, por ejemplo, **3;config;Error;;0;0;none;0;1456416792;tst-servercs;** .

   c. Ejecute `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`:

   **Resultado esperado**: devuelve un resultado vacío o no existente.

Si la comprobación anterior no fue correcta, ejecute estas comprobaciones adicionales:

1. Asegúrese de que waagent esté instalado y habilitado.

   a.  Ejecute `sudo ls -al /var/lib/waagent/`:

     **Resultado esperado**: muestra el contenido del directorio de waagent.

   b.  Ejecute `ps -ax | grep waagent`:

   **Resultado esperado**: muestra una entrada similar a: `python /usr/sbin/waagent -daemon`.

1. Asegúrese de que la extensión de Azure para SAP esté instalada y en ejecución.

   a.  Ejecute `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`:

   **Resultado esperado**: muestra el contenido del directorio de la extensión de Azure para SAP.

   b. Ejecute `ps -ax | grep AzureEnhanced`:

   **Resultado esperado**: muestra una entrada similar a: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`.

1. Instale SAP Host Agent tal y como se describe en la nota de SAP [1031096], y compruebe la salida de `saposcol`.

   a.  Ejecute `/usr/sap/hostctrl/exe/saposcol -d`:

   b.  Ejecute `dump ccm`:

   c.  Compruebe si la métrica **Virtualization_Configuration\Enhanced Monitoring Access** es **true**.

Si ya tiene instalado un servidor de aplicaciones de SAP NetWeaver ABAP, abra la transacción ST06 y compruebe si la supervisión está habilitada.

Si se produce un error en alguna de estas comprobaciones o necesita información detallada acerca de cómo volver a implementar la extensión, consulte [Solución de problemas de Linux][troubleshoot-linux] o [Solución de problemas de Windows][troubleshoot-windows].

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

1. El script comprueba la configuración de la máquina virtual que seleccione.

   ![Salida de la prueba correcta de la extensión de Azure para SAP][deployment-guide-figure-1300]

Asegúrese de que todos los resultados de la comprobación del estado de mantenimiento sean **OK**. Si algunas comprobaciones no muestran **Aceptar**, ejecute el cmdlet de actualización como se describe en [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con la CLI de Azure][configure-linux] o [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con PowerShell][configure-windows]. Espere 15 minutos y repita las comprobaciones descritas en [Comprobación de preparación][readiness-check] y este capítulo. Si las comprobaciones siguen indicando problemas en algunos contadores o en todos ellos, consulte [Solución de problemas de Linux][troubleshoot-linux] o [Solución de problemas de Windows][troubleshoot-windows].

> [!Note]
> En casos en los que use Azure Managed Disks estándar, puede que experimente algunas advertencias. En lugar de que las pruebas devuelvan "Correcto", se mostrarán advertencias. Esto es normal y se espera en el caso de ese tipo de disco. Consulte también [Solución de problemas de Linux][troubleshoot-linux] o [Solución de problemas de Windows][troubleshoot-windows].
>

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
   ```azurecliw
   az vm aem verify -g <resource-group-name> -n <vm name> 
   ```
El script comprueba la configuración de la máquina virtual que seleccione.

Asegúrese de que todos los resultados de la comprobación del estado de mantenimiento sean **OK**. Si algunas comprobaciones no muestran **Aceptar**, ejecute el cmdlet de actualización como se describe en [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con la CLI de Azure][configure-linux] o [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con PowerShell][configure-windows]. Espere 15 minutos y repita las comprobaciones descritas en [Comprobación de preparación][readiness-check] y este capítulo. Si las comprobaciones siguen indicando problemas en algunos contadores o en todos ellos, consulte [Solución de problemas de Linux][troubleshoot-linux] o [Solución de problemas de Windows][troubleshoot-windows].

## <a name="troubleshooting-for-windows"></a><a name="f566b77e-5427-451b-b4ca-6b93114d51e8"></a>Solución de problemas de Windows
 
### <a name="azure-performance-counters-do-not-show-up-at-all"></a>No aparecen contadores de rendimiento de Azure
El servicio AzureEnhancedMonitoring Windows recopila métricas de rendimiento en Azure. Si el servicio no se instaló correctamente o si no se está ejecutando en la máquina virtual, no se recopilará ninguna métrica de rendimiento.
 
#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>El directorio de instalación de la extensión de Azure para SAP está vacío

##### <a name="issue"></a>Incidencia
El directorio de instalación C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\<version>\drop está vacío.

##### <a name="solution"></a>Solución
La extensión no está instalada. Determine si se trata de un problema de proxy (como se describió anteriormente). Puede que tenga que reiniciar la máquina o volver a ejecutar el script de configuración `Set-AzVMAEMExtension`.

#### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>No existe el servicio para la extensión de Azure para SAP

##### <a name="issue"></a>Incidencia

El servicio de Windows AzureEnhancedMonitoring no existe.

La salida de Azperflib.exe produce un error:

![La ejecución de azperflib.exe indica que el servicio de la extensión de Azure para SAP no está en ejecución][deployment-guide-figure-1400]
<a name="figure-14"></a>
 
##### <a name="solution"></a>Solución

Si el servicio no existe, significa que la extensión de Azure para SAP no se instaló correctamente. Vuelva a implementar la extensión tal como se describe en [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con la CLI de Azure][configure-linux] o [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con PowerShell][configure-windows].

Después de implementar la extensión, vuelva a comprobar si los contadores de rendimiento de Azure aparecen en la máquina virtual de Azure.

#### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>El servicio de la extensión de Azure para SAP existe, pero no se inicia
##### <a name="issue"></a>Incidencia
El servicio de Windows AzureEnhancedMonitoring existe y está habilitado, pero no se inicia. Consulte el registro de eventos de la aplicación para más información.
##### <a name="solution"></a>Solución
La configuración es incorrecta. Reinicie la extensión de Azure para SAP en la máquina virtual, tal como se describe en Configuración de la extensión de Azure para SAP.
 
### <a name="some-azure-performance-counters-are-missing"></a>Faltan algunos contadores de rendimiento de Azure
El servicio AzureEnhancedMonitoring Windows recopila métricas de rendimiento en Azure. El servicio obtiene datos de varios orígenes. Algunos datos de configuración se recopilan localmente, y algunas métricas de rendimiento se leen de Diagnósticos de Azure. Los contadores de almacenamiento proceden de los registros en el nivel de suscripción de almacenamiento.

Si la solución de problemas descrita en la nota de SAP [1999351] no resuelve el problema, vuelva a ejecutar el script de configuración `Set-AzVMAEMExtension`. Puede que tenga que esperar una hora porque los contadores de diagnóstico o análisis de almacenamiento no se crean de inmediato una vez habilitados. Si el problema continúa, abra un mensaje de servicio al cliente de SAP acerca del componente BC-OP-NT-AZR para una máquina virtual Windows o BC-OP-LNX-AZR para una máquina virtual Linux.

## <a name="troubleshooting-for-linux"></a><a name="a4dae567-e7fd-4d4b-8279-510b8e5fae4a"></a>Solución de problemas de Linux

### <a name="azure-performance-counters-do-not-show-up-at-all"></a>No aparecen contadores de rendimiento de Azure
Un demonio recopila las métricas de rendimiento en Azure. Si el demonio no se está ejecutando, no se recopilará ninguna métrica de rendimiento.

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>El directorio de instalación de la extensión de Azure para SAP está vacío
##### <a name="issue"></a>Incidencia
El directorio \\var\\lib\\waagent\\ no contiene un subdirectorio para la extensión de Azure para SAP.
##### <a name="solution"></a>Solución
La extensión no está instalada. Determine si se trata de un problema de proxy (como se describió anteriormente). Puede que tenga que reiniciar la máquina o volver a ejecutar el script de configuración `Set-AzVMAEMExtension`.

#### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>La ejecución de Set-AzVMAEMExtension y Test-AzVMAEMExtension muestra mensajes de advertencia que indican que Managed Disks estándar no se admite.
##### <a name="issue"></a>Incidencia
Al ejecutar Set-AzVMAEMExtension o Test-AzVMAEMExtension, se muestran mensajes como los siguientes:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Cuando ejecuta azperfli.exe como se describió anteriormente, puede obtener un resultado que indica un estado incorrecto.

##### <a name="solution"></a>Solución
Los mensajes se deben a que el servicio Managed Disks estándar no proporciona las API que usa la extensión de Azure para SAP para comprobar las estadísticas de las cuentas de Azure Storage estándar. Esto no es motivo de preocupación. La razón para introducir la recopilación de datos de las cuentas de almacenamiento de discos estándar era limitar las entradas y salidas que se producían con frecuencia. Los discos administrados evitan tal limitación al restringir el número de discos en una cuenta de almacenamiento. Por lo tanto, no es esencial tener ese tipo de datos.

### <a name="some-azure-performance-counters-are-missing"></a>Faltan algunos contadores de rendimiento de Azure

Un demonio recopila las métricas de rendimiento en Azure, y obtiene datos de varios orígenes. Algunos datos de configuración se recopilan localmente, y algunas métricas de rendimiento se leen de Diagnósticos de Azure. Los contadores de almacenamiento proceden de los registros en su suscripción de almacenamiento.

Para ver una lista completa y actualizada de los problemas conocidos, consulte la nota de SAP [1999351], que contiene información adicional sobre la solución de problemas de la extensión de Azure para SAP.

Si la solución de problemas descrita en la nota de SAP [1999351] no resuelve el problema, vuelva a ejecutar el script de configuración `Set-AzVMAEMExtension` tal como se describe en [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con la CLI de Azure][configure-linux] o [Configuración de la extensión de máquina virtual de Azure para soluciones de SAP con PowerShell][configure-windows]. Puede que tenga que esperar una hora porque los contadores de diagnóstico o análisis de almacenamiento no se crean de inmediato una vez habilitados. Si el problema continúa, abra un mensaje de servicio al cliente de SAP acerca del componente BC-OP-NT-AZR para una máquina virtual Windows o BC-OP-LNX-AZR para una máquina virtual Linux.

## <a name="azure-extension-error-codes"></a>Códigos de error de extensión de Azure

| Id. de error | Descripción del error | Solución |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | Falta la configuración de la aplicación. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | No hay ningún identificador de implementación en la configuración de la aplicación. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | No hay ningún valor de RoleInstanceId en la configuración de la aplicación. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | No hay ningún valor de RoleInstanceId en la configuración de la aplicación. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | No se puede leer la configuración de Azure. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | Falta el archivo de configuración de la aplicación. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | No hay ningún tamaño de VM en la configuración de la aplicación. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | Error del contador GlobalMemoryStatusEx. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | Error del contador MaxHwFrequency. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | Error en los contadores de NIC. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | Error en el contador de asignación de disco. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | Error del contador del nombre del procesador. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | Error en el contador de asignación de disco. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | Falta la métrica "Disk type" en el archivo de configuración de la extensión config.xml. "Disk type", junto con otros contadores, se introdujo en la versión 2.2.0.68 el 16/12/2015. Si implementó la extensión antes de 16/12/2015, usa el archivo de configuración anterior. El marco de la extensión de Azure actualiza automáticamente la extensión a una versión más reciente, pero el archivo config.xml permanece inalterado. Para actualizar la configuración, descargue y ejecute el script de configuración de PowerShell más reciente. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | Sin almacenamiento en caché de disco. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | Sin rendimiento de SLA de disco. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | Sin IOPS de SLA de disco. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | Error en el contador de asignación de disco. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | Error del contador de cambios más recientes de hardware. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | Error en los contadores de NIC. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | Debido a sysprep en la VM, el SID de Windows ha cambiado. | [repita la implementación después de sysprep][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>str/007 | Error de acceso al análisis de almacenamiento. <br /><br />Dado que el rellenado de los datos de análisis de almacenamiento en una VM recién creada puede necesitar hasta media hora, el error puede desaparecer después de un tiempo. El error sigue apareciendo, vuelva a ejecutar el script de instalación. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="str_010"></a>str/010 | No hay contadores de Storage Analytics. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="str_009"></a>str/009 | Error de Storage Analytics. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="wad_004"></a>wad/004 | Configuración de WAD incorrecta. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="wad_002"></a>wad/002 | Formato de WAD inesperado. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="wad_001"></a>wad/001 | No se encontró ningún contador de WAD. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/040 | No se encontró ningún contador de WAD. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="wad_003"></a>wad/003 | No se puede leer la tabla de WAD. No existe ninguna conexión con la tabla de WAD. Puede haber varias causas:<br /><br /> 1) configuración obsoleta <br />2) no hay conexión de red con Azure <br />3) problemas con la configuración de WAD | [ejecute el script de instalación][deployment-guide-run-the-script]<br />[repare la conexión a Internet][deployment-guide-fix-internet-connection]<br />[póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="prf_011"></a>prf/011 | Error en las métricas de NIC de Perfmon. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="prf_012"></a>prf/012 | Error en las métricas de disco de Perfmon. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="prf_013"></a>prf/013 | Error en algunas métricas de prefmon. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="prf_014"></a>prf/014 | Error de Perfmon al crear un contador. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | No hay proveedores de métricas configurados. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |
| <a name="str_006"></a>str/006 | Configuración de Storage Analytics incorrecta. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="str_032"></a>str/032 | Error en las métricas de Storage Analytics. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | Error en uno de los proveedores de métricas. | [ejecute el script de instalación][deployment-guide-run-the-script] |
| <a name="str_034"></a>str/034 | Error en un subproceso del proveedor. | [póngase en contacto con el servicio de soporte técnico][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>Instrucciones detalladas sobre las soluciones proporcionadas

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>Ejecute el script de configuración

Siga los pasos descritos en el capítulo [Configuración de la extensión de Azure para SAP][configure-windows] de esta guía para volver a instalar la extensión. Tenga en cuenta que algunos contadores pueden necesitar hasta 30 minutos para el aprovisionamiento.

Si los errores no desaparecen, [póngase en contacto con el soporte técnico][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>Póngase en contacto con el soporte técnico

Hay un error inesperado o no hay ninguna solución conocida. Recopile el archivo AzureEnhancedMonitoring_service.log ubicado en la carpeta C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\drop (Windows) o /var/log/azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux (Linux) y póngase en contacto con el soporte técnico de SAP para recibir más ayuda.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>Repita la implementación después de sysprep

Si tiene previsto compilar una imagen de SO de sysprep generalizada (que puede incluir software de SAP), se recomienda que esta imagen no incluya la extensión de Azure para SAP. Debe instalar la extensión de Azure para SAP una vez implementada la nueva instancia de la imagen de sistema operativo generalizada.

Sin embargo, si la imagen de sistema operativo generalizada y preparada con sysprep ya contiene la extensión de Azure para SAP, puede aplicar la siguiente solución alternativa para volver a configurar la extensión en la instancia de VM recién implementada:

* En la instancia de VM recién implementada, elimine el contenido de las siguientes carpetas:  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\Status

* Siga los pasos descritos en el capítulo [Configuración de la extensión de Azure para SAP][configure-windows] de esta guía para volver a instalar la extensión.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>Repare la conexión a Internet

La máquina virtual de Microsoft Azure que ejecuta la extensión de Azure para SAP requiere acceso a Internet. Si esta VM de Azure forma parte de una red virtual de Azure o de un dominio local, asegúrese de que la configuración de proxy correspondiente esté establecida. Esta configuración también debe ser válida para que la cuenta LocalSystem tenga acceso a Internet. Siga el capítulo [Configuración del proxy][configure-proxy] de esta guía.

Además, si necesita establecer una dirección IP estática para la VM de Azure, no la configure manualmente dentro de la VM de Azure, sino mediante [Azure PowerShell](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-ps.md), la [CLI de Azure](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-cli.md), [Azure Portal](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-pportal.md). La dirección IP estática se propaga mediante el servicio DHCP de Azure.

No se admite la definición manual de una dirección IP estática dentro de la VM de Azure y puede provocar problemas con la extensión de Azure para SAP.


## <a name="next-steps"></a>Pasos siguientes
* [Implementación de Azure Virtual Machines para SAP NetWeaver](./deployment-guide.md)
* [Implementación y planeamiento de Azure Virtual Machines para SAP NetWeaver](./planning-guide.md)