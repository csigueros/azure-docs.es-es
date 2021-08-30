---
title: Matriz de protección de MABS (Azure Backup Server) V3 UR1
description: En este artículo se incluye una matriz de compatibilidad en la que se indican todas las cargas de trabajo, los tipos de datos y las instalaciones que protege Azure Backup Server.
ms.date: 07/27/2021
ms.topic: conceptual
ms.openlocfilehash: c95f9564a12b430795a1a000a074af8869adce43
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720694"
---
# <a name="mabs-azure-backup-server-v3-ur1-and-later-protection-matrix"></a>Matriz de protección de MABS (Azure Backup Server) V3 UR1 (y versiones posteriores)

En este artículo se indican los diferentes servidores y cargas de trabajo que pueden protegerse con Azure Backup Server. En la siguiente matriz se indican los elementos que pueden protegerse con Azure Backup Server.

Use la siguiente matriz para MABS v3 UR1 (y versiones posteriores):

* Cargas de trabajo: el tipo de carga de trabajo de la tecnología.

* Versión: versión de MABS compatible con las cargas de trabajo.

* Instalación de MABS: el equipo o la ubicación donde desea instalar MABS.

* Protección y recuperación: muestra la información detallada sobre las cargas de trabajo, como el contenedor de almacenamiento admitido o la implementación compatible.

