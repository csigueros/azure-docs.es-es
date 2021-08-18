---
title: Copia de aplicaciones y datos en nodos de grupo
description: Obtenga información sobre cómo copiar aplicaciones y datos en nodos de grupo.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: d53a285319e46f9311684b0ad66c1a278750f8b9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752088"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Copia de aplicaciones y datos en nodos de grupo

Azure Batch admite varias maneras de copiar datos y aplicaciones en nodos de proceso para que estén disponibles para su uso en tareas.

El método que elija puede depender del ámbito del archivo o de la aplicación. Es posible que se necesiten datos y aplicaciones para ejecutar todo el trabajo y, por tanto, deban instalarse en cada nodo. Algunos archivos o aplicaciones pueden ser necesarios solo para una tarea específica. Otros puede que deban instalarse para el trabajo, pero no es necesario que estén en cada nodo. Batch dispone de herramientas para cada uno de estos escenarios.

## <a name="determine-the-scope-required-of-a-file"></a>Determinación del ámbito para el que se necesita un archivo

Debe determinar el ámbito de un archivo, es decir, si el archivo es necesario para un grupo, un trabajo o una tarea. Los archivos cuyo ámbito es el grupo deben usar paquetes de aplicación de grupo o una tarea de inicio. Los archivos cuyo ámbito es el trabajo deben usar una tarea de preparación del trabajo. Un buen ejemplo de archivos cuyo ámbito es el grupo o el trabajo son las aplicaciones. Los archivos cuyo ámbito es la tarea deben usar archivos de recursos de tareas.

## <a name="pool-start-task-resource-files"></a>Archivos de recursos de tareas de inicio de grupo

En el caso de aplicaciones o datos que deban instalarse en cada nodo del grupo, use archivos de recursos de la tarea de inicio del grupo. Use este método junto con un [paquete de aplicación](batch-application-packages.md) o la recopilación de archivos de recursos de la tarea de inicio para ejecutar un comando de instalación.  

Por ejemplo, puede usar la línea de comandos de la tarea de inicio para mover o instalar aplicaciones. También puede especificar una lista de archivos o contenedores en una cuenta de almacenamiento de Azure. Para más información, consulte [Add#ResourceFile en la documentación de REST](/rest/api/batchservice/pool/add#resourcefile).

Si cada trabajo que se ejecuta en el grupo ejecuta una aplicación (.exe) que debe instalarse primero con un archivo .msi, deberá establecer la propiedad **esperar operación correcta** en **true**. Para más información, consulte [Add#StartTask en la documentación de REST](/rest/api/batchservice/pool/add#starttask).

## <a name="application-package-references"></a>Referencias de paquetes de aplicación

En el caso de las aplicaciones o los datos que deban instalarse en cada nodo del grupo, considere la posibilidad de usar [paquetes de aplicación](batch-application-packages.md). No hay ningún comando de instalación asociado a un paquete de aplicación, pero puede usar una tarea de inicio para ejecutar cualquier comando de instalación. Si la aplicación no requiere instalación o está formada por un gran número de archivos, puede usar este método.

Los paquetes de aplicación son útiles cuando se tiene un gran número de archivos, ya que pueden combinar muchas referencias de archivo en una pequeña carga. Si intenta incluir más de 100 archivos de recursos independientes en una tarea, el servicio de Batch puede encontrar limitaciones internas del sistema para una sola tarea. Los paquetes de aplicación también son útiles cuando hay muchas versiones diferentes de la misma aplicación y es necesario elegir entre ellas.

## <a name="extensions"></a>Extensiones

Las [extensiones](create-pool-extensions.md) son pequeñas aplicaciones que facilitan la configuración e instalación posteriores al aprovisionamiento en nodos de proceso de Batch. Al crear un grupo, puede seleccionar una extensión compatible para instalar en los nodos de proceso a medida que se aprovisionan. Después, la extensión puede realizar su operación esperada.

## <a name="job-preparation-task-resource-files"></a>Archivos de recursos de la tarea de preparación del trabajo

En el caso de las aplicaciones o los datos que deban instalarse para que se ejecute el trabajo, pero que no sea necesario instalarlos en todo el grupo, considere la posibilidad de utilizar [archivos de recursos de la tarea de preparación del trabajo](./batch-job-prep-release.md).

Por ejemplo, si su grupo tiene muchos tipos diferentes de trabajos y solo uno de ellos necesita un archivo .msi para ejecutarse, sería conveniente colocar el paso de instalación en una tarea de preparación del trabajo.

## <a name="task-resource-files"></a>Archivos de recursos de tareas

Los archivos de recursos de tareas son adecuados cuando la aplicación o los datos solo son pertinentes para una tarea.

Por ejemplo, podría tener cinco tareas, cada una de las cuales procese un archivo diferente y, luego, escribir la salida en el almacenamiento de blobs. En este caso, el archivo de entrada debe especificarse en la colección de archivos de recursos de tareas, ya que cada tarea tiene su propio archivo de entrada.

## <a name="additional-ways-to-get-data-onto-nodes"></a>Formas adicionales de introducir datos en nodos

Dado que tiene control sobre los nodos de Azure Batch y puede ejecutar archivos ejecutables personalizados, puede extraer datos de cualquier número de orígenes personalizados. Asegúrese de que el nodo de Batch tenga conectividad con el destino y de que posea credenciales para ese origen en el nodo.

Estos son algunos ejemplos de formas de transferir datos a los nodos de Batch:

- Descargar datos de SQL.
- Descargar datos de otros servicios web o de ubicaciones personalizadas.
- Asignar un recurso compartido de red.

## <a name="azure-storage"></a>Almacenamiento de Azure

Tenga en cuenta que el almacenamiento de blobs tiene destinos de escalabilidad de descarga. Los objetivos de escalabilidad de recursos compartidos de archivos de almacenamiento de Azure son los mismos que para un solo blob. El tamaño afectará al número de nodos y a los grupos que necesita.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a usar [paquetes de aplicación con Batch](batch-application-packages.md).
- Aprenda sobre cómo [trabajar con nodos y grupos](nodes-and-pools.md).
