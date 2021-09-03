---
title: Implementación de SAP NLS/SDA con SAP IQ en Azure | Microsoft Docs
description: Planee, implemente y configure la solución SAP NLS/SDA con SAP IQ en Azure.
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
ms.openlocfilehash: a0641f6c00712e0d3dd32ff04d7bff9695dc4ce1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781328"
---
# <a name="sap-bw-near-line-storage-nls-implementation-guide-with-sap-iq-on-azure"></a>Guía de implementación de Nearline Storage (NLS) de SAP BW con SAP IQ en Azure

## <a name="overview"></a>Información general

A lo largo de los años, el cliente que ejecuta el sistema SAP BW ve un crecimiento exponencial en el tamaño de la base de datos que se traduce en un aumento en el costo de proceso. Para lograr el equilibrio perfecto entre costo y rendimiento, el cliente puede usar Nearline Storage (NLS) para migrar los datos históricos. La implementación de NLS basada en SAP IQ es el método estándar de SAP para mover datos históricos de la base de datos principal (SAP HANA o anyDB). El adaptador de SAP IQ como una solución Nearline se entrega con el sistema SAP BW. La integración de SAP IQ permite separar los datos a los que se accede con frecuencia de aquellos con un acceso no frecuente, lo que hace que la demanda de recursos sea menor en el sistema SAP BW. 

En esta guía se proporcionan las directrices para planear, implementar y configurar el almacenamiento Nearline (NLS) de SAP BW con SAP IQ en Azure. Esta guía está pensada para describir los servicios y características comunes de Azure que son pertinentes para la implementación de SAP IQ - NLS y no se describe ninguna solución de asociado de NLS. Esta guía no está pensada para reemplazar la documentación estándar de SAP sobre la implementación de NLS con SAP IQ, sino que complementa la documentación oficial de instalación y administración. 

## <a name="solution-overview"></a>Información general de la solución

En un sistema BW operativo, el volumen de datos aumenta constantemente y estos datos son necesarios durante un período más largo debido a los requisitos empresariales y legales. El gran volumen de datos puede afectar al rendimiento del sistema y aumenta el esfuerzo de administración, lo que da lugar a la necesidad de implementar una estrategia de vencimiento de los datos. Si quiere mantener la cantidad de datos en el sistema SAP BW sin eliminarlos, puede usar el archivado de datos. Los datos se mueven primero al nivel de almacenamiento de archivo o al almacenamiento Nearline y, después, se eliminan del sistema BW. Puede acceder a los datos directamente o volver a cargarlos según sea necesario en función de cómo se hayan archivado. 

Los usuarios de SAP BW pueden usar SAP IQ como solución de almacenamiento Nearline (NLS). El adaptador para SAP IQ como solución Nearline se entrega con el sistema BW. Con NLS implementado, los datos usados con frecuencia se almacenan en una base de datos en línea de SAP BW (SAP HANA o cualquier base de datos), mientras que los datos a los que se accede con poca frecuencia se almacenan en SAP IQ, lo que reduce el costo de administrar los datos y mejora el rendimiento del sistema SAP BW. Para garantizar la coherencia entre los datos en línea y los datos Nearline, las particiones archivadas se bloquean y son de solo lectura. 

SAP IQ admite dos tipos de arquitectura: simplex y multiplex. En la arquitectura simplex, una única instancia del servidor IQ de SAP IQ se ejecuta en una sola máquina virtual y los archivos pueden encontrarse en una máquina host o en un dispositivo de almacenamiento de red. 

> [!Important]
> En el caso de la solución SAP-NLS, SAP solo proporciona o evalúa la arquitectura simplex.

![Información general sobre la solución SAP IQ](media/sap-iq-deployment-guide/sap-iq-solution-overview.png)

En Azure, el servidor de SAP IQ debe implementarse en una máquina virtual (VM) independiente. No se recomienda instalar software de SAP IQ en un servidor existente que ya tenga otra instancia de base de datos en ejecución, ya que SAP IQ usa una CPU y una memoria completas para su propio uso. Se puede usar un servidor de SAP IQ para varias implementaciones de SAP-NLS. 

