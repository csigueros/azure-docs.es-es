---
title: Limitación del rendimiento total aprovisionado en la cuenta de Azure Cosmos DB
description: Aprenda a limitar el rendimiento total aprovisionado en la cuenta de Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/04/2021
ms.author: thweiss
ms.custom: ignite-fall-2021
ms.openlocfilehash: b8812e0d61485a4fd5c7ceb7a2f57d47b90bfeca
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090861"
---
# <a name="limit-the-total-throughput-provisioned-on-your-azure-cosmos-db-account"></a>Limitación del rendimiento total aprovisionado en la cuenta de Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Cuando se usa una cuenta de Azure Cosmos DB en modo de [rendimiento aprovisionado](./set-throughput.md), la mayoría de los costos normalmente proceden de la cantidad de rendimiento que se ha aprovisionado en la cuenta. En concreto, estos costos se ven directamente influidos por:

- El número de bases de datos con rendimiento compartido.
- El número de contenedores con rendimiento dedicado.
- La cantidad de rendimiento aprovisionado en cada uno de estos recursos.
- El número de regiones donde la cuenta está disponible.

Puede ser difícil realizar un seguimiento de la cantidad total de rendimiento que se ha aprovisionado en la cuenta, especialmente cuando se empieza a usar Azure Cosmos DB. Esto puede dar lugar a cargos inesperados si esta cantidad acaba superando un presupuesto determinado que no se esperaba superar. Para ayudar a controlar mejor los costos, Azure Cosmos DB permite limitar el rendimiento total aprovisionado en la cuenta.

> [!NOTE]
> Esta característica no está disponible en cuentas [sin servidor](./serverless.md).

Después de establecer un límite en el rendimiento total de la cuenta, cualquiera de las siguientes operaciones que tenga como resultado la superación de este límite se bloquea y genera un error de manera explícita:

- Creación de una nueva base de datos con rendimiento compartido.
- Creación de un nuevo contenedor con rendimiento dedicado.
- Aumento del rendimiento aprovisionado en un recurso configurado en modo estándar (manual).
- Aumento del rendimiento máximo aprovisionado en un recurso configurado en modo de escalabilidad automática.
- Incorporación de una nueva región a la cuenta.

> [!NOTE]
> En el caso de los recursos configurados en modo de escalabilidad automática, es el rendimiento máximo configurado en el recurso el que cuenta para el rendimiento total de la cuenta.

## <a name="set-the-total-throughput-limit-from-the-azure-portal"></a>Establecimiento del límite de rendimiento total desde Azure Portal

### <a name="new-account"></a>Nueva cuenta

Al crear una nueva cuenta de Azure Cosmos DB desde el portal, tiene la opción de limitar el rendimiento total de la cuenta:

:::image type="content" source="./media/limit-total-account-throughput/create-account.png" alt-text="Captura de pantalla de Azure Portal que muestra cómo limitar el rendimiento total de la cuenta al crear una nueva" border="true":::

Al activar esta opción se limita el rendimiento total de la cuenta a 4000 RU/s. Puede cambiar este valor una vez creada la cuenta.

### <a name="existing-account"></a>Cuenta existente

En Azure Portal, vaya a la cuenta de Azure Cosmos DB y seleccione **Administración de costos** en el menú de la izquierda.

:::image type="content" source="./media/limit-total-account-throughput/existing-account.png" alt-text="Captura de pantalla de Azure Portal que muestra cómo actualizar el rendimiento total de la cuenta en una cuenta existente" border="true":::

En esta sección se muestra un resumen del rendimiento total aprovisionado en la cuenta y se permite configurar el límite de rendimiento total. Están disponibles las tres opciones siguientes:

- **Limitar el rendimiento total aprovisionado de la cuenta a la cantidad incluida en el descuento por nivel gratuito**. Esta opción solo está disponible en las cuentas de nivel gratuito y limita el rendimiento total de la cuenta a 1000 RU/s. Al activar esta opción se asegura de no incurrir en ningún cargo por el rendimiento aprovisionado.
- **Permitir que el rendimiento total de la cuenta se aprovisione hasta una cantidad personalizada**. Esta opción permite especificar el rendimiento total aprovisionado que no se quiere superar. Como referencia se muestra una estimación de costos mensual correspondiente a la entrada.
  > [!NOTE]
  > Este límite personalizado no puede ser inferior al rendimiento total aprovisionado actualmente en toda la cuenta.
- **Sin límite, permitir que el rendimiento total de la cuenta se aprovisione hasta cualquier cantidad**. Esta opción deshabilita el límite.

## <a name="set-the-total-throughput-limit-programmatically"></a>Establecimiento del límite de rendimiento total mediante programación

### <a name="using-azure-resource-manager-templates"></a>Uso de plantillas del Administrador de recursos de Azure

Al crear o actualizar la cuenta de Azure Cosmos DB con Azure Resource Manager, puede configurar el límite de rendimiento total si establece la propiedad `properties.capacity.totalThroughputLimit`:

```json
{
  "location": "West US",
  "kind": "DocumentDB",
  "properties": {
    "locations": [
      {
        "locationName": "West US",
        "failoverPriority": 0,
        "isZoneRedundant": false
      }
    ],
    "databaseAccountOfferType": "Standard",
    "capacity": {
        "totalThroughputLimit": 2000
    }
  }
}
```

Establezca esta propiedad en `-1` para deshabilitar el límite.

## <a name="next-steps"></a>Pasos siguientes

- Introducción al [Planeamiento y administración de los costos](./plan-manage-costs.md) de Azure Cosmos DB.
- Más información sobre el [rendimiento aprovisionado](./set-throughput.md).
- Más información sobre la [Optimización del costo de rendimiento aprovisionado](./optimize-cost-throughput.md).
