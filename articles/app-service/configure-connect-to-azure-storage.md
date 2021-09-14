---
title: Montaje de Azure Storage como un recurso compartido local (contenedor)
description: Aprenda a conectar un recurso compartido de red personalizado en una aplicación en contenedor de Azure App Service. Comparta archivos entre aplicaciones, administre el contenido estático de forma remota y acceda a él de forma local, etc.
author: msangapu-msft
ms.topic: article
ms.date: 09/02/2021
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 77dc45d71a4a9706dd645289dd5839ee97c17314
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123472027"
---
# <a name="mount-azure-storage-as-a-local-share-in-a-container-app-in-app-service"></a>Montaje de Azure Storage como un recurso compartido local en una aplicación de contenedor en App Service

::: zone pivot="container-windows"

> [!NOTE]
>Azure Storage en un contenedor Windows de App Service se encuentra **en versión preliminar** y **no se admite** para **escenarios de producción**.

En esta guía, se muestra cómo montar archivos de Azure Storage como un recurso compartido de red en un contenedor Windows en App Service. Solo se admiten [recursos compartidos de archivos de Azure Files](../storage/files/storage-how-to-use-files-cli.md) y [recursos compartidos de archivos Premium](../storage/files/storage-how-to-create-file-share.md). Entre las ventajas del montaje personalizado del almacenamiento se encuentran:

::: zone-end

::: zone pivot="container-linux"

En esta guía, se muestra cómo montar Azure Storage como un recurso compartido de red en un contenedor Linux integrado o en un contenedor Linux personalizado en App Service. Entre las ventajas del montaje personalizado del almacenamiento se encuentran:

::: zone-end

- Configurar el almacenamiento persistente de la aplicación de App Service y administrar el almacenamiento por separado.
- Hacer que el contenido estático, como vídeo e imágenes, esté disponible para la aplicación de App Service. 
- Escribir archivos de registro de la aplicación o archivar el anterior registro de la aplicación en recursos compartidos de archivos de Azure.  
- Compartir el contenido entre varias aplicaciones o con otros servicios de Azure.

::: zone pivot="container-windows"

Se admiten las siguientes características para los contenedores Windows:

- Acceso protegido a cuentas de almacenamiento con [vínculos privados](../storage/common/storage-private-endpoints.md) (cuando se usa la [integración de red virtual](web-sites-integrate-with-vnet.md)). En este momento no está disponible la compatibilidad con [puntos de conexión de servicio](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network).
- Azure Files (lectura y escritura).
- Hasta cinco puntos de montaje por aplicación.
- Asignaciones de letras de unidad (de `C:` a `Z:`).

::: zone-end

::: zone pivot="container-linux"

Se admiten las siguientes características para los contenedores Linux:

- Acceso protegido a cuentas de almacenamiento con [puntos de conexión de servicio](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) y [vínculos privados](../storage/common/storage-private-endpoints.md) (cuando se usa la [integración de red virtual](web-sites-integrate-with-vnet.md)).
- Azure Files (lectura y escritura).
- Blobs de Azure (solo lectura).
- Hasta cinco puntos de montaje por aplicación.

::: zone-end

## <a name="prerequisites"></a>Prerrequisitos

::: zone pivot="container-windows"

