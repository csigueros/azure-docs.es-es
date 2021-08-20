---
title: 'Creación de una instancia de Apache Kafka para Confluent Cloud mediante la CLI de Azure: soluciones de partners de Azure'
description: En este artículo, se describe cómo usar la CLI de Azure para crear una instancia de Apache Kafka para Confluent Cloud.
ms.service: partner-services
ms.topic: quickstart
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: a9c49b1a19cbed080255492b90554ce0b138c3bd
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112535027"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-cli"></a>Inicio rápido: Introducción a Apache Kafka para Confluent Cloud mediante la CLI de Azure

En está guía de inicio rápido, usará Azure Marketplace y la CLI de Azure para crear una instancia de Apache Kafka para Confluent Cloud.

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

Empiece por preparar el entorno para la CLI de Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Después de iniciar sesión, use el comando [az confluent organization create](/cli/azure/confluent/organization#az_confluent_organization_create) para crear el nuevo recurso de organización:

```azurecli
az confluent organization create --name "myOrganization" --resource-group "myResourceGroup" \
 --location "my location" \ 
 --offer-detail id="string" plan-id="string" plan-name="string" publisher-id="string" term-unit="string" \ 
 --user-detail email-address="contoso@microsoft.com" first-name="string" last-name="string" \ 
 --tags Environment="Dev" 
```

> [!NOTE]
> Si desea que el comando devuelva el control antes de que finalice la operación de creación, agregue el parámetro opcional `--no-wait`. La operación continúa en ejecución hasta que se crea la organización de Confluent.
 
Para pausar la ejecución de la CLI hasta que se produzca un evento o condición específicos de una organización, use el comando [az confluent organization wait](/cli/azure/confluent/organization#az_confluent_organization_wait). Por ejemplo, para esperar hasta que se cree una organización:

```azurecli
az confluent organization wait --name "myOrganization" --resource-group "myResourceGroup" --created
```

Para ver una lista de las organizaciones existentes, use el comando [az confluent organization list](/cli/azure/confluent/organization#az_confluent_organization_list).

Puede ver todas las organizaciones de la suscripción:

```azurecli
az confluent organization list
```

O bien, ver las organizaciones de un grupo de recursos:

```azurecli
az confluent organization list --resource-group "myResourceGroup"
```

Para ver las propiedades de una organización específica, use el comando [az confluent organization show](/cli/azure/confluent/organization#az_confluent_organization_show).

Puede ver la organización por nombre:

```azurecli
az confluent organization show --name "myOrganization" --resource-group "myResourceGroup"
```

O bien, ver la organización por identificador de recurso:

```azurecli
az confluent organization show --ids "/subscriptions/{SubID}/resourceGroups/{myResourceGroup}/providers/Microsoft.Confluent/organizations/{myOrganization}"
```

Si recibe un error, consulte [Solución de problemas de las soluciones de Apache Kafka para Confluent Cloud](troubleshoot.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Administración del recurso de Confluent Cloud](manage.md)
