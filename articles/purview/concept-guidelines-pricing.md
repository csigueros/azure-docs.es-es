---
title: Directrices de precios de Purview
description: En este artículo, se proporciona una guía para comprender los distintos componentes de los precios de Purview.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: conceptual
ms.date: 10/03/2021
ms.openlocfilehash: 393414ad1d2c50f2a3bb76ce045614e19b50f928
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534054"
---
# <a name="azure-purview-pricing"></a>Precios de Azure Purview   

Azure Purview permite una experiencia de gobernanza unificada al proporcionar un único panel para administrar la gobernanza de datos mediante la habilitación del examen automatizado y la clasificación de datos a gran escala.


## <a name="intended-audience"></a>Destinatarios

- Equipos de administración y gobernanza de datos

## <a name="why-do-you-need-to-understand-the-components-of-the-azure-purview-pricing"></a>¿Por qué necesita comprender los componentes de los precios de Azure Purview? 

- Aunque los precios de Azure Purview se basan en un modelo de **Pago por uso** basado en suscripciones, hay varias dimensiones que puede tener en cuenta al presupuestar para Purview.
- Esta guía está pensada para ayudarle a planear los presupuestos de Purview proporcionando una vista sobre los distintos factores de control que afectan al presupuesto.


## <a name="factors-impacting-azure-pricing"></a>Factores que afectan a los precios de Azure  
Hay costos **directos** e **indirectos** que se deben tener en cuenta al planear la administración de costos y los presupuestos de Purview.

### <a name="direct-costs"></a>Costos directos

Los costos directos que afectan a los precios de Azure Purview se basan en las tres dimensiones siguientes:
- **Mapa de datos elástico**
- **Examen y clasificación automatizados**
- **Conjuntos de recursos avanzados**

#### <a name="elastic-data-map"></a>Mapa de datos elástico

- El **mapa de datos** es la base de la arquitectura de Purview y, por tanto, debe estar actualizado con la información de los recursos del patrimonio de datos en un momento dado.

- El mapa de datos se cobra en términos de **unidad de capacidad** (CU). El mapa de datos se aprovisiona en una CU si el catálogo almacena hasta 2 GB de almacenamiento de metadatos y atiende hasta 25 operaciones de mapa de datos por segundo.

- Al aprovisionar una cuenta inicialmente, el mapa de datos siempre se aprovisiona en una CU.

- Sin embargo, el mapa de datos se escala automáticamente entre los límites mínimo y máximo de esa ventana de elasticidad para satisfacer los cambios en el mapa de datos con respecto a dos factores principales: **rendimiento de la operación** y **almacenamiento de metadatos**.

##### <a name="operation-throughput"></a>Rendimiento de la operación

- Factor controlado por eventos basado en las operaciones de creación, lectura, actualización y eliminación realizadas en el mapa de datos.
- Algunos ejemplos de las operaciones de mapa de datos serían: creación de un recurso en el mapa de datos, adición de una relación a un recurso como propietario, administrador, primario o linaje, edición de un recurso para agregar metadatos empresariales como la descripción o el glosario de términos, búsqueda de palabras clave que devuelve resultados a la página de resultados de la búsqueda, e importación o exportación de información mediante API.
- Si hay varias consultas ejecutadas en el mapa de datos, el número de operaciones de E/S también aumenta, lo que da lugar al escalado vertical del mapa de datos.
- El número de usuarios simultáneos también constituye un factor que gobierna la unidad de capacidad del mapa de datos.
- Otros factores que se deben tener en cuenta son el tipo de consulta de búsqueda, la interacción de API, los flujos de trabajo, las aprobaciones, etc.
- Nivel de ampliación de datos
    - Cuando se necesitan más operaciones por segundo de rendimiento, el mapa de datos puede escalar automáticamente dentro de la ventana de elasticidad para satisfacer la carga modificada.
    - Esto constituye la **característica de ampliación**, que se debe calcular y planear.
    - La característica de ampliación consta del **nivel de ampliación** y la **duración de la ampliación** durante la que existe la ráfaga de datos.
        - El **nivel de ampliación** es un índice multiplicativo de la elasticidad uniforme esperada en estado estable.
        - La **duración de la ampliación** es el porcentaje del mes en el que se esperan dichas ráfagas (en elasticidad) debido al aumento de los metadatos o a un mayor número de operaciones en el mapa de datos.


