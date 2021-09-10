---
title: Creación de un punto de conexión de servidor de Azure File Sync
description: Comprenda las opciones durante la creación del punto de conexión de servidor y cómo aplicarlas mejor a su situación.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 06/01/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: b981ae7394e070d4da793fddccdd52e4350bde86
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799471"
---
# <a name="create-an-azure-file-sync-server-endpoint"></a>Creación de un punto de conexión de servidor de Azure File Sync

Un punto de conexión de servidor representa una ubicación específica en un servidor registrado, como una carpeta en un volumen de servidor. Un punto de conexión de servidor debe cumplir las condiciones siguientes:

- Un punto de conexión de servidor debe ser una ruta de acceso en un servidor registrado (en lugar de un recurso compartido montado). No se admite el almacenamiento conectado a la red (NAS).
- Aunque el punto de conexión del servidor puede estar en el volumen del sistema, es posible que los puntos de conexión de servidor del volumen del sistema no usen la nube por niveles.
- No se admite el cambio de la ruta de acceso o letra de unidad después de establecer un punto de conexión de servidor en un volumen. Asegúrese de que usa una ruta de acceso adecuada antes de crear el punto de conexión del servidor.
- Un servidor registrado puede admitir varios puntos de conexión de servidor, pero un grupo de sincronización solo puede tener un punto de conexión de servidor por servidor registrado en un momento dado. Otros puntos de conexión de servidor dentro del grupo de sincronización deben estar en servidores registrados diferentes.
- Pueden existir varios puntos de conexión de servidor en el mismo volumen si sus espacios de nombres no se superponen (por ejemplo, F:\sync1 y F:\sync2) y cada punto de conexión se sincroniza en un grupo de sincronización único. 

Este artículo le ayuda a comprender las opciones y las decisiones necesarias para crear un nuevo punto de conexión de servidor e iniciar la sincronización. Para que esto funcione, debe haber terminado de [planear la implementación de Azure File Sync](file-sync-planning.md) y también haber implementado los [recursos necesarios en los pasos anteriores](file-sync-deployment-guide.md) para crear un punto de conexión de servidor.

## <a name="prerequisites"></a>Prerequisites

Para crear un punto de conexión de servidor, primero debe asegurarse de que se cumplen los criterios siguientes: 
- El servidor tiene instalado el agente de Azure File Sync y se ha registrado. Puede encontrar instrucciones para instalar el agente de Azure File Sync en el artículo [Registro y cancelación del registro de un servidor con Azure File Sync](file-sync-server-registration.md). 
- Asegúrese de que se ha implementado un servicio de sincronización de almacenamiento. Para más información sobre cómo implementar un servicio de sincronización de almacenamiento, vea [How to deploy Azure File Sync](file-sync-deployment-guide.md) (Implementación de Azure Files Sync). 
- Asegúrese de que se ha implementado un grupo de sincronización. Aprenda a [crear un grupo de sincronización](file-sync-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint).
- Asegúrese de que el servidor está conectado a Internet y que se puede acceder a Azure. Azure File Sync usa el puerto 443 para toda la comunicación entre el servidor y el servicio en la nube.

## <a name="create-a-server-endpoint"></a>Creación de un punto de conexión de servidor

[!INCLUDE [storage-files-sync-create-server-endpoint](../../../includes/storage-files-sync-create-server-endpoint.md)]

### <a name="cloud-tiering-section"></a>Sección Nube por niveles

Al crear un nuevo punto de conexión de servidor, puede optar por la característica de nube por niveles Azure File Sync. Las opciones de la sección **Nube por niveles** se pueden cambiar más adelante. Sin embargo, hay diferentes opciones disponibles en la sección siguiente en función de tener habilitada la nube por niveles o no para el nuevo punto de conexión de servidor.

Consulte el [artículo sobre la nube por niveles](file-sync-cloud-tiering-overview.md) que trata los aspectos básicos, [las directivas](file-sync-cloud-tiering-policy.md) y los procedimientos recomendados en detalle. 

### <a name="initial-sync-section"></a>Sección de sincronización inicial

