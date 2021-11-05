---
title: Contenedores en la versión preliminar de Azure Container Apps
description: Información sobre cómo se administran y configuran los contenedores en Azure Container Apps
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 09/16/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: a471f0026140f5d2b3a559788422e92447e14cb8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093058"
---
# <a name="containers-in-azure-container-apps-preview"></a>Contenedores en la versión preliminar de Azure Container Apps

Azure Container Apps le administra los detalles de Kubernetes y las orquestaciones de contenedores. Los contenedores de Azure Container Apps pueden usar cualquier runtime, lenguaje de programación o pila de desarrollo de su elección.

:::image type="content" source="media/containers/azure-container-apps-containers.png" alt-text="Azure Container Apps: contenedores":::

Azure Container Apps admite:

- Cualquier imagen de contenedor basada en Linux
- Contenedores de cualquier registro de contenedor público o privado

Otras características son:

- No hay ninguna imagen de contenedor base que sea necesaria.
- Los cambios en la sección de configuración de ARM `template` desencadenan una nueva [revisión de aplicación de contenedor](application-lifecycle-management.md).
- Si un contenedor se bloquea, se reinicia automáticamente.

## <a name="configuration"></a>Configuración

En la configuración de ejemplo siguiente se muestran las opciones disponibles al configurar un contenedor.

```json
{
  ...
  "template": {
    "containers": [
      {
        "image": "myacr.azurecr.io/myrepo/api-service:v1",
        "name": "my-container-image",
        "command": ["/bin/queue"],
        "args": [],
        "env": [
          {
            "name": "HTTP_PORT",
            "value": "8080"
          }
        ],
        "resources": {
            "cpu": 1,
            "memory": "250Mb"
        }
    }]
  }
}
```

| Configuración | Descripción | Observaciones |
|---|---|---|
| `image` | Nombre de la imagen de contenedor para la aplicación de contenedor. | Este valor toma la forma de `repository/image-name:tag`. |
| `name` | Nombre descriptivo del contenedor. | Se usa para la identificación y los informes. |
| `command` | Comando de inicio del contenedor. | Equivalente al campo de [punto de entrada](https://docs.docker.com/engine/reference/builder/) de Docker.  |
| `args` | Argumentos del comando de inicio. | Las entradas de la matriz se unen para crear una lista de parámetros que se pasa al comando de inicio. |
| `env` | Matriz de pares clave-valor que definen variables de entorno. | Utilice `secretRef` en lugar del campo `value` para hacer referencia a un secreto. |
| `resources.cpu` | Número de CPU asignadas al contenedor. | Los valores deben cumplir las siguientes reglas: el valor debe ser mayor que cero y menor que 2, y puede ser cualquier número decimal, con un máximo de una posición decimal. Por ejemplo, `1.1` es válido, pero `1.55` no lo es. El valor predeterminado es 0,5 CPU por contenedor. |
| `resources.memory` | Cantidad de RAM asignada al contenedor. | Este valor es de hasta `4Gi`. Los únicos unidos permitidos son [gibibytes](https://simple.wikipedia.org/wiki/Gibibyte) (`Gi`). Los valores deben cumplir las siguientes reglas: el valor debe ser mayor que cero y menor que `4Gi`, y puede ser cualquier número decimal, con un máximo de dos posiciones decimales. Por ejemplo, `1.25Gi` es válido, pero `1.555Gi` no lo es. La opción predeterminada es `1Gi` por contenedor.  |

La cantidad total de CPU y memoria solicitada para todos los contenedores de una aplicación de contenedor debe sumar una de las siguientes combinaciones.

| vCPU | Memoria en Gi |
|---|---|
| 0.5 | 1.0 |
| 1.0 | 2.0 |
| 1.5 | 3,0 |
| 2,0 | 4,0 |

- Todas las solicitudes de CPU de todos los contenedores deben coincidir con uno de los valores de la columna de vCPU.
- Todas las solicitudes de memoria de todos los contenedores deben coincidir con el valor de memoria de la columna de memoria en la misma fila de la columna de CPU.

## <a name="multiple-containers"></a>Varios contenedores

Puede definir varios contenedores en una sola aplicación de contenedor. Los grupos de contenedores se conocen como [pods](https://kubernetes.io/docs/concepts/workloads/pods). Los contenedores de un pod comparten recursos de disco duro y red y experimentan el mismo [ciclo de vida de la aplicación](application-lifecycle-management.md).

Para ejecutar varios contenedores juntos, defina más de un contenedor en la matriz `containers` de la configuración.

Entre los motivos para ejecutar contenedores juntos en un pod se incluyen:

- Utilizar un contenedor como sidecar para la aplicación principal.
- Utilizar un espacio en disco compartido y una red virtual.
- Compartir reglas de escalabilidad entre contenedores.
- Agrupar varios contenedores que deben ejecutarse siempre juntos.
- Habilitar la comunicación directa entre contenedores en el mismo host.

## <a name="container-registries"></a>Registros de contenedor

Puede implementar imágenes hospedadas en registros privados donde se proporcionan credenciales mediante la configuración de Container Apps.

Para usar un registro de contenedor, primero debe definir los campos necesarios en la sección `registries` de la [configuración](azure-resource-manager-api-spec.md).

```json
{
  ...
  "registries": {
    "server": "docker.io",
    "username": "my-registry-user-name",
    "passwordSecretRef": "my-password-secretref-name"
  }
}
```

Con esta configuración, se pueden usar las credenciales guardadas cuando hace referencia a una imagen de contenedor en `image` en la matriz `containers`.

En el ejemplo siguiente se muestra cómo implementar una aplicación desde Azure Container Registry.

```json
{
  ...
  "configuration": {
      "secrets": [
          {
              "name": "acr-password",
              "value": "my-acr-password"
          }
      ],
      "registries": [
          {
              "server": "myacr.azurecr.io",
              "username": "someuser",
              "passwordSecretRef": "acr-password"
          }
      ]
  }
}
```

## <a name="limitations"></a>Limitaciones

Azure Container Apps tiene las siguientes limitaciones:

- **Contenedores con privilegios**: Azure Container Apps no puede ejecutar contenedores con privilegios. Si el programa intenta ejecutar un proceso que requiere acceso raíz, la aplicación dentro del contenedor experimenta un error en tiempo de ejecución.

- **Sistema operativo**: se necesitan imágenes de contenedor basadas en Linux.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Entorno](environment.md)
