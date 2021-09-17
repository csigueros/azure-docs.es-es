---
title: Procedimientos recomendados para la recuperación ante desastres con Azure File Sync
description: Obtenga información sobre los procedimientos recomendados para la recuperación ante desastres con Azure File Sync. En concreto, alta disponibilidad, protección de datos y redundancia de datos.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/18/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 87fd5de90896a1a5b97cf9fa07a880ebcea1f35b
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122420603"
---
# <a name="best-practices-for-disaster-recovery-with-azure-file-sync"></a>Procedimientos recomendados para la recuperación ante desastres con Azure File Sync

Para Azure File Sync, hay tres áreas principales que se deben tener en cuenta para la recuperación ante desastres: alta disponibilidad, protección y copia de seguridad de datos y redundancia de datos. En este artículo se trata cada área y le ayuda a decidir qué configuración usar para su propia solución de recuperación ante desastres.

En una implementación de Azure File Sync, el punto de conexión de nube siempre contiene una copia completa de los datos, mientras que el servidor local se puede ver como una caché descartable de los datos. En caso de desastre del lado servidor, puede recuperarlo mediante el aprovisionamiento de un nuevo servidor, la instalación del agente de Azure File Sync en él y su configuración como un nuevo punto de conexión de servidor.

Debido a su naturaleza híbrida, algunas estrategias tradicionales de copia de seguridad y recuperación ante desastres del servidor no funcionarán con Azure File Sync. Para cualquier servidor registrado, Azure File Sync no admite:

> [!WARNING]
> La toma de cualquiera de estas acciones puede dar lugar a problemas de sincronización o archivos en capas rotos que pueden provocar la pérdida de datos. Si ha realizado una de estas acciones, póngase en contacto con el Soporte técnico de Azure para asegurarse de que la implementación es correcta.

- Transferencia de unidades de disco de un servidor a otro
- Restauración a partir de una copia de seguridad del sistema operativo
- Clonación del sistema operativo de un servidor en otro servidor
- Reversión a un punto de control de máquina virtual anterior
- Restauración de archivos desde la copia de seguridad local si la nube por niveles está habilitada


## <a name="high-availability"></a>Alta disponibilidad

Hay dos estrategias diferentes que puede usar para lograr una alta disponibilidad para el servidor local. Puede configurar un clúster de conmutación por error o configurar un servidor en espera. El factor determinante entre cualquiera de las configuraciones es cuánto está dispuesto a invertir en el sistema y si la reducción del tiempo que el sistema está fuera de servicio en caso de desastre merece ese costo adicional.

Para un clúster de conmutación por error, no es necesario realizar ningún paso especial para usar Azure File Sync. Para un servidor en espera, debe realizar las siguientes configuraciones:

Debe tener un servidor secundario con puntos de conexión de servidor diferentes que se sincronicen con el mismo grupo de sincronización que el servidor principal, pero que no habiliten el acceso del usuario final al servidor. Esto permite que todos los archivos se sincronicen desde el servidor principal al servidor en espera. Puede considerar la posibilidad de habilitar los niveles de solo espacio de nombres para que solo se descargue inicialmente el espacio de nombres. Si se produce un error en el servidor principal, puede usar DFS-N para volver a configurar rápidamente el acceso del usuario final al servidor en espera.

## <a name="data-protectionbackup"></a>Protección y copia de seguridad de datos

La protección de los datos reales es un componente clave de una solución de recuperación ante desastres. Hay dos maneras principales de hacerlo con los recursos compartidos de archivos de Azure: puede hacer una copia de seguridad de los datos en la nube o de forma local. Se recomienda encarecidamente realizar una copia de seguridad de los datos en la nube porque el punto de conexión de nube contendrá una copia completa de los datos, mientras que los puntos de conexión de servidor solo pueden contener un subconjunto de los datos.

### <a name="back-up-your-data-in-the-cloud"></a>Copia de seguridad de los datos en la nube

Debe usar [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffile-sync%2ftoc.json) como solución de copia de seguridad en la nube. Azure Backup controla la programación, retención y restauración de las copias de seguridad, entre otras cosas. Si lo prefiere, puede tomar [instantáneas de recurso compartido](../files/storage-snapshots-files.md?toc=/azure/storage/file-sync/toc.json) manualmente y configurar su propia solución de programación y retención, pero esto no es lo ideal. Como alternativa, puede usar soluciones de terceros para realizar directamente una copia de seguridad de los recursos compartidos de archivos de Azure.