## <a name="support-matrix"></a>Matrices compatibles

En esta sección se describe de forma general la matriz de compatibilidad para la solución SAP IQ-NLS, que se trata con más detalle en este documento. Además, consulte la [matriz de disponibilidad de productos (PAM)](https://userapps.support.sap.com/sap/support/pam) para obtener más información actualizada según su versión de SAP IQ. 

- **Sistema operativo**: SAP IQ solo está certificado en el nivel de sistema operativo. Puede ejecutar el sistema operativo certificado de SAP IQ en el entorno de Azure, siempre y cuando se admita para ejecutarse en la infraestructura de Azure. Para obtener más información, consulte la nota de SAP [2133194](https://launchpad.support.sap.com/#/notes/2133194).

- **Compatibilidad de SAP BW**: el almacenamiento Nearline para SAP IQ solo se libera para los sistemas SAP BW que ya se ejecutan con Unicode. Siga la nota de SAP [1796393](https://launchpad.support.sap.com/#/notes/1796393) que contiene información sobre SAP BW.

- **Almacenamiento**: en Azure, SAP IQ admite discos administrados prémium (Windows/Linux), discos compartidos de Azure (solo Windows) y Azure NetApp Files (solo NFS - Linux). 

## <a name="sizing"></a>Ajuste de tamaño

El tamaño de SAP IQ se limita a la CPU, la memoria y el almacenamiento. Las directrices generales de tamaño de SAP IQ en Azure se pueden encontrar en la nota de SAP [1951789](https://launchpad.support.sap.com/#/notes/1951789). La recomendación de tamaño que se obtiene siguiendo las directrices debe asignarse a los tipos de máquina virtual de Azure certificados para SAP. En la nota de SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533), se especifica la lista de los productos de SAP y los tipos de máquinas virtuales de Azure admitidos. 

> [!Tip]
>
> Para un sistema productivo, se recomienda usar máquinas virtuales de la serie E debido a su relación de núcleo a memoria. 

La guía de tamaño y la hoja de cálculo de tamaños de SAP IQ mencionadas en la nota de SAP [1951789](https://launchpad.support.sap.com/#/notes/1951789) se desarrollan para el uso nativo de la base de datos de SAP IQ y no reflejan los recursos para el planeamiento de la base de datos de IQ <SID>; podría acabar con recursos no usados para SAP-NLS.

## <a name="azure-resources"></a>Recursos de Azure

### <a name="choosing-regions"></a>Elección de regiones

Si ya está ejecutando sus sistemas SAP en Azure, probablemente ya tiene identificada su región. La implementación de SAP IQ debe estar en la misma región que la del sistema SAP BW para el que va a implementar la solución NLS. Pero debe investigar para asegurarse de que los servicios necesarios que necesita SAP IQ, como Azure NetApp Files (solo NFS - Linux) estén disponibles en esas regiones para decidir la arquitectura de SAP IQ. Para comprobar la disponibilidad del servicio en su región, puede consultar el sitio [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/).

### <a name="availability-sets"></a>Conjuntos de disponibilidad 

Para lograr la redundancia de los sistemas SAP en la infraestructura de Azure, la aplicación debe implementarse en conjuntos o zonas de disponibilidad. Técnicamente, la alta disponibilidad de SAP IQ se puede lograr mediante la arquitectura multiplex de IQ, pero esta arquitectura no cumple los requisitos de la solución NLS. Para lograr una alta disponibilidad para la arquitectura simplex de SAP IQ, es necesario configurar un clúster de dos nodos con una solución personalizada. El clúster de SAP IQ de dos nodos se puede implementar en conjuntos o zonas de disponibilidad, pero el almacenamiento de Azure que se conecta a los nodos decide su método de implementación. Actualmente, el disco prémium compartido de Azure y Azure NetApp Files no admiten la implementación zonal, que solo deja la implementación de SAP IQ en la opción del conjunto de disponibilidad. 

### <a name="virtual-machines"></a>Máquinas virtuales

Según el tamaño de SAP IQ, deberá asignar sus requisitos a máquinas virtuales de Azure, lo que se admite en Azure para el producto SAP. La nota de SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533) es un buen punto de partida que enumera los tipos de máquina virtual de Azure admitidos para los productos de SAP en Windows y Linux. Además, debe tener en cuenta que no solo debe seleccionar los tipos de VM que son compatibles, también hay que comprobar si están disponibles en una región específica. Puede comprobar la disponibilidad en la página de [productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/). Para elegir el modelo de precios, puede consultar [Máquinas virtuales de Azure para la carga de trabajo de SAP](planning-guide.md#azure-virtual-machines-for-sap-workload). 

Para un sistema productivo, se recomienda usar máquinas virtuales de la serie E debido a su relación de núcleo a memoria.

### <a name="storage"></a>Almacenamiento

Azure Storage tiene diferentes tipos de almacenamiento disponibles para los clientes, y los detalles de los mismos se pueden leer en el artículo [¿Qué tipos de disco están disponibles en Azure?](../../disks-types.md) Algunos de los tipos de almacenamiento tienen un uso limitado para escenarios de SAP. No obstante, hay varios tipos de almacenamiento de Azure Storage que son idóneos para escenarios específicos de carga de trabajo de SAP, o bien están optimizados para ello. Para obtener más información, consulte la guía de [Tipos de Azure Storage para una carga de trabajo de SAP](planning-guide-storage.md), ya que resalta las distintas opciones de almacenamiento que son adecuadas para SAP. Para SAP IQ en Azure, se puede usar el siguiente almacenamiento de Azure en función del sistema operativo (Windows o Linux) y del método de implementación (independiente o de alta disponibilidad).

- Discos administrados por Azure

  Es un volumen de almacenamiento de nivel de bloque que Azure administra. Puede usar discos administrados de Azure para la implementación simplex de SAP IQ. Hay diferentes tipos de [discos administrados de Azure](../../managed-disks-overview.md) disponibles, pero se recomienda usar los [SSD prémium](../../disks-types.md#premium-ssd) para SAP IQ. 

- Discos compartidos de Azure

  Los [discos compartidos de Azure](../../disks-shared.md) son una nueva característica para los discos administrados de Azure que permite conectar un disco administrado a varias máquinas virtuales (VM) al mismo tiempo. Los discos administrados compartidos no ofrecen de forma nativa un sistema de archivos totalmente administrado al que se pueda obtener acceso mediante SMB/NFS. Debe usar un administrador de clústeres, como el [clúster de conmutación por error de Windows Server](https://github.com/MicrosoftDocs/windowsserverdocs/blob/master/WindowsServerDocs/failover-clustering/failover-clustering-overview.md) (WSFC), que controle la comunicación del nodo de clúster, así como el bloqueo de escritura. Para implementar una solución de alta disponibilidad para la arquitectura simplex de SAP IQ en Windows, puede usar discos compartidos de Azure entre dos nodos administrados por WSFC. La arquitectura de implementación de SAP IQ con un disco compartido de Azure se describe en el artículo [Implementación de la solución SAP IQ NLS de alta disponibilidad con un disco compartido de Azure en Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089).

- Azure NetApp Files

  La implementación de SAP IQ en Linux puede usar [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) como sistema de archivos (protocolo NFS) para instalarse como una solución independiente o de alta disponibilidad. Como esta oferta de almacenamiento no está disponible en todas las regiones, consulte el sitio [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/) para buscar información actualizada. La arquitectura de implementación de SAP IQ con Azure NetApp Files se describe en el artículo [Implementación de la solución SAP IQ NLS de alta disponibilidad con Azure NetApp Files en SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172).

En la tabla siguiente, se muestra la recomendación de cada tipo de almacenamiento en función del sistema operativo. 

| Tipo de almacenamiento        | Windows | Linux |
| ------------------- | ------- | ----- |
| Discos administrados por Azure | Sí     | Sí   |
| Discos compartidos de Azure  | Sí     | No    |
| Azure NetApp Files  | No      | Sí   |

### <a name="networking"></a>Redes

Azure proporciona una infraestructura de red que permite asignar todos los escenarios que se pueden llevar a cabo con el sistema SAP BW que usa SAP IQ como almacenamiento Nearline, como la conexión a un sistema local, a sistemas de diferentes redes virtuales y otros. Para obtener más información, consulte [Redes de Microsoft Azure para cargas de trabajo de SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

## <a name="deploy-sap-iq-on-windows"></a>Implementación de SAP IQ en Windows

### <a name="server-preparation-and-installation"></a>Preparación e instalación del servidor

Siga la guía más reciente de SAP para preparar los servidores para la implementación de NLS con SAP IQ en Windows. Para obtener la información más actualizada, consulte el primer documento de instrucciones publicado por SAP, que puede encontrar en la nota de SAP [2780668 - Primera guía de SAP - Implementación de BW NLS con SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668). Trata la información completa relacionada con los requisitos previos para los sistemas SAP BW, el diseño del sistema de archivos de IQ, la instalación, la configuración posterior y la integración de BW NLS con IQ.

### <a name="high-availability-deployment"></a>Implementación de alta disponibilidad

SAP IQ admite una arquitectura simplex y una arquitectura multiplex. En el caso de la solución NLS, solo está disponible y evaluada la arquitectura de servidor simplex. Simplex es una instancia única de un servidor de SAP IQ que se ejecuta en una sola máquina virtual. Técnicamente, la alta disponibilidad de SAP IQ se puede lograr mediante la arquitectura de servidor multiplex, pero esta arquitectura no cumple los requisitos de la solución NLS. En el caso de la arquitectura de servidor simplex, SAP no proporciona características ni procedimientos para ejecutar SAP IQ en una configuración de alta disponibilidad. 

Para configurar la alta disponibilidad de SAP IQ en Windows para la arquitectura de servidor simplex, debe configurar una solución personalizada, que requiere una configuración adicional, como el clúster de conmutación por error de Microsoft Windows Server, el disco compartido, etc. Una de estas soluciones personalizadas para SAP IQ en Windows se describe con detalle en el blog [Implementación de una solución SAP IQ NLS de alta disponibilidad con el disco compartido de Azure en Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089).

### <a name="back-up-and-restore"></a>Copia de seguridad y restauración

En Azure, puede programar la copia de seguridad de la base de datos de SAP IQ como se describe en [Administración de IQ: copia de seguridad, restauración y recuperación de datos](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html). SAP IQ proporciona diferentes tipos de copias de seguridad de base de datos y pueden encontrarse detalles sobre cada tipo de copia de seguridad en [Escenarios de copia de seguridad](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html).

- Copia de seguridad completa: realiza una copia completa de la base de datos. 
- Copia de seguridad incremental: copia todas las transacciones desde la última copia de seguridad de cualquier tipo. 
- Incremental desde la copia de seguridad completa: hace una copia de seguridad de todos los cambios realizados en la base de datos desde la última copia de seguridad completa.
- Copia de seguridad virtual: copia toda la base de datos excepto los datos de la tabla y los metadatos del almacén de IQ.

Según el tamaño de la base de datos de IQ, puede programar la copia de seguridad de la base de datos desde cualquiera de los escenarios de copia de seguridad. Pero si usa SAP IQ con la interfaz de NLS que proporciona SAP y quiere automatizar el proceso de copia de seguridad de la base de datos de IQ, lo que garantiza que la base de datos de SAP IQ siempre se pueda recuperar en un estado coherente sin pérdida de datos con respecto a los procesos de movimiento de datos entre la base de datos principal y la base de datos de SAP IQ, consulte la nota de SAP [2741824: Cómo configurar la automatización de la copia de seguridad para el almacenamiento Nearline o en reposo de SAP IQ](https://launchpad.support.sap.com/#/notes/2741824), en la que se proporcionan detalles sobre cómo configurar la automatización para el almacenamiento Nearline de SAP IQ. 

Para bases de datos de IQ grandes, puede usar la copia de seguridad virtual en SAP IQ. Para obtener más información sobre la copia de seguridad virtual, vea [Copias de seguridad virtuales](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html), [Introducción a la copia de seguridad virtual en SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ) y la nota de SAP [2461985: Cómo realizar una copia de seguridad de una base de datos grande de SAP IQ](https://launchpad.support.sap.com/#/notes/0002461985).

Si usa la unidad de red (protocolo SMB) para realizar copias de seguridad y restaurar el servidor de SAP IQ en Windows, asegúrese de usar la ruta UNC para la copia de seguridad. Se requieren tres barras diagonales inversas "\\\\\" al usar la ruta UNC para la copia de seguridad y la restauración.

```sql
BACKUP DATABASE FULL TO '\\\sapiq.internal.contoso.net\sapiq-backup\backup\data\<filename>'
```

## <a name="deploy-sap-iq-on-linux"></a>Implementación de SAP IQ en Linux

### <a name="server-preparation-and-installation"></a>Preparación e instalación del servidor

Siga la guía más reciente de SAP para preparar los servidores para la implementación de NLS con SAP IQ en Linux. Para obtener la información más actualizada, consulte el primer documento de instrucciones publicado por SAP, que puede encontrar en la nota de SAP [2780668 - Primera guía de SAP - Implementación de BW NLS con SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668). Trata la información completa relacionada con los requisitos previos para los sistemas SAP BW, el diseño del sistema de archivos de IQ, la instalación, la configuración posterior y la integración de BW NLS con IQ.

### <a name="high-availability-deployment"></a>Implementación de alta disponibilidad

SAP IQ admite una arquitectura simplex y una arquitectura multiplex. En el caso de la solución NLS, solo está disponible y evaluada la arquitectura de servidor simplex. Simplex es una instancia única de un servidor de SAP IQ que se ejecuta en una sola máquina virtual. Técnicamente, la alta disponibilidad de SAP IQ se puede lograr mediante la arquitectura de servidor multiplex, pero esta arquitectura no cumple los requisitos de la solución NLS. En el caso de la arquitectura de servidor simplex, SAP no proporciona características ni procedimientos para ejecutar SAP IQ en una configuración de alta disponibilidad. 

Para configurar la alta disponibilidad de SAP IQ en Linux para una arquitectura de servidor simplex, es necesario configurar una solución personalizada, que requiere una configuración adicional, como Pacemaker. Una de estas soluciones personalizadas para SAP IQ en Linux se describe con detalle en el blog [Implementación de una solución SAP IQ NLS de alta disponibilidad con Azure NetApp Files en SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172).

### <a name="back-up-and-restore"></a>Copia de seguridad y restauración

En Azure, puede programar la copia de seguridad de la base de datos de SAP IQ como se describe en [Administración de IQ: copia de seguridad, restauración y recuperación de datos](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html). SAP IQ proporciona diferentes tipos de copias de seguridad de base de datos y pueden encontrarse detalles sobre cada tipo de copia de seguridad en [Escenarios de copia de seguridad](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html).

- Copia de seguridad completa: realiza una copia completa de la base de datos. 
- Copia de seguridad incremental: copia todas las transacciones desde la última copia de seguridad de cualquier tipo. 
- Incremental desde la copia de seguridad completa: hace una copia de seguridad de todos los cambios realizados en la base de datos desde la última copia de seguridad completa.
- Copia de seguridad virtual: copia toda la base de datos excepto los datos de la tabla y los metadatos del almacén de IQ.

Según el tamaño de la base de datos de IQ, puede programar la copia de seguridad de la base de datos. Pero si usa SAP IQ con la interfaz de NLS que proporciona SAP y quiere automatizar el proceso de copia de seguridad de la base de datos de IQ, lo que garantiza que la base de datos de SAP IQ siempre se pueda recuperar en un estado sin pérdida de datos con respecto a los procesos de movimiento de datos entre la base de datos principal y la base de datos de SAP IQ, consulte la nota de SAP [2741824: Cómo configurar la automatización de la copia de seguridad para el almacenamiento Nearline o en reposo de SAP IQ](https://launchpad.support.sap.com/#/notes/2741824), en la que se proporcionan detalles sobre cómo configurar la automatización para el almacenamiento Nearline de SAP IQ. 

Para bases de datos de IQ grandes, puede usar la copia de seguridad virtual en SAP IQ. Para obtener más información sobre la copia de seguridad virtual, vea [Copias de seguridad virtuales](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html), [Introducción a la copia de seguridad virtual en SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ) y la nota de SAP [2461985: Cómo realizar una copia de seguridad de una base de datos grande de SAP IQ](https://launchpad.support.sap.com/#/notes/0002461985).

## <a name="disaster-recovery"></a>Recuperación ante desastres

En esta sección se explica la estrategia para proporcionar protección de recuperación ante desastres (DR) para la solución de SAP IQ - NLS. Complementa el documento [Recuperación ante desastres para SAP](../../../site-recovery/site-recovery-sap.md), que representa los recursos principales para el enfoque general de DR de SAP. El proceso descrito en este documento se presenta en un nivel abstracto. Pero debe validar los pasos exactos y realizar una prueba exhaustiva de la estrategia de recuperación ante desastres. 

Para SAP IQ, vea la nota de SAP [2566083](https://launchpad.support.sap.com/#/notes/0002566083), en la que se describen los métodos para implementar un entorno de recuperación ante desastres de forma segura. En Azure, también puede usar [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) para la estrategia de recuperación ante desastres de SAP IQ. La estrategia de recuperación ante desastres de SAP IQ depende de la forma en que se implemente en Azure y también debe estar en línea con el sistema SAP BW. 

- Implementación independiente de SAP IQ

  Si ha instalado SAP IQ como sistema independiente que no tiene redundancia de nivel de aplicación ni alta disponibilidad, pero la empresa requiere una configuración de recuperación ante desastres. En un sistema de IQ independiente, todos los discos (discos administrados por Azure) conectados a la máquina virtual serán locales. [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) se puede usar para replicar una máquina virtual de SAP IQ independiente en la región secundaria. Replica en la región secundaria los servidores y todos los discos administrados conectados para que, cuando se produzcan desastres o interrupciones, pueda conmutar por error fácilmente a su entorno replicado y seguir trabajando. Para comenzar a replicar las máquinas virtuales de SAP IQ en la región de recuperación ante desastres de Azure, siga las instrucciones de [Replicación de una máquina virtual en Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md). 

- Implementación de alta disponibilidad de SAP IQ

  Si ha instalado SAP IQ como un sistema de alta disponibilidad en el que los archivos binarios de IQ y los archivos de base de datos están en un disco compartido de Azure (solo Windows) o en la unidad de red, como Azure NetApp Files (solo Linux). En esta configuración, debe identificar si necesita una misma instancia de SAP IQ de alta disponibilidad en el sitio de recuperación ante desastres o si una instancia de SAP IQ independiente será suficiente para los requisitos empresariales. En el caso de que necesite una instancia de SAP IQ independiente en el sitio de recuperación ante desastres, puede usar [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) para replicar la máquina virtual principal de SAP IQ en la región secundaria. Replica los servidores y todos los discos administrados conectados localmente a la región secundaria, pero no replicará el disco compartido de Azure ni la unidad de red, como Azure NetApp Files. Para copiar datos desde un disco compartido de Azure o una unidad de red, puede usar cualquier herramienta de copia de base de archivos para replicar datos entre regiones de Azure. Para más información sobre cómo copiar el volumen de Azure NetApp Files en otra región, consulte [Preguntas más frecuentes acerca de Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region).

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de la recuperación ante desastres para la implementación de una aplicación de SAP de niveles múltiples](../../../site-recovery/site-recovery-sap.md)
- [Planeamiento e implementación de Azure Virtual Machines para SAP](planning-guide.md)
- [Implementación de Azure Virtual Machines para SAP](deployment-guide.md)
