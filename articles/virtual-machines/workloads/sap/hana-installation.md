---
title: Instalación de SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Obtenga información sobre cómo instalar y configurar SAP HANA en Azure (instancias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/16/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cfd57400eb50651dd7d908dad4c50ab94760286a
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114404973"
---
# <a name="install-and-configure-sap-hana-large-instances-on-azure"></a>Instalación y configuración de SAP HANA en Azure (instancias grandes)

En este artículo se explica cómo validar, configurar e instalar SAP HANA (instancias grandes) (HLI) en Azure (que también se conoce como BareMetal Infrastructure).

## <a name="prerequisites"></a>Requisitos previos

Antes de leer este artículo, familiarícese con:
- [Términos comunes de HANA (instancias grandes)](hana-know-terms.md)
- [SKU de HANA (instancias grandes)](hana-available-skus.md)

Consulte también:
- [Conexión de las máquinas virtuales de Azure a HANA Instancias grandes](hana-connect-azure-vm-large-instances.md)
- [Conexión de una red virtual a HANA (instancias grandes)](hana-connect-vnet-express-route.md)

## <a name="planning-your-installation"></a>Planeamiento de la instalación

La instalación de SAP HANA es su responsabilidad. Puede empezar a instalar un servidor nuevo de SAP HANA en Azure (instancias grandes) después de establecer la conectividad entre las redes virtuales de Azure y las unidades de HANA (instancia grande). 

> [!Note]
> Según la directiva de SAP, la instalación de SAP HANA la debe realizar una persona que haya aprobado el examen de certificación de Certified SAP Technology Associate, el examen de certificación de instalación de SAP HANA o que sea integrador de sistemas (SI) certificado por SAP.

Cuando tenga previsto instalar HANA 2.0, consulte la [nota de compatibilidad de SAP n.° 2235581: Sistemas operativos compatibles con SAP HANA](https://launchpad.support.sap.com/#/notes/2235581/E). Asegúrese de que el sistema operativo (SO) sea compatible con la versión de SAP HANA que va a instalar. El sistema operativo compatible con HANA 2.0 es más restrictivo que el SO compatible con HANA 1.0. Confirme que la versión del sistema operativo que le interesa es compatible con la instancia grande de HANA determinada. Use esta [lista](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) y seleccione la HLI para ver los detalles de la lista de SO compatibles para esa unidad. 

Antes de comenzar la instalación de HANA valide lo siguiente:
- [Unidades HLI](#validate-the-hana-large-instance-units)
- [Configuración del sistema operativo](#operating-system)
- [Configuración de la red](#networking)
- [Configuración de almacenamiento](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Validación de las unidades de HANA (instancias grandes)

Después de recibir HANA (instancias grandes) de Microsoft, establezca acceso y conectividad con ellas. Luego, valide la configuración siguiente y haga los ajustes necesarios.

1. Compruebe en Azure Portal si las instancias se muestran con las SKU y el sistema operativo correctos. Para más información, consulte [Control de instancias grandes de HANA en Azure mediante Azure Portal](./hana-li-portal.md).

2. Registre el sistema operativo de la instancia con el proveedor del sistema operativo. Este paso incluye registrar el sistema operativo SUSE Linux en una instancia de SUSE SMT que se implementó en una máquina virtual de Azure. 

    La instancia grande de HANA se puede conectar a esta instancia de SMT. (Para más información, consulte [Configuración de servidor SMT para SUSE Linux](hana-setup-smt.md)). Si usa un sistema operativo Red Hat, se debe registrar con la instancia de Red Hat Subscription Manager a la que necesite conectarse. Para más información, consulte los comentarios que aparecen en [¿Qué es SAP HANA en Azure (instancias grandes)?](./hana-overview-architecture.md?toc=/azure/virtual-machines/linux/toc.json). 

    Este paso es necesario para aplicar revisiones en el sistema operativo, lo que es su responsabilidad. En el caso de SUSE, consulte la documentación sobre la [instalación y configuración de SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

3. Compruebe si hay revisiones y correcciones nuevas de la versión específica del sistema operativo. Compruebe que HANA (instancia grande) tiene las revisiones más recientes. A veces no se incluyen las revisiones más recientes, así que asegúrese de comprobarlo.

4. Compruebe las notas de SAP correspondientes a la instalación y configuración de SAP HANA sobre la versión específica del sistema operativo. Microsoft no siempre configurará una HLI por completo. Los cambios en las recomendaciones o las modificaciones en las notas o configuraciones de SAP en función de escenarios individuales pueden hacer que esto sea imposible.  

    Por lo tanto, asegúrese de leer las notas de SAP relacionadas con SAP HANA para su versión exacta de Linux. Compruebe también las configuraciones de la versión del sistema operativo necesaria y aplique los valores de configuración si todavía no lo hace.

    Específicamente, compruebe los siguientes parámetros y, si es necesario, ajústelos a:

    - net.core.rmem_max = 16777216
    - net.core.wmem_max = 16777216
    - net.core.rmem_default = 16777216
    - net.core.wmem_default = 16777216
    - net.core.optmem_max = 16777216
    - net.ipv4.tcp_rmem = 65536 16777216 16777216
    - net.ipv4.tcp_wmem = 65536 16777216 16777216

    A partir de SP1 SLES12 y Red Hat Enterprise Linux 7.2, se deben establecer estos parámetros en un archivo de configuración en el directorio /etc/sysctl.d. Por ejemplo, se debe crear un archivo de configuración con el nombre 91-NetApp-HANA.conf. Para las versiones anteriores de SLES y RHEL, estos parámetros deben establecerse en in/etc/sysctl.conf.

    Para todas las versiones de RHEL que empiecen con RHEL 6.3, tenga en cuenta: 
    - El parámetro sunrpc.tcp_slot_table_entries = 128 se debe establecer en /etc/modprobe.d/sunrpc-local.conf. Si el archivo no existe, agregue la entrada para crearlo: 
        - options sunrpc tcp_max_slot_table_entries=128

5. Compruebe la hora del sistema de la instancia grande de HANA. Las instancias se implementan con una zona horaria del sistema. Esta zona horaria representa la ubicación de la región de Azure donde se encuentra el sello de instancia grande de HANA. Puede cambiar la hora del sistema o la zona horaria de sus instancias. 

    Si pide más instancias para su inquilino, se debe adaptar la zona horaria de las instancias recién entregadas. Microsoft no tiene ninguna información sobre la zona horaria del sistema donde instale las instancias tras la entrega. Por lo tanto, las instancias recién implementadas pueden no tener la misma zona horaria que a las que cambió. Es su responsabilidad adaptar la zona horaria de las instancias que se entregaron, según sea necesario. 

6. Compruebe etc/hosts. A medida que se van entregando las hojas, se les asignan direcciones IP diferentes con fines distintos. Es importante comprobar el archivo etc/hosts cuando se agregan unidades a un inquilino existente. Es posible que el archivo etc/hosts de los sistemas recién implementados no se mantenga correctamente con las direcciones IP de los sistemas entregados anteriormente. Asegúrese de que una instancia recién implementada pueda resolver los nombres de las unidades que implementó en el inquilino. 


## <a name="operating-system"></a>Sistema operativo

El espacio de intercambio de la imagen del sistema operativo proporcionada está establecido en 2 GB de acuerdo con la [nota de compatibilidad de SAP n.º 1999997 con las preguntas frecuentes sobre la memoria de SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Si desea una configuración diferente, debe establecerla por sí mismo.

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/download/) es la distribución de Linux instalada para SAP HANA en Azure (instancias grandes). Esta distribución proporciona funcionalidades específicas de SAP, incluidos los parámetros predefinidos para ejecutar SAP en SLES de manera eficaz.

Si desea obtener varios recursos útiles relacionados con la implementación de SAP HANA en SLES, consulte:
- [Biblioteca de recursos/notas del producto](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) en el sitio web de SUSE.
- [SAP en SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) en SAP Community Network (SCN).

Estos recursos incluyen información sobre cómo configurar la alta disponibilidad, la protección de seguridad específica de las operaciones de SAP, y mucho más.

Estos son otros recursos para SAP en SUSE:

- [SAP HANA on SUSE Linux Site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) (Sitio de SAP HANA en SUSE Linux)
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.scribd.com/document/351887168/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12-color-en-pdf) (Procedimiento recomendado para SAP: poner en cola la replicación - SAP NetWeaver en SUSE Linux Enterprise 12)
- [ClamSAP – SLES Virus Protection for SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (ClamSAP: protección contra virus de SLES para SAP), incluido SLES 12 para SAP Applications

Los documentos siguientes son las notas de compatibilidad de SAP aplicables a la implementación de SAP HANA en SLES 12:

- [SAP Support Note #1944799 - SAP HANA Guidelines for SLES Operating System Installation](http://service.sap.com/sap/support/notes/1944799) (Nota de compatibilidad de SAP n.º 1944799: Instrucciones de SAP HANA para la instalación del sistema operativo SLES)
- [SAP Support Note #2205917 - SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E) (Nota de compatibilidad de SAP n.º 2205917: Configuración recomendada del sistema operativo de SAP HANA DB para SLES 12 en aplicaciones SAP)
- [SAP support note #1984787 – SUSE Linux Enterprise Server 12:  installation notes](https://launchpad.support.sap.com/#/notes/1984787) (Nota de compatibilidad de SAP n.º 1984787: Notas de instalación de SUSE Linux Enterprise Server 12)
- [SAP support note #171356 – SAP software on Linux:  General Information](https://launchpad.support.sap.com/#/notes/1984787) (Nota de compatibilidad de SAP nº 171356 - Software SAP en Linux: información general)
- [SAP support note #1391070 – Linux UUID solutions](https://launchpad.support.sap.com/#/notes/1391070) (Nota de compatibilidad de SAP n.º 1391070: Soluciones UUID de Linux)

[Red Hat Enterprise Linux para SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) es otra oferta para ejecutar SAP HANA en instancias grandes de HANA. Ya están disponibles y se admiten las versiones de RHEL 7.2 y 7.3. Para más información sobre SAP en Red Hat, consulte el [sitio de SAP HANA en Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Los documentos siguientes son las notas de compatibilidad de SAP aplicables a la implementación de SAP HANA en Red Hat:

- [SAP support note #2009879 - SAP HANA guidelines for Red Hat Enterprise Linux (RHEL) operating system](https://launchpad.support.sap.com/#/notes/2009879/E) (Nota de compatibilidad de SAP n.º 2009879: Directrices de SAP HANA para el sistema operativo Red Hat Enterprise Linux [RHEL])
- [SAP support note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690) (2292690 - SAP HANA DB: configuraciones de sistema operativo recomendadas para RHEL 7)
- [SAP support note #1391070 – Linux UUID solutions](https://launchpad.support.sap.com/#/notes/1391070) (Nota de compatibilidad de SAP n.º 1391070: Soluciones UUID de Linux)
- [SAP support note #2228351 - Linux: SAP HANA Database SPS 11 revision 110 (or higher) on RHEL 6 or SLES 11](https://launchpad.support.sap.com/#/notes/2228351) (Nota de compatibilidad de SAP n.º 2228351 - Linux: Revisión 110 (o superior) de SAP HANA Database SPS 11 en RHEL 6 o SLES 11)
- [SAP support note #2397039 - FAQ: SAP on RHEL](https://launchpad.support.sap.com/#/notes/2397039) (Nota de compatibilidad de SAP n.º 2397039 sobre preguntas frecuentes de SAP en RHEL)
- [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: Installation and upgrade](https://launchpad.support.sap.com/#/notes/2002167) (Nota de compatibilidad de SAP n.º 2002167: Instalación y actualización de Red Hat Enterprise Linux 7.x)

### <a name="time-synchronization"></a>Sincronización de la hora

Las aplicaciones de SAP basadas en la arquitectura de SAP NetWeaver son sensibles a las diferencias horarias para los componentes del sistema SAP. Los volcados cortos de ABAP de SAP con el título de error de ZDATE\_LARGE\_TIME\_DIFF probablemente le resulten familiares, ya que estos volcados cortos aparecen cuando la hora del sistema de diferentes servidores o máquinas virtuales (VM) se distancia demasiado.

En el caso de SAP HANA en Azure (instancias grandes), la sincronización de la hora en Azure no se aplica a las unidades de proceso en las marcas de instancia grande. Tampoco se aplica para ejecutar aplicaciones de SAP en máquinas virtuales nativas de Azure, porque Azure garantiza que la hora de un sistema está correctamente sincronizada. 

Como resultado, debe configurar un servidor horario independiente. Este servidor lo usarán los servidores de aplicaciones de SAP que se ejecutan en máquinas virtuales de Azure. También lo usarán las instancias de base de datos de SAP HANA que se ejecutan en instancias grandes de HANA. La infraestructura de almacenamiento en marcas de instancia grande se sincroniza con los servidores de Protocolo de tiempo de red (NTP).


## <a name="networking"></a>Redes
Para diseñar las redes virtuales de Azure y conectar esas redes virtuales a instancias grandes de HANA, asegúrese de seguir las recomendaciones descritas en:

- [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](./hana-overview-architecture.md)
- [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md)

Hay algunos detalles que vale la pena mencionar acerca de las funciones de red de las unidades individuales. Cada unidad de instancia grande HANA viene con dos o tres direcciones IP asignadas a dos o tres puertos de controladora de interfaz de red (NIC). En las configuraciones de escalado horizontal de HANA y el escenario de replicación del sistema de HANA se utilizan tres direcciones IP. Una de las direcciones IP asignadas a la NIC de la unidad está fuera del grupo de direcciones IP del servidor que se describió en la [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](./hana-overview-architecture.md).

Para más información sobre los detalles de Ethernet para su arquitectura, consulte los [escenarios admitidos de HLI](hana-supported-scenario.md).

## <a name="storage"></a>Almacenamiento

El diseño del almacenamiento de SAP HANA (instancias largas) es configurado por SAP HANA en Administración de servicios de Azure a través de las instrucciones recomendadas de SAP. Estas instrucciones se documentan en los [Requisitos de almacenamiento de SAP HANA](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). 

Los tamaños aproximados de los diferentes volúmenes con las diferentes SKU de grandes instancias de HANA se documentan en [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](hana-overview-architecture.md).

Las convenciones de nomenclatura de los volúmenes de almacenamiento se muestran en la tabla siguiente:

| Uso del almacenamiento | Nombre de montaje | Nombre del volumen | 
| --- | --- | ---|
| Datos de HANA | /hana/data/SID/mnt0000\<m> | Almacenamiento IP: /hana_data_SID_mnt00001_tenant_vol |
| Registro HANA | /hana/log/SID/mnt0000\<m> | Almacenamiento IP: /hana_log_SID_mnt00001_tenant_vol |
| Copia de seguridad del registro de HANA | /hana/log/backups | Almacenamiento IP: /hana_log_backups_SID_mnt00001_tenant_vol |
| HANA compartido | /hana/shared/SID | Almacenamiento IP: IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Almacenamiento IP: /hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* es el identificador de sistema de la instancia de HANA. 

*Tenant* es una enumeración interna de operaciones al implementar un inquilino.

HANA y usr/sap comparten el mismo volumen. La nomenclatura de los puntos de montaje incluye el identificador de sistema de las instancias de HANA y el número de montaje. En las implementaciones de escalado vertical solo hay un montaje, como mnt00001. En las implementaciones escaladas, verá tantos montajes como nodos de trabajo y principales tiene. 

Para los entornos de escalado horizontal, los datos, el registro y los volúmenes de copia de seguridad del registro se comparten y se adjuntan a cada nodo en la configuración de escalado horizontal. Para las configuraciones que son varias instancias de SAP, se crea un conjunto diferente de volúmenes y se adjunta a la instancia grande de HANA. Consulte los [escenarios admitidos por HLI](hana-supported-scenario.md) para información detallada sobre la distribución del almacenamiento.

Las instancias grandes de HANA incluyen un volumen de disco enorme para HANA/data y un volumen HANA/log/backup. Hicimos que el volumen HANA/data tenga ese tamaño porque las instantáneas de almacenamiento utilizan el mismo volumen de disco. Cuantas más instantáneas de almacenamiento realice, más espacio consumen las instantáneas en los volúmenes de almacenamiento asignados. 

El volumen HANA/log/backup no está pensado para ser el volumen para las copias de seguridad de las bases de datos. Su tamaño se ajusta para usarlo como el volumen de copia de seguridad para las copias de seguridad del registro de transacciones de HANA. Para más información, consulte [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md). 

Para aumentar el almacenamiento, puede comprar más capacidad en incrementos de 1 TB. Este almacenamiento adicional se puede agregar como volúmenes nuevos a una instancia grande de HANA.

Durante la incorporación de SAP HANA en Administración de servicios de Azure, especificará un identificador de usuario (UID) y un identificador de grupo (GID) para el usuario sidadm y el grupo sapsys (por ejemplo, 1000,500). Debe usar estos mismos valores durante la instalación del sistema SAP HANA. Como quiere implementar varias instancias de HANA en una unidad, obtendrá varios conjuntos de volúmenes (un conjunto por cada instancia). Por lo tanto, en el momento de la implementación, deberá definir:

- El SID de las distintas instancias de HANA (del que se deriva sidadm).
- Los tamaños de memoria de las distintas instancias de HANA. El tamaño de memoria por instancia define el tamaño de los volúmenes en cada conjunto de volúmenes individuales.

En función de las recomendaciones del proveedor de almacenamiento se configuran las siguientes opciones de montaje para todos los volúmenes montados (se excluyen los LUN de inicio):

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Estos puntos de montaje se configuran en /etc/fstab como se muestra en las capturas de pantalla siguientes:

![Captura de pantalla que muestra fstab de volúmenes montados en la unidad de instancia grande de HANA.](./media/hana-installation/image1_fstab.PNG)

La salida del comando df -h en una instancia grande de HANA S72m tiene este aspecto:

![Captura de pantalla que muestra la salida del comando para la instancia grande de HANA.](./media/hana-installation/image2_df_output.PNG)


El controlador de almacenamiento y los nodos de las marcas de instancia grandes se sincronizan con los servidores NTP. Es importante sincronizar las máquinas virtuales de Azure y SAP HANA en Azure (instancias grandes) con el servidor NTP. Elimina un desfase de tiempo significativo entre la infraestructura y las unidades de proceso de Azure o las marcas de instancia grande.

A fin de optimizar SAP HANA para el almacenamiento subyacente, establezca los parámetros de configuración de SAP HANA siguientes:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
En las versiones de SAP HANA 1.0 hasta SPS12, estos parámetros se pueden establecer durante la instalación de la base de datos de SAP HANA, tal y como se describe en la [nota de SAP n.º 2267798 sobre configuración de la base de datos de SAP HANA](https://launchpad.support.sap.com/#/notes/2267798)

También puede configurar los parámetros después de la instalación de la base de datos de SAP HANA mediante el marco de hdbparam. 

El almacenamiento usado en instancias grandes de HANA tiene una limitación de tamaño de archivo. La [limitación de tamaño es de 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) por archivo. A diferencia de las limitaciones de tamaño de archivo de los sistemas de archivos EXT3, HANA no es consciente implícitamente de la limitación de almacenamiento exigida por el almacenamiento de instancias grandes de HANA. Por consiguiente, HANA no creará automáticamente un nuevo archivo de datos cuando se alcance el límite de tamaño de archivo de 16 TB. Cuando HANA intente aumentar el archivo más allá de 16 TB, informará de errores y el servidor de índice terminará bloqueándose.

> [!IMPORTANT]
> A fin de impedir que HANA intente aumentar los archivos de datos más allá del límite de tamaño de archivo de 16 TB del almacenamiento de instancias grandes de HANA, establezca los parámetros siguientes en el archivo de configuración global.ini de SAP HANA:
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Vea también la nota de SAP [2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Tenga en cuenta la nota de SAP [2631285](https://launchpad.support.sap.com/#/notes/2631285).


Con SAP HANA 2.0, el marco de hdbparam está en desuso. Por lo tanto, los parámetros deben establecerse mediante comandos SQL. Para más información, consulte [SAP note #2399079: Elimination of hdbparam in HANA 2](https://launchpad.support.sap.com/#/notes/2399079) (Nota de SAP n.º 2399079: eliminación de hdbparam en HANA 2).

Consulte los [escenarios admitidos por HLI](hana-supported-scenario.md) para información sobre la distribución de almacenamiento correspondiente a su arquitectura.


## <a name="next-steps"></a>Pasos siguientes

Siga los pasos para instalar SAP HANA en Azure (instancias grandes).

> [!div class="nextstepaction"]
> [Instalación de HANA en SAP HANA en Azure (instancias grandes)](hana-example-installation.md)
