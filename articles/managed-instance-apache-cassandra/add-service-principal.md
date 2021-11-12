---
title: Adición de una entidad de servicio de Cosmos DB para Azure Managed Instance for Apache Cassandra
description: Aprenda a agregar una entidad de servicio de Cosmos DB a una red virtual existente para Azure Managed Instance for Apache Cassandra
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: thvankra
ms.custom: ignite-fall-2021
ms.openlocfilehash: 06fd7f23db4b4df9b1153ac8b00afb35c265d0be
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858474"
---
# <a name="use-azure-portal-to-add-cosmos-db-service-principal"></a>Uso de Azure Portal para agregar una entidad de servicio de Cosmos DB

Para una implementación correcta en una red virtual existente, Azure Managed Instance for Apache Cassandra requiere la entidad de servicio de Azure Cosmos DB con un rol (como Colaborador de red) que permita la acción `Microsoft.Network/virtualNetworks/subnets/join/action`. En algunas circunstancias, puede ser necesario agregar estos permisos manualmente. En este artículo se muestra cómo hacerlo mediante Azure Portal. 

## <a name="add-cosmos-db-service-principal"></a>Adición de una entidad de servicio de Cosmos DB

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Vaya a la red virtual de destino en su suscripción, seleccione la pestaña de control de acceso y haga clic en `add role assignment`:

   :::image type="content" source="./media/add-service-principal/service-principal-1.png" alt-text="Agregar asignación de roles" lightbox="./media/add-service-principal/service-principal-1.png" border="true"::: 

1. Busque el rol `Network Contributor`, resáltelo y, a continuación, seleccione la pestaña `members`:

   :::image type="content" source="./media/add-service-principal/service-principal-2.png" alt-text="Add Network Contributor" lightbox="./media/add-service-principal/service-principal-2.png" border="true"::: (Agregar colaborador de red) 

   > [!NOTE]
   > No es necesario tener un rol con permisos tan amplios como Colaborador de red; este se usa como ejemplo para simplificar. También puede crear un rol de cliente con permisos más limitados, siempre y cuando permita la acción `Microsoft.Network/virtualNetworks/subnets/join/action`.

1. Asegúrese de que `User, group, or service principal` está seleccionado para `Assign access to` y, a continuación, haga clic en `Select members` para buscar la entidad de servicio `Azure Cosmos DB`. Selecciónela en la ventana de la derecha:

   :::image type="content" source="./media/add-service-principal/service-principal-3.png" alt-text="Selección de una entidad de servicio de Cosmos DB" lightbox="./media/add-service-principal/service-principal-3.png" border="true"::: 

1. Haga clic en la pestaña `Review + assign` de la parte superior y, a continuación, haga clic en el botón `Review + assign` de la parte inferior. Ahora la entidad de servicio de Cosmos DB debería estar asignada. 

   :::image type="content" source="./media/add-service-principal/service-principal-4.png" alt-text="Revisión y asignación" lightbox="./media/add-service-principal/service-principal-4.png" border="true"::: 

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a asignar la entidad de servicio de Azure Cosmos DB con un rol adecuado a una red virtual para permitir implementaciones administradas de Cassandra. Más información sobre Azure Managed Instance for Apache Cassandra en los siguientes artículos:

* [¿Qué es Azure Managed Instance for Apache Cassandra? (versión preliminar)](introduction.md)
* [Administración de recursos de Azure Managed Instance for Apache Cassandra mediante la CLI de Azure](manage-resources-cli.md)
