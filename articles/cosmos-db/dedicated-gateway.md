---
title: Puerta de enlace dedicada de Azure Cosmos DB
description: Una puerta de enlace dedicada es un proceso que actúa como front-end en su cuenta de Azure Cosmos DB. Cuando se conecta a la puerta de enlace dedicada, esta enruta las solicitudes y almacena los datos en caché.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: tisande
ms.openlocfilehash: 8c253abdfc8ded8ab5507d482640d78e6d4469c8
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226597"
---
# <a name="azure-cosmos-db-dedicated-gateway---overview-preview"></a>Puerta de enlace dedicada de Azure Cosmos DB: información general (versión preliminar)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Una puerta de enlace dedicada es un proceso del lado servidor que actúa como front-end en su cuenta de Azure Cosmos DB. Cuando se conecta a la puerta de enlace dedicada, esta enruta las solicitudes y almacena los datos en caché. Al igual que el rendimiento aprovisionado, la puerta de enlace dedicada se factura por horas.

## <a name="overview"></a>Información general

Puede aprovisionar una puerta de enlace dedicada para mejorar el rendimiento a gran escala. La razón más común por la que aprovisionar una puerta de enlace dedicada sería para el almacenamiento en caché. Al aprovisionar una puerta de enlace dedicada, se configura automáticamente una [caché integrada](integrated-cache.md) dentro de la puerta de enlace dedicada. Las consultas y las lecturas puntuales que llegan a la caché integrada no usan ninguna RU. El aprovisionamiento de una puerta de enlace dedicada con una caché integrada puede ayudar a que las cargas de trabajo con mucha actividad de lectura reduzcan los costos de Azure Cosmos DB.

La puerta de enlace dedicada está integrada en Azure Cosmos DB. Al [aprovisionar una puerta de enlace dedicada](how-to-configure-integrated-cache.md), tiene un nodo totalmente administrado que enruta las solicitudes a las particiones de back-end. La conexión a Azure Cosmos DB con la puerta de enlace dedicada proporciona una latencia menor y más predecible que la conexión a Azure Cosmos DB con la puerta de enlace estándar. Incluso los errores de caché observarán mejoras de latencia al comparar la puerta de enlace dedicada y la puerta de enlace estándar.

Solo se requieren cambios mínimos en el código para que la aplicación use una puerta de enlace dedicada. Tanto las cuentas de Azure Cosmos DB nuevas como las existentes pueden aprovisionar una puerta de enlace dedicada para mejorar el rendimiento de lectura.

> [!NOTE]
> ¿Tiene comentarios sobre la puerta de enlace dedicada? Queremos conocerlos. No dude en compartir comentarios directamente con el equipo de ingeniería de Azure Cosmos DB: cosmoscachefeedback@microsoft.com.

## <a name="connection-modes"></a>Modos de conexión

Hay tres maneras de conectarse a una cuenta de Azure Cosmos DB:

