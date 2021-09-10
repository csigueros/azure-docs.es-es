---
title: 'Creación de un vínculo privado para administrar recursos: Azure Portal'
description: Use Azure Portal para crear un vínculo privado para administrar recursos.
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: efe1fdb47bf550b996f4f44eed9f2d41f2a1083d
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227320"
---
# <a name="use-portal-to-create-private-link-for-managing-azure-resources"></a>Uso del portal para crear un vínculo privado para administrar recursos

En este artículo se explica cómo usar [Azure Private Link](../../private-link/index.yml) para restringir el acceso a fin de administrar los recursos de las suscripciones. En él se muestra el uso de Azure Portal para configurar la administración de los recursos por medio del acceso privado.

[!INCLUDE [Create content](../../../includes/resource-manager-create-rmpl.md)]

## <a name="create-resource-management-private-link"></a>Creación de un vínculo privado de administración de recursos

Al crear un vínculo privado de administración de recursos, la asociación del vínculo privado se crea automáticamente.

1. En el [portal](https://portal.azure.com), busque la opción **Vínculos privados de administración de recursos** y selecciónela entre las disponibles.

   :::image type="content" source="./media/create-private-link-access-portal/search.png" alt-text="Búsqueda de Vínculos privados de administración de recursos":::

1. Si la suscripción aún no tiene vínculos privados de administración de recursos, aparece una página en blanco. Seleccione **Crear vínculo privado de administración de recursos**.

   :::image type="content" source="./media/create-private-link-access-portal/start-create.png" alt-text="Selección de Crear vínculo privado de administración de recursos":::

1. Proporcione valores para el nuevo vínculo privado de administración de recursos. Se usa el grupo de administración raíz del directorio seleccionado para el nuevo recurso. Seleccione **Revisar + crear**.

   :::image type="content" source="./media/create-private-link-access-portal/provide-values.png" alt-text="Especificación de valores para vínculos privados de administración de recursos":::

1. Una vez pasada la validación, seleccione **Crear**.

## <a name="create-private-endpoint"></a>Creación de un punto de conexión privado

Ahora cree un punto de conexión privado que haga referencia al vínculo privado de administración de recursos.

1. Vaya a **Private Link Center**. Seleccione **Create private link** (Crear vínculo privado).

   :::image type="content" source="./media/create-private-link-access-portal/private-link-center.png" alt-text="Selección de Private Link Center":::

1. En la pestaña **Aspectos básicos**, proporcione valores para el punto de conexión privado.

   :::image type="content" source="./media/create-private-link-access-portal/private-endpoint-basics.png" alt-text="Especificación de valores para los aspectos básicos":::

1. Seleccione **Conectarse a un recurso de Azure en mi directorio** en la pestaña **Recurso**. En el tipo de recurso, seleccione **Microsoft.Authorization/resourceManagementPrivateLinks**. En el subrecurso de destino, seleccione **ResourceManagement**.

   :::image type="content" source="./media/create-private-link-access-portal/private-endpoint-resource.png" alt-text="Especificación de valores para el recurso":::

1. En la pestaña **Configuración**, seleccione la red virtual. Se recomienda integrar con una zona DNS privada. Seleccione **Revisar + crear**.

1. Una vez pasada la validación, seleccione **Crear**.

## <a name="verify-private-dns-zone"></a>Verificación de una zona DNS privada

Para asegurarse de que el entorno se ha configurado correctamente, compruebe la dirección IP local de la zona DNS.

1. En el grupo de recursos donde ha implementado el punto de conexión privado, seleccione el recurso de zona DNS privada denominado **privatelink.azure.com**.

1. Compruebe que el conjunto de registros denominado **management** tiene una dirección IP local válida.

   :::image type="content" source="./media/create-private-link-access-portal/verify.png" alt-text="Comprobación de la dirección IP local":::

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre los vínculos privados, vea [Azure Private Link](../../private-link/index.yml).