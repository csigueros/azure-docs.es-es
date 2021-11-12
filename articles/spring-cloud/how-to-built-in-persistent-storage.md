---
title: Cómo usar el almacenamiento persistente integrado en Azure Spring Cloud | Microsoft Docs
description: Cómo usar el almacenamiento persistente integrado en Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b56932b9638ede2b3db36050310a06921d115103
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478248"
---
# <a name="use-built-in-persistent-storage-in-azure-spring-cloud"></a>Uso del almacenamiento persistente integrado en Azure Spring Cloud

**Este artículo se aplica a:** ✔️ Java ✔️ C#

Azure Spring Cloud proporciona dos tipos de almacenamiento integrado para las aplicaciones: persistente y temporal.

De forma predeterminada, Azure Spring Cloud proporciona el almacenamiento temporal para cada instancia de aplicación. El almacenamiento temporal se limita a 5 GB por instancia con la ruta de acceso de montaje predeterminada /tmp.

> [!WARNING]
> Si reinicia una instancia de aplicación, se eliminará de forma permanente el almacenamiento temporal asociado.

El almacenamiento persistente es un contenedor de recursos compartidos de archivos administrado por Azure que se asigna por aplicación. Todas las instancias de una aplicación comparten los datos almacenados en el almacenamiento persistente. Una instancia de Azure Spring Cloud puede tener un máximo de diez aplicaciones con almacenamiento persistente habilitado. Cada aplicación recibe 50 GB de almacenamiento persistente. La ruta de montaje predeterminada para el almacenamiento persistente es /persistent.

> [!WARNING]
> Si deshabilita el almacenamiento persistente de una aplicación, se desasignará todo ese almacenamiento y se perderán todos los datos almacenados.

## <a name="enable-or-disable-built-in-persistent-storage"></a>Habilitación o deshabilitación del almacenamiento persistente integrado

Puede modificar el estado del almacenamiento persistente integrado mediante Azure Portal o la CLI de Azure.

#### <a name="portal"></a>[Portal](#tab/azure-portal)
## <a name="enable-or-disable-built-in-persistent-storage-with-the-portal"></a>Habilitación o deshabilitación del almacenamiento persistente integrado con el portal
Se puede usar el portal para habilitar o deshabilitar el almacenamiento persistente integrado.

1. En la página **principal** de Azure Portal, seleccione **Todos los recursos**.

    >![Localización del icono Todos los recursos](media/portal-all-resources.jpg)

1. Seleccione el recurso de Azure Spring Cloud que necesita almacenamiento persistente. En este ejemplo, la aplicación seleccionada se denomina **upspring**.

    > ![Seleccione la aplicación.](media/select-service.jpg)

1. En el encabezado **Configuración**, seleccione **Aplicaciones**.

1. Sus servicios de Azure Spring Cloud aparecen en una tabla.  Seleccione el servicio al que quiere agregar almacenamiento persistente. En este ejemplo, el servicio de **puerta de enlace** está seleccionado.

    > ![Selección del servicio](media/select-gateway.jpg)

1. En la página de configuración del servicio, seleccione **Configuración**.

1. Vaya a la pestaña **Almacenamiento persistente** y seleccione **Habilitar** para activarlo, o bien seleccione **Deshabilitar** para desactivarlo.

    > ![Habilitación del almacenamiento persistente](media/enable-persistent-storage.jpg)

Si el almacenamiento persistente está habilitado, su tamaño y ruta de acceso se muestran en la pestaña **Almacenamiento persistente**.

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
## <a name="use-the-azure-cli-to-enable-or-disable-built-in-persistent-storage"></a>Use la CLI de Azure habilitar o deshabilitar el almacenamiento persistente integrado.
Si es necesario, instale la extensión de Spring Cloud para la CLI de Azure mediante este comando:

```azurecli
az extension add --name spring-cloud
```

Otras operaciones:

* Para crear una aplicación con almacenamiento persistente habilitado:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Para habilitar el almacenamiento persistente integrado para una aplicación existente:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Para deshabilitar el almacenamiento persistente integrado en una aplicación existente:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```
---
> [!WARNING]
> Si deshabilita el almacenamiento persistente de una aplicación, se desasignará todo ese almacenamiento y se perderán todos los datos almacenados.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre las [cuotas de servicio y la aplicación](./quotas.md).
* Aprenda a [escalar manualmente su aplicación](./how-to-scale-manual.md).
