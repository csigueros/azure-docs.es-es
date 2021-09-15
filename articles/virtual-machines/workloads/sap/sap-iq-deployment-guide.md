---
title: Implementación de SAP BW NLS con SAP IQ en Azure | Microsoft Docs
description: Planee, implemente y configure la solución SAP BW NLS con SAP IQ en Azure.
services: virtual-machines,virtual-machines-windows,virtual-machines-linux,virtual-network,storage,azure-netapp-files,azure-shared-disk,managed-disk
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2021
ms.author: depadia
ms.openlocfilehash: 7b4f49efff29717e3345a2cc04286e9c3e86c8f7
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123306537"
---
# <a name="sap-bw-nls-implementation-guide-with-sap-iq-on-azure"></a>Guía de implementación de SAP BW NLS con SAP IQ en Azure

A lo largo de los años, los clientes que ejecutan el sistema SAP Business Warehouse (BW) ven un crecimiento exponencial en el tamaño de la base de datos, lo que aumenta el costo de proceso. Para lograr el equilibrio adecuado entre costo y rendimiento, los clientes pueden usar el almacenamiento casi en línea (NLS) para migrar los datos históricos. 

La implementación de NLS basada en SAP IQ es el método estándar de SAP para mover datos históricos de una base de datos principal (SAP HANA o AnyDB). La integración de SAP IQ permite separar los datos a los que se accede con frecuencia de aquellos con un acceso no frecuente, lo que hace que la demanda de recursos sea menor en el sistema SAP BW. 

En esta guía, se proporcionan instrucciones para planear, implementar y configurar SAP BW NLS con SAP IQ en Azure. Esta guía trata los servicios y características comunes de Azure que son pertinentes para la implementación de SAP IQ NLS y no trata ninguna solución de asociados de NLS. 

Esta guía no reemplaza la documentación estándar de SAP sobre la implementación de NLS con SAP IQ. En su lugar, complementa la documentación oficial de instalación y administración. 

## <a name="solution-overview"></a>Información general de la solución

En un sistema SAP BW operativo, el volumen de datos aumenta constantemente debido a los requisitos empresariales y legales. El gran volumen de datos puede afectar al rendimiento del sistema y aumenta el esfuerzo de administración, lo que da lugar a la necesidad de implementar una estrategia de vencimiento por fechas de los datos. 

Si quiere mantener la cantidad de datos en el sistema SAP BW sin eliminarlos, puede usar el archivado de datos. Los datos se mueven primero al nivel de almacenamiento de archivo o al almacenamiento casi en línea y, después, se eliminan del sistema SAP BW. Puede acceder a los datos directamente o volver a cargarlos según sea necesario en función de cómo se hayan archivado. 

Los usuarios de SAP BW pueden usar SAP IQ como una solución de almacenamiento casi en línea. El adaptador para SAP IQ como solución casi en línea se entrega con el sistema SAP BW. Con NLS implementado, los datos usados con frecuencia se almacenan en una base de datos en línea de SAP BW (SAP HANA o AnyDB). Los datos a los que se accede con poca frecuencia se almacenan en SAP IQ, lo que reduce el costo de administración de datos y mejora el rendimiento del sistema SAP BW. Para garantizar la coherencia entre los datos en línea y los datos Nearline, las particiones archivadas se bloquean y son de solo lectura. 

SAP IQ admite dos tipos de arquitectura: simplex y multiplex. En una arquitectura simplex, se ejecuta una única instancia de un servidor de SAP IQ en una sola máquina virtual. Los archivos se pueden encontrar en una máquina host o en un dispositivo de almacenamiento de red. 

> [!Important]
> En el caso de la solución SAP NLS, SAP solo proporciona y ha evaluado la arquitectura simplex.

![Diagrama que muestra información general de la solución SAP I Q.](media/sap-iq-deployment-guide/sap-iq-solution-overview.png)