- [Modo directo](#connect-to-azure-cosmos-db-using-direct-mode)
- [Modo de puerta de enlace mediante la puerta de enlace estándar](#connect-to-azure-cosmos-db-using-gateway-mode)
- [Modo de puerta de enlace mediante la puerta de enlace dedicada](#connect-to-azure-cosmos-db-using-the-dedicated-gateway) (solo disponible para cuentas de API de SQL)

### <a name="connect-to-azure-cosmos-db-using-direct-mode"></a>Conexión a Azure Cosmos DB con el modo directo

Cuando se conecta a Azure Cosmos DB con el modo directo, la aplicación se conecta directamente al back-end de Azure Cosmos DB. Incluso si tiene muchas particiones físicas, el enrutamiento de solicitudes se controla completamente en el lado cliente. El modo directo ofrece baja latencia porque la aplicación puede comunicarse directamente con el back-end de Azure Cosmos DB y no necesita un salto de red intermedio.

Representación gráfica de la conexión en modo directo:

:::image type="content" source="./media/dedicated-gateway/direct-mode.png" alt-text="Imagen que muestra cómo funciona el modo directo de Cosmos DB" border="false":::

### <a name="connect-to-azure-cosmos-db-using-gateway-mode"></a>Conexión a Azure Cosmos DB con el modo de puerta de enlace

Si se conecta a Azure Cosmos DB con el modo de puerta de enlace, la aplicación se conectará primero a un nodo front-end, que controla el enrutamiento de la solicitud a los nodos back-end adecuados. Dado que el modo de puerta de enlace implica un salto de red adicional, puede observar una latencia ligeramente mayor en comparación con el modo directo. 

Al conectarse a Azure Cosmos DB con el modo de puerta de enlace, puede conectarse con cualquiera de las siguientes opciones:

* **Puerta de enlace estándar**: aunque el back-end, que incluye el rendimiento y el almacenamiento aprovisionados, tiene capacidad dedicada por contenedor, la puerta de enlace estándar se comparte entre muchas cuentas de Azure Cosmos. Resulta práctico para muchos clientes compartir una puerta de enlace estándar, ya que los recursos de proceso que consume cada cliente individual son pequeños.
* **Puerta de enlace dedicada**: en esta puerta de enlace, el back-end y la puerta de enlace tienen capacidad dedicada. La memoria caché integrada requiere una puerta de enlace dedicada porque requiere una CPU y memoria significativas que son específicas de la cuenta de Azure Cosmos.

### <a name="connect-to-azure-cosmos-db-using-the-dedicated-gateway"></a>Conexión a Azure Cosmos DB con la puerta de enlace dedicada

Debe conectarse a Azure Cosmos DB mediante la puerta de enlace dedicada para poder usar la memoria caché integrada. La puerta de enlace dedicada tiene un punto de conexión diferente del estándar que se proporciona con la cuenta de Azure Cosmos DB. Cuando se conecta al punto de conexión de puerta de enlace dedicada, la aplicación envía una solicitud a la puerta de enlace dedicada, que luego enruta la solicitud a distintos nodos back-end. Si es posible, la memoria caché integrada servirá el resultado.

Diagrama de conexión del modo de puerta de enlace con una puerta de enlace dedicada:

:::image type="content" source="./media/dedicated-gateway/dedicated-gateway-mode.png" alt-text="Imagen que muestra cómo funciona la puerta de enlace dedicada de Cosmos DB" border="false":::
 
## <a name="provisioning-the-dedicated-gateway"></a>Aprovisionamiento de la puerta de enlace dedicada

Un clúster de puerta de enlace dedicada se puede aprovisionar en cuentas de API Core (SQL). Un clúster de puerta de enlace dedicada puede tener hasta cinco nodos, y se pueden agregar o quitar nodos en cualquier momento. Todos los nodos de puerta de enlace dedicada de la cuenta [comparten la misma cadena de conexión](how-to-configure-integrated-cache.md#configuring-the-integrated-cache).

Los nodos de puerta de enlace dedicada son independientes entre sí. Al aprovisionar varios nodos de puerta de enlace dedicada, cualquier nodo único puede enrutar cualquier solicitud determinada. Además, cada nodo tiene una caché integrada e independiente del resto. Los datos en caché dentro de cada nodo dependen de los datos que se han [escrito o leído](integrated-cache.md#item-cache) recientemente a través de ese nodo específico. En otras palabras, si un elemento o una consulta se almacenan en caché en un nodo, no necesariamente se almacena en caché en los demás.

Para el desarrollo, se recomienda empezar con un nodo, pero para producción, debe aprovisionar tres o más nodos para alta disponibilidad. [Aprenda a aprovisionar un clúster de puerta de enlace dedicada con una caché integrada](how-to-configure-integrated-cache.md). El aprovisionamiento de varios nodos de puerta de enlace dedicada permite que el clúster de puerta de enlace dedicada siga enrutando las solicitudes y sirviendo los datos almacenados en caché, incluso cuando uno de los nodos de puerta de enlace dedicada no está disponible.

Dado que está en versión preliminar pública, la puerta de enlace dedicada no tiene un Acuerdo de Nivel de Servicio de disponibilidad. Sin embargo, por lo general, debería esperar una disponibilidad comparable al resto de la cuenta de Azure Cosmos DB.

La puerta de enlace dedicada está disponible en los siguientes tamaños:

| **Nombre de SKU** | **vCPU** | **Memoria**  |
| ------------ | -------- | ----------- |
| **D4s**      | **4**    | **16 GB** |
| **D8s**      | **8**    | **32 GB** |
| **D16s**     | **16**   | **64 GB** |

> [!NOTE]
> Una vez creados, no se puede modificar el tamaño de los nodos de puerta de enlace dedicada. Sin embargo, puede agregar o quitar nodos.

## <a name="dedicated-gateway-in-multi-region-accounts"></a>Puerta de enlace dedicada en cuentas de varias regiones

Al aprovisionar un clúster de puerta de enlace dedicada en cuentas de varias regiones, se aprovisionan clústeres de puerta de enlace dedicada idénticos en cada región. Por ejemplo, considere una cuenta de Azure Cosmos DB en Este de EE. UU. y Norte de Europa. Si aprovisiona un clúster de puerta de enlace dedicada con dos nodos D8 en esta cuenta, tendría cuatro nodos D8 en total: dos en Este de EE. UU. y dos en Norte de Europa. No es necesario configurar explícitamente puertas de enlace dedicadas en cada región, y la cadena de conexión sigue siendo la misma. Tampoco hay ningún cambio en los procedimientos recomendados para realizar conmutaciones por error.

> [!NOTE]
> No se puede aprovisionar un clúster de puerta de enlace dedicada en cuentas con zonas de disponibilidad habilitadas

Al igual que los nodos de un clúster, los nodos de puerta de enlace dedicada entre regiones son independientes. Es posible que los datos en caché de cada región sean diferentes, en función de las lecturas o escrituras recientes en esa región.

## <a name="limitations"></a>Limitaciones

La puerta de enlace dedicada tiene las siguientes limitaciones durante la versión preliminar pública:

- Las puertas de enlace dedicadas solo se admiten en cuentas de API de SQL.
- No se puede aprovisionar una puerta de enlace dedicada en las cuentas de Azure Cosmos DB con [firewalls de IP](how-to-configure-firewall.md) o [Private Link](how-to-configure-private-endpoints.md) configurados.
- No se puede aprovisionar una puerta de enlace dedicada en las cuentas de Azure Cosmos DB con [zonas de disponibilidad](high-availability.md#availability-zone-support) habilitadas.
- No se puede usar el [control de acceso basado en roles (RBAC)](how-to-setup-rbac.md) paraautenticar las solicitudes del plano de datos enrutadas a través de la puerta de enlace dedicada.

## <a name="supported-regions"></a>Regiones admitidas

La puerta de enlace dedicada se encuentra en versión preliminar pública y aún no es compatible con todas las regiones de Azure. A lo largo de la versión preliminar pública, se agregará más capacidad. No habrá restricciones de región cuando la puerta de enlace dedicada esté disponible con carácter general.

Lista actual de regiones de Azure admitidas:

| **América** | **Europa y África**  | **Asia Pacífico**  |
| ------------ | -------- | ----------- | 
| Sur de Brasil      | Centro de Francia    | Centro de Australia |
| Centro de Canadá  | Sur de Francia    | Centro de Australia 2 |
| Este de Canadá     | Norte de Alemania   | Sudeste de Australia |
| Centro de EE. UU.     | Centro-oeste de Alemania   | Centro de la India |
| Este de EE. UU.     | Norte de Europa   | Este de Asia |
| Este de EE. UU. 2     | Norte de Suiza   | Japón Occidental |
| Centro-Norte de EE. UU     | Sur de Reino Unido   | Centro de Corea del Sur |
| Centro-sur de EE. UU.     | Oeste de Reino Unido   | Corea del Sur |
| Centro-Oeste de EE. UU.     | Oeste de Europa   | Sudeste de Asia |
| Oeste de EE. UU.     |   | Centro de Emiratos Árabes Unidos |
| Oeste de EE. UU. 2     |    | Oeste de la India |


## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre el uso de la puerta de enlace dedicada en los artículos siguientes:

- [Caché integrada](integrated-cache.md)
- [Configuración de la memoria caché integrada](how-to-configure-integrated-cache.md)
- [Preguntas más frecuentes sobre la caché integrada](integrated-cache-faq.md)
- ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
    - Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
    - Si conoce las velocidades de solicitud típicas de la carga de trabajo de la base de datos actual, lea sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).