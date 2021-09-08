---
title: Migración de un almacén de App Configuration a otra región
description: Aprenda a migrar un almacén de App Configuration a otra región.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: c3ffe773e16eb7a658f219e980711eca2add1aac
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123077319"
---
# <a name="move-an-app-configuration-store-to-another-region"></a>Migración de un almacén de App Configuration a otra región 

Los almacenes de App Configuration son específicos de una región y no pueden moverse entre regiones automáticamente. Debe crear un nuevo almacén de App Configuration en la región de destino y mover el contenido del almacén de origen al nuevo almacén de destino. Hay varias razones que pueden llevar a mover la configuración a otra región. Por ejemplo, aprovechar una nueva región de Azure con zona de disponibilidad, implementar características o servicios que solo están disponibles en regiones concretas, o cumplir requisitos de gobernanza y directivas internos. 

En los pasos siguientes se explica el proceso de creación de un nuevo almacén de destino y de exportación del almacén actual a la nueva región. 

## <a name="design-considerations"></a>Consideraciones de diseño

Antes de comenzar, tenga en cuenta los siguientes conceptos:

* Los nombres de los almacenes de configuración son únicos globalmente. 
* Tiene que volver a configurar las directivas de acceso y la configuración de red en el nuevo almacén de configuración.

## <a name="create-the-target-configuration-store"></a>Creación del almacén de configuración de destino

