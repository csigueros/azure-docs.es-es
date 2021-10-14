---
title: Archivo de inclusión
description: archivo de inclusión
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 07/08/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: a39b49c8a75f8ffbf99b3d92917c8e5e3e0cae55
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585271"
---
**Requisitos del servidor de procesos y configuración**


## <a name="hardware-requirements"></a>Requisitos de hardware

**Componente** | **Requisito** 
--- | ---
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | 3, incluidos el disco del sistema operativo, el disco de memoria caché del servidor de procesos, la unidad de retención para la conmutación por recuperación 
Espacio libre en el disco (caché del servidor de procesos) | 600 GB
Espacio libre en el disco (disco de retención) | 600 GB
 | 

## <a name="software-requirements"></a>Requisitos de software

**Componente** | **Requisito** 
--- | ---
Sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016
Configuración regional del sistema operativo | Inglés (en-*)
Roles de Windows Server | No habilite estos roles: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Directivas de grupo | No habilite estas directivas de grupo: <br> - Impedir el acceso al símbolo del sistema. <br> - Impedir el acceso a herramientas de edición del Registro. <br> - Confiar en la lógica de datos adjuntos de archivos. <br> - Activar la ejecución de scripts. <br> [Más información](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Ningún sitio web predeterminado debe existir previamente <br> - Ningún sitio web o aplicación que escuche en el puerto 443 deben existir previamente <br>- Habilitar la [autenticación anónima](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Habilitar la configuración de [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 
FIPS (Estándar federal de procesamiento de información) | No habilitar el modo FIPS
|

## <a name="network-requirements"></a>Requisitos de red

**Componente** | **Requisito** 
--- | --- 
Tipo de dirección IP | estática 
Puertos | 443 (orquestación del canal de control)<br>9443 (Transporte de datos) 
Tipo de NIC | VMXNET3 (si el servidor de configuración es una máquina virtual de VMware)
 |
**Acceso a Internet** (El servidor necesita acceder a estas direcciones URL, directamente o mediante proxy):|
\*.backup.windowsazure.com | Se usa para la transferencia y coordinación de datos replicados
\*blob.core.windows.net | Se usa para tener acceso a la cuenta de almacenamiento que almacena los datos replicados. Puede proporcionar la dirección URL específica de la cuenta de almacenamiento en caché.
\*.hypervrecoverymanager.windowsazure.com | Se usa para las operaciones de administración de replicación y coordinación
https:\//login.microsoftonline.com | Se usa para las operaciones de administración de replicación y coordinación 
time.nist.gov | Se usan para comprobar la sincronización de la hora entre el sistema y la hora global.
time.windows.com | Se usan para comprobar la sincronización de la hora entre el sistema y la hora global.
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> *.services.visualstudio.com (opcional) </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | El programa de instalación de OVF necesita acceso a estas direcciones URL adicionales. Azure Active Directory las usa para la administración de identidades y control de acceso.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Para completar la descarga de MySQL. </br> En algunas regiones, la descarga podría redirigirse a la dirección URL de la red CDN. Asegúrese de que la dirección URL de la red CDN también esté aprobada, si es necesario.
|

> [!NOTE]
> Si tiene [conexión mediante vínculos privados](../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md) al almacén de Site Recovery, no necesita ningún acceso a Internet adicional para el servidor de configuración. Una excepción a este hecho se produce durante la configuración de la máquina de CS mediante la plantilla OVA ya que necesitará acceder a las siguientes direcciones URL por encima de los accesos de vínculo privado https://management.azure.com, https://www.live.com y https://www.microsoft.com. Si no desea permitir el acceso a estas direcciones URL, configure el CS con un instalador unificado.

> [!NOTE]
> Al configurar puntos de conexión privados para proteger máquinas físicas y de VMware, deberá instalar MySQL en el servidor de configuración manualmente. Siga [estos pasos](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server#configure-settings) para realizar la instalación manual. 

## <a name="required-software"></a>Requisitos de software

**Componente** | **Requisito** 
--- | ---
VMware vSphere PowerCLI | No es necesario para las versiones 9.14 y superiores.
MYSQL | Se debe instalar MySQL. Lo puede instalar manualmente o Site Recovery puede instalarlo. (Para más información, vaya a la [definición de configuración](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings))
|

## <a name="sizing-and-capacity-requirements"></a>Requisitos de capacidad y tamaño

En la tabla siguiente se resumen los requisitos de capacidad del servidor de configuración. Si va a replicar varias máquinas virtuales de VMware, consulte las [consideraciones de planeamiento de capacidad](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) y ejecute la herramienta [Azure Site Recovery Deployment Planner](../articles/site-recovery/site-recovery-deployment-planner.md).


**CPU** | **Memoria** | **Disco de caché** | **Frecuencia de cambio de datos** | **Máquinas replicadas**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 sockets * 4 núcleos \@ 2,5 GHz | 16 GB | 300 GB | 500 GB o menos | < 100 máquinas
12 vCPU<br/><br/> 2 socks * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB - 1 TB | De 100 a 150 máquinas
16 vCPU<br/><br/> 2 socks * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1 - 2 TB | De 150 a 200 máquinas
|