La sección **Sincronización inicial** solo está disponible para el primer punto de conexión de servidor de un grupo de sincronización. Para cualquier punto de conexión de servidor adicional, consulte la [sección de descarga inicial](#initial-download-section).

Hay dos comportamientos de sincronización iniciales fundamentalmente diferentes:

:::row:::
    :::column:::
        **Combinar**
    :::column-end:::
    :::column:::
        **Carga autoritativa**
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Combinar es la opción estándar y está seleccionada de forma predeterminada. Debe dejar la selección en **Combinar** a menos que para determinados escenarios de migración. 
* Al unir una ubicación de servidor, en la mayoría de los escenarios, la ubicación del servidor o el recurso compartido en la nube están vacíos. En estos casos, **Combinar** es el comportamiento correcto y dará lugar a los resultados esperados. 
* Cuando ambas ubicaciones contienen archivos y carpetas, los espacios de nombres se combinarán. Si hay archivos o nombres de carpeta en el servidor que también existen en el recurso compartido en la nube, habrá un conflicto de sincronización. [Los conflictos se resuelven automáticamente](../files/storage-files-faq.md#afs-conflict-resolution). </br> </br> Dentro de la opción **Combinar**, hay una selección para saber cómo llegará inicialmente al servidor el contenido del recurso compartido de archivos de Azure. Esta selección no tiene ningún impacto si el recurso compartido de archivos de Azure está vacío. Puede encontrar más detalles en el párrafo siguiente, [Descarga inicial](#initial-download-section).
    :::column-end:::
    :::column:::
        La **carga autoritativa** es una opción de sincronización inicial reservada para un escenario de migración específico. Sincronización de la misma ruta del servidor que también se utilizó para inicializar el recurso compartido en la nube con, por ejemplo, Azure Data Box. En este caso, la nube y las ubicaciones del servidor tienen principalmente los mismos datos, pero el servidor es ligeramente más reciente. Los usuarios seguían haciendo cambios mientras Data Box estaba en transporte. A continuación, este escenario de migración requiere la actualización de la nube sin problemas con los cambios en el servidor (más reciente) sin producir conflictos. Por lo tanto, el servidor es la autoridad que dará forma al espacio de nombres y Data Box se usó para evitar la carga inicial a gran escala desde el servidor. La carga autoritativa del servidor permite la adopción de la nube sin tiempo de inactividad, incluso cuando se usó un mecanismo de transporte de datos sin conexión para almacenar el almacenamiento en la nube.
    :::column-end:::
:::row-end:::

Un punto de conexión de servidor solo puede realizar correctamente el aprovisionamiento con la opción de carga autoritativa, cuando la ubicación del servidor tiene datos en él. Este bloque es para protegerse de errores de configuración accidentales. La carga autoritativa funciona como RoboCopy /MIR. Este modo refleja el origen al destino. El origen es el servidor AFS y el destino es el recurso compartido en la nube. La carga autoritativa dará forma al destino en la imagen del origen. 

* Los archivos y carpetas nuevos o actualizados se cargarán desde el servidor.
* Los archivos y carpetas que ya no existen en el servidor se eliminarán del recurso compartido en la nube.
* Los cambios de solo metadatos en archivos y carpetas del servidor se trasladarán de forma eficaz al recurso compartido en la nube como actualizaciones de solo metadatos.
* Es posible que existan archivos y carpetas en el servidor y el recurso compartido en la nube. Sin embargo, algunos archivos o carpetas pueden haber cambiado su directorio primario en el servidor desde la matriz del recurso compartido de archivos de Azure. Estos archivos y carpetas se purgarán del recurso compartido en la nube y se volverán a cargar. Por este problema, es mejor evitar la reestructuración del espacio de nombres a mayor escala durante una migración.

### <a name="initial-download-section"></a>Sección de descarga inicial

La sección **Descarga inicial** está disponible para el segundo y más puntos de conexión de servidor de un grupo de sincronización. El [primer punto de conexión de servidor de un grupo de sincronización tiene opciones adicionales](#initial-sync-section) relacionadas con la migración con Azure Data Box. Estas opciones no se aplican si este punto de conexión de servidor no es el primero del grupo de sincronización.

> [!NOTE]
> La selección de una opción de descarga inicial no tiene ningún impacto si el recurso compartido de archivos de Azure está vacío.

Como parte de esta sección, se puede elegir cómo llegará inicialmente al servidor el contenido del recurso compartido de archivos de Azure:

:::row:::
    :::column:::
        :::image type="content" source="media/file-sync-server-endpoint-create/initial-download-options.png" alt-text="Imagen que describe las opciones del Asistente para crear el puntos de conexión del servidor de Azure Portal" lightbox="media/file-sync-server-endpoint-create/initial-download-options-expanded.png":::
    :::column-end:::
    :::column:::
* **Solo espacio de nombres** </br> Solo llevará la estructura de archivos y carpetas del recurso compartido de archivos de Azure al servidor local. No se descarga ningún contenido del archivo. Esta opción es la predeterminada si anteriormente habilitó la nube por niveles para este nuevo punto de conexión de servidor.
* **Espacio de nombres en primer lugar y, a continuación, el contenido** </br> Para una disponibilidad más rápida de los datos, el espacio de nombres se desactivo primero, independientemente de su configuración de la nube por niveles. Una vez que el espacio de nombres está disponible en el servidor, el contenido del archivo se recupera de la nube al servidor. La recuperación se produce en función de la marca de tiempo de la última modificación en cada archivo. Si no hay suficiente espacio en el volumen del servidor, los archivos restantes permanecerán en niveles. Esta opción es la predeterminada si no ha activado la nube por niveles para este punto de conexión de servidor.
* **Evitar los archivos en niveles** </br> Esta opción descargará cada archivo en su totalidad antes de que el archivo se muestre en la carpeta del servidor. Esta opción evita que un archivo en niveles exista en el servidor. Un elemento de espacio de nombres y el contenido del archivo siempre están presentes al mismo tiempo. Evite esta opción si la recuperación ante desastres rápida desde la nube es la razón para crear un punto de conexión de servidor. Si tiene aplicaciones que requieren que los archivos completos estén presentes y no toleran archivos almacenados por niveles en su espacio de nombres, esta es la opción idónea. Esta opción no está disponible si usa la nube por niveles para el nuevo punto de conexión de servidor.
    :::column-end:::
:::row-end:::

Una vez seleccionada una opción de descarga inicial, no puede cambiarla después de confirmar la creación del punto de conexión del servidor. Cómo aparecen los archivos en el servidor una vez que finaliza la descarga inicial, depende del uso de la característica de nube por niveles y de si optó o no por [recuperar proactivamente los cambios en la nube](file-sync-cloud-tiering-overview.md#proactive-recalling). Esta última es una característica útil para sincronizar grupos con varios puntos de conexión de servidor en diferentes ubicaciones geográficas.

* **La nube por niveles está habilitada** </br> Los archivos nuevos y modificados de otros puntos de conexión de servidor aparecerán como archivos en niveles en este punto de conexión de servidor. Estos cambios solo aparecerán como archivos completos si optó por la [recuperación proactiva](file-sync-cloud-tiering-overview.md#proactive-recalling) de los cambios en el recurso compartido de archivos de Azure por parte de otros puntos de conexión de servidor.
*  **La nube por niveles está deshabilitada** </br> Los archivos nuevos y modificados de otros puntos de conexión de servidor aparecerán como archivos completos en este punto de conexión de servidor. No aparecerán primero como archivos en niveles y, a continuación, se recuperarán. Los archivos en niveles con la nube por niveles desactivada son una característica de recuperación ante desastres rápida y solo aparecen durante el aprovisionamiento inicial.


## <a name="next-steps"></a>Pasos siguientes

Hay más información sobre los recursos compartidos de archivos de Azure y Azure File Sync. Los artículos siguientes lo ayudarán a comprender las opciones avanzadas y los procedimientos recomendados. También proporcionan ayuda para solucionar problemas. Estos artículos contienen vínculos a la [documentación del recurso compartido de archivos de Azure](../files/storage-files-introduction.md) cuando corresponda.

* [Información general acerca de la migración](../files/storage-files-migration-overview.md)
* [Planeamiento de una implementación de Azure File Sync](../file-sync/file-sync-planning.md)
* [Creación de un recurso compartido de archivos](../files/storage-how-to-create-file-share.md)
* [Solución de problemas de Azure File Sync](../file-sync/file-sync-troubleshoot.md)
