---
title: Habilitación del acceso privado con Private Link (versión preliminar)
titleSuffix: Azure Digital Twins
description: Descubra cómo habilitar el acceso privado para soluciones de Azure Digital Twins con Private Link.
author: baanders
ms.author: baanders
ms.date: 7/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 93435355a2d75a2346d076c44b377726789431a3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452341"
---
# <a name="enable-private-access-with-private-link-preview"></a>Habilitación del acceso privado con Private Link (versión preliminar)

En este artículo se describen las distintas formas de [habilitar Private Link con un punto de conexión privado para una instancia de Azure Digital Twins](concepts-security.md#private-network-access-with-azure-private-link-preview) (actualmente en versión preliminar). La configuración de un punto de conexión privado para la instancia de Azure Digital Twins le permite protegerla y eliminar la exposición pública, además de evitar la filtración de datos desde la instancia de [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

Estos son los pasos que se describen en este artículo: 
1. Activación de Private Link y configuración de un punto de conexión privado para una instancia de Azure Digital Twins.
1. Visualización, edición o eliminación de un punto de conexión privado de una instancia.
1. Deshabilitación o habilitación de marcas de acceso a la red pública para restringir el acceso de la API únicamente a las conexiones de Private Link.

## <a name="prerequisites"></a>Requisitos previos

Para poder configurar un punto de conexión privado, necesitará una [red virtual de Azure (VNet)](../virtual-network/virtual-networks-overview.md)  en la que se pueda implementar el punto de conexión. Si aún no tiene una red virtual, puede seguir uno de los [inicios rápidos de Azure Virtual Network](../virtual-network/quick-create-portal.md) para configurarla.

## <a name="add-a-private-endpoint-to-azure-digital-twins"></a>Adición de un punto de conexión privado a Azure Digital Twins 

Puede usar [Azure Portal](https://portal.azure.com) o la [CLI de Azure](/cli/azure/what-is-azure-cli) para activar Private Link con un punto de conexión privado para una instancia de Azure Digital Twins. 

Si quiere configurar Private Link como parte de la configuración inicial de la instancia, deberá usar Azure Portal. Si quiere habilitar Private Link en una instancia después de crearla, puede usar Azure Portal o la CLI de Azure. Cualquiera de estos métodos de creación proporcionará las mismas opciones de configuración y el mismo resultado final para la instancia.

Use las pestañas de las secciones siguientes para seleccionar las instrucciones de la experiencia que prefiera.

>[!TIP]
> También puede configurar un punto de conexión de Private Link mediante el servicio Private Link, en lugar de hacerlo con la instancia de Azure Digital Twins. Esto también proporciona las mismas opciones de configuración y el mismo resultado final.
>
> Para obtener más información sobre la configuración de recursos de Private Link, consulte la documentación de Private Link para [Azure Portal](../private-link/create-private-endpoint-portal.md), la [CLI de Azure](../private-link/create-private-endpoint-cli.md), [Azure Resource Manager](../private-link/create-private-endpoint-template.md) o [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-instance-creation"></a>Adición de un punto de conexión privado durante la creación de la instancia

En esta sección, creará un punto de conexión privado con Private Link como parte de la configuración inicial de una instancia de Azure Digital Twins. Esta acción solo se puede realizar en Azure Portal.

# <a name="portal"></a>[Portal](#tab/portal)

En esta sección se describe cómo activar Private Link al configurar una instancia de Azure Digital Twins en Azure Portal. 

Las opciones de Private Link se encuentran en la pestaña **Redes** de configuración de la instancia.

1. Empiece a configurar una instancia de Azure Digital Twins en Azure Portal. Para obtener instrucciones, consulte [Configuración de una instancia y autenticación](how-to-set-up-instance-portal.md).
1. Cuando llegue a la pestaña **Redes** de la configuración de la instancia, puede habilitar puntos de conexión privados si selecciona la opción **Punto de conexión privado** para el **Método de conectividad**.

    Esto agregará una sección llamada **Puntos de conexión privados** donde puede configurar los detalles del punto de conexión privado. Seleccione el botón **+ Agregar** para continuar.
    
    :::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Captura de pantalla de Azure Portal que muestra la pestaña Redes de una nueva instancia de Azure Digital Twins, en la que se resalta cómo crear un punto de conexión privado. El botón &quot;Agregar&quot; está resaltado." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

1. En la página **Crear punto de conexión privado** que se abre, escriba los detalles de un nuevo punto de conexión privado.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la página Crear punto de conexión privado. Contiene los campos que se describen a continuación.":::

    1. Rellene las selecciones para la **Suscripción** y el **Grupo de recursos**. Establezca la **Ubicación** en la misma de la red virtual que va a usar. Elija un **Nombre** para el punto de conexión y, para **Subrecursos de destino**, seleccione *API*.

    1. A continuación, seleccione la **Red virtual** y la **Subred** que quiere usar para implementar el punto de conexión.

    1. Por último, seleccione si quiere **Integrar con la zona DNS privada**. Puede usar el valor predeterminado de **Sí**, o bien, para obtener ayuda con esta opción, puede seguir el vínculo del portal para [obtener más información sobre la integración de DNS privado](../private-link/private-endpoint-overview.md#dns-configuration).

    1. Después de rellenar las opciones de configuración, seleccione **Aceptar** para finalizar.

1. Esto le devolverá a la pestaña **Redes** de la configuración de la instancia de Azure Digital Twins. Compruebe que el nuevo punto de conexión está visible en **Conexiones de punto de conexión privado**.
    
    :::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Captura de pantalla de Azure Portal que muestra la pestaña Redes de Azure Digital Twins con un punto de conexión privado recién creado." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

1. Use los botones de navegación inferiores para continuar con el resto de la configuración de la instancia.

# <a name="cli"></a>[CLI](#tab/cli)

No puede agregar un punto de conexión de Private Link durante la creación de la instancia mediante la CLI de Azure. 

Puede cambiar a Azure Portal para agregar el punto de conexión durante la creación de la instancia, o bien continúe con la sección siguiente y use la CLI para agregar un punto de conexión privado después de crear la instancia.

--- 

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Adición de un punto de conexión privado a una instancia existente

En esta sección, habilitará Private Link con un punto de conexión privado en una instancia de Azure Digital Twins que ya existe.

# <a name="portal"></a>[Portal](#tab/portal)

1. En primer lugar, navegue a [Azure Portal](https://portal.azure.com) en un explorador. Abra la instancia de Azure Digital Twins; para ello, busque su nombre en la barra de búsqueda del portal.

1. En el menú de la izquierda, seleccione **Redes (versión preliminar)** .

1. Cambie a la pestaña **Conexiones de punto de conexión privado**.

1. Seleccione **+ Punto de conexión privado** para abrir la configuración **Crear un punto de conexión privado**.

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Captura de pantalla de Azure Portal que muestra la página Redes de una instancia existente de Azure Digital Twins, en la que se resalta cómo crear puntos de conexión privados." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. En la pestaña  **Aspectos básicos** , escriba o seleccione la **Suscripción** y el **Grupo de recursos** del proyecto, y un **Nombre** y una **Región** para el punto de conexión. La región debe ser la misma que la de la red virtual que usa.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la pestaña (Básico) del cuadro de diálogo Crear punto de conexión privado. Contiene los campos descritos antes.":::

    Cuando haya terminado, seleccione el botón **Siguiente: Recurso >** para ir a la pestaña siguiente.

1. En la pestaña **Recurso**, escriba o seleccione esta información: 
    * **Método de conexión**: seleccione **Conectarse a un recurso de Azure en mi directorio** para buscar la instancia de Azure Digital Twins.
    * **Suscripción**: escriba la suscripción.
    * **Tipo de recurso**: seleccione **Microsoft.DigitalTwins/digitalTwinsInstances**.
    * **Recursos**: seleccione el nombre de la instancia de Azure Digital Twins.
    * **Recurso secundario de destino**: Seleccione **API**.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la segunda pestaña (Recurso) del cuadro de diálogo Crear punto de conexión privado. Contiene los campos descritos antes.":::

    Cuando haya terminado, seleccione el botón **Siguiente: Configuración >** para ir a la pestaña siguiente.    

1. En la pestaña  **Configuración**, escriba o seleccione esta información:
    * **Red virtual**: Seleccione la red virtual.
    * **Subred**: elija una subred de la red virtual.
    * **Integración con una zona DNS privada**: seleccione si quiere **Integrar con la zona DNS privada**. Puede usar el valor predeterminado de **Sí**, o bien, para obtener ayuda con esta opción, puede seguir el vínculo del portal para [obtener más información sobre la integración de DNS privado](../private-link/private-endpoint-overview.md#dns-configuration).
    Si selecciona **Sí**, puede dejar la información de configuración predeterminada.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la tercera pestaña (Configuración) del cuadro de diálogo Crear punto de conexión privado. Contiene los campos descritos antes.":::

    Cuando haya terminado, puede seleccionar el botón **Revisar y crear** para finalizar la instalación. 

1. En la pestaña **Revisar y crear**, revise las selecciones y seleccione el botón  **Crear**. 

Cuando finalice la implementación del punto de conexión, debe aparecer en las conexiones de punto de conexión privado de la instancia de Azure Digital Twins.

# <a name="cli"></a>[CLI](#tab/cli)

Para crear un punto de conexión privado y vincularlo a una instancia de Azure Digital Twins mediante la CLI de Azure, use el comando [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create). Identifique la instancia de Azure Digital Twins mediante su identificador completo en el parámetro `--private-connection-resource-id`.

Este es un ejemplo que usa el comando para crear un punto de conexión privado, con solo los parámetros necesarios.

```azurecli-interactive
az network private-endpoint create --connection-name <private-link-service-connection> --name <name-for-private-endpoint> --resource-group <resource-group> --subnet <subnet-ID> --private-connection-resource-id "/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<Azure-Digital-Twins-instance-name>" 
```

Para obtener una lista completa de los parámetros obligatorios y opcionales, así como más ejemplos de creación de puntos de conexión privados, consulte la documentación de referencia del comando [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create).

--- 

## <a name="manage-private-endpoint-connections"></a>Administración de conexiones de punto de conexión privado

En esta sección, aprenderá a visualizar, editar y eliminar un punto de conexión privado después de crearlo.

# <a name="portal"></a>[Portal](#tab/portal)

Una vez que se ha creado un punto de conexión privado para la instancia de Azure Digital Twins, puede verlo en la pestaña **Redes (versión preliminar)** de la instancia de Azure Digital Twins. En esta página se mostrarán todas las conexiones de punto de conexión privado asociadas a la instancia.

:::image type="content" source="media/how-to-enable-private-link/view-endpoint-digital-twins.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la página Redes de una instancia existente de Azure Digital Twins con un punto de conexión privado." lightbox="media/how-to-enable-private-link/view-endpoint-digital-twins.png":::


Seleccione el punto de conexión para ver su información en detalle, realizar cambios en sus opciones de configuración o eliminar la conexión.

>[!TIP]
> El punto de conexión también se puede ver desde Private Link Center en Azure Portal.

# <a name="cli"></a>[CLI](#tab/cli)

Una vez que se ha creado un punto de conexión privado para la instancia de Azure Digital Twins, puede usar los comandos [az dt network private-endpoint connection](/cli/azure/dt/network/private-endpoint/connection?view=azure-cli-latest&preserve-view=true) para continuar administrando las **conexiones** de punto de conexión privado relacionadas con la instancia. Estas son algunas de las operaciones:
* Mostrar una conexión de punto de conexión privado
* Establecer el estado de conexión del punto de conexión privado
* Eliminar la conexión del punto de conexión privado
* Mostrar todas las conexiones del punto de conexión privado de una instancia

Para más información y ejemplos, consulte la documentación de referencia [az dt network private-endpoint](/cli/azure/dt/network/private-endpoint?view=azure-cli-latest&preserve-view=true).

### <a name="get-additional-private-link-information"></a>Obtención de información adicional sobre Private Link

Puede obtener información adicional sobre el estado de Private Link de la instancia con el comando [az dt network private-link](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true). Estas son algunas de las operaciones:
* Enumerar los vínculos privados asociados a una instancia de Azure Digital Twins
* Mostrar un vínculo privado asociado a la instancia

Para más información y ejemplos, consulte la documentación de referencia del comando [az dt network private-link](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true).

--- 

## <a name="disable--enable-public-network-access-flags"></a>Habilitación o deshabilitación de las marcas de acceso a la red pública

Puede configurar la instancia de Azure Digital Twins para denegar todas las conexiones públicas y permitir solo las conexiones a través de puntos de conexión privados a fin de mejorar la seguridad de la red. Esta acción se realiza con una **marca de acceso a la red pública**. 

Esta directiva permite restringir el acceso de la API únicamente a las conexiones de Private Link. Cuando la marca de acceso a la red pública está establecida en *deshabilitada*, todas las llamadas API REST al plano de datos de la instancia de Azure Digital Twins desde la nube pública devolverán `403, Unauthorized`. Como alternativa, cuando la directiva se establece en *deshabilitada* y se realiza una solicitud a través de un punto de conexión privado, la llamada API se realizará correctamente.

Puede actualizar el valor de la marca de red mediante [Azure Portal](https://portal.azure.com), la [CLI de Azure](/cli/azure/) o la [herramienta de comandos ARMClient](https://github.com/projectkudu/ARMClient).

# <a name="portal"></a>[Portal](#tab/portal-2)

Para deshabilitar o habilitar el acceso a la red pública en [Azure Portal](https://portal.azure.com), abra el portal y vaya a la instancia de Azure Digital Twins.

1. En el menú de la izquierda, seleccione **Redes (versión preliminar)** .

1. En la pestaña **Acceso público**, establezca **Permitir el acceso de red público**, en **Deshabilitado** o **Todas las redes**.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Captura de pantalla de Azure Portal que muestra la página Redes de una instancia de Azure Digital Twins, en la que se resalta cómo alternar el acceso público." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Seleccione **Guardar**.

# <a name="cli"></a>[CLI](#tab/cli-2)

En la CLI de Azure, puede deshabilitar o habilitar el acceso a la red pública agregando un parámetro `--public-network-access` al comando `az dt create`. Aunque este comando también se puede utilizar para crear una nueva instancia, puede utilizarlo para editar las propiedades de una instancia existente proporcionándole el nombre de una instancia que ya exista. (Para más información acerca de este comando, consulte la [documentación de referencia](/cli/azure/dt?view=azure-cli-latest&preserve-view=true#az_dt_create) o las [instrucciones generales para configurar una instancia de Azure Digital Twins](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Para **deshabilitar** el acceso a la red pública para una instancia de Azure Digital Twins, use el parámetro `--public-network-access` de la siguiente manera:

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Disabled
```

Para **habilitar** el acceso a la red pública en una instancia en la que está deshabilitado, use el siguiente comando parecido:

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Enabled
```

# <a name="armclient"></a>[ARMClient](#tab/arm-client-2)

Con la [herramienta de comandos ARMClient](https://github.com/projectkudu/ARMClient), el acceso a la red pública se habilita o deshabilita mediante los comandos siguientes. 

Para **deshabilitar** el acceso a la red pública:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Para **habilitar** el acceso a la red pública:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

---


## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre Private Link para Azure: 
* [¿Qué es el servicio Azure Private Link?](../private-link/private-link-service-overview.md)