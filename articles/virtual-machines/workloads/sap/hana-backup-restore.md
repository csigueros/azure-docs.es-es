---
title: Copia de seguridad y restauración de HANA en SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Obtenga información sobre el proceso de copia de seguridad y restauración de SAP HANA en instancias grandes de HANA.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/02/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b60ba5c169f660cd01f95f53fbc292a768f1b5e
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113358946"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>Copia de seguridad y restauración de SAP HANA en instancias grandes de HANA

>[!IMPORTANT]
>Este artículo no reemplaza la documentación de administración de SAP HANA ni las notas de SAP. Se espera que tenga conocimientos sobre la administración y operaciones de SAP HANA, sobre todo en lo relativo a los temas de copia de seguridad, restauración, alta disponibilidad y recuperación ante desastres. En este artículo se muestran capturas de pantalla de SAP HANA Studio. El contenido, la estructura y la naturaleza de las pantallas de las herramientas de administración de SAP y de las herramientas en sí pueden cambiar de una versión a otra de SAP HANA.

En este artículo, se describe el proceso de copia de seguridad y restauración de SAP HANA en instancias grandes de HANA (también conocido como infraestructura sin sistema operativo). 

Algunos de los procesos descritos en este artículo están simplificados. No están pensados como pasos detallados para incluirse en los manuales de operaciones. Para crear manuales de operaciones para las configuraciones, ejecute y pruebe los procesos con sus versiones y versiones específicas de HANA. A continuación, puede documentar los procesos para las configuraciones.

Uno de los aspectos más importantes del funcionamiento de las bases de datos es protegerlas de eventos catastróficos. Estos eventos pueden deberse a cualquier cosa, desde desastres naturales a simples errores del usuario. La copia de seguridad de una base de datos, con la capacidad de realizar la restauración a cualquier momento dado (como antes de que alguien eliminara datos críticos), ofrece una protección crítica. Puede restaurar la base de datos a un estado que sea lo más parecido posible al que había antes de la interrupción.

Se deben realizar dos tipos de copias de seguridad para obtener la capacidad de restauración:

- Copias de seguridad de bases de datos: completas, incrementales o diferenciales
- Copias de seguridad del registro de transacciones

Puede realizar copias de seguridad completas de bases de datos en el nivel de aplicación o copias de seguridad con instantáneas de almacenamiento. Las instantáneas de almacenamiento no reemplazan las copias de seguridad del registro de transacciones. Las copias de seguridad del registro de transacciones siguen siendo importantes para restaurar la base de datos a un momento dado o para eliminar las transacciones ya confirmadas de los registros. Aun así, las instantáneas de almacenamiento pueden acelerar la recuperación al proporcionar rápidamente una imagen de puesta al día de la base de datos. 

SAP HANA en Azure (instancias grandes) ofrece dos opciones de copia de seguridad y restauración:

- Puede usar una herramienta de protección de datos de terceros para crear copias de seguridad. Esta herramienta debe ser capaz de crear instantáneas coherentes con la aplicación o debe poder usar la interfaz de respaldo para transmitir con varias sesiones a una ubicación de copia de seguridad adecuada. Se admiten varias herramientas. La elección de la herramienta debe analizarse y diseñarse con el equipo del proyecto para cumplir los requisitos de las ventanas de copia de seguridad del cliente. Además, es muy importante probar el procedimiento de copia de seguridad y restauración durante la fase del proyecto.
- Puede usar copias de seguridad de instantáneas de almacenamiento con una utilidad proporcionada por Microsoft, como se describe en el capítulo siguiente.

>[!NOTE]
>Antes de HANA 2.0 SPS4, no se podía realizar instantáneas de bases de datos de contenedores de bases de datos multiinquilino (es decir, de más de un inquilino).
Con SPS4 y las versiones más recientes, SAP es totalmente compatible con esta característica de instantánea.



## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Uso de instantáneas de almacenamiento de SAP HANA en Azure (instancias grandes)