En Azure, el servidor de SAP IQ se debe implementar en una máquina virtual (VM) independiente. No se recomienda instalar software de SAP IQ en un servidor existente que ya tenga otra instancia de base de datos en ejecución, ya que SAP IQ hace un uso completo de la CPU y la memoria para su propio uso. Se puede usar un servidor de SAP IQ para varias implementaciones de SAP NLS. 

## <a name="support-matrix"></a>Matrices compatibles

La matriz de compatibilidad para una solución NLS de SAP IQ incluye:

- **Sistema operativo**: SAP IQ solo está certificado en el nivel de sistema operativo. Puede ejecutar un sistema operativo certificado por SAP IQ en el entorno de Azure, siempre y cuando sea compatible para ejecutarse en la infraestructura de Azure. Para más información, consulte la [nota de SAP 2133194](https://launchpad.support.sap.com/#/notes/2133194).

- **Compatibilidad de SAP BW**: el almacenamiento Nearline para SAP IQ solo se libera para los sistemas SAP BW que ya se ejecutan con Unicode. La nota de SAP [1796393](https://launchpad.support.sap.com/#/notes/1796393) contiene información sobre SAP BW.

- **Almacenamiento**: en Azure, SAP IQ admite discos administrados prémium (Windows y Linux), discos compartidos de Azure (solo Windows) y Azure NetApp Files (solo Linux). 

Consulte la [matriz de disponibilidad de productos](https://userapps.support.sap.com/sap/support/pam) para obtener información más actualizada según su versión de SAP IQ. 

## <a name="sizing"></a>Ajuste de tamaño

El tamaño de SAP IQ se limita a la CPU, la memoria y el almacenamiento. Las directrices generales de dimensionamiento de SAP IQ en Azure se pueden encontrar en la [nota de SAP 1951789](https://launchpad.support.sap.com/#/notes/1951789). La recomendación de dimensionamiento que se obtiene siguiendo las directrices se debe asignar a los tipos de máquina virtual de Azure certificados para SAP. En la [nota de SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533), se especifica la lista de los productos de SAP y los tipos de máquinas virtuales de Azure admitidos. 

La guía de dimensionamiento de IQ de SAP y la hoja de cálculo de dimensionamiento mencionadas en la [nota de SAP 1951789](https://launchpad.support.sap.com/#/notes/1951789) se desarrollaron para el uso nativo de una base de datos de SAP IQ. Dado que no reflejan los recursos para el planeamiento de una base de datos de SAP IQ, es posible que termine con recursos no utilizados para SAP NLS.

## <a name="azure-resources"></a>Recursos de Azure

### <a name="regions"></a>Regions

Si ya está ejecutando sus sistemas SAP en Azure, probablemente ya tiene identificada su región. La implementación de SAP IQ debe estar en la misma región que el sistema SAP BW para el que va a implementar la solución NLS. 

Para determinar la arquitectura de SAP IQ, debe asegurarse de que los servicios requeridos por SAP IQ, como Azure NetApp Files (solo NFS para Linux), estén disponibles en esa región. Para comprobar la disponibilidad del servicio en su región, consulte la página web [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/).

### <a name="availability-sets"></a>Conjuntos de disponibilidad 

Para lograr la redundancia de los sistemas SAP en la infraestructura de Azure, la aplicación se debe implementar en conjuntos de disponibilidad o zonas de disponibilidad. Aunque puede lograr la alta disponibilidad de SAP IQ mediante la arquitectura multiplex de SAP IQ, la arquitectura multiplex no cumple los requisitos de la solución NLS. 

Para lograr una alta disponibilidad para la arquitectura simplex de SAP IQ, es necesario configurar un clúster de dos nodos con una solución personalizada. El clúster de SAP IQ de dos nodos se puede implementar en conjuntos de disponibilidad o zonas de disponibilidad, pero el tipo de almacenamiento de Azure que se conecta a los nodos decide su método de implementación. Actualmente, los discos prémium compartidos de Azure y Azure NetApp Files no admiten la implementación zonal. Esto deja solo la opción de implementación de SAP IQ en conjuntos de disponibilidad. 

### <a name="virtual-machines"></a>Máquinas virtuales

En función del dimensionamiento de SAP IQ, debe asignar sus requisitos a máquinas virtuales de Azure. Este enfoque se admite en Azure para productos de SAP. La [nota de SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533) es un buen punto de partida que enumera los tipos de máquina virtual de Azure admitidos para los productos de SAP en Windows y Linux. 

Además de seleccionar solo tipos de máquina virtual admitidos, también debe comprobar si estos tipos de máquina virtual están disponibles en una región específica. Puede comprobar la disponibilidad de tipos de máquina virtual en la página web [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/). Para elegir el modelo de precios, consulte [Máquinas virtuales de Azure para la carga de trabajo de SAP](planning-guide.md#azure-virtual-machines-for-sap-workload). 

> [!TIP]
> En el caso de los sistemas de producción, se recomienda usar máquinas virtuales de la serie E debido a su relación entre núcleos y memoria.

### <a name="storage"></a>Storage

Azure Storage tiene varios tipos de almacenamiento disponibles para los clientes. Puede encontrar detalles sobre ellos en el artículo [¿Qué tipos de disco están disponibles en Azure?](../../disks-types.md) 

Algunos de los tipos de almacenamiento de Azure tienen un uso limitado para escenarios de SAP, pero otros tipos son adecuados u optimizados para escenarios específicos de carga de trabajo de SAP. Para más información, consulte la guía [Tipos de Azure Storage para una carga de trabajo de SAP](planning-guide-storage.md). En ella, se resaltan las opciones de almacenamiento que son adecuadas para SAP. 

Para SAP IQ en Azure, puede usar los siguientes tipos de almacenamiento de Azure. La elección depende del sistema operativo (Windows o Linux) y del método de implementación (independiente o de alta disponibilidad).

- Azure Managed Disks

  Un [disco administrado](../../managed-disks-overview.md) es un volumen de almacenamiento de nivel de bloque administrado por Azure. Puede usar discos administrados para la implementación simplex de SAP IQ. Hay varios tipos de discos administrados disponibles, pero se recomienda usar [SSD prémium](../../disks-types.md#premium-ssd) para SAP IQ. 

- Discos compartidos de Azure

  Los [discos compartidos](../../disks-shared.md) son una nueva característica para los discos administrados de Azure que permite conectar un disco administrado a varias máquinas virtuales al mismo tiempo. Los discos administrados compartidos no ofrecen de forma nativa un sistema de archivos totalmente administrado al que se pueda acceder mediante SMB o NFS. Debe usar un administrador de clústeres, como el [clúster de conmutación por error de Windows Server](https://github.com/MicrosoftDocs/windowsserverdocs/blob/master/WindowsServerDocs/failover-clustering/failover-clustering-overview.md) (WSFC), que controle la comunicación de los nodos de clúster, así como el bloqueo de las escrituras. 
  
  Para implementar una solución de alta disponibilidad para la arquitectura simplex de SAP IQ en Windows, puede usar discos compartidos de Azure entre los dos nodos administrados por WSFC. En el artículo [Implementación de la solución de alta disponibilidad SAP IQ NLS con un disco compartido de Azure en Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089), se describe la arquitectura de implementación de SAP IQ con discos compartidos de Azure.

- Azure NetApp Files

  La implementación de SAP IQ en Linux puede usar [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) como sistema de archivos (protocolo NFS) para su instalación como una solución independiente o de alta disponibilidad. Esta oferta de almacenamiento no está disponible en todas las regiones. Consulte la página web [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/) para buscar información actualizada. La arquitectura de implementación de SAP IQ con Azure NetApp Files se describe en el artículo [Implementación de la solución de alta disponibilidad SAP IQ NLS con Azure NetApp Files en SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172).

En la tabla siguiente, se muestra la recomendación para cada tipo de almacenamiento en función del sistema operativo: 

| Tipo de almacenamiento        | Windows | Linux |
| ------------------- | ------- | ----- |
| Azure Managed Disks | Sí     | Sí   |
| Discos compartidos de Azure  | Sí     | No    |
| Azure NetApp Files  | No      | Sí   |

### <a name="networking"></a>Redes

Azure proporciona una infraestructura de red que permite la asignación de todos los escenarios que se pueden realizar para un sistema SAP BW que usa SAP IQ como almacenamiento casi en línea. Estos escenarios incluyen la conexión a sistemas locales, la conexión a sistemas de otras redes virtuales y otros. Para más información, consulte [Redes de Microsoft Azure para cargas de trabajo de SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

## <a name="deploy-sap-iq-on-windows"></a>Implementación de SAP IQ en Windows

### <a name="server-preparation-and-installation"></a>Preparación e instalación del servidor

Para preparar los servidores para la implementación de NLS con SAP IQ en Windows, puede obtener la información más actualizada en la [nota de SAP 2780668 con una guía para la implementación de BW NLS con SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668). Contiene información completa sobre los requisitos previos para los sistemas SAP BW, el diseño del sistema de archivos de SAP IQ, la instalación, las tareas posteriores a la configuración y la integración de SAP BW NLS con SAP IQ.

### <a name="high-availability-deployment"></a>Implementación de alta disponibilidad

SAP IQ admite una arquitectura simplex y una arquitectura multiplex. En el caso de la solución NLS, solo está disponible y evaluada la arquitectura de servidor simplex. Simplex es una instancia única de un servidor de SAP IQ que se ejecuta en una sola máquina virtual. 

Técnicamente, puede lograr la alta disponibilidad de SAP IQ mediante la arquitectura de servidor multiplex, pero la arquitectura multiplex no cumple los requisitos de la solución NLS. En el caso de la arquitectura de servidor simplex, SAP no proporciona características ni procedimientos para ejecutar SAP IQ en una configuración de alta disponibilidad. 

Para configurar la alta disponibilidad de SAP IQ en Windows para la arquitectura de servidor simplex, debe configurar una solución personalizada, lo que requiere una configuración adicional, como un clúster de conmutación por error de Windows Server y discos compartidos. Una de estas soluciones personalizadas para SAP IQ en Windows se describe con detalle en [Implementación de una solución SAP IQ NLS de alta disponibilidad con un disco compartido de Azure en Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089).

### <a name="backup-and-restore"></a>Copia de seguridad y restauración

En Azure, puede programar la copia de seguridad de la base de datos de SAP IQ como se describe en [Administración de SAP IQ: copia de seguridad, restauración y recuperación de datos](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html). SAP IQ proporciona los siguientes tipos de copias de seguridad de base de datos. Puede encontrar detalles sobre cada tipo de copia de seguridad en [Escenarios de copia de seguridad](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html).

- **Copia de seguridad completa**: realiza una copia completa de la base de datos. 
- **Copia de seguridad incremental**: copia todas las transacciones desde la última copia de seguridad de cualquier tipo. 
- **Incremental desde la copia de seguridad completa**: hace una copia de seguridad de todos los cambios realizados en la base de datos desde la última copia de seguridad completa.
- **Copia de seguridad virtual**: copia toda la base de datos excepto los datos y los metadatos de las tablas del almacén de SAP IQ.

Según el tamaño de la base de datos de SAP IQ, puede programar la copia de seguridad de la base de datos desde cualquiera de los escenarios de copia de seguridad. Pero si usa SAP IQ con la interfaz NLS que proporciona SAP, es posible que quiera automatizar el proceso de copia de seguridad de una base de datos de SAP IQ. La automatización garantiza que la base de datos de SAP IQ siempre se pueda recuperar a un estado coherente sin pérdida de los datos que se mueven entre la base de datos principal y la base de datos de SAP IQ. Para obtener detalles sobre la configuración de la automatización para el almacenamiento casi en línea de SAP IQ, consulte [Nota de SAP 2741824: Configuración de la automatización de la copia de seguridad para el almacenamiento en reposo y casi en línea de SAP IQ](https://launchpad.support.sap.com/#/notes/2741824). 

Para una base de datos de SAP IQ de gran tamaño, puede usar copias de seguridad virtuales. Para más información, consulte [Copias de seguridad virtuales](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html) e [Introducción a la copia de seguridad virtual en SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ). Consulte también [Nota de SAP 2461985: Copia de seguridad de una base de datos de SAP IQ de gran tamaño](https://launchpad.support.sap.com/#/notes/0002461985).

Si usa una unidad de red (protocolo SMB) para realizar copias de seguridad y restaurar el servidor de SAP IQ en Windows, asegúrese de usar la ruta de acceso UNC para la copia de seguridad. Se requieren tres barras diagonales inversas (`\\\`) cuando se usa una ruta de acceso UNC para la copia de seguridad y restauración:

```sql
BACKUP DATABASE FULL TO '\\\sapiq.internal.contoso.net\sapiq-backup\backup\data\<filename>'
```

## <a name="deploy-sap-iq-on-linux"></a>Implementación de SAP IQ en Linux

### <a name="server-preparation-and-installation"></a>Preparación e instalación del servidor

Para preparar los servidores para la implementación de NLS con SAP IQ en Linux, puede obtener la información más actualizada en la [nota de SAP 2780668 con una guía para la implementación de BW NLS con SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668). Contiene información completa sobre los requisitos previos para los sistemas SAP BW, el diseño del sistema de archivos de SAP IQ, la instalación, las tareas posteriores a la configuración y la integración de SAP BW NLS con SAP IQ.

### <a name="high-availability-deployment"></a>Implementación de alta disponibilidad

SAP IQ admite una arquitectura simplex y una arquitectura multiplex. En el caso de la solución NLS, solo está disponible y evaluada la arquitectura de servidor simplex. Simplex es una instancia única de un servidor de SAP IQ que se ejecuta en una sola máquina virtual. 

Técnicamente, puede lograr la alta disponibilidad de SAP IQ mediante la arquitectura de servidor multiplex, pero la arquitectura multiplex no cumple los requisitos de la solución NLS. En el caso de la arquitectura de servidor simplex, SAP no proporciona características ni procedimientos para ejecutar SAP IQ en una configuración de alta disponibilidad.

Para configurar la alta disponibilidad de SAP IQ en Linux para una arquitectura de servidor simplex, es necesario configurar una solución personalizada, que requiere una configuración adicional, como Pacemaker. Una de estas soluciones personalizadas para SAP IQ en Linux se describe con detalle en [Implementación de una solución SAP IQ NLS de alta disponibilidad con Azure NetApp Files en SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172).

### <a name="backup-and-restore"></a>Copia de seguridad y restauración

En Azure, puede programar la copia de seguridad de la base de datos de SAP IQ como se describe en [Administración de SAP IQ: copia de seguridad, restauración y recuperación de datos](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html). SAP IQ proporciona los siguientes tipos de copias de seguridad de base de datos. Puede encontrar detalles sobre cada tipo de copia de seguridad en [Escenarios de copia de seguridad](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html).

- **Copia de seguridad completa**: realiza una copia completa de la base de datos. 
- **Copia de seguridad incremental**: copia todas las transacciones desde la última copia de seguridad de cualquier tipo. 
- **Incremental desde la copia de seguridad completa**: hace una copia de seguridad de todos los cambios realizados en la base de datos desde la última copia de seguridad completa.
- **Copia de seguridad virtual**: copia toda la base de datos excepto los datos y los metadatos de las tablas del almacén de SAP IQ.

Según el tamaño de la base de datos de SAP IQ, puede programar la copia de seguridad de la base de datos desde cualquiera de los escenarios de copia de seguridad. Pero si usa SAP IQ con la interfaz NLS que proporciona SAP, es posible que quiera automatizar el proceso de copia de seguridad de una base de datos de SAP IQ. La automatización garantiza que la base de datos de SAP IQ siempre se pueda recuperar a un estado coherente sin pérdida de los datos que se mueven entre la base de datos principal y la base de datos de SAP IQ. Para obtener detalles sobre la configuración de la automatización para el almacenamiento casi en línea de SAP IQ, consulte [Nota de SAP 2741824: Configuración de la automatización de la copia de seguridad para el almacenamiento en reposo y casi en línea de SAP IQ](https://launchpad.support.sap.com/#/notes/2741824). 

Para una base de datos de SAP IQ de gran tamaño, puede usar copias de seguridad virtuales. Para más información, consulte [Copias de seguridad virtuales](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html) e [Introducción a la copia de seguridad virtual en SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ). Consulte también [Nota de SAP 2461985: Copia de seguridad de una base de datos de SAP IQ de gran tamaño](https://launchpad.support.sap.com/#/notes/0002461985).

## <a name="disaster-recovery"></a>Recuperación ante desastres

En esta sección, se explica la estrategia para proporcionar protección de recuperación ante desastres (DR) para la solución NLS de SAP IQ. Complementa el artículo [Configuración de la recuperación ante desastres para la implementación de una aplicación de SAP NetWeaver de niveles múltiples](../../../site-recovery/site-recovery-sap.md), que representa los recursos principales para el enfoque general de DR de SAP. El proceso descrito en este artículo se presenta en un nivel abstracto. Debe validar los pasos exactos y realizar una prueba exhaustiva de la estrategia de recuperación ante desastres. 

Para SAP IQ, consulte la [nota de SAP 2566083](https://launchpad.support.sap.com/#/notes/0002566083), en la que se describen los métodos para implementar un entorno de recuperación ante desastres de forma segura. En Azure, también puede usar [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) para la estrategia de recuperación ante desastres de SAP IQ. La estrategia de recuperación ante desastres de SAP IQ depende de la forma en que se implemente en Azure y también debe estar en línea con el sistema SAP BW. 

### <a name="standalone-deployment-of-sap-iq"></a>Implementación independiente de SAP IQ

Si ha instalado SAP IQ como un sistema independiente que no tiene redundancia de nivel de aplicación ni alta disponibilidad, pero la empresa requiere una configuración de recuperación ante desastres, todos los discos (discos administrados de Azure) conectados a la máquina virtual serán locales. 
  
Puede usar [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) para replicar una máquina virtual de SAP IQ independiente en la región secundaria. Este replica en la región secundaria los servidores y todos los discos administrados conectados para que, si se producen desastres o interrupciones, pueda conmutar por error fácilmente al entorno replicado y seguir trabajando. Para comenzar a replicar las máquinas virtuales de SAP IQ en la región de recuperación ante desastres de Azure, siga las instrucciones de [Replicación de una máquina virtual en Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md). 

### <a name="highly-available-deployment-of-sap-iq"></a>Implementación de alta disponibilidad de SAP IQ

Si ha instalado SAP IQ como un sistema de alta disponibilidad en el que los archivos binarios de SAP IQ y los archivos de base de datos están en un disco compartido de Azure (solo Windows) o en una unidad de red como Azure NetApp Files (solo Linux), debe identificar:

- Si necesita el mismo sistema SAP IQ de alta disponibilidad en el sitio de recuperación ante desastres.
- Si una instancia de SAP IQ independiente será suficiente para sus requisitos empresariales. 
  
Si necesita una instancia de SAP IQ independiente en el sitio de recuperación ante desastres, puede usar [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) para replicar la máquina virtual principal de SAP IQ en la región secundaria. Este replica los servidores y todos los discos administrados conectados localmente a la región secundaria, pero no replica un disco compartido de Azure ni una unidad de red como Azure NetApp Files. 
  
Para copiar datos desde un disco compartido de Azure o una unidad de red, puede usar cualquier herramienta de copia basada en archivos para replicar los datos entre regiones de Azure. Para más información sobre cómo copiar un volumen de Azure NetApp Files en otra región, consulte [Preguntas más frecuentes acerca de Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region).

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de la recuperación ante desastres para la implementación de una aplicación de SAP de niveles múltiples](../../../site-recovery/site-recovery-sap.md)
- [Planeamiento e implementación de Azure Virtual Machines para SAP](planning-guide.md)
- [Implementación de Azure Virtual Machines para SAP](deployment-guide.md)
