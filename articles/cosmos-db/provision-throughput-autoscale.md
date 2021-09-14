---
title: Creación de contenedores y bases de datos de Azure Cosmos en modo Escalabilidad automática
description: Obtenga información sobre las ventajas, los casos de uso y la manera de aprovisionar los contenedores y bases de datos de Azure Cosmos en el modo Escalabilidad automática.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/18/2021
ms.custom: seo-nov-2020
ms.openlocfilehash: 628cba9c515b58e23c984c6dcd34fa35ff63c78a
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123028628"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>Creación de contenedores y bases de datos de Azure Cosmos con rendimiento de escalabilidad automática
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

En Azure Cosmos DB, puede configurar el rendimiento aprovisionado estándar (manual) o de escalado automático en las bases de datos y los contenedores. El rendimiento aprovisionado de escalado automático de Azure Cosmos DB permite **escalar el rendimiento (RU/s) de la base de datos o el contenedor de forma automática e instantánea**. El rendimiento se escala en función del uso, sin afectar a la disponibilidad, la latencia o el rendimiento de la carga de trabajo.

El rendimiento aprovisionado con escalabilidad automática es adecuado para cargas de trabajo críticas que tienen patrones de tráfico variables o imprevisibles y requieren SLA para el alto rendimiento y la escala. En este artículo se describen las ventajas y los casos de uso del rendimiento aprovisionado con escalabilidad automática.

## <a name="benefits-of-autoscale"></a>Ventajas del escalado automático

Las bases de datos y los contenedores de Azure Cosmos que se han configurado con rendimiento aprovisionado con escalabilidad automática tienen las ventajas siguientes:

* **Simple:** La escalabilidad automática elimina la complejidad de la administración de RU/s con scripting personalizado y del escalado manual de la capacidad. 

* **Escalable:** Las bases de datos y los contenedores escalan automáticamente el rendimiento aprovisionado según sea necesario. No hay ninguna interrupción en las conexiones de cliente ni en las aplicaciones y no afectan a los SLA de Azure Cosmos DB.

* **Rentable:** La escalabilidad automática ayuda a optimizar el uso de RU/s y el costo por uso mediante la reducción vertical cuando no está en uso. Solo paga por los recursos que necesitan las cargas de trabajo por hora. De todas las horas del mes, si establece el valor de RU/s (Tmax) y usa toda la cantidad durante el 66 % de las horas o menos, ahorrará con la escalabilidad automática. Para obtener más información, consulte el artículo de [procedimientos para elegir entre el rendimiento aprovisionado estándar (manual) y el de escalabilidad automática](how-to-choose-offer.md).

* **Alta disponibilidad:** Las bases de datos y los contenedores que usan la escalabilidad automática usan el mismo back-end de Azure Cosmos DB distribuido globalmente, tolerante a errores y de alta disponibilidad para garantizar la durabilidad de los datos y la alta disponibilidad.

## <a name="use-cases-of-autoscale"></a>Casos de uso de la escalabilidad automática

Los casos de uso de la escalabilidad automática incluyen:

* **Cargas de trabajo impredecibles o variables:** cuando las cargas de trabajo tienen picos variables o imprevisibles en su uso, la escalabilidad automática ayuda a escalar y reducir verticalmente en función del uso. Entre los ejemplos se incluyen sitios web minoristas que tienen diferentes patrones de tráfico en función de la estacionalidad, cargas de trabajo de IOT que tienen picos en varias ocasiones durante el día, aplicaciones de línea de negocio que tienen su uso máximo varias veces al mes o al año y muchos más. Con la escalabilidad automática, ya no es necesario aprovisionar manualmente para los casos de capacidad máxima o media. 

* **Aplicaciones nuevas:** Si va a desarrollar una nueva aplicación y no está seguro del rendimiento (RU/s) que necesita, la escalabilidad automática le ayuda a empezar a trabajar. Puede empezar con un punto de entrada de escalabilidad automática de 400 a 4 000 RU/s, supervisar el uso y determinar las RU/s correctas a lo largo del tiempo.

* **Aplicaciones usadas con poca frecuencia:** Si tiene una aplicación que solo se usa durante unas horas varias veces al día, la semana o el mes, como un sitio web, un blog o una aplicación de bajo volumen, la escalabilidad automática ajusta la capacidad para administrar el pico de uso y la reduce verticalmente cuando finaliza. 

* **Cargas de trabajo de desarrollo y pruebas:** si usted o su equipo usan bases de datos y contenedores de Azure Cosmos durante las horas de trabajo, pero no las necesitan por las noches o los fines de semana, la escalabilidad automática ayuda a ahorrar costos al reducirse verticalmente al mínimo cuando no hay uso. 

* **Cargas de trabajo o consultas de producción programadas:** si tiene una serie de solicitudes, operaciones o consultas programadas que desea ejecutar durante períodos de inactividad, puede hacerlo fácilmente con la escalabilidad automática. Cuando necesite ejecutar la carga de trabajo, el rendimiento se escalará automáticamente al necesario y se reducirá verticalmente después. 

La creación de una solución personalizada para estos problemas no solo requiere una cantidad enorme de tiempo, sino que también presenta complejidad en la configuración y el código de la aplicación. La escalabilidad automática permite los escenarios anteriores de forma integrada y elimina la necesidad de un escalado personalizado o manual de la capacidad. 

## <a name="how-autoscale-provisioned-throughput-works"></a>Cómo funciona el rendimiento aprovisionado de escalabilidad automática