>[!NOTE]
>Con MABS v3 UR1 (y versiones posteriores), la compatibilidad con el agente de protección de 32 bits está en desuso. Consulte [Desuso del agente de protección de 32 bits](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>Matriz de compatibilidad de protección

En las secciones siguientes se detalla la matriz de compatibilidad de protección para MABS:

* [Copia de seguridad de aplicaciones](#applications-backup)
* [Copia de seguridad de máquinas virtuales](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Copia de seguridad de aplicaciones

| **Carga de trabajo**               | **Versión**                                                  | **Instalación de Azure Backup Server**                       | **Compatibilidad con Azure Backup Server** | **Protección y recuperación**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Equipos cliente (64 bits) | Windows 10                                                  | Servidor físico  <br><br>    Máquina virtual de Hyper-V   <br><br>   Máquina virtual VMware | V3 UR1 y V3 UR2                            | Volumen, recurso compartido, carpeta, archivos, volúmenes desduplicados   <br><br>   Los volúmenes protegidos deben ser NTFS. No se admiten FAT ni FAT32.  <br><br>    Los volúmenes deben ser de al menos 1 GB. Azure Backup Server usa el Servicio de instantáneas de volumen (VSS) para tomar la instantánea de datos y esta solo funciona si el volumen es de al menos 1 GB. |
| Servidores (64 bits)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Máquina virtual de Azure (cuando la carga de trabajo se ejecute como máquina virtual de Azure)  <br><br>    Servidor físico  <br><br>    Máquina virtual de Hyper-V <br><br>     Máquina virtual de VMware  <br><br>    Azure Stack | V3 UR1 y V3 UR2                            | Volumen, recurso compartido, carpeta y archivo <br><br>    Volúmenes desduplicados (solo NTFS) <br><br>Al proteger un volumen NTFS desduplicado de WS 2016 con MABS v3 en Windows Server 2019, puede que las recuperaciones se vean afectadas. Disponemos de una corrección para realizar recuperaciones sin usar la desduplicación que se incluirá en versiones posteriores de MABS. Si necesita esta corrección en MABS v3 UR1, póngase en contacto con el soporte técnico de MABS.<br><br> Al proteger un volumen NTFS desduplicado de WS 2019 con MABS v3 en Windows Server 2016, las copias de seguridad y restauraciones no se desduplicarán. Por lo tanto, las copias de seguridad consumirán más espacio en el servidor de MABS en comparación con el volumen NTFS desduplicado original.   <br><br>   Estado del sistema y reconstrucción completa (no se admite cuando la carga de trabajo se ejecuta como máquina virtual de Azure) |
| Servidores (64 bits)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (debe instalar [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)) | Servidor físico  <br><br>    Máquina virtual de Hyper-V  <br><br>      Máquina virtual de VMware  <br><br>   Azure Stack | V3 UR1 y V3 UR2                           | Volumen, recurso compartido, carpeta, archivo, estado del sistema y reconstrucción completa        |
| SQL Server                | SQL Server 2019, 2017, 2016 y [Service Packs compatibles](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 y [Service Packs](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) compatibles | Servidor físico  <br><br>     Máquina virtual de Hyper-V   <br><br>     Máquina virtual de VMware  <br><br>   Máquina virtual de Azure (cuando la carga de trabajo se ejecute como máquina virtual de Azure)  <br><br>     Azure Stack | V3 UR1 y V3 UR2                            | Todos los escenarios de implementación: base de datos       <br><br>    MABS v3 UR2 y las versiones posteriores admiten la copia de seguridad de la base de datos de SQL Database, almacenada en un volumen compartido de clúster.     <br><br>  MABS V3 UR1 admite la copia de seguridad de bases de datos SQL en volúmenes ReFS     <br><br>     MABS no admite bases de datos de SQL Server hospedadas en Servidores de archivos de escalabilidad horizontal (SOFS) de Windows Server 2012. <br><br>   MABS no puede proteger grupos de disponibilidad distribuidos (DAG) ni grupos de disponibilidad (AG) de SQL Server si el nombre de rol que hay en el clúster de conmutación por error es distinto al que hay en el AG en SQL.       |
| Exchange                   | Exchange 2019, 2016                                         | Servidor físico   <br><br>   Máquina virtual de Hyper-V  <br><br>      Máquina virtual de VMware  <br><br>   Azure Stack  <br><br>    Máquina virtual de Azure (cuando la carga de trabajo se ejecute como máquina virtual de Azure) | V3 UR1 y V3 UR2                            | Protección (todos los escenarios de implementación): instancia independiente de Exchange Server y base de datos en grupo de disponibilidad de base de datos (DAG)  <br><br>    Recuperación (todos los escenarios de implementación): buzón de correo y bases de datos de buzón de correo en un grupo de disponibilidad de base de datos (DAG)    <br><br>  La copia de seguridad de Exchange a través de ReFS es compatible con MABS V3 UR1 |
| SharePoint                 | SharePoint 2019, 2016 con el Service Pack más reciente                       | Servidor físico  <br><br>    Máquina virtual de Hyper-V <br><br>    Máquina virtual de VMware  <br><br>   Máquina virtual de Azure (cuando la carga de trabajo se ejecute como máquina virtual de Azure)   <br><br>   Azure Stack | V3 UR1 y V3 UR2                            | Protección (todos los escenarios de implementación): granja de servidores, contenido del servidor web front-end  <br><br>    Recuperación (todos los escenarios de implementación): granja, base de datos, aplicación web, elemento de lista o archivo, búsqueda de SharePoint y servidor web front-end  <br><br>    No se admite la protección para una granja de SharePoint que use la característica Always On de SQL Server 2012 para las bases de datos de contenido. |

## <a name="vm-backup"></a>Copia de seguridad de máquinas virtuales

| **Carga de trabajo**                                                 | **Versión**                                             | **Instalación de Azure Backup Server**                      | **Compatibilidad con Azure Backup Server** | **Protección y recuperación**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Host de Hyper-V: agente de protección MABS en el servidor host de Hyper-V, clúster o máquina virtual | Windows Server 2019, 2016, 2012 R2, 2012               | Servidor físico  <br><br>    Máquina virtual de Hyper-V <br><br>    Máquina virtual de VMware | V3 UR1 y V3 UR2                                 | Protección: máquinas virtuales, volúmenes compartidos de clúster (CSV)  <br><br>    Recuperación: máquina virtual, recuperación de elementos de archivos y carpetas solo disponible para Windows, volúmenes y discos duros virtuales |
| Azure Stack HCI  |       V1 y 20H2      |    Servidor físico        <br><br>     Máquina virtual de Azure Stack HCI/Hyper-V     <br><br>    Máquina virtual VMware     |    V3 UR2 y versiones posteriores   | Protección: máquinas virtuales, volúmenes compartidos de clúster (CSV)      <br><br>     Recuperación: máquina virtual, recuperación de elementos de archivos y carpetas solo disponible para Windows, volúmenes y discos duros virtuales |
| Máquinas virtuales de VMware                                                  | VMware Server 5.5, 6.0 o 6.5, 6.7 (versión con licencia) | Máquina virtual de Hyper-V  <br><br>   Máquina virtual de VMware         | V3 UR1                             | Protección:  Máquinas virtuales de VMware en volúmenes compartidos de clúster (CSV), NFS y almacenamiento de SAN   <br><br>     Recuperación: máquina virtual, recuperación de elementos de archivos y carpetas solo disponible para Windows, volúmenes y discos duros virtuales <br><br>    VMware vApps no está admitido. |
| Máquinas virtuales de VMware      |   VMware Server 7.0, 6.7, 6.5 o 6.0 (versión con licencia)     |    Máquina virtual de Hyper-V     <br><br>    Máquina virtual VMware    |   V3 UR2 y versiones posteriores    |   Protección: máquinas virtuales de VMware en volúmenes compartidos de clúster (CSV), NFS y almacenamiento SAN     <br><br>    Recuperación: máquina virtual, recuperación de elementos de archivos y carpetas solo disponible para Windows, volúmenes y discos duros virtuales     <br><br>    VMware vApps no compatible.  |

>[!NOTE]
> MABS no admite la copia de seguridad de máquinas virtuales con discos de acceso directo o las que usan un VHD remoto. En estos casos, se recomienda usar la copia de seguridad de nivel de invitado con MABS e instalar un agente en la máquina virtual para realizar una copia de seguridad de los datos.

## <a name="linux"></a>Linux

| **Carga de trabajo** | **Versión**                               | **Instalación de Azure Backup Server**                      | **Compatibilidad con Azure Backup Server** | **Protección y recuperación**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux ejecutado como invitado de [Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) o [VMware](backup-azure-backup-server-vmware.md) | Servidor físico, máquina virtual de Hyper-V local, máquina virtual Windows en VMWare | V3 UR1 y V3 UR2                             | Hyper-V debe ejecutarse en Windows Server 2012 R2, Windows Server 2016 o Windows Server 2019. Protección:  máquina virtual completa   <br><br>   Recuperación: máquina virtual completa   <br><br>    Solo se admiten instantáneas coherentes con archivos.    <br><br>   Para obtener una lista completa de las distribuciones y las versiones de Linux compatibles, consulte el artículo [Distribuciones de Linux aprobadas en Azure](../virtual-machines/linux/endorsed-distros.md). |

## <a name="azure-expressroute-support"></a>Compatibilidad con Azure ExpressRoute

Puede realizar una copia de seguridad de los datos mediante Azure ExpressRoute con emparejamiento público (disponible para circuitos antiguos) y emparejamiento de Microsoft. La copia de seguridad por emparejamiento privado no se admite.

Con el emparejamiento público: asegúrese de tener acceso a los siguientes dominios y direcciones:

* URLs
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Direcciones IP
  * 20.190.128.0/18
  * 40.126.0.0/18

Con el emparejamiento de Microsoft, seleccione los siguientes servicios o regiones y los valores de comunidad correspondientes:

* Azure Active Directory (12076:5060)
* Región de Microsoft Azure (según la ubicación del almacén de Recovery Services)
* Azure Storage (según la ubicación del almacén de Recovery Services)

Para más información, consulte los [requisitos de enrutamiento de ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>El emparejamiento público está en desuso para circuitos nuevos.

## <a name="cluster-support"></a>Compatibilidad con clústeres

Azure Backup Server puede proteger datos en las siguientes aplicaciones agrupadas en clúster:

* Servidores de archivos

* SQL Server

* Hyper-V: si se protege un clúster de Hyper-V mediante el agente de protección MABS escalado horizontalmente, no es posible agregar protección secundaria a las cargas de trabajo de Hyper-V protegidas.

* Exchange Server: Azure Backup Server puede proteger clústeres de discos no compartidos para versiones de Exchange Server admitidas (replicación continua en clúster) y también puede proteger instancias de Exchange Server configuradas para replicación continua local.

* SQL Server : Azure Backup Server no admite la realización de copias de seguridad de bases de datos de SQL Server hospedadas en volúmenes compartidos de clúster (CSV).

>[!NOTE]
>MABS solo admite la protección de máquinas virtuales de Hyper-V en volúmenes compartidos de clúster (CSV). No se admite la protección de otras cargas de trabajo hospedadas en CSV.

Azure Backup Server puede proteger cargas de trabajo de clúster que se encuentren en el mismo dominio que el servidor MABS y en un dominio secundario o de confianza. Si quiere proteger los orígenes de datos de dominios o grupos de trabajo que no son de confianza, use NTLM o una autenticación de certificado para un único servidor, o bien una autenticación de certificado para un único clúster.

## <a name="data-protection-issues"></a>Problemas de protección de datos

* MABS no puede hacer la copia de seguridad de máquinas virtuales usando unidades compartidas (que posiblemente estén asociadas a otras máquinas virtuales), dado que VSS Writer de Hyper-V no puede hacerla de volúmenes copiados mediante discos duros virtuales compartidos.

* La ruta de acceso de una carpeta compartida protegida incluye la ruta de acceso lógica del volumen. Si mueve la carpeta compartida, se producirá un error de protección. Si tiene que mover una carpeta compartida protegida, quítela de su grupo de protección y, a continuación, tras el cambio de ubicación, agréguela a la protección.  Además, si cambia la ruta de acceso de un origen de datos protegido de un volumen que utiliza el Sistema de cifrado de archivos (EFS, por sus siglas en inglés), y la nueva ruta de acceso de archivo tiene más de 5120 caracteres, se producirá un error en la protección.

* No puede cambiar el dominio de un equipo protegido y continuar con la protección sin interrupciones. Tampoco puede cambiar el dominio de un equipo protegido y asociar las réplicas y los puntos de recuperación existentes con el equipo en el momento de volver a protegerlo. Si tiene que cambiar el dominio de un equipo protegido, en primer lugar, quite los orígenes de datos del equipo de la protección. Luego, proteja el origen de datos en el equipo una vez que tenga un nuevo dominio.

* No puede cambiar el nombre de un equipo protegido y continuar con la protección sin interrupciones. Tampoco puede cambiar el nombre de un equipo protegido y asociar las réplicas y los puntos de recuperación existentes con el equipo cuando se vuelva a proteger. Si tiene que cambiar el nombre de un equipo protegido, en primer lugar, quite los orígenes de datos del equipo de la protección. Luego, proteja el origen de datos en el equipo una vez que tenga un nuevo nombre.

* MABS identifica automáticamente la zona horaria de un equipo protegido durante la instalación del agente de protección. Si un equipo protegido se mueve a una zona horaria diferente después de configurar la protección, no olvide cambiar la hora del equipo en el Panel de control. Luego, actualice la zona horaria en la base de datos de MABS.

* MABS puede proteger las cargas de trabajo que están en el mismo dominio que el servidor de MABS o en dominios secundarios y de confianza. También puede proteger las cargas de trabajo siguientes en los grupos de trabajo y dominios que no son de confianza con la autenticación de certificados o NTLM:

  * SQL Server
  * Servidor de archivos
  * Hyper-V

  Estas cargas de trabajo se pueden ejecutar en un solo servidor o en una configuración de clúster. Para proteger una carga de trabajo que no está en un dominio de confianza, consulte [Preparación de equipos en grupos de trabajo y dominios de confianza](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019&preserve-view=true#supported-scenarios) para obtener los detalles exactos de lo que se admite y la autenticación necesaria.

## <a name="unsupported-data-types"></a>Tipos de datos no admitidos

MABS no admite la protección de los siguientes tipos de datos:

* Vínculos físicos

* Puntos de análisis, incluyendo vínculos DFS y puntos de unión

* Metadatos de punto de montaje: un grupo de protección puede contener datos con puntos de montaje. En este caso, DPM protege el volumen montado que es el destino del punto de montaje, pero no protege los metadatos del punto de montaje. Al recuperar los datos que contienen puntos de montaje, deberá volver a crear manualmente la jerarquía de puntos de montaje.

* Datos en volúmenes montados dentro de volúmenes montados

* Papelera de reciclaje

* Archivos de paginación

* Carpeta de información del volumen del sistema. Para proteger la información del sistema de un equipo, tendrá que seleccionar el estado del sistema del equipo como miembro del grupo de protección.

* Volúmenes no NTFS

* Archivos que contienen vínculos físicos o vínculos simbólicos de Windows Vista.

* Datos de los recursos compartidos de archivos que hospedan los UPD (discos de perfil de usuario)

* Archivos con cualquiera de las siguientes combinaciones de atributos:

  * Cifrado y punto de análisis

  * Cifrado y Almacenamiento de instancia única (SIS)

  * Cifrado y distinción entre mayúsculas y minúsculas

  * Cifrado y dispersión

  * Distinción entre mayúsculas y minúsculas y SIS

  * Compresión y SIS

## <a name="next-steps"></a>Pasos siguientes

* [Matriz de compatibilidad para copia de seguridad con Microsoft Azure Backup Server o System Center DPM](backup-support-matrix-mabs-dpm.md)