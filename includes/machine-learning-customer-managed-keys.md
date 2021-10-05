---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 09/14/2021
ms.author: larryfr
ms.openlocfilehash: 757179959035c6bfce77b1feaaf5bfeff5aab001
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621606"
---
> [!IMPORTANT]
> La instancia de Cosmos DB se crea en un grupo de recursos administrados por Microsoft en __su suscripción__. Los siguientes servicios también se crean en este grupo de recursos y se usan en la configuración de claves administradas por el cliente:
> * Cuenta de Azure Storage
> * Azure Search
>
> Puesto que estos servicios se crean en la suscripción de Azure, se le cobrará por estas instancias de servicio. Si su suscripción no tiene suficiente cuota para el servicio Azure Cosmos DB, se producirá un error. Para obtener más información sobre las cuotas, consulte [Cuotas de servicio de Azure Cosmos DB](/azure/cosmos-db/concepts-limits).
>
> El grupo de recursos administrado se denomina con el formato `<AML Workspace Resource Group Name><GUID>`. Si el área de trabajo de Azure Machine Learning usa un punto de conexión privado, también se crea una red virtual en este grupo de recursos. Esta red virtual se usa para proteger la comunicación entre los servicios de este grupo de recursos y el área de trabajo de Azure Machine Learning.
> 
> * __No elimine el grupo de recursos__ que contiene esta instancia de Cosmos DB, ni ninguno de los recursos que se crean de forma automática en este grupo. Si necesita eliminar el grupo de recursos, la instancia de Cosmos DB, etc., primero debe eliminar el área de trabajo de Azure Machine Learning que la usa. El grupo de recursos, la instancia de Cosmos DB y los otros recursos que se crean automáticamente se eliminan cuando se elimina el área de trabajo asociada.
> * Las [__unidades de solicitud__](../articles/cosmos-db/request-units.md) que usa esta cuenta de Cosmos DB se escalan automáticamente según sea necesario. El valor __mínimo__ de RU es __1200__. El valor __máximo__ de RU es __12000__.
> * No __puede proporcionar una red virtual propia para usarla con la instancia de Cosmos DB__ que se crea. Tampoco __puede modificar la red virtual__. Por ejemplo, no puede cambiar el rango de direcciones IP que usa.
> 
> Para calcular el costo adicional de la instancia de Azure Cosmos DB, use la [calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).