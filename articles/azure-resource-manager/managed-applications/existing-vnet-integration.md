---
title: Implementación en una red virtual existente
description: Aquí se explica cómo habilitar usuarios de la aplicación administrada para seleccionar una red virtual existente. La red virtual puede estar fuera de la aplicación administrada.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: 0f35e12b62b0efd9f79f83a1832c317d7eabe99b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648342"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Uso de una red virtual existente con Azure Managed Applications

En este artículo se muestra cómo definir una aplicación administrada de Azure que se integra con una red virtual existente en la suscripción del cliente. La aplicación administrada permite al cliente decidir si quiere crear una nueva red virtual o usar una existente. La red virtual existente puede estar fuera del grupo de recursos administrado.

## <a name="main-template"></a>Plantilla principal

En primer lugar, vamos a echar un vistazo al archivo _mainTemplate.json_. Toda la plantilla para implementar una máquina virtual y sus recursos asociados se muestra a continuación. Luego, vamos a examinar más detalladamente los elementos de la plantilla que están relacionadas con el uso de una red virtual existente.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

Observe que la red virtual se [implementa de forma condicional](../templates/conditional-resource-deployment.md). El cliente pasa un valor de parámetro que indica si se va a crear una red virtual nueva o se va a usar una existente. Si el cliente selecciona una red virtual nueva, se implementa el recurso. De lo contrario, el recurso se omite durante la implementación.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

La variable del identificador de red virtual tiene dos propiedades. Una propiedad devuelve el identificador de recurso cuando se implementa una nueva red virtual. La otra devuelve el identificador de recurso cuando se usa una red virtual existente. El identificador de recurso de la red virtual existente incluye el nombre del grupo de recursos que contiene la red virtual.

El identificador de subred se construye a partir del valor del identificador de red virtual. Usa el valor que coincide con la selección de los clientes.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

La interfaz de red se establece en la variable del identificador de subred.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>Definición de la interfaz de usuario

Ahora vamos a echar un vistazo al archivo _createUiDefinition.json_. El archivo completo es:

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

El archivo incluye un elemento de red virtual.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

Ese elemento permite al cliente seleccionar una red virtual nueva o existente.

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="Red virtual nueva o existente":::

En las salidas, se incluye un valor que indica si el cliente ha seleccionado una red virtual nueva o existente. También hay un valor de identidad administrada.

> [!NOTE]
> El valor de salida de la identidad administrada debe tener el nombre **managedIdentity**.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo crear el archivo de definición de la interfaz de usuario, vea [CreateUiDefinition.json para la experiencia de creación de aplicaciones administradas de Azure](create-uidefinition-overview.md).
