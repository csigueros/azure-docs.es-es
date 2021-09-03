---
title: Elija un plan de tarifa.
titleSuffix: Azure Cognitive Search
description: 'Obtenga información sobre los planes de tarifa (o SKU) de Azure Cognitive Search. Un servicio de búsqueda puede aprovisionarse en estos niveles: Gratis, Básico y Estándar. Estándar está disponible en varias configuraciones de recursos y niveles de capacidad.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: e62f2a07b4266671bb055e6a672e13f69e2a0bab
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982823"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Selección de un plan de tarifa de Azure Cognitive Search

Parte del proceso de [creación de un servicio de búsqueda](search-create-service-portal.md) es elegir un plan de tarifa (o SKU) fijado para el período de vigencia del servicio. En el portal, el nivel se especifica en la página **Seleccionar plan de tarifa** al crear el servicio. Si en su lugar va a aprovisionar un servicio desde PowerShell o la CLI de Azure, el nivel se especifica mediante el parámetro **`-Sku`** .

El plan que seleccione determina:

+ Número máximo de índices y otros objetos permitido en el servicio
+ Tamaño y velocidad de particiones (almacenamiento físico)
+ Tarifa facturable como un costo mensual fijo, pero también un costo incremental si agrega capacidad.

En algunas instancias, el nivel que elija determinará la disponibilidad de [características prémium](#premium-features).

Los precios, o el costo mensual estimado de ejecución del servicio, se muestran en la página **Seleccionar plan de tarifa** del portal. Debe comprobar los [precios del servicio](https://azure.microsoft.com/pricing/details/search/) para obtener información sobre los costos estimados.

> [!NOTE]
> ¿Busca información sobre "SKU de Azure"? Empiece en [Precios de Azure](https://azure.microsoft.com/pricing/) y desplácese hacia abajo por los vínculos a las páginas de precios por servicio.

## <a name="tier-descriptions"></a>Descripciones de los planes

Los planes incluyen **Gratis**, **Básico**, **Estándar** y **Almacenamiento optimizado**. Estándar y Almacenamiento optimizado están disponibles con varias configuraciones y capacidades. En la siguiente captura de pantalla de Azure Portal se muestran los niveles disponibles, menos los precios (que puede encontrar en el portal y en la [página de precios](https://azure.microsoft.com/pricing/details/search/). 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="Gráfico del plan de tarifa" border="true":::

El plan de tarifa **Gratis** crea un servicio de búsqueda limitado para proyectos más pequeños, como la ejecución de tutoriales y ejemplos de código. Internamente, los recursos se comparten entre varios suscriptores. No se puede escalar el servicio gratis, ni se pueden ejecutar grandes cargas de trabajo.

Los planes de tarifa **Básico** y **Estándar** son los niveles facturables usados con mayor frecuencia. **Estándar** es el valor predeterminado, ya que ofrece más flexibilidad para el escalado de cargas de trabajo. Con recursos dedicados bajo su control, puede implementar proyectos más grandes, optimizar el rendimiento y aumentar la capacidad.

Algunos niveles están diseñados para determinados tipos de trabajo. Por ejemplo, **Estándar 3 Alta densidad (S3 HD)** es un *modo de hospedaje* para S3, donde el hardware subyacente está optimizado para un gran número de índices más pequeños y está pensado para escenarios de varios inquilinos. S3 HD tiene el mismo cargo por unidad que S3, pero el hardware está optimizado para lecturas de archivos rápidas en un gran número de índices más pequeños.

Los niveles **Almacenamiento optimizado** ofrecen mayor capacidad de almacenamiento a un precio menor por TB que los niveles Estándar. El principal inconveniente es una mayor latencia de consulta, lo cual debe validar para sus requisitos de aplicación específica. Para saber más sobre las consideraciones de rendimiento de este nivel, vea [Estrategias de implementación y procedimientos recomendados para optimizar el rendimiento en Azure Search](search-performance-optimization.md).

Encontrará más información sobre los distintos niveles en la [página de precios](https://azure.microsoft.com/pricing/details/search/) del artículo [Límites de servicio en Azure Cognitive Search](search-limits-quotas-capacity.md) y en la página del portal cuando vaya a aprovisionar un servicio.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Disponibilidad de características por nivel

La mayoría de las características están disponibles en todos los niveles, incluido el nivel gratis. En algunos casos, el nivel que elija afectará a su capacidad para implementar una característica. En la tabla siguiente se describen las restricciones de características relacionadas con el nivel de servicio.

| Característica | Limitaciones |
|---------|-------------|
| [Indizadores](search-indexer-overview.md) | Los indizadores no están disponibles en S3 HD.  |
| [Enriquecimiento con IA](cognitive-search-concept-intro.md) | Se ejecuta en el nivel Gratis, pero no se recomienda. |
| [Identidades administradas o de confianza para el acceso saliente (indexador)](search-howto-managed-identities-data-sources.md) | No disponibles en el nivel Gratis.|
| [Claves de cifrado administradas por el cliente](search-security-manage-encryption-keys.md) | No disponibles en el nivel Gratis. |
| [Acceso al firewall de IP](service-configure-firewall.md) | No disponibles en el nivel Gratis. |
| [Punto de conexión privado (integración con Azure Private Link)](service-create-private-endpoint.md) | En el caso de las conexiones entrantes a un servicio de búsqueda, no está disponible en el nivel Gratis. En el caso de las conexiones salientes de indexadores a otros recursos de Azure, no es´ta disponible en el nivel Gratis ni S3 HD. Para los indexadores que usan conjuntos de aptitudes, no está disponible en los niveles Gratis, Básico, S1 o S3 HD.| 
| [Zonas de disponibilidad](search-performance-optimization.md) | No disponibles en los niveles Gratis o Básico. |
| [Búsqueda semántica (versión preliminar)] | No disponible en los niveles Gratis o Básico. |

Es posible que las características que consumen muchos recursos no funcionen bien a menos que se le proporcione capacidad suficiente. Por ejemplo, el [enriquecimiento con IA](cognitive-search-concept-intro.md) contiene funciones de ejecución prolongada que agotan el tiempo de espera en un servicio Gratis, a menos que el conjunto de datos sea pequeño.

## <a name="upper-limits"></a>Límites superiores

Los niveles determinan el almacenamiento máximo del propio servicio, así como el número máximo de índices, indexadores, orígenes de datos, habilidades y mapas de sinónimos que se pueden crear. Para obtener un desglose completo de todos los límites, consulte [Límites de servicio en Azure Cognitive Search](search-limits-quotas-capacity.md). 

## <a name="partition-size-and-speed"></a>Tamaño de partición y velocidad

Los precios del plan incluyen detalles sobre el almacenamiento por partición, que va desde 2 GB para Básico hasta 2 TB para los niveles optimizados para almacenamiento (L2). Otras características de hardware, como la velocidad de las operaciones, la latencia y las tasas de transferencia, no se publican, pero los niveles que están diseñados para arquitecturas de soluciones específicas se basan en hardware que tiene las características para admitir esos escenarios. Para obtener más información sobre particiones, vea [Estimación y administración de la capacidad](search-capacity-planning.md) y [Escalado para mejorar el rendimiento](search-performance-optimization.md).

## <a name="billing-rates"></a>Tasas de facturación

Los niveles tienen tasas de facturación diferentes, con tasas mayores para los niveles que se ejecutan en hardware más caro o proporcionan características más costosas. La tasa de facturación por nivel puede encontrarse en las [páginas de precios de Azure](https://azure.microsoft.com/pricing/details/search/) para Azure Cognitive Search.

Una vez que se crea un servicio, la tasa de facturación se convierte en un *costo fijo* al ejecutar el servicio permanentemente y un *costo incremental* si se elige agregar más capacidad.

Los servicios de búsqueda se asignan a los recursos informáticos en forma de *particiones* (para almacenamiento) y *réplicas* (instancias del motor de consultas). Inicialmente, se crea un servicio con uno de cada y la tasa de facturación incluye ambos recursos. Sin embargo, si escala la capacidad, los costos aumentan o disminuyen en incrementos de la tasa facturable.

Esto se muestra en el ejemplo siguiente. Supongamos que tenemos una tasa de facturación hipotética de 100 $ al mes. Si mantiene el servicio de búsqueda en su capacidad inicial de una partición y una réplica, pagará 100 $ al final del mes. Sin embargo, si agrega dos réplicas más para lograr una alta disponibilidad, la factura mensual aumenta a 300 $ (100 $ para el primer par de réplica-partición más 200 $ para las dos réplicas).

Este modelo de precios se basa en el concepto de aplicación de la tasa de facturación al número de *unidades de búsqueda* (SU) que usa un servicio de búsqueda. Inicialmente, todos los servicios se aprovisionan en una SU, pero puede aumentar las SU mediante la adición de particiones o réplicas para administrar cargas de trabajo mayores. Para obtener más información, consulte [Cómo calcular los costos de un servicio de búsqueda](search-sku-manage-costs.md).

## <a name="next-steps"></a>Pasos siguientes

La mejor manera de elegir un plan de tarifa es comenzar con un nivel de costo mínimo y, a continuación, permitir que la experiencia y las pruebas indiquen si decide mantener el servicio o crear uno nuevo en un nivel superior. En los pasos siguientes, se recomienda crear un servicio de búsqueda en un nivel que pueda adaptarse al nivel de pruebas que propone realizar y, a continuación, revisar las siguientes instrucciones para obtener recomendaciones sobre el cálculo del costo y la capacidad.

+ [Creación de una instancia de Search Service](search-create-service-portal.md)
+ [cálculo de los costos](search-sku-manage-costs.md)
+ [Cálculo de capacidad](search-sku-manage-costs.md)