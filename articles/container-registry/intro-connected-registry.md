---
title: ¿Qué es un registro conectado?
description: Información general y escenarios de la característica de registro conectado de Azure Container Registry
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 10/21/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: ccc80f2ce5fb8393824686101d2069beee58efa4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030514"
---
# <a name="what-is-a-connected-registry"></a>¿Qué es un registro conectado? 

En este artículo, obtendrá información sobre la característica de *registro conectado* de [Azure Container Registry](container-registry-intro.md). Un registro conectado es una réplica local o remota que sincroniza imágenes de contenedor y otros artefactos de OCI con el registro de contenedor de Azure basado en la nube. Use un registro conectado para ayudar a acelerar el acceso a los artefactos del registro en el entorno local y para compilar escenarios avanzados, por ejemplo, mediante el uso de [IoT Edge anidado](../iot-edge/tutorial-nested-iot-edge.md).

> [!NOTE]
> El registro conectado es una característica en versión preliminar del nivel de servicio de registro de contenedor **Premium** y está sujeto a [limitaciones](#limitations). Para obtener información sobre los límites y los niveles de servicio de los registros, vea [Niveles de servicio de Azure Container Registry](container-registry-skus.md).

## <a name="available-regions"></a>Regiones disponibles

* Este de Asia
* Norte de Europa
* Oeste de Europa
* Este de EE. UU.

## <a name="scenarios"></a>Escenarios

Un registro de contenedor de Azure basado en la nube proporciona [características](container-registry-intro.md#key-features) como la replicación geográfica, la seguridad integrada, el almacenamiento administrado por Azure y la integración con canalizaciones de desarrollo e implementación de Azure. Al mismo tiempo, los clientes amplían sus inversiones en la nube a sus soluciones locales y de campo.

Para ejecutarse con el rendimiento y la confiabilidad necesarios en entornos locales o remotos, las cargas de trabajo de contenedor necesitan que las imágenes de contenedor y los artefactos relacionados estén disponibles en las proximidades. El registro conectado proporciona una solución de registro local eficaz que sincroniza periódicamente el contenido con un registro de contenedor de Azure basado en la nube.

Entre los escenarios de un registro conectado se incluyen:

* Factorías conectadas
* Ubicaciones comerciales de punto de venta
* Envío, extracción de petróleo, minería y otros entornos conectados ocasionalmente

## <a name="how-does-the-connected-registry-work"></a>¿Cómo funciona el registro conectado?

En la imagen siguiente se muestra un modelo de implementación típico para el registro conectado.

:::image type="content" source="media/intro-connected-registry/connected-registry-overview.svg" alt-text="Introducción al registro conectado":::

### <a name="deployment"></a>Implementación

Cada registro conectado es un recurso que se administra mediante un registro de contenedor de Azure basado en la nube. El elemento primario principal de la jerarquía del registro conectado es un registro de contenedor de Azure en una nube de Azure o en una implementación privada de [Azure Stack Hub](/azure-stack/operator/azure-stack-overview).

Use las herramientas de Azure para instalar el registro conectado en un servidor o dispositivo local o en un entorno que admita cargas de trabajo de contenedor locales, como [Azure IoT Edge](../iot-edge/tutorial-nested-iot-edge.md).

El *estado de activación* del registro conectado indica si se implementa en el entorno local.

* **Activo**: el registro conectado está implementado actualmente en el entorno local. No se puede implementar de nuevo hasta que se desactive. 
* **Inactivo**: el registro conectado no está implementado en el entorno local. Se puede implementar en este momento.  
 
### <a name="content-synchronization"></a>Sincronización de contenido

El registro conectado accede regularmente al registro en la nube para sincronizar imágenes de contenedor y artefactos de OCI. 

También se puede configurar para sincronizar un subconjunto de los repositorios desde el registro en la nube o para sincronizar solo durante determinados intervalos para reducir el tráfico entre la nube y el entorno local.

### <a name="modes"></a>Modos

Un registro conectado puede funcionar en uno de estos dos modos: *ReadWrite* o *ReadOnly*.

- **Modo ReadWrite**: el modo predeterminado permite a los clientes extraer e insertar artefactos (lectura y escritura) en el registro conectado. Los artefactos que se inserten en el registro conectado se sincronizarán con el registro en la nube. 
        
  El modo ReadWrite es útil cuando hay un entorno de desarrollo local en su lugar. Las imágenes se insertan en el registro conectado local y desde allí se sincronizan con la nube.

- **Modo ReadOnly**: cuando el registro conectado está en modo ReadOnly, los clientes solo pueden extraer (leer) artefactos. Esta configuración se usa para escenarios de IoT Edge anidados u otros escenarios en los que los clientes necesitan extraer una imagen de contenedor para funcionar.

### <a name="registry-hierarchy"></a>Jerarquía del registro

Cada registro conectado debe estar conectado a un elemento primario. El elemento primario principal es el registro en la nube. En escenarios jerárquicos como [IoT Edge anidado](overview-connected-registry-and-iot-edge.md), puede anidar registros conectados en cualquier modo. El elemento primario conectado al registro en la nube puede funcionar en cualquier modo. 

Los registros secundarios deben ser compatibles con sus funcionalidades primarias. Por lo tanto, los registros conectados en modo ReadWrite y ReadOnly pueden ser elementos secundarios de un registro conectado que funciona en modo ReadWrite, pero solo un registro en modo ReadOnly puede ser un elemento secundario de un registro conectado que funciona en modo ReadOnly.  

## <a name="client-access"></a>Acceso de clientes

Los clientes locales usan herramientas estándar, como la CLI de Docker, para insertar o extraer contenido de un registro conectado. Para administrar el acceso de cliente, cree [tokens][repository-scoped-permissions] de Azure Container Registry para el acceso a cada registro conectado. Puede establecer el ámbito de los tokens de cliente para el acceso de extracción o inserción a uno o varios repositorios del registro.

Cada registro conectado también debe comunicarse regularmente con su registro primario. Para ello, el registro en la nube emite un token de sincronización (*token de sincronización*). Este token se usa para autenticarse con su registro primario para las operaciones de sincronización y administración.

Para obtener más información, vea [Administración del acceso a un registro conectado][overview-connected-registry-access].

## <a name="limitations"></a>Limitaciones

- El número de tokens y mapas de ámbito [se limita](container-registry-skus.md) a 20 000 cada uno para un único registro de contenedor. Esto limita indirectamente el número de registros conectados para un registro en la nube, ya que cada registro conectado necesita una sincronización y un token de cliente.
- El número de permisos de repositorio en una asignación de ámbito está limitado a 500.
- El número de clientes para el registro conectado está limitado actualmente a 20.
- Actualmente no se admite el [bloqueo de imágenes](container-registry-image-lock.md) a través de metadatos de repositorio, manifiesto o etiqueta para los registros conectados.
- La [eliminación del repositorio](container-registry-delete.md) no se admite en el registro conectado mediante el modo ReadOnly.
- Actualmente no se admiten los [registros de recursos](monitor-service-reference.md#resource-logs) para los registros conectados.
- El registro conectado se combina con el punto de conexión de datos de la región principal del registro. No se admite la migración automática para la [replicación geográfica](container-registry-geo-replication.md).
- La eliminación de un registro conectado requiere la eliminación manual de los contenedores locales, así como la eliminación de los respectivos tokens o mapas de ámbito en la nube.
- Las limitaciones de sincronización del registro conectado son las siguientes:
  - Para la sincronización continua:
    - `minMessageTtl` es 1 día
    - `maxMessageTtl` es 90 días
  - En escenarios conectados ocasionalmente, donde desea especificar la ventana de sincronización:
    - `minSyncWindow` es 1 h
    - `maxSyncWindow` es 7 días

## <a name="next-steps"></a>Pasos siguientes

En esta introducción, ha aprendido sobre el registro conectado y algunos conceptos básicos. Continúe con uno de los siguientes artículos para conocer escenarios específicos en los que se puede usar el registro conectado.

> [!div class="nextstepaction"]
> [Introducción: acceso a un registro conectado][overview-connected-registry-access]
> 
> [!div class="nextstepaction"]
> [Introducción: Registro conectado e IoT Edge][overview-connected-registry-and-iot-edge]

<!-- LINKS - internal -->
[overview-connected-registry-access]:overview-connected-registry-access.md
[overview-connected-registry-and-iot-edge]:overview-connected-registry-and-iot-edge.md
[repository-scoped-permissions]: container-registry-repository-scoped-permissions.md
