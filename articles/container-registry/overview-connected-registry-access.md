---
title: Acceso a un registro conectado
description: Introducción a la autenticación y autorización basadas en tokens para registros conectados en Azure Container Registry
author: toddysm
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a98772cced7b5f7e72c66d134e9ff652f64b4086
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091109"
---
# <a name="understand-access-to-a-connected-registry"></a>Acceso a un registro conectado

Para acceder y administrar un [registro conectado](intro-connected-registry.md), actualmente solo se admite la [autenticación basada en tokens](container-registry-repository-scoped-permissions.md) de ACR. Como se muestra en la siguiente imagen, cada registro conectado usa dos tipos diferentes de tokens:

* [**Tokens de cliente**](#client-tokens): uno o varios tokens que los clientes locales usan para autenticarse con un registro conectado e insertar o extraer imágenes y artefactos hacia o desde él.
* [**Token de sincronización**](#sync-token): token que usa cada registro conectado para acceder a su contenido primario y sincronizarlo.

![Información general sobre la autenticación de registros conectados](media/overview-connected-registry-access/connected-registry-authentication-overview.svg)

> [!IMPORTANT]
> Almacene las contraseñas de token para cada registro conectado en una ubicación segura. Una vez creadas, las contraseñas de token no se pueden recuperar. Puede volver a generar contraseñas de token en cualquier momento.

## <a name="client-tokens"></a>Tokens de cliente

Para administrar el acceso de cliente a un registro conectado, cree tokens con ámbito para acciones en uno o varios repositorios. Después de crear un token, configure el registro conectado para que acepte el token mediante el comando [az acr connected-registry update](/cli/azure/acr/connected-registry#az_acr_connected_registry_update). Así, un cliente puede usar las credenciales de token para acceder a un punto de conexión de Registro conectado; por ejemplo, para usar comandos de la CLI de Docker para extraer o insertar imágenes en el registro conectado.

Las opciones para configurar acciones de token de cliente dependen de si el registro conectado permite operaciones de inserción y extracción o funciones como un reflejo de solo extracción. 
* Un registro conectado en el [modo ReadWrite](intro-connected-registry.md#modes) predeterminado permite operaciones de extracción e inserción, por lo que puede crear un token que permita acciones para *leer* y *escribir* contenido del repositorio en ese registro. 
* Con los registros conectados en [modo ReadOnly](intro-connected-registry.md#modes), los tokens de cliente solo pueden permitir acciones para *leer* contenido del repositorio.

### <a name="manage-client-tokens"></a>Administración de tokens de cliente

Actualice los tokens de cliente, las contraseñas o los mapas de ámbito, según sea necesario, mediante los comandos [az acr token](/cli/az/acr#az_acr_token) y [az acr scope-map](/cli/az/acr#az_acr_scope-map). Las actualizaciones de los tokens de cliente se propagan automáticamente a los registros conectados que aceptan el token.

## <a name="sync-token"></a>Token de sincronización

Cada registro conectado usa un token de sincronización para autenticarse con su elemento primario inmediato, que podría ser otro registro conectado o el registro en la nube. El registro conectado usa automáticamente este token al sincronizar contenido con el elemento primario o realizar otras actualizaciones. 

* El token de sincronización y las contraseñas se generan automáticamente al crear el recurso de registro conectado. Ejecute el comando [az acr connected-registry install renew-credentials][az-acr-connected-registry-install-renew-credentials] para volver a generar las contraseñas.
* Incluya las credenciales de token de sincronización en la configuración que se usa para implementar el registro conectado en el entorno local. 
* De forma predeterminada, al token de sincronización se le concede permiso para sincronizar los repositorios seleccionados con su elemento primario. Debe proporcionar un token de sincronización existente o uno o varios repositorios para sincronizar al crear el recurso de registro conectado.
* También tiene permisos para leer y escribir mensajes de sincronización en una puerta de enlace que se usa para comunicarse con el elemento primario del registro conectado. Estos mensajes controlan la programación de sincronización y administran otras actualizaciones entre el registro conectado y su elemento primario.

### <a name="manage-sync-token"></a>Administración del token de sincronización

Actualice los tokens de sincronización, las contraseñas o los mapas de ámbito, según sea necesario, mediante los comandos [az acr token](/cli/az/acr#az_acr_token) y [az acr scope-map](/cli/az/acr#az_acr_scope-map). Las actualizaciones de los tokens de sincronización se propagan automáticamente al registro conectado. Al actualizar el token de sincronización, siga las prácticas estándar de rotación de contraseñas.

> [!NOTE]
> El token de sincronización no se puede eliminar hasta que se elimine el registro conectado asociado al token. Puede deshabilitar un registro conectado estableciendo el estado del token de sincronización en `disabled`. 

## <a name="registry-endpoints"></a>Puntos de conexión del registro

Las credenciales de token de los registros conectados están limitadas al acceso a puntos de conexión de registro específicos:

* Un token de cliente accede al punto de conexión del registro conectado. El punto de conexión del registro conectado es el URI del servidor de inicio de sesión, que suele ser la dirección IP del servidor o del dispositivo que lo hospeda.

* Un token de sincronización accede al punto de conexión del registro primario, que es otro punto de conexión del registro conectado o el propio registro en la nube. Cuando está limitado al acceso al registro en la nube, el token de sincronización debe llegar a dos puntos de conexión del registro:

    - El nombre del servidor de inicio de sesión completo, por ejemplo, `contoso.azurecr.io`. Este punto de conexión se usa para la autenticación.
    - Un [punto de conexión de datos](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) regional completo para el registro en la nube, por ejemplo, `contoso.westus2.data.azurecr.io`. Este punto de conexión se usa para intercambiar mensajes con el registro conectado con fines de sincronización. 

## <a name="next-steps"></a>Pasos siguientes

Continúe con el siguiente artículo para conocer escenarios específicos en los que se puede usar el registro conectado.

> [!div class="nextstepaction"]
> [Introducción: Registro conectado e IoT Edge][overview-connected-registry-and-iot-edge]

<!-- LINKS - internal -->
[az-acr-connected-registry-update]: /cli/azure/acr/connected-registry#az_acr_connected_registry_update
[az-acr-connected-registry-install-renew-credentials]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_install_renew_credentials
[overview-connected-registry-and-iot-edge]:overview-connected-registry-and-iot-edge.md
[repository-scoped-permissions]: container-registry-repository-scoped-permissions.md