### <a name="portal"></a>[Portal](#tab/portal)     
Para crear un nuevo almacén de App Configuration en el portal, siga estos pasos: 
1.  Inicie sesión en [Azure Portal](https://portal.azure.com). En la esquina superior izquierda de la página principal, seleccione **Crear un recurso**. En el cuadro **Buscar en Marketplace**, escriba *App Configuration* y presione <kbd>Entrar</kbd>. 

    ![Buscar App Configuration](../../includes/media/azure-app-configuration-create/azure-portal-search.png)
1. Seleccione **Configuración de la aplicación** en los resultados de la búsqueda y, después, **Crear**.

    ![Seleccionar Crear](../../includes/media/azure-app-configuration-create/azure-portal-app-configuration-create.png)
1. En el panel **Crear de App Configuration**, introduzca la siguiente configuración:
    
    | Configuración | Valor sugerido | Descripción |
    |---|---|---|
    | **Suscripción** | Su suscripción | Seleccione la suscripción de Azure del almacén original. |
    | **Grupos de recursos** | Su grupo de recursos | Seleccione el grupo de recursos de Azure del almacén original. |
    | **Nombre del recurso** | Nombre único globalmente | Escriba un nombre de recurso único para usarlo en el almacén de App Configuration de destino. No puede ser el mismo nombre que el del almacén de configuración anterior. |
    | **Ubicación** | Ubicación de destino | Seleccione la región de destino a la que quiere mover el almacén de configuración. |
    | **Plan de tarifa** | *Estándar* | Seleccione el plan de tarifa deseado. Para más información, consulte la [página de precios de App Configuration](https://azure.microsoft.com/pricing/details/app-configuration). |
1. Seleccione **Revisar y crear** para validar la configuración.
1. Seleccione **Crear**. La implementación puede tardar unos minutos.
1. Una vez que el recurso se haya implementado, vuelva a crear las directivas de acceso y las opciones de configuración de red del almacén de origen, puesto que no se transfieren con la configuración. Esto puede incluir el uso de identidades administradas, redes virtuales y acceso de red pública. 
    
#### <a name="azure-cli"></a>[CLI de Azure](#tab/azcli)
Para crear un nuevo almacén de App Configuration en la CLI, siga estos pasos: 
1. Inicie sesión en la CLI de Azure con las credenciales.
    ```azurecli
    az login
    ```
1. Cree un nuevo almacén de configuración con el comando `create`
    ```azurecli
    az appconfig create -g MyResourceGroup -n MyResourceName -l targetlocation --sku Standard 
    ```
    y especifique la siguiente configuración:

    | Configuración | Valor sugerido | Descripción |
    |---|---|---|
    | **Grupos de recursos** | Su grupo de recursos | Seleccione el grupo de recursos de Azure del almacén original. |
    | **Nombre del recurso** | Nombre único globalmente | Escriba un nombre de recurso único para usarlo en el almacén de App Configuration de destino. No puede ser el mismo nombre que el del almacén de configuración anterior. |
    | **Ubicación** | Ubicación de destino | Seleccione la región de destino a la que quiere mover el almacén de configuración. |
    | **Sku** | *Estándar* | Seleccione el plan de tarifa deseado. Para más información, consulte la [página de precios de App Configuration](https://azure.microsoft.com/pricing/details/app-configuration). |
1. La implementación puede tardar unos minutos. Una vez completada, vuelva a crear las directivas de acceso y las opciones de configuración de red del almacén de origen, puesto que no se transfieren con los valores de configuración. Esto puede incluir el uso de identidades administradas, redes virtuales y acceso de red pública. Para obtener más información, vea la [documentación de la CLI](./cli-samples.md).
---

## <a name="transfer-your-configuration-key-values"></a>Transferencia de pares clave-valor de configuración  

### <a name="portal"></a>[Portal](#tab/portal)
Siga estos pasos para exportar la configuración al almacén de destino mediante el portal:
1. Vaya al almacén de configuración de origen en [Azure Portal](https://portal.azure.com) y seleccione **Importar o Exportar** en **Operaciones**.
1. Seleccione **Exportar** y luego **App Configuration** en la lista desplegable **Servicio de destino**. 
    ![Exportación a otro almacén de configuración](media/export-to-config-store.png)
1. Haga clic en **Seleccionar recurso** y especifique la **Suscripción** y el **Grupo de recursos**. El **Recurso** es el nombre del almacén de configuración de destino creado anteriormente. 
1. Seleccione **Aplicar** para comprobar el almacén de configuración de destino. 
1. Deje los campos De, Hora y Etiqueta en sus valores predeterminados y seleccione **Aplicar**. 
1. Para comprobar que las configuraciones se han transferido correctamente desde el almacén de origen al de destino, vaya al almacén de configuración de destino en el portal. Seleccione **Explorador de configuración** en **Operaciones** y compruebe que contiene los mismos pares clave-valor que el almacén original. 
    > [!NOTE]
    > Este proceso solo permite que los pares clave-valor de configuración se exporten por una etiqueta a la vez. Para exportar varios, repita los pasos del 2 al 5 para cada etiqueta. 

### <a name="azure-cli"></a>[CLI de Azure](#tab/azcli)
Siga estos pasos para exportar la configuración al almacén de destino mediante Azure:
1. En la CLI de Azure, escriba el siguiente comando para exportar todos los valores del almacén de configuración de origen al de destino. 
    ```azurecli
    az appconfig kv export -n SourceConfigurationStore -d appconfig --dest-name TargetConfigurationStore --key * --label * --preserve-labels
    ```
1. Para comprobar que las configuraciones se han transferido correctamente desde el almacén de origen al de destino, enumere todos los pares de clave-valor del almacén de destino. 
    ```azurecli
    az appconfig kv list -n TargetAppConfiguration --all
    ```
---
## <a name="delete-your-source-configuration-store"></a>Eliminación del almacén de configuración de origen 

Si la configuración se ha transferido al almacén de destino, puede optar por eliminar el almacén de configuración de origen. 

### <a name="portal"></a>[Portal](#tab/portal)
Siga estos pasos para eliminar el almacén de configuración de origen en el portal:
1. Inicie sesión en [Azure Portal](https://portal.azure.com) y después seleccione **Grupos de recursos**.
1. En el cuadro de texto **Filtrar por nombre**, escriba el nombre del grupo de recursos. 
1. En la lista resultados, seleccione el nombre del grupo de recursos para ver la información general.
1. Seleccione el almacén de configuración de origen y, en la hoja **Información general**, seleccione **Eliminar**. 
1. Se le pide que confirme la eliminación del almacén de configuración, así que seleccione **Sí**.

Transcurridos unos instantes, el almacén de configuración de origen se ha eliminado.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azcli)
Siga estos pasos para eliminar el almacén de configuración de origen en la CLI de Azure:
1. Ejecute el siguiente comando en la CLI de Azure: 
    ```azurecli
    az appconfig delete -g ResourceGroupName -n SourceConfiguration
    ```
    Tenga en cuenta que el **Grupo de recursos** es el asociado al almacén de configuración de origen. 
1. La eliminación del almacén de configuración de origen puede llevar unos minutos. Puede comprobar que la operación se ha realizado correctamente si enumera todos los almacenes de configuración actuales del grupo de recursos. 
    ```azurecli
    az appconfig list -g MyResourceGroup
    ```
    Transcurridos unos instantes, el almacén de configuración de origen se ha eliminado.

---
## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Copia de seguridad automática de pares clave-valor de almacenes de Azure App Configuration](./howto-move-resource-between-regions.md)
>[Resistencia y recuperación ante desastres de Azure App Configuration](./concept-disaster-recovery.md)