Al configurar contenedores y bases de datos con escalabilidad automática, se especifica el rendimiento máximo `Tmax` requerido. Azure Cosmos DB escala el rendimiento `T` en intervalos de `0.1*Tmax <= T <= Tmax`. Por ejemplo, si establece el rendimiento máximo en 20 000 RU/s, el rendimiento se escalará entre 2 000 y 20 000 RU/s. Dado que el escalado es automático e instantáneo, puede consumir en cualquier momento hasta el valor de `Tmax` aprovisionado sin ningún retraso. 

Cada hora, se le facturará el mayor rendimiento `T` al que se escaló el sistema en dicha hora.

El punto de entrada para el rendimiento máximo de la escalabilidad automática, `Tmax`, comienza en 4 000 RU/s, que se escalan entre 400 y 4 000 RU/s. Puede establecer `Tmax` en incrementos de 1 000 RU/s y cambiar el valor en cualquier momento.  

## <a name="enable-autoscale-on-existing-resources"></a>Habilitación de la escalabilidad automática en recursos existentes

Use [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container), la [CLI](how-to-provision-autoscale-throughput.md#azure-cli) o [PowerShell](how-to-provision-autoscale-throughput.md#azure-powershell) para habilitar la escalabilidad automática en una base de datos o un contenedor existente. Puede cambiar entre la escalabilidad automática y el rendimiento aprovisionado estándar (manual) en cualquier momento. Consulte esta [documentación](autoscale-faq.yml#how-does-the-migration-between-autoscale-and-standard--manual--provisioned-throughput-work-) para obtener más información.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> Límites de rendimiento y almacenamiento para Escalabilidad automática

Para cualquier valor de `Tmax`, la base de datos o el contenedor pueden almacenar un total de `0.01 * Tmax GB`. Una vez alcanzada esta cantidad de almacenamiento, el número máximo de RU/s se incrementará automáticamente en función del nuevo valor del almacenamiento, sin que ello afecte a la aplicación. 

Por ejemplo, si comienza con un máximo de 50 000 RU/s (que se escalan entre 5 000 y 50 000 RU/s), puede almacenar hasta 500 GB de datos. Si supera los 500 GB: por ejemplo, el almacenamiento es ahora de 600 GB, el nuevo número máximo será de 60 000 RU/s (que se escalan entre 6 000 y 60 000 RU/s).

Si usa el rendimiento de nivel de base de datos con escalabilidad automática, puede hacer que los primeros 25 contenedores compartan un número máximo de 4 000 RU/s de escalabilidad automática (que se escalan entre 400 y 4 000 RU/s), siempre que no supere los 40 GB de almacenamiento. Consulte esta [documentación](autoscale-faq.yml#can-i-change-the-max-ru-s-on-the-database-or-container--) para obtener más información.

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>Comparación: contenedores configurados con rendimiento manual frente a la escalabilidad automática
Para más información, consulte esta [documentación](how-to-choose-offer.md) sobre cómo elegir entre el rendimiento estándar (manual) y la escalabilidad automática.  

|| Contenedores con rendimiento estándar (manual)  | Contenedores con rendimiento de escalabilidad automática |
|---------|---------|---------|
| **Rendimiento aprovisionado (RU/s)** | Aprovisionada manualmente. | Escalado automático e instantáneo en función de los patrones de uso de la carga de trabajo. |
| **Limitación de velocidad de las solicitudes o las operaciones (429)**  | Puede ocurrir si el consumo supera la capacidad aprovisionada. | No ocurrirá si utiliza RU/s dentro del intervalo de rendimiento de escalabilidad automática que ha establecido.    |
| **Planificación de capacidad** |  Debe realizar un planeamiento de la capacidad y aprovisionar el rendimiento exacto que necesita. |    El sistema se encarga automáticamente del planeamiento y la administración de la capacidad. |
| **Precios** | Se paga por las RU/s aprovisionadas manualmente, con la [tarifa de RU/s estándar (manual) por hora](https://azure.microsoft.com/pricing/details/cosmos-db/). | Se paga por hora por el número mayor de RU/s a las que se ha escalado verticalmente el sistema en dicha hora. <br/><br/> En el caso de las cuentas de una sola región de escritura, se paga por las RU/s utilizadas en una hora, con la [tarifa de RU/s por hora de la escalabilidad automática](https://azure.microsoft.com/pricing/details/cosmos-db/). <br/><br/>En el caso de las cuentas con varias regiones de escritura, no se aplica ningún cargo adicional por Escalabilidad automática. Se paga por el rendimiento usado por hora con la misma [tarifa de RU/s por hora de escritura en varias regiones](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| **Adecuado para los tipos de carga de trabajo** |  Cargas de trabajo predecibles y estables|   Cargas de trabajo impredecibles y variables  |

## <a name="next-steps"></a>Pasos siguientes

* Revise las [preguntas más frecuentes sobre Escalabilidad automática](autoscale-faq.yml).
* Obtenga información acerca de cómo [elegir entre el rendimiento manual y de escalabilidad automática](how-to-choose-offer.md).
* Obtenga información sobre el [aprovisionamiento del rendimiento de escalabilidad automática en una base de datos o un contenedor de Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Más información sobre la [creación de particiones](partitioning-overview.md) en Azure Cosmos DB.
* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Para ello, puede usar información sobre el clúster de base de datos existente.
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
    * Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, lea sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).


