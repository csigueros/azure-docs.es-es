---
title: Niveles y características del servicio de registro
description: Obtenga información sobre las características y los límites (cuotas) de los niveles de servicio (SKU) Básico, Estándar y Premium de Azure Container Registry.
ms.topic: article
ms.date: 08/12/2021
ms.openlocfilehash: 7f9fe5d461dede4510d3fc8069f42e7950803984
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582352"
---
# <a name="azure-container-registry-service-tiers"></a>Niveles del servicio Azure Container Registry

Azure Container Registry está disponible en varios niveles de servicio (también conocidos como SKU). Estos niveles ofrecen precios predecibles y varias opciones para alinearse con la capacidad y los patrones de uso de su registro de Docker privado en Azure.

| Nivel | Descripción |
| --- | ----------- |
| **Basic** | Un punto de entrada optimizado para los costos para que los desarrolladores aprendan sobre Azure Container Registry. Los registros básicos tienen las mismas funcionalidades de programación que Estándar y Premium (como [integración de la autenticación](container-registry-authentication.md#individual-login-with-azure-ad) de Azure Active Directory, la [eliminación de imágenes][container-registry-delete] y [webhooks][container-registry-webhook]). Sin embargo, el almacenamiento incluido y el rendimiento de las imágenes son más adecuadas para escenarios de uso inferior. |
| **Estándar** | Los registros estándar ofrecen las mismas funcionalidades que los básicos, pero con más almacenamiento y un mayor rendimiento de las imágenes. Los registros estándar deberían satisfacer las necesidades de la mayoría de los escenarios de producción. |
| **Premium** | Los registros premium proporcionan la mayor cantidad de almacenamiento incluido y operaciones simultáneas, por lo que permiten trabajar con escenarios de mayor volumen. Además de la mayor capacidad de rendimiento de las imágenes, el nivel Premium agrega características tales como [replicación geográfica][container-registry-geo-replication] para la administración de un único registro en varias regiones, [confianza del contenido](container-registry-content-trust.md) para la firma de etiquetas de imagen, y [vínculo privado con puntos de conexión privados](container-registry-private-link.md) para restringir el acceso al Registro. |

Los niveles de servicio Básico, Estándar y Premium ofrecen las mismas funcionalidades de programación. También aprovechan el [almacenamiento de imágenes][container-registry-storage] administrado totalmente por Azure. La elección de un nivel de servicio superior aporta mayor rendimiento y escalabilidad. Con varios niveles de servicio, puede empezar a trabajar con el nivel Básico y, después, cambiar a Estándar y a Premium a medida que aumente su uso del registro.

## <a name="service-tier-features-and-limits"></a>Características y límites del nivel de servicio

En la tabla siguiente se detallan las características y los límites de registro de los niveles de servicio Básico, Estándar y Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="registry-throughput-and-throttling"></a>Rendimiento y limitación del registro

### <a name="throughput"></a>Rendimiento 

Cuando genere una alta tasa de operaciones del registro, use los límites del nivel de servicio para las operaciones de lectura y escritura y el ancho de banda como orientación para conocer el rendimiento máximo esperado. Estos límites afectan a las operaciones del plano de datos, como enumerar, eliminar, insertar y extraer imágenes y otros artefactos.

Para calcular el rendimiento concretamente de las extracciones e inserciones de imágenes, tenga en cuenta los límites del registro y los siguientes factores: 

* Número y tamaño de las capas de imagen
* Reutilización de las capas o las imágenes base entre las imágenes
* Llamadas API adicionales que podrían ser necesarias para cada extracción o inserción

Para obtener más información, consulte la documentación de [HTTP API V2 de Docker](https://docs.docker.com/registry/spec/api/).

Al evaluar el rendimiento del registro o solucionar problemas, tenga en cuenta también la configuración del entorno de cliente:

* La configuración del demonio de Docker para operaciones simultáneas
* La conexión de red al punto de conexión de datos del registro (o puntos de conexión, si el registro tiene [replicación geográfica](container-registry-geo-replication.md)).

Si experimenta problemas relacionados con el rendimiento del registro, consulte [Solución de problemas de rendimiento del registro](container-registry-troubleshoot-performance.md). 

#### <a name="example"></a>Ejemplo

Para insertar una sola imagen `nginx:latest` de 133 MB en un registro de contenedor de Azure, se requieren varias operaciones de lectura y escritura para las cinco capas de la imagen: 

* Operaciones de lectura para leer el manifiesto de imagen, si existe en el registro
* Operaciones de escritura para escribir el blob de configuración de la imagen
* Operaciones de escritura para escribir el manifiesto de imagen

### <a name="throttling"></a>Limitaciones

Puede experimentar una limitación de las operaciones de extracción o inserción cuando el registro determina que la tasa de solicitudes supera los límites permitidos para el nivel de servicio del registro. Podría ver un error HTTP 429 similar a `Too many requests`.

Es posible que se produzca una limitación de forma temporal cuando se genere una ráfaga de operaciones de extracción o inserción de imágenes durante un período muy corto, incluso si la tasa media de operaciones de lectura y escritura está dentro de los límites del registro. Podría tener que implementar lógica de reintento con algún tipo de retroceso en el código o reducir la tasa máxima de solicitudes al registro.

## <a name="show-registry-usage"></a>Visualización de la utilización del registro

Use el comando [az acr show-usage](/cli/az/acr#az_acr_show_usage), o bien la API REST [List Usages](/rest/api/containerregstry/registries/list-usages), para obtener una instantánea del consumo actual del registro de almacenamiento y otros recursos, en comparación con los límites del nivel de servicio de ese registro. La utilización del almacenamiento también aparece en la página **Información general** del registro en el portal.

La información de utilización le ayuda a tomar decisiones sobre [cómo cambiar el nivel de servicio](#changing-tiers) cuando el registro se acerca a un límite. Esta información también le ayuda a [administrar el consumo](container-registry-best-practices.md#manage-registry-size). 

> [!NOTE]
> La utilización del almacenamiento por parte del registro solo se debe usar como guía y es posible que no refleje las operaciones recientes del registro. Supervise la [métrica StorageUsed](monitor-service-reference.md#container-registry-metrics) del registro para obtener datos actualizados. 

En función del nivel de servicio del registro, la información de utilización incluye algunos o todos los elementos siguientes, junto con el límite de ese nivel:

* Almacenamiento consumido en bytes<sup>1</sup>
* Número de [webhooks](container-registry-webhook.md)
* Número de [replicaciones geográficas](container-registry-geo-replication.md) (incluye la réplica principal)
* Número de [puntos de conexión privados](container-registry-private-link.md)
* Número de [reglas de acceso IP](container-registry-access-selected-networks.md)
* Número de [reglas de red virtual](container-registry-vnet.md)

<sup>1</sup>En un registro con replicación geográfica, se muestra la utilización del almacenamiento para la región principal. Multiplique por el número de replicaciones para el almacenamiento total consumido.

## <a name="changing-tiers"></a>Cambio de niveles de servicio

Puede cambiar el nivel de servicio de un registro con la CLI de Azure o en Azure Portal. Puede moverse libremente de un nivel de servicio a otro siempre que el nivel al que se vaya a cambiar tenga la capacidad de almacenamiento máximo necesaria. 

Moverse entre los niveles de servicio no produce ningún tiempo de inactividad en el registro ni afecta a las operaciones del registro.

### <a name="azure-cli"></a>Azure CLI

Para moverse entre las los niveles de servicio en la CLI de Azure, use el comando [az acr update][az-acr-update]. Por ejemplo, para cambiar a Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal de Azure

En la **información general** del registro de contenedor en Azure Portal, seleccione **Actualizar** y después seleccione una **SKU** nueva en la lista desplegable de SKU.

![Actualización de la SKU del registro de contenedor en Azure Portal][update-registry-sku]

## <a name="pricing"></a>Precios

Para más información sobre los precios de cada uno los niveles de servicio de Azure Container Registry, consulte [Precios de Container Registry][container-registry-pricing].

Para obtener más información acerca de los precios de las transferencias de datos, consulte [Detalles de precios de Bandwidth](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Pasos siguientes

**Mapa de ruta de Azure Container Registry**

Visite el [mapa de ruta de ACR][acr-roadmap] en GitHub para obtener información sobre las próximas características del servicio.

**UserVoice en Azure Container Registry**

Envíe y vote las sugerencias de nuevas características de [UserVoice en ACR][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