- [Una aplicación de contenedor de Windows existente en Azure App Service](quickstart-custom-container.md)
- [Creación de un recurso compartido de Azure Files](../storage/files/storage-how-to-use-files-cli.md)
- [Carga de archivos en un recurso compartido de Azure Files](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- Una [aplicación de App Service en Linux](index.yml) existente.
- Una [cuenta de Azure Storage](../storage/common/storage-account-create.md?tabs=azure-cli).
- Un [recurso compartido y un directorio de Azure](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> Azure Storage es un almacenamiento no predeterminado para App Service y se factura por separado, no se incluye con App Service.
>

## <a name="limitations"></a>Limitaciones

::: zone pivot="container-windows"

- No se admiten montajes de Azure Storage para aplicaciones Windows nativas (no contenedorizadas).
- No se admiten blobs de Azure.
- El [firewall de Azure Storage](../storage/common/storage-network-security.md) solo se admite mediante [puntos de conexión privados](../storage/common/storage-private-endpoints.md) (cuando se usa la [integración de red virtual](web-sites-integrate-with-vnet.md)). La compatibilidad con DNS personalizado no está disponible actualmente cuando la cuenta de Azure Storage montada usa un punto de conexión privado.
- No se admite el acceso FTP/FTPS al almacenamiento montado (use el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/)).
- No se admite la asignación de `[C-Z]:\`, `[C-Z]:\home`, `/`ni `/home` al montaje personalizado del almacenamiento.
- Los montajes de Azure Storage no se pueden usar junto con la opción de configuración de clonación durante la creación de [ranuras de implementación](deploy-staging-slots.md).
- No se hace una copia de seguridad de los montajes de Azure Storage cuando se hace una [copia de seguridad de la aplicación](manage-backup.md). Asegúrese de seguir los procedimientos recomendados para hacer una copia de seguridad de las cuentas de Azure Storage. 

::: zone-end

::: zone pivot="container-linux"

- El [firewall de Azure Storage](../storage/common/storage-network-security.md) solo se admite mediante [puntos de conexión de servicio](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) y [puntos de conexión privados](../storage/common/storage-private-endpoints.md) (cuando se usa la [integración de red virtual](web-sites-integrate-with-vnet.md)). La compatibilidad con DNS personalizado no está disponible actualmente cuando la cuenta de Azure Storage montada usa un punto de conexión privado.
- No se admite el acceso FTP/FTPS al montaje personalizado del almacenamiento (use el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/)).
- La compatibilidad con la CLI de Azure, Azure PowerShell y Azure SDK se encuentra en versión preliminar.
- No se admite la asignación de `/` ni `/home` al montaje personalizado del almacenamiento.
- Los montajes de Azure Storage no se pueden usar junto con la opción de configuración de clonación durante la creación de [ranuras de implementación](deploy-staging-slots.md).
- No se hace una copia de seguridad de los montajes de Azure Storage cuando se hace una [copia de seguridad de la aplicación](manage-backup.md). Asegúrese de seguir los procedimientos recomendados para hacer una copia de seguridad de las cuentas de Azure Storage. 

::: zone-end

::: zone pivot="container-windows"
## <a name="mount-storage-to-windows-container"></a>Montaje de Azure Storage en un contenedor Windows
::: zone-end
::: zone pivot="container-linux"
## <a name="mount-storage-to-linux-container"></a>Montaje de Azure Storage en un contenedor Linux
::: zone-end

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Vaya a la aplicación en [Azure Portal](https://portal.azure.com).
1. En el panel de navegación izquierdo, haga clic en **Configuración** > **Asignaciones de ruta de acceso** > **Nuevo montaje de Azure Storage**. 
1. Configure el montaje del almacenamiento según la tabla siguiente. Cuando termine, haga clic en **Aceptar**.

    ::: zone pivot="container-windows"
    | Configuración | Descripción |
    |-|-|
    | **Nombre** | Nombre de la configuración de montaje. No se permiten espacios. |
    | **Opciones de configuración** | Seleccione **Básico** si la cuenta de almacenamiento no usa [puntos de conexión de privados](../storage/common/storage-private-endpoints.md). En caso contrario, seleccione **Avanzado**. |
    | **Cuentas de almacenamiento** | Cuenta de Azure Storage. Debe contener un recurso compartido de Azure Files. |
    | **Nombre del recurso compartido** | Recurso compartido de archivos que se va a montar. |
    | **Clave de acceso** (solo para Avanzado) | [Clave de acceso](../storage/common/storage-account-keys-manage.md) de la cuenta de almacenamiento. |
    | **Ruta de acceso de montaje** | Directorio dentro del contenedor de Windows que se va a montar en Azure Storage. No use un directorio raíz (`[C-Z]:\` o `/`), ni el directorio `home` (`[C-Z]:\home`, o `/home`).|
    ::: zone-end
    ::: zone pivot="container-linux"
    | Configuración | Descripción |
    |-|-|
    | **Nombre** | Nombre de la configuración de montaje. No se permiten espacios. |
    | **Opciones de configuración** | Seleccione **Básico** si la cuenta de almacenamiento no usa [puntos de conexión de servicio](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) ni [puntos de conexión privados](../storage/common/storage-private-endpoints.md). En caso contrario, seleccione **Avanzado**. |
    | **Cuentas de almacenamiento** | Cuenta de Azure Storage. |
    | **Tipo de almacenamiento** | Seleccione el tipo en función del almacenamiento que quiere montar. Los blobs de Azure únicamente admiten el acceso de solo lectura. |
    | **Contenedor de Storage** o **nombre del recurso compartido** | Recurso compartido de archivos o contenedor de blobs que se va a montar. |
    | **Clave de acceso** (solo para Avanzado) | [Clave de acceso](../storage/common/storage-account-keys-manage.md) de la cuenta de almacenamiento. |
    | **Ruta de acceso de montaje** | Directorio dentro del contenedor Linux que se va a montar en Azure Storage. No utilice `/` ni `/home`.|
    ::: zone-end

    > [!CAUTION]
    > El directorio especificado en **Ruta de acceso de montaje** en el contenedor debe estar vacío. Cualquier contenido almacenado en este directorio se elimina cuando se monta Azure Storage (por ejemplo, si especifica un directorio que se encuentre en `/home`). Si va a migrar archivos de una aplicación existente, haga una copia de seguridad de la aplicación y de su contenido antes de comenzar.
    >
    
# <a name="azure-cli"></a>[CLI de Azure](#tab/cli)

Use el comando [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add). Por ejemplo:

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

::: zone pivot="container-windows"
- El valor de `--storage-type` debe ser `AzureFiles` para contenedores Windows. 
- El elemento `mount-path-directory` debe tener el formato `/path/to/dir` o `[C-Z]:\path\to\dir` sin letra de unidad. No use un directorio raíz (`[C-Z]:\` o `/`), ni el directorio `home` (`[C-Z]:\home`, o `/home`).
::: zone-end
::: zone pivot="container-linux"
- `--storage-type` puede ser `AzureBlob` o `AzureFiles`. `AzureBlob` es de solo lectura.
- `--mount-path` es el directorio dentro del contenedor Linux que se va a montar en Azure Storage. No use `/` (el directorio raíz).
::: zone-end

Ejecute el siguiente comando para comprobar que el almacenamiento está montado:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

> [!CAUTION]
> El directorio especificado en `--mount-path` en el contenedor debe estar vacío. Cualquier contenido almacenado en este directorio se elimina cuando se monta Azure Storage (por ejemplo, si especifica un directorio que se encuentre en `/home`). Si va a migrar archivos de una aplicación existente, haga una copia de seguridad de la aplicación y de su contenido antes de comenzar.
>

Ejecute el siguiente comando para comprobar la configuración:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

---

> [!NOTE]
> Agregar, editar o eliminar un montaje de almacenamiento hace que la aplicación se reinicie. 

::: zone pivot="container-linux"

## <a name="test-the-mounted-storage"></a>Prueba del almacenamiento montado

Para validar que el almacenamiento de Azure Storage se ha montado correctamente para la aplicación:

1. [Abra una sesión SSH](configure-linux-open-ssh-session.md) en el contenedor.
1. Ejecute el comando siguiente en el terminal SSH:

    ```bash
    df –h 
    ```
1. Compruebe si el recurso compartido de almacenamiento está montado. Si no está presente, hay un problema con el montaje del recurso compartido de almacenamiento.
1. Compruebe la latencia o la accesibilidad general del montaje de almacenamiento con el siguiente comando:

    ```bash
    tcpping Storageaccount.file.core.windows.net 
    ```

::: zone-end

## <a name="best-practices"></a>Procedimientos recomendados

- Para evitar posibles problemas relacionados con la latencia, coloque la aplicación y la cuenta de Azure Storage en la misma región de Azure. Sin embargo, tenga en cuenta que si la aplicación y la cuenta de Azure Storage están en la misma región de Azure, y concede acceso desde las direcciones IP de App Service en la [configuración del firewall de Azure Storage](../storage/common/storage-network-security.md), no se respetan estas restricciones de dirección IP.
::: zone pivot="container-windows"
- El directorio de montaje en la aplicación del contenedor debe estar vacío. Cualquier contenido almacenado en esta ruta se elimina cuando se monta Azure Storage. Si va a migrar archivos de una aplicación existente, haga una copia de seguridad de la aplicación y de su contenido antes de comenzar.
::: zone-end
::: zone pivot="container-linux"
- El directorio de montaje en la aplicación del contenedor debe estar vacío. Cualquier contenido almacenado en esta ruta de acceso se elimina cuando se monta Azure Storage (por ejemplo, si especifica un directorio que se encuentre en `/home`). Si va a migrar archivos de una aplicación existente, haga una copia de seguridad de la aplicación y de su contenido antes de comenzar.

- No se recomienda montar el almacenamiento en `/home` porque puede producir cuellos de botella de rendimiento para la aplicación. 
::: zone-end
- En la cuenta de Azure Storage, evite [volver a generar la clave de acceso](../storage/common/storage-account-keys-manage.md) que se usa para montar el almacenamiento en la aplicación. La cuenta de almacenamiento contiene dos claves diferentes. Use un enfoque paso a paso para asegurarse de que el montaje del almacenamiento permanece disponible para la aplicación durante la regeneración de claves. Por ejemplo, suponiendo que se ha utilizado **key1** para configurar el montaje del almacenamiento en la aplicación:

    1. Vuelva a generar **key2**. 
    1. En la configuración del montaje del almacenamiento, actualice el acceso a la clave para usar la clave **key2** que se ha vuelto a generar.
    1. Vuelva a generar **key1**.

- Si elimina una cuenta, un contenedor o un recurso compartido de Azure Storage, quite la configuración del montaje del almacenamiento correspondiente en la aplicación para evitar posibles escenarios de error. 

- La cuenta de Azure Storage montada puede tener el nivel de rendimiento Estándar o Premium. En función de los requisitos de capacidad y rendimiento de la aplicación, elija el nivel de rendimiento adecuado para la cuenta de almacenamiento. Consulte los objetivos de escalabilidad y rendimiento que corresponden al tipo de almacenamiento:

    - [Para Files](../storage/files/storage-files-scale-targets.md) (contenedores de Windows y Linux)
    - [Para Blobs](../storage/blobs/scalability-targets.md) (solo contenedores de Linux)

- Si la aplicación [se escala a varias instancias](../azure-monitor/autoscale/autoscale-get-started.md), todas las instancias se conectan a la misma cuenta de Azure Storage montada. Para evitar problemas y cuellos de botella de rendimiento, elija el nivel de rendimiento adecuado para la cuenta de almacenamiento.  

- No se recomienda usar montajes de almacenamiento para bases de datos locales (como SQLite) ni para otras aplicaciones y componentes que se basen en manipuladores de archivos y bloqueos. 

- Al usar [puntos de conexión privados](../storage/common/storage-private-endpoints.md) de Azure Storage con la aplicación, debe establecer las dos siguientes opciones de configuración de la aplicación:

    - `WEBSITE_DNS_SERVER` = `168.63.129.16`
    - `WEBSITE_VNET_ROUTE_ALL` = `1`

- Si [inicia una conmutación por error del almacenamiento](../storage/common/storage-initiate-account-failover.md) y la cuenta de almacenamiento está montada en la aplicación, el montaje no se conectará hasta que reinicie la aplicación o quite y agregue el montaje de Azure Storage. 

## <a name="next-steps"></a>Pasos siguientes

::: zone pivot="container-windows"

- [Migración de software personalizado a Azure App Service mediante un contenedor personalizado](tutorial-custom-container.md?pivots=container-windows)

::: zone-end

::: zone pivot="container-linux"

- [Configurar un contenedor personalizado](configure-custom-container.md?pivots=platform-linux)

::: zone-end