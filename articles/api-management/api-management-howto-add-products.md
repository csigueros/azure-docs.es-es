---
title: 'Tutorial: Creación y publicación de un producto en Azure API Management'
description: En este tutorial, va a crear y publicar un producto en Azure API Management. Una vez publicado, los desarrolladores pueden empezar a usar sus API.
author: dlepow
ms.service: api-management
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: danlep
ms.openlocfilehash: f9d4b1565cc217aaa149e34fc278da779a3d869e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643334"
---
# <a name="tutorial-create-and-publish-a-product"></a>Tutorial: Crear y publicar un producto  

En Azure API Management, un [*producto*](api-management-terminology.md#term-definitions) contiene una o varias API, así como una cuota de uso y los términos de uso. Una vez publicado un producto, los desarrolladores pueden suscribirse al producto y comenzar a usar las API del producto.  

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear y publicar un producto
> * Agregar una API al producto

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="Productos de API Management en el portal":::


## <a name="prerequisites"></a>Requisitos previos

+ Conocer la [terminología de API Management de Azure](api-management-terminology.md).
+ Complete el siguiente inicio rápido: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ Además, realice el siguiente tutorial: [Importación y publicación de la primera API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Crear y publicar un producto

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en Azure Portal y vaya a la instancia de API Management.
1. En el panel de navegación izquierdo, seleccione **Productos** >  **+ Agregar**.
1.  En la ventana **Agregar producto**, escriba los valores que se describen en la tabla siguiente para crear el producto.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="Adición de un producto en el portal":::

    | Nombre                     | Descripción                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nombre para mostrar             | El nombre que desea que aparezca en el [portal para desarrolladores](api-management-howto-developer-portal.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Descripción              | Proporcione información sobre el producto, como su finalidad, las API a las que proporciona acceso y otros detalles.                                                                                                                                               |
    | State                    | Seleccione **Publicado** si quiere publicar el producto. Para poder llamar a las API de un producto, este debe publicarse. De forma predeterminada, los nuevos productos no se publican y solo son visibles para el grupo **Administradores**.                                                                                      |
    | Requiere suscripción    | Seleccione si un usuario debe suscribirse para usar el producto.                                                                                                                                                                                                                                   |
    | Requiere aprobación        | Seleccione si quiere que un administrador revise y acepte o rechace los intentos de suscripción a este producto. Si no se selecciona, los intentos de suscripción se aprueban automáticamente.                                                                                                                         |
    | Límite de recuento de suscripciones | Tiene la opción de limitar el número de varias suscripciones simultáneas.                                                                                                                                                                                                                                |
    | Términos legales              | Puede incluir los términos de uso del producto que deben aceptar los suscriptores para usarlo.                                                                                                                                                                                                             |
    | API existentes                     | Seleccione una o varias API. También puede agregar las API después de crear el producto. Para más información, consulte [Incorporación de las API a un producto](#add-apis-to-a-product) más adelante en este artículo. |

3. Seleccione **Crear** para crear el producto.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para empezar a usar la CLI de Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Para crear un producto, ejecute el comando [az apim product create](/cli/azure/apim/product#az_apim_product_create):

```azurecli
az apim product create --resource-group apim-hello-word-resource-group \
    --product-name "Contoso product" --product-id contoso-product \
    --service-name apim-hello-world --subscription-required true \
    --state published --description "This is a test."
```

Puede especificar varios valores para el producto:

   | Parámetro | Descripción |
   |-----------|-------------|
   | `--product-name` | El nombre que desea que aparezca en el [portal para desarrolladores](api-management-howto-developer-portal.md). |
   | `--description`  | Proporcione información sobre el producto, como su finalidad, las API a las que proporciona acceso y otros detalles. |
   | `--state`        | Seleccione **Publicado** si quiere publicar el producto. Para poder llamar a las API de un producto, este debe publicarse. De forma predeterminada, los nuevos productos no se publican y solo son visibles para el grupo **Administradores**. |
   | `--subscription-required` | Seleccione si un usuario debe suscribirse para usar el producto. |
   | `--approval-required` | Seleccione si quiere que un administrador revise y acepte o rechace los intentos de suscripción a este producto. Si no se selecciona, los intentos de suscripción se aprueban automáticamente. |
   | `--subscriptions-limit` | Tiene la opción de limitar el número de varias suscripciones simultáneas.|
   | `--legal-terms`         | Puede incluir los términos de uso del producto que deben aceptar los suscriptores para usarlo. |

Para ver los productos actuales, utilice el comando [az apim product list](/cli/azure/apim/product#az_apim_product_list):

```azurecli
az apim product list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Puede eliminar un producto mediante el comando [az apim product delete](/cli/azure/apim/product#az_apim_product_delete):

```azurecli
az apim product delete --product-id contoso-product \
    --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --delete-subscriptions true
```

---

### <a name="add-more-configurations"></a>Adición de más configuraciones

Siga configurando el producto después de guardarlo. En la instancia de API Management, seleccione el producto en la ventana **Productos**. Agregue o actualice:

|Elemento   |Descripción  |
|---------|---------|
|Configuración     |    Los metadatos y el estado del producto     |
|API existentes     |  Las API asociadas al producto.       |
|[Directivas](api-management-howto-policies.md)     |  Las directivas aplicadas a las API de producto      |
|Control de acceso     |  La visibilidad del producto para desarrolladores o invitados       |
|[Suscripciones](api-management-subscriptions.md)    |    Los suscriptores a los productos     |

## <a name="add-apis-to-a-product"></a>Incorporación de API a un producto

Los productos son asociaciones de una o varias API. Puede incluir varias API y ofrecerlas a los desarrolladores mediante el portal para desarrolladores. Durante la creación del producto, puede agregar una o varias API existentes. También puede agregar las API al producto más adelante, bien desde la página **Configuración** de los productos o durante la creación de una API.

En primer lugar, los desarrolladores deben suscribirse a un producto para acceder a la API. Al suscribirse, obtienen una clave de suscripción que funciona con cualquier API de ese producto. Si creó la instancia de APIM, ya es un administrador, así que, de forma predeterminada, está suscrito a todos los productos.

### <a name="add-an-api-to-an-existing-product"></a>Adición de una API a un producto existente

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. En el panel de navegación izquierdo de la instancia de API Management, seleccione **Productos**.
1. Seleccione un producto y, luego, elija **API**.
1. Seleccione **+Agregar**.
1. Elija una o varias API y, luego, elija **Seleccionar**.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="Incorporación de una API a un producto existente":::

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

1. Para ver las API administradas, utilice el comando [az apim api list](/cli/azure/apim/api#az_apim_api_list):

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

1. Para agregar una API al producto, ejecute el comando [az apim product api add](/cli/azure/apim/product/api#az_apim_product_api_add):

   ```azurecli
   az apim product api add --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --product-id contoso-product \
       --service-name apim-hello-world
   ```

1. Compruebe la adición con el comando [az apim product api list](/cli/azure/apim/product/api#az_apim_product_api_list):

   ```azurecli
   az apim product api list --resource-group apim-hello-word-resource-group \
       --product-id contoso-product --service-name apim-hello-world --output table
   ```

Puede eliminar una API de un producto con el comando [az apim product api delete](/cli/azure/apim/product/api#az_apim_product_api_delete):

```azurecli
az apim product api delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --product-id contoso-product \
    --service-name apim-hello-world
```

---

> [!TIP]
> Puede crear o actualizar la suscripción del usuario a un producto con claves de suscripción personalizadas mediante la [API REST](/rest/api/apimanagement/2020-12-01/subscription/create-or-update) o un comando de PowerShell.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Crear y publicar un producto
> * Agregar una API al producto

Avance hasta el siguiente tutorial:

> [!div class="nextstepaction"]
> [Create blank API and mock API responses](mock-api-responses.md) (Creación de una API en blanco y simulación de respuestas de API)
