---
title: Habilitación del acceso de extracción anónimo
description: Opcionalmente, habilite el acceso de extracción anónimo para que el contenido del registro de contenedor de Azure esté disponible públicamente.
ms.topic: how-to
ms.date: 09/17/2021
ms.custom: ''
ms.openlocfilehash: 816e99cd5ba2ba83958a9ec7b9b0ad679e4a6550
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547281"
---
# <a name="make-your-container-registry-content-publicly-available"></a>Hacer público el contenido del registro de contenedor

La configuración de un registro de contenedor de Azure para el acceso de extracción anónimo (no autenticado) es una característica opcional que permite que cualquier usuario con acceso a Internet pueda extraer cualquier contenido del registro.

El acceso de extracción anónimo es una característica en versión preliminar, disponible en los [niveles de servicio](container-registry-skus.md) Standard y Premium. Para configurar el acceso de extracción anónimo, actualice un registro mediante la CLI de Azure (versión 2.21.0 o posterior): Para la instalación o la actualización, consulte [Instalación de la CLI de Azure](/cli/azure-install-cli).

## <a name="about-anonymous-pull-access"></a>Acerca del acceso de extracción anónimo

De forma predeterminada, el acceso para extraer o insertar contenido en un registro de contenedor de Azure solo está disponible para los [usuarios autenticados](container-registry-authentication.md). Habilitar el acceso de extracción anónimo (no autenticado) hace que todo el contenido del registro esté disponible públicamente para las acciones de lectura (extracción). El acceso de extracción anónimo se puede usar en escenarios que no requieren autenticación del usuario, como la distribución de imágenes de contenedor públicas.

- Habilite el acceso de extracción anónimo actualizando las propiedades de un registro existente.
- Después de habilitar el acceso de extracción anónimo, puede deshabilitar ese acceso en cualquier momento.
- Solo las operaciones de plano de datos están disponibles para los clientes no autenticados.
- El registro puede limitar una alta tasa de solicitudes no autenticadas.
- Si previamente se autenticó en el registro, asegúrese de borrar las credenciales antes de intentar una operación de extracción anónima.

> [!WARNING]
> El acceso de extracción anónimo se aplica actualmente a todos los repositorios del registro. Si administra el acceso al repositorio mediante el uso de [tokens de ámbito de repositorio](container-registry-repository-scoped-permissions.md), todos los usuarios pueden extraer de estos repositorios en un registro habilitado para la extracción anónima. Se recomienda eliminar los tokens cuando está habilitado el acceso de extracción anónimo.

## <a name="configure-anonymous-pull-access"></a>Configuración del acceso de extracción anónimo 

### <a name="enable-anonymous-pull-access"></a>Habilitación del acceso de extracción anónimo
Actualice un registro mediante el comando [az acr update](/cli/azure/acr#az_acr_update) y pase el parámetro `--anonymous-pull-enabled`. De forma predeterminada, la extracción anónima está deshabilitada en el registro.
          
```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

> [!IMPORTANT]
> Si previamente se autenticó en el registro con credenciales de Docker, ejecute `docker logout` para asegurarse de borrar las credenciales existentes antes de intentar operaciones de extracción anónimas. De lo contrario, es posible que vea un mensaje de error similar a "Acceso de extracción denegado".

### <a name="disable-anonymous-pull-access"></a>Deshabilitación del acceso de extracción anónimo
Deshabilite el acceso de extracción anónimo estableciendo `--anonymous-pull-enabled` en `false`.

```azurecli
az acr update --name myregistry --anonymous-pull-enabled false
```

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre el uso de [tokens con ámbito de repositorio](container-registry-repository-scoped-permissions.md).
* Obtenga más información sobre las opciones para [autenticarse](container-registry-authentication.md) en un registro de contenedor de Azure.