##### <a name="metadata-storage"></a>Almacenamiento de metadatos

- Si el número de recursos se reduce en el patrimonio de datos y, a continuación, se quita en el mapa de datos mediante exámenes incrementales posteriores, el componente de almacenamiento se reduce automáticamente y, por tanto, el mapa de datos se reduce verticalmente.


#### <a name="automated-scanning--classification"></a>Examen y clasificación automatizados

- Un **examen completo** procesa todos los recursos dentro de un ámbito seleccionado de un origen de datos, mientras que un **examen incremental** detecta y procesa los recursos que se han creado, modificado o eliminado desde el examen anterior correcto. 

- Todos los exámenes (exámenes completos o incrementales) recogerán los recursos **actualizados, modificados o eliminados**.

- Es importante tener en cuenta y evitar los escenarios en los que varias personas o grupos que pertenecen a distintos departamentos establecen exámenes para el mismo origen de datos, lo que da lugar a precios adicionales por el examen duplicado.

- Se recomienda programar **exámenes incrementales frecuentes** después del examen completo inicial alineados con los cambios del patrimonio de datos. Esto garantizará que el mapa de datos se mantenga siempre actualizado y que los exámenes incrementales consuman menos horas de núcleo virtual en comparación con un examen completo.

- El vínculo **"Ver detalles"** de un origen de datos permite a los usuarios ejecutar un examen completo. Sin embargo, se recomienda considerar la posibilidad de ejecutar exámenes incrementales después de un examen completo para un examen optimizado, excepto cuando hay un cambio en el conjunto de reglas de examen (clasificaciones o tipos de archivo).

- Se recomienda **registrar el origen de datos en una colección primaria** y los **exámenes de ámbito en una colección secundaria** con distintos controles de acceso para asegurarse de que no se producen costos de exámenes duplicados.

- Se recomienda limitar los usuarios a los que se les permite registrar orígenes de datos para examinarlos mediante el **control de acceso detallado** y el rol **Administrador de orígenes de datos** mediante la [autorización de colecciones](./catalog-permissions.md). Esto garantizará que solo se puedan registrar orígenes de datos válidos y que se controlen las horas de núcleo virtual de examen, lo que reduce los costos de examen.

- Tenga en cuenta que el **tipo de origen de datos** y el **número de recursos** que se examinan afectan a la duración del examen.

- Se recomienda **crear conjuntos de reglas de examen personalizados** para incluir solo el subconjunto de **tipos de archivo** disponibles en el patrimonio de datos y las **clasificaciones** pertinentes para los requisitos empresariales a fin de garantizar un uso óptimo de los detectores.

- Al crear un nuevo examen para un origen de datos, se recomienda seguir el **orden de preparación** recomendado antes de ejecutar realmente el examen. Esto incluye la recopilación de los requisitos de las **clasificaciones específicas de la empresa** y los **tipos de archivo** (para las cuentas de almacenamiento) para permitir que se definan los conjuntos de reglas de examen adecuados para evitar varios exámenes y controlar los costos innecesarios de varios exámenes por requisitos omitidos.

- Se recomienda alinear las programaciones de exámenes con el tamaño de las máquinas virtuales del entorno de ejecución de integración autohospedado (SHIR) para evitar costos adicionales vinculados a las máquinas virtuales.


#### <a name="advanced-resource-sets"></a>Conjuntos de recursos avanzados

- Azure Purview usa **conjuntos de recursos** para abordar el desafío de asignar grandes cantidades de recursos de datos a un único recurso lógico al proporcionar la capacidad de examinar todos los archivos del lago de datos y buscar patrones (GUID, patrones de localización, etc.) para agruparlos como un único recurso en el mapa de datos.

- El **conjunto de recursos avanzado** es una característica opcional que permite a los clientes obtener información calculada enriquecida del conjunto de recursos como el tamaño total, el recuento de particiones, etc., y permite la personalización de la agrupación del conjunto de recursos mediante reglas de patrón. Si la característica Conjunto de recursos avanzado no está habilitada, el catálogo de datos seguirá conteniendo recursos del conjunto de recursos, pero sin las propiedades agregadas. En este caso, no se facturará ningún medidor de "conjunto de recursos" al cliente.

- Se recomienda usar la característica de conjunto de recursos básico antes de cambiar a los conjuntos de recursos avanzados en Purview para comprobar si se cumplen los requisitos.

