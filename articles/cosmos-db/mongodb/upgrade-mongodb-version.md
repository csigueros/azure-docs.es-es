---
title: Actualización de la versión de Mongo de la cuenta de API de Azure Cosmos DB para MongoDB
description: Procedimiento para actualizar sin problemas la versión del protocolo de conexión de MongoDB para las cuentas de API de Azure Cosmos DB para MongoDB existentes
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 751b5546313247b80d6e6701aa6945938cc40a50
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779911"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Actualización de la versión de API de la cuenta de API de Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

En este artículo se describe cómo actualizar la versión de API de la cuenta de API de Azure Cosmos DB para MongoDB. Después de la actualización, puede usar la funcionalidad más reciente de la API de Azure Cosmos DB para MongoDB. El proceso de actualización no interrumpe la disponibilidad de la cuenta y no consume RU/s ni reduce la capacidad de la base de datos en ningún momento. Este proceso no afectará a los índices o datos existentes. 

Cuando actualice a una nueva versión de la API, comience con cargas de trabajo de desarrollo y pruebas antes de actualizar las cargas de trabajo de producción. Es importante actualizar los clientes a una versión compatible con la versión de API a la que se va a actualizar antes de actualizar la cuenta de API de Azure Cosmos DB para MongoDB.

>[!Note]
> En este momento, solo las cuentas aptas que usan la versión 3.2 del servidor se pueden actualizar a la versión 3.6 o 4.0. Si la cuenta no muestra la opción de actualización, [abra una incidencia de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Actualización a 4.0 o 3.6

### <a name="benefits-of-upgrading-to-version-40"></a>Ventajas de la actualización a la versión 4.0

A continuación se describen las nuevas características incluidas en la versión 4.0:
- Compatibilidad con transacciones de varios documentos en colecciones no particionadas.
- Nuevos operadores de agregación
- Rendimiento de análisis mejorado
- Almacenamiento más rápido y más eficiente

### <a name="benefits-of-upgrading-to-version-36"></a>Ventajas de la actualización a la versión 3.6

A continuación se describen las nuevas características incluidas en la versión 3.6:
- Rendimiento y estabilidad mejorada
- Compatibilidad con los nuevos comandos de base de datos
- Compatibilidad con la canalización de agregación de forma predeterminada y nuevas fases de agregación
- Compatibilidad con flujos de cambios
- Compatibilidad con índices compuestos
- Compatibilidad entre particiones para las operaciones siguientes: actualización, eliminación, recuento y ordenación
- Rendimiento mejorado para las siguientes operaciones de agregado: $count, $skip, $limit y $group
- Ahora se admite la indexación con caracteres comodín

### <a name="changes-from-version-32"></a>Cambios con respecto a la versión 3.2

- De forma predeterminada, la característica de [reintentos de servidor (SSR)](prevent-rate-limiting-errors.md) está habilitada, por lo que las solicitudes de la aplicación cliente no devolverán 16 500 errores. En su lugar, las solicitudes se reanudarán hasta que finalicen o cumplan el tiempo de espera de 60 segundos.
- El tiempo de espera de cada solicitud se establece en 60 segundos.
- Las colecciones de MongoDB creadas con la nueva versión del protocolo de conexión solo tendrán la propiedad `_id` indexada de forma predeterminada.

### <a name="action-required-when-upgrading-from-32"></a>Acción obligatoria al actualizar desde la versión 3.2

Para la actualización desde la versión 3.2, el sufijo del punto de conexión de la cuenta de base de datos se actualizará al formato siguiente:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Si está actualizando desde la versión 3.2, tendrá que reemplazar el punto de conexión existente en las aplicaciones y los controladores que se conectan con esta cuenta de base de datos. **Solo las conexiones que usan el nuevo punto de conexión tendrán acceso a las características de la nueva versión de API**. El punto de conexión 3.2 anterior debe tener el sufijo `.documents.azure.com`.

>[!Note]
> Es posible que este punto de conexión tenga ligeras diferencias si la cuenta se ha creado en una nube de Azure soberana, gubernamental o restringida.

## <a name="how-to-upgrade"></a>Procedimiento de actualización

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Vaya a la cuenta de Azure Cosmos DB API para MongoDB. Abra el panel de **Información general** y compruebe que la **versión actual del Servidor** sea 3.2 o 3.6.

    :::image type="content" source="./media/upgrade-mongodb-version/check-current-version.png" alt-text="Compruebe la versión actual de la cuenta de MongoDB desde el Azure Portal." border="true":::

1. En el menú de la izquierda, abra el panel `Features`. Este panel muestra las características de nivel de cuenta que están disponibles para su cuenta de base de datos.

1. Seleccione la fila `Upgrade MongoDB server version`. Si no ve esta opción, es posible que la cuenta no sea válida para esta actualización. En ese caso, abra una [incidencia de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

    :::image type="content" source="./media/upgrade-mongodb-version/upgrade-server-version.png" alt-text="Abra la hoja Características y actualice su cuenta." border="true":::

1. Consulte la información que se muestra sobre esta actualización específica. Seleccione `Set server version to 4.0` (o 3.6 según la versión actual).

    :::image type="content" source="./media/upgrade-mongodb-version/select-upgrade.png" alt-text="Revise la guía de actualización y seleccione Actualizar." border="true":::

1. Después de iniciar la actualización, el menú de **Características** aparece atenuado y el estado se establece en *Pendiente*. Esta actualización tarda unos 15 minutos en completarse. Este proceso no afectará a la funcionalidad u operaciones existentes de la cuenta de base de datos. Una vez que haya finalizado, el estado de la **versión de actualización del servidor MongoDB** mostrará la versión actualizada. Póngase en contacto con el [soporte técnico](https://azure.microsoft.com/support/create-ticket/) si se produce alguna incidencia al procesar la solicitud.

1. A continuación se indican algunas consideraciones después de actualizar la cuenta:

    1. Si ha realizado la actualización desde la versión 3.2, vuelva al panel **Información general** y copie la nueva cadena de conexión que va a usar en la aplicación. No se interrumpirá la cadena de conexión anterior que ejecuta la versión 3.2. Para garantizar una experiencia coherente, todas las aplicaciones deben usar el nuevo punto de conexión.

    1. Si ha actualizado desde la versión 3.6, la cadena de conexión existente se actualizará a la versión especificada y debe seguir utilizándose.

## <a name="how-to-downgrade"></a>Cambio a una versión anterior

También puede cambiar a una versión anterior la cuenta desde la versión 4.0 a la 3.6 mediante los mismos pasos de la sección "Procedimiento de actualización".

Si ha actualizado de la versión 3.2 a la (4.0 o 3.6) y desea cambiar de nuevo a la versión 3.2, simplemente puede volver a usar la cadena de conexión anterior (3.2) con el host `accountname.documents.azure.com`, que permanece activo después de la actualización que ejecuta la versión 3.2.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las [características admitidas y no admitidas de la versión 4.0 de MongoDB](feature-support-40.md).
- Obtenga información sobre las [características admitidas y no admitidas de la versión 3.6 de MongoDB](feature-support-36.md).
- Para más información, consulte las [características de la versión 3.6 de Mongo](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
