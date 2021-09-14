---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/03/2021
ms.author: tomfitz
ms.openlocfilehash: 6a7c9b230ffa42a539dd41cc9301915d7dbe3a34
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479166"
---
## <a name="choose-the-right-export-option"></a>Elección de la opción de exportación correcta

Hay dos maneras de exportar una plantilla:

* **Exportar desde el grupo de recursos o el recurso**. Esta opción genera una plantilla nueva a partir de los recursos existentes. La plantilla exportada es una "instantánea" del estado actual del grupo de recursos. Puede exportar un grupo de recursos completo o recursos específicos dentro de ese grupo de recursos.

* **Guardar desde el historial**. Esta opción recupera una copia exacta de una plantilla usada para la implementación. Especifique la implementación a partir del historial de implementación. 

Según la opción que elija, las plantillas exportadas tendrán distintas calidades.

| Desde el grupo de recursos o el recurso | Desde el historial |
| --------------------- | ----------------- |
| La plantilla se genera automáticamente. Es probable que quiera mejorar el código antes de implementarlo. | La plantilla es una copia exacta de la creada por el autor de la plantilla. Está lista para volver a implementarse sin ningún cambio. |
| La plantilla es una instantánea del estado actual de los recursos. Incluye todos los cambios manuales que se hayan realizado después de la implementación. | En la plantilla solo se muestra el estado de los recursos en el momento de la implementación. No se incluyen los cambios manuales que haya realizado después de la implementación. |
| Puede seleccionar los recursos de un grupo de recursos que se van a exportar. | Se incluyen todos los recursos para una implementación específica. No se puede seleccionar un subconjunto de esos recursos o agregar recursos que se hayan agregado en otro momento. |
| En la plantilla se incluyen todas las propiedades de los recursos, incluidas algunas que normalmente no se establecerían durante la implementación. Es posible que quiera quitar o borrar estas propiedades antes de volver a usar la plantilla. | En la plantilla solo se incluyen las propiedades necesarias para la implementación. La plantilla es más dispersa y fácil de leer. |
| Probablemente en la plantilla no se incluyan todos los parámetros que necesita para su reutilización. La mayoría de los valores de propiedad están codificados de forma rígida en la plantilla. Para volver a implementar la plantilla en otros entornos, tendrá que agregar parámetros que aumenten la capacidad de configurar los recursos.  También puede anular la selección de **Incluir parámetros** y así podrá crear sus propios parámetros. | En la plantilla se incluyen parámetros que facilitan la tarea de volver a implementar en otros entornos. |

Exporte la plantilla desde un grupo de recursos o un recurso cuando:

* Tenga que capturar cambios de los recursos que se han realizado después de la implementación original.
* Quiera seleccionar los recursos que se van a exportar.
* Los recursos no se crearon con una plantilla.

Exporte la plantilla desde el historial cuando:

* Quiera una plantilla fácil de reutilizar.
* No necesite incluir los cambios realizados después de la implementación original.
