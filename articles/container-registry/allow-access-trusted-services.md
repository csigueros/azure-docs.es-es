---
title: Acceso al registro con restricción de red con el servicio de confianza de Azure
description: Habilitación de una instancia de servicio de Azure de confianza para acceder de forma segura a un registro de contenedor con restricción de red para extraer o insertar imágenes
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: da22d525bbe86403f26daf7c3505c5dadffe4b81
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654163"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Permitir que los servicios de confianza accedan de forma segura a un registro de contenedor con restricción de red (versión preliminar)

Azure Container Registry puede permitir seleccionar servicios de confianza de Azure para que accedan a un registro configurado con reglas de acceso a la red. Cuando se permiten los servicios de confianza, una instancia de servicio de confianza puede omitir de forma segura las reglas de red del registro y realizar operaciones como extraer o insertar imágenes. En este artículo se explica cómo habilitar y usar servicios de confianza con un registro de contenedor de Azure restringido por la red.

Use Azure Cloud Shell o una instalación local de la CLI de Azure para ejecutar los ejemplos de comando de este artículo. Si quiere usarla de forma local, necesitará la versión 2.18 u otra posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

Permitir que los servicios de confianza de Azure accedan al registro es una característica en **versión preliminar**.

## <a name="limitations"></a>Limitaciones

* En los escenarios de acceso al registro que necesitan una identidad administrada, solo se puede usar una identidad asignada por el sistema. Las identidades administradas asignadas por el usuario no se admiten actualmente.
* La habilitación de servicios de confianza no se aplica a un registro de contenedor configurado con un [punto de conexión de servicio](container-registry-vnet.md). La característica solo afecta a los registros que están restringidos con un [punto de conexión privado](container-registry-private-link.md) o que tienen aplicadas [reglas de acceso con direcciones IP públicas](container-registry-access-selected-networks.md). 

## <a name="about-trusted-services"></a>Acerca de los servicios de confianza

Azure Container Registry tiene un modelo de seguridad por niveles, que admite varias configuraciones de red que restringen el acceso a un registro, entre las que se incluyen:

* [Punto de conexión privado con Azure Private Link](container-registry-private-link.md). Cuando se configura, a un punto de conexión privado del registro solo pueden acceder los recursos de la red virtual mediante direcciones IP privadas.  
* [Reglas de firewall de registro](container-registry-access-selected-networks.md), que permiten acceder al punto de conexión público del registro solo desde intervalos de direcciones o direcciones IP públicas específicas. También puede usar el firewall para bloquear todo el acceso al punto de conexión público al usar puntos de conexión privados.

Cuando se implementa en una red virtual o se configura con reglas de firewall, un registro deniega el acceso a los usuarios o servicios desde fuera de esos orígenes. 

Varios servicios de Azure multiinquilino funcionan desde redes que no se pueden incluir en esta configuración de red del registro, lo que impide que realicen operaciones como extraer imágenes del registro o insertarlas en él. Al designar determinadas instancias de servicio como "de confianza", un propietario del registro puede permitir seleccionar recursos de Azure para que ignoren de forma segura la configuración de red del registro para realizar operaciones en el registro. 

### <a name="trusted-services"></a>Servicios de confianza

Las instancias de los siguientes servicios pueden acceder a un registro de contenedor con restricción de red si la opción **allow trusted services** (Permitir servicios de confianza) del registro está habilitada (opción predeterminada). Se agregarán más servicios con el tiempo.

