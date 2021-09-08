---
title: Actualización del sistema operativo para SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Obtenga información sobre cómo realizar la actualización del sistema operativo para SAP HANA en Azure (instancias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a7f066a350eae18b9c847d206310cfc3ab8bd57
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112913390"
---
# <a name="operating-system-upgrade"></a>Actualización del sistema operativo
En este artículo, se describen los detalles de las actualizaciones del sistema operativo (SO) en HANA Instancias grandes (HLI), también conocidas como infraestructura baremetal.

>[!NOTE]
>La actualización del sistema operativo es su responsabilidad. El soporte técnico de operaciones de Microsoft puede guiarle en las áreas clave de la actualización, pero consulte también al proveedor del sistema operativo al planear una actualización.

Durante el aprovisionamiento de HLI, el equipo de operaciones de Microsoft instala el sistema operativo.
Es necesario que mantenga el sistema operativo. Por ejemplo, debe realizar la aplicación de revisiones, la optimización y la actualización, entre otras, en HLI. Antes de realizar cambios importantes en el sistema operativo, por ejemplo, la actualización de SP1 a SP2, abra una incidencia de soporte técnico para ponerse en contacto con el equipo de operaciones de Microsoft. De este modo, podrán consultarle. Se recomienda abrir esta incidencia de soporte técnico al menos una semana antes de la actualización. 

El vale incluye:

* El id. de suscripción de HLI.
* El nombre del servidor.
* El nivel de revisión que planea aplicar.
* La fecha en la que planea efectuar este cambio. 

Para la matriz de compatibilidad de las distintas versiones de SAP HANA con las diferentes versiones de Linux, consulte la [Nota de SAP 2235581](https://launchpad.support.sap.com/#/notes/2235581).

## <a name="known-issues"></a>Problemas conocidos

Hay un par de problemas conocidos con la actualización:
- En la clase de SKU de tipo II, se quita Software Foundation Server (SFS) durante la actualización del sistema operativo. Tendrá que reinstalar la versión de SFS compatible después de finalizar la actualización del sistema operativo.
- Se produce una reversión de los controladores de la tarjeta Ethernet (ENIC y FNIC) a una versión anterior. Después de la actualización, deberá volver a instalar la versión compatible de los controladores.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Configuración recomendada de SAP HANA (instancias grandes) (tipo I)

La configuración del sistema operativo puede desviarse de la configuración recomendada a lo largo del tiempo. Esta desviación se puede producir debido a la aplicación de revisiones, las actualizaciones del sistema y otros cambios que pueda realizar. Microsoft identifica las actualizaciones necesarias para garantizar que HANA Instancias grandes esté configurado de forma óptima para conseguir el mejor rendimiento y resistencia. En las instrucciones siguientes, se describen las recomendaciones dirigidas al rendimiento de la red, la estabilidad del sistema y el rendimiento óptimo de HANA.

### <a name="compatible-enicfnic-driver-versions"></a>Versiones compatibles del controlador eNIC/fNIC
  Para conseguir el rendimiento de red y la estabilidad del sistema adecuados, asegúrese de tener instalada la versión adecuada específica del sistema operativo de los controladores eNIC y fNIC según la siguiente tabla de compatibilidad. Los servidores se entregan a los clientes con versiones compatibles. Sin embargo, los controladores se pueden revertir a las versiones predeterminadas durante la aplicación de revisiones del sistema operativo o del kernel. Asegúrese de que se ejecute la versión del controlador adecuada después de las operaciones de aplicación de revisiones del sistema operativo o del kernel.
       
      
  |  Fabricante del sistema operativo    |  Versión del paquete del sistema operativo     |  Versión de firmware  |  Controlador eNIC |  Controlador fNIC | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3i           |  4.0.0.6     |   2.0.0.60   |  
  |   SuSE        |  SLES 12 SP4            |   4.1.1b           |  4.0.0.6     |   2.0.0.60   |  
  |   SuSE        |  SLES 12 SP5            |   3.2.3i           |  4.0.0.8     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP5            |   4.1.1b           |  4.0.0.6     |   2.0.0.59   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7.6               |   3.2.3i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7.6               |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Comandos para actualizar controladores y limpiar paquetes RPM antiguos

#### <a name="command-to-check-existing-installed-drivers"></a>Comando para comprobar los controladores instalados existentes
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Eliminación del RPM de eNIC/fNIC existente
```
rpm -e <old-rpm-package>
```
#### <a name="install-recommended-enicfnic-driver-packages"></a>Instalación de los paquetes de controladores eNIC/fNIC recomendados
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-installation"></a>Comandos para confirmar la instalación
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>Pasos para la instalación de controladores eNIC/fNIC durante la actualización del sistema operativo

* Actualizar la versión del sistema operativo
* Quitar los paquetes RPM antiguos
* Instalar los controladores eNIC/fNIC compatibles según la versión del sistema operativo instalada
* Reiniciar el sistema
* Después del reinicio, comprobar la versión de eNIC/fNIC.


### <a name="suse-hlis-grub-update-failure"></a>Error de actualización de GRUB de unidades HLI de SuSE
HANA (instancias grandes) de SAP en Azure (tipo 1) puede encontrase en un estado de no arranque tras la actualización. El procedimiento siguiente soluciona este problema.

#### <a name="execution-steps"></a>Pasos de ejecución

-   Ejecute el comando `multipath -ll`.
-   Obtenga el identificador del número de unidad lógica (LUN) o use el comando: `fdisk -l | grep mapper`
-   Actualice el archivo `/etc/default/grub_installdevice` con la línea `/dev/mapper/<LUN ID>`. Ejemplo: /dev/mapper/3600a09803830372f483f495242534a56

>[!NOTE]
>El identificador del LUN varía de un servidor a otro.


### <a name="disable-error-detection-and-correction"></a>Deshabilitación de la detección y corrección de errores 
   Los módulos de detección y corrección de errores (EDAC) ayudan a detectar y corregir errores de memoria. Sin embargo, el hardware de tipo I de HLI subyacente ya detecta y corrige los errores de memoria. Habilitar la misma característica en los niveles de hardware y sistema operativo puede producir conflictos y provocar apagados no planeados del servidor. Se recomienda deshabilitar los módulos EDAC del sistema operativo.

#### <a name="execution-steps"></a>Pasos de ejecución

- Compruebe si los módulos EDAC están habilitados. Si el comando siguiente devuelve una salida, los módulos están habilitados.

```
lsmod | grep -i edac 
```
- Para deshabilitar los módulos, anexe las líneas siguientes al archivo. `/etc/modprobe.d/blacklist.conf`.
```
blacklist sb_edac
blacklist edac_core
```
Es necesario un reinicio para que los cambios tengan efecto. Después del reinicio, vuelva a ejecutar el comando `lsmod` y compruebe que los módulos no estén habilitados.

### <a name="kernel-parameters"></a>Parámetros de kernel
Asegúrese de que se aplique el valor correcto para `transparent_hugepage`, `numa_balancing`, `processor.max_cstate`, `ignore_ce` y `intel_idle.max_cstate`.

* intel_idle.max_cstate=1
* processor.max_cstate=1
* transparent_hugepage=never
* numa_balancing=disable
* mce=ignore_ce

#### <a name="execution-steps"></a>Pasos de ejecución

- Agregue estos parámetros a la línea `GRB_CMDLINE_LINUX` del archivo `/etc/default/grub`:

```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
- Cree un archivo GRUB.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
- Reinicie el sistema.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo configurar un servidor SMT para SUSE Linux.

> [!div class="nextstepaction"]
> [Configuración de servidor SMT para SUSE Linux](hana-setup-smt.md)