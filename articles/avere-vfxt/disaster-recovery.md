---
title: Guía de recuperación ante desastres para Avere vFXT for Azure
description: Procedimientos para proteger los datos de Avere vFXT for Azure ante la eliminación accidental o las interrupciones
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: ec58e921940be71b0189bcba96596c18b1b8786b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751495"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Guía de recuperación ante desastres para Avere vFXT for Azure

En este artículo se describen las estrategias para proteger el flujo de trabajo de Avere vFXT for Azure y se proporciona una guía para realizar copias de seguridad de los datos, de forma que pueda recuperarse en caso de accidentes o interrupciones.

Avere vFXT for Azure almacena temporalmente los datos en su caché. Los datos se almacenan a largo plazo en sistemas de almacenamiento de back-end: sistemas de hardware locales, contenedores de Azure Blob Storage o ambos.

Para protegerse frente a interrupciones y posibles pérdidas de datos, tenga en cuenta estas cuatro áreas:

* Protección frente al tiempo de inactividad si un sistema de Avere vFXT for Azure deja de estar disponible
* Protección de los datos en la caché del clúster
* Protección de los datos en el almacenamiento de hardware conectado a la red de back-end
* Protección de los datos en el almacenamiento en la nube de Azure Blob de back-end

Cada cliente de Avere vFXT for Azure debe crear su propio plan de recuperación ante desastres completo, que incluya planes para estos elementos. También se puede crear resistencia en las aplicaciones que se usan con el clúster de vFXT. Lea los vínculos de [Pasos siguientes](#next-steps) para obtener ayuda.

## <a name="protect-against-downtime"></a>Protección frente al tiempo de inactividad

La redundancia está integrada en el producto Avere vFXT for Azure:

* El clúster tiene alta disponibilidad y los nodos individuales del clúster pueden conmutar por error con una interrupción mínima.
* Los datos modificados en la caché se escriben periódicamente en los archivadores básicos de back-end (almacenamiento en hardware conectado a la red o Azure Blob) para el almacenamiento a largo plazo.

Cada clúster de Avere vFXT for Azure debe encontrarse en una sola zona de disponibilidad, pero se pueden usar clústeres redundantes ubicados en distintas zonas o regiones, para proporcionar acceso rápidamente en caso de una interrupción regional.

También se pueden colocar contenedores de almacenamiento en varias regiones, si le preocupa perder el acceso a los datos. Tenga en cuenta, sin embargo, que las transacciones entre regiones tienen una mayor latencia y un costo más alto que las transacciones que permanecen dentro de una región.

## <a name="protect-data-in-the-cluster-cache"></a>Protección de los datos en la caché del clúster

Los datos en caché siempre se escriben en los archivadores básicos antes de un apagado normal; sin embargo, en un apagado no controlado se pueden perder los datos modificados de la caché.

Si usa el clúster solo para optimizar las lecturas de archivos, no hay ningún cambio que se pueda perder. Si también usa el clúster para copiar en caché los cambios de archivo (escrituras), decida si quiere ajustar o no las [directivas de caché](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) de los archivadores básicos<!-- link to legacy doc --> para personalizar la frecuencia con la que se escriben los datos en el almacenamiento a largo plazo.

En general, el plan de recuperación debe centrarse en la creación de copias de seguridad de los sistemas de almacenamiento de back-end, que contienen más datos y suelen ser más importantes para volver a establecer el flujo de trabajo después de un error.

## <a name="protect-data-in-nas-core-filers"></a>Protección de los datos en los archivadores básicos de almacenamiento conectado a la red

Use los métodos aceptados para proteger los datos almacenados en un archivador básico de almacenamiento de hardware conectado a la red local, incluidas las instantáneas y las copias de seguridad completas, tal y como recomienda el proveedor del almacenamiento conectado a la red. La recuperación ante desastres de estos archivadores básicos queda fuera del ámbito de este artículo.

## <a name="protect-data-in-azure-blob-storage"></a>Protección de los datos en Azure Blob Storage

Avere vFXT for Azure usa el almacenamiento con redundancia local (LRS) para los archivadores básicos de Azure Blob. Esto significa que los datos de los contenedores de Blob se copian automáticamente para protegerse frente a errores de hardware transitorios dentro de un centro de datos.

En esta sección se ofrecen sugerencias sobre cómo proteger aún más los datos de Blob Storage ante las poco frecuentes interrupciones de toda la región o las eliminaciones accidentales.

Los procedimientos recomendados para proteger los datos en Azure Blob Storage incluyen:

* Copie los datos críticos en otra cuenta de almacenamiento de otra región con frecuencia (con la frecuencia que se determine en el plan de recuperación ante desastres).
* Controle el acceso a los datos de todos los sistemas de destino para evitar daños o eliminaciones accidentales. Considere la posibilidad de utilizar [bloqueos de recursos](../azure-resource-manager/management/lock-resources.md) en el almacenamiento de datos.
* Habilite la característica de [instantánea de nube](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) de Avere vFXT for Azure para los archivadores básicos de Blob.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Copia de los datos del archivador básico de Avere vFXT en una cuenta de copia de seguridad

Siga estos pasos para establecer una copia de seguridad de datos en otra cuenta.

1. Si es necesario, genere una nueva clave de cifrado y guárdela de forma segura fuera de los sistemas afectados.

   Si el clúster de Avere vFXT for Azure cifra los datos, debe generar una nueva clave de cifrado antes de copiar los datos en otra cuenta de almacenamiento. Almacene esta clave y la contraseña de forma segura en una instalación protegida que no se vea afectada por un error regional.

   Debe proporcionar esta clave al agregar el contenedor a un clúster, incluso si va a volver a agregarlo a su clúster original.

   Consulte el artículo sobre la [configuración de cifrado en la nube](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> para obtener información detallada.

   Si el contenedor usa solo el cifrado integrado de Azure, puede omitir este paso.

1. Quite el archivador básico del sistema. Esto obliga al clúster a escribir todos los datos modificados en el almacenamiento de back-end.

   Si bien tendrá que volver a agregar el archivador básico después de la copia de seguridad, quitarlo es la mejor manera de garantizar que todos los datos se escriben por completo en el back-end. La opción "suspender" puede dejar a veces datos modificados en la caché. <!-- xxx true? or just metadata? -->

   Anote el nombre del archivador básico y la información de unión (que aparece en la página **Namespace** [Espacio de nombres] del panel de control) para poder replicarlo al volver a agregar el contenedor después de la copia de seguridad.

   Use el panel de control del clúster para quitar el archivador básico. [Abra el panel de control del clúster](avere-vfxt-cluster-gui.md) y elija **Core Filer** > **Manage core filers** (Archivador básico > Administrar archivadores básicos). Busque el sistema de almacenamiento del que desea hacer una copia de seguridad y use el botón **Remove** (Quitar) para eliminarlo del clúster.

1. Cree un nuevo contenedor de almacenamiento de blobs vacío en otra cuenta de almacenamiento de otra región.

1. Use cualquier herramienta de copia adecuada para copiar los datos del archivador básico en el nuevo contenedor. La copia debe replicar los datos sin cambios y sin transformar el formato propietario del sistema de archivos en la nube que usa Avere vFXT for Azure. Las herramientas basadas en Azure incluyen [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md) y [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Después de copiar los datos en el contenedor de copia de seguridad, vuelva a agregar el contenedor original al clúster, tal como se describe en [Configurar el almacenamiento](avere-vfxt-add-storage.md).

   * Use el mismo nombre de archivador básico y la misma información de unión para que no sea necesario cambiar los flujos de trabajo del cliente.
   * Establezca el valor de **Bucket contents** (Contenido del cubo) como la opción de datos existente.
   * Si el clúster cifró el contenedor, debe especificar la clave de cifrado actual para su contenido; se trata de la clave que actualizó en el paso uno.

En las copias de seguridad siguientes a la primera, ya no necesita crear un nuevo contenedor de almacenamiento. Sin embargo, considere la posibilidad de generar una nueva clave de cifrado cada vez que realice una copia de seguridad, para asegurarse de que tiene la clave actual almacenada en un lugar que recuerde.

### <a name="access-a-backup-data-source-during-an-outage"></a>Acceso a un origen de datos de copia de seguridad durante una interrupción

Para acceder al contenedor de copia de seguridad desde un clúster de Avere vFXT for Azure, siga este proceso:

1. Si es necesario, cree un nuevo clúster de Avere vFXT for Azure en una región no afectada.

   > [!TIP]
   > Al crear un clúster de Avere vFXT for Azure, puede guardar una copia de la plantilla y los parámetros de creación. Si guarda esta información al crear el clúster principal, puede usarla para crear un clúster de reemplazo con las mismas propiedades. En la página [Resumen](avere-vfxt-deploy.md#validation-and-purchase), haga clic en el vínculo **Descargar plantilla y parámetros**. Guarde la información en un archivo antes de crear el clúster.

1. Agregue un nuevo archivador básico en la nube que apunte al contenedor de blobs duplicado.

   Asegúrese de especificar que el contenedor de destino ya contiene datos, en el valor de **Bucket contents** (Contenido del cubo) del asistente para la creación de un archivador básico. Si deja accidentalmente este valor como **Empty** (Vacío), recibirá una alerta.  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Si es necesario, actualice los clientes para que monten el nuevo clúster o el nuevo archivador básico en lugar del original. Si agrega el archivador básico de reemplazo con el mismo nombre y la misma ruta de acceso de unión que el contenedor original, no necesitará actualizar los procesos de cliente a menos que necesite montar el nuevo clúster en una nueva dirección IP.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre la personalización de la configuración de Avere vFXT for Azure, lea [Ajuste del clúster](avere-vfxt-tuning.md).
* Más información sobre la recuperación ante desastres y la compilación de aplicaciones resistentes en Azure:

  * [Guía técnica sobre resistencia en Azure](/azure/architecture/reliability/architect)
  * [Recuperación después de una interrupción del servicio en toda la región](/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Recuperación ante desastres y alta disponibilidad para aplicaciones de Azure](/azure/architecture/framework/resiliency/backup-and-recovery)
  <!-- can't find these in the source tree to use relative links -->
