---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/31/2021
ms.author: tomfitz
ms.openlocfilehash: 76c1ed0a2a1b1a2cca77988d218e2460f99003e6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436222"
---
## <a name="limitations"></a>Limitaciones

Al realizar una exportación de un grupo de recursos o un recurso, la plantilla exportada se genera a partir de los [esquemas publicados](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) para cada tipo de recurso. En ocasiones, el esquema no tiene la versión más reciente de un tipo de recurso. Compruebe la plantilla exportada para asegurarse de que incluye las propiedades necesarias. Si es preciso, edite la plantilla exportada para usar la versión de API que necesite.

La característica de exportación de plantillas no admite la exportación de recursos de Azure Data Factory. Para más información sobre cómo exportar recursos de Data Factory, consulte [Copia o clonación de una factoría de datos en Azure Data Factory](../articles/data-factory/copy-clone-data-factory.md).

Para exportar los recursos creados mediante el modelo de implementación clásica, debe [migrarlos al modelo de implementación de Resource Manager](../articles/virtual-machines/migration-classic-resource-manager-overview.md).

Si recibe una advertencia al exportar una plantilla que indica que un tipo de recurso no se ha exportado, aún puede detectar las propiedades de ese recurso. Para obtener las propiedades de los recursos, vea [Referencia de plantillas](/azure/templates). También puede consultar la [API de REST de Azure](/rest/api/azure/) para el tipo de recurso.

Hay un límite de 200 recursos en el grupo de recursos para el que se crea la plantilla exportada. Si intenta exportar un grupo de recursos con más de 200 recursos, se muestra el mensaje de error `Export template is not supported for resource groups more than 200 resources`.