Cuando se indica, el acceso por parte del servicio de confianza requiere una configuración adicional de una identidad administrada en una instancia de servicio, la asignación de un [rol RBAC](container-registry-roles.md) y la autenticación con el registro. Para ver pasos de ejemplo, consulte [Flujo de trabajo de servicios de confianza](#trusted-services-workflow) en este mismo artículo.

|Servicio de confianza  |Escenarios de uso admitidos  | Configuración de la identidad administrada con el rol RBAC
|---------|---------|------|
| Azure Security Center | Examen de vulnerabilidades por parte de [Azure Defender para registros de contenedor](scan-images-defender.md) | No |
|ACR Tasks     | [Acceso al registro primario o a otro registro desde una tarea de ACR](container-registry-tasks-cross-registry-authentication.md)       | Sí |
|Machine Learning | [Implementar](../machine-learning/how-to-deploy-custom-container.md) o [entrenar](../machine-learning/how-to-train-with-custom-image.md) un modelo en un área de trabajo de Machine Learning mediante una imagen de contenedor personalizada de Docker | Sí |
|Azure Container Registry | [Importación de imágenes](container-registry-import-images.md) hacia o desde un registro de contenedor de Azure restringido por la red | No |

> [!NOTE]
> Actualmente, la habilitación del valor permitir servicios de confianza no se aplica a otros servicios administrados de Azure, incluidos App Service y Azure Container Instances.

## <a name="allow-trusted-services---cli"></a>Permitir servicios de confianza (CLI)

De forma predeterminada, la opción para permitir servicios de confianza está habilitada en un nuevo registro de contenedor de Azure. Para deshabilitar o habilitar la configuración, ejecute el comando [az acr update](/cli/azure/acr#az_acr_update).

Para deshabilitarlo:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

Para habilitar la configuración en un registro existente o en un registro en el que ya está deshabilitada:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Permitir servicios de confianza (portal)

De forma predeterminada, la opción para permitir servicios de confianza está habilitada en un nuevo registro de contenedor de Azure. 

Para deshabilitar o volver a habilitar la configuración en el portal:

1. En el portal, vaya al registro de contenedor.
1. En **Configuración**, seleccione **Redes**. 
1. En **Permitir el acceso de red público**, seleccione **Redes seleccionadas** o **Deshabilitado**.
1. Realice una de las siguientes acciones:
    * Para deshabilitar el acceso de los servicios de confianza, en **Firewall exception** (Excepción de firewall), desactive **Allow trusted Microsoft services to access this container registry** (Permitir que los servicios de Microsoft de confianza accedan a este registro de contenedor). 
    * Para admitir los servicios de confianza, en **Firewall exception** (Excepción de firewall), active **Allow trusted Microsoft services to access this container registry** (Permitir que los servicios de Microsoft de confianza accedan a este registro de contenedor).
1. Seleccione **Guardar**.

## <a name="trusted-services-workflow"></a>Flujo de trabajo de los servicios de confianza

Este es un flujo de trabajo típico para permitir que una instancia de un servicio de confianza acceda a un registro de contenedor con restricción de red. Este flujo de trabajo es necesario cuando se usa la identidad administrada de una instancia de servicio para omitir las reglas de red del registro.

1. Habilite una [identidad administrada asignada por el sistema para los recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md) en una instancia de uno de los [servicios de confianza](#trusted-services) para Azure Container Registry.
1. Asigne la identidad de un [rol de Azure](container-registry-roles.md) al registro. Por ejemplo, asigne el rol ACRPull para extraer las imágenes del contenedor.
1. En el registro con restricción de red, configure la opción para permitir el acceso de los servicios de confianza.
1. Use las credenciales de la identidad para autenticarse en el registro con restricción de red. 
1. Extraiga imágenes del registro o realice otras operaciones permitidas por el rol.

### <a name="example-acr-tasks"></a>Ejemplo: ACR Tasks

En el ejemplo siguiente se muestra cómo usar las tareas de ACR como un servicio de confianza. Vea [Autenticación entre registros en una tarea de ACR mediante una identidad administrada por Azure](container-registry-tasks-cross-registry-authentication.md) para obtener más información sobre la tarea.

1. Cree o actualice una instancia de Azure Container Registry.
[Cree](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) una tarea de ACR. 
    * Habilite una identidad administrada asignada por el sistema al crear la tarea.
    * Deshabilite el modo de autenticación predeterminado (`--auth-mode None`) de la tarea.
1. Asigne a la identidad de la tarea [un rol de Azure para acceder al registro](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources). Por ejemplo, asigne el rol AcrPush, que tiene permisos para extraer e insertar imágenes.
2. [Agregue credenciales de identidad administrada](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) a la tarea.
3. Para confirmar que la tarea omite las restricciones de red, [deshabilite el acceso público](container-registry-access-selected-networks.md#disable-public-network-access) en el registro.
4. Ejecute la tarea. Si el registro y la tarea están configurados correctamente, la tarea se ejecuta correctamente, ya que el registro permite el acceso.

Para probar la deshabilitación del acceso de los servicios de confianza:

1. Deshabilite el valor para permitir el acceso de los servicios de confianza.
1. Vuelva a ejecutar la tarea. En este caso, se produce un error en la ejecución de la tarea, ya que el registro ya no permite el acceso de la tarea.

## <a name="next-steps"></a>Pasos siguientes

* Para restringir el acceso a un registro mediante un punto de conexión privado de una red virtual, consulte [Configuración de Azure Private Link para un registro de contenedor de Azure](container-registry-private-link.md).
* Para configurar las reglas de firewall del registro, vea [Configuración de reglas de red de dirección IP pública](container-registry-access-selected-networks.md).