Si se produce un desastre, puede restaurar desde una instantánea de recurso compartido, que es una copia de solo lectura a un momento dado del recurso compartido de archivos. Dado que estas instantáneas son de solo lectura, no se verán afectadas por el ransomware. En el caso de conjuntos de datos grandes, en los que las operaciones de restauración de recurso compartido completo llevan mucho tiempo, puede habilitar el acceso directo de los usuarios a la instantánea para que los usuarios puedan copiar los datos que necesitan en su unidad local mientras se completa la restauración.

Las instantáneas se almacenan directamente en el recurso compartido de archivos de Azure, tanto si las toma manualmente como si Azure Backup las toma automáticamente. Por lo tanto, debe [habilitar la eliminación temporal](../files/storage-files-prevent-file-share-deletion.md?toc=/azure/storage/file-sync/toc.json) para proteger las instantáneas frente a eliminaciones accidentales del recurso compartido de archivos.

Para más información, consulte [Acerca de la copia de seguridad de recursos compartidos de archivos de Azure](../../backup/azure-file-share-backup-overview.md) o póngase en contacto con el proveedor de copias de seguridad para ver si admite la copia de seguridad de recursos compartidos de Azure.

### <a name="back-up-your-data-on-premises"></a>Copia de seguridad de los datos locales

Si habilita la nube por niveles, no implemente una solución de copia de seguridad local. Con la nube por niveles habilitada, solo un subconjunto de los datos se almacenará localmente en el servidor, el resto de los datos se almacenará en el punto de conexión de nube. Dependiendo de la solución de copia de seguridad que use para una copia de seguridad local, los archivos en capas: se omitirán y no se realizará copia de seguridad (debido a su atributo FILE_ATTRIBUTE_RECALL_ONDATA_ACCESS), solo se realizarán copias de seguridad como archivo en capas y es posible que no sean accesibles durante la restauración debido a cambios en el recurso compartido en directo, o se recuperarán en el disco, lo que dará lugar a cargos elevados de salida.

Si decide usar una solución de copia de seguridad local, las copias de seguridad deben realizarse en un servidor del grupo de sincronización que tenga deshabilitada la nube por niveles. Al realizar una restauración, use las opciones de restauración de nivel de volumen o de archivo. Los archivos restaurados con la opción de restauración a nivel de archivo se sincronizarán con todos los puntos de conexión del grupo de sincronización y los archivos existentes se reemplazarán con la versión restaurada desde la copia de seguridad. Las restauraciones de nivel de volumen no reemplazarán las versiones de archivo más recientes en el punto de conexión de nube ni en otros puntos de conexión de servidor.