- Considere la posibilidad de activar los conjuntos de recursos avanzados si:
    - el esquema de los lagos de datos cambia constantemente y busca un valor adicional más allá de la característica de conjunto de recursos básico para permitir que Purview calcule parámetros como el número de particiones o el tamaño del patrimonio de datos como un servicio.
    - es necesario personalizar cómo se agrupan los recursos del conjunto de recursos. 

- Es importante tener en cuenta que la facturación de los conjuntos de recursos avanzados se basa en el proceso utilizado por el nivel sin conexión para agregar la información del conjunto de recursos y depende del tamaño y el número de conjuntos de recursos del catálogo.


### <a name="indirect-costs"></a>Costos indirectos   

Los costos indirectos que afectan a los precios de Azure Purview que se deben tener en cuenta son:

- [Recursos administrados](https://azure.microsoft.com/pricing/details/azure-purview/)
    - Cuando se aprovisiona una cuenta de Purview, se crean una cuenta de almacenamiento y una cola del centro de eventos dentro de la suscripción para llevar a cabo un examen seguro, lo que se puede cobrar por separado.


- [Punto de conexión privado de Azure](./catalog-private-link.md)
    - Los puntos de conexión privados de Azure se usan para las cuentas de Purview en las que es necesario que los usuarios de una red virtual (VNet) accedan de forma segura al catálogo mediante un vínculo privado.
    - Los requisitos previos para configurar puntos de conexión privados pueden dar lugar a costos adicionales.

- [Costos relacionados con el entorno de ejecución de integración autohospedado](./manage-integration-runtimes.md) 
    - El entorno de ejecución de integración autohospedado requiere infraestructura, lo que genera costos adicionales.
    - Es necesario implementar y registrar el entorno de ejecución de integración autohospedado (SHIR) en la misma red virtual en la que están implementados los puntos de conexión privados de ingesta de Azure Purview.
    - [Requisitos de memoria adicionales para el examen](./register-scan-sapecc-source.md#creating-and-running-a-scan)
        - Determinados orígenes de datos, como SAP, requieren memoria adicional en la máquina del SHIR para el examen.


- [Dimensionamiento de las máquinas virtuales](../virtual-machines/sizes.md)
    - Planee el dimensionamiento de las máquinas virtuales con el fin de distribuir la carga de trabajo de examen entre las máquinas virtuales para optimizar los núcleos virtuales utilizados durante la ejecución de los exámenes.

- [Licencia de Microsoft 365](./create-sensitivity-label.md) 
    - Se pueden aplicar automáticamente etiquetas de confidencialidad de Microsoft Information Protection (MIP) a los recursos de Azure en Azure Purview.
    - Las etiquetas de confidencialidad de MIP se crean y administran en el Centro de seguridad y cumplimiento (SCC) de Microsoft 365.
    - Para crear etiquetas de confidencialidad para su uso en Azure Purview debe tener una licencia de Microsoft 365 activa, que ofrece las ventajas del etiquetado automático. Para ver la lista completa de licencias, consulte la información sobre etiquetas de confidencialidad en las preguntas más frecuentes de Azure Purview. 

- [Alertas de Azure](../azure-monitor/alerts/alerts-overview.md)
    - Las alertas de Azure pueden notificar a los clientes los problemas encontrados con la infraestructura o las aplicaciones mediante los datos de supervisión de Azure Monitor.
    - Los precios adicionales de las alertas de Azure están disponibles [aquí](https://azure.microsoft.com/pricing/details/monitor/).

- [Presupuestos y alertas de Cost Management](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md)
    - Las alertas de costos generadas automáticamente se usan en Azure para supervisar el uso y el gasto de Azure en función del momento en el que se consumen los recursos de Azure.
    - Azure permite crear y administrar presupuestos de Azure. Consulte este [tutorial](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

- Cargos por salida de varias nubes
    - Tenga en cuenta los cargos de salida (cargos mínimos agregados como parte de la suscripción a varias nubes) asociados al examen de orígenes de datos de varias nubes (por ejemplo, AWS, Google) que ejecutan servicios nativos, excepto los orígenes de S3 y RDS.


## <a name="next-steps"></a>Pasos siguientes
- [Precios de Azure Purview](https://azure.microsoft.com/pricing/details/azure-purview/)