La infraestructura de almacenamiento subyacente de SAP HANA en Azure (instancias grandes) admite instantáneas de almacenamiento de volúmenes. Se admite tanto la copia de seguridad como la restauración de volúmenes, pero es preciso tener en cuenta lo siguiente:

- En lugar de las copias de seguridad de bases de datos completas, se toman instantáneas del volumen de almacenamiento con frecuencia.
- Antes de que se desencadene una instantánea de almacenamiento en los volúmenes /hana/data, la herramienta de instantáneas (azacsnap) inicia una instantánea de SAP HANA. Esta instantánea de SAP HANA es el punto de coherencia de las posibles restauraciones del registro después de la recuperación de la instantánea de almacenamiento. 
- Para que la instantánea de HANA se realice correctamente, debe disponer de una instancia de HANA activa. En un escenario con HANA System Replication (HSR), la instantánea de almacenamiento no se admite en un nodo secundario actual en el que no se pueda realizar una instantánea de HANA.
- Después de que la instantánea de almacenamiento se ejecute correctamente, se eliminará la instantánea de SAP HANA.
- Se pueden crear instantáneas de otros volúmenes como /hana/shared (incluido /usr/sap) en cualquier momento sin interacción con la base de datos.

Las copias de seguridad del registro de transacciones se realizan con frecuencia y se almacenan en el volumen hana/logbackups o en Azure. Puede desencadenar el volumen /hana/logbackups que contiene las copias de seguridad del registro de transacciones para tomar una instantánea por separado. En ese caso, no es preciso ejecutar una instantánea de datos de HANA. Dado que todos los archivos de /hana/logbackup son coherentes porque están "sin conexión", también puede hacer una copia de seguridad de ellos en cualquier momento en otra ubicación de copia de seguridad para archivarlos.
Si debe restaurar una base de datos a un momento dado, en el caso de una interrupción de producción, la herramienta azacsnap puede clonar cualquier instantánea de datos en un nuevo volumen para recuperar la base de datos (método de restauración preferido), o bien restaurar una instantánea en el mismo volumen de datos donde se encuentra la base de datos.

>[!NOTE]
> Si restaura una instantánea anterior (snaprevert) en el volumen de datos original, se eliminarán todas las instantáneas creadas más recientes. El sistema de almacenamiento hace esto porque los puntos de datos de las instantáneas creadas más recientes no serán válidos. Empiece siempre a revertir la instantánea más reciente o, todavía mejor, clone la instantánea en un nuevo volumen. Mediante el proceso de clonación, no se eliminará nada.


## <a name="storage-snapshot-considerations"></a>Consideraciones de las instantáneas de almacenamiento

>[!NOTE]
>Las instantáneas de almacenamiento consumen espacio de almacenamiento que está asignado a las unidades de HANA (instancias grandes). Tenga en cuenta los siguientes aspectos de la programación de instantáneas de almacenamiento y del número de instantáneas que va a conservar. 

Mecánica concreta de las instantáneas de almacenamiento de SAP HANA en Azure (instancias grandes):

- Una instantánea de almacenamiento concreta en el momento en que se toma consume poco espacio de almacenamiento.
- Cuando el contenido de datos cambia y el contenido de los archivos de datos de SAP HANA cambia en el volumen de almacenamiento, la instantánea debe almacenar el contenido del bloque original y los cambios de datos.
- El resultado es que la instantánea de almacenamiento aumenta de tamaño. Cuanto más tiempo dure la instantánea, mayor será la instantánea de almacenamiento.
- Cuantos más cambios se realicen en el volumen de la base de datos de SAP HANA a lo largo de la vigencia de una instantánea de almacenamiento, mayor será el consumo de espacio de dicha instantánea.

SAP HANA en Azure (instancias grandes) incluye tamaños de volumen fijos para los volúmenes de datos y de registro de SAP HANA. La realización de instantáneas de esos volúmenes consume el espacio del volumen. Necesita:

- Determinar cuándo se deben programar las instantáneas de almacenamiento.
- Supervisar el consumo de espacio de los volúmenes de almacenamiento. 
- Administrar el número de instantáneas que se almacenan. 

Puede deshabilitar las instantáneas de almacenamiento al importar grandes cantidades de datos o al realizar otros cambios importantes en la base de datos de HANA. 


En las secciones siguientes se proporciona información para realizar estas instantáneas y se incluyen recomendaciones generales:

- Aunque el hardware puede admitir 255 instantáneas por volumen, es conveniente no acercarse a ese número. La recomendación es usar 250 o menos.
- Antes de realizar instantáneas de almacenamiento, supervise el espacio disponible y lleve un seguimiento de él.
- Reduzca el número de instantáneas del almacenamiento en función del espacio libre. Puede reducir el número de instantáneas que mantiene, o ampliar los volúmenes. Puede solicitar más almacenamiento en unidades de un terabyte.
- Durante actividades como mover datos a SAP HANA con herramientas de migración de la plataforma SAP (R3load) o restaurar bases de datos de SAP HANA a partir de copias de seguridad, deshabilite las instantáneas de almacenamiento en el volumen /hana/data. 
- Durante reorganizaciones mayores de tablas de SAP HANA, evite realizar instantáneas de mantenimiento cuando sea posible.
- Las instantáneas de almacenamiento son un requisito previo para sacar provecho de las funcionalidades de recuperación ante desastres de SAP HANA en Azure (instancias grandes).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Requisitos previos para usar instantáneas de almacenamiento de autoservicio

Lea la documentación titulada [¿Qué es la herramienta Azure Application Consistent Snapshot?](../../../azure-netapp-files/azacsnap-introduction.md)

Hay dos maneras de implementar esta herramienta. 
1)  Localmente en el servidor de bases de datos
2)  De forma remota en una máquina virtual de "copia de seguridad"

Si crea una máquina virtual de copia de seguridad, asegúrese de que esté instalado en dicha máquina virtual el cliente de HANA más reciente. Con este método, azacsnap debe poder abrir una conexión de base de datos remota a una instancia de HANA que se ejecute en otra máquina virtual.
Debe solicitar una clave SSH y un usuario de almacenamiento al equipo de Soporte técnico de Microsoft para poder acceder al almacenamiento. Sin esta clave SSH y el usuario, no es posible crear instantáneas.

## <a name="download-and-set-up-azacsnap"></a>Descarga y configuración de azacsnap

Para configurar instantáneas de almacenamiento con instancias grandes de HANA, empiece por descargar e instalar la herramienta azacsnap, tal como se describe en [Primeros pasos con la herramienta Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-get-started.md).

La herramienta azacsnap crea un usuario denominado azacsnap de forma predeterminada. Si prefiere otro nombre, puede especificarlo durante la instalación. Vea la documentación anterior para obtener información detallada.

## <a name="subsequent-next-steps"></a>Pasos posteriores
Consulte la documentación de azacsnap:

- [Instalación de la herramienta Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-installation.md)
- [Configuración de la herramienta Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [Prueba de la herramienta Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-cmd-ref-test.md)
- [Copias de seguridad con la herramienta Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)
- [Obtención de detalles con la herramienta Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-cmd-ref-details.md)
- [Eliminación con la herramienta Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-cmd-ref-delete.md)
- [Restauración con la herramienta Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-cmd-ref-restore.md)
- [Recuperación ante desastres con la herramienta Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-disaster-recovery.md)
- [Solución de problemas con la herramienta Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-troubleshoot.md)
- [Sugerencias y trucos de uso de la herramienta Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-tips.md)


## <a name="next-steps"></a>Pasos siguientes

Consulte el artículo [¿Qué es la herramienta Azure Application Consistent Snapshot?](../../../azure-netapp-files/azacsnap-introduction.md)

  