En la versión 9 del agente de Azure File Sync y versiones posteriores, las [instantáneas de Servicio de instantáneas de volumen (VSS)](file-sync-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service) (incluida la pestaña **Versiones anteriores**) ya se admiten en los volúmenes que tienen habilitada la nube por niveles. Esto le permite realizar restauraciones de autoservicio en lugar de depender de un administrador para que realice las restauraciones. Sin embargo, debe habilitar la compatibilidad con versiones anteriores a través de PowerShell, lo que aumentará los costos de almacenamiento de instantáneas. Las instantáneas de VSS no protegen frente a desastres en el propio punto de conexión de servidor, por lo que solo se deben usar junto con las copias de seguridad del lado de la nube. Para obtener más información, consulte [Autoservicio de restauración a través de versiones anteriores y VSS](file-sync-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="data-redundancy"></a>Redundancia de datos

Para garantizar una solución sólida de recuperación ante desastres, agregue algún tipo de redundancia de datos a la infraestructura. Hay cuatro ofertas de redundancia para Azure Files: [almacenamiento con redundancia local (LRS)](../common/storage-redundancy.md#locally-redundant-storage), [almacenamiento con redundancia de zona (ZRS)](../common/storage-redundancy.md#zone-redundant-storage), [almacenamiento con redundancia geográfica (GRS)](../common/storage-redundancy.md#geo-redundant-storage) y [almacenamiento con redundancia de zona geográfica (GZRS)](../common/storage-redundancy.md#geo-zone-redundant-storage).

- [Almacenamiento con redundancia local (LRS)](../common/storage-redundancy.md#locally-redundant-storage): con LRS, todos los archivos se almacenan tres veces dentro de un clúster de almacenamiento de Azure. Esto protege contra la pérdida de datos debido a errores de hardware, como una unidad de disco incorrecta. No obstante, si se produce un desastre como un incendio o una inundación en el centro de datos, es posible que todas las réplicas de una cuenta de almacenamiento con LRS se pierdan o no se puedan recuperar.
- [Almacenamiento con redundancia de zona (ZRS)](../common/storage-redundancy.md#zone-redundant-storage): con ZRS, se almacenan tres copias de cada archivo. Sin embargo, estas copias están aisladas físicamente en tres clústeres de almacenamiento distintos en distintas *zonas de disponibilidad* de Azure. Las zonas de disponibilidad son ubicaciones físicas exclusivas dentro de una región de Azure. Cada zona consta de uno o varios centros de datos equipados con alimentación, refrigeración y redes independientes. No se acepta la escritura en el almacenamiento hasta que se escribe en los clústeres de almacenamiento en las tres zonas de disponibilidad. 
- [Almacenamiento con redundancia geográfica (GRS)](../common/storage-redundancy.md#geo-redundant-storage): con GRS, tiene dos regiones, una principal y una secundaria. Los archivos se almacenan tres veces dentro de un clúster de almacenamiento de Azure en la región primaria. Las escrituras se replican de forma asincrónica en una región secundaria definida por Microsoft. GRS proporciona seis copias de los datos distribuidas entre dos regiones de Azure.
- [Almacenamiento con redundancia de zona geográfica (GZRS)](../common/storage-redundancy.md#geo-zone-redundant-storage): puede pensar en GZRS como si fuera como ZRS, pero con redundancia geográfica. Con GZRS, los archivos se almacenan tres veces en tres clústeres de almacenamiento distintos en la región primaria. Todas las escrituras se replican de forma asincrónica en una región secundaria definida por Microsoft.

Para una solución sólida de recuperación ante desastres, la mayoría de los clientes deben considerar ZRS. ZRS agrega la menor cantidad de costo adicional por sus ventajas de redundancia de datos agregadas y también es la solución más fluida en caso de una interrupción. Si la directiva o los requisitos normativos de su organización requieren redundancia geográfica para los datos, considere la posibilidad de usar GRS o GZRS.

### <a name="geo-redundancy"></a>Redundancia geográfica

Si habilita GRS o GZRS en la cuenta de almacenamiento que contiene el punto de conexión de nube, también debe habilitarlo en el servicio de sincronización de almacenamiento. Esto garantiza que toda la información sobre la topología de Azure File Sync y los datos contenidos en el punto de conexión de nube se copian de forma asincrónica en la región secundaria emparejada en caso de desastre.

En el caso de los recursos configurados con GRS o GZRS, Microsoft iniciará la conmutación por error del servicio si se considera que la región primaria es irrecuperable permanentemente o no está disponible durante mucho tiempo. El servicio de Azure File Sync conmutará por error automáticamente a la región emparejada en caso de desastre en la región cuando el servicio de sincronización de almacenamiento use GRS o GZRS. Si usa Azure File Sync configurado con GRS o GZRS, no es necesario realizar ninguna acción en caso de desastre.

Aunque puede solicitar manualmente una conmutación por error del servicio de sincronización de almacenamiento a su región emparejada de GRS o GZRS, no se recomienda hacerlo fuera de las interrupciones regionales a gran escala, ya que el proceso no es fluido y puede incurrir en costos adicionales. Para iniciar el proceso, abra una solicitud de soporte técnico y solicite la conmutación por error de las cuentas de almacenamiento de Azure que contienen el recurso compartido de archivos de Azure y el servicio de sincronización de almacenamiento.

> [!WARNING]
> Si va a iniciar este proceso manualmente, debe ponerse en contacto con el soporte técnico para solicitar que su servicio de sincronización de almacenamiento se conmute por error. Si intenta crear un nuevo servicio de sincronización de almacenamiento con los mismos puntos de conexión de servidor en la región secundaria, los datos adicionales pueden permanecer en la cuenta de almacenamiento, ya que la instalación anterior de Azure File Sync no se limpiará.

Una vez que se produce una conmutación por error, los puntos de conexión de servidor cambiarán para sincronizarse automáticamente con el punto de conexión de nube en la región secundaria. Sin embargo, los puntos de conexión de servidor deben conciliarse con los puntos de conexión de nube. Esto puede dar lugar a conflictos de archivos, ya que es posible que los datos de la región secundaria no se puedan encontrar hasta la principal.

## <a name="next-steps"></a>Pasos siguientes

[Acerca de la copia de seguridad de recursos compartidos de archivos de Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffile-sync%2ftoc.json)