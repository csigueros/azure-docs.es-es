---
title: Consideraciones para ejecutar servicios de archivos ISV en Azure
titleSuffix: Azure Storage
description: Guía básica para distintas opciones de ISV sobre la ejecución de servicios de archivos en Azure
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 05/24/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: a880e64a710638561eb171b2d7bb14973ef58224
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969760"
---
# <a name="running-isv-file-services-in-azure"></a>Ejecución de servicios de archivos ISV en Azure

Azure ofrece varias opciones para almacenar datos de archivos. Los servicios nativos de Azure son:
- [Azure Files](https://azure.microsoft.com/services/storage/files/): ofrece recursos compartidos de archivos en la nube totalmente administrados, a los que se puede acceder mediante los protocolos SMB y NFS estándar de la industria. Los archivos de Azure ofrecen dos tipos diferentes (estándar y prémium) con características de rendimiento diferentes.
- [Azure NetApp Files:](https://azure.microsoft.com/services/netapp/) son recursos compartidos de archivos totalmente administrados en la nube y que están diseñados para cumplir los requisitos de rendimiento de las aplicaciones de línea de negocio empresariales. Azure NetApp Files ofrece varios niveles de servicio con diferentes limitaciones de rendimiento (estándar, prémium y ultra).
- [Azure Blob Storage:](https://azure.microsoft.com/services/storage/blobs/) es una plataforma de almacenamiento de objetos a gran escala que se usa para almacenar datos no estructurados. Azure Blob Storage ofrece dos tipos diferentes (estándar y prémium) con características de rendimiento diferentes. 
  
Existen varios artículos que describen las diferencias y recomendaciones sobre la selección del servicio de archivos nativo. Puede obtener más información:
- En nuestra guía de migración se describe el [gráfico de flujo básico](../../../common/storage-migration-overview.md#choose-a-target-storage-service).
- [Comparación detallada entre Azure Files y Azure NetApp Files](../../../files/storage-files-netapp-comparison.md)

Muchas soluciones de proveedores de software independientes (ISV) pueden proporcionar servicios de archivos en Azure. En este artículo se tratan dos temas:
- el tema que proporciona información general sobre la selección de servicios de archivos.
- el tema que describe las diferencias entre las soluciones de ISV.
  
La lista completa de soluciones de ISV comprobadas está disponible en [Asociados de Azure Storage para el almacenamiento principal y secundario](./partner-overview.md).

## <a name="considerations"></a>Consideraciones

Al seleccionar la mejor solución para ejecutar un servicio de archivos en Azure, es importantes tener en cuenta varias consideraciones.

### <a name="basic-considerations"></a>Consideraciones básicas

La consideración de funcionalidad básica examina las funcionalidades más comunes de las plataformas de almacenamiento subyacentes. Este paso se realiza inicialmente para explorar qué soluciones proporcionan la funcionalidad básica que necesitamos para satisfacer nuestros requisitos. Las características básicas importantes varían de un caso a otro. Las más comunes son la compatibilidad con protocolos, el tamaño del espacio de nombres, la creación automática de niveles, el cifrado, la compatibilidad con WORM y la autenticación. Puede ver la lista de funcionalidades básicas que nuestras soluciones de ISV admiten en la [comparación de características](#isv-solutions-comparison).

### <a name="performance-considerations"></a>Consideraciones de rendimiento

Después de seleccionar qué solución satisface una funcionalidad básica, debe tener en cuenta el rendimiento necesario. La estimación del ajuste del rendimiento depende de tres características de rendimiento básicas:
  - Latencia
  - Ancho de banda
  - IOPS o TPS

La importancia de las características básicas de rendimiento depende de la simultaneidad de las cargas de trabajo (número de solicitudes en paralelo) y del tamaño del bloque (tamaño de la solicitud).

| Tipo de carga de trabajo | Recomendaciones |
| -------------------- | --------------- |
| **Simultaneidad baja** | La latencia es la consideración más importante. Con una latencia menor, se puede lograr más rendimiento. En cargas de trabajo con poca simultaneidad, las limitaciones de IOPS y ancho de banda rara vez se cruzan. |
| **Simultaneidad alta** | El impacto en la latencia es mucho menor debido a la alta simultaneidad. Se deben tener en cuenta IOPS y el ancho de banda. |
| **Tamaño de bloque** | En el caso de las cargas de trabajo con tamaños de bloque pequeños, es más importante tener en cuenta los límites de IOPS, mientras que los límites de ancho de banda son más importantes para las cargas de trabajo con tamaños de bloque grandes. |

Cualquier carga de trabajo de almacenamiento se puede describir siempre que se tengan esas características en mente. Por ejemplo, las cargas de trabajo OLTP suelen tener una simultaneidad alta y tamaños de bloque pequeños. Las cargas de trabajo de HPC suelen tener una simultaneidad alta, pero el tamaño del bloque puede variar de pequeño a grande. 
    
Siempre se recomiendan algunas reglas generales:
  - Selección de protocolo: si es posible, use SMB3 con compatibilidad multicanal o NFSv3 con compatibilidad con nconnect, ya que esto proporcionará un mejor rendimiento. Evite el uso de protocolos heredados desde el rendimiento, pero también las consideraciones de seguridad. Tenga en cuenta que los clientes también se pueden optimizar; esto se recomienda para maximizar el rendimiento.
  - Redes: si el tipo de VM lo admite, use redes aceleradas. Reducirá la latencia y siempre tendrá un efecto positivo en el rendimiento. 
  - Tipo de VM: seleccione el tipo de VM más adecuado para la carga de trabajo. Si la carga de trabajo tiene un número reducido de clientes, la ejecución de servicios de archivos en un número menor de VM más grandes es la opción más adecuada. Por el contrario, un gran número de clientes puede beneficiarse de la ejecución de servicios de archivos en un mayor número de VM más pequeñas.
  - Para cargas de trabajo de baja simultaneidad y tamaño de bloque pequeño, explore soluciones que:
    - usen discos administrados (discos Premium o SSD Ultra) o
    - tengan un algoritmo de almacenamiento en caché adecuado.
  - Para cargas de trabajo de gran simultaneidad y tamaño de bloque grande, explore las soluciones que usan Azure Blob Storage como back-end.

## <a name="isv-solutions-overview-and-example-use-cases"></a>Soluciones de ISV: información general y casos de uso de ejemplo

En este artículo se comparan varias soluciones de ISV que proporcionan servicios de archivos en Azure.

| Solución | Información general | Casos de uso de ejemplo |
| -------- | ----------- | ----------------- |
| **Nasuni** | **UniFS** es un servicio de archivos empresarial con una alternativa en la nube más sencilla y de bajo costo basada en Microsoft Azure. | - Almacenamiento de archivos principal <br> - Recursos compartidos de archivos de departamento <br> - Administración centralizada de archivos <br> - Colaboración entre varios sitios con bloqueo de archivos global <br> - Windows Virtual Desktop <br> - Recursos compartidos de archivos de trabajo remoto o VDI |
| **NetApp** | **Cloud Volumes ONTAP** optimiza los costos de almacenamiento en la nube y el rendimiento, a la vez que mejora la protección de datos, la seguridad y el cumplimiento. Incluye una opción de administración de datos de nivel empresarial, disponibilidad y durabilidad. | - Aplicaciones empresariales <br> - Bases de datos relacionales y de NoSQL <br> - Macrodatos y análisis <br> - Datos persistentes para contenedores <br> - Canalizaciones de CI/CD <br> - Recuperación ante desastres para soluciones de NetApp locales |
| **Panzura**| **CloudFS** es un sistema de archivos global de empresa híbrida que permite acceder al mismo conjunto de datos local o en la nube. | - Reemplazo de NAS empresarial <br> - Colaboración global <br> - Acceso nativo en la nube a datos no estructurados para Analytics, IA/ML. |
| **Tiger Technology** | **Tiger Bridge** es una solución de software de administración de datos. Proporciona niveles entre un sistema de archivos NTFS y Azure Blob Storage o los discos administrados de Azure. Asimismo, crea un espacio de nombres único con bloqueo de archivos local. | - Analytics <br> - Archivo en la nube <br> - Protección continua de datos (CDP) <br> - Recuperación ante desastres para Windows servers <br> - Sincronización y colaboración de sincronización múltiple <br> - Flujos de trabajo remotos (VDI) |
| **XenData** | **Cloud File Gateway** crea un sistema de archivos global altamente escalable mediante servidores de archivos de Windows. | - Uso compartido global de archivos científicos y de ingeniería <br> - Edición de vídeo colaborativa |

## <a name="isv-solutions-comparison"></a>Comparación de soluciones de ISV

### <a name="supported-protocols"></a>Protocolos admitidos

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **SMB 2.1**                                         | Sí                  | Sí                            | Sí                       | Sí                   | Sí                   |
| **SMB 3.0**                                         | Sí                  | Sí                            | Sí                       | Sí                   | Sí                   |
| **SMB 3.1**                                         | Sí                  | Sí                            | Sí                       | Sí                   | Sí                   |
| **NFS v3**                                          | Sí                  | Sí                            | Sí                       | Sí                   | Sí                   |
| **NFS v4.1**                                        | Sí                  | Sí                            | Sí                       | Sí                   | Sí                   |
| **iSCSI**                                           | No                   | Sí                            | No                        | Sí                   | No                    |

### <a name="supported-services-for-persistent-storage"></a>Servicios admitidos para el almacenamiento persistente

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Discos administrados**                                   | No                   | Sí                            | Sí                       | Sí                   | No                    |
| **Discos no administrados**                                 | No                   | No                             | No                        | Sí                   | No                    |
| **Blobs en bloques de Azure Storage**                       | Sí                  | Sí (en niveles)                  | Sí                       | Sí                   | Sí                   |
| **Blobs en páginas de Azure Storage**                        | No                   | Sí (para alta disponibilidad)                   | Sí                       | No                    | No                    |
| **Compatibilidad con el nivel de archivo de Azure**                      | No                   | No                             | Sí                       | Sí                   | Sí                   |
| **Archivos accesibles en formato no opaco**           | No                   | No                             | No                        | Sí                   | Sí                   |

### <a name="extended-features"></a>Características ampliadas

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Entorno operativo**                           | UniFS                | ONTAP                          | PFOS                      | Windows Server        | Windows Server              |
| **Alta disponibilidad**                               | Sí                  | Sí                            | Sí                       | Sí (requiere configuración)  | Sí                   |
| **Conmutación automática por error entre nodos del clúster** | Sí                  | Sí                            | Sí                       | Sí (clúster de Windows) | Sí (clúster de Windows) |
| **Conmutación automática por error entre zonas de disponibilidad**    | Sí                  | No                             | Sí                       | Sí (clúster de Windows) | Sí (clúster de Windows) |
| **Conmutación automática por error entre regiones**               | Sí (compatibilidad con Nasuni)| No                         | No                        | Sí (clúster de Windows) | Sí (clúster de Windows) |
| **Compatibilidad con instantáneas**                                | Sí                  | Sí                            | Sí                       | Sí                   | No                    |
| **Compatibilidad coherente con instantáneas**                     | Sí                  | Sí                            | Sí                       | Sí                   | No                    |
| **Copia de seguridad integrada**                               | Sí                  | Sí (complemento)                   | No                        | Sí                   | Sí                   |
| **Control de versiones**                                      | Sí                  | Sí                            | No                        | Sí                   | Sí                   |
| **Restauración en el nivel de archivo**                              | Sí                  | Sí                            | Sí                       | Sí                   | Sí                   |
| **Restauración de nivel de volumen**                            | Sí                  | Sí                            | Sí                       | Sí                   | Sí                   |
| **WORM**                                            | Sí                  | Sí                            | No                        | Sí                   | No                    |
| **Niveles automáticos**                               | Sí                  | Sí                            | No                        | Sí                   | Sí                   |
| **Bloqueo global de archivos**                             | Sí                  | Sí (caché global de archivos de NetApp) | Sí                       | Sí                   | Sí                   |
| **Agregación de espacios de nombres a través de orígenes de back-end**      | Sí                  | Sí                            | No                        | Sí                   | Sí                   |
| **Almacenamiento en caché de datos activos**                          | Sí                  | Sí                            | Sí                       | sí                   | Sí                   |
| **Modos de almacenamiento en caché admitidos**                         | LRU, anclado manualmente | LRU                            | LRU, anclado manualmente      | LRU                   | LRU                   |
| **Cifrado en reposo**                              | Sí                  | Sí                            | Sí                       | Sí                   | No                    |
| **Desduplicación**                                  | Sí                  | Sí                            | Sí                       | No                    | No                    |
| **Compresión**                                     | Sí                  | Sí                            | Sí                       | No                    | No                    |

### <a name="authentication-sources"></a>Orígenes de autenticación

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Soporte técnico de Azure AD**                                | Sí (a través de ADDS)       | Sí (a través de ADDS)                 | Sí (a través de ADDS)            | Sí (a través de ADDS)        | Sí (a través de ADDS)        |
| **Compatibilidad con Active Directory**                        | Sí                  | Sí                            | Sí                       | Sí                   | Sí                   |
| **Compatibilidad con LDAP**                                    | Sí                  | Sí                            | No                        | No                    | Sí                   |

### <a name="management"></a>Administración

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **REST API**                                        | Sí                  | Sí                            | Sí                       | Sí                   | No                    |
| **Web GUI**                                         | Sí                  | Sí                            | Sí                       | No                    | No                    |

### <a name="scalability"></a>Escalabilidad

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Número máximo de nodos en un único clúster**     | 100                  | 2 (alta disponibilidad)                         | Hasta 60 nodos probados    | N/A                 | N/A                 |
| **Número máximo de volúmenes**                       | 800                  | 1024                           | Sin límite                 | N/A                 | 1                     |
| **Número máximo de instantáneas**                     | Sin límite            | Sin límite                      | Sin límite                 | N/A                 | N/A                 |
| **Tamaño máximo de un espacio de nombres individual**              | Sin límite            | Depende de la infraestructura      | Sin límite                 | N/A                 | N/A                 |

### <a name="licensing"></a>Licencias

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **BYOL**                                            | Sí                  | Sí                            | Sí                       | Sí                   | sí                   |
| **Apto para ventajas de Azure**                          | No                   | Sí                            | Sí                       | No                    | No                    |
| **Modelo de implementación (IaaS, SaaS)**                   | IaaS                 | IaaS                           | IaaS                      | IaaS                  | IaaS                  |

### <a name="other-features"></a>Otras características

**Nasuni**
- [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/nasunicorporation.nasuni-nea-90-prod?tab=Overview)
- Puede usar discos administrados para almacenar datos en caché
- Administración centralizada de todos los dispositivos perimetrales
- Historial de control de versiones de archivos ilimitado
- Tamaño ilimitado del sistema de archivos
- Tamaño ilimitado de archivos
- Acceso a archivos a través de HTTPS, API REST y FTP
- Equilibrio de carga de varios archivos con Azure Load Balancer
- Cifrado con claves administradas por el cliente
- Admite herramientas de auditoría y seguridad de terceros (por ejemplo, Varonis, Stealthbits)
- Implementaciones meticulosas y servicios profesionales

**NetApp**
- [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/netapp.netapp-ontap-cloud?tab=Overview)
- Ahorro de desduplicación que se pasa al cliente a través de un consumo reducido de infraestructura

**Panzura**
- [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/panzura-file-system.panzura-freedom-filer)
- Bloqueo del nivel de bytes (se abren varias R/W simultáneas)

**Tiger Technology**
- Invisible para las aplicaciones
- Restauración parcial
- Integración de Windows Shell (superposición, menú contextual, hoja de propiedades)
- Compatibilidad con la eliminación temporal y la recuperación de elementos eliminados de Azure
- Opción para aplicar cambios de nombre al destino en la nube
- Escritura parcial en objetos
- Protección contra ransomware
- Sincronización y colaboración de varios sitios

**XenData**
- El servicio Cosmos DB proporciona una sincronización rápida de varias puertas de enlace, incluidos los archivos de propietario específicos de la aplicación para la colaboración global. 
- Cada puerta de enlace tiene un control extremadamente pormenorizado del contenido almacenado localmente en la caché.
- Admite streaming de vídeo y restauraciones de archivos parciales.
- Admite cargas de Azure Data Box.
- Cifrado proporcionado por Azure Blob Storage.

## <a name="next-steps"></a>Pasos siguientes

Más información:

- [Azure Disks](../../../../virtual-machines/managed-disks-overview.md)
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
- [Asociados comprobados para el almacenamiento principal y secundario](./partner-overview.md)
- [Información general sobre la migración del almacenamiento](../../../common/storage-migration-overview.md)