---
title: Alta disponibilidad de SAP NW en máquinas virtuales de Azure que se ejecutan en SLES utilizando NFS en Azure Files| Microsoft Docs
description: Guía de alta disponibilidad de SAP NetWeaver en SUSE Linux Enterprise Server utilizando NFS en Azure Files para aplicaciones SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/16/2021
ms.author: radeltch
ms.openlocfilehash: 3000f5414c51b1ce7842367bb4449c69fa9aadfa
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557208"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-nfs-on-azure-files"></a>Alta disponibilidad de SAP NetWeaver en máquinas virtuales Azure que se ejecutan en SUSE Linux Enterprise Server utilizando NFS en Azure Files  

[dbms-guide]:dbms_guide_general.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[afs-azure-doc]:../../../storage/files/storage-files-introduction.md
[afs-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[2578899]:https://launchpad.support.sap.com/#/notes/2578899
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[2360818]:https://launchpad.support.sap.com/#/notes/2360818


[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-relnotes]:https://www.suse.com/releasenotes/index.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

En este artículo, se explica cómo implementar y configurar máquinas virtuales, cómo instalar el marco del clúster y cómo instalar un sistema SAP NetWeaver de alta disponibilidad utilizando [NFS en Azure Files](../../../storage/files/files-nfs-protocol.md). Las configuraciones de ejemplo usan máquinas virtuales que se ejecutan en SUSE Linux Enterprise Server (SLES).      

## <a name="prerequisites"></a>Requisitos previos  

* [Documentación de Azure Files][afs-azure-doc] 
* Nota de SAP [1928533][1928533], que incluye:  
  * La lista de tamaños de máquina virtual de Azure que se admiten para la implementación del software de SAP
  * Información importante sobre la capacidad para los tamaños de máquina virtual de Azure
  * Software de SAP admitido y combinaciones de sistema operativo y base de datos
  * Versión del kernel de SAP requerida para Windows y Linux en Microsoft Azure
* La nota de SAP [2015553][2015553] enumera los requisitos previos para las implementaciones de software de SAP admitidas por SAP en Azure.
* La nota de SAP [2205917][2205917] contiene configuraciones recomendadas del sistema operativo para SUSE Linux Enterprise Server para SAP Applications
* La nota de SAP [2178632][2178632] contiene información detallada sobre todas las métricas de supervisión notificadas para SAP en Azure.
* La nota de SAP [2191498][2191498] incluye la versión de SAP Host Agent necesaria para Linux en Azure.
* La nota de SAP [2243692][2243692] incluye información acerca de las licencias de SAP en Linux en Azure.
* La nota de SAP [1984787][1984787] incluye información general sobre SUSE Linux Enterprise Server 12.
* La nota de SAP [2578899][2578899] incluye información general sobre SUSE Linux Enterprise Server 15.
* La nota de SAP [1999351][1999351] contiene más soluciones de problemas de la extensión de supervisión mejorada de Azure para SAP.
* La [WIKI de la comunidad SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene todas las notas de SAP que se necesitan para Linux.
* [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide]
* [Implementación de Azure Virtual Machines para SAP en Linux][deployment-guide]
* [Implementación de DBMS de Azure Virtual Machines para SAP en Linux][dbms-guide]
* [Guías de procedimientos recomendados de SUSE SAP HA][suse-ha-guide] Las guías contienen toda la información necesaria para la configuración de Netweaver HA y la replicación del sistema de SAP HANA en el entorno local. Use estas guías como base de referencia general. Proporcionan información mucho más detallada.
* [Notas de la versión de la extensión de alta disponibilidad de SUSE][suse-relnotes]


## <a name="overview"></a>Información general

Para implementar el nivel de aplicación de SAP NetWeaver, necesita directorios compartidos como `/sapmnt/SID` y `/usr/sap/trans` en el entorno. Además, al implementar un sistema SAP de alta disponibilidad, debe proteger y crear sistemas de archivos de alta disponibilidad como `/sapmnt/SID` y `/usr/sap/SID/ASCS`.

Ahora, puede colocar estos sistemas de archivos en la característica [NFS de Azure Files](../../../storage/files/files-nfs-protocol.md). La característica NFS de Azure Files es una solución de almacenamiento de alta disponibilidad. Esta solución ofrece almacenamiento sincrónico con redundancia de zona (ZRS) y es adecuada para las instancias ASCS/ERS de SAP implementadas en diferentes zonas de disponibilidad.  Seguirá necesitando un clúster de Pacemaker para proteger los componentes de único punto de error, como los servicios centrales de SAP Netweaver (ASCS/SCS).  

En las configuraciones de ejemplo y los comandos de instalación, se utilizan los siguientes números de instancia:

| Nombre de instancia | Número de instancia |
| ---------------- | ------------------ |
| ABAP SAP Central Services (ASCS) | 00 |
| ERS | 01 |
| Servidor de aplicaciones principal (PAS) | 02 |
| Servidor de aplicaciones adicional (AAS) | 03 |
| Identificador del sistema SAP | NW1 |

:::image type="complex" source="./media/high-availability-guide-suse/high-availability-guide-suse-nfs-azure-files.png" alt-text="Alta disponibilidad de SAP NetWeaver con la característica NFS de Azure Files":::
   En este diagrama se muestra una arquitectura de alta disponibilidad de SAP Netweaver típica. Los sistemas de archivos "sapmnt" y "saptrans" se implementan en recursos compartidos de NFS en Azure Files. Los servicios centrales de SAP están protegidos por un clúster de Pacemaker. Las máquinas virtuales en clúster están detrás de un equilibrador de carga de Azure. Los recursos compartidos de NFS se montan utilizando un punto de conexión privado.
:::image-end:::

## <a name="prepare-infrastructure"></a>Preparación de la infraestructura

En este documento, se presupone que ya ha implementado una instancia de [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), una subred y un grupo de recursos.

1. Implemente las máquinas virtuales. Puede implementar las máquinas virtuales en conjuntos de disponibilidad (o en zonas de disponibilidad) si la región de Azure admite estas opciones. Si necesita otras direcciones IP para las máquinas virtuales, implemente y asocie una segunda NIC. No agregue direcciones IP secundarias a la NIC principal. [No pueden utilizarse direcciones IP flotantes de Azure Load Balancer en este escenario](../../../load-balancer/load-balancer-multivip-overview.md#limitations).  
 
2. En las direcciones IP virtuales, implemente y configure un [equilibrador de carga](../../../load-balancer/load-balancer-overview.md) de Azure. Se recomienda utilizar un [equilibrador de carga estándar](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

    1. Configure dos IP de front-end: una para ASCS (`10.90.90.10`) y otra para ERS (`10.90.90.9`).
    2. Cree un grupo de back-end y agregue las dos máquinas virtuales, que formarán parte del clúster.
    3. Cree el sondeo de estado de ASCS. El puerto de sondeo es `62000`. Cree el puerto de sondeo de ERS. El puerto de sondeo de ERS es `62101`. Cuando más adelante configure los recursos de Pacemaker, tendrá que usar puertos de sondeo que coincidan.
    4. Configure las reglas de equilibrio de carga de ASCS y ERS. Seleccione las direcciones IP de front-end, los sondeos de estado y el grupo de back-end. Seleccione puertos de alta disponibilidad, aumente el tiempo de espera de inactividad a 30 minutos y habilite la dirección IP flotante.

### <a name="deploy-azure-files-storage-account-and-nfs-shares"></a>Implementación de la cuenta de almacenamiento de Azure Files y los recursos compartidos de NFS 

La característica NFS de Azure Files se ejecuta con un [almacenamiento Premium de Azure Files][afs-azure-doc]. Antes de configurar NFS en Azure Files, consulte el [procedimiento para crear un recurso compartido en NFS](../../../storage/files/storage-files-how-to-create-nfs-shares.md?tabs=azure-portal).    

Existen dos opciones de redundancia dentro de una región de Azure:

- El [almacenamiento con redundancia local (LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage), que permite replicar los datos de forma sincrónica y local en la zona.
- El [almacenamiento con redundancia de zona (ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage), que replica los datos sincrónicamente entre las tres [zonas de disponibilidad](../../../availability-zones/az-overview.md) de la región.

Compruebe si la región de Azure seleccionada ofrece NFS 4.1 en Azure Files con la redundancia adecuada. Consulte la [disponibilidad de Azure Files en cada región de Azure][afs-avail-matrix] en el nivel de **almacenamiento de archivos Premium**. Si el escenario tiene ZRS, [asegúrese de que la región de Azure permite utilizar recursos compartidos de archivos Premium](../../../storage/common/storage-redundancy.md#zone-redundant-storage).

Se recomienda acceder a la cuenta de Azure Storage a través de un [punto de conexión privado de Azure](../../../storage/files/storage-files-networking-endpoints.md?tabs=azure-portal). No olvide que debe implementar el punto de conexión de la cuenta de almacenamiento de Azure Files y las máquinas virtuales, donde debe montar los recursos compartidos de NFS, en la misma red virtual de Azure o en redes virtuales de Azure emparejadas.

1. Implemente una cuenta de File Storage llamada `sapafsnfs`. En este ejemplo, utilizaremos ZRS. Si no conoce bien este proceso, consulte [Creación de una cuenta de almacenamiento](../../../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal#create-a-storage-account) en Azure Portal.
1. En la pestaña **Aspectos básicos**, use esta configuración:

    1. En **Nombre de cuenta de almacenamiento**, escriba `sapafsnfs`.
    
    1. En **Rendimiento**, seleccione **Premium**.
    
    1. En **Tipo de cuenta prémium**, seleccione **FileStorage**.
    
    1. En **Replicación,** seleccione Redundancia de zona (ZRS).

1. Seleccione **Next** (Siguiente).

1. En la pestaña **Opciones avanzadas**, desactive **Require secure transfer for REST API Operations** (Requerir transferencia segura en operaciones de API REST). Si no desactiva esta opción, no podrá montar el recurso compartido de NFS en la máquina virtual. Se agotará el tiempo de espera de la operación de montaje.

1. Seleccione **Next** (Siguiente).

1. En la sección **Redes**, configure estas opciones: 

    1. En **Networking connectivity**, (Conectividad de red), en **Método de conectividad**, seleccione **Punto de conexión privado**.  
    
    1. En **Punto de conexión privado**, seleccione **Agregar un punto de conexión privado**.
    
1. En el panel **Crear un punto de conexión privado**, seleccione la **suscripción**, el **grupo de recursos** y la **ubicación**. 
    
    En **Nombre**, escriba `sapafsnfs_pe`.
        
    En **Subrecurso de almacenamiento**, seleccione **archivo**.
        
    En **Redes**, en **Red virtual**, seleccione la red virtual y la subred que se van a utilizar. De nuevo, puede usar la red virtual en la se encuentran las máquinas virtuales de SAP o una red virtual emparejada.
       
    En **Integración de DNS privado**, acepte la opción predeterminada, **Sí**, de **Integrar con la zona DNS privada**. No olvide seleccionar la **zona DNS privada**.
        
    Seleccione **Aceptar**.
        
1. Seleccione la pestaña **Redes** y **Siguiente**.

1. En la pestaña **Protección de datos**, deje todos los valores predeterminados. 

1. Seleccione **Revisar y crear** para validar la configuración.

1. Espere a que se complete la validación. Corrija los problemas antes de continuar.

1. En la pestaña **Revisar y crear**, seleccione **Crear**.


A continuación, implemente los recursos compartidos de NFS en la cuenta de almacenamiento que ha creado. En este ejemplo, hay dos recursos compartidos de NFS, `sapnw1` y `saptrans`.    
1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione o busque las **cuentas de almacenamiento**. 

1. En la página **Cuentas de almacenamiento**, seleccione **sapafsnfs**.

1.  En el menú de recursos de **sapafsnfs**, en **Almacenamiento de datos**, seleccione **Recursos compartidos de archivos**.

1. En la página **Recursos compartidos de archivos**, seleccione **Recurso compartido de archivos**.

   1. En **Nombre**, escriba `sapnw1`, `saptrans`.
   1.  Seleccione el tamaño adecuado para el recurso compartido. Por ejemplo, **128 GB**.  A la hora de calcular el tamaño, tenga en cuenta los datos almacenados en el recurso compartido, los requisitos de IOPS y los requisitos de rendimiento.  Para más información, consulte este artículo sobre los [objetivos de los recursos compartidos de archivos de Azure](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets).
   1. Seleccione **NFS** como protocolo.
   1. Seleccione **No root Squash** (Sin squash raíz).  De lo contrario, al montar los recursos compartidos en las máquinas virtuales, no podrá ver el propietario ni el grupo de archivos.

   > [!IMPORTANT]
   > El tamaño del recurso compartido anterior es solo un ejemplo. Asegúrese de elegir el tamaño adecuado para sus recursos compartidos. El tamaño no solo depende del tamaño de los datos almacenados en el recurso compartido, sino también de los requisitos de IOPS y del rendimiento. Para más información, consulte este artículo sobre los [objetivos de los recursos compartidos de Azure](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets).  

   Los sistemas de archivos SAP que no tienen que montarse mediante NFS también se pueden implementar en [Azure Disk Storage](../../disks-types.md#premium-ssds). En este ejemplo, puede implementar `/usr/sap/NW1/D02` y `/usr/sap/NW1/D03` en Azure Disk Storage. 

### <a name="important-considerations-for-nfs-on-azure-files-shares"></a>Consideraciones importantes sobre NFS en los recursos compartidos de Azure Files

Cuando planee la implementación con NFS en Azure Files, tenga en cuenta los siguientes puntos importantes:  

- El tamaño mínimo del recurso compartido es 100 GiB. Solo se paga por la [capacidad de los recursos compartidos aprovisionados](../../../storage/files/understanding-billing.md#provisioned-model). 
- El tamaño de los recursos compartidos de NFS no solo depende de los requisitos de capacidad, sino también de los requisitos de IOPS y de los requisitos de rendimiento. Para más información, consulte este artículo sobre los [objetivos de los recursos compartidos de Azure](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets).
- Pruebe la carga de trabajo para validar el tamaño y asegurarse de que se ajusta a los objetivos de rendimiento. Para más información sobre la resolución de los problemas de rendimiento con NFS en Azure Files, consulte [Solución de problemas de rendimiento de recursos compartidos de archivos de Azure](../../../storage/files/storage-troubleshooting-files-performance.md).
- En el caso de los sistemas SAP J2EE, `/usr/sap/<SID>` no se puede colocar en la característica NFS de Azure Files.
- Si el sistema SAP tiene una gran carga de trabajos por lotes, es posible que tenga millones de registros de trabajos. Si los registros de trabajos por lotes de SAP se almacenan en el sistema de archivos, preste especial atención al tamaño del recurso compartido `sapmnt`. A partir de SAP_BASIS 7.52, los registros de trabajos por lotes se almacenarán de forma predeterminada en la base de datos. Para más información, consulte este artículo sobre el [registro de trabajos en la base de datos][2360818].     
- Implemente un recurso compartido `sapmnt` diferente para cada sistema SAP.
- No use el recurso compartido `sapmnt` para ninguna otra actividad, como las interfaces o `saptrans`.
- No use el recurso compartido `saptrans` para ninguna otra actividad, como las interfaces o `sapmnt`.
- Evite agrupar los recursos compartidos de demasiados sistemas SAP en una sola cuenta de almacenamiento. También hay [objetivos de escalado del rendimiento de la cuenta de almacenamiento](../../../storage/files/storage-files-scale-targets.md#storage-account-scale-targets). Además, debe tener cuidado para no superar los límites de la cuenta de almacenamiento.
- En general, no agrupe los recursos compartidos de más de cinco sistemas SAP en una sola cuenta de almacenamiento. Esta guía le ayudará a evitar que sobrepase los límites de la cuenta de almacenamiento y simplificará el análisis de rendimiento.   
- En general, evite mezclar recursos compartidos de sistemas SAP que sean de producción y no producción en la misma cuenta de almacenamiento.
- Es conveniente que realice la implementación en SLES 15 SP2 o una versión posterior para aprovechar las [mejoras del cliente de NFS](../../../storage/files/storage-troubleshooting-files-nfs.md#ls-hangs-for-large-directory-enumeration-on-some-kernels).     
- Utilice un punto de conexión privado. En el improbable caso de que se produzca un error en una zona, las sesiones de NFS se redirigirán automáticamente a una zona en buen estado. No será necesario que vuelva a montar los recursos compartidos de NFS en las máquinas virtuales.
- Si va a implementar las máquinas virtuales en diferentes zonas de disponibilidad, use una [cuenta de almacenamiento con ZRS](../../../storage/common/storage-redundancy.md#zone-redundant-storage) en las regiones de Azure que admitan esta funcionalidad. 
- En la actualidad, Azure Files no admite la replicación automática entre regiones para escenarios de recuperación ante desastres.  

## <a name="setting-up-ascs"></a>Configuración de (A)SCS

En este ejemplo, va a implementar los recursos manualmente en [Azure Portal](https://portal.azure.com/#home).

### <a name="deploy-azure-load-balancer-via-azure-portal"></a>Implementación de Azure Load Balancer en Azure Portal

Después de implementar las máquinas virtuales del sistema SAP, cree un equilibrador de carga. A continuación, utilice las máquinas virtuales del grupo de back-end.

1. Cree un equilibrador de carga estándar interno.
   1. Creación de las direcciones IP de front-end
      1. Dirección IP 10.90.90.10 de ASCS
         1. Abra el equilibrador de carga, seleccione el grupo de direcciones IP de front-end y haga clic en Agregar
         1. Escriba el nombre del nuevo grupo de direcciones IP de front-end (por ejemplo, **frontend.NW1.ASCS**).
         1. Establezca Asignación en Estática y escriba la dirección IP (por ejemplo, **10.90.90.10**).
         1. Haga clic en Aceptar
      1. Dirección IP 10.90.90.9 de ASCS ERS
         * Repita los pasos anteriores a partir de "a" para crear una dirección IP para ERS (por ejemplo, **10.90.90.9** y **frontend.NW1.ERS**).
   1. Creación del grupo de servidores back-end
      1. Abra el equilibrador de carga, seleccione los grupos de back-end y haga clic en Agregar
      1. Escriba el nombre del nuevo grupo de servidores back-end (por ejemplo, **backend.NW1**).
      1. Haga clic en Agregar una máquina virtual.
      1. Seleccione Máquina virtual.
      1. Seleccione las máquinas virtuales del clúster de (A)SCS y sus direcciones IP.
      1. Haga clic en Agregar  
      
   1. Creación de los sondeos de estado
      1. Puerto 620 **00** para ASCS
         1. Abra el equilibrador de carga, seleccione los sondeos de estado y haga clic en Agregar
         1. Escriba el nombre del nuevo sondeo de estado (por ejemplo **health.NW1.ASCS**).
         1. Seleccione TCP como protocolo, puerto 620 **00**, y mantenga el intervalo de 5 y el umbral incorrecto 2.
         1. Haga clic en Aceptar
      1. Puerto 621 **01** para ASCS ERS.
            * Repita los pasos anteriores a partir de "c" para crear un sondeo de estado para ERS (por ejemplo 621 **01** y **health.NW1.ERS**).
   1. Reglas de equilibrio de carga.
      1. Creación de un grupo de servidores back-end para ASCS
         1. Abra el equilibrador de carga, seleccione las reglas de equilibrio de carga y haga clic en Agregar.
         1. Escriba el nombre de la nueva regla del equilibrador de carga (por ejemplo, **lb.NW1.ASCS**)
         1. Seleccione la dirección IP de front-end de ASCS, el grupo de back-end y el sondeo de estado que creó anteriormente (por ejemplo, **frontend.NW1.ASCS**, **backend.NW1** y **health.NW1.ASCS**)
         1. Seleccione **Puertos HA**
         1. **Asegúrese de habilitar la dirección IP flotante**
         1. Haga clic en Aceptar
         * Repita los pasos anteriores para crear reglas de equilibrio de carga para ERS (por ejemplo **lb.NW1.ERS**)
1. Como alternativa, si el escenario requiere un equilibrador de carga básico (interno), siga estos pasos:  
   1. Creación de las direcciones IP de front-end
      1. Dirección IP 10.90.90.10 de ASCS
         1. Abra el equilibrador de carga, seleccione el grupo de direcciones IP de front-end y haga clic en Agregar
         1. Escriba el nombre del nuevo grupo de direcciones IP de front-end (por ejemplo, **frontend.NW1.ASCS**).
         1. Establezca Asignación en Estática y escriba la dirección IP (por ejemplo, **10.90.90.10**).
         1. Haga clic en Aceptar
      1. Dirección IP 10.90.90.9 de ASCS ERS
         * Repita los pasos anteriores a partir de "a" para crear una dirección IP para ERS (por ejemplo, **10.90.90.9** y **frontend.NW1.ERS**).
   1. Creación del grupo de servidores back-end
      1. Abra el equilibrador de carga, seleccione los grupos de back-end y haga clic en Agregar
      1. Escriba el nombre del nuevo grupo de servidores back-end (por ejemplo, **backend.NW1**).
      1. Haga clic en Agregar una máquina virtual.
      1. Seleccione el conjunto de disponibilidad que creó anteriormente para ASCS. 
      1. Seleccione las máquinas virtuales del clúster (A)SCS.
      1. Haga clic en Aceptar
   1. Creación de los sondeos de estado
      1. Puerto 620 **00** para ASCS
         1. Abra el equilibrador de carga, seleccione los sondeos de estado y haga clic en Agregar
         1. Escriba el nombre del nuevo sondeo de estado (por ejemplo **health.NW1.ASCS**).
         1. Seleccione TCP como protocolo, puerto 620 **00**, y mantenga el intervalo de 5 y el umbral incorrecto 2.
         1. Haga clic en Aceptar
      1. Puerto 621 **01** para ASCS ERS.
            * Repita los pasos anteriores a partir de "c" para crear un sondeo de estado para ERS (por ejemplo 621 **01** y **health.NW1.ERS**).
   1. Reglas de equilibrio de carga.
      1. TCP 32 **00** para ASCS
         1. Abra el equilibrador de carga, seleccione las reglas de equilibrio de carga y haga clic en Agregar.
         1. Escriba el nombre de la nueva regla del equilibrador de carga (por ejemplo, **lb.NW1.ASCS.3200**).
         1. Seleccione la dirección IP de front-end de ASCS, el grupo de servidores back-end y el sondeo de estado que creó anteriormente (por ejemplo, **frontend.NW1.ASCS**).
         1. Conserve el protocolo **TCP** y escriba el puerto **3200**.
         1. Aumente el tiempo de espera de inactividad a 30 minutos
         1. **Asegúrese de habilitar la dirección IP flotante**
         1. Haga clic en Aceptar
      1. Puertos adicionales para ASCS
         * Repita los pasos anteriores para los puertos 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 y TCP para ASCS.
      1. Puertos adicionales para ASCS ERS
         * Repita los pasos anteriores a partir de "d" para los puertos 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** 16 y TCP para ASCS ERS

      
      > [!IMPORTANT]
      > La dirección IP flotante no se admite en una configuración de IP secundaria de NIC para los escenarios de equilibrio de carga. Para ver detalles, consulte [Limitaciones de Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Si necesita una dirección IP adicional para la VM, implemente una segunda NIC.  

      > [!Note]
      > Cuando las máquinas virtuales sin direcciones IP públicas se colocan en el grupo de back-end de Standard Load Balancer interno (sin dirección IP pública), no hay conectividad saliente de Internet, a menos que se realice una configuración adicional para permitir el enrutamiento a puntos de conexión públicos. Para obtener más información sobre cómo obtener conectividad saliente, vea [Conectividad de punto de conexión público para máquinas virtuales con Azure Standard Load Balancer en escenarios de alta disponibilidad de SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

      > [!IMPORTANT]
      > No habilite las marcas de tiempo TCP en VM de Azure que se encuentren detrás de Azure Load Balancer. Si habilita las marcas de tiempo TCP provocará un error en los sondeos de estado. Establezca el parámetro **net.ipv4.tcp_timestamps** a **0**. Consulte [Sondeos de estado de Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md) para obtener más información.

### <a name="create-pacemaker-cluster"></a>Creación del clúster de Pacemaker

Siga los pasos de [Configuración de Pacemaker en SUSE Linux Enterprise Server en Azure](high-availability-guide-suse-pacemaker.md) si desea crear un clúster de Pacemaker básico para SAP (A)SCS.

### <a name="installation"></a>Instalación

Los elementos siguientes tienen el prefijo **[A]** : aplicable a todos los nodos, **[1]** : aplicable solo al nodo 1 o **[2]** : aplicable solo al nodo 2.

1. **[A]** Instale la última versión del conector de SUSE.

    ```bash
    sudo zypper install sap-suse-cluster-connector
    ```

   > [!NOTE]
   > El problema conocido con el uso de un guion en los nombres de host se ha corregido con la versión **3.1.1** del paquete **sap-suse-cluster-connector**. Si utiliza nodos de clúster con un guion en el nombre de host, asegúrese de usar al menos la versión 3.1.1 del paquete sap-suse-cluster-connector. Si lo hace, el clúster no funcionará. 

   Asegúrese de que instaló la nueva versión del conector de clúster SUSE SAP. La antigua se llamaba sap_suse_cluster_connector y la nueva se llama **sap-suse-cluster-connector**.  

2. **[A]** Actualice los agentes de recursos de SAP  
   
   Se requiere una revisión del paquete de agentes de recursos para usar la configuración nueva, que es la que describe este artículo. Puede usar el comando siguiente para comprobar si la revisión ya está instalada

    ```bash
    sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
    ```

   La salida debe ser similar a

    ```bash
    <parameter name="IS_ERS" unique="0" required="0">;
    ```

   Si el comando grep no encuentra el parámetro IS_ERS, necesita instalar la revisión que aparece en [la página de descarga de SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)


3. **[A]** Configure la resolución nombres de host

   Puede usar un servidor DNS o modificar /etc/hosts en todos los nodos. En este ejemplo se muestra cómo utilizar el archivo /etc/hosts.
   Reemplace la dirección IP y el nombre de host en los siguientes comandos

    ```bash
    sudo vi /etc/hosts
    ```

   Inserte las siguientes líneas en /etc/hosts. Cambie la dirección IP y el nombre de host para que coincida con su entorno   

    ```bash
     # IP address of cluster node 1
     10.90.90.7    sap-cl1
     # IP address of cluster node 2
     10.90.90.8     sap-cl2
     # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
     10.90.90.10   sapascs
     # IP address of the load balancer frontend configuration for SAP Netweaver ERS
     10.90.90.9    sapers
    ```

4. **[1]** Cree los directorios de SAP en el recurso compartido de NFS.  
   Monte temporalmente el recurso compartido de NFS **sapnw1** en una de las máquinas virtuales y cree los directorios de SAP que se usarán como puntos de montaje anidados.  

    ```bash
    # mount temporarily the volume
    sudo mkdir -p /saptmp
    sudo mount -t nfs sapnfs.file.core.windows.net:/sapnfsafs/sapnw1 /saptmp -o vers=4,minorversion=1,sec=sys
    # create the SAP directories
    sudo cd /saptmp
    sudo mkdir -p sapmntNW1
    sudo mkdir -p usrsapNW1ascs
    sudo mkdir -p usrsapNW1ers
    sudo mkdir -p usrsapNW1sys
    # unmount the volume and delete the temporary directory
    cd ..
    sudo umount /saptmp
    sudo rmdir /saptmp
    ``` 

## <a name="prepare-for-sap-netweaver-installation"></a>Preparación de la instalación de SAP NetWeaver

1. **[A]** Cree los directorios compartidos

    ```bash
    sudo mkdir -p /sapmnt/NW1
    sudo mkdir -p /usr/sap/trans
    sudo mkdir -p /usr/sap/NW1/SYS
    sudo mkdir -p /usr/sap/NW1/ASCS00
    sudo mkdir -p /usr/sap/NW1/ERS01
    
    sudo chattr +i /sapmnt/NW1
    sudo chattr +i /usr/sap/trans
    sudo chattr +i /usr/sap/NW1/SYS
    sudo chattr +i /usr/sap/NW1/ASCS00
    sudo chattr +i /usr/sap/NW1/ERS01
    ```

2. **[A]** Monte los sistemas de archivos, que no se controlarán mediante el clúster de Pacemaker.  

    ```bash
    vi /etc/fstab
    # Add the following lines to fstab, save and exit
    sapnfs.file.core.windows.net:/sapnfsafs/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/sapmntNW1 /sapmnt/NW1  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1sys/ /usr/sap/NW1/SYS  nfs vers=4,minorversion=1,sec=sys  0  0
    
    # Mount the file systems
    mount -a 
    ```

3. **[A]** Configure el archivo de intercambio

    ```bash
    sudo vi /etc/waagent.conf
    
    # Set the property ResourceDisk.EnableSwap to y
    # Create and use swapfile on resource disk.
    ResourceDisk.EnableSwap=y
   
    # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
    # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
    # Size of the swapfile.
    ResourceDisk.SwapSizeMB=2000
    ```

   Reinicie el agente para activar el cambio

    ```bash
    sudo service waagent restart
    ```

### <a name="installing-sap-netweaver-ascsers"></a>Instalación de SAP NetWeaver ASCS/ERS

1. **[1]** Cree un recurso IP virtual y un sondeo de estado para la instancia de ASCS

   > [!IMPORTANT]
   > Se recomienda usar el agente de recursos azure-lb, que forma parte de los agentes de recursos de paquetes, con los siguientes requisitos de versión de paquete:
   > - En el caso de SLES 12 SP4/SP5, la versión debe ser, al menos, resource-agents-4.3.018.a7fb5035-3.30.1.  
   > - En el caso de SLES 15 y ediciones posteriores, la versión debe ser al menos resource-agents-4.3.0184.6ee15eb2-4.13.1.  
   

    ```bash
    sudo crm node standby sap-cl2
    sudo crm configure primitive fs_NW1_ASCS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ascs' directory='/usr/sap/NW1/ASCS00' fstype='nfs' options='sec=sys,vers=4.1' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
    
    sudo crm configure primitive vip_NW1_ASCS IPaddr2 \
      params ip=10.90.90.10 cidr_netmask=24 \
      op monitor interval=10 timeout=20
    
    sudo crm configure primitive nc_NW1_ASCS azure-lb port=62000
    
    sudo crm configure group g-NW1_ASCS fs_NW1_ASCS nc_NW1_ASCS vip_NW1_ASCS \
       meta resource-stickiness=3000
    ```

   Asegúrese de que el estado del clúster sea el correcto y que se iniciaron todos los recursos. No es importante en qué nodo se ejecutan los recursos.

    ```bash 
    sudo crm_mon -r
    # Node sap-cl2: standby
    # Online: [ sap-cl1 ]
    #
    # Full list of resources:
    #
    # stonith-sbd     (stonith:external/sbd): Started sap-cl1
    # Resource Group: g-NW1_ASCS
    #  fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl1
    #  nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #  vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
   
    ```
  
2. **[1]** Instale SAP NetWeaver ASCS  

   Instale SAP NetWeaver ASCS como raíz del primer nodo utilizando un nombre de host virtual que se corresponda con la dirección IP de la configuración de front-end del equilibrador de carga de ASCS; por ejemplo, ***sapascs** _, _*_10.90.90.10_*_ y el número de instancia que usó para el sondeo del equilibrador de carga; por ejemplo, _*_00_**.

   Puede usar el parámetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que un usuario no raíz se conecta a sapinst. Puede usar el parámetro SAPINST_USE_HOSTNAME para instalar SAP con el nombre de host virtual.

    ```bash
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
    ```

   Si se produce un error en la instalación al crear una subcarpeta en /usr/sap/**NW1**/ASCS **00**, pruebe a establecer el propietario y el grupo de la carpeta ASCS **00** e inténtelo de nuevo. 

    ```bash
    chown nw1adm /usr/sap/NW1/ASCS00
    chgrp sapsys /usr/sap/NW1/ASCS00
    ```

3. **[1]** Cree un recurso IP virtual y un sondeo de estado para la instancia de ERS

    ```bash
    sudo crm node online sap-cl2
    sudo crm node standby sap-cl1
    sudo crm configure primitive fs_NW1_ERS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ers' directory='/usr/sap/NW1/ERS01' fstype='nfs' options='sec=sys,vers=4.1' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW1_ERS IPaddr2 \
      params ip=10.90.90.9 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW1_ERS azure-lb port=62101
    
    sudo crm configure group g-NW1_ERS fs_NW1_ERS nc_NW1_ERS vip_NW1_ERS
    ```

   Asegúrese de que el estado del clúster sea el correcto y que se iniciaron todos los recursos. No es importante en qué nodo se ejecutan los recursos.

    ```bash
    sudo crm_mon -r
   
    # Node sap-cl1: standby
    # Online: [ sap-cl2 ]
    # 
    # Full list of resources:
    #
    # stonith-sbd     (stonith:external/sbd): Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #  Resource Group: g-NW1_ERS
    #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started sap-cl2 
    #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ERS  (ocf::heartbeat:IPaddr2):     Started sap-cl2
    ```

4. **[2]** Instale SAP NetWeaver ERS

   Instale SAP NetWeaver ERS como raíz del segundo nodo utilizando un nombre de host virtual que se corresponda con la dirección IP de la configuración de front-end del equilibrador de carga de ERS (por ejemplo, **sapers**), **10.90.90.9** y el número de instancia que usó para el sondeo del equilibrador de carga (por ejemplo, **01**).

   Puede usar el parámetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que un usuario no raíz se conecta a sapinst. Puede usar el parámetro SAPINST_USE_HOSTNAME para instalar SAP con el nombre de host virtual.

    ```bash
    <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Use SWPM SP 20 PL 05 o superior. Las versiones inferiores no establecen correctamente los permisos y se producirá un error de instalación.

   Si se produce un error en la instalación al crear una subcarpeta en /usr/sap/**NW1**/ERS **01**, pruebe a establecer el propietario y el grupo de la carpeta ERS **01** e inténtelo de nuevo.

    ```bash
    chown nw1adm /usr/sap/NW1/ERS01
    chgrp sapsys /usr/sap/NW1/ERS01
    ```

5. **[1]** Adapte los perfiles de instancias ASCS/SCS y ERS
 
   * Perfil ASCS/SCS

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ASCS00_sapascs
    
    # Change the restart command to a start command
    #Restart_Program_01 = local $(_EN) pf=$(_PF)
    Start_Program_01 = local $(_EN) pf=$(_PF)
    
    # Add the following lines
    service/halib = $(DIR_CT_RUN)/saphascriptco.so
    service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
    
    # Add the keep alive parameter, if using ENSA1
    enque/encni/set_so_keepalive = true
    ```

   En el caso de ENSA1 y ENSA2, asegúrese de que los parámetros del sistema operativo `keepalive` se establecen tal y como se describe en la nota de SAP [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

   * Perfil ERS

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ERS01_sapers
     
    # Change the restart command to a start command
    #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
    Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
    
    # Add the following lines
    service/halib = $(DIR_CT_RUN)/saphascriptco.so
    service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
    
    # remove Autostart from ERS profile
    # Autostart = 1
    ```

6. **[A]** Configure la conexión persistente

   La comunicación entre el servidor de aplicaciones de SAP NetWeaver y ASCS/SCS se enruta a través de un equilibrador de carga de software. El equilibrador de carga desconecta las conexiones inactivas después de un tiempo de expiración que se puede configurar. Para evitarlo, si usa ENSA1, debe establecer un parámetro en el perfil de ASCS/SCS de SAP NetWeaver. Cambie la opción `keepalive` del sistema Linux en todos los servidores SAP de ENSA1 y ENSA2. Para más información, lea la [nota de SAP 1410736][1410736].

    ```bash
    # Change the Linux system configuration
    sudo sysctl net.ipv4.tcp_keepalive_time=300
    ```

7. **[A]** Configure los usuarios de SAP después de la instalación

    ```bash
    # Add sidadm to the haclient group
    sudo usermod -aG haclient nw1adm
    ```

8. **[1]** Agregue los servicios SAP de ASCS y ERS al archivo `sapservice`

   Agregue la entrada del servicio ASCS al segundo nodo y copie la entrada del servicio ERS al primer nodo.

    ```bash
    cat /usr/sap/sapservices | grep ASCS00 | sudo ssh sap-cl2 "cat >>/usr/sap/sapservices"
    sudo ssh sap-cl2 "cat /usr/sap/sapservices" | grep ERS01 | sudo tee -a /usr/sap/sapservices
    ```

9. **[1]** Cree los recursos de clúster de SAP

   Si usa la arquitectura de servidor 1 de puesta en cola (ENSA1), defina los recursos tal como se indica:

    ```bash
    sudo crm configure property maintenance-mode="true"
   
    sudo crm configure primitive rsc_sap_NW1_ASCS00 SAPInstance \
     operations \$id=rsc_sap_NW1_ASCS00-operations \
     op monitor interval=11 timeout=60 on-fail=restart \
     params InstanceName=NW1_ASCS00_sapascs START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_sapascs" \
     AUTOMATIC_RECOVER=false \
     meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
    sudo crm configure primitive rsc_sap_NW1_ERS01 SAPInstance \
     operations \$id=rsc_sap_NW1_ERS01-operations \
     op monitor interval=11 timeout=60 on-fail=restart \
     params InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" AUTOMATIC_RECOVER=false IS_ERS=true \
     meta priority=1000
   
    sudo crm configure modgroup g-NW1_ASCS add rsc_sap_NW1_ASCS00
    sudo crm configure modgroup g-NW1_ERS add rsc_sap_NW1_ERS01
   
    sudo crm configure colocation col_sap_NW1_no_both -5000: g-NW1_ERS g-NW1_ASCS
    sudo crm configure location loc_sap_NW1_failover_to_ers rsc_sap_NW1_ASCS00 rule 2000: runs_ers_NW1 eq 1
    sudo crm configure order ord_sap_NW1_first_start_ascs Optional: rsc_sap_NW1_ASCS00:start rsc_sap_NW1_ERS01:stop symmetrical=false
   
    sudo crm node online sap-cl1
    sudo crm configure property maintenance-mode="false"
    ```

   SAP introdujo una opción de compatibilidad con el servidor 2 de puesta en cola, incluida la replicación, a partir de la versión de SAP NW 7.52. A partir de la plataforma ABAP (versión 1809), el servidor 2 de puesta en cola está instalado de forma predeterminada. Consulte la nota de SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) para consultar la compatibilidad con el servidor 2 de puesta en cola.  

   Si usa la arquitectura de servidor 2 de puesta en cola ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), defina los recursos tal como se indica:

    ```bash
    sudo crm configure property maintenance-mode="true"
   
    sudo crm configure primitive rsc_sap_NW1_ASCS00 SAPInstance \
     operations \$id=rsc_sap_NW1_ASCS00-operations \
     op monitor interval=11 timeout=60 on-fail=restart \
     params InstanceName=NW1_ASCS00_sapascs START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_sapascs" \
     AUTOMATIC_RECOVER=false \
     meta resource-stickiness=5000
   
    sudo crm configure primitive rsc_sap_NW1_ERS01 SAPInstance \
     operations \$id=rsc_sap_NW1_ERS01-operations \
     op monitor interval=11 timeout=60 on-fail=restart \
     params InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" AUTOMATIC_RECOVER=false IS_ERS=true
   
    sudo crm configure modgroup g-NW1_ASCS add rsc_sap_NW1_ASCS00
    sudo crm configure modgroup g-NW1_ERS add rsc_sap_NW1_ERS01
    
    sudo crm configure colocation col_sap_NW1_no_both -5000: g-NW1_ERS g-NW1_ASCS
    sudo crm configure order ord_sap_NW1_first_start_ascs Optional: rsc_sap_NW1_ASCS00:start rsc_sap_NW1_ERS01:stop symmetrical=false
   
    sudo crm node online sap-cl1
    sudo crm configure property maintenance-mode="false"
    ```

   Si está actualizando desde una versión anterior y va a cambiar al servidor 2 de puesta en cola, consulte la nota de SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Asegúrese de que el estado del clúster sea el correcto y que se iniciaron todos los recursos. No es importante en qué nodo se ejecutan los recursos.

    ```bash
    sudo crm_mon -r
    # Full list of resources:
    # 
    # stonith-sbd     (stonith:external/sbd): Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl1
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
    #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started sap-cl1
    #  Resource Group: g-NW1_ERS
    #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #      rsc_sap_NW1_ERS01  (ocf::heartbeat:SAPInstance):   Started sap-cl1
    ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparación del servidor de aplicaciones de SAP NetWeaver 

Algunas bases de datos requieren que la instalación de la instancia de base de datos se ejecute en un servidor de aplicaciones. Prepare las máquinas virtuales del servidor de aplicaciones para poder usarlas en estos casos.

En los pasos siguientes, se supone que el servidor de aplicaciones se va a instalar en un servidor que no es ASCS/SCS ni HANA. De lo contrario, no se necesitan algunos de los pasos que aparecen a continuación (como configurar la resolución de nombres de host).

Los elementos siguientes tienen el prefijo **[A]** (aplicable a PAS y AAS), **[P]** (solo aplicable a PAS) o **[S]** (solo aplicable a AAS).


1. **[A]** Configure el sistema operativo

   Reduzca el tamaño de la caché de datos incorrectos. Para más información, consulte [Low write performance on SLES 11/12 servers with large RAM](https://www.suse.com/support/kb/doc/?id=7010287) (Bajo rendimiento de escritura en servidores SLES 11/12 con RAM grande).

    ```bash
    sudo vi /etc/sysctl.conf
    # Change/set the following settings
    vm.dirty_bytes = 629145600
    vm.dirty_background_bytes = 314572800
    ```

1. **[A]** Configure la resolución nombres de host

   Puede usar un servidor DNS o modificar /etc/hosts en todos los nodos. En este ejemplo se muestra cómo utilizar el archivo /etc/hosts.
   Reemplace la dirección IP y el nombre de host en los siguientes comandos

    ```bash
    sudo vi /etc/hosts
    ```

   Inserte las siguientes líneas en /etc/hosts. Cambie la dirección IP y el nombre de host para que coincida con su entorno

    ```bash
    10.90.90.7    sap-cl1
    10.90.90.8    sap-cl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    10.90.90.10   sapascs
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    10.90.90.9    sapers
    10.90.90.12   sapa01
    10.90.90.13   sapa02
    ```

1. **[A]** Cree el directorio sapmnt.

    ```bash
    sudo mkdir -p /sapmnt/NW1
    sudo mkdir -p /usr/sap/trans

    sudo chattr +i /sapmnt/NW1
    sudo chattr +i /usr/sap/trans
    ```

1. **[A]** Monte los sistemas de archivos.

    ```bash
    vi /etc/fstab
    # Add the following lines to fstab, save and exit
    sapnfs.file.core.windows.net:/sapnfsafs/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/sapmntNW1 /sapmnt/NW1  nfs vers=4,minorversion=1,sec=sys  0  0
    
    # Mount the file systems
    mount -a 
    ```   

1. **[A]** Configure el archivo de intercambio

    ```bash
    sudo vi /etc/waagent.conf
   
    # Set the property ResourceDisk.EnableSwap to y
    # Create and use swapfile on resource disk.
    ResourceDisk.EnableSwap=y
    
    # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
    # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
    # Size of the swapfile.
    ResourceDisk.SwapSizeMB=2000
    ```

   Reinicie el agente para activar el cambio

    ```bash
    sudo service waagent restart
    ```

## <a name="install-database"></a>Instalar la base de datos

En este ejemplo, SAP NetWeaver se instala en SAP HANA. En esta instalación puede usar todas las bases de datos admitidas. Para más información acerca de cómo instalar SAP HANA en Azure, consulte [Alta disponibilidad de SAP HANA en Azure Virtual Machines (VM)][sap-hana-ha]. Para ver una lista de las bases de datos admitidas, consulte la [nota de SAP 1928533][1928533].

Instale la instancia de base de datos de SAP NetWeaver como raíz con un nombre de host virtual que se asigna a la dirección IP de la configuración de front-end del equilibrador de carga para la base de datos.  
Puede usar el parámetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que un usuario no raíz se conecta a sapinst.

   ```bash
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Instalación del servidor de aplicaciones de SAP NetWeaver

Siga estos pasos para instalar un servidor de aplicaciones de SAP.

1. **[A]** Prepare el servidor de aplicaciones. Siga los pasos descritos en el capítulo [Preparación del servidor de aplicaciones de SAP NetWeaver](high-availability-guide-suse-nfs-azure-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) anterior para preparar el servidor de aplicaciones.

2. **[A]** Instale el servidor de aplicaciones de SAP NetWeaver.  
   Instale un servidor de aplicaciones de SAP NetWeaver principal o adicional.

   Puede usar el parámetro de sapinst SAPINST_REMOTE_ACCESS_USER para permitir que un usuario no raíz se conecta a sapinst.

    ```bash
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

3. **[A]** Actualice el almacenamiento seguro de SAP HANA.

   Actualice el almacenamiento seguro de SAP HANA que apunte al nombre virtual de la configuración de la replicación del sistema SAP HANA.

   Ejecute el siguiente comando para mostrar las entradas:
    ```bash
    hdbuserstore List
    ```

   El comando debería mostrar todas las entradas, que deberían ser parecidas a estas:
    ```bash
    DATA FILE       : /home/nw1adm/.hdb/sapa01/SSFS_HDB.DAT
    KEY FILE        : /home/nw1adm/.hdb/sapa01/SSFS_HDB.KEY
   
    KEY DEFAULT 
      ENV : 10.90.90.5:30313
      USER: SAPABAP1
      DATABASE: NW1
    ```

   En este ejemplo, la dirección IP de la entrada predeterminada apunta a la máquina virtual, y no al equilibrador de carga. Cambie la entrada para que apunte al nombre de host virtual del equilibrador de carga. Asegúrese de usar el mismo puerto y el mismo nombre de la base de datos. Por ejemplo, `30313` y `NW1` en la salida de muestra. 

   ```bash
   su - nw1adm
   hdbuserstore SET DEFAULT nw1db:30313@NW1 SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-cluster-setup"></a>Prueba de configuración del clúster

Pruebe exhaustivamente el clúster de Pacemaker. [Ejecute las pruebas de conmutación por error típicas](./high-availability-guide-suse.md#test-the-cluster-setup).

## <a name="next-steps"></a>Pasos siguientes

* [Alta disponibilidad para SAP NetWeaver en máquinas virtuales de Azure en SUSE Linux Enterprise Server para SAP Applications: guía de varios SID](./high-availability-guide-suse-multi-sid.md)
* [Planeamiento e implementación de Azure Virtual Machines para SAP][planning-guide]
* [Implementación de Azure Virtual Machines para SAP][deployment-guide]
* [Implementación de DBMS de Azure Virtual Machines para SAP][dbms-guide]
* Para más información sobre cómo establecer la alta disponibilidad y planear la recuperación ante desastres de SAP HANA en Azure Virtual Machines, consulte [Alta disponibilidad de SAP HANA en las máquinas virtuales de Azure][sap-hana-ha].