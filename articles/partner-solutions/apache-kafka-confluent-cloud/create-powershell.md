---
title: 'Creación de una instancia de Apache Kafka para Confluent Cloud mediante Azure PowerShell: Soluciones para asociados de Azure'
description: En este artículo se describe cómo usar Azure PowerShell para crear una instancia de Apache Kafka para Confluent Cloud.
ms.service: partner-services
ms.topic: quickstart
ms.date: 11/03/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 696e5138d80b1ba799f96fe60da3be0f4f1d2548
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485884"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-powershell"></a>Inicio rápido: Introducción a Apache Kafka para Confluent Cloud mediante Azure PowerShell

En este inicio rápido, usará Azure Marketplace y Azure PowerShell para crear una instancia de Apache Kafka para Confluent Cloud.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure. Si no tiene una suscripción de Azure activa, cree una [cuenta gratuita](https://azure.microsoft.com/free/).
- Debe tener el rol _Propietario_ o _Colaborador_ para la suscripción de Azure. La integración entre Azure y Confluent solo la pueden configurar los usuarios con acceso de _Propietario_ o _Colaborador_. Antes de comenzar, [confirme que tiene el acceso adecuado](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Búsqueda de la oferta

Use Azure Portal para buscar la aplicación Apache Kafka on Confluent Cloud.

1. En un explorador web, vaya a [Azure Portal](https://portal.azure.com/) e inicie sesión.

1. Si ha visitado el **Marketplace** en una sesión reciente, seleccione el icono entre las opciones disponibles. En caso contrario, busque _Marketplace_.

    :::image type="content" source="media/marketplace.png" alt-text="Icono de Marketplace.":::

1. En la página **Marketplace**, tiene dos opciones según el tipo de plan que desee. Puede registrarse para obtener un plan de pago por uso o un plan de compromiso. El plan de pago por uso está disponible públicamente. El plan de compromiso está disponible para los clientes que han sido aprobados para una oferta privada.

   - En el caso de los clientes de **pago por uso**, busque _Apache Kafka on Confluent Cloud_. Seleccione la oferta de Apache Kafka on Confluent Cloud.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Búsqueda de la oferta de Azure Marketplace.":::

   - Para los clientes con un plan de **compromiso**, seleccione el vínculo **View private offers** (Ver ofertas privadas). El compromiso requiere que se registre para una cantidad mínima de gastos. Utilice esta opción solo si sabe que necesita el servicio durante un tiempo prolongado.

     :::image type="content" source="media/view-private-offers.png" alt-text="Ver ofertas privadas.":::

     Busque _Apache Kafka on Confluent Cloud_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="Selección de la oferta privada.":::

## <a name="create-resource"></a>Crear el recurso

Después de seleccionar la oferta de Apache Kafka on Confluent Cloud, está listo para configurar la aplicación.

Empiece por preparar el entorno para Azure PowerShell:

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> Aunque el módulo **Az.Confluent** de PowerShell está en versión preliminar, se debe instalar por separado mediante el cmdlet `Install-Module`.

```azurepowershell
Install-Module -Name Az.Confluent -Scope CurrentUser -Repository PSGallery -Force
```

Después de iniciar sesión, use el cmdlet [New-AzConfluentOrganization](/powershell/module/az.confluent/new-azconfluentorganization) para crear el recurso de la organización:

```azurepowershell
$ConfluentOrgParams = @{
    Name = 'myOrganization'
    ResourceGroupName = 'myResourceGroup'
    Location = 'my location'
    OfferDetailId = 'string'
    OfferDetailPlanId = 'string'
    OfferDetailPlanName = 'string'
    OfferDetailPublisherId = 'string'
    OfferDetailTermUnit = 'string'
    UserDetailEmailAddress = 'contoso@microsoft.com'
    UserDetailFirstName = 'string'
    UserDetailLastName = 'string'
    Tag = @{Environment='Dev'}
}

New-AzConfluentOrganization @ConfluentOrgParams
```

> [!NOTE]
> Si desea que el comando devuelva el control antes de que finalice la operación de creación, agregue el parámetro opcional `-NoWait`. La operación continúa en ejecución hasta que se crea la organización de Confluent.

Para ver una lista de las organizaciones existentes, use el cmdlet [Get-AzConfluentOrganization](/powershell/module/az.confluent/get-azconfluentorganization).

Puede ver todas las organizaciones de la suscripción:

```azurepowershell
Get-AzConfluentOrganization
```

O bien, ver las organizaciones de un grupo de recursos:

```azurepowershell
Get-AzConfluentOrganization -ResourceGroupName myResourceGroup
```

Para ver las propiedades de una organización concreta, use el cmdlet `Get-AzConfluentOrganization` con los parámetros `Name` y `ResourceGroupName`.

Puede ver la organización por nombre:

```azurepowershell
Get-AzConfluentOrganization -Name myOrganization -ResourceGroupName myResourceGroup
```

Si recibe un error, consulte [Solución de problemas de las soluciones de Apache Kafka para Confluent Cloud](troubleshoot.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Administración del recurso de Confluent Cloud](manage.md)
