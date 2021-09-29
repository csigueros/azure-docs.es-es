---
title: Comparación del acceso a Azure Files, Blob Storage y Azure NetApp Files con NFS
description: Compare el acceso a Azure Files, Azure Blob Storage y Azure NetApp Files con NFS.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: rogarana
ms.openlocfilehash: 3dcc0a93473e5f0b14f8921114172dbf6506d8aa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636750"
---
# <a name="compare-access-to-azure-files-blob-storage-and-azure-netapp-files-with-nfs"></a>Comparación del acceso a Azure Files, Blob Storage y Azure NetApp Files con NFS

En este artículo se proporciona una comparación entre cada una de estas ofertas si se accede a ellas mediante el protocolo [Network File System (NFS)](https://en.wikipedia.org/wiki/Network_File_System). Esta comparación no se aplica si se accede a ellas por cualquier otro método.

Si busca comparaciones más generales, consulte [este artículo](storage-introduction.md) para comparar Azure Blob Storage y Azure Files, o [este otro](../files/storage-files-netapp-comparison.md) para comparar Azure Files y Azure NetApp Files.

## <a name="comparison"></a>De comparación

|Category  |Azure Blob Storage  |Azure Files  |Azure NetApp Files  |
|---------|---------|---------|---------|
|Casos de uso     |Blob Storage es más adecuado para cargas de trabajo de acceso secuencial con numerosas lecturas a gran escala en las que los datos se ingieren una vez y se modifican mínimamente después.<br></br>Blob Storage ofrece el costo total de propiedad más bajo, si hay poco o ningún mantenimiento.<br></br>Algunos escenarios de ejemplo son: datos analíticos a gran escala, computación de alto rendimiento sensible a la capacidad de proceso, copia de seguridad y archivo, conducción autónoma, representación multimedia o secuenciación genómica.         |Azure Files es un servicio de alta disponibilidad que está optimizado para cargas de trabajo de acceso aleatorio.<br></br>En el caso de los recursos compartidos de NFS, Azure Files proporciona compatibilidad completa con el sistema de archivos POSIX y se puede usar fácilmente desde plataformas de contenedor, como Azure Container Instance (ACI) y Azure Kubernetes Service (AKS) con el controlador CSI integrado, además de plataformas basadas en máquinas virtuales.<br></br>Algunos escenarios de ejemplo son: archivos compartidos, bases de datos, directorios principales, aplicaciones tradicionales, ERP, CMS, migraciones NAS que no requieren administración avanzada y aplicaciones personalizadas que requieren almacenamiento de archivos de escalabilidad horizontal.         |Servicio de archivos totalmente administrado en la nube, con tecnología de NetApp, con funcionalidades de administración avanzadas.<br></br>NetApp Files es adecuado para cargas de trabajo que requieren acceso aleatorio y proporciona una amplia compatibilidad con protocolos y funcionalidades de protección de datos.<br></br>Algunos escenarios de ejemplo son: migración NAS empresarial local que requiere numerosas funcionalidades de administración, cargas de trabajo sensibles a la latencia, como SAP HANA, proceso de alto rendimiento con uso intensivo de IOPS o sensible a la latencia o cargas de trabajo que requieren acceso simultáneo a varios protocolos.         |
|Protocolos disponibles     |NFS 3.0<br></br>REST<br></br>Data Lake Storage Gen2         |SMB<br><br>NFS 4.1 (versión preliminar)<br></br> (No hay interoperabilidad entre ninguno de los protocolos).         |NFS 3.0 y 4.1<br></br>SMB         |
|Características principales     | Se integra con la caché HPC en el caso de cargas de trabajo de baja latencia. <br> </br> Administración integrada, incluido el ciclo de vida, los blobs inmutables, la conmutación por error de datos y el índice de metadatos.         | Redundancia zonal para alta disponibilidad. <br></br> Baja latencia constante (menos de 10 ms). <br></br>Rendimiento y costo predecibles que se escalan con la capacidad.         |Latencia extremadamente baja (menos de un milisegundo).<br></br>Completa funcionalidad de administración NetApp ONTAP, como SnapMirror en la nube.<br></br>Experiencia coherente de nube híbrida.         |
|Rendimiento (por volumen).     |Hasta 20 000 IOPS, con un rendimiento de hasta 100 GiB/s.         |Hasta 100 000 IOPS, con un rendimiento de hasta 80 GiB/s.         |Hasta 460 000 IOPS, con un rendimiento de 36 Gib/s.         |
|Escala     | Hasta 2 PiB para un solo volumen. <br></br> Hasta 4,75 TiB aproximadamente para un solo archivo.<br></br>No hay requisitos mínimos de capacidad.         |Hasta 100 TiB para un único recurso compartido de archivos.<br></br>Hasta 4 TiB para un solo archivo.<br></br>100 GiB de capacidad mínima.         |Hasta 100 TiB para un solo volumen.<br></br>Hasta 16 TiB para un solo archivo.<br></br>Experiencia coherente de nube híbrida.         |
|Precios     |[Precios de Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/)         |[Precios de Azure Files](https://azure.microsoft.com/pricing/details/storage/files/)         |[Precios de Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/)         |

## <a name="next-steps"></a>Pasos siguientes

- Para más información, consulte [Compatibilidad del protocolo Network File System (NFS) 3.0 en Azure Blob Storage (versión preliminar)](../blobs/network-file-system-protocol-support.md).
- Para acceder Azure Files con NFS, consulte [Recursos compartidos de archivos NFS en Azure Files](../files/files-nfs-protocol.md).
- Para acceder a Azure NetApp Files con NFS, consulte [Inicio rápido: Configuración de Azure NetApp Files y creación de un volumen NFS](../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